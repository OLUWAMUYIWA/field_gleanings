> Let A = a 1 a 2 . . . a N and B = b 1 b 2 . . . b M be sequences of length N and M respectively. The edit graph for A
and B has a vertex at each point in the grid (x,y), x∈[0,N] and y∈[0,M]. The vertices of the edit graph are con-
nected by horizontal, vertical, and diagonal directed edges to form a directed acyclic graph. Horizontal edges con-
nect each vertex to its right neighbor, i.e. (x−1,y)→(x,y) for x∈[1,N] and y∈[0,M]. Vertical edges connect each
vertex to the neighbor below it, i.e. (x,y−1)→(x,y) for x∈[0,N] and y∈[1,M]. If a x = b y then there is a diagonal 
edge connecting vertex (x−1,y−1) to vertex (x,y). The points (x,y) for which a x = b y are called match points. The
total number of match points between A and B is the parameter R characterizing the Hunt & Szymanski algorithm
[11]. It is also the number of diagonal edges in the edit graph as diagonal edges are in one-to-one correspondence
with match points. Figure 1 depicts the edit graph for the sequences A = abcabba and B = cbabac.

![fig 1](https://user-images.githubusercontent.com/16711905/138037080-2b6f8fdd-dced-42ab-abc9-122b15fde819.png)


![fig 2](https://user-images.githubusercontent.com/16711905/138057513-487696c1-a974-43f1-8c8c-1d2548eff9aa.png)
