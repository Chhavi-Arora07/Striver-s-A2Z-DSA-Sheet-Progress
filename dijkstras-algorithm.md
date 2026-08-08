# Dijkstra's Algorithm - Shortest Path

Dijkstra's algorithm finds the shortest distance from a source node to all other nodes in a weighted graph.

## Idea

1. Maintain a distance array, initialized to infinity for all nodes except the source (0).
2. Use a min-priority queue to always process the closest unvisited node next.
3. For the current node, relax all its outgoing edges - if going through the current node gives a shorter path to a neighbor, update that neighbor's distance.
4. Repeat until all nodes are visited or the queue is empty.

**Requirements / limitations:**
- Works on graphs with **non-negative edge weights only**. Negative weights can produce incorrect results because the algorithm assumes a node's shortest distance is finalized once popped from the queue.
- Works on both directed and undirected graphs, and does **not** require the graph to be acyclic (unlike the topological-sort approach).
- Complexity: **O((V + E) log V)** using a binary heap priority queue.

---

## Python Implementation

```python
import heapq

def dijkstra(graph, num_nodes, source):
    """
    graph: dict {u: [(v, weight), ...]}
    num_nodes: total number of nodes (0-indexed)
    source: starting node
    """
    dist = [float('inf')] * num_nodes
    dist[source] = 0

    # Min-heap of (distance, node)
    pq = [(0, source)]
    visited = [False] * num_nodes

    while pq:
        d, u = heapq.heappop(pq)

        if visited[u]:
            continue
        visited[u] = True

        for v, w in graph.get(u, []):
            if not visited[v] and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                heapq.heappush(pq, (dist[v], v))

    return dist


# Example usage
if __name__ == "__main__":
    graph = {
        0: [(1, 4), (2, 1)],
        1: [(3, 1)],
        2: [(1, 2), (3, 5)],
        3: []
    }

    num_nodes = 4
    source = 0

    distances = dijkstra(graph, num_nodes, source)

    for node, d in enumerate(distances):
        print(f"Distance to node {node}: {d if d != float('inf') else 'unreachable'}")
```

### Output

```
Distance to node 0: 0
Distance to node 1: 3
Distance to node 2: 1
Distance to node 3: 4
```

---

## Java Implementation

```java
import java.util.*;

public class Dijkstra {

    // Edge class to hold destination and weight
    static class Edge {
        int to, weight;
        Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }

    public static int[] dijkstra(List<List<Edge>> graph, int numNodes, int source) {
        int[] dist = new int[numNodes];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[source] = 0;

        // Min-heap of [distance, node]
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        pq.add(new int[]{0, source});

        boolean[] visited = new boolean[numNodes];

        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int d = curr[0], u = curr[1];

            if (visited[u]) continue;
            visited[u] = true;

            for (Edge e : graph.get(u)) {
                if (!visited[e.to] && dist[u] != Integer.MAX_VALUE
                        && dist[u] + e.weight < dist[e.to]) {
                    dist[e.to] = dist[u] + e.weight;
                    pq.add(new int[]{dist[e.to], e.to});
                }
            }
        }

        return dist;
    }

    public static void main(String[] args) {
        int numNodes = 4;
        List<List<Edge>> graph = new ArrayList<>();
        for (int i = 0; i < numNodes; i++) {
            graph.add(new ArrayList<>());
        }

        // Example graph edges
        graph.get(0).add(new Edge(1, 4));
        graph.get(0).add(new Edge(2, 1));
        graph.get(1).add(new Edge(3, 1));
        graph.get(2).add(new Edge(1, 2));
        graph.get(2).add(new Edge(3, 5));

        int source = 0;
        int[] distances = dijkstra(graph, numNodes, source);

        for (int i = 0; i < numNodes; i++) {
            String d = distances[i] == Integer.MAX_VALUE ? "unreachable" : String.valueOf(distances[i]);
            System.out.println("Distance to node " + i + ": " + d);
        }
    }
}
```

### Output

```
Distance to node 0: 0
Distance to node 1: 3
Distance to node 2: 1
Distance to node 3: 4
```

### Notes on Java version

- Uses `PriorityQueue<int[]>` with a comparator on distance to act as the min-heap.
- `Integer.MAX_VALUE` represents infinity - the `dist[u] != Integer.MAX_VALUE` check guards against overflow when adding edge weights.
- Same complexity as the Python version: **O((V + E) log V)**.

---

## Complexity Summary

| Step                          | Complexity        |
|--------------------------------|--------------------|
| Priority queue operations       | O((V + E) log V)  |
| **Total**                       | **O((V + E) log V)** |

## Dijkstra vs. Topological Sort Shortest Path

| | Dijkstra | Topological Sort |
|---|---|---|
| Graph type | Any (directed/undirected) | DAG only |
| Negative weights | Not supported | Supported |
| Complexity | O((V+E) log V) | O(V + E) |
