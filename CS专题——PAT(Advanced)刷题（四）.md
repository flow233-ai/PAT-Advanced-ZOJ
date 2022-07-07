# 1031：Hello World for U
此题代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
string s;
int n1,n2,n3;

int main(){
    cin >> s;
    n1 = n3 = (s.size() + 2) / 3;
    n2 = s.size() - n1 - n3;
    for(int i = 0;i < n1;i++){
        cout<<s[i];
        if(i != n1 - 1){
            for(int j = 0;j < n2;j++)
                cout<<' ';
        }
        else{
            cout<<s.substr(i+1,n2);
        }
        cout<<s[s.size()-i-1]<<endl;
    }
    return 0;
}
```
# 1032：Sharing
**这题的思路就是“用空间换时间”，也就是记忆化搜索的思路，跟动态规划有异曲同工之妙**，额外用一个数组来操作两个节点已经走过的路径，且两个节点要同时走（你走一步，我走一步），这样子能减少搜素的时间。
此题代码如下：
```cpp
#include<iostream>
#include<string>
#define Maxsize 100000

using namespace std;
class Node{
    public:
    char data;
    string next;
};

string ad1,ad2;
long int N;
Node *a;
int *b;

int main(){
    cin >> ad1 >> ad2 >> N;
    a = new Node[Maxsize];
    b = new int[Maxsize];
    int tad1;
    for(int i = 0;i < N;i++){
        cin >> tad1;
        cin >> a[tad1].data >> a[tad1].next;
    }
    long int temp1,temp2;
    while(ad1 != "-1" && ad2 != "-1"){
        temp1 = stoi(ad1);temp2 = stoi(ad2);
        if(temp2 == temp1){
            cout<<ad1;
            return 0;
        }
        if(b[temp2] == 1){
            cout<<ad2;
            return 0;
        }
        if(b[temp1] == -1){
            cout<<ad1;
            return 0;
        }
        b[temp2]  = -1;
        b[temp1] = 1;
        ad1 = a[temp1].next;
        ad2 = a[temp2].next;
    }
    while(ad1 != "-1"){
        temp1 = stoi(ad1);
        if(b[temp1] == -1){
            cout<<ad1;
            return 0;
        }
        ad1 = a[temp1].next;
    }
    while(ad2 != "-1"){
        temp2 = stoi(ad2);
        if(b[temp2] == -1){
            cout<<ad2;
            return 0;
        }
        ad2 = a[temp2].next;
    }
    cout<<"-1";
    return 0;
}
```
# 1033：To Fill or Not to Fill
此题代码如下：
```cpp
#include<iostream>
#include<cmath>
#include <bits/stdc++.h>

using namespace std;
class Node{
    public:
    float p;
    int d;
};

class Node2{
    public:
    float p;
    int d;
    int ind;
};

int Cmax,D,N;
int Cnow = 0;
float Da;
Node *a;
Node2 *b;
float *fee,*extra;
void HSort(Node *H,int n);
void HSort2(Node2 *H,int n);

int main(){
    cin >> Cmax >> D >> Da >> N;
    a = new Node[N];
    b = new Node2[N];
    fee = new float[N];
    extra = new float[N];
    for(int i = 0;i < N;i++){
        cin >> a[i].p >> a[i].d;
        extra[i] = Cmax;
    }
    HSort(a,N);
    int tempd;
    float tempfee;
    for(int i = 0;i < N;i++){
        if(i == 0){
            if(a[i].d == 0){
                fee[0] = 0;
                b[0].d = a[0].d;
                b[0].p = a[0].p;
                b[0].ind = 0;
                continue;
            }
            else{
                cout<<"The maximum travel distance = 0.00";
                return 0;
            }
        }
        tempd = a[i].d - a[i-1].d;
        if((Cmax - Cnow) * Da < tempd){
            cout<<"The maximum travel distance = "<<fixed<<setprecision(2)<<a[i-1].d + (Cmax - Cnow) * Da;
            return 0;
        }
        tempfee = 0;
        int j = 0;
        int original = 0;
        int count = 0;
        while(tempd > 0 && count != 10){
            if(j == i){
                cout<<"The maximum travel distance = "<<fixed<<setprecision(2)<<a[i].d - tempd;
                return 0;
            }
            if(a[i-1].d - a[j].d >= Cmax * Da)
                continue;
            original = b[j].ind;
            cout<<original<<' '<<extra[original]<<' '<<tempd<<endl;
            if(extra[original] * Da >= tempd){
                if(a[i].d - a[j].d >= Cmax * Da){
                    tempfee += (a[j].d + Cmax * Da - a[i-1].d) / Da * b[j].p;
                    extra[original] -= (a[j].d + Cmax * Da - a[i-1].d) / Da;
                    Cnow += (a[j].d + Cmax * Da - a[i-1].d) / Da;
                    tempd = a[i].d - a[j].d - Cmax * Da;
                    j++;
                }else{
                    tempfee += tempd / Da * b[j].p;
                    //cout<<tempd<<' '<<Da<<' '<<H[j].p<<' '<<tempfee<<endl;
                    extra[original] -= tempd / Da;
                    Cnow += tempd / Da;
                    tempd = 0;
                }
            }
            else{
                if(a[i].d - a[j].d >= Cmax * Da){
                    tempfee += (a[j].d + Cmax * Da - a[i-1].d) / Da * b[j].p;
                    extra[original] -= (a[j].d + Cmax * Da - a[i-1].d) / Da;
                    Cnow += (a[j].d + Cmax * Da - a[i-1].d) / Da;
                    tempd = a[i].d - a[j].d - Cmax * Da;
                    j++;
                }else{
                    tempfee += extra[original] * b[j].p;
                    tempd -= extra[original] * Da;
                    Cnow += extra[original];
                    extra[original] = 0;
                    j++;
                }
            }
        }
        //cout<<tempfee;
        fee[i] = fee[i-1] + tempfee;
        b[i].d = a[i].d;
        b[i].p = a[i].p;
        b[i].ind = i;
        HSort2(b,i+1);
        Cnow = 0;
        count++;
    }
    int j = 0;
    tempd = D - a[N-1].d;
    int original;
    while(tempd > 0){
        if(j == N){
            cout<<"The maximum travel distance = "<<fixed<<setprecision(2)<<(float)(D - tempd);
            return 0;
        }
        original = b[j].ind;
        cout<<original<<' '<<extra[original]<<endl;
        if(extra[original] * Da >= tempd){
            if(D - a[j].d >= Cmax * Da){
                fee[N-1] += (a[j].d + Cmax * Da - a[N-1].d) / Da * b[j].p;
                extra[original] -= (a[j].d + Cmax * Da - a[N-1].d) / Da;
                tempd = D - a[j].d - Cmax * Da;
                j++;
            }else{
                fee[N-1] += tempd / Da * b[j].p;
                //cout<<tempd<<' '<<Da<<' '<<H[j].p<<' '<<tempfee<<endl;
                extra[original] -= tempd / Da;
                tempd = 0;
            }
        }
        else{
            if(D - a[j].d >= Cmax * Da){
                fee[N-1] += (a[j].d + Cmax * Da - a[N-1].d) / Da * b[j].p;
                extra[original] -= (a[j].d + Cmax * Da - a[N-1].d) / Da;
                tempd = D - a[j].d - Cmax * Da;
                j++;
            }else{
                fee[N-1] += extra[original] * b[j].p;
                tempd -= extra[original] * Da;
                extra[original] = 0;
                j++;
            }
        }
    }
    //for(int i = 0;i < N;i++)
    //    cout<<fee[i]<<endl;
    cout<<fixed<<setprecision(2)<<fee[N-1];
    return 0;
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
                float t1 = H[k].p;
                int t = H[k].d;
                for(int p = j;p < k;p += D){
                    if(t < H[p].d){
                        for(int s = k;s > p;s -= D){
                            H[s].p = H[s-D].p;
                            H[s].d = H[s-D].d;
                        }
                        H[p].p = t1;
                        H[p].d = t;
                        break;
                    }
                }
            }
        }
    }
}

void HSort2(Node2 *H,int n){
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
                float t1 = H[k].p;
                int t = H[k].d;
                int t2 = H[k].ind;
                for(int p = j;p < k;p += D){
                    if(t1 < H[p].p){
                        for(int s = k;s > p;s -= D){
                            H[s].p = H[s-D].p;
                            H[s].d = H[s-D].d;
                            H[s].ind = H[s-D].ind;
                        }
                        H[p].p = t1;
                        H[p].d = t;
                        H[p].ind = t2;
                        break;
                    }
                }
            }
        }
    }
}
```
# 1034：Head of a Gang
这道题主要思路是**并查集的操作**，通过Union确定集合，再根据限制条件判断这些集合是不是Gang。然后因为最后的输出要按照字符串升序输出，所以还有用**希尔排序**使the head of gang有序。这里还有一个很关键的点是要使用**哈希函数**来将字符串转化为唯一对应的数组索引。
此题代码如下：
```cpp
#include<iostream>
#include<string>
#include<vector>
#include<cmath>

using namespace std;

class Node{
    public:
    int n;
    long int w;
    int totalnum;
    Node(){w = totalnum = 0;}
};

class Node2{
    public:
    string s;
    long int w; 
};

int N,K,si;
Node *a;
vector<Node2> stack;
Node2 *b;

int Hash(string s);
string invHash(int n);
int Findparent(int index);
void Union(int index1, int index2,long int ad);
bool Check(int index1, int index2);
void HSort(Node2 *H,int n);

int main(){
    Node2 n2;
    cin >> N >> K;
    a = new Node[17576];
    for(int i = 0;i < 17576;i++)
        a[i].n = -1;
    string s1,s2;
    int t,ind1,ind2;
    for(int i = 0;i < N;i++){
        cin >> s1 >> s2 >> t;
        ind1 = Hash(s1);
        ind2 = Hash(s2);
        a[ind1].w += t;
        if(ind2 != ind1)
            a[ind2].w += t;
        Union(ind1,ind2,t);
    }
    for(int i = 0;i < 17576;i++){
        if(a[i].n < -2 && a[i].totalnum > K){
            n2.s = invHash(i);
            n2.w = -a[i].n;
            stack.push_back(n2);
        }
    }
    si = stack.size();
    b = new Node2[si];
    int count = 0;
    while(stack.size() != 0){
        b[count++] = stack.back();
        stack.pop_back();
    }
    //for(int i = 0;i < si;i++)
    //    cout<<b[i].s<<' '<<b[i].w<<endl;
    HSort(b,si);
    cout<<si<<endl;
    for(int i = 0;i < si;i++)
        cout<<b[i].s<<' '<<b[i].w<<endl;
    return 0;
}

//找父亲节点
int Findparent(int index){
    int temp = index;
    while(a[temp].n >= 0)
        temp = a[temp].n;
    //路径压缩
    if(index != temp)
        a[index].n = temp;
    return temp;
}

//并，需要用到路径压缩和根据集合大小union
void Union(int index1, int index2,long int ad){
    int root1 = Findparent(index1);
    int root2 = Findparent(index2);
    if(root1 == root2){
        if(a[index1].w > a[root2].w && a[index1].w >= a[index2].w){
            int temp;
            temp = a[root2].n;
            a[root2].n = index1;
            a[index1].n = temp;
            a[index1].totalnum = a[root2].totalnum;
            a[index1].totalnum += ad;
        }
        else if(a[index2].w > a[root2].w && a[index2].w > a[index1].w){
            int temp;
            temp = a[root2].n;
            a[root2].n = index2;
            a[index2].n = temp;
            a[index2].totalnum = a[root2].totalnum;
            a[index2].totalnum += ad;
        }
        else
            a[root1].totalnum += ad;
    }
    //小的集合并到大的集合中
    else if(a[root1].n >= a[root2].n){
        int temp = a[root1].n;
        a[root1].n = root2;
        a[root2].n += temp;
        a[root2].totalnum += ad;
        if(a[index1].w > a[root2].w && a[index1].w >= a[index2].w){
            int temp2;
            temp2 = a[root2].n;
            a[root2].n = index1;
            a[index1].n = temp2;
            a[index1].totalnum = a[root2].totalnum;
        }
        else if(a[index2].w > a[root2].w && a[index2].w > a[index1].w){
            int temp2;
            temp2 = a[root2].n;
            a[root2].n = index2;
            a[index2].n = temp2;
            a[index2].totalnum = a[root2].totalnum;
        }
    }
    else{
        int temp = a[root2].n;
        a[root2].n = root1;
        a[root1].n += temp;
        a[root1].totalnum += ad;
        if(a[index1].w > a[root1].w && a[index1].w >= a[index2].w){
            int temp2;
            temp2 = a[root1].n;
            a[root1].n = index1;
            a[index1].n = temp2;
            a[index1].totalnum = a[root1].totalnum;
        }
        else if(a[index2].w > a[root2].w && a[index2].w > a[index1].w){
            int temp2;
            temp2 = a[root1].n;
            a[root1].n = index2;
            a[index2].n = temp2;
            a[index2].totalnum = a[root1].totalnum;
        }
    }
}

//查，查找两个节点
bool Check(int index1, int index2){
    int root1 = Findparent(index1);
    int root2 = Findparent(index2);
    if(root1 == root2)
        return true;
    else
        return false;
}

int Hash(string s){
    int num = 0;
    num += ((int)s[0] - 65) * 26 * 26;
    num += ((int)s[1] - 65) * 26;
    num += (int)s[2] - 65;
    return num;
}

void HSort(Node2 *H,int n){
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
                string t = H[k].s;
                long int t1 = H[k].w;
                for(int p = j;p < k;p += D){
                    if(t < H[p].s){
                        for(int s = k;s > p;s -= D){
                            H[s].s = H[s-D].s;
                            H[s].w = H[s-D].w;
                        }
                        H[p].s = t;
                        H[p].w = t1;
                        break;
                    }
                }
            }
        }
    }
}

string invHash(int n){
    string temp = "";
    temp += n / (26 * 26) + 'A';
    n = n % (26 * 26);
    temp += n / 26 + 'A';
    n = n % 26;
    temp += n + 'A';
    return temp;
}
```
# 1035：Password
此题代码如下：
```cpp
#include<iostream>
#include<string>
#include<queue>

using namespace std;
class Node{
public:
    string n;
    string p;
};

int N;
int flag;
queue<Node> q;

int main(){
    Node temp;
    cin >> N;
    for(int i = 0;i < N;i++){
        flag = 0;
        cin >> temp.n >> temp.p;
        for(int i = 0;i < temp.p.size();i++){
            if(temp.p[i] == '0'){
                temp.p[i] = '%';
                if(flag == 0)
                    flag = 1;
            }
            else if(temp.p[i] == '1'){
                temp.p[i] = '@';
                if(flag == 0)
                    flag = 1;
            }
            else if(temp.p[i] == 'l'){
                temp.p[i] = 'L';
                if(flag == 0)
                    flag = 1;
            }
            else if(temp.p[i] == 'O'){
                temp.p[i] = 'o';
                if(flag == 0)
                    flag = 1;
            }
        }
        if(flag == 1)
            q.push(temp);
    }
    if(q.size() == 0 && N == 1){
        cout<<"There is 1 account and no account is modified";
        return 0;
    }
    else if(q.size() == 0 && N != 1){
        cout<<"There are "<<N<<" accounts and no account is modified";
        return 0;
    }
    cout<<q.size()<<endl;
    while(q.size() != 0){
        cout<<q.front().n<<' '<<q.front().p<<endl;
        q.pop();
    }
    return 0;
}
```
# 1036：Boys vs Girls
此题代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
int N;
int maxnum = -1;
int minnum = -1;
string mn,mk,fn,fk;

int main(){
    cin >> N;
    string s1,s2;
    char c;int temp;
    for(int i = 0;i < N;i++){
        cin >> s1 >> c >> s2 >> temp;
        if(c == 'F'){
            if(maxnum == -1){
                maxnum = temp;
                fn = s1;
                fk = s2;
            }
            else{
                if(temp > maxnum){
                    maxnum = temp;
                    fn = s1;
                    fk = s2;
                }
            }
        }
        else{
            if(minnum == -1){
                minnum = temp;
                mn = s1;
                mk = s2;
            }
            else{
                if(temp < minnum){
                    minnum = temp;
                    mn = s1;
                    mk = s2;
                }
            }
        }
    }
    if(maxnum != -1)
        cout<<fn<<' '<<fk<<endl;
    else
        cout<<"Absent"<<endl;
    if(minnum != -1)
        cout<<mn<<' '<<mk<<endl;
    else
        cout<<"Absent"<<endl;
    if(maxnum == -1 || minnum == -1)
        cout<<"NA"<<endl;
    else
        cout<<maxnum-minnum;
    return 0;
}
```
# 1037：Magic Coupon
这道题我最初的思路是正的要乘上正的，负的乘负的，然后很容易想到**Top K问题**，只不过是要对正和负都做一次Top K。**Top K问题经常用到最大堆和最小堆以及堆排序**，所以这里我也用的这个方法，但是不知道为什么有一个Checkpoint一直没有通过，我估计是最后的和超过了C++能表示的数据范围。
此题代码如下：
```cpp
#include<iostream>

using namespace std;
long int Nc,Np,tempNc,tempNp;
long int *a,*b,*H1,*H2;
float num = 0;

void Swap(long int &a, long int &b);
void CreateHeap(int index,long int *H);
void CreateHeap2(int index,long int *H);
void DeleteHeap(int index,long int *H,long int temp);

int main(){
    cin >> Nc;
    tempNc = Nc;
    H1 =  new long int[Nc+1];
    for(int i = 1;i <= Nc;i++){
        cin >> H1[i];
        CreateHeap(i,H1);
    }
    cin >> Np;
    tempNp = Np;
    H2 =  new long int[Np+1];
    for(int i = 1;i <= Np;i++){
        cin >> H2[i];
        CreateHeap(i,H2);
    }
    while(tempNc != 0 && tempNp != 0){
        if(H1[1] > 0 && H2[1] > 0){
            num += float(H1[1] * H2[1]);
            DeleteHeap(1,H1,tempNc);
            DeleteHeap(1,H2,tempNp);
            tempNc--;tempNp--;
        }
        else
            break;
    }
    //cout<<num<<endl;
    for(int i = 1;i <= tempNc;i++)
        CreateHeap2(i,H1);
    for(int i = 1;i <= tempNp;i++)
        CreateHeap2(i,H2);
    while(tempNc != 0 && tempNp != 0){
        if(H1[1] < 0 && H2[1] < 0){
            num += float(H1[1] * H2[1]);
            DeleteHeap(1,H1,tempNc);
            DeleteHeap(1,H2,tempNp);
            tempNc--;tempNp--;
        }
        else
            break;
    }
    cout<<num<<endl;
    return 0;
}

void Swap(long int &a, long int &b){
    long int temp1;
    temp1 = a;
    a = b;
    b = temp1;
}

void CreateHeap(int index,long int *H){
    //建堆，插入新元素后调整成堆
    int temp = index;
    for(;temp != 1;temp = temp/2){
        if(H[temp] > H[temp/2])
            Swap(H[temp],H[temp/2]);
        else
            break;
    }
}

void CreateHeap2(int index,long int *H){
    //建堆，插入新元素后调整成堆
    int temp = index;
    for(;temp != 1;temp = temp/2){
        if(H[temp] < H[temp/2])
            Swap(H[temp],H[temp/2]);
        else
            break;
    }
}

//堆的删除，用来实现堆排序
void DeleteHeap(int index,long int *H,long int tempN){
    int Ms = tempN;
    if(Ms == 0)
        return;
    else if(Ms == 1){
        return;
    }
    if(index == 1){
        H[1] = H[Ms];
    }
    //从两个子节点中找出比现在这个元素小的，交换位置并迭代函数
    //下面考虑了两种节点的多种情况（可能为空）
    if(2*index <= tempN && 2*index+1 <= tempN){
        if(H[2*index] >= H[2*index+1] && H[2*index] > H[index]){
            Swap(H[index],H[2*index]);
            DeleteHeap(2*index,H,tempN);
        }
        else if(H[2*index] <= H[2*index+1] && H[2*index+1] > H[index]){
            Swap(H[index],H[2*index+1]);
            DeleteHeap(2*index+1,H,tempN);
        }
        else
            return;
    }
    else if(2*index <= tempN && 2*index+1 > tempN){
        if(H[2*index] > H[index]){
            Swap(H[index],H[2*index]);
            DeleteHeap(2*index,H,tempN);
        }
        else
            return;
    }
    else
        return;
}
```
# 1038：Recover the Smallest Number
这道题我没有AC，最后有一个测试点显示运行超时，我觉得是因为当子序列长时冒泡排序效率太低导致的，但是我觉得我的思路差不多是正确的了，就是整体上分两步排序：

   - 一是用希尔排序，排序关键字为每个string的首字符
   - 二是用冒泡排序，对同个首字符的子字符串序列进行排序，这里排序的标准是当交换两个字符串后这部分值比原来小的话，就交换相邻两个字符串

此题代码如下：
```cpp
#include<iostream>
#include<string>
#include<cmath>

using namespace std;
int N;
string *a;

void Swap(string &a, string &b);
void HSort(string *H,int n);
void BSort(string *H,int s,int e);

int main(){
    cin >> N;
    a = new string[N];
    for(int i = 0;i < N;i++)
        cin >> a[i];
    HSort(a,N);
    int i = 0;
    for(;i < N;i++){
        if(i != 0 && a[i][0] == a[i-1][0]){
            int j = i;
            for(;j < N;j++){
                if(a[j][0] != a[j-1][0])
                    break;
            }
            BSort(a,i-1,j-1);
            i = j - 1;
        }
    }
    int flag2 = 0;
    for(int i = 0;i < N;i++){
        if(flag2 == 0){
            for(int j = 0;j < a[i].size();j++){
                if(flag2 == 0 && a[i][j] == '0')
                    continue;
                else{
                    cout<<a[i][j];
                    if(flag2 == 0)
                        flag2 = 1;
                }
            }
        }
        else
            cout << a[i];
    }
    if(flag2 == 0)
        cout<<'0';
    return 0;
}

void HSort(string *H,int n){
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
                string t = H[k];
                for(int p = j;p < k;p += D){
                    if(t[0] < H[p][0]){
                        for(int s = k;s > p;s -= D){
                            H[s] = H[s-D];
                        }
                        H[p] = t;
                        break;
                    }
                }
            }
        }
    }
}

void BSort(string *H,int s,int e){
    int flag;
    while(true){
        flag = 0;
        for(int i = s;i <= e-1;i++){
            for(int j = i;j <=e-1;j++){
                if((H[j] + H[j+1]).compare(H[j+1] + H[j]) == 1){
                    Swap(H[j],H[j+1]);
                    if(flag == 0)
                        flag = 1;
                }
            }
        }
        if(flag == 0)
            break;
    }
}

void Swap(string &a, string &b){
    string temp = a;
    a = b;
    b = temp;
}

```
# 1039：Course List for Student
这题主要思想就是哈希函数和链表的元素插入（BTW，链表插入卡了我好久）
此题代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
class Node{
    private:
    long int value;
    Node* next;
    public:
    Node(){ next = NULL; }
    long int Getvalue() { return this->value; }
    Node* Getnext() { return this->next; }
    void Setvalue(long int num) { value = num; }
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
    void Addnum(long int num){
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
                if(num > p->Getvalue()){
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
    void Delnum(long int num){
        Node* p1 = head;
        Node* p2 = head;
        if (head == NULL){
            cout << "这是空列表" << endl;
            return;
        }
        else{
            if (head->Getvalue() == num){
                head = head->Getnext();
                return;
            }
            else
                p2 = p2->Getnext();
            while (p2 != NULL){
                if (p2->Getvalue() == num){
                    p1->Setnext(p2->Getnext());
                    cout << "删除成功" << endl;
                    return;
                }
                p1 = p1->Getnext();
                p2 = p2->Getnext();
            }
        }
    }
};

long int N,K;
Nodearray *a;

long int Hash(string s);

int main(){
    cin >> N >> K;
    int index,snum;
    string temp;
    a = new Nodearray[175760];
    for(int k = 0;k < K;k++){
        cin >> index >> snum;
        for(int i = 0;i < snum;i++){
            cin >> temp;
            a[Hash(temp)].Addnum(index);
        }
    }
    string sqn;
    Node* p;
    while(cin >> sqn){
        cout<<sqn;
        p = a[Hash(sqn)].head;
        cout<<' '<<a[Hash(sqn)].total;
        while(p != NULL){
            cout<<' '<<p->Getvalue();
            p = p->Getnext();
        }
        cout<<endl;
    }
    return 0;
}

long int Hash(string s){
    long int num = 0;
    num += ((int)s[0] - 65) * 26 * 26;
    num += ((int)s[1] - 65) * 26;
    num += (int)s[2] - 65;
    num *= 10;
    num += (int)s[3] - 48;
    return num;
}
```
# 1040：Longest Symmetric String
提示：分别考虑最后的结果是奇数和偶数的情况即可。
此题代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
string s;
int maxlen = 0;
int j;

int main(){
    getline(cin,s);
    if(s.size() == 1){
        cout<<'1';
        return 0;
    }
    else if(s.size() == 2){
        if(s[0] == s[1])
            cout<<'2';
        else
            cout<<'1';
        return 0;
    }
    int i = 1;
    for(;i < s.size();){
        j = 1;
        while(i-j >= 0 && i+j <s.size()){
            if(s[i-j] == s[i+j])
                j++;
            else
                break;
        }
        if(2*j-1 > maxlen)
            maxlen = 2*j-1;
        if(s[i] == s[i-1]){
            j = 1;
            while(i-1-j >= 0 && i+j <s.size()){
                if(s[i-1-j] == s[i+j])
                    j++;
                else
                    break;
            }
            if(2*j > maxlen)
                maxlen = 2*j;
        }
        i++;
    }
    cout<<maxlen;
    return 0;
}
```
# 阶段性总结
