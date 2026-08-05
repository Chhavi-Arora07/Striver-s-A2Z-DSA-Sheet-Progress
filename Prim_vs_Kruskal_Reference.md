# Prim's vs Kruskal's Algorithm (Java Reference)

## Goal

Both algorithms find a **Minimum Spanning Tree (MST)** of a connected,
weighted, undirected graph.

An MST: - Connects all vertices. - Uses exactly `V - 1` edges. - Has no
cycles. - Has the minimum possible total weight.

------------------------------------------------------------------------

# Prim's Algorithm

## Neuron

> Grow one tree by repeatedly taking the **cheapest edge that connects a
> new node**.

## Data Structures

-   `PriorityQueue`
-   `boolean[] visited`
-   Adjacency List

## Java Skeleton

``` java
PriorityQueue<int[]> pq =
    new PriorityQueue<>((a,b) -> a[0] - b[0]);

boolean[] vis = new boolean[V];

pq.offer(new int[]{0,0}); // {weight,node}

int mstWeight = 0;

while(!pq.isEmpty()){

    int[] curr = pq.poll();

    int wt = curr[0];
    int node = curr[1];

    if(vis[node]) continue;

    vis[node] = true;
    mstWeight += wt;

    for(int[] edge : adj.get(node)){

        int next = edge[0];
        int edgeWt = edge[1];

        if(!vis[next]){
            pq.offer(new int[]{edgeWt,next});
        }
    }
}
```

## Time Complexity

`O(E log V)`

------------------------------------------------------------------------

# Kruskal's Algorithm

## Neuron

> Sort every edge by weight and keep taking the cheapest edge that
> **doesn't create a cycle**.

## Data Structures

-   Edge List
-   Sorting
-   Disjoint Set Union (Union-Find)

## Java Skeleton

``` java
Collections.sort(edges,
    (a,b)->a.w-b.w);

DisjointSet ds = new DisjointSet(V);

int mstWeight = 0;

for(Edge e : edges){

    if(ds.find(e.u) != ds.find(e.v)){

        ds.union(e.u,e.v);

        mstWeight += e.w;
    }
}
```

## Time Complexity

`O(E log E)`

------------------------------------------------------------------------

# Prim vs Kruskal

  Prim                         Kruskal
  ---------------------------- ---------------------------
  Grows one tree               Builds forest then merges
  PQ                           Sorting + DSU
  Works from a starting node   No starting node
  Better for dense graphs      Better for sparse graphs

------------------------------------------------------------------------

# Recognition

Use Prim when: - Adjacency List given - Asked for MST - PQ feels natural

Use Kruskal when: - Edge list given - DSU is available - Sorting edges
is easy

------------------------------------------------------------------------

# Things Charlie Should Remember

1.  DFS/BFS produce **a** spanning tree.
2.  Prim/Kruskal produce the **minimum** spanning tree.
3.  Prim's PQ stores the frontier of the current MST.
4.  Kruskal's DSU prevents cycles.
5.  Both stop after selecting `V - 1` edges.
