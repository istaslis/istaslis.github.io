##Dynamic programming

>Remember your Past.

 If the given problem can be broken up in to smaller sub-problems and these smaller subproblems are in turn divided in to still-smaller ones, and in this process, if you observe some over-lappping subproblems, then its a big hint for DP
 the optimal solutions to the subproblems contribute to the optimal solution of the given problem 

1. **(Top-down) Memoization**  Start solving the given problem by breaking it down. If you see that the problem has been solved already, then just return the saved answer.
2. **(Bottom-up) Dynamic Programming** Analyze the problem and see the order in which the sub-problems are solved and start solving from the trivial subproblem, up towards the given problem. 
	
Difference with respect to divide-and-conquer: in d-and-c the problem is divided by non-overlapping subproblems

Useful: $C(n,m) = C(n-1,m) + C(n-1,m-1)$



###Problem : Minimum Steps to One
On a positive integer, you can perform any one of the following 3 steps. 

1. Subtract 1 from it. ( n = n - 1 )  , 
2. If its divisible by 2, divide by 2. ( if n % 2 == 0 , then n = n / 2  )  , 
3. If its divisible by 3, divide by 3. ( if n % 3 == 0 , then n = n / 3  ). 

Given a positive integer n, find the minimum number of steps that takes n to 1
eg: 

1. For n = 1 , output: 0
2. For n = 4 , output: 2  ( 4  /2 = 2  /2 = 1 )    
3. For n = 7 , output: 3  (  7  -1 = 6   /3 = 2   /2 = 1 )

#### Recursive solution
``` python
def f(N):
    if N==1: return 0
    r = 1+f(N-1)
    
    if N%2==0: r = min(r,1+f(N/2))
    if N%3==0: r = min(r,1+f(N/3))
    
    return r
>> f(10)
>> 3
>> %timeit f(100)
>> 100 loops, best of 3: 19.2 ms per loop
```

But many f(N) are recalculated multiple times. Solution - remember them.
####DP solution

Main idea: remember any intermediate solution in the temporary array `M`, and don't repeat calculations if there is a solution already.

Here in order to use timing command, I wrapped DP solution into separate function which initializes array first.

``` python
def fDPrun(N):
    M = [-1 for i in range(1000)]
    M[1] = 0
    def fDP(n):
        if M[n]!=-1: return M[n]
        r=1+fDP(n-1)
        if n%2==0: r = min(r,1+fDP(n/2))
        if n%3==0: r = min(r,1+fDP(n/3))
        M[n] = r
        return r
    return fDP(N)

>> fDPrun(10)
>> 3
>> %timeit fDPrun(100)
>> 10000 loops, best of 3: 110 µs per loop
```

###Problem: Longest common subsequence (LCS)

####Recursive solution:
``` python
A=[1,4,6,8,5,3,6,8]
B=[4,6,6,2,1,4,5,6,8]
def LCSrec(N,M):
    if N==0 or M==0: return 0
    if A[N-1]==B[M-1]: return 1+LCSrec(N-1,M-1)
    else: return max(LCSrec(N-1,M),LCSrec(N,M-1))

>> %timeit LCSrec(len(A),len(B))  
>> 1000 loops, best of 3: 277 µs per loop
```
####Dynamic programming
``` python
N=len(A)
M=len(B)

def LCSdp():
    Mlcs=[[-1 for i in range(M+1)] for j in range(N+1)]
    for i in range(N+1):
        for j in range(M+1):
            if i==0 or j==0:
                Mlcs[i][j] = 0
            else: ##important, you can easily go negative in index...
                if A[i-1]==B[j-1]: 
                    Mlcs[i][j] = 1+Mlcs[i-1][j-1]
                else: Mlcs[i][j] = max(Mlcs[i-1][j],Mlcs[i][j-1])
    return Mlcs[N][M]
>> %timeit LCSdp()
>> 10000 loops, best of 3: 47.8 µs per loop
```
but for larger values A,B

``` python
A=list("AAACCGTGAGTTATTCGTTCTAGAA")
B=list("CACCCCTAAGGTACCTTTGGTT")
>> %timeit LCSdp()
>> 1000 loops, best of 3: 311 µs per loop
```

After the computation the resulting `Mlcs` array for sequencies `A=[1,4,6,8,5,3,6,8]` and `B=[4,6,6,2,1,4,5,6,8]` looks like this:

```
[0, 4, 6, 6, 2, 1, 4, 5, 6, 8]
[1, 0, 0, 0, 0, 1, 1, 1, 1, 1]
[4, 1, 1, 1, 1, 1, 2, 2, 2, 2]
[6, 1, 2, 2, 2, 2, 2, 2, 3, 3]
[8, 1, 2, 2, 2, 2, 2, 2, 3, 4]
[5, 1, 2, 2, 2, 2, 2, 3, 3, 4]
[3, 1, 2, 2, 2, 2, 2, 3, 3, 4]
[6, 1, 2, 3, 3, 3, 3, 3, 4, 4]
[8, 1, 2, 3, 3, 3, 3, 3, 4, 5]
```

where first column represents vector `A` and first row represents `B`.

```
0	4	6	6	2	1	4	5	6	8	
1	0	0	0	0	1*	1	1	1	1	
4	1	1	1	1	1	2*	2	2	2	
6	1	2	2	2	2	2*	2	3	3	
8	1	2	2	2	2	2*	2	3	4	
5	1	2	2	2	2	2	3*	3	4	
3	1	2	2	2	2	2	3*	3	4	
6	1	2	3	3	3	3	3	4*	4	
8	1	2	3	3	3	3	3	4	5*	

```

For bigger sequences

``` python
A=list("AAACCGTGAGTTATTCGTTCTAGAA")
B=list("CACCCCTAAGGTACCTTTGGTTC")
>> %timeit LCSdp()
>> 1000 loops, best of 3: 304 µs per loop
```
and LCS is equal to 14. The recusive solution hasn't finished in the reasonable time...
Backtrack is pretty simple: start with longest and go diagonal if `A[i]=B[j]`, stay on the longer path otherwise.

```
0	C*	A	C	C	C	C	T	A	A	G	G	T	A	C	C	T	T	T	G	G	T	T	C	
A	0	1*	1*	1*	1	1	1	1	1	1	1	1	1	1	1	1	1	1	1	1	1	1	1	
A	0	1	1	1*	1	1	1	2	2	2	2	2	2	2	2	2	2	2	2	2	2	2	2	
A	0	1	1	1*	1	1	1	2	3	3	3	3	3	3	3	3	3	3	3	3	3	3	3	
C	1	1	2	2	2*	2	2	2	3	3	3	3	3	4	4	4	4	4	4	4	4	4	4	
C	1	1	2	3	3	3*	3	3	3	3	3	3	3	4	5	5	5	5	5	5	5	5	5	
G	1	1	2	3	3	3*	3	3	3	4	4	4	4	4	5	5	5	5	6	6	6	6	6	
T	1	1	2	3	3	3	4*	4*	4*	4	4	5	5	5	5	6	6	6	6	6	7	7	7	
G	1	1	2	3	3	3	4	4	4	5*	5	5	5	5	5	6	6	6	7	7	7	7	7	
A	1	2	2	3	3	3	4	5	5	5*	5	5	6	6	6	6	6	6	7	7	7	7	7	
G	1	2	2	3	3	3	4	5	5	6	6*	6	6	6	6	6	6	6	7	8	8	8	8	
T	1	2	2	3	3	3	4	5	5	6	6	7*	7*	7*	7*	7	7	7	7	8	9	9	9	
T	1	2	2	3	3	3	4	5	5	6	6	7	7	7	7	8*	8	8	8	8	9	10	10	
A	1	2	2	3	3	3	4	5	6	6	6	7	8	8	8	8*	8	8	8	8	9	10	10	
T	1	2	2	3	3	3	4	5	6	6	6	7	8	8	8	9	9*	9	9	9	9	10	10	
T	1	2	2	3	3	3	4	5	6	6	6	7	8	8	8	9	10	10*	10*	10	10	10	10	
C	1	2	3	3	4	4	4	5	6	6	6	7	8	9	9	9	10	10	10*	10	10	10	11	
G	1	2	3	3	4	4	4	5	6	7	7	7	8	9	9	9	10	10	11	11*	11	11	11	
T	1	2	3	3	4	4	5	5	6	7	7	8	8	9	9	10	10	11	11	11	12*	12	12	
T	1	2	3	3	4	4	5	5	6	7	7	8	8	9	9	10	11	11	11	11	12	13*	13	
C	1	2	3	4	4	5	5	5	6	7	7	8	8	9	10	10	11	11	11	11	12	13	14*	
T	1	2	3	4	4	5	6	6	6	7	7	8	8	9	10	11	11	12	12	12	12	13	14*	
A	1	2	3	4	4	5	6	7	7	7	7	8	9	9	10	11	11	12	12	12	12	13	14*	
G	1	2	3	4	4	5	6	7	7	8	8	8	9	9	10	11	11	12	13	13	13	13	14*	
A	1	2	3	4	4	5	6	7	8	8	8	8	9	9	10	11	11	12	13	13	13	13	14*	
A	1	2	3	4	4	5	6	7	8	8	8	8	9	9	10	11	11	12	13	13	13	13	14
```

```python
i=N
j=M
path = []
lcs = []
while i>0 and j>0:
    if A[i-1]==B[j-1]:
        path.append((i,j))
        lcs.insert(0,A[i-1])
        i-=1
        j-=1
    else:
        path.append((i,j))
        if Mlcs[i-1][j]<Mlcs[i][j-1]:
            j-=1
        else:
            i-=1
lcs
>>['A', 'C', 'C', 'T', 'G', 'G', 'T', 'T', 'T', 'T', 'G', 'T', 'T', 'C']
```

### Knapsack problem
Given weights `w[i]` and values `v[i]` of items, put maximum total value into the sack of the total weight less than or equal to `W`.
####Recursive solution

Idea: if we know the solution for the weight W, taking one item `v[i]` from the sack gives solution for `W-w[i]`.

``` python
N=3
vi=[1,4,6]
wi=[1,2,5]

def KnapsackRec(W):
    if W==0: return 0
    r=-1
    for i in range(N):
        if W-wi[i]>=0:
            r=max(r,vi[i]+KnapsackRec(W-wi[i]))
    return r
>> KnapsackRec(10)
>> 20
```

####DP solution, bottom-up
The same, but solution is built starting from trivial case `W=0`, and increasing.

``` python
N=3
vi=[1,4,6]
wi=[1,2,5]


def Knapsack(W):
    mi=[-1 for x in range(W)]
    mi[0]=0
    for w in range(1,W):
        for i in range(N):         
            if w-wi[i]>=0:
                mi[w] = max(mi[w],vi[i]+mi[w-wi[i]])
    print mi
>> Knapsack(11)
>> [0, 1, 4, 5, 8, 9, 12, 13, 16, 17, 20]
```