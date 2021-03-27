---
title: "[Luogu P4246] [SHOI2008]堵塞的交通"
date: 2019-03-22 04:04:18
tags: "迁移"
---
<h1>题面</h1>
<p><a href="https://www.luogu.org/problemnew/show/P4246" target="_blank"  rel="nofollow" >P4246 [SHOI2008]堵塞的交通</a></p>
<hr />
<h1>Solution</h1>
<p><del>这题的确是有线段树上大分类讨论的在线做法</del>，但是本菜鸡还是想主要讲一下离线暴力做法。</p>
<p>这题我们考虑离线下来搞。离线之后，我们会发现，<strong>某条边会在某些询问区间中出现。</strong><br />
考虑<strong>以询问的编号为下标建线段树，我们把每一条边出现的时间段全部加到线段树里面去。</strong><br />
接下来，直接<strong>在线段树上跑dfs</strong>,每到一个区间，就把这个区间里面存的边通通在并查集中连上；每完成一个区间，就把这个区间连上的边通通取消（类似于回溯）。<br />
这样搞，我们每次到一个叶子节点的时候，这个叶子节点代表的询问上所要连的边一定已经全部连上了，直接在并查集中查询任意节点的父亲的$size$是否为$n$即可。</p>
<p>因为我们这里有撤销（回溯）操作，因此必需使用<strong>按秩合并</strong>的并查集。我们只需要开一个栈，把每次修改的fa的节点记录下来即可完成撤销的操作。</p>
<p>对于边和点，我们大力编号一下即可qwq。</p>
<p>时间复杂度$O(nlog^2n)$ 吗？<br />
问题是这玩意能过啊.....<br />
<del>因此，我们的时间复杂度是$O($能过$)$</del></p>
<p>就酱，我们又切掉一道题啦。(ﾉﾟ∀ﾟ)ﾉ</p>
<hr />
<h1>Code</h1>
<p><strong>数据生成器</strong></p>
<pre><code class="language-cpp line-numbers">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;cstdlib&gt;
#include&lt;ctime&gt;
#include&lt;cmath&gt;
using namespace std;
const int N=5;
const int M=25;
int main()
{
    srand(time(NULL));
    freopen("4246.in","w",stdout);

    int n=N;
    cout&lt;&lt;n&lt;&lt;endl;

    for(int i=1;i&lt;=M;i++)
    {
        int op=rand()%5;
        if(op==0 or op==1)
        {
            int x1=rand()%n+1,x2=rand()%n+1,y1=rand()%2+1,y2=rand()%2+1;
            cout&lt;&lt;"Ask "&lt;&lt;y1&lt;&lt;" "&lt;&lt;x1&lt;&lt;" "&lt;&lt;y2&lt;&lt;" "&lt;&lt;x2&lt;&lt;endl;
        }
        else
        {
            int x1=rand()%n+1,x2=rand()%n+1,y1=rand()%2+1,y2=rand()%2+1;
            while(1)
            {
                if(x1==x2 and abs(y2-y1)==1) break;
                if(y1==y2 and abs(x2-x1)==1) break;
                x2=rand()%n+1,y2=rand()%2+1;
            }
            if(op==2) cout&lt;&lt;"Close ";
            else cout&lt;&lt;"Open ";
            cout&lt;&lt;y1&lt;&lt;" "&lt;&lt;x1&lt;&lt;" "&lt;&lt;y2&lt;&lt;" "&lt;&lt;x2&lt;&lt;endl;
        }
    }

    cout&lt;&lt;"Exit";
    return 0;
}

</code></pre>
<p><strong>正解</strong></p>
<pre><code class="language-cpp line-numbers">//Luogu P4246 [SHOI2008]堵塞的交通
//Mar,22ed,2019
//线段树分治离线维护动态图连通性
#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;cstring&gt;
#include&lt;vector&gt;
using namespace std;
long long read()
{
    long long x=0,f=1; char c=getchar();
    while(!isdigit(c)){if(c=='-') f=-1;c=getchar();}
    while(isdigit(c)){x=x*10+c-'0';c=getchar();}
    return x*f;
}
const int N=100000+100;
const int M=N*4;
int ans[N];
struct UnF
{   
    int size[N],fa[N],mstack[M],top;
    void Init(int n)
    {
        for(int i=1;i&lt;=n;i++)
            size[i]=1;
    }
    int FindFather(int x)
    {
        if(fa[x]==0) return x;
        return FindFather(fa[x]);
    }
    void Link(int x,int y)
    {
        mstack[++top]=0;
        int fa_x=FindFather(x),fa_y=FindFather(y);
        if(size[fa_x]&gt;size[fa_y]) 
            swap(x,y),swap(fa_x,fa_y);
        if(fa_x==fa_y) return;
        mstack[top]=fa_x;   
        fa[fa_x]=fa_y,size[fa_y]+=size[fa_x];
    }
    int Query(int x,int y)
    {
        if(FindFather(x)==FindFather(y))
            return true; 
        return false;
    }
    void Undo()
    {
        if(mstack[top]==0)
        {
            top--;
            return;
        }
        size[fa[mstack[top]]]-=size[mstack[top]];
        fa[mstack[top]]=0;
        top--;
    }
}unf;
struct OP
{
    int s,t,id;
}op[N],op2[M*20],e[M];
struct SegmentTree
{
    #define mid ((now_l+now_r)&gt;&gt;1)
    #define lson (now&lt;&lt;1)
    #define rson (now&lt;&lt;1|1)
    vector &lt;int&gt; w[N&lt;&lt;2];
    void Insert(int l,int r,int x,int now,int now_l,int now_r)
    {
        if(now_l&gt;=l and now_r&lt;=r)
        {
            w[now].push_back(x);
            return;
        }
        if(l&lt;=mid) Insert(l,r,x,lson,now_l,mid);
        if(r&gt;mid) Insert(l,r,x,rson,mid+1,now_r);
    }
    void dfs(int now,int now_l,int now_r)
    {
        if(now_l&gt;now_r) return;
        for(int i=0;i&lt;int(w[now].size());i++)
            unf.Link(e[w[now][i]].s,e[w[now][i]].t);
        if(now_l==now_r)
            ans[now_l]=unf.Query(op[now_l].s,op[now_l].t);
        else
        {
            dfs(lson,now_l,mid);
            dfs(rson,mid+1,now_r);
        }
        for(int i=0;i&lt;int(w[now].size());i++)
            unf.Undo();
    }
    #undef mid
    #undef lson
    #undef rson
}sgt;
int last[M],n,q,p;//q次询问,p个区间
bool w[3][N][3];
inline int GetID(int x,int y,int type)
{
    return ((y-1)*n+x-1)*3+type;
}
inline int GetID2(int x,int y)
{
    return (y-1)*n+x;
}
int main()
{
    freopen("4246.in","r",stdin);
    freopen("4246.out","w",stdout);

    n=read();
    char OP[10];
    while(1)
    {
        scanf("%s",OP+1);
        if(OP[1]=='E') break;
        else if(OP[1]=='O' or OP[1]=='C')
        {
            int y1=read(),x1=read(),y2=read(),x2=read(),type=1;
            if(OP[1]=='C') type=0;
            if(x1&gt;x2) swap(x1,x2),swap(y1,y2);
            if(y1&gt;y2) swap(x1,x2),swap(y1,y2);
            if(x2==x1+1)
            {
                if(w[y1][x1][0]==type) continue;
                w[y1][x1][0]=type;
                type=0;
            }
            else
            {
                if(w[y1][x1][2]==type) continue;
                w[y1][x1][2]=w[y2][x2][1]=type;
                type=2;
            }

            if(OP[1]=='O')
                last[GetID(x1,y1,type)]=q+1;
            else
            {
                op2[++p].s=last[GetID(x1,y1,type)],op2[p].t=q,op2[p].id=GetID(x1,y1,type);
                last[GetID(x1,y1,type)]=0;
            }
        }
        else
        {
            int y1=read(),x1=read(),y2=read(),x2=read();
            op[++q].s=GetID2(x1,y1),op[q].t=GetID2(x2,y2);
        }
    }
    for(int i=0;i&lt;=6*n+100;i++)
        if(last[i]!=0)
            op2[++p].s=last[i],op2[p].t=q,op2[p].id=i;

    for(int i=1;i&lt;=2;i++)
        for(int j=1;j&lt;=n;j++)
            for(int k=0;k&lt;=2;k++)
            {
                e[GetID(j,i,k)].s=GetID2(j,i);
                if(k==0) e[GetID(j,i,k)].t=GetID2(j+1,i);
                if(k==2) e[GetID(j,i,k)].t=GetID2(j,i+1);
            }
    unf.Init(n*2+1);
    for(int i=1;i&lt;=p;i++)
        if(op2[i].s&lt;=op2[i].t)
        {
            sgt.Insert(op2[i].s,op2[i].t,op2[i].id,1,1,q);
            cerr&lt;&lt;op2[i].s&lt;&lt;" "&lt;&lt;op2[i].t&lt;&lt;" "&lt;&lt;op2[i].id&lt;&lt;endl;
        }

    sgt.dfs(1,1,q);

    for(int i=1;i&lt;=q;i++)
        if(ans[i]==0)
            printf("N\n");
        else
            printf("Y\n");
    return 0;
}

</code></pre>
