---
title: Kruskals Greedy Algorithm for MST
description: 
permalink: posts/{{ title | slug }}/index.html
date: '2019-06-04'
tags: [cp-notes]
---

The code that follows was already written before. To do it, I created a straightforward linked list + insert using a queued linked list and then sorting it was my original plan, but it seemed to be overcomplicated.

Simply insert the sort so that the tail is very slight. It serves no purpose, and, when used in the tree list, appears more NC. Actually, its better to use `heap` or `qsort` after all input has been received, and to dynamically allocate enough space based on the input `n`.

```cpp
#include<stdio.h>
#include<stdlib.h>

/*
* kruskal's greedy algorithm to find the minimum spanning tree (heap+join set)
* test input data:

6 10 //6 vertices and 10 edges
1 5 2 //the weight of the edges from vertex 1 to vertex 5 2
2 4 3
4 5 1
0 3 6
2 3 7
5 2 6
4 0 7
1 3 5
1 2 4
3 5 9

*/

struct edge {
    intstart, end, weight;
};

void siftdown (struct edge *a, int n, int i) {
    struct edge t;
    if (i <1 || i> n) return;
    while(i * 2 <= n) {
        i *= 2;
        if (i + 1 <= n && a[i].weight> a[i + 1].weight) i++;
        if(a[i].weight <a[i/2].weight) {
            t = a[i];
            a[i] = a[i/2];
            a[i/2] = t;
        }
        else {
            break;
        }
    }
}

void siftup (struct edge *a, int n, int i) {
    struct edge t;
    if (i <1 || i> n) return;
    while (i> 1) {
        if(a[i].weight <a[i/2].weight) {
            t = a[i];
            a[i] = a[i/2];
            a[i/2] = t;
        }
        else {
            break;
        }
        i /= 2;
    }
}

void makeheap(struct edge *a, int n) {
    int i;
    for(i = n / 2; i >= 1;
        --i ) { siftdown(a, n, i);
    }
}

struct edge pop(struct edge *a, int *n) {
    structedge t;
    t = a[1];
    a[1] = a[*n];
    (*n)--; //hanging here for the second time, can’t write *n--(*n; n-= 1;), to write (*n)--
    siftdown(a, *n, 1);
    returnt;
}

void dump(struct edge *a, int n) {
    int i;
    for (i = 0; i <n; ++i) {
        printf("edge (%d, %d), %d\n", a[i].start, a[i].end, a[i].weight);
    }
}

void initUnionSet(int a[], int n) {
    int i;
    for(i = 0; i <n; ++i) {
        a[i] = i;
    }
}

int getFather(int a[], int x) {
    returna[x] == x? x: (a[x] = getFather(a, a[x]));
}

void merge(int a[], int x, inty) {
    x = getFather(a, x);
    y = getFather(a, y);
    a[x] = y;
}

int haveCommonAncestor(int a[], int x, int y) {
    return(getFather(a, x) == getFather(a, y)? 1: 0);
}


int main () {
    int m, n, i, k, *father;
    struct edge *input, *output, t;

    scanf("%d%d", &m, &n);
    input = (struct edge *)malloc(sizeof(struct edge) * (n + 1));
    for (i = 1; i <= n; ++i) {
        scanf("%d%d%d", &input[i].start, &input[i].end, &input[i].weight);
    }
    makeheap(input, n);
    dump(input+1, n);
    printf("end input\n");
    
    intn1 = n;
    output = (struct edge *)malloc(sizeof(structedge) * (m-1));
    father = (int *)malloc(sizeof(int) * n);
    initUnionSet(father, m);
    k = 0;
    printf("\nStart:\n");
    while(n1> 0) {
        t = pop(input, &n1);
        printf("edge (%d,%d), %d ", t.start, t.end, t.weight);
        if (0 == haveCommonAncestor(father, t.start, t.end)) {
            printf("added in\n");
            output[k] = t;
            k++;
            merge(father, t.start, t.end);
            if (k == m-1) {
                printf("~~ok~~\n");
                break;
            }
        }
        else {
            printf("ignored\n");
        }
    }
    printf("\nresult:\n");
    dump(output, k);

    free(input);
    free(output);
    free(father);
    return0;
}
```
