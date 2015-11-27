A _correct_ algorithm is one that always terminates in the correct answer, no matter the inputs.

The number of operations for an algorithm is typically a function of its inputs, typically denoted $n$.

## Major algorithm design paradigms:

- __Divide and conquer__: break up the problem into subproblems which can be recursively solved, then combine the results of the subproblems in some way to form the solution for the original problem.
- __Greedy__: (to do)
- __Dynamic programming__: (to do)


## Merge sort

A canonical example of divide and conquer, which improves over other sorting algorithms such as:

- _selection sort_: each pass over the array identifies the minimum element of the unsorted elements
- _insertion sort_: iterates over each element in the list, inserting it into the proper position of the elements it has already looked at (i.e. the "prefix", or head, or front, of the array)
- _bubble sort_: look at pairs of elements in the array and swap those out of order until the entire array is sorted

TODO merge sort graphic

With merge sort, we take an unsorted array and split in half, then recursively apply merge sort to each half. Then the sorted halves are re-combined ("merged") to give the final sorted array.

This merge step is accomplished by iterating over the two sorted halves in parallel and comparing their values, copying over the smallest of the two to the final sorted array.

Given two sorted halves `A` and `B`, with our final sorted array `C`, The basics of the merging subroutine of the algorithm looks like:

    for k in range(n):
        if A[i] < B[j]:
            C[k] = A[i]
            i += 1
        else if B[j] < A[i]:
            C[k] = B[j]
            j += 1

This is a simplified version, of course, which does not capture end cases (e.g. if `A` is shorter than `B`) nor does it capture when `A[i] == B[j]`.

Let's consider how many operations there are of this (simplified) merge portion of merge sort.

- increment `k`
- compare `A[i]` and `B[j]`
- set `C[k]`
- increment `i` or `j`

So each loop has $4n$ operations.

We also have two other operations where we initialize `i` and `j`.

So in total, this simplified merge subroutine has $4n + 2$ operations.

You could argue that we need an additional operation in the loop for comparing `k` to `n` to see if the loop should terminate (in which case we would have $5n + 2$), but in the end, these small differences don't mean much.

We can assume that $n \geq 1$, so we can say that the runtime for this simplified merge subroutine is $\leq 6n$.


$\log_2 n$ can be thought of as the number of times you divide a value by 2 until you get below or equal to 1. This captures the recursive component of the algorithm, i.e. for there are $\log_2 n$ levels of recursion since it recurses by dividing its input into two.

Intuitively, each level reduces the input by a factor of 2. For instance, if $n=8$, then the first split would have inputs of $n=4$, then the next split would have inputs of $n=2$, then the last split would have inputs of $n=1$. That's three levels (the first level doesn't count, as it is considered the external call to the function), i.e. $\log_2 8 = 3$.

Each level has $2^j$ subproblems with inputs of length $\frac{n}{2^j}$ (e.g. if $n=8$, the first level, $i=1$, has $2^1=2$ different subproblems with inputs of length $\frac{8}{2^1} = 4$).

So the complete analysis would works as follow:

For a level $j$, there are $2^j$ subproblems, and each subproblem really only runs the merge subroutine, which we have already approximated as $6m$ operations, where $m$ is the input size. We know that each of $j$'s subproblems has an input of size $\frac{n}{2^j}$, that is, each subproblem has $6 \frac{n}{2^j}$ operations. So the level as a total has (as an upper bound) the following number of operations:

$$
2^j 6 \frac{n}{2^j}
$$

Which then simplifies:

$$
\require{cancel}
\cancel{2^j} 6 \frac{n}{\cancel{2^j}} = 6n
$$

So the number of operations is independent of the level's depth!

Thus, the total number of operations is just the number of levels times the number of operations for a level:

$$
6n (\log_2(n) + 1) = 6n \log_2 n + 6n
$$

(We include a $+1$ for the final merge step)

### Kinds of algorithmic analysis

- worst-case analysis: the upper bound running time that is true for any arbitrary input of length $n$
- average-case analysis: assuming that all input are equally likely, the average running time
- benchmarks: runtime on an agreed-upon set of "typical" inputs

Average-case analysis and benchmarks requires some domain knowledge about what inputs to expect. When you want to do a more "general purpose" analysis, worst-case analysis is preferable.

### Other algorithmic analysis notes

- Constant factors are typically ignored - this simplifies things, these constants can vary according to a lot of different factors (architecture, compiler, programmer), and in the end, it doesn't have much of an effect on the analysis.
- We focus on __asymptotic analysis__; that is, we focus on large input sizes.
    - Algorithms which are inefficient for large $n$ may be better on small $n$ when compared to algorithms that perform well for large $n$. For example, insertion sort has an upper bound runtime of $\frac{n^2}{2}$, which, for small $n$ (e.g. $n < 90$), is better than merge sort. This is because constant factors are more meaningful with small inputs. Anyways, with small $n$, it often doesn't really matter what algorithm you use, since the input is so small, there are unlikely to be significant performance differences, so analysis of small input sizes is not very valuable (or interesting).

Thus we define a "fast" algorithm as one in which the worst-case running time grows slowly with input size.

## Asymptotic Analysis

With asymptotic analysis, we suppress constant factors and lower-order terms, since they don't matter much for large inputs, and because the constant factors can vary quite a bit depending on the architecture, compiler, programmer, etc.

For example, we'd take our previous upper bound for merge sort, $6n \log_2 n + 6n$ and rewrite it as just $n \log n$ ($\log$ typically implies $\log_2$).

Then we say the running time for merge sort is $O(n \log n)$, said "big-oh of $n \log n$", the $O$ implies that we have dropped the constant factors and lower-order terms.

### Loop examples

Consider the following algorithm for finding an element in an array:

```python
def func(i, arr):
    for el in arr:
        if el == i:
            return True
    return False
```

This has the running time of $O(n)$ since, in the worst case, it checks every item.

Now consider the following:

```python
def func2(i, arr):
    return func(i, arr), func(i, arr)
```

This still has the running time of $O(n)$, although it has twice the number of operations (i.e. $\sim 2n$ operations total), we drop the constant factor $2$.

Now consider the following algorithm for checking if two arrays have a common element:

```python
def func3(arr1, arr2):
    for el1 in arr1:
        for el2 in arr2:
            if el1 == el2:
                return True
    return False
```

This has a runtime of $O(n^2)$, which is called a __quadratic time__ algorithm.

The following algorithm for checking duplicates in an array also has a runtime of $O(n^2)$, again due to dropping constant factors:

```python
def func4(arr):
    for i, el1 in enumerate(arr):
        for el2 in arr[i:]:
            if el1 == el2:
                return True
    return False
```

### Big-Oh formal definition

Say we have a function $T(n)$, $n \geq 0$, which is usually the worst-case running time of an algorithm.

We say that $T(n) = O(f(n))$ if and only if there exist constants $c, n_0 > 0$ such that $T(n) \leq c f(n)$ for all $n \geq n_0$.

That is, we can multiply $f(n)$ by some constant $c$ such that there is some value $n_0$, after which $T(n)$ is always below $c f(n)$.

For example: we demonstrated that $6n \log_2 n + 6n$ is the worst-case running time for merge sort. For merge sort, this is $T(n)$. We described merge sort's running time in big-oh notation with $O(n \log n)$. This is appropriate because there exists some constant $c$ we can multiply $n \log n$ by such that, after some input size $n_0$, $c f(n)$ is always larger than $T(n)$. In this sense, $n_0$ defines a sufficiently large input.

As a simple example, we can prove that $2^{n+10} = O(2^n)$.

So the inequality is:

$$
2^{n+10} \leq c 2^n
$$

We can re-write this:

$$
2^{10} 2^n \leq c 2^n
$$

Then it's clear that if we set $c=2^{10}$, this inequality holds, and it happens to hold for all $n$, so we can just set $n_0 = 1$. Thus $2^{n+10} = O(2^n)$ is in fact true.

### Big-Omega notation

$T(n) = \Omega(f(n))$ if and only if there exist constants $c, n_0 > 0$ such that $T(n) \geq c f(n)$ for all $n \geq n_0$.

That is, we can multiply $f(n)$ by some constant $c$ such that there is some value $n_0$, after which $T(n)$ is always _above_ $c f(n)$.

### Big-Theta notation

$T(n) = \Theta(f(n))$ if and only if $T(n) = O(f(n))$ _and_ $T(n) = \Omega(f(n))$. That is, $T(n)$ eventually stays sandwiched between $c_1 f(n)$ and $c_2 f(n)$ after some value $n_0$.

### Little-Oh notation

Stricter than big-oh, in that this must be true for all positive constants.

$T(n) = o(f(n))$ if and only if for _all_ constants $c>0$, there exists a constant $n_0$ such that $T(n) \leq c f(n)$ for all $n \geq n_0$.

## The divide-and-conquer paradigm

This consists of:

- Divide the problem into smaller subproblems. You do not have to literally divide the problem in the algorithm's implementation; this may just be a conceptual step.
- Compute the subproblems using recursion.
- Combine the subproblem solutions into the problem for the original problem.

### The Master Method/Theorem

The Master Method provides an easy way of computing the upper-bound runtime for a divide-and-conquer algorithm, so long as it satisfies the assumptions:

Assume that subproblems have equal size and the recurrence has the format:

- Base case: $T(n) \leq c$, where $c$ is a constant, for all sufficiently small $n$.
- For all larger $n$: $T(n) \leq a T(\frac{n}{b}) + O(n^d)$, where $a$ is the number of recursive calls, $a \geq 1$, each subproblem has input size $\frac{n}{b}$, $b > 1$, and outside each recursive call, we do some additional $O(n^d)$ of work (e.g. a combine step), parameterized by $d$, $d \geq 0$.

$$
T(n) =
\begin{cases}
O(n^d \log_n) & \text{if $a = b^d$} \\
O(n^d) & \text{if $a < b^d$} \\
O(n^{\log_b a}) & \text{if $a > b^d$}
\end{cases}
$$

Note that the logarithm base does not matter in the first case, since it just changes the leading constant (which doesn't matter in Big-Oh notation), whereas in the last case the base _does_ matter, because it has a more significant effect.

To use the master method, we just need to determine $a,b,d$.

For example:

With merge sort, there are two recursive calls (thus $a=2$), and the input size to each recursive call is half the original input (thus $b=2$), and the combine step only involves the merge operation ($d=1$).

Thus, working out the master method inequality, we get $2 = 2^1$, i.e. $a = b^d$, thus:

$$
T(n) = O(n \log_n)
$$

#### Proof of the Master Method

(Carrying over the previously-stated assumptions)

For simplicity, we'll also assume that $n$ is a power of $b$, but this proof holds in the general case as well.

In the recursion tree, at each level $j=0,1,2,\dots,\log_b n$, there are $a^j$ subproblems, each of size $n/b^j$.

At a level $j$, the total work, not including the work in recursive calls, is:

$$
\leq a^j c (\frac{n}{b^j})^d
$$

Note that the $a$ and $b$ terms are dependent on the level $j$, but the $c,n,d$ terms are not. We can rearrange the expression to separate those terms:

$$
\leq cn^d (\frac{a}{b^d})^j
$$

To get the total work, we can sum over all the levels:

$$
\leq cn^d \sum_{j=0}^{\log_b n} (\frac{a}{b^d})^j
$$

We can think of $a$ as the rate of subproblem proliferation (i.e. how the number of subproblems grow with level depth) and $b^d$ as the rate of work shrinkage per subproblem.

There are three possible scenarios, corresponding to the master method's three cases:

- If $a < b^d$, then the amount of work decreases with the recursion level.
- If $a > b^d$, then the amount of work increases with the recursion level.
- If $a = b^d$, then the amount of work stays the same with the recursion level.

If $a = b^d$, then the summation term in the total work expression, $\sum_{j=0}^{\log_b n} (\frac{a}{b^d})^j$, simply becomes $\log_b n + 1$, thus the total work upper bound in that case is just $c n^d (\log_b n + 1)$, which is just $O(n^d \log n)$.

A geometric sum for $r \neq 1$:

$$
1 + r + r^2 + r^3 + \dots + r^k
$$

Can be expressed in the following closed form:

$$
\frac{r^{k+1} - 1}{r-1}
$$

If $r < 1$ is constant, then this is $\leq \frac{1}{1-r}$ (i.e. it is some constant independent of $k$).
If $r > 1$ is constant, then this is $\leq r^k(1 + \frac{1}{r-1})$, where the last term $(1 + \frac{1}{r-1})$ is a constant independent of $k$.

We can bring this back to the master method by setting $r = \frac{a}{b^d}$.

If $a < b^d$, then the summation term in the total work expression becomes a constant (as demonstrated with the geometric sum); thus in Big-Oh, that summation term drops, and we are left with $O(n^d)$.

If $a > b^d$, then the summation term in the total work becomes a constant times $r^k$, where $k = \log_b n$, i.e. the summation term becomes a constant times $(\frac{a}{b^d})^{\log_b n}$. So we get $O(n^d (\frac{a}{b^d})^{\log_b n})$, which simplifies to $O(a^{\log_b n})$, which ends up being the number of leavens in the recursion tree. This is equivalent to  $O(n^{\log_b a})$.

## QuickSort

Another divide-and-conquer sorting algorithm.

- $O(n \log n)$ time "on average"
- Works in-place (minimal extra memory needed)

Given $n$ unsorted numbers, sort them in increasing order. Here we will assume no duplicates (it can be extended to handle duplicates).

Key idea: partition the array around a _pivot element_:

- Select a pivot element
- Partition the array: rearrange the array so that everything less than the pivot is moved to the left of the pivot, and everything greater than the pivot is moved to the right of the pivot.
    - The effect of this is that the pivot element ends up in its final position
    - Partitioning the array:
        - takes linear ($O(n)$) time
        - requires no extra memory
        - reduces the problem size

Basic algorithm:

```
QuickSort(array A, length n):
- if n=1 return
- p = ChoosePivot(A, n)
- Partition A around p
- QuickSort(elements less than p, num elements less than p)
- QuickSort(elements greater than p, num elements greater than p)
```

Note that there is no combine step needed.

The run time of QuickSort depends on how you choose the pivot.

### Partitioning

Set the pivot element to be the 1st element of the array. If it is not the 1st element, swap it with the current 1st element.

We do a single scan through the array, starting with the element following the pivot. The elements we have already looked at are properly partitioned at index $i$. The index $j$ separates what elements we've already seen from those we still need to scan, so we increment $j$ with each step. When we encounter an out-of-order element (i.e. an element on the wrong side of the partition), we swap it with the element at $i$ (i.e. swap $A[i]$ with$ A[j]$), then set $i = i + 1$. Then, once the scan is complete, the pivot element is swapped with the element at $i-1$.

There's only $O(1)$ work per array entry, so the partition run time is $O(n)$.

### Choosing a pivot

How we choose pivots has a big effect on the runtime of QuickSort - worst case, it may be $O(n^2)$, but it could be as good as $O(n \log n)$ if we get lucky and our pivots are the medians of the arrays (i.e. produce 50-50 splits).

We can choose pivots _randomly_ so that, on average, we get good performance. We choose pivot points uniformly randomly (i.e. each element has a $1/k$ chance of being picked as the pivot). We don't always need to pick the median, just a pivot that produces a "good enough" split (i.e. a 25-75 split or better), and we'll get $O(n \log n)$ running time.

Half the elements give us a 25-75 split (a 25-75 split covers 50% of the elements, any of those elements would give us the desired split), so we expect to choose a good enough pivot half the time.

## Randomized Selection

### The Selection Problem

Given an array of $n$ distinct numbers and a number $i \in \{1, \dots, n\}$, output an $i$th order statistic (i.e. $i$th smallest element of the input array).

The selection problem can be reduced to sorting: apply merge sort, then return the $i$th element of the sorted array (this is $O(n \log n)$ time).

### The RSelect Algorithm

We can also modify quick sort to do even better with $O(n)$ time (on average). The key is the portioning step. We pick a pivot as described previously (randomly), and then consider what order statistic we are looking for.

For instance, say we are looking for the 5th order statistic in an input array of length 10. We partition the array and the pivot ends up in the third position of the partitioned array. In this case, we should look for the 2nd order statistic on the right side of the pivot (i.e. we recurse on that side). In this case, the 2nd order statistic on the right side of the pivot is the 5th order statistic of the overall array.

Thus, in comparison to quick sort, we only need to recurse on one side. We say that the RSelect algorithm is in phase $j$ if the current array size is between $\frac{3}{4}^{j+1} n, \frac{3}{4}^j n$. That is, $\frac{3}{4}^j n$ is the upper-bound on the array size for phase $j$.

We also say that $X_j$ is the number of recursive calls the algorithm is in during phase $j$.

We use these terms to keep track of RSelect's progress to analyze its performance.

The upper-bound running time of RSelect is $\leq \sum_{\text{phases}_j} X_j c (\frac{3}{4})^j n$. So $c (\frac{3}{4})^j n$ is the number of work per phase $j$ subproblem and $X_j$ is the number of subproblems in phase $j$.

Because the pivot is randomly chosen, the right side of this inequality is a random variable because the $X_j$ terms are random variables (that is, the number of subproblems in a phase depend on the chosen pivot, which is picked randomly).

Note that if RSelect chooses a pivot giving a 25-75 split or better, the current phase ends (i.e. the new subarray's length is at most 75% of its old length, which demarcates a new phase by our definition of phases).

Like with quick sort, the probability of a 25-75 split or better is 50%. The expected value of $X_j$, i.e. $E[X_j]$, is the number of expected subproblems; this is essentially the number of expected subproblems until we get the desired split which moves us to the next phase $j+1$.

With the 50% probability of "success" (getting the desired 25-75 split), $X_j$ is a geometric random variable, like a (fair) coin flip. For coin flips, if we consider a random variable $N$ which describes how many flips to flip a heads, its expected value is $E[N] = 2$. $X_j$ is also a geometric random variable and thus its expected value is also 2.

We can take the previous upper-bound for RSelect and consider its expected value (after rearranging some terms):

$$
\leq E[cn \sum_{\text{phases}_j} X_j (\frac{3}{4})^j]
$$

Because of the linearity of expectation, we can re-write this as:

$$
\leq cn \sum_{\text{phases}_j} (\frac{3}{4})^j E[X_j]
$$

Replacing the computed expected value, we get:

$$
\leq 2cn \sum_{\text{phases}_j} (\frac{3}{4})^j
$$

The $\sum_{\text{phases}_j} (\frac{3}{4})^j$ term is a geometric sum, which can be re-written as $\frac{1}{1-3/4} = 4$, so the entire expected runtime can be stated:

$$
\leq 8cn
$$

Which is just $O(n)$.

## Graphs

Notation: $n$ is the number of vertices, $m$ is the number of edges.

For a connected graph with no parallel edges (i.e. each pair of vertices has only zero or one edge between it), $m$ is somewhere between $\Omega(n)$ and $O(n^2)$.

Generally, a graph is said to be _sparse_ if $m$ is $O(n)$ or close to it (that is, it has the lower end of number of edges). If $m$ is closer to $O(n^2)$, this is generally said to be a _dense_ graph.

An adjacency matrix requires $\Theta(n^2)$ space. If the graph is sparse, this is a waste of space, and an adjacency list is more appropriate - you have an array of vertices and an array of edges. Each edge points to its endpoints, and each vertex points to edges incident on it. This requires $\Theta(m+n)$ space (because the array of vertices takes $\Theta(n)$ space and the arrays of edges, edge-to-endpoints, and vertex-to-edges each take $\Theta(m)$, for $\Theta(n+3m) = \Theta(m+n)$), so it is better for sparse graphs.

### The Minimum Cut Problem

A _cut_ of a graph $(V,E)$ is a partition of $V$ into two non-empty sets $A$ and $B$. The _crossing edges_ of a cut $(A,B)$ are those with one endpoint in each of $(A,B)$ for an undirected graph, or, for a directed graph, have their tail in $A$ and their head in $B$.

The _minimum cut problem_ involves identifying a cut of a graph with the fewest number of crossing edges.

#### The Random Contraction Algorithm

While there are more than two vertices, pick a remaining edge $(u,v)$ uniformly at random. Then we take endpoints of the edge ($u, v$) and merge (or "contract") them into a single vertex (keeping their edges to other vertices). The merging may result in parallel edges or self-loops - we remove self-loops but parallel edges are kept.

Each iteration decreases the vertices until we just have two; we return the cut represented by these final two vertices (that is, the vertices contracted into the one vertex are considered $A$, and the vertices contracted into the other vertex are considered $B$).

Sometimes this algorithm _does not_ identify a min cut, depending on its random choices of vertices to contract. What is the probability of getting a min cut?

Say we have a graph $G=(V,E)$ with $n$ vertices, $m$ edges, and we have a minimum cut we want to find, $(A,B)$. Let $k$ be the size of the minimum cut (the number of edges crossing $(A,B)$) The crossing edges will be denoted $F$.

If some edge of $F$ is contracted, we will fail to find $(A,B)$. Put another way, if only edges inside $A$ or inside $B$ get contracted, then we will successfully find $(A,B)$ (in fact, this are the only condition under which will be successful).

Thus we can state the probability of finding $(A,B)$ as the probability of never contracting an edge $F$. We'll notate $S_i$ as the event that an edge of $F$ is contracted in iteration $i$. So to put this another way, we want to find the probability of $\lnot S_1, \lnot S_2, \dots, \lnot S_{n-2}$.

For the first iteration, the probability of contracting an edge from $F$ is $k/m$. It's easier to work with if we state this in terms of the number of vertices $n$ since we know how that value changes over the course of the algorithm.

We can say that each vertex has at least degree $k$ (i.e. at least $k$ incident edges). This is because if $k$ is the minimum number of crossing edges, a feasible set is $A$ containing a single vertex and the rest being in $B$, in which case that lone vertex must have at minimum $k$ edges.

For any graph, if we sum the degrees of all the vertices, we will get $2m$ (that is, $\sum_{v \in V} \text{degree}(v) = 2m$). So we know that $2m \geq kn$, i.e. $m \geq \frac{kn}{2}$.

Thus we can say that, at worst, the probability of contracting an edge from $F$ in the first iteration is $\frac{k}{m} \leq \frac{2}{n}$, i.e. $P(S_1) \leq \frac{2}{n}$.

For the second iteration, we are considering the probability $P(\lnot S_1, \lnot S_2)$, which, using conditional probability, can be restated $P(\lnot S_2 | \lnot S_1)P(\lnot S_1)$. We know that $P(\lnot S_1) \geq 1 - \frac{2}{n}$, and $P(\lnot S_2|\lnot S_1) \geq 1 - \frac{2}{n-1}$ (since each vertex still has at least degree $k$).

This continues across all iterations until the number of vertices is $n-2$ and ends up being:

$$
(1-\frac{2}{n})(1-\frac{2}{n-1})(1-\frac{2}{n-2}) \dots (1-\frac{2}{n-(n-4)})(1-\frac{2}{n-(n-3)})
$$

Which ends up simplifying to:

$$
\frac{2}{n(n-1)}
$$

Which we can roughly lower bound to $\geq \frac{1}{n^2}$.

This is quite a low success probability, but it is a non-trivial (i.e. practically high) success probability compared to a completely random strategy.

One approach is to apply this algorithm in $N$ repeated trials and keep the smallest cut found.

We define $T_i$ as the event where the cut $(A,B)$ is found on the $i$th attempt. These events are independent.

The probability that all $N$ trials fail is $P(\lnot T_1, \lnot T_2, \dots, \lnot T_N)$; by independence this is equivalent to $\prod_{i=1}^N P(\lnot T_i)$, and by our previous calculation, the probability of $P(\lnot T_i)$ is bounded above by $1 - \frac{1}{n^2}$, so this is equivalent to $\leq (1 - \frac{1}{n^2})^N$.

If we set $N = n^2 \ln(n)$, then $\prod_{i=1}^N P(\lnot T_i) \leq \frac{1}{n}$.

So what's the running time? It is polynomial in $n$ and $m$ but then we do repeated trials to have better assurance of success. If we do $N = n^2 \ln(n)$ trials and for each trial we look at all the edges, we get $\Omega(n^2 \ln(n) m)$, so it is quite slow. But there are augmented approaches that get better performance than this.

---

Other algorithmic design methods:

- Randomization: involves some randomness

Primitives: there are some algorithms that are so fast that they are considered "primitives", i.e. to be used as building blocks for more complex algorithms.


## Data Structures

Motivation for data structures: to organize data so that it can be accessed quickly and usefully.

For example: lists, stacks, queues, heaps, search trees, hashtables, bloom filters, etc.

Different data structures are appropriate for different operations (and thus different kinds of problems).

### Heaps

A _heap_ (sometimes called a _priority queue_) is a container for objects that have keys; these keys are comparable (e.g. we can say that one key is bigger than another).

Supported operations:

- `insert`: add a new object to the heap, runtime $O(\log n)$
- `extract-min`: remove the object with the minimum key value (ties broken arbitrarily), runtime $O(\log n)$

Alternatively, there are max-heaps which return the maximum key value (this can be emulated by a heap by negating key values such that the max becomes the min, etc).

Sometimes there are additional operations supported:

- `heapify`: initialize a heap in linear time (i.e. $O(n)$ time, faster than inserting them one-by-one)
- `delete`: delete an arbitrary element from the middle of the heap in $O(\log n)$ time

For example, you can have a heap where events are your objects and their keys are a scheduled time to occur. Thus when you `extract-min` you always get the next event scheduled to occur.

### Balanced Binary Search Tree

A _balanced binary search tree_ can be thought of as a dynamic sorted array (i.e. a sorted array which supports `insert` and `delete` operations).

First, consider sorted arrays. They support the following operations:

- `search`: binary search, runtime $O(\log n)$
- `select`: select an element by index, runtime $O(1)$
- `min` and `max`: return first and last element of the array (respectively), runtime $O(1)$
- `predecessor` and `successor`: return next smallest and next largest element of the array (respectively), runtime $O(1)$
- `rank`: the number of elements less than or equal to a given value, runtime $O(\log n)$ (search for the given value and return the position)
- `output`: output elements in sorted order, runtime $O(n)$ (since they are already sorted)

With sorted arrays, insertion and deletions have $O(n)$ runtime, which is too slow.

If you want more logarithmic-time insertions and deletions, we can use a balanced binary search tree. This supports the same operations as sorted arrays (though some are slower) in addition to faster insertions and deletions:

- `search`: runtime $O(\log n)$
- `select`: runtime $O(\log n)$ (slower than sorted arrays)
- `min` and `max`: runtime $O(\log n)$ (slower than sorted arrays)
- `predecessor` and `successor`: runtime $O(\log n)$ (slower than sorted arrays)
- `rank`: runtime $O(\log n)$
- `output`: runtime $O(n)$
- `insert`: runtime $O(\log n)$
- `delete`: runtime $O(\log n)$

To understand how balanced binary search trees, first consider binary search trees.

#### Binary Search Tree

A binary search tree (BST) is a data structure for efficient searching.

The keys that are stored are the nodes of the tree.

Each node has three pointers: one to its parent, one to its left child, and one to its right child.

These pointers can be null (e.g. the root node has no parent).

The _search tree property_ asserts that for every node, all the keys stored in its left subtree should be less than its key, and all the keys in its right subtree should be greater than its key.

You can also have a convention for handling equal keys (e.g. just put it on the left or the right subtree).

This search tree property is what makes it very easy to search for particular values.

Note that there are many possible binary search trees for a given set of keys. The same set of keys could be arranged as a very deep and narrow BST, or as a very shallow and wide one. The worst case is a depth of about $n$, which is more of a chain than a tree; the best case is a depth of about $\log_2 n$, which is perfectly _balanced_.

This search tree property also makes insertion simple. You search for the key to be inserted, which will fail since the key is not in the tree yet, and you get a null pointer - you just assign that pointer to point to the new key. In the case of duplicates, you insert it according to whatever convention you decided on (as mentioned previously). This insert method maintains the search tree property.

Search and insert performance are dependent on the depth of the tree, so at worst the runtime is $O(\text{height})$.

The min and max operations are simple: go down the leftmost branch for the min key and go down the rightmost branch for the max key.

The predecessor operation is a bit more complicated. First you search for the key in question. Then, if the key's node has a left subtree, just take the max of that subtree. However, if the key does not have a left subtree, move up the tree through its parent and ancestors until you find a node with a key less than the key in question. (The successor operation is accomplished in a similar way.)

The deletion operation is tricky. First we must search for the key we want to delete. Then there are three possibilities:

- the node has no children, so we can just delete it and be done
- the node has one child; we can just delete the node and replace it with its child
- the node has two children; we first compute the predecessor of the node, then swap it with the node, then delete the node

For the select and rank operations, we can _augment_ our search tree by including additional information at each node: the size of its subtree, including itself (i.e. number of descendants + 1). Augmenting the data structure in this way does add some overhead, e.g. we have to maintain the data/keep it updated whenever we modify the tree.

For select, we want to find the $i$th value of the data structure. Starting at a node $x$, say $a$ is the size of its left subtree. If $a = i-1$, return $x$. If $a \geq i$, recursively select the $i$th value of the left subtree. If $a < i-1$, recursively select the $(i-a-1)$th value of the right subtree.

#### Balanced Binary Search Trees (Red-Black Trees)

Balanced binary search trees have the "best" depth of about $\log_2 n$.

There are different kinds of balanced binary search trees (which are all quite similar), here we will talk about red-black trees (other kinds are AVL trees, splaytrees, B trees, etc).

Red-Black trees maintain some invariants/constraints which are what guarantee that the tree is balanced:

1. each node stores an additional bit indicating if it is a "red" or "black" node
2. the root is always black
3. never allow two reds in a row (e.g. all of a red node's children are black)
4. every path from the root node to a null pointer (e.g. an unsuccessful search) must go through the same number of black nodes

Consider the following: for a binary search tree, if every root-null path has $\geq k$ nodes, then the tree includes at the top a perfectly balanced search tree of depth $k-1$. Thus there must be at least $2^k - 1$ nodes in the tree, i.e. $n \geq 2^k - 1$. We can restate this as $k \leq \log_2(n+1)$

In a red-black tree, there is a root-null path with at most $log_2(n+1)$ black nodes (e.g. it can have a root-null path composed of only black nodes).

The fourth constraint on red-black trees means that _every_ root-null path has $\leq log_2(n+1)$ black nodes. The third constraint means that we can never have more red nodes than black nodes (because the red nodes can never come one after the other) in a path. So at most a root-null path will have $\leq 2 log_2(n+1)$, which gives us a balanced tree.

### Hash Tables

_Hash tables_ (also called _dictionaries_) allow us to maintain a (possibly evolving) set of stuff.

The core operations include:

- `insert` using a key
- `delete` using a key
- `lookup` using a key

When implemented properly, and on non-pathological data, these operations all run in $O(1)$ time.

Hash tables do _not_ maintain any ordering.

Basically, hash tables use some hash function to produce a hash for an object (some number); this hash is the "address" of the object in the hash table.

More specifically, we have a hash function which gives us a value in some range $[0, n]$; we have an array of length $n$, so the hash function tells us and what index to place some object.

There is a chance of _collisions_ in which two different objects produce the same hash.

There are two main solutions for resolving collisions:

- _(separate) chaining_: if there is a collision, store the objects together at that index as a list
- _open addressing_: here, a hash function specifies a _sequence_ (called a _probe sequence_) instead of a single value. Try the first value, if its occupied, try the next, and so on.
  - one strategy, _linear probing_, just has you try the hash value + 1 and keep incrementing by one until an empty bucket is found
  - another is _double hashing_, in which you have two hash functions, you look at the first hash, if occupied, offset by the second hash until you find an empty bucket

Each is more appropriate in different situations.

The performance of a hash table depends a lot on the particular hash function. Ideally, we want a hash function that:

- has good performance (i.e. low collisions)
- should be easy to store
- should be fast to evaluate (constant time)

Designing hash functions is as much an art as it is a science. They are quite difficult to design.

A hash table has a _load factor_ (sometimes just called _load_), denoted $\alpha = \frac{\text{# objects in hash table}}{\text{# buckets in hash table}}$.

For hash table operations to run constant time, it is necessary that $\alpha = O(1)$. Ideally, it is less than 1, especially with open addressing.

So for good performance, we need to control load. For example, if $\alpha$ passes some threshold, e.g. 0.75, then we may want to expand the hash table to lower the load.

Every hash function has a "pathological" data set which it performs poorly on. There is no hash function which is guaranteed to spread out _every_ data set evenly (i.e. have low collisions on any arbitrary data set). You can often reverse engineer this pathological data set by analyzing the hash function.

However, for some hash functions, it is "infeasible" to figure out its pathological data set (as is the case with cryptographic hash functions).

One approach is to define a _family_ of hash functions, rather than just one, and randomly choose a hash function to use at runtime. This has the property that, on average, you do well across all datasets.

### Bloom Filters

Bloom filters are a variant on hash tables - they are more space efficient, but they allow for some errors (that is, there's a chance of a false positive). In some contexts, this is tolerable.

They are more space efficient because they do not actually store the objects themselves. They are more commonly used to keep track of what objects have been seen so far. They typically do not support deletions (there are variants that do incorporate deletions but they are more complicated). There is also a small chance that it will say that it's seen an object that it hasn't (i.e. false positives).

Like a hash table, a bloom filter consists of an array $A$, but each entry in the array is just one bit. Say we have a set of objects $S$ and the total number of bits $n$ - a bloom filter will use only $\frac{n}{|S|}$ bits per object in $S$.

We also have $k$ hash functions $h_1, \dots, h_k$ (usually $k$ is small).

The insert operation is defined:

```python
hash_funcs = [...]
for i in range(k):
  A[h[i](input)] = 1
```

That is, we just set the values of those bits to 1.

Thus lookup is just to check that all the corresponding bits for an object are 1.

We can't have any false negatives because those bits will not have been set and bits are never reset back to zero.

False positives are possible, however, because some other objects may have in aggregate set the bits corresponding to another object.

## References

- Algorithms: Design and Analysis, Part 1. Tim Roughgarden. Stanford/Coursera.