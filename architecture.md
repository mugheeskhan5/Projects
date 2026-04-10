# Architecture & Design Notes

## Class Diagram (simplified)

```
TicketSystem
├── TicketLinkedList   (owns Ticket*)
│   └── TicketNode → TicketNode → ...
├── UserBST            (owns User*)
│   └── BSTNode (left/right subtrees)
├── AgentManager       (owns Agent*)
│   └── vector<Agent*>
│       └── Agent.tickets[5]  (non-owning Ticket*)
├── ResolutionStack    (non-owning Ticket*)
│   └── std::stack<Ticket*>
└── PriorityQueue[4]   (non-owning Ticket*)
    └── vector<Ticket*>  (max-heap)
```

## Memory Ownership Model

```
Ticket objects: created in TicketSystem::addTicket()
                owned by  TicketLinkedList
                referenced (non-owning) by PriorityQueue heaps,
                Agent.tickets[], and ResolutionStack

User objects:   created by caller, inserted into UserBST
                owned by  UserBST (freed in destroyTree())

Agent objects:  created by caller, inserted into AgentManager
                owned by  AgentManager (freed in destructor)
```

## Priority Queue – Max-Heap

Heap invariant: `parent.priority ≥ child.priority`

- **Enqueue** – append, heapify-up   → O(log n)
- **Dequeue** – swap root with last, pop, heapify-down → O(log n)
- **Peek**    – read `heap[0]`       → O(1)

Four independent heaps (one per department): IT, Admin, Accounts, Academics.

## BST – User Profiles

- **Insert** – standard recursive BST insert keyed on `user->id`
- **Search by ID** – O(log n) average, O(n) worst (unbalanced)
- **Search by Name** – O(n) linear scan of inorder traversal
- **Inorder traversal** – returns users sorted by ID

Note: The BST is not self-balancing (AVL/Red-Black). For small datasets (< 1000 users) this is acceptable; for production use, consider `std::map`.

## Sorting Algorithms

### Quick Sort (in-place, O(n log n) average)
- Partition strategy: last-element pivot (Lomuto scheme)
- Sort key: priority (descending) or ticket ID (ascending)

### Merge Sort (stable, O(n log n) worst-case)
- Standard top-down recursive merge sort
- Helper named `mergeParts` (not `merge`) to avoid ADL clash with `std::merge`

### Bubble Sort – Agent workload
- O(n²) – acceptable because the agent pool is tiny (< 20 agents)
- Sorts ascending by `agent->assigned`

## Agent Load Balancing

`AgentManager::assignTicket` uses a simple greedy strategy:

1. Find the **available** agent of matching type with minimum current load.
2. If no available agent exists, find any agent of matching type with minimum load (even if full — `Agent::assign` will return false and the ticket stays queued).

This prevents starvation of one agent while another sits idle.

## Priority Aging

`Ticket::boostPriority()` adds +2 (capped at 10) when `waitingMinutes > 30`.  
`TicketSystem::updatePriorities()` iterates all open tickets, refreshes wait times, and applies boosts. Call this periodically to prevent low-priority tickets from waiting indefinitely (starvation prevention).
