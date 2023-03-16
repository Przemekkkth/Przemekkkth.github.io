---
title: A star pathfinding algorithm
author: Przemek-Bądaruk
date: 2022-11-03 22:32:00 -0500
categories: [Qt, visualization]
tags: [pathfinding, algorithm, visualization]
---

# A star pathfinding algorithm
A* (pronounced "A star") is a popular pathfinding algorithm used in computer science to find the shortest path between two points in a graph or map. It is a heuristic search algorithm, which means that it uses an estimate of the distance between nodes to guide its search.

The A* algorithm works by maintaining a list of nodes that have been visited, as well as a list of nodes that are yet to be visited. It starts at the starting node and explores the neighboring nodes, calculating their "f score," which is a combination of the node's distance from the start node and its estimated distance to the target node. The algorithm then selects the node with the lowest f score as the next node to explore, and continues this process until it reaches the target node.

One of the main advantages of the A* algorithm is its efficiency. It is able to quickly find the shortest path between two points in a graph, even if the graph is quite large. Additionally, the algorithm can be customized by changing the heuristic function used to estimate the distance between nodes. This allows the algorithm to be tailored to specific applications, such as pathfinding in video games or route planning for logistics.

* **A star pathfinding algorithm**

![Image](https://user-images.githubusercontent.com/28188300/199688841-395a904d-4663-434a-afa6-07caad4e7608.gif)


[Source code](https://github.com/Przemekkkth/A-Star-Pathfinding_Qt-Cpp)

[See online](/assets/games/starpathfinding/index.html)


