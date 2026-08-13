# Segment Tree

A Segment Tree is a binary tree data structure used for storing information about intervals (segments). It allows efficient **range queries** (sum, min, max, etc.) and **point/range updates** on an array.

## Idea

1. Build a tree where each leaf represents a single array element, and each internal node represents the combination (e.g. sum, min, max) of its children's ranges.
2. Build takes O(N), each query or update takes O(log N).
3. Common operations:
   - **Build**: construct the tree from an array.
   - **Query(l, r)**: get the combined result (sum/min/max/etc.) over range `[l, r]`.
   - **Update(i, val)**: update a single element and propagate the change up the tree.

**Use cases:** range sum queries, range min/max queries, range GCD, with point updates - common in competitive programming and problems requiring frequent range queries on mutable arrays.

**Complexity:**
- Build: **O(N)**
- Query: **O(log N)**
- Update: **O(log N)**
- Space: **O(N)** (typically array-based, sized `4*N`)

---

## Python Implementation (Sum Segment Tree)

```python
class SegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.tree = [0] * (4 * self.n)
        if self.n > 0:
            self.build(arr, 0, 0, self.n - 1)

    def build(self, arr, node, start, end):
        if start == end:
            self.tree[node] = arr[start]
            return
        mid = (start + end) // 2
        left, right = 2 * node + 1, 2 * node + 2
        self.build(arr, left, start, mid)
        self.build(arr, right, mid + 1, end)
        self.tree[node] = self.tree[left] + self.tree[right]

    def query(self, l, r):
        """Sum of elements in range [l, r] (inclusive)."""
        return self._query(0, 0, self.n - 1, l, r)

    def _query(self, node, start, end, l, r):
        if r < start or end < l:
            return 0  # out of range
        if l <= start and end <= r:
            return self.tree[node]  # fully in range
        mid = (start + end) // 2
        left, right = 2 * node + 1, 2 * node + 2
        return self._query(left, start, mid, l, r) + \
               self._query(right, mid + 1, end, l, r)

    def update(self, idx, val):
        """Point update: set arr[idx] = val."""
        self._update(0, 0, self.n - 1, idx, val)

    def _update(self, node, start, end, idx, val):
        if start == end:
            self.tree[node] = val
            return
        mid = (start + end) // 2
        left, right = 2 * node + 1, 2 * node + 2
        if idx <= mid:
            self._update(left, start, mid, idx, val)
        else:
            self._update(right, mid + 1, end, idx, val)
        self.tree[node] = self.tree[left] + self.tree[right]


# Example usage
if __name__ == "__main__":
    arr = [1, 3, 5, 7, 9, 11]
    st = SegmentTree(arr)

    print(f"Sum of range [1, 3]: {st.query(1, 3)}")  # 3+5+7 = 15

    st.update(1, 10)  # arr becomes [1, 10, 5, 7, 9, 11]
    print(f"Sum of range [1, 3] after update: {st.query(1, 3)}")  # 10+5+7 = 22
```

### Output

```
Sum of range [1, 3]: 15
Sum of range [1, 3] after update: 22
```

---

## Java Implementation (Sum Segment Tree)

```java
public class SegmentTree {
    private int[] tree;
    private int n;

    public SegmentTree(int[] arr) {
        n = arr.length;
        tree = new int[4 * n];
        if (n > 0) {
            build(arr, 0, 0, n - 1);
        }
    }

    private void build(int[] arr, int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
            return;
        }
        int mid = (start + end) / 2;
        int left = 2 * node + 1, right = 2 * node + 2;
        build(arr, left, start, mid);
        build(arr, right, mid + 1, end);
        tree[node] = tree[left] + tree[right];
    }

    // Sum of elements in range [l, r] (inclusive)
    public int query(int l, int r) {
        return query(0, 0, n - 1, l, r);
    }

    private int query(int node, int start, int end, int l, int r) {
        if (r < start || end < l) {
            return 0; // out of range
        }
        if (l <= start && end <= r) {
            return tree[node]; // fully in range
        }
        int mid = (start + end) / 2;
        int left = 2 * node + 1, right = 2 * node + 2;
        return query(left, start, mid, l, r) + query(right, mid + 1, end, l, r);
    }

    // Point update: set arr[idx] = val
    public void update(int idx, int val) {
        update(0, 0, n - 1, idx, val);
    }

    private void update(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;
            return;
        }
        int mid = (start + end) / 2;
        int left = 2 * node + 1, right = 2 * node + 2;
        if (idx <= mid) {
            update(left, start, mid, idx, val);
        } else {
            update(right, mid + 1, end, idx, val);
        }
        tree[node] = tree[left] + tree[right];
    }

    public static void main(String[] args) {
        int[] arr = {1, 3, 5, 7, 9, 11};
        SegmentTree st = new SegmentTree(arr);

        System.out.println("Sum of range [1, 3]: " + st.query(1, 3)); // 3+5+7 = 15

        st.update(1, 10); // arr becomes [1, 10, 5, 7, 9, 11]
        System.out.println("Sum of range [1, 3] after update: " + st.query(1, 3)); // 10+5+7 = 22
    }
}
```

### Output

```
Sum of range [1, 3]: 15
Sum of range [1, 3] after update: 22
```

### Notes on Java version

- Tree stored as a flat array of size `4*n` (a safe upper bound for a complete binary tree over `n` leaves).
- Recursive build/query/update using `2*node+1` and `2*node+2` for left/right children.
- Easily adapted to min/max/GCD by changing the combine step (`tree[node] = tree[left] + tree[right]`) and the out-of-range sentinel value in `query`.

---

## Adapting to Other Operations

| Operation | Combine step | Out-of-range sentinel |
|---|---|---|
| Sum | `tree[node] = left + right` | `0` |
| Min | `tree[node] = min(left, right)` | `+infinity` |
| Max | `tree[node] = max(left, right)` | `-infinity` |
| GCD | `tree[node] = gcd(left, right)` | `0` |

---

## Complexity Summary

| Step | Complexity |
|---|---|
| Build | O(N) |
| Query | O(log N) |
| Update | O(log N) |
| Space | O(N) |

## Segment Tree vs. Alternatives

| | Segment Tree | Fenwick Tree (BIT) | Prefix Sum Array |
|---|---|---|---|
| Range query | O(log N) | O(log N) | O(1) |
| Point update | O(log N) | O(log N) | O(N) |
| Range update + range query | O(log N) with lazy propagation | O(log N) with tricks | Not supported |
| Flexibility (min/max/GCD, etc.) | High | Limited (mainly sum) | Low |
| Space | O(N) (4N typical) | O(N) | O(N) |
