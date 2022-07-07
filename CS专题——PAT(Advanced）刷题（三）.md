这是该系列的第二期，以后每期都会更新10题目，且会一直保持更新（有一些暂时空缺的题目日后会补起来）。然后说明一下，有一些题目因为实在太简单了，就没有写思路或感想，直接分享代码了，所以大家懂得都懂，相信这种题目也难不住大家的。还有因为Advanced中涉及到比较多的数据结构和算法的内容，但是我本人还在学习算法与数据结构，所以可能接下来一段时间会暂缓更新，以数据结构和算法的学习为主。
# 1021：Deepest Root
此题我还没有AC，主要是有一个Checkpoint显示内存超限，正在想办法优化空间存储。这道题的主要思路是迪杰斯特拉最短路算法+用一个并查集来记录图中的连通分量。
此题代码如下：
```cpp
#include<iostream>
#include<queue>

using namespace std;

int **a;
int N,maxlen;
int *vis,*dis,*b;
int min1 = 99999999;
int u = 0;
int flag = 0;
queue<int> q;

void dijkstra(int input);
int Checkmax();
int Findparent(int index);
void Union(int index1, int index2);
bool Check(int index1, int index2);

int main(){
    maxlen = 0;
    cin >> N;
    a = new int*[N];
    vis = new int[N];
    dis = new int[N];
    b = new int[N];
    for(int i = 0;i < N;i++){
        a[i] = new int[N];
        b[i] = -1;
    }
    for (int i = 0; i < N; i++){
        for (int j = 0; j < N; j++){
            if (i == j)
                a[i][j] = 0;
            else
                a[i][j] = 99999999;
        }
    }
    int index1,index2;
    for(int i = 0;i < N-1;i++){
        cin >> index1 >> index2;
        a[index1-1][index2-1] = a[index2-1][index1-1] = 1;
        if(Check(index1-1,index2-1) == false)
            Union(index1-1,index2-1);
        else if(Check(index1-1,index2-1) == true && flag == 0)
            flag = 1;
    }
    if(flag == 1){
        int count = 0;
        for(int i = 0; i < N;i++){
            if(b[i] < 0)
                count++;
        }
        cout<<"Error: "<<count<<" components";
        return 0;
    }
    for(int i = 0;i < N;i++){
        for(int j = 0;j < N;j++){
            vis[j] = 0;
            dis[j] = a[i][j];
        }
        vis[i] = 1;
        dijkstra(i);
        if(i == 0){
            maxlen = Checkmax();
            q.push(i);
            continue;
        }
        else if(Checkmax() > maxlen){
            maxlen = Checkmax();
            q = queue<int>();
            q.push(i);
        }
        else if(Checkmax() == maxlen)
            q.push(i);
    }
    while(q.size() != 0){
        cout<<(q.front()+1)<<endl;
        q.pop();
    }
    return 0;
}

void dijkstra(int input){
    for (int i = 0; i < N - 1; i++){
        min1 = 99999999;
        // 寻找权值最小的点u
        for (int j = 0; j < N; j++){
            if (vis[j] == 0 && dis[j] < min1){
                min1 = dis[j];
                u = j;
            }
        }
        vis[u] = 1;
        for (int v = 0; v < N; v++){
            // 对于每个u可达的v来说
            if (a[u][v] < 99999999){
                // 如果当前的dis[v]不满足三角形不等式，那么进行松弛操作
                if (dis[v] > dis[u] + a[u][v]){
                    dis[v] = dis[u] + a[u][v];
                }
            }
        }
    }
}

int Checkmax(){
    int temp = -1;
    for(int i = 0;i < N;i++){
        if(temp == -1)
            temp = dis[i];
        else if(dis[i] > temp)
            temp = dis[i];
    }
    return temp;
}

//找父亲节点
int Findparent(int index){
    int temp = index;
    while(b[temp] >= 0){
        temp = b[temp];
    }
    //路径压缩
    if(index != temp)
        b[index] = temp;
    return temp;
}

//并，需要用到路径压缩和根据集合大小union
void Union(int index1, int index2){
    int root1 = Findparent(index1);
    int root2 = Findparent(index2);
    //小的集合并到大的集合中
    if(b[root1] >= b[root2]){
        int temp = b[root1];
        b[root1] = root2;
        b[root2] += temp;
    }
    else{
        int temp = b[root2];
        b[root2] = root1;
        b[root1] += temp;
    }
}

//查，查找两个节点
bool Check(int index1, int index2){
    if(Findparent(index1) == Findparent(index2))
        return true;
    else
        return false;
}
```
# 1022：Digital Library
此题代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
class Node2{
    public:
    long int ID;
    string n;
    string author;
    string kw[5];
    int kwn;
    string pub;
    string y;
    Node2(){kwn = 0;}
    Node2 &operator=(const Node2& input){
        if(this == &input)
            return *this;
        this->ID = input.ID;
        this->n = input.n;
        this->author = input.author;
        for(int i = 0;i < input.kwn;i++)
            this->kw[i] = input.kw[i];
        this->kwn = input.kwn;
        this->pub = input.pub;
        this->y = input.y;
        return *this;
    }
};

class Node{
    public:
    Node2 value;
    Node* next;
    Node(){ next = NULL; }
    Node2 Getvalue() { return this->value; }
    Node* Getnext() { return this->next; }
    void Setvalue(Node2 num) { value = num; }
    void Setnext(Node* a) { next = a; }
};

class Nodearray{
    public:
    Node* head;
    Node* tail;
    int total;
    Nodearray(){
        head = NULL;
        tail = NULL;
        total = 0;
    }
    void Addnum(Node2 num){
        total++;
        if (head == NULL){
            head = new Node;
            head->Setvalue(num);
            head->Setnext(NULL);
            tail = head;
        }
        else{
            Node* p = this->head;
            Node* tempp = p;
            while(p != NULL){
                if(num.ID > (p->Getvalue()).ID){
                    tempp = p;
                    p = p->Getnext();
                }
                else{
                    Node* temp;
                    temp = new Node;
                    temp->Setvalue(num);
                    if(p == this->head){
                        temp->Setnext(p);
                        this->head = temp;
                        return;
                    }else{
                        temp->Setnext(p);
                        tempp->Setnext(temp);
                        return;
                    }
                }
            }
            tempp->Setnext(new Node);
            tempp = tempp->Getnext();
            tempp->Setvalue(num);
            tempp->Setnext(NULL);
            return;
        }
    }
};

int N,M;
char c;
Nodearray a;

int main(){
    cin >> N;
    Node2 temp;
    string s;
    for(int n = 0;n < N;n++){
        temp.kwn = 0;
        cin >> temp.ID;
        c = getchar();
        getline(cin,temp.n);
        getline(cin,temp.author);
        getline(cin,s);
        while(s.find_first_of(" ") < s.size()){
            temp.kw[temp.kwn++] = s.substr(0,s.find_first_of(" "));
            s = s.substr(s.find_first_of(" ")+1,s.size() - (s.find_first_of(" ")+1) );
        }
        temp.kw[temp.kwn++] = s;
        getline(cin,temp.pub);
        cin >> temp.y;
        a.Addnum(temp);
    }
    cin >> M;
    string s2,s3;
    int flag;
    for(int m = 0;m < M;m++){
        flag = 0;
        cin >> s2;
        c = getchar();
        getline(cin,s3);
        cout<<s2<<' '<<s3<<endl;
        Node *p = a.head;
        while(p != NULL){
            if(s2[0] == '1'){
                if(p->Getvalue().n == s3){
                    cout<<p->Getvalue().ID<<endl;
                    if(flag == 0)
                        flag = 1;
                }
            }else if(s2[0] == '2'){
                if(p->Getvalue().author == s3){
                    cout<<p->Getvalue().ID<<endl;
                    if(flag == 0)
                        flag = 1;
                }
            }else if(s2[0] == '3'){
                for(int i = 0;i < p->Getvalue().kwn;i++){
                    //cout<<p->Getvalue().kw[i]<<' '<<s3<<endl;
                    if(p->Getvalue().kw[i] == s3){
                        cout<<p->Getvalue().ID<<endl;
                        if(flag == 0)
                            flag = 1;
                        break;
                    }
                }
            }else if(s2[0] == '4'){
                if(p->Getvalue().pub == s3){
                    cout<<p->Getvalue().ID<<endl;
                    if(flag == 0)
                        flag = 1;
                }
            }else if(s2[0] == '5'){
                if(p->Getvalue().y == s3){
                    cout<<p->Getvalue().ID<<endl;
                    if(flag == 0)
                        flag = 1;
                }
            }
            p = p->Getnext();
        }
        if(flag == 0)
            cout<<"Not Found"<<endl;
    }
    return 0;
}
```
# 1023：Have Fun with Numbers
此题代码如下：
```cpp
#include<iostream>
#include<string>
#define Maxsize 20

using namespace std;
string s;
int a[Maxsize] = {0};

int main(){
    cin >> s;
    for(int i = 0;i < s.size();i++)
        a[(int)s[i] - 48] ++;
    int addnum = 0;
    int tempnum;
    for(int i = s.size() - 1;i > -1;i--){
        tempnum = ((int)s[i] - 48) * 2 + addnum;
        addnum = tempnum / 10;
        s[i] = tempnum % 10 + '0';
    }
    if(addnum == 1)
        s = "1" + s;
    for(int i = 0;i < s.size();i++){
        if(a[(int)s[i] - 48] < 0){
            cout<<"No"<<endl;
            cout<<s;
            return 0;
        }
        a[(int)s[i] - 48] --;
    }
    for(int i = 0;i < Maxsize;i++){
        if(a[(int)s[i] - 48] != 0){
            cout<<"No"<<endl;
            cout<<s;
            return 0;
        }
    }
    cout<<"Yes"<<endl;
    cout<<s;
    return 0;
}
```
# 1024：Palindromic Number
字符串类问题如果显示“运行时错误”的话，那一定是字符串没法用long int表示。
此题代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
string temp,temp2;
int K;

string Add(string s1,string s2);

int main(){
    cin >> temp >> K;
    if(K == 0 || temp.size() == 1){
        cout<<temp<<endl;
        cout<<'0';
        return 0;
    }
    for(int i = 0;i < temp.size()/2;i++){
        if(temp[i] != temp[temp.size()-i-1])
            break;
        if(i == (temp.size()/2 - 1)){
            cout<<temp<<endl;
            cout<<'0';
            return 0;
        }
    }
    temp2 = "";
    int flag = 0;
    for(int i = 0;i < K;i++){
        for(int j = 0;j < temp.size();j++)
            temp2 += temp[temp.size()-j-1];
        temp = Add(temp,temp2);
        //cout<<temp<<endl;
        for(int j = 0;j < temp.size()/2;j++){
            if(temp[j] != temp[temp.size()-j-1])
                break;
            if(j == (temp.size()/2-1)){
                cout<<temp<<endl;
                cout<<(i+1);
                flag = 1;
                return 0;
            }
        }
        temp2 = "";
        if(i == (K -1)){
            cout<<temp<<endl;
            cout<<K;
        }
    }
    return 0;
}

string Add(string s1,string s2){
    int addnum = 0;
    int i = 0;
    int tempnum;
    while(i != s1.size() && i != s2.size()){
        tempnum = (int)s1[s1.size()-i-1] + (int)s2[s2.size()-i-1] + addnum - 96;
        s1[s1.size()-i-1] = tempnum % 10 + '0';
        addnum = tempnum / 10;
        i++;
    }
    if(addnum != 0)
        s1 = "1" + s1;
    return s1;
}

```
# 1025：PAT Ranking
这道题我最后用到了**希尔排序**和**归并排序（两个有序子列的归并）**两种排序方法。
此题代码如下：
```cpp
#include<iostream>
#include<string>
#include<cmath>
#define Maxsize2 30000
#define Maxsize 300

using namespace std;

class Node{
public:
    string r;
    int g;
    int fr;
    int l;
    int lr;
    Node(){
        r = " ";
    }
};

int N,K,t,D;
int num = 0;
Node *total,*temp,*temptotal,*H1,*H2;
string t1;

void HSort(Node *H,int n);
void Merge(int start,int leftend,Node *a,Node *b);

int main(){
    cin >> N;
    total = new Node[Maxsize2];
    temptotal = new Node[Maxsize2];
    temp = new Node[Maxsize];
    for(int i = 0;i < N;i++){
        cin >> K;
        for(int j = 0;j < K;j++)
            cin >> temp[j].r >> temp[j].g;
        HSort(temp,K);
        int tempnum;
        for(int j = 0;j < K;j++){
            temp[j].l = i + 1;
            if(j != 0 && temp[j].g == temp[j-1].g)
                temp[j].lr = tempnum;
            else{
                tempnum = j + 1;
                temp[j].lr = j + 1;
            }
        }
        if(i % 2 == 0){
            H1 = total;
            H2 = temptotal;
        }
        else{
            H2 = total;
            H1 = temptotal;
        }
        for(int j = 0;j < K;j++){
                H1[j + num].r = temp[j].r;
                H1[j + num].g = temp[j].g;
                H1[j + num].l = temp[j].l;
                H1[j + num].lr = temp[j].lr;
        }
        if(i != 0)
            Merge(0,num-1,H1,H2);
        else{
            for(int j = 0;j < K;j++){
                temptotal[j].r = total[j].r;
                temptotal[j].g = total[j].g;
                temptotal[j].l = total[j].l;
                temptotal[j].lr = total[j].lr;
            }
        }
        num += K;
    }
    if(N % 2 != 0){
        for(int j = 0;j < num;j++){
            total[j].r = temptotal[j].r;
            total[j].g = temptotal[j].g;
            total[j].l = temptotal[j].l;
            total[j].lr = temptotal[j].lr;
        }
    }
    int tempnum;
    for(int j = 0;j < num;j++){
        if(j != 0 && total[j].g == total[j-1].g)
            total[j].fr = tempnum;
        else{
            tempnum = j + 1;
            total[j].fr = j + 1;
        }
    }
    cout<<num<<endl;
    for(int i = 0;i < num;i++)
        cout<<total[i].r<<' '<<total[i].fr<<' '<<total[i].l<<' '<<total[i].lr<<endl;
    return 0;
}

void HSort(Node *H,int n){
    int count = 1;
    while((int)pow(2,count) - 1 < n)
        count++;
    count--;
    for(int i = count;i > 0;i--){
        D = (int)pow(2,i) - 1;
        for(int j = 0;j < D;j++){
            for(int k = j;k < n;k += D){
                if(k == j)
                    continue;
                t1 = H[k].r;
                t = H[k].g;
                for(int p = j;p < k;p += D){
                    if((t > H[p].g)||(t == H[p].g && t1 < H[p].r)){
                        for(int s = k;s > p;s -= D){
                            H[s].r = H[s-D].r;
                            H[s].g = H[s-D].g;
                        }
                        H[p].r = t1;
                        H[p].g = t;
                        break;
                    }
                }
            }
        }
    }
}

void Merge(int start,int leftend,Node *a,Node *b){
    int l = start;
    int r = leftend + 1;
    int end = leftend + K;
    while(l <= leftend && r <= end){
        if(a[l].g > a[r].g || (a[l].g == a[r].g && a[l].r < a[r].r)){
            b[start].r = a[l].r;
            b[start].g = a[l].g;
            b[start].l = a[l].l;
            b[start].lr = a[l].lr;
            l++;
        }
        else{
            b[start].r = a[r].r;
            b[start].g = a[r].g;
            b[start].l = a[r].l;
            b[start].lr = a[r].lr;
            r++;
        }
        start++;
    }
    while(l <= leftend){
        b[start].r = a[l].r;
        b[start].g = a[l].g;
        b[start].l = a[l].l;
        b[start].lr = a[l].lr;
        start++;l++;
    }
    while(r <= end){
        b[start].r = a[r].r;
        b[start].g = a[r].g;
        b[start].l = a[r].l;
        b[start].lr = a[r].lr;
        start++;r++;
    }
}
```
# 1026：Table Tennis
这题其实后来我知道我自己思路错了，下面是我后来想的思路：

   - 从所有桌中找一个最快服务完的桌，看看这个桌是不是VIP桌
   - 如果是VIP桌，则看看有没有已经到了的VIP用户
   - 看下有没有已经到了的VIP用户，有就服务他，没有就从队列里拉个最快到的用户
   - 然后接着从所有桌中找一个最快服务完的桌
   - 如果这个桌是VIP桌，看下有没有已经到了的VIP用户，有就服务他，没有就从剩下用户中拉个最快到的
   - 一直循环
   - 知道最后所有桌的服务完的时间已经超过了21：00，这时候看看最后一批服务完的用户有没有21：00之前到的，有就放到队列里

此题代码如下：
```cpp
#include<iostream>
#include<string>
#include<cmath>

using namespace std;
class Node{
public:
    long int arr;
    int pla;
    long int lea;
    int tag;
    int ind;
    Node &operator=(const Node& aa){
        if(this == &aa)
            return *this;
        this->arr = aa.arr;
        this->pla = aa.pla;
        this->lea = aa.lea;
        this->tag = aa.tag;
        this->ind = aa.ind;
        return *this;
    }
};

int N,K,M,tempN;
Node *a,*b;
long int *now;
Node *nowpla;
int *serve,*spe2;
int flag;

long int totime(string s);
int r(long int a,long int b);
string tostr(long int num);
void Swap(Node &a, Node &b);
int findmin();
int findmin2();
long int maxnum(long int a,long int b);
void HSort(Node *H,int n);

int main(){
    cin >> N;
    a = new Node[N];
    b = new Node[N];
    string s;
    for(int i = 0;i < N;i++){
        cin >> s >> a[i].pla >> a[i].tag;
        if(a[i].pla > 120)
            a[i].pla = 120;
        a[i].arr = totime(s);
    }
    HSort(a,N);
    int index;
    cin >> K >> M;
    tempN = N;
    serve = new int[K];
    spe2 = new int[K];
    now = new long int[K];
    nowpla = new Node[K];
    for(int i = 0;i < M;i++){
        cin >> index;
        spe2[index-1] = 1;
    }
    for(int i = 0;i < K;i++)
        nowpla[i].tag = -1;
    int flag;
    while(tempN != 0){
        flag = 0;
        index = findmin();
        if(now[index] > 46800)
            break;
        if(spe2[index] == 1){
            for(int i = N-tempN;i < N;i++){
                if(a[i].arr > now[index])
                    break;
                else if(a[i].arr <= now[index] && a[i].tag == 1){
                    Node temp = a[i];
                    for(int j = i;j > N- tempN;j--)
                        a[j] = a[j-1];
                    a[N-tempN] = temp;
                    flag = 1;
                    break;
                }
            }
        }
        if(flag == 0)
            index = findmin2();
        if(a[N-tempN].tag == 1){
            for(int i = 0;i < K;i++){
                if(spe2[i] == 1 && now[i] <= a[N-tempN].arr){
                    index = i;
                    break;
                }
            }
        }
        //cout<<flag<<' '<<tostr(a[N-tempN].arr)<<' '<<N-tempN<<' '<<index<<endl;
        a[N-tempN].ind = N - tempN;
        a[N-tempN].lea = maxnum(now[index],a[N-tempN].arr);
        now[index] = maxnum(now[index],a[N-tempN].arr) + a[N-tempN].pla * 60;
        if(nowpla[index].tag != -1){
            b[nowpla[index].ind] = nowpla[index];
            serve[index] ++;
        }
        nowpla[index] = a[N-tempN];
        tempN--;
    }
    for(int i = 0;i < K;i++){
        if(now[i] - nowpla[i].pla * 60 <= 46800 && now[i] > 0){
            b[nowpla[i].ind] = nowpla[i];
            serve[i] ++;
        }
    }
    for(int i = 0;i < N - tempN;i++){
        if(i != 0 && b[i].arr == 0)
            break;
        cout<<tostr(b[i].arr)<<' '<<tostr(b[i].lea)<<' '<<r(b[i].lea,b[i].arr)<<endl;
    }
    cout<<serve[0];
    for(int i = 1;i < K;i++)
        cout<<' '<<serve[i];
    return 0;
}

long int maxnum(long int a,long int b){
    if(a >= b)
        return a;
    else
        return b;
}

long int totime(string s){
    int pos1 = s.find_first_of(":");
    int pos2 = s.find_last_of(":");
    long int num = 0; 
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

int findmin2(){
    int mini = -1;
    for(int i = 0;i < K;i++){
        if(now[i] <= a[N-tempN].arr)
            return i;
        else if(mini == -1)
            mini = i;
        else if(a[i].arr < a[mini].arr)
            mini = i;
    }
    return mini;
}

string tostr(long int num){
    string t;
    int hour = num / 3600 + 8;
    num = num % 3600;
    int minute = num / 60;
    num = num % 60;
    string h = to_string(hour);
    string m = to_string(minute);
    string se = to_string(num);
    if(h.size() == 1)
        h = '0' + h;
    if(m.size() == 1)
        m = '0' + m;
    if(se.size() == 1)
        se = '0' + se;
    t = h + ':' + m + ':' + se;
    return t;
}

int r(long int a,long int b){
    int temp = (a - b) / 60;
    a = (a - b) % 60;
    if(a >= 30)
        temp += 1;
    return temp;
}

void HSort(Node *H,int n){
    int count = 1;
    while((int)pow(2,count) - 1 < n)
        count++;
    count--;
    for(int i = count;i > 0;i--){
        int D = (int)pow(2,i) - 1;
        for(int j = 0;j < D;j++){
            for(int k = j;k < n;k += D){
                if(k == j)
                    continue;
                Node temp = H[k];
                for(int p = j;p < k;p += D){
                    if(temp.arr < H[p].arr){
                        for(int s = k;s > p;s -= D)
                            H[s] = H[s-D];
                        H[p] = temp;
                        break;
                    }
                }
            }
        }
    }
}
```
# 1027：Colors in Mars
此题代码如下：
```cpp
#include<iostream>
#include<string>
#define radix 13

using namespace std;
int n;

string Transform(int n);

int main(){
    cout<<'#';
    for(int i = 0;i < 3;i++){
        cin >> n;
        cout<<Transform(n);
    }
    return 0;
}

string Transform(int n){
    string temp = "";
    int tempnum;
    while(n > radix){
        tempnum = n / radix;
        if(tempnum < 10)
            temp += (tempnum + '0');
        else if(tempnum == 10)
            temp += 'A';
        else if(tempnum == 11)
            temp += 'B';
        else if(tempnum == 12)
            temp += 'C';
        n = n % radix;
    }
    if(n < 10)
        temp += (n + '0');
    else if(n == 10)
        temp += 'A';
    else if(n == 11)
        temp += 'B';
    else if(n == 12)
        temp += 'C';
    if(temp.size() == 1)
        temp = "0" + temp;
    return temp;
}

```
# 1028：List Sorting
此题代码如下：
```cpp
#include<iostream>
#include<string>
#include<cmath>

using namespace std;

class Node{
public:
    string ID;
    string name;
    int g;
};

int N,C,D;
string t1,t2;
int t3;
Node *a;

void HSort1(Node *H,int n);
void HSort2(Node *H,int n);
void HSort3(Node *H,int n);

int main(){
    cin >> N >> C;
    a = new Node[N];
    for(int i = 0;i < N;i++)
        cin >> a[i].ID >> a[i].name >> a[i].g;
    if(C == 1)
        HSort1(a,N);
    else if(C == 2)
        HSort2(a,N);
    else
        HSort3(a,N);
    for(int i = 0;i < N;i++)
        cout<<a[i].ID<<' '<<a[i].name<<' '<<a[i].g<<endl;
    return 0;
}

void HSort1(Node *H,int n){
    int count = 1;
    while((int)pow(2,count) - 1 < n)
        count++;
    count--;
    for(int i = count;i > 0;i--){
        D = (int)pow(2,i) - 1;
        for(int j = 0;j < D;j++){
            for(int k = j;k < n;k += D){
                if(k == j)
                    continue;
                t1 = H[k].ID;
                t2 = H[k].name;
                t3 = H[k].g;
                for(int p = j;p < k;p += D){
                    if(t1 < H[p].ID){
                        for(int s = k;s > p;s -= D){
                            H[s].ID = H[s-D].ID;
                            H[s].name = H[s-D].name;
                            H[s].g = H[s-D].g;
                        }
                        H[p].ID = t1;
                        H[p].name = t2;
                        H[p].g = t3;
                        break;
                    }
                }
            }
        }
    }
}

void HSort2(Node *H,int n){
    int count = 1;
    while((int)pow(2,count) - 1 < n)
        count++;
    count--;
    for(int i = count;i > 0;i--){
        D = (int)pow(2,i) - 1;
        for(int j = 0;j < D;j++){
            for(int k = j;k < n;k += D){
                if(k == j)
                    continue;
                t1 = H[k].ID;
                t2 = H[k].name;
                t3 = H[k].g;
                for(int p = j;p < k;p += D){
                    if(t2.compare(H[p].name) < 0 || (t2.compare(H[p].name) == 0 && t1 < H[p].ID)){
                        for(int s = k;s > p;s -= D){
                            H[s].ID = H[s-D].ID;
                            H[s].name = H[s-D].name;
                            H[s].g = H[s-D].g;
                        }
                        H[p].ID = t1;
                        H[p].name = t2;
                        H[p].g = t3;
                        break;
                    }
                }
            }
        }
    }
}

void HSort3(Node *H,int n){
    int count = 1;
    while((int)pow(2,count) - 1 < n)
        count++;
    count--;
    for(int i = count;i > 0;i--){
        D = (int)pow(2,i) - 1;
        for(int j = 0;j < D;j++){
            for(int k = j;k < n;k += D){
                if(k == j)
                    continue;
                t1 = H[k].ID;
                t2 = H[k].name;
                t3 = H[k].g;
                for(int p = j;p < k;p += D){
                    if(t3 < H[p].g || (t3 == H[p].g && t1 < H[p].ID)){
                        for(int s = k;s > p;s -= D){
                            H[s].ID = H[s-D].ID;
                            H[s].name = H[s-D].name;
                            H[s].g = H[s-D].g;
                        }
                        H[p].ID = t1;
                        H[p].name = t2;
                        H[p].g = t3;
                        break;
                    }
                }
            }
        }
    }
}
```
# 1029：Median
此题代码如下：
```cpp
#include<iostream>

using namespace std;
int N,Na,Nb;
long int *a,*b;

int main(){
    cin >> N;
    Na = N;
    a = new long int[N];
    for(int i = 0;i < N;i++)
        cin >> a[i];
    cin >> N;
    Nb = N;
    b = new long int[N];
    for(int i = 0;i < N;i++)
        cin >> b[i];
    int ia = 0;
    int ib = 0;
    int count = 0;
    if((Na + Nb) % 2 == 1){
        while(true){
            if(a[ia] < b[ib]){
                ia++;
                count++;
                if(count == (Na + Nb) / 2 + 1){
                    cout<<a[ia-1];
                    return 0;
                }
            }else if(a[ia] > b[ib]){
                ib++;
                count++;
                if(count == (Na + Nb) / 2 + 1){
                    cout<<b[ib-1];
                    return 0;
                }
            }
            else{
                if(count == (Na + Nb) / 2 || count == (Na + Nb) / 2 - 1){
                    cout<<a[ia];
                    return 0;
                }
                count += 2;
                ia++;
                ib++;
            }
        }
    }
    else{
        int num1,num2;
        while(true){
            if(a[ia] < b[ib]){
                ia++;
                count++;
                if(count == (Na + Nb) / 2)
                    num1 = a[ia-1];
                else if(count == (Na + Nb) / 2 + 1){
                    num2 = a[ia-1];
                    cout<<(float)(num1 + num2) / 2;
                    return 0;
                }
            }else if(a[ia] > b[ib]){
                ib++;
                count++;
                if(count == (Na + Nb) / 2)
                    num2 = b[ib-1];
                else if(count == (Na + Nb) / 2 + 1){
                    num2 = b[ib-1];
                    cout<<(float)(num1 + num2) / 2;
                    return 0;
                }
            }
            else{
                if(count == (Na + Nb) / 2 - 1){
                    cout<<a[ia];
                    return 0;
                }else if(count == (Na + Nb) / 2){
                    num2 = a[ia];
                    cout<<(float)(num1 + num2) / 2;
                    return 0;
                }else if(count == (Na + Nb) / 2 - 2){
                    num1 = a[ia];
                }
                count += 2;
                ia++;
                ib++;
            }
        }
    }
    return 0;
}
```
# 1030：Travel Plan
此题代码如下：
```cpp
#include<iostream>
#include<vector>
#include<map>
#include<cstdio>
#include<algorithm>
#include<queue>
#define inf 10000000
using namespace std;
struct node
{
    int dis;
    int cost=inf;
};
node a[505][505];
int minn[505];
int path[505];
int dist[505];
int mark[505];
int main()
{
    int n,m,start,endd;
    cin>>n>>m>>start>>endd;
    for(int i=0;i<n;i++)
    {
        for(int j=0;j<n;j++)
        {
            if(i==j) a[i][j].dis=0;
            else
                a[i][j].dis=inf;
        }
    }
    for(int i=0;i<m;i++)
    {
        int b,c,d,e;
        cin>>b>>c>>d>>e;
        a[b][c].dis=a[c][b].dis=d;
        a[b][c].cost=a[c][b].cost=e;
    }
    for(int i=0;i<n;i++)
    {
        dist[i]=a[start][i].dis;
        if(dist[i]!=inf&&i!=start) path[i]=start;
        else
            path[i]=-1;
        minn[i]=a[start][i].cost;
    }
    mark[start]=1;
    for(int i=0;i<n-1;i++)
    {
        int xiao=inf;
        int biaoji;
        for(int j=0;j<n;j++)
        {
            if(mark[j]==0&&dist[j]<xiao)
            {
                xiao=dist[j];
                biaoji=j;
            }
        }
        mark[biaoji]=1;
        for(int j=0;j<n;j++)
        {
            if(mark[j]==0)
            {
                if(dist[biaoji]+a[biaoji][j].dis<dist[j])
                {
                    dist[j]=dist[biaoji]+a[biaoji][j].dis;
                    minn[j]=minn[biaoji]+a[biaoji][j].cost;
                    path[j]=biaoji;
                }
                else if(dist[biaoji]+a[biaoji][j].dis==dist[j])
                {
                    if(minn[biaoji]+a[biaoji][j].cost<minn[j])
                    {
                        minn[j]=minn[biaoji]+a[biaoji][j].cost;
                        path[j]=biaoji;
                    }
                }
            }
 
        }
    }
    int lu[505];
    lu[0]=endd;
    int num=1;
    int tmp=endd;
    while(path[tmp]!=start)
    {
        lu[num++]=path[tmp];
        tmp=path[tmp];
    }
    cout<<start;
    for(int i=num-1;i>=0;i--)
    {
        cout<<" "<<lu[i];
    }
    cout<<" "<<dist[endd]<<" "<<minn[endd];
 
}
```
# 阶段性总结
