# Shortest Path Using Topological Sort

A classic algorithm for finding shortest distances from a source node in a **Directed Acyclic Graph (DAG)** with weighted edges.

## Idea

1. Topologically sort the DAG.
2. Process nodes in that order, relaxing edges of each node as you go.
3. Since a node's dependencies are guaranteed to come before it in topo order, its shortest distance is finalized by the time you process it.

This is faster than Dijkstra for DAGs — **O(V + E)** instead of O((V+E) log V).

**Note:** This approach only works on DAGs (no cycles) — topological sort itself requires that. Unlike Dijkstra, it also works fine with **negative edge weights**, since relaxation order is guaranteed by the topo sort rather than by picking the minimum each time.

---

## Python Implementation

```python
from collections import defaultdict, deque

def shortest_path_topo(graph, num_nodes, source):
    """
    graph: dict {u: [(v, weight), ...]}
    num_nodes: total number of nodes (0-indexed)
    source: starting node
    """
    # Step 1: Compute in-degrees
    in_degree = [0] * num_nodes
    for u in graph:
        for v, w in graph[u]:
            in_degree[v] += 1

    # Step 2: Topological sort (Kahn's algorithm)
    queue = deque([i for i in range(num_nodes) if in_degree[i] == 0])
    topo_order = []

    while queue:
        u = queue.popleft()
        topo_order.append(u)
        for v, w in graph[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    # Step 3: Initialize distances
    dist = [float('inf')] * num_nodes
    dist[source] = 0

    # Step 4: Relax edges in topological order
    for u in topo_order:
        if dist[u] != float('inf'):
            for v, w in graph[u]:
                if dist[u] + w < dist[v]:
                    dist[v] = dist[u] + w

    return dist


# Example usage
if __name__ == "__main__":
    graph = defaultdict(list)
    graph[0] = [(1, 2), (2, 4)]
    graph[1] = [(2, 1), (3, 7)]
    graph[2] = [(3, 3)]
    graph[3] = []

    num_nodes = 4
    source = 0

    distances = shortest_path_topo(graph, num_nodes, source)

    for node, d in enumerate(distances):
        print(f"Distance to node {node}: {d if d != float('inf') else 'unreachable'}")
```

### Output

```
Distance to node 0: 0
Distance to node 1: 2
Distance to node 2: 3
Distance to node 3: 6
```

---

## Java Implementation

```java
import java.util.*;

public class ShortestPathTopoSort {

    // Edge class to hold destination and weight
    static class Edge {
        int to, weight;
        Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }

    public static int[] shortestPath(List<List<Edge>> graph, int numNodes, int source) {
        // Step 1: Compute in-degrees
        int[] inDegree = new int[numNodes];
        for (int u = 0; u < numNodes; u++) {
            for (Edge e : graph.get(u)) {
                inDegree[e.to]++;
            }
        }

        // Step 2: Topological sort using Kahn's algorithm
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < numNodes; i++) {
            if (inDegree[i] == 0) queue.add(i);
        }

        List<Integer> topoOrder = new ArrayList<>();
        while (!queue.isEmpty()) {
            int u = queue.poll();
            topoOrder.add(u);
            for (Edge e : graph.get(u)) {
                inDegree[e.to]--;
                if (inDegree[e.to] == 0) {
                    queue.add(e.to);
                }
            }
        }

        // Step 3: Initialize distances
        int[] dist = new int[numNodes];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[source] = 0;

        // Step 4: Relax edges in topological order
        for (int u : topoOrder) {
            if (dist[u] != Integer.MAX_VALUE) {
                for (Edge e : graph.get(u)) {
                    if (dist[u] + e.weight < dist[e.to]) {
                        dist[e.to] = dist[u] + e.weight;
                    }
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

        // Example DAG edges
        graph.get(0).add(new Edge(1, 2));
        graph.get(0).add(new Edge(2, 4));
        graph.get(1).add(new Edge(2, 1));
        graph.get(1).add(new Edge(3, 7));
        graph.get(2).add(new Edge(3, 3));

        int source = 0;
        int[] distances = shortestPath(graph, numNodes, source);

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
Distance to node 1: 2
Distance to node 2: 3
Distance to node 3: 6
```

### Notes on Java version

- Uses an adjacency list (`List<List<Edge>>`), which is the standard Java representation for graphs.
- `Integer.MAX_VALUE` represents infinity — be careful not to add to it directly (could overflow); the `if (dist[u] != Integer.MAX_VALUE)` check guards against that.
- Same complexity as the Python version: **O(V + E)**.

---

## Complexity Summary

| Step                  | Complexity |
|------------------------|------------|
| Topological sort       | O(V + E)   |
| Edge relaxation        | O(V + E)   |
| **Total**               | **O(V + E)** |

Compare to Dijkstra's algorithm at **O((V + E) log V)** — this approach is faster, but only applicable to DAGs.
