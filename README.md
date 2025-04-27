# PDC

---

### **Q1: Given a 4x4 mesh network, calculate the total number of links, diameter, bisection width and arc connectivity.**

- **Total nodes** = 4 × 4 = 16  
- **Total links**:  
  Each internal node has 2 links (right and down), edges have fewer.  
  - Horizontal links = 4 rows × (4 - 1) = 12  
  - Vertical links = 4 columns × (4 - 1) = 12  
  - **Total links** = 12 + 12 = **24**
- **Diameter** (max hops between two nodes) = (rows - 1) + (columns - 1) = 3 + 3 = **6**
- **Bisection width** (minimum links to cut to divide into two halves) = 4 (cut across center rows or columns) = **4**
- **Arc connectivity** (minimum links removal to disconnect the graph) = **2** (since each internal node has 2 independent paths).

---

### **Q2: Draw the Flynn’s taxonomy of parallel computers.**

Flynn's Taxonomy categorizes computers based on instruction and data streams:

| Classification | Description |
|:---------------|:------------|
| SISD | Single Instruction, Single Data (traditional uniprocessors) |
| SIMD | Single Instruction, Multiple Data (e.g., GPUs, Vector Processors) |
| MISD | Multiple Instruction, Single Data (rare; e.g., fault-tolerant systems) |
| MIMD | Multiple Instruction, Multiple Data (e.g., Multiprocessors, Distributed Systems) |

(📌 **Diagram**: Two axes — Instruction Stream vs. Data Stream — resulting in the four categories.)

---

### **Q3: Illustrate the shared memory challenges in multiprocessor environments.**

Shared memory challenges:
- **Cache coherence**: Different processors' caches may become inconsistent.
- **Synchronization**: Properly managing access to shared variables (locks, semaphores).
- **False sharing**: Two processors modify different data on the same cache line, causing unnecessary cache invalidations.
- **Memory consistency**: Maintaining the expected order of read/write operations across processors.
- **Scalability**: Increasing number of processors leads to memory access bottlenecks.

---

### **Q4: For a 6 node Ring and linear network calculate the total number of links, diameter, bisection width and arc connectivity.**

- **Ring**:
  - Links = Nodes = **6**
  - Diameter = floor(N/2) = floor(6/2) = **3**
  - Bisection width = 2
  - Arc connectivity = 2

- **Linear** (line):
  - Links = Nodes - 1 = 6 - 1 = **5**
  - Diameter = Nodes - 1 = 6 - 1 = **5**
  - Bisection width = 1
  - Arc connectivity = 1

---

### **Q5: Illustrate parallel compare-exchange and compare-split operations.**

- **Compare-Exchange**:  
  - Two elements are compared; if needed, they are exchanged to be in ascending (or descending) order.  
  - Example: Compare A and B; if A > B, swap them.

- **Compare-Split**:  
  - Each processor holds a list of elements.
  - Pairs of processors exchange and split their combined lists — one keeps the smaller half, the other keeps the larger half.

(📌 Both operations are building blocks for parallel sorting algorithms like Bitonic Sort and Odd-Even Transposition Sort.)


---

## **Q6: List various parallel algorithm models with examples.**

Parallel algorithm models describe **strategies** to design parallel programs:

| Model | Description | Example |
|:------|:------------|:--------|
| **Data Parallelism** | Same operation applied simultaneously on different data items. | Vector addition on GPU. |
| **Task Parallelism** | Different tasks run in parallel, possibly on different data. | Web server handling multiple requests. |
| **Master-Slave Model** | One master distributes tasks to worker slaves. | Parallel matrix multiplication: master distributes rows. |
| **Divide-and-Conquer** | Split problem into subproblems, solve independently, and combine. | Parallel Merge Sort, Quick Sort. |
| **Pipeline Model** | Computation divided into stages where different stages are processed in parallel. | Assembly line scheduling, Image processing. |
| **Work Pool Model (Bag of Tasks)** | Tasks are dynamically assigned to processors from a pool. | Parallel graph traversal. |

🔵 **Note**: Choosing the right model is crucial for performance, scalability, and programming simplicity.

---

## **Q7: Illustrate computing parallel prefix sums on an eight-node hypercube.**

**Parallel prefix sum (scan)**: Given an array `A = [a0, a1, a2, ..., a7]`, compute prefix sums: `[a0, a0+a1, a0+a1+a2, ..., a0+a1+...+a7]`.

Steps on **8-node hypercube**:
1. **Dimension 0 (1st step)**: Each node exchanges with neighbor differing in bit 0 (LSB).  
   - Node 0 and 1, Node 2 and 3, etc.
2. **Dimension 1 (2nd step)**: Exchange across nodes differing in bit 1.
   - Nodes 0-2, 1-3, 4-6, 5-7.
3. **Dimension 2 (3rd step)**: Exchange across nodes differing in bit 2.
   - Nodes 0-4, 1-5, 2-6, 3-7.

Each node **adds received value** to its current sum at each step.

- **Time complexity**: O(log n) for n nodes.

✅ **Final output** at each node = cumulative sum up to that point.

---

## **Q8: Depict Multiprocessor vs. Multicomputer Systems**

| Feature | Multiprocessor | Multicomputer |
|:--------|:---------------|:--------------|
| Architecture | Shared memory | Distributed memory |
| Communication | Memory access (fast) | Message passing (slower) |
| Coupling | Tightly coupled | Loosely coupled |
| Examples | Multi-core CPUs (Intel i9) | Cluster of PCs, Google Data Centers |
| Cost | High (specialized hardware) | Can be built from commodity machines |
| Fault Tolerance | Harder (single point of failure) | Easier (failover across nodes) |

📌 **Summary**:  
- Multiprocessor → Single physical machine, many processors.  
- Multicomputer → Multiple networked computers working together.

---

## **Q9: Suppose we were to add n numbers on two computers (each computer adds n/2 numbers initially). Compute Parallel Execution Time.**

Steps:
1. First Computer (P1) sums first **n/2** numbers.
2. Second Computer (P2) sums second **n/2** numbers.
3. P2 sends its partial sum to P1.
4. P1 adds both partial sums.

Let:
- **Tcomp** = time to add two numbers.
- **Tcomm** = communication time to send result.

Then:
- Local sums on each = (n/2 - 1) additions.
- Final addition at P1 = 1 addition.

Thus, **Parallel Execution Time (Tp)**:
\[
T_p = T_{comp} \times \left( \frac{n}{2} - 1 + 1 \right) + T_{comm} = T_{comp} \times \frac{n}{2} + T_{comm}
\]

---

## **Q10: Compute Parallel run Time for adding a list of n numbers using separate send()s and recv()s with m processors (assume n is a power of 2).**

Steps:
1. Initially, each processor holds approximately **n/m** numbers.
2. Each processor computes a **local sum** (n/m - 1 additions).
3. Then **log₂(m)** communication steps:
   - In each step, processors pair up: send partial sum, add received sum.

Thus, Parallel Runtime:
\[
T_p = T_{comp} \times \left( \frac{n}{m} - 1 + \log_2(m) \right) + T_{comm} \times \log_2(m)
\]
where:
- **Tcomp** = time per addition.
- **Tcomm** = time per communication step.

📌 **Important Observations**:
- More processors = less computation per processor but more communication.
- Optimal **m** is such that computation time balances communication time.


---

## **Q11: Calculate the total communication cost or time taken by the one-to-all broadcast operation on Ring, Mesh, and Hypercube networks. (p processes, m words)**

### **Ring**:
- Each message is sent to a neighbor in one direction.
- Total steps = **p-1** (each node sends once to next).
- Communication cost:  
\[
T_{Ring} = (p-1)(t_s + m \times t_w)
\]
where:
- \(t_s\) = startup time per message,
- \(t_w\) = time per word.

---

### **Mesh**:
- Broadcast in 2 phases: row-wise and column-wise.
- Steps = **2(\sqrt{p} - 1)** for \(\sqrt{p} \times \sqrt{p}\) mesh.
- Communication cost:  
\[
T_{Mesh} = 2(\sqrt{p} - 1)(t_s + m \times t_w)
\]

---

### **Hypercube**:
- Requires **log₂(p)** steps.
- In each step, half the nodes that know the data send it to another node.
- Communication cost:  
\[
T_{Hypercube} = \log_2(p)(t_s + m \times t_w)
\]

---
🔵 **Observation**:  
- **Ring** is slowest (linear).
- **Hypercube** is fastest (logarithmic).

---

## **Q12: Calculate the parallel run time for tree-structured global sum (n integers across p processors, each processor has q = n/p integers).**

Steps:
1. Each processor sums its local q numbers: \(q-1\) additions.
2. Then global reduction happens in a tree structure in \(\log_2(p)\) steps:
   - Each step: partial sums sent upward and added.

Parallel Runtime:
\[
T_p = T_{comp} \times (q-1 + \log_2(p)) + T_{comm} \times \log_2(p)
\]
where:
- \(T_{comp}\) = time per addition,
- \(T_{comm}\) = time per communication.

---
🔵 **Key**:  
Tree structure greatly reduces communication time compared to linear summing.

---

## **Q13: Write an algorithm for the parallel formulation of odd-even transposition sort on an n-process ring and analyze its run time.**

### **Algorithm Steps**:
1. Each process holds one number initially.
2. For **n** phases:
   - If phase is **odd**: processes with **odd indices** send and compare-swap with neighbor at i+1.
   - If phase is **even**: **even indices** do the same.

### **Pseudocode**:
```plaintext
for phase = 1 to n do
   if phase is odd then
      for i = 1 to n-1 step 2
         compare_exchange(process[i], process[i+1])
   else
      for i = 0 to n-2 step 2
         compare_exchange(process[i], process[i+1])
```
**Compare-exchange**: if out of order, swap values.

### **Run Time Analysis**:
- **n phases** total.
- In each phase:
  - Communication with neighbor (send/receive).
  - Compare and possible swap (constant time).

Thus:
\[
T_p = n \times (T_{comm} + T_{comp})
\]
where:
- \(T_{comm}\) = communication time per neighbor,
- \(T_{comp}\) = comparison/swap time.

---
🔵 **Key**:  
- Simple to implement.
- Good for **small n**, but not efficient for large n.

---

## **Q14: Given an input array of size n=8 [3, 7, 2, 5, 8, 1, 6, 4], perform the first step of Bitonic Sort to create a Bitonic sequence. What is the resulting sequence?**

Bitonic Sort First Step:  
- Make two halves:
  - First half sorted **ascending**.
  - Second half sorted **descending**.

Given array:  
`[3, 7, 2, 5, 8, 1, 6, 4]`

- First half (indices 0–3): sort ascending → `[2, 3, 5, 7]`
- Second half (indices 4–7): sort descending → `[8, 6, 4, 1]`

Thus, resulting **Bitonic sequence**:
\[
[2, 3, 5, 7, 8, 6, 4, 1]
\]

---
🔵 **Key**:  
**Bitonic sequence** = monotonically increasing then decreasing (or vice versa).

---

## **Q15: Parallelism is applicable to Bubble Sort? Explain parallel odd-even transposition sorting algorithm in a distributed-memory environment.**

### **Parallelism in Bubble Sort**:
- Traditional Bubble Sort is hard to parallelize because comparisons depend on previous swaps.
- **BUT**, **Odd-Even Transposition Sort** is a parallel variant.

---

### **Parallel Odd-Even Transposition Sort (Distributed-Memory)**:
- Each process holds one or more elements.
- Alternate between odd and even phases:
  - **Odd phase**: compare (P1,P2), (P3,P4), etc.
  - **Even phase**: compare (P0,P1), (P2,P3), etc.
- After n phases, the array is sorted.

---
**Steps**:
1. In **odd phase**, processes with odd rank send and receive values with their right neighbor and swap if necessary.
2. In **even phase**, even ranks do the same.

Each **compare-exchange** is independent for that phase, so can happen **in parallel**!

---

**Advantages**:
- Easy to implement.
- Scales decently for small arrays.

**Drawbacks**:
- Still **O(n²)** comparisons — not suitable for very large datasets.

---

