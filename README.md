# Ticket Management System

A **C++17** console-based ticket management system built as a DSA final project. Every major data structure and algorithm is implemented from scratch — no STL containers are used for the core logic (except `std::stack` for the resolution log and `std::vector` as a dynamic array backing the heap).

---

## Data Structures

| Structure | Where Used | Purpose |
|-----------|-----------|---------|
| **Binary Search Tree (BST)** | `UserBST` | Store & look up user profiles by ID in O(log n) |
| **Max-Heap** | `PriorityQueue` | Serve highest-priority ticket first per department |
| **Singly Linked List** | `TicketLinkedList` | Master archive of every ticket ever created |
| **Stack (LIFO)** | `ResolutionStack` | Chronological log of resolved tickets |

## Algorithms

| Algorithm | Where Used | Purpose |
|-----------|-----------|---------|
| **Quick Sort** | `SortingSearchManager` | Sort tickets by priority or ID |
| **Merge Sort** | `SortingSearchManager` | Alternative stable sort for tickets |
| **Bubble Sort** | `AgentManager` | Sort agents by current workload |
| **Binary Search** | `SortingSearchManager` | O(log n) ticket & user lookup |
| **Heap-Up / Heap-Down** | `PriorityQueue` | Maintain max-heap invariant on insert/remove |
| **BST Insert / Search** | `UserBST` | O(log n) user management |

---

## Features

### Admin Panel
- Add tickets with priority (1–10), type, and description
- Assign tickets to least-loaded agents (greedy load balancing)
- Resolve tickets (moves them to the LIFO resolution stack)
- View all tickets, sorted tickets, and per-type priority queues
- Search tickets by ID (linear or binary) or by User ID
- Sort tickets using Quick Sort, Merge Sort, or by wait time
- Priority aging — boost priority of tickets waiting > 30 minutes
- View and sort agents by workload (bubble sort)
- User management via BST with binary search

### User Panel
- Submit a ticket (auto-registers new users)
- View own tickets
- View any department's priority queue
- View all open tickets
- View own user profile

---

## Project Structure

```
ticket-management-system/
├── src/
│   └── main.cpp          # All source code (single-file project)
├── docs/
│   └── architecture.md   # Detailed design notes
├── CMakeLists.txt         # CMake build (cross-platform)
├── Makefile               # Quick make targets
├── .gitignore
└── README.md
```

---

## Build & Run

### Prerequisites
- C++17-compatible compiler: `g++ 7+`, `clang++ 5+`, or `MSVC 2017+`
- **Linux / macOS**: `make` or `cmake`
- **Windows**: MinGW, MSYS2, or Visual Studio

### Using Make (Linux / macOS)
```bash
git clone https://github.com/<your-username>/ticket-management-system.git
cd ticket-management-system
make          # build
make run      # build + run
make debug    # build with debug symbols
make clean    # remove binaries
```

### Using CMake (all platforms)
```bash
mkdir build && cd build
cmake ..
cmake --build .
./ticket_system       # Linux / macOS
ticket_system.exe     # Windows
```

### Direct compile
```bash
g++ -std=c++17 -Wall -O2 -o ticket_system src/main.cpp
./ticket_system
```

---

## Usage Guide

```
=======================================================
   TICKET MANAGEMENT SYSTEM
   Using: BST · Heap · Linked List · Stack
          QuickSort · MergeSort · BinarySearch
=======================================================

  [1] Admin Mode
  [2] User Mode
  [0] Exit
```

**Demo data loaded on startup:**

| User ID | Name | Department |
|---------|------|-----------|
| 1001 | Tommy | IT |
| 1002 | Arif | Admin |
| 1003 | Umer | Accounts |
| 1004 | Abdullah | Academics |

Four demo tickets are also pre-loaded so you can explore all features immediately.

---

## Design Notes

- **Ticket ownership**: `TicketLinkedList` owns all `Ticket*` objects and frees them in its destructor. Priority queues hold non-owning pointers.
- **User ownership**: `UserBST` owns all `User*` objects and frees them via a post-order tree traversal in its destructor.
- **Agent ownership**: `AgentManager` owns all `Agent*` objects.
- **No memory leaks**: Every class has a proper destructor; copy constructors and assignment operators are deleted where ownership matters.
- **`srand`** is called once at the start of `TicketSystem::TicketSystem()` before any `rand()` invocations.
- **`mergeParts`** (internal helper) is deliberately named to avoid ADL ambiguity with `std::merge`.

---
## License

MIT — see [LICENSE](LICENSE) for details.

---

## Author
Mughees Khan
