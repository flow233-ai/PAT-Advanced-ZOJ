这是该系列的第二期，以后每期都会更新10题目，且会一直保持更新（有一些暂时空缺的题目日后会补起来）。然后说明一下，有一些题目因为实在太简单了，就没有写思路或感想，直接分享代码了，所以大家懂得都懂，相信这种题目也难不住大家的。
# 1011：World Cup Betting
此题代码如下：
```cpp
#include<iostream>
#include <bits/stdc++.h>

using namespace std;

int main(){
    int count = 0;
    float maxnum,total;
    total = 1;
    char c;
    float temp;
    while(count != 3){
        for(int i = 0;i < 3;i++){
            cin >> temp;
            if(i == 0){
                maxnum = temp;
                c = 'W';
            }
            else{
                if(maxnum < temp){
                    maxnum = temp;
                    if(i == 1)
                        c = 'T';
                    else
                        c = 'L';
                }
            }
        }
        total *= maxnum;
        if(count != 0)
            cout<<' ';
        cout<<c;
        count++;
    }
    cout<<' '<<fixed<<setprecision(2)<<(total*0.65-1)*2; 
    return 0;
}
```
# 1012：The Best Rank
这道题大概花了70分钟AC，难度一般，我用了堆排序对四种成绩分别进行排序，但是建议做题前先想清楚思路，三思而后行。PAT最锻炼人的就是Debug能力，因为Checkpoint没有给信息，只知道有几个Checkpoint，所以只能通过测试和猜测来更正代码。这道题我最后有一个Checkpoint一直显示“答案错误”，经过分析，错误点只可能出现在1）前面的平均值计算或2）排序的同分情况；最后我逐个排查，最终发现确实是同分情况没有考虑，更正代码后就AC了。
通过这题，我还总结了一个方法，就是使用动态内存分配（**这样使得不同测试用例占用内存不同）**，这样的话即使Checkppoint本身不带信息，我们也可以通过每个Checkpoint后面的耗时和内存占用来推测——例如如果一个Chechpoint内存很大，说明测试样例输入多；如果一个Checkpoint内存小，但是这个点错了，说明是一些细节没有考虑在代码内，再多思考下细节等等）
此题代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
//数据类型一，用来存放每个信息的最佳排名和对应成绩类型
class Node{
public:
    string s;
    char c;
    int rank;
    Node(){ c = ' ';}
};
//数据类型二，用来对四种成绩排序
class Node2{
public:
    int index;
    int value;
    Node2(){index = -1;}
};

int N,M,tempN;
Node *A;
Node2 *a,*c,*m,*e;

void Swap(Node2 &a, Node2 &b);
void CreateHeap(int index,int input);
void DeleteHeap(int index,int input);

int main(){
    //数据读取
    cin >> N >> M;
    A = new Node[N];
    a = new Node2[N+1];
    c = new Node2[N+1];
    m = new Node2[N+1];
    e = new Node2[N+1];
    int numa,numc,numm,nume;
    string ss;float temp;
    for(int n = 0;n < N;n++){
        cin >> ss >> numc >> numm >> nume;
        temp = (float)(numc + numm + nume) / 3;
        if(temp - (int)temp >= 0.5)
            numa = (int)temp + 1;
        else
            numa = (int)temp;
        //cout<<numa<<' '<<numc<<' '<<numm<<' '<<nume<<endl;
        A[n].s = ss;
        a[n+1].index = n;
        c[n+1].index = n;
        m[n+1].index = n;
        e[n+1].index = n;
        a[n+1].value = numa;
        c[n+1].value = numc;
        m[n+1].value = numm;
        e[n+1].value = nume;
        //建立四个最大堆
        CreateHeap(n+1,1);
        CreateHeap(n+1,2);
        CreateHeap(n+1,3);
        CreateHeap(n+1,4);
    }
    tempN = N;
    //堆排序
    //引入同排名机制，同分同排名
    int tempnum,ranking;
    tempnum = -1;ranking = 1;
    while(tempN != 0){
        A[a[1].index].c = 'A';
        if(a[1].value == tempnum)
            A[a[1].index].rank = ranking;
        else{
            ranking = N - tempN + 1;
            A[a[1].index].rank = ranking;
        }
        tempnum = a[1].value;
        DeleteHeap(1,1);
        tempN--; 
    }
    tempN = N;tempnum = -1;ranking = 1;
    while(tempN != 0){
        if(c[1].value != tempnum)
            ranking = N - tempN + 1;
        if(A[c[1].index].rank > ranking){
            A[c[1].index].c = 'C';
            A[c[1].index].rank = ranking;
        }
        tempnum = c[1].value;
        DeleteHeap(1,2);
        tempN--;
    }
    tempN = N;tempnum = -1;ranking = 1;
    while(tempN != 0){
        if(m[1].value != tempnum)
            ranking = N - tempN + 1;
        if(A[m[1].index].rank > ranking){
            A[m[1].index].c = 'M';
            A[m[1].index].rank = ranking;
        }
        tempnum = m[1].value;
        DeleteHeap(1,3);
        tempN--;
    }
    tempN = N;tempnum = -1;ranking = 1;
    while(tempN != 0){
        if(e[1].value != tempnum)
            ranking = N - tempN + 1;
        if(A[e[1].index].rank > ranking){
            A[e[1].index].c = 'E';
            A[e[1].index].rank = ranking;
        }
        tempnum = e[1].value;
        DeleteHeap(1,4);
        tempN--;
    }
    //for(int i = 0;i < N;i++)
    //    cout<<A[i].s<<" "<<A[i].c<<" "<<A[i].rank<<endl;
    //输出每个学生的最佳排名
    string s2;
    for(int i = 0;i < M;i++){
        cin >> s2;
        int j = 0;
        for(;j < N;j++){
            if(A[j].s == s2){
                cout<<A[j].rank<<' '<<A[j].c<<endl;
                break;
            }
        }
        if(j == N)
            cout<<"N/A"<<endl;
    }
    return 0;
}

void Swap(Node2 &a, Node2 &b){
    int temp1,temp2;
    temp1 = a.index;
    temp2 = a.value;
    a.index = b.index;
    a.value = b.value;
    b.index = temp1;
    b.value = temp2;
}

void CreateHeap(int index,int input){
    //建堆，插入新元素后调整成堆
    Node2 *temps;
    if(input == 1)
        temps = a;
    else if(input == 2)
        temps = c;
    else if(input == 3)
        temps = m;
    else
        temps = e;
    int temp = index;
    for(;temp != 1;temp = temp/2){
        if(temps[temp].value > temps[temp/2].value)
            Swap(temps[temp],temps[temp/2]);
        else
            break;
    }
}

//堆的删除，用来实现堆排序
void DeleteHeap(int index,int input){
    Node2 *H;
    if(input == 1)
        H = a;
    else if(input == 2)
        H = c;
    else if(input == 3)
        H = m;
    else
        H = e;
    int Ms = tempN;
    if(Ms == 0)
        return;
    else if(Ms == 1){
        return;
    }
    if(index == 1){
        H[1].index = H[Ms].index;
        H[1].value = H[Ms].value;
    }
    //从两个子节点中找出比现在这个元素小的，交换位置并迭代函数
    //下面考虑了两种节点的多种情况（可能为空）
    if(2*index <= tempN && 2*index+1 <= tempN && H[2*index].index != -1 && H[2*index+1].index != -1){
        if(H[2*index].value >= H[2*index+1].value && H[2*index].value > H[index].value){
            Swap(H[index],H[2*index]);
            DeleteHeap(2*index,input);
        }
        else if(H[2*index].value <= H[2*index+1].value && H[2*index+1].value > H[index].value){
            Swap(H[index],H[2*index+1]);
            DeleteHeap(2*index+1,input);
        }
        else
            return;
    }
    else if(2*index <= tempN && 2*index+1 <= tempN && H[2*index].index != -1 && H[2*index+1].index == -1){
        if(H[2*index].value > H[index].value){
            Swap(H[index],H[2*index]);
            DeleteHeap(2*index,input);
        }
        else
            return;
    }
    else if(2*index <= tempN && 2*index+1 <= tempN && H[2*index].index == -1 && H[2*index+1].index != -1){
        if(H[2*index+1].value > H[index].value){
            Swap(H[index],H[2*index+1]);
            DeleteHeap(2*index+1,input);
        }
        else
            return;
    }
    else
        return;
}


```
# 1013：Battle Over Cities
这道题怎么说呢？我一开始一下子就想到连通分量个数，但是打算用并查集的，通过数并查集中根节点的个数+重构邻接矩阵来做这道题，但后来突然想起来好像如果只是单纯数连通分量个数的话，DFS或BFS会更好一点，所以就改成了DFS+重构邻接矩阵的方法了，45分钟AC；不过后来一想，**如果这道题改成需要让你详细写出每个极大连通分量的具体包含节点，可能并查集也可以做，不过这种情况下DFS还是可以做**，只是要在现有代码的基础上稍作修改，感兴趣的小伙伴可以想想这个问题啦。
此题代码如下：
```cpp
#include<iostream>
#define Maxsize 1000

using namespace std;
int N,M,K,index,count;
int **a;
int *visited,*tempa;

void DFS(int ind);

int main(){
    //数据读取部分
    cin >> N >> M >> K;
    a = new int*[N];
    tempa = new int[N];
    visited = new int[N];
    for(int i = 0;i < N;i++)
        a[i] = new int[N];
    for(int i = 0;i < N;i++)
        for(int j = i;j < N;j++)
            a[i][j] = a[j][i] = -1;
    int index1,index2;
    for(int i = 0;i < M;i++){
        cin >> index1 >> index2;
        a[index1-1][index2-1] = a[index2-1][index1-1] = 1;
    }
    //check部分
    for(int i = 0;i < K;i++){
        for(int j = 0;j < N;j++)
            visited[j] = 0;
        count = 0;
        cin >> index;
        for(int j = 0;j < N;j++){
            tempa[j] = a[index-1][j];
            a[index-1][j] = a[j][index-1] = -1;
        }
        //DFS确定连通分量个数
        for(int i = 0;i < N;i++){
            if(visited[i] == 0){
                DFS(i);
                count++;
            }
        }
        cout<<(count-2)<<endl;
        //恢复邻接矩阵
        for(int j = 0;j < N;j++)
            a[index-1][j] = a[j][index-1] = tempa[j];
    }
    return 0;
}

void DFS(int ind){
    visited[ind] = 1;
    if(ind == index-1)
        return;
    for(int i = 0;i < N;i++){
        if(a[ind][i] != -1 && visited[i] != 1)
            DFS(i);
    }
}

```
# 1014：Waiting in Line
这种题目就属于是一种变换的题目，就是不是让你求最优什么的，就是给你一套变换或者过程进行的规则，你用代码编写一个符合规则的模拟仿真，输出结果即可。这题用到了队列。
此题代码如下：
```cpp
#include<iostream>
#include<queue>
#include<string>

using namespace std;
int N,M,K,Q;
int *poss,*done,*total,*now;
queue<int> *q;

int findmin();
int findmin2();

int main(){
    cin >> N >> M >> K >> Q;
    poss = new int[K];
    done = new int[K];
    now = new int[N];
    for(int i = 0;i < K;i++)
        cin >> poss[i];
    q = new queue<int>[N];
    int tempnow = 0;
    for(int i = 0;i < K;i++){
        if(i >= N*M)
            break;
        q[i % N].push(i);
        tempnow = i;
    }
    for(int i = 0;i < N;i++){
        if(q[i].size() != 0)
            now[i] += poss[q[i].front()];
    }
    int index;
    int nowf = 0;
    while(nowf != K){
        index = findmin2();
        //cout<<index<<' '<<q[index].front()<<' '<<now[index]<<endl;
        done[q[index].front()] = now[index];
        q[index].pop();
        nowf++;
        if(tempnow < K - 1){
            tempnow++;
            q[index].push(tempnow);
        }
        if(q[index].size() != 0)
            now[index] += poss[q[index].front()];
    }
    int num;
    string h,m;
    for(int i = 0;i < Q;i++){
        cin >> index;
        num = done[index-1];
        if(num - poss[index-1] >= 540){
            cout<<"Sorry"<<endl;
            continue;
        }
        int hour = 8 + num / 60;
        int minute = num % 60;
        h = to_string(hour);
        m = to_string(minute);
        if(h.size() == 1)
            h = '0' + h;
        if(m.size() == 1)
            m = '0' + m;
        cout<<h<<':'<<m<<endl;
    }
    return 0;
}

int findmin(){
    int minnum = -1;
    for(int i = 0;i < N;i++){
        if(minnum == -1)
            minnum = i;
        else if(q[minnum].size() > q[i].size())
            minnum = i;
    }
    return minnum;
}

int findmin2(){
    int minnum = -1;
    for(int i = 0;i < N;i++){
        if(minnum == -1 && q[i].size() != 0)
            minnum = i;
        else if(minnum != -1 && now[minnum] > now[i] && q[i].size() != 0)
            minnum = i;
    }
    return minnum;
}
```
# 1015：Reversible Primes
此题代码如下：
```cpp
#include<iostream>
#include<vector>
#include<cmath>

using namespace std;
int N,D,count;

bool Isprime(int num);

int main(){
    vector<int> stack;
    while(true){
        //数据读取
        count = 0;
        cin >> N;
        if(N < 0)
            break;
        cin >> D;
        //判断N自身是不是质数
        if(Isprime(N) == false){
            cout<<"No"<<endl;
            continue;
        }
        //位数拆解并存进堆栈
        while(N >= D){
            stack.push_back(N % D);
            N = N / D;
        }
        stack.push_back(N);
        N = 0;
        //这里代码其实有点问题，应该每个循环结束都将堆栈重新清空的
        //逆序累加得到reverse的数
        while(stack.size() != 0){
            N += stack.back() * (int)pow(D,count++);
            stack.pop_back();
        }
        //判断reverse后是不是质数
        if(Isprime(N) == true)
            cout<<"Yes"<<endl;
        else
            cout<<"No"<<endl;
        stack.clear();
    }
    //if(Isprime(19) == true) cout<<"1";
    return 0;
}

//根据素数定理判断一个数是不是质数
bool Isprime(int num){
    if(num == 2 || num == 3)
        return true;
    if(num == 1)
        return false;
    int temp = (int)sqrt(num);
    int *a = new int[temp];
    a[0] = 2;a[1] = 3;
    int total = 2;
    for(int i = 4;i <= temp;i++){
        for(int j = 0;j < total;j++){
            if(i % a[j] == 0)
                break;
            if(j == total-1){
                a[total++] = i;
                break;
            }
        }
    }
    for(int i = 0;i < total;i++){
        if(num % a[i] == 0)
            return false;
    }
    return true;
}
```
# 1017：Queueing at Bank
跟1014异曲同工，根据规则仿真模拟就完事了。
**~~（经过1014和1017，我彻底知道了审题的重要性，真的哭出来！！！）~~**
此题代码如下：
```cpp
#include<iostream>
#include <bits/stdc++.h>
#include<cstdio>
#include<cstdlib>
#include<string>
#include<queue>

using namespace std;
class Node{
public:
    int t;
    int pos_t;
    Node &operator=(const Node& b){
        if(this == &b)
            return *this;
        this->t = b.t;
        this->pos_t = b.pos_t;
        return *this;
    }
};

int N,K,tempN;
Node *a;
int *done,*now;
long int ttt = 0;

int totime(string s);
int findmin();
void Swap(Node &a, Node &b);
void CreateHeap(int index);
void DeleteHeap(int index);

int main(){
    cin >> N >> K;
    tempN = N;
    a = new Node[N + 1];
    done = new int[N];
    now = new int[K];
    string s;
    for(int i = 1;i <= N;i++){
        cin >> s >> a[i].pos_t;
        a[i].pos_t = 60 * a[i].pos_t;
        a[i].t = totime(s);
        CreateHeap(i);
    }
    for(int i = 0;i < K;i++){
        if(tempN == 0 && a[1].t > 32400){
            cout<<fixed<<setprecision(1)<<(float)ttt / (60 * N);
            return 0;
        }
        ttt += max(0,a[1].t) - a[1].t;
        now[i] = max(0,a[1].t) + min(3600,a[1].pos_t);
        //cout<<max(0,a[1].t) - a[1].t<<endl;
        DeleteHeap(1);
        tempN--;
    }
    int index;
    while(tempN != 0){
        index = findmin();
        if(a[1].t > 32400)
            break;
        ttt += max(now[index],a[1].t) - a[1].t;
        //cout<<index<<' '<<max(now[index],a[1].t) - a[1].t<<endl;
        now[index] = max(now[index],a[1].t) + min(3600,a[1].pos_t);
        DeleteHeap(1);
        tempN--;
    }
    cout<<fixed<<setprecision(1)<<(float)ttt / (60 * (N - tempN));
    return 0;
}

int totime(string s){
    int pos1 = s.find_first_of(":");
    int pos2 = s.find_last_of(":");
    int num = 0; 
    num += (stoi(s.substr(0,pos1)) - 8) * 3600;
    num += stoi(s.substr(pos1+1,pos2-pos1-1)) * 60;
    num += stoi(s.substr(pos2+1,s.size()-pos2-1));
    return num;
}


void Swap(Node &a, Node &b){
    Node temp1;
    temp1 = a;
    a = b;
    b = temp1;
}

void CreateHeap(int index){
    //建堆，插入新元素后调整成堆
    int temp = index;
    for(;temp != 1;temp = temp/2){
        if(a[temp].t < a[temp/2].t)
            Swap(a[temp],a[temp/2]);
        else
            break;
    }
}

//堆的删除，用来实现堆排序
void DeleteHeap(int index){
    int Ms = tempN;
    if(Ms == 0)
        return;
    else if(Ms == 1){
        return;
    }
    if(index == 1){
        a[1] = a[Ms];
    }
    //从两个子节点中找出比现在这个元素小的，交换位置并迭代函数
    //下面考虑了两种节点的多种情况（可能为空）
    if(2*index <= tempN && 2*index+1 <= tempN){
        if(a[2*index].t <= a[2*index+1].t && a[2*index].t < a[index].t){
            Swap(a[index],a[2*index]);
            DeleteHeap(2*index);
        }
        else if(a[2*index].t >= a[2*index+1].t && a[2*index+1].t < a[index].t){
            Swap(a[index],a[2*index+1]);
            DeleteHeap(2*index+1);
        }
        else
            return;
    }
    else if(2*index <= tempN && 2*index+1 > tempN){
        if(a[2*index].t < a[index].t){
            Swap(a[index],a[2*index]);
            DeleteHeap(2*index);
        }
        else
            return;
    }
    else
        return;
}

int findmin(){
    int minnum = -1;
    for(int i = 0;i < K;i++){
        if(minnum == -1){
            minnum = i;
            continue;
        }
        if(now[minnum] > now[i])
            minnum = i;
    }
    return minnum;
}
```
# 1018：Public Bike Management
此题代码如下：
```cpp
#include<iostream>
#include<vector>
#define num 9999

using namespace std;
int cmax,N,sp,M,min1,u,standard;
int **a;
int *c;
int *vis,*dis,*nex,*rescue,*rescuet;

void  Dij(int input);

int main(){
    cin >> cmax >> N >> sp >> M;
    vector<int> stack;
    c = new int[N];
    a = new int*[N + 1];
    vis = new int[N + 1];
    dis = new int[N + 1];
    nex = new int[N + 1];
    rescue = new int[N + 1];
    rescuet = new int[N + 1];
    for(int i = 0;i < N + 1;i++)
        a[i] = new int[N + 1];
    for(int i = 0;i < N + 1;i++){
        for(int j = i;j < N + 1;j++)
            a[i][j] = a[j][i] = num;
    }
    for(int i = 0;i < N;i++){
        cin >> c[i];
        rescue[i + 1] = c[i] - cmax / 2;
    }
    rescue[0] = 0;
    int index1,index2,road;
    for(int i = 0;i < M;i++){
        cin >> index1 >> index2 >> road;
        a[index1][index2] = a[index2][index1] = road;
    }
    standard = - rescue[sp];
    if(true){
        for (int j = 0; j < N + 1; j++)
            dis[j] = a[0][j];
        for (int j = 1; j < N + 1; j++){
            if(dis[j] != num){
                rescuet[j] = rescue[j];
                nex[j] = 0;
            }
            else
                nex[j] = -1;
        }
        rescuet[0] = rescue[0];
        nex[0] = 0;
        vis[0] = 1;
        Dij(0);
        int temp = sp;
        while(temp != 0){
            stack.push_back(temp);
            temp = nex[temp];
        }
        //for(int i = 0;i < N + 1;i++)
        //    cout<<' '<<rescuet[i];
        //cout<<endl;
        if(rescuet[sp] <= 0){
            cout<<-rescuet[sp];
            cout<<" 0";
            while(stack.size() != 0){
                cout<<"->"<<stack.back();
                stack.pop_back();
            }
            cout<<" 0";
            return 0;
        }
        else{
            cout<<'0';
            cout<<" 0";
            while(stack.size() != 0){
                cout<<"->"<<stack.back();
                stack.pop_back();
            }
            cout<<' '<<rescuet[sp];
            return 0;
        }
    }
    return 0;
}

void  Dij(int input){
    for (int i = 0; i < N; i++){
        min1 = num;
        // 寻找权值最小的点u
        for (int j = 0; j < N + 1; j++){
            if (vis[j] == 0 && dis[j] < min1){
                min1 = dis[j];
                u = j;
            }
        }
        vis[u] = 1;
        for (int v = 0; v < N + 1; v++){
            // 对于每个u可达的v来说
            if (a[u][v] < num){
                // 如果当前的dis[v]不满足三角形不等式，那么进行松弛操作
                if (dis[v] > dis[u] + a[u][v]){
                    dis[v] = dis[u] + a[u][v];
                    rescuet[v] = rescuet[u] + rescue[v];
                    nex[v] = u;
                }
                else if(dis[v] == dis[u] + a[u][v]){
                    if(rescuet[v] < standard && rescuet[u] + rescue[v] < standard && rescuet[v] < rescuet[u] + rescue[v]){
                        rescuet[v] = rescuet[u] + rescue[v];
                        nex[v] = u;
                    }
                    else if(rescuet[v] < standard && rescuet[u] + rescue[v] > standard){
                        rescuet[v] = rescuet[u] + rescue[v];
                        nex[v] = u;
                    }
                    else if(rescuet[v] > standard && rescuet[u] + rescue[v] > standard && rescuet[v] > rescuet[u] + rescue[v]){
                        rescuet[v] = rescuet[u] + rescue[v];
                        nex[v] = u;
                    }
                }
            }
        }
    }
}

```
# 1020：Tree Traversals
树的后序的最后一个节点一定是中间那个节点，然后对应去中序序列中将中序序列根据中间那个节点分成左右两个子序列，然后递归执行，就能得到最后的结果。
此题代码如下：
```cpp
#include<iostream>
#include<queue>

using namespace std;
class Node{
public:
    int leftnode;
    int rightnode;
    Node(){leftnode = rightnode = -1;}
};

int N,pos,maxnum;
int *a,*b;
queue<int> q;
Node *c;

void divide(int starta,int enda,int startb,int endb,Node&n);

int main(){
    cin >> N;
    a = new int[N];
    b = new int[N];
    for(int i = 0;i < N;i++)
        cin >> a[i];
    maxnum = a[0];
    for(int i = 0;i < N;i++){
        cin >> b[i];
        if(b[i] > maxnum)
            maxnum = b[i];
    }
    c = new Node[maxnum+1];
    divide(0,N-1,0,N-1,c[a[N-1]]);
    //for(int i = 0;i <= maxnum;i++)
    //    cout<<c[i].leftnode<<' '<<c[i].rightnode<<endl;
    q.push(a[N-1]);
    int count = 0;
    while(q.size() != 0 && count < N){
        if(count != 0)
            cout<<' ';
        count++;
        cout<<q.front();
        if(c[q.front()].leftnode != -1)
            q.push(c[q.front()].leftnode);
        if(c[q.front()].rightnode != -1)
            q.push(c[q.front()].rightnode);
        q.pop();
    }
    return 0;
}

void divide(int starta,int enda,int startb,int endb,Node&n){
    if(starta == enda)
        return;
    for(int i = startb;i <= endb;i++){
        if(b[i] == a[enda]){
            pos = i;
            break;
        }
    }
    if(pos == endb){
        n.leftnode = a[enda-1];
        divide(starta,enda-1,startb,endb-1,c[a[enda-1]]);
        return;
    }
    if(pos == startb){
        n.rightnode = a[starta+1];
        divide(starta,enda-1,startb+1,endb,c[a[starta+1]]);
        return;
    }
    else{
        int flag = 0;
        for(int i = starta;i <= enda;i++){
            for(int  j = startb;j < pos;j++){
                if(a[i] == b[j])
                    break;
                if(j == pos - 1){
                    n.leftnode = a[i-1];
                    n.rightnode = a[enda-1];
                    //cout<<n.leftnode<<' '<<n.rightnode<<endl;
                    divide(starta,i-1,startb,pos-1,c[a[i-1]]);
                    divide(i,enda-1,pos+1,endb,c[a[enda-1]]);
                    flag = 1;
                }
            }
            if(flag == 1)
                break;
        }
    }
}
```
# 阶段性总结
