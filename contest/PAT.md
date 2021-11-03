# PAT



Pay attention to **ide**, practice using dev cpp befor testing

whether ac or error, check  the optimal one

Order：follow the order by liu,**summarize the most exercises varieties**

 take notes(words, template,tricks)

Find bugs using Liu

20+25+25+30

**value review**

Pay attention to some tips just before pat!!!!

**Read the context slower, and put more consideration!**

## 1074

sort(nodes.begin(),nodes.end(),[] (node &a,node &b){ return **a.order<b.order**;});

Sort from small to big

## 1032

Suffix:A morpheme added at the end of a word to form a derivative, e.g., *-ation*, *-fy*, *-ing*, *-itis*.

## 1097

Init a array` cstring`

~~~c++
memset(isExist, false, sizeof(isExist))
~~~

because of the unconvinence finding a node by pointer, it is essential to sort nodes and iterate them by indices

## ***1103***

tie：平局

For a search problem :

- get the search space
- determine the strategy:
  - how to push the recursion
  - what to record when recursion
  - when to end the recursion
  - whether use current node and do different operations
  - starting position

a classic dfs:

~~~c++
void DFS(int index,int nowk,int sum,int facsum)
{
    if((nowk==k)&&(sum==n))
    {
        if(facsum>maxfacsum)
        {
            maxfacsum=facsum;
            ans=temp;
        }
        return;
    }
    else if((nowk>k)||(sum>n)||(index<1))
    {
        return;
    }
    else
    {
        // choose index
        temp.emplace_back(index);
        			DFS(index,nowk+1,sum+fac[index],facsum+index);
        // not choose
        temp.pop_back();
        DFS(index-1,nowk,sum,facsum);
    }
}
~~~

## 1091

volume：容量

Bfs:use a queue

## 1020

递归过程的考虑，一定是一个抽象的、有概括性的过程考虑，而不是从一个具体的例子去建模，要从通用的模版角度，利用下标等信息，推进递归的进程；同时，处理好边界条件以及递归结束条件。

从具体的例子体会到要使用递归思想后，就开始考虑通用的模式，而不要试想从例子中得出。

## 1058

Make use of ` scanf` flexibly

~~~c++
scanf("%lld.%lld.%lld %lld.%lld.%lld", &a, &b, &c, &d, &e, &f);
//input a string like "xx.xx.xx"
//no need to split
~~~

## 1073

~~~c++
gets(str)->cin.getline(str,len)
~~~

## l5896

Set\map:**ordered**

rBegin():**the last one**

find if a **key** in a map:

~~~c++
map.count(key)
//in:1,not:0
~~~

## l2033

promise that every number%x **equals**

给定数轴上的n个点，在数轴上的所有点中，中位数离所有顶点的距离之和最小。

（加多减少，中间最低）

## l2035

make both of two sum **close to sum/2**

` trick:times 2 every element before calculating(make sure that sum/2 is intager)`

Calculate sum of selecting 0~n element from half of the array 

~~~c++
class Solution {
public:
    int minimumDifference(vector<int>& nums) {
        for(auto &num:nums)
        {
            //in case that sum/2 is not intager
            num*=2;
        }
        int sum=accumulate(nums.begin(),nums.end(),0);
        int n=nums.size()/2;
        vector<unordered_set<int>> ls(n+1),rs(n+1);
        ls[0].insert(0);
        rs[0].insert(0);
        //an elegant dynamic programming
        //find n+1 possible situations from two parts 
        //scan every element begin to end(you use curr based on count-1)
        for(int curr=0;curr<n;curr++)
        {
            //select in a strict order,but update using count variation
            for(int count=curr;count>=0;count--)
            {
                for(auto val:ls[count])
                {
                    ls[count+1].insert(val+nums[curr]);
                }
            }
        }
        for(int curr=n;curr<2*n;curr++)
        {
            //right part ,restart1 
            for(int count=curr-n;count>=0;count--)
            {
                for(auto val:rs[count])
                {
                    rs[count+1].insert(val+nums[curr]);
                }
            }
        }
        vector<vector<int>> lp(n+1),rp(n+1);
        for(int i=0;i<n+1;i++)
        {
            lp[i]=vector<int>(ls[i].begin(),ls[i].end());
            rp[i]=vector<int>(rs[i].begin(),rs[i].end());
            //all the possible situations,use two pointers to close to target
            sort(lp[i].begin(),lp[i].end());
            sort(rp[i].begin(),rp[i].end());
        }
        int target=sum/2;
        int ans=INT_MAX;
        for(int i=0;i<n+1;i++)
        {
            int lefp=0;
            int rightp=rp[n-i].size()-1;
            while((lefp<lp[i].size())&&(rightp>=0))
            {
                //target:the real sum of original array,times 2 beforehand
                int curr_sum=lp[i][lefp]+rp[n-i][rightp];
                ans=min(ans,abs(curr_sum-target));
                if(curr_sum<target)
                {
                    lefp++;
                }
                else
                {
                    rightp--;
                }
            }
        }
        return ans;
        
    }
};

~~~

(not the optimal solution...)

## l5905

注意到timetime和changechange都是全局统一的，我们到达一个点的时间，实际上是由经过的边数唯一确定的。

每条边一样，走的路少了，对应的等的时间也会少。

二次松弛？

假设最短k到n，第二短（不加回头）为k+1，若回头，为k+2

~~~c++
const int INF = 0x3f3f3f3f;

class Solution {
public:
    int secondMinimum(int n, vector<vector<int>>& edges, int time, int change) {
        vector<vector<int>> adj(n);
      //correct to 0-n-1
      //more convnient to use
        for (auto &edge : edges) {
            int u = edge[0] - 1, v = edge[1] - 1;
            adj[u].push_back(v);
            adj[v].push_back(u);
        }
        
        queue<int> q;
      //the smallest number of edge to this node
        vector<int> d(n, INF);
      //whether we can get n using k+1(d[n-1]+1) edges
        vector<bool> can(n);
        d[0] = 0;
        q.emplace(0);
        while (!q.empty()) {
            int u = q.front();
            q.pop();
          //a way to v passing u
            for (int v : adj[u]) {
              //find the shortest way to v(one-way,if you go back from u,it must be longer)
                if (d[u] + 1 < d[v]) {
                    d[v] = d[u] + 1;
                  //normal bfs,you first get the floor of v
                    q.emplace(v);
                } else if (d[u] == d[v] || (can[u] && d[u] < d[v])) {     
                  //u is on the way to v(using d[v]+1)
                  //1.shortest equals
                  //2.promise that u is shadow than v,and can[u]+shortest[u,v]
                  //first time to find that we can get v using d[v]+1
                    if (!can[v])
                      //take the place of u,examine every possible twice 
                        q.emplace(v);
                    can[v] = true;
                }
            }
        }
        
        int dist = d[n - 1] + 2;
        if (can[n - 1]) 
            dist--;
        
        int t = 0;
        while (dist--) {
            t += time;
          //pass the last edge to destination,you dont need to wait
            if (dist > 0 && t % (2 * change) >= change)
                t = (t / (2 * change) + 1) * 2 * change;
        }
        
        return t;
    }
};
~~~

Mycode:

~~~c++
class Solution
{
public:
    int secondMinimum(int n, vector<vector<int>> &edges, int time, int change)
    {
        vector<vector<int>> adj(n);
        for (const auto &edge : edges)
        {
            int u = edge[0] - 1;
            int v = edge[1] - 1;
            adj[u].push_back(v);
            adj[v].push_back(u);
        }
        queue<int> q;
        vector<int> shortest(n, INT_MAX);
        vector<int> second(n, 0);
        q.emplace(0);
        shortest[0] = 0;
        while (!q.empty())
        {
            int u = q.front();
            q.pop();
            for (int v : adj[u])
            {
                if ((shortest[u] + 1) < shortest[v])
                {
                    shortest[v] = shortest[u] + 1;
                    q.emplace(v);
                }
                else if ((shortest[u] == shortest[v]) || ((second[u] == 1) && (shortest[u] < shortest[v])))
                {
                    if (second[v] == 0)
                    {
                        q.emplace(v);
                    }
                    second[v] = true;
                }
            }
        }

        int dist = shortest[n - 1] + 2;
        if (second[n - 1] == 1)
        {
            dist--;
        }
        int ans = 0;
        while (dist--)
        {
            ans += time;
            if (dist > 0 && (ans % (2 * change) >= change))
            {
                ans = (ans / (2 * change) + 1) * 2 * change;
            }
        }
        return ans;
    }
};
~~~

## PAT1077

**Reverse strings** to find common longest **prefix**, then we can scan using same index

find the minlen of string in case of segment fault

you need to **get a whole line** to string

## PAT1062

Sort all info **in one manner!**

~~~c++
// order only using this one
bool cmp(info &a, info &b)
{
    // return a boolean expression to subtract situation
    if(a.flag!=b.flag) return a.flag<b.flag;
    else if(a.sum!=b.sum) return a.sum>b.sum;
    else if(a.vg!=b.vg) return a.vg>b.vg;
    else a.id<b.id;
}
~~~

Cin faster than printf

## pat1012

Accumulate:**numeric**

Now:determine to compare wich as a **index**

~~~c
bool cmp(Stu &a, Stu &b)
{ return a.grade[now]>b.grade[now];}
~~~

consider about same rank:

- the first one is must 1st,but the last is **not definately Nth**
- next one depends on the beforehand one ,it's the **correct order**

~~~c
Rank[stu[0].id][now]=1;
        for(int i=1;i<N;i++)
        {
            if(stu[i].grade[now]==stu[i-1].grade[now])
            {
                Rank[stu[i].id][now]=Rank[stu[i-1].id][now];
            }
            else
            {
                Rank[stu[i].id][now]=i+1;
            }
        }
        // some extrme examples!
        // for(int j=0;j<N-1;)
        // {
        //     if(stu[j].grade[now]==stu[j+1].grade[now])
        //     {
        //         Rank[stu[j].id][now]=Rank[stu[j+1].id][now]=curr_rank;
        //         curr_rank+=2;
        //         j+=2;
        //     }
        //     else
        //     {
        //         Rank[stu[j].id][now]=curr_rank;
        //         curr_rank++;
        //         j++;
        //     }
        // }
        // Rank[stu[N-1].id][now]=N;
~~~

## pta1016

chronological：ordered by time series
