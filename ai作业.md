动态规划算法

```pseudocode
QUEUE:=(s-s),g(s)=0; 
LOOP: IF QUEUE=( ) THEN EXIT(Fail); 
    PATH:=FIRST(QUEUE),n:=LAST(PATH); 
    IF GOAL(n) 
    	THEN EXIT(SUCCESS); 
    EXPAND(n)→{mi},计算g(mi)=g(n,mi); 
    REMOVE(s-n,QUEUE),ADD(s-mi,QUEUE); 
	在QUEUE中，如有多条到达某个公共节点的路径，只保留g值最小的那条路径，其余删去；g值小的排前。 
	GO LOOP
```



计算过程

初始化: 

```pseudocode
queue=(A2-A2), g(A2) = 7;
goal=E5;
```

循环

```pseudocode
LOOP 1: 
	path=A2-A2; n=A2; 
	expand: 
		n->A1, g(A1)=12;
        n->A3, g(A3)=18;
        n->B2, g(B2)=29;
   	queue(A1, A2, B2);

LOOP 2:
	A1
```

