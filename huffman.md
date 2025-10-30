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
    * **Adaptive:** Builds knowledge of the data *as it works*.

---

### Binary Codes

* **Binary Code:** A system representing data using two symbols, '0' and '1'.
* **Codeword:** The binary string representing a specific character.
* **Fixed-Length Code:** All codewords have the same length.
    * **Example:** ASCII. If you have 32 unique characters, you need $2^5 = 32$, so every codeword is 5 bits long.
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
    * **Codewords:** The path from the **root** to a leaf. A '0' represents a left turn, and a '1' represents a right turn.
    * **Optimal Code Tree:** An optimal code is *always* represented by a **full binary tree** (where every internal node has exactly two children).

* **Calculating Total Size:** The total bits $B(D)$ for a file is the sum of (each character's frequency $\times$ its codeword length).
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
3.  Repeat until one node remains. The last node in the queue is the root of the completed Huffman tree.

#### Implementation and Runtime

* **Naive:** Using an array and searching for the minimums takes $O(n^2)$ time.
* **Efficient:** Using a **min-priority queue** (min-heap) achieves a runtime of **$O(n \log n)$**. This is because each of the $n$ INSERT and $2(n-1)$ EXTRACTMIN operations takes $O(\log n)$ time.

#### Correctness and Optimality

* **Correct (Prefix-Free):** Yes. The algorithm is correct because all characters are at leaf nodes, so no character's path (code) can be a prefix of another's.
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

**Final Codewords (assigning 0=left, 1=right):**
*(Based on the tree in Figure 2)*

* `a`: 0
* `b`: 101
* `c`: 100
* `d`: 111
* `e`: 1101
* `f`: 1100

*(Note: The `Var. Length Code` table in the document has a different set of codes. The codes above are derived from the tree in Figure 2, which is the correct output for the algorithm walkthrough described.)*

## 4. Practice Questions (Quiz Prep) ❓

Test your understanding with these questions based on the notes.

1.  What is the key difference between lossless and lossy compression?
2.  Why does a variable-length code like `a=0`, `b=01` cause problems?
3.  What is the defining property of a "prefix-free" code?
4.  In a binary tree representing a prefix-free code, where are the characters *always* located?
    * (a) The root
    * (b) Internal nodes
    * (c) Leaf nodes
    * (d) All nodes
5.  An optimal prefix code is *always* represented by what specific type of binary tree?
6.  What is the "greedy choice" that Huffman's algorithm makes at each step?
7.  What data structure is used to improve the runtime of Huffman's algorithm to $O(n \log n)$?
8.  **True or False:** The Fano-Shannon algorithm is guaranteed to produce an optimal prefix-free code.
9.  If you have two characters, `x` and `y`, with the lowest frequencies, what does the "Greedy Choice Property" guarantee about them in an optimal tree?
10. If character 'a' has a frequency of 50 and character 'b' has a frequency of 5, which character will have a *longer* codeword in a Huffman code?
