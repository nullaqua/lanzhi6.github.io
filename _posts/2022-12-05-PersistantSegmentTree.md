---
title: PersistantSegmentTree
date: 2022-12-05 16:00:00 +0800
categories: [cpp,线段树]
tags: []
author: Lanzhi
math: true
---

可持久化动态开点权值线段树(维护区间第$k$大)

```c++
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;
typedef unsigned long long ull;

struct SegmentTree
{
    SegmentTree(int l,int r) : l(l),r(r),sum(0),ls(nullptr),rs(nullptr)
    {
    }

private:
    int l,r;
    int sum;
    SegmentTree *ls,*rs;

    inline void makeSon()
    {
        int mid=(this->l+this->r)>>1;
        if (ls==nullptr)
        {
            ls=new SegmentTree(l,mid);
        }
        if (rs==nullptr)
        {
            rs=new SegmentTree(mid+1,r);
        }
    }

    inline SegmentTree *copy()
    {
        auto *p=new SegmentTree(this->l,this->r);
        p->sum=this->sum;
        p->ls=this->ls;
        p->rs=this->rs;
        return p;
    }

    inline void setUp()
    {
        this->sum=ls->sum+rs->sum;
    }

    void insert0(int x)
    {
        if (this->l==this->r&&this->l==x)
        {
            this->sum++;
            return;
        }
        int mid=(this->l+this->r)>>1;
        this->makeSon();
        if (x<=mid)
        {
            this->ls=this->ls->copy();
            this->ls->insert0(x);
        }
        else
        {
            this->rs=this->rs->copy();
            this->rs->insert0(x);
        }
        setUp();
    }

public:
    SegmentTree *insert(int x)
    {
        SegmentTree *p=this->copy();
        p->insert0(x);
        return p;
    }

    int search(SegmentTree *p,int id)
    {
        if (this->l==this->r)
        {
            return this->l;
        }
        this->makeSon();
        int x=p!=nullptr&&p->ls!=nullptr?p->ls->sum:0;
        if (this->ls->sum-x>=id)
        {
            return this->ls->search(p!=nullptr?p->ls:nullptr,id);
        }
        else
        {
            return this->rs->search(p!=nullptr?p->rs:nullptr,id-(this->ls->sum-x));
        }
    }
};

SegmentTree *root[200005];

int n,q;

int main()
{
    root[0]=new SegmentTree(-(1<<30),1<<30);
    cin>>n>>q;
    for (int i=1;i<=n;i++)
    {
        int x;
        cin>>x;
        root[i]=root[i-1]->insert(x);
    }
    while (q--)
    {
        int x,y,k;
        cin>>x>>y>>k;
        cout<<root[y]->search(root[x-1],k)<<'\n';
    }
    return 0;
}
```

