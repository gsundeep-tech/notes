Union Find

```python

class UnionFind:
    def __init__(self, size):
        self.root = [i for i in range(size)]
    
    def find(self, x):
        if x == self.root[x]:
            return x
        self.root[x] = self.find(self.root[x])
        return self.root[x]

    def union(self, x, y):
        parentX = self.find(x)
        parentY = self.find(y)
        if parentX < parentY:
            self.root[parentY] = parentX
        else:
            self.root[parentX] = parentY
    
    def connected(self, x, y):
        return self.find(x) == self.find(y)

```

Using Ranking - more optimized way

```python
class UnionFind:
    
    def __init__(self, size):
        self.root = [i for i in range(size)]
        self.rank = [1] * size
    
    def find(self, x):
        if self.root[x] == x:
            return x
        
        self.root[x] = self.find(self.root[x])
        return self.root[x]
    
    def union(self, x, y):
        parentX = self.find(x)
        parentY = self.find(y)
        if parentX != parentY:
            if self.rank[parentX] > self.rank[parentY]:
                self.root[parentY] = parentX
            elif self.rank[parentY] > self.rank[parentX]:
                self.root[parentX] = parentY
            else:
                self.root[parentY] = parentX
                self.rank[parentX] += 1
    
    def is_connected(self, x, y):
        return self.find(x) == self.find(y)

```

Traversal 
1. BFS
2. DFS

```python

def bfs(graph, source=(0, 0), destination=(0, 0)):
    """
    Graph dict[list]: Graph is passed as a adjacency list
    
    """
```

Shortest Path
1. Dijkstra's Algo


## 1. Dijkstra's Algo
Dijkstra's Algorithm is used to find the shortest distance from a given source in a weighted graph without negative edges
Note: It works only on the weighted graph without negative edges, for negative edges we need to use the Bellman Ford Algorithm


Problem: Given a graph and a source vertex in the graph, find the shortest paths from the source to all vertices in the given graph

```python

def single_source_shortest_path_for_adjacency_list(graph, source):
    """
    graph: Adjacency list with the (node, weight)
    source: source node

    Reference: 
        1. https://www.tutorialspoint.com/Dijkstra-s-Algorithm-for-Adjacency-List-Representation
        2. https://www.scaler.com/topics/data-structures/dijkstra-algorithm/
    """
    num_nodes = len(graph)

    distance = {}
    distance[source] = 0

    visited = {}
    prev = [None] * num_nodes
    heap = [(0, source)]

    while len(heap) > 0:
        dist, curr_node = heapq.heappop(heap)

        if curr_node in visited:
            continue
        
        visited[curr_node] = True

        for adj_node, weight in graph.get(curr_node):

            if adj_node in visited:
                continue
            
            if dist + weight < distance.get(adj_node, float('inf')):
                distance[adj_node] = dist + weight
                heapq.heapappend(heap, (adj_node, dist + weight))
                prev[adj_node] = curr_node
   
    print(distance) # shortest distance from the source to all nodes
    print(prev) # path to achieve that shortest distance

def single_source_shortest_path_for_adjacency_matrix(graph, source):
    """
    graph: Matrix with weights as the values and indexes would be the nodes
    source: source node
    """
    rows_length = len(graph)
    cols_length = len(graph[0])

    distance = {}
    visited = {}
    distance[(source[0], source[1])] = 0

    heap = [(0, source[0], source[1])]
    dirs = [(0, 1), (0, -1), (1, 0), (-1, 0)]

    while len(heap) > 0:
        dist, curr_x, curr_y = heapq.heappop(heap)
        
        if (curr_x, curr_y) in visited:
            continue

        visited[(curr_x, curr_y)] = True

        for (_x, _y) in dirs:
            new_x = curr_x + _x
            new_y = curr_y + _y

            if (new_x, new_y) in visited:
                continue
            
            if 0 <= new_x < rows_length and 0 <= new_y < cols_length and dist + graph[new_x][new_y] < distance.get((new_x, new_y), float('inf')):
                distance[(new_x, new_y)] = dist + graph[new_x][new_y]
                heapq.heapappend(heap, (dist + graph[new_x][new_y], new_x, new_y))
    print(distance)
```