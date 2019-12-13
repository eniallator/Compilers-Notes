# Garbage Collection 🚮 + ♻

- There are three ways of reusing heap storage
  - By hand
  - Automatically, using a garbage collector
  - Typing system

## Heap Management

- AUTOMATE ALL THE THINGS 🕺
- If a variable is no longer reachable or in scope, then it is garbage and so should be removed

### Algorithm For Computing Reachability

```javascript
reach(roots) =
  for each r in roots
    if (r not yet processed)
      mark r as reachable
      let [r1, ..., rn] be pointers contained in r
      reach([r1, ..., rn])
```

- 2 algorithms for GC
  - Mark and sweep (AKA tracing GC)
  - Stop and copy

#### Mark And Sweep

- 2 phases:
  - Mark phase which computes the reachable parts of the heap, starting from roots
  - Sweep phase which unclaims unreachable memory by going through whole heap
- During mark, it will change a markbit to a `1` value for the sweep phase
- Sweep:

```javascript
sweep(heap) =
  for each cell c in heap
    if (c.markbit == 1)
      c.markbit := 0
    else
      putOnFreeList(c)
```

- Bad part is u have to go through the entire heap twice in the worst case
  - Very slow

##### Constructing free list

- Garbage collection is done when the free list has not enough space
- Look at each element and then see if it's marked or free
- Create a linked list where the pointers to each free item

#### Stop And Copy

- Splits the heap into 2
- Only ever use up half the heap
- GC finds objects and copies them from old space to new space
- Objects contain pointers to other objects
  - These pointers must be rewritten to account for new location (including roots)
- As we copy from old to new, we store a forwarding pointer in old object
  - So any old objects that used to point to the old version, will know what the updated version is
- No need to daisy chain the free spaces because the free spaces are after the last item on the heap
- Usually the fastest algorithm
- Disadvantages
  - Only uses half the heap
  - Pointers get rewritten (prevents address arithmetic)

## Trade-Off For GC

- Effortlessly prevents many serious and hard to track down bugs, hence leads to much nicer and more readable program structure
  - Much nicer/easier to write in GC languages
- But reduces programmer control, e.g data layout in memory or when memory is deallocated
  - Sometimes it's a problem
- The GC (or OS kernel) itself has to be written that doesn't have GC

## GC Pauses

- Extremely effective at avoiding problems
- Program will pause when GC is going on
