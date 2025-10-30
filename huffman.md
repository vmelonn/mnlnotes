# Study Guide: Huffman Codes

This guide covers the fundamental concepts of Huffman coding, from basic data compression theory to the algorithm's implementation and optimality, based on the provided lecture notes.

## 1. Key Concepts 🧠

### Data Compression

* **Data Compression:** The process of reducing a file's size for easier storage or transmission.
* **Lossless Compression:** The original data can be *perfectly* reconstructed from the compressed file ($D = D'$).
    * **Examples:** Huffman coding, Lempel-Ziv (.gif).
* **Lossy Compression:** The reconstructed data is an *approximation* of the original ($D \approx D'$).
    * **Examples:** .mp3, .jpg, .mpg.
* **Non-adaptive vs. Adaptive:**
    * **Non-adaptive:** Assumes *prior knowledge* of data, like character frequencies.
    * **Adaptive:** Builds knowledge of the data *as it works*, assuming no prior knowledge.

---

### Binary Codes

* **Binary Code:** A system representing data using two symbols, '0' and '1'.
* **Codeword:** The binary string representing a specific character.
* **Fixed-Length Code:** All codewords have the same length.
    * **Example:** ASCII and Unicode. If you have 32 unique characters, you need $2^5 = 32$, so every codeword is 5 bits long.
* **Variable-Length Code:** Codewords can have different lengths.
    * **Goal:** Give shorter codes to more frequent characters to save space.
* **The Ambiguity Problem:** Simple variable-length codes can be ambiguous.
    * **Example:** If `a = 1` and `b = 110`, the string `1101` could be decoded as `bad` or `acad`. This is because `a`'s code is a *prefix* of `b`'s code.

---

### Prefix-Free Codes

* **Definition:** A code where **no codeword is a prefix of any other codeword**.
* **Benefit:** This property ensures there is no ambiguity and the code is **uniquely decipherable**.
* **Tree Visualization:** Prefix-free codes can be represented by a binary tree.
    * **Characters:** Stored *only* at the **leaf nodes**.
    * **Codewords:** The path from the **root** to a leaf. A '0' represents a left turn (edge) and a '1' represents a right turn (edge).
    * **Optimal Code Tree:** An optimal code is *always* represented by a **full binary tree** (where every internal node has exactly two children).
* **Calculating Total Size:** The total bits $B(D)$ for a file is the sum of (each character's frequency $\times$ its codeword length/depth).
    * $B(D) = \sum f(a_i) \times (\text{depth of } a_i \text{ in tree})$.
* **Problem Goal:** Find a prefix-free code (a tree) that **minimizes this total size** $B(D)$.

## 2. The Algorithms ⚙️

### Fano-Shannon Code

* A **top-down** "divide and conquer" approach.
* It recursively splits the set of characters into two groups of *roughly equal total frequency*.
* **Is it optimal?** No. It is **not guaranteed** to produce an optimal code.

---

### Huffman Code

* A **greedy algorithm** that constructs an optimal prefix-free code from the **bottom up**.
* **Greedy Choice:** At every step, it merges the two available nodes (trees) with the **lowest frequencies**.

#### Algorithm Steps

1.  Create a leaf node for each character, storing its frequency. Put these nodes into a min-priority queue (which sorts them by frequency).
2.  While there is more than one node in the queue:
    a.  **Extract** the two nodes with the *lowest* frequencies, $x$ and $y$.
    b.  **Create** a new internal node $z$.
    c.  Set $x$ and $y$ as the left and right children of $z$.
    d.  Set the frequency of $z$ to be $f(z) = f(x) + f(y)$.
    e.  **Insert** the new node $z$ back into the priority queue.
3.  The last node remaining in the queue is the root of the completed Huffman tree.

#### Implementation and Runtime

* **Naive:** Using an array and searching for the minimums (2 FINDMIN operations per iteration) takes $O(n^2)$ time.
* **Efficient:** Using a **min-priority queue** (min-heap) achieves a runtime of **$O(n \log n)$**. This is because each of the $n$ INSERT and $2(n-1)$ EXTRACTMIN operations takes $O(\log n)$ time.

#### Correctness and Optimality

* **Correct (Prefix-Free):** Yes. The algorithm is correct because all characters are at leaf nodes. The path from the root to a leaf cannot be a prefix of a path to another leaf.
* **Optimal:** Yes. The algorithm is proven to be optimal because it exhibits:
    1.  **Greedy Choice Property:** There is always an optimal tree where the two least frequent symbols are siblings. Huffman's algorithm makes this choice.
    2.  **Optimal Substructure:** An optimal tree for the full problem is built from an optimal solution to a subproblem (the tree with the two lowest nodes merged).

## 3. Algorithm Walkthrough (Example) 🚶‍♂️

Let's build the Huffman tree for the data from Example 2.

* **Frequencies:** `a: 45`, `b: 13`, `c: 12`, `d: 16`, `e: 9`, `f: 5`

**Steps (using a min-priority queue):**

1.  **Initial Queue:**
    `[ (f: 5), (e: 9), (c: 12), (b: 13), (d: 16), (a: 45) ]`

2.  **Merge 1:** Extract `(f: 5)` and `(e: 9)`.
    * Create new node `z1` with frequency $5 + 9 = 14$.
    * **Queue:** `[ (c: 12), (b: 13), (z1: 14), (d: 16), (a: 45) ]`

3.  **Merge 2:** Extract `(c: 12)` and `(b: 13)`.
    * Create new node `z2` with frequency $12 + 13 = 25$.
    * **Queue:** `[ (z1: 14), (d: 16), (z2: 25), (a: 45) ]`

4.  **Merge 3:** Extract `(z1: 14)` and `(d: 16)`.
    * Create new node `z3` with frequency $14 + 16 = 30$.
    * **Queue:** `[ (z2: 25), (z3: 30), (a: 45) ]`

5.  **Merge 4:** Extract `(z2: 25)` and `(z3: 30)`.
    * Create new node `z4` with frequency $25 + 30 = 55$.
    * **Queue:** `[ (a: 45), (z4: 55) ]`

6.  **Merge 5 (Final):** Extract `(a: 45)` and `(z4: 55)`.
    * Create `ROOT` node with frequency $45 + 55 = 100$.
    * **Queue:** `[ (ROOT: 100) ]`

The algorithm is complete. The resulting tree matches Figure 2.

**Final Codewords (assigning 0=left, 1=right from tree):**

* `a`: 0
* `b`: 101
* `c`: 100
* `d`: 111
* `e`: 1101
* `f`: 1100

*(These codes are derived from the tree in Figure 2. The table in the document shows a different variable-length code, which the tree in Figure 2 is an improvement upon).*

## 4. Practice Questions (with Answers) ❓

1.  **What is the key difference between lossless and lossy compression?**
    * **Answer:** In lossless compression, the original data can be recovered *exactly* ($D=D'$). In lossy compression, the recovered data is only an *approximation* ($D' \approx D$).

2.  **Why does a variable-length code like `a=0`, `b=01` cause problems?**
    * **Answer:** It causes ambiguity. The code for 'a' (`0`) is a prefix of the code for 'b' (`01`). A string like `01` could be decoded as 'ab' or just 'b' (if 'a' was 0 and 'b' was 1).

3.  **What is the defining property of a "prefix-free" code?**
    * **Answer:** No codeword is a prefix of any other codeword.

4.  **In a binary tree representing a prefix-free code, where are the characters *always* located?**
    * **Answer:** At the leaf nodes.

5.  **An optimal prefix code is *always* represented by what specific type of binary tree?**
    * **Answer:** A full binary tree (where every non-leaf node has two children).

6.  **What is the "greedy choice" that Huffman's algorithm makes at each step?**
    * **Answer:** It chooses the two trees (nodes) with the lowest frequencies to merge.

7.  **What data structure is used to improve the runtime of Huffman's algorithm to $O(n \log n)$?**
    * **Answer:** A min-priority queue (or min-heap).

8.  **True or False:** The Fano-Shannon algorithm is guaranteed to produce an optimal prefix-free code.
    * **Answer:** False. It is not guaranteed to be optimal.

9.  **If you have two characters, `x` and `y`, with the lowest frequencies, what does the "Greedy Choice Property" guarantee about them in an optimal tree?**
    * **Answer:** It guarantees that *there exists* an optimal tree where `x` and `y` are siblings (have the same parent).

10. **If character 'a' has a frequency of 50 and character 'b' has a frequency of 5, which character will have a *longer* codeword in a Huffman code?**
    * **Answer:** Character 'b'. The Huffman algorithm gives shorter codes to more frequent characters and longer codes to less frequent ones.

---

## 5. Glossary of Undefined Terms 📖

This section explains technical terms used in the notes that are not explicitly defined.

* **Greedy Algorithm:** An algorithm that makes the *locally optimal* choice at each step, hoping it will lead to a *globally optimal* solution. Huffman's algorithm is a prime example.
* **Min-Priority Queue (Min-Heap):** A special data structure that efficiently stores elements with associated priorities (in this case, frequencies). It allows you to quickly find and remove the element with the *minimum* priority (the lowest frequency).
* **Divide and Conquer:** An algorithmic strategy where a problem is recursively broken down into smaller, similar subproblems. The solutions to the subproblems are then combined to solve the original problem. This is used by the Fano-Shannon code.
* **Runtime ($O(n^2)$, $O(n \log n)$):** This is "Big O Notation." It describes how the time an algorithm takes to run scales with the size of its input ($n$). $O(n^2)$ (quadratic) is much slower than $O(n \log n)$ (log-linear) for large inputs.
* **ASCII / Unicode:** Standardized examples of *fixed-length codes* used to represent text characters in computers.
* **Optimal Substructure:** A key property of a problem. It means that an optimal solution to the overall problem can be constructed from optimal solutions to its subproblems.
* **Greedy Choice Property:** A key property of a problem. It means that a globally optimal solution can be arrived at by making a sequence of locally optimal (greedy) choices.

---

## 6. Comprehensive Quiz Questions 📝

### Multiple Choice Questions (MCQs)

**1. Which of these is an example of *lossy* compression?**
* (a) Huffman coding
* (b) Lempel-Ziv
* (c) .gif
* (d) .jpg
* **Answer: (d) .jpg** (Examples of lossy compression include .jpg, .mp3, and .mpg. Huffman and Lempel-Ziv/.gif are lossless).

**2. ASCII is an example of...**
* (a) A variable-length code
* (b) A fixed-length code
* (c) A prefix-free code
* (d) A lossy code
* **Answer: (b) A fixed-length code**.

**3. In a prefix-free code, no codeword is a _______ of another codeword.**
* (a) Suffix
* (b) Reversal
* (c) Prefix
* (d) Child
* **Answer: (c) Prefix**.

**4. In the binary tree representation of a prefix-free code, characters are *only* found at the...**
* (a) Root node
* (b) Leaf nodes
* (c) Internal nodes
* (d) Nodes with a '0' edge
* **Answer: (b) Leaf nodes**.

**5. An optimal prefix code is always represented by what kind of tree?**
* (a) A binary search tree
* (b) A complete binary tree
* (c) A full binary tree
* (d) A balanced binary tree
* **Answer: (c) A full binary tree**.

**6. The runtime of the Huffman algorithm using a min-priority queue is...**
* (a) $O(n)$
* (b) $O(n^2)$
* (c) $O(n \log n)$
* (d) $O(2^n)$
* **Answer: (c) $O(n \log n)$**.

**7. Which algorithm uses a top-down, divide and conquer approach but is *not* guaranteed to be optimal?**
* (a) Huffman
* (b) Lempel-Ziv
* (c) Fano-Shannon
* (d) ASCII
* **Answer: (c) Fano-Shannon**.

**8. At each step, the Huffman algorithm merges the two nodes with the...**
* (a) Highest frequencies
* (b) Lowest frequencies
* (c) Deepest depth
* (d) Shortest codewords
* **Answer: (b) Lowest frequencies**.

**9. Given `a=1`, `b=110`, `c=10`, `d=111`, why is the string `1101111` ambiguous?**
* (a) Because `c` is a prefix of `d`.
* (b) Because `a` is a prefix of `b`.
* (c) Because the code is not full.
* (d) The string is not ambiguous.
* **Answer: (b) Because `a` is a prefix of `b`**. (The string could be `bad` or `acad`).

**10. How is the total number of bits $B(D)$ for an encoded file calculated using the tree?**
* (a) The sum of all frequencies in the tree.
* (b) The sum of the depths of all leaves.
* (c) The sum of (frequency $\times$ depth) for all internal nodes.
* (d) The sum of (frequency $\times$ depth) for all leaf nodes (characters).
* **Answer: (d) The sum of (frequency $\times$ depth) for all leaf nodes (characters)**.

### Short Answer Questions

1.  **What is the main motivation for using variable-length codes?**
    * **Answer:** To achieve compression by assigning shorter codewords to more frequent characters and longer codewords to less frequent ones.

2.  **How does the Huffman algorithm work? (Summarize the steps).**
    * **Answer:** It builds a tree from the bottom up. It repeatedly finds the two nodes (starting with characters) with the lowest frequencies, merges them under a new parent node (whose frequency is the sum of the children), and continues this process until only one node (the root) remains.

3.  **What two properties must a problem have to be provably solvable by a greedy algorithm like Huffman's?**
    * **Answer:** The **greedy-choice property** and the **optimal-substructure property**.

4.  **What is the difference between an adaptive and non-adaptive compression algorithm?**
    * **Answer:** A non-adaptive algorithm needs prior knowledge of the data (like character frequencies) to work. An adaptive algorithm builds this knowledge as it goes, without any prior information.

5.  **Why is the Huffman code guaranteed to be prefix-free?**
    * **Answer:** Because by definition of the algorithm, all the characters (alphabets) are placed at the leaf nodes. Since a codeword is the path from the root to a leaf, no path can be a prefix of another path.

6.  **What is the runtime of the "naive" Huffman implementation, and why?**
    * **Answer:** $O(n^2)$. This is because in each of the $n-1$ iterations, it has to perform two "FINDMIN" operations, which can take $O(n)$ time each if using a simple list or array.

7.  **What is the total number of leaves and internal nodes in an optimal Huffman tree for an alphabet of $C$ characters?**
    * **Answer:** It has exactly $C$ leaves (one for each character) and exactly $C-1$ internal nodes.
