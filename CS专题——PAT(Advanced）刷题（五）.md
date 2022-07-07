哈哈哈我又来做PAT了，为什么说”又“呢？——就是因为其实我前一段时间去刷ZOJ了，被ZOJ题目的难度虐的不行，发现ZOJ不太适合像我这种还在学习算法与数据结构的萌新小白，就果断回到PAT的怀抱了（不是说PAT就简单（doge）），那这次也是我PAT甲级刷题的第五次笔记，希望大家多多支持。
# 1041：Be Unique
直接用空间换时间，**因为答案是要最先出现的单独数，容易想到使用队列（队列先进先出）**，如果队列队首不止出现一次了，就弹出，再判断下一个队首是否只出现一次。在线处理，**常数时间复杂度**$O(N)$。
代码如下：
```cpp
#include<iostream>
#include<queue>

using namespace std;
long int N;
long int index;
long int a[10000];

int main(){
    cin >> N;
    queue<long int> q;
    for(int n = 0;n < N;n++){
        cin >> index;
        if(a[index-1] == 0)
            q.push(index);
        else{
            if(q.size() != 0 && index == q.front()){
                q.pop();
                while(q.size() != 0 && a[q.front()-1] != 1)
                    q.pop();
            }
        }
        a[index-1] ++;
    }
    if(q.size() == 0)
        cout<<"None";
    else
        cout<<q.front();
    return 0;
}
```
# 1042：Shuffling Machine
代码如下：
```cpp
#include<iostream>
#include<cstdio>

using namespace std;
int K;
int a[54];
int a1[54];
int a2[54];

int main(){
    cin >> K;
    int index;
    for(int i = 0;i < 54;i++){
        cin >> index;
        a[i] = index-1;
        a1[index-1] = i;
    }
    //for(int i = 0;i < 54;i++)
    //    cout<<a1[i]<<" ";
    //cout<<endl;
    for(int i = 1;i < K;i++){
        for(int j = 0;j < 54;j++){
            if(i % 2 == 1)
                a2[a[j]] = a1[j];
            else
                a1[a[j]] = a2[j];
        }
    }
    int count = 0;
    if(K % 2 == 1){
        for(int i = 0;i < 54;i++){
            if(count == 0){
                count++;
            }
            else
                cout<<" ";
            if(a1[i] == 52)
                cout<<"J1";
            else if(a1[i] == 53)
                cout<<"J2";
            else{
                int huase = a1[i]/13;
                int num = (a1[i] + 1)%13;
                if(num == 0)
                    num = 13;
                if(huase == 0)
                    cout<<"S";
                else if(huase == 1)
                    cout<<"H";
                else if(huase == 2)
                    cout<<"C";
                else
                    cout<<"D";
                cout<<num;
            }
        }
    }
    else{
        for(int i = 0;i < 54;i++){
            if(count == 0){
                count++;
            }
            else
                cout<<" ";
            if(a2[i] == 52)
                cout<<"J1";
            else if(a2[i] == 53)
                cout<<"J2";
            else{
                int huase = a2[i]/13;
                int num = (a2[i] + 1)%13;
                if(num == 0)
                    num = 13;
                if(huase == 0)
                    cout<<"S";
                else if(huase == 1)
                    cout<<"H";
                else if(huase == 2)
                    cout<<"C";
                else
                    cout<<"D";
                cout<<num;
            }
        }
    }
    return 0;
}
```
# 1043：Is it a Binary Tree
这道题难点在于Mirror的问题，所以我们可以通过第一次的函数使用来确定是否是Mirror，然后将序列二分，递归使用判断函数判断子序列是否也是BST即可，主要还是用到树的递归性质。
代码如下：
```cpp
#include<iostream>
#include<vector>

using namespace std;
int N,flag,pat,flag2;
int *a;
vector<int> s;

void Check(int l,int r,int p);

int main(){
    flag = pat = flag2 = 0;
    cin >> N;
    a = new int[N];
    for(int n = 0;n < N;n++)
        cin >> a[n];
    Check(0,N-1,pat);
    if(flag2 == 1){
        pat = 1;
        Check(0,N-1,pat);
    }
    if(flag == 1)
        cout<<"NO";
    else{
        cout<<"YES"<<endl;
        cout<<s.back();
        s.pop_back();
        while(s.size() != 0){
            cout<<' '<<s.back();
            s.pop_back();
        }
    }
    return 0;
}

void Check(int l,int r,int p){
    int num = p;
    if(flag == 1)
        return;
    if(l > r)
        return;
    s.push_back(a[l]);
    if(l == r)
        return;
    int start = l + 1;
    for(;start <= r;start++){
        if(a[start] >= a[l] && p == 0)
            break;
        else if(a[start] < a[l] && p == 1)
            break;
    }
    int temp = start;
    for(;start <= r;start++){
        if(a[start] < a[l] && p == 0){
            if(l == 0 && r == N-1 && temp == l + 1){
                s.pop_back();
                flag2 = 1;
                return;
            }
            else{
                flag = 1;
                break;
            }
        }
        else if(a[start] >= a[l] && p == 1 && (l != 0 || r != N-1)){
            flag = 1;
            break;
        }
    }
    Check(temp,r,p);
    Check(l+1,temp-1,p);
}
```
# 1044：Shopping in Mars
这道题一开始我用的DP算法（动态规划），但是发现DP和直接遍历没有太大区别，于是改用**快慢指针**，然后考虑每次快慢指针内的求和值，针对不同求和值的目标值的大小情况来进行分别处理。
代码如下：
```cpp
#include<iostream>
#include<queue>

using namespace std;
class Node{
public:
    int left;
    int right;
};

long int N,M;
long int *a;
long int maxnum,tempnum;
Node temp;
queue<Node> q;

void clear(queue<Node>& q){
	queue<Node> empty;
	swap(empty,q);
}

int main(){
    //输入数据
    int flag = 0;
    maxnum = -1;
    cin >> N >> M;
    a = new long int[N];
    for(int i = 0;i < N;i++)
        cin >> a[i];
    int s,e;
    //定义双指针，快慢指针
    s = e = 0;
    tempnum = a[s];
    while(s != N){
        //cout<<s<<' '<<e<<endl;
        if(tempnum == M){
            //发现求和为目标值，则输出并令慢指针+1
            if(flag == 0)
                flag = 1;
            cout<<(s+1)<<'-'<<(e+1)<<endl;
            if(s == N-1)
                break;
            else{
                tempnum -= a[s];
                s ++;
            }
        }
        if(tempnum > M){
            //发现求和值大于目标值，则判断下其是不是目前大值中的最小值
            if(maxnum == -1){
                maxnum = tempnum;
                temp.left = s;
                temp.right = e;
                //用一个队列来保存满足条件的i-j对
                q.push(temp);
            }
            else if(tempnum < maxnum){
                maxnum = tempnum;
                temp.left = s;
                temp.right = e;
                clear(q);
                q.push(temp);
            }
            else if(tempnum == maxnum){
                temp.left = s;
                temp.right = e;
                q.push(temp);
            }
            tempnum -= a[s];
            s ++;
        }
        else{
            //如果快指针已经扫到末尾了，则退出循环
            //疑问：提前退出循环会不会影响最小大值的情况？
            if(e == N-1)
                break;
            else{
                //否则快指针+1，求和值变大
                e ++;
                tempnum += a[e];
            }
        }
    }
    if(flag == 0){
        while(q.size() != 0){
            cout<<(q.front().left+1)<<'-'<<(q.front().right+1)<<endl;
            q.pop();
        }
    }
    return 0;
}
```
# 1045：Favorite Color Stripe
经典的DP问题，先将其他不喜欢的颜色去掉，然后DP直接秒了。
代码如下：
```cpp
#include<iostream>
#include<algorithm>
#include<vector>
#define MAXM 201
using namespace std;
int main() {
	int N, M, L;
	int index = 0;
	int maxN = -1;
	cin >> N;
	cin >> M;
	vector<int> fav(MAXM);
    int temp;
	for (int i = 1; i <= M; i++) {
		cin >> temp;
		fav[temp] = i;
	}
	cin >> L;
	vector<int> color(L);
    //去除不在喜欢颜色里的颜色片段
	for (int i = 0; i < L; i++) {
		cin >> temp;
		if (fav[temp] != 0)
			color[index++] = temp;
	}
    //dp算法，直接秒杀
	vector<int> dp(L);
	for (int i = 0; i < index; i++) {
		dp[i] = 1;
		for (int j = 0; j < i; j++) {
			if (fav[color[i]] >= fav[color[j]])
				dp[i] = max(dp[i], dp[j] + 1);
		}
		maxN = max(dp[i], maxN);
	}
	cout << maxN << endl;
}
```
# 1046：Shortest Distance
我的代码如下：
```cpp
#include<iostream>
#include<cstdio>
#define MAX 1000

using namespace std;
long int N;
int M;
int *a;
int **dp;

int main(){
    cin >> N;
    a = new int[N];
    dp = new int*[N];
    for(int i = 0;i < N;i++)
        dp[i] = new int[N];
    for(int i = 0;i < N;i++){
        for(int j = 0;j < N;j++){
            if(i == j)
                dp[i][j] = 0;
            else
                dp[i][j] = MAX;
        }
    }
    int num = 0;
    for(int i = 0;i < N;i++){
        cin >> a[i];
        num += a[i];
    }
    for(int i = 0;i < N;i++){
        for(int j = i;j < N;j++){
            if(i == j)
                continue;
            else
                dp[i][j] = dp[j][i] = dp[i][j-1] + a[j-1];
        }
    }
    cin >> M;
    int ind1,ind2;
    for(int m = 0;m < M;m++){
        cin >> ind1 >> ind2;
        if(dp[ind1-1][ind2-1] * 2 > num)
            dp[ind1-1][ind2-1] = num - dp[ind1-1][ind2-1];
        cout<<dp[ind1-1][ind2-1]<<endl;
    }
    return 0;
}
```
# 1047：Student List for Course
不知道为什么耗时超限，链表查找这么慢的吗。。。
代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;

class Node{
    public:
    string s;
    long int value;
    Node* next;
    Node(){ next = NULL;value = 0; }
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
    void Addnum(string num){
        total++;
        if (head == NULL){
            head = new Node;
            head->s = num;
            head->Setvalue(1);
            head->Setnext(NULL);
            tail = head;
        }
        else{
            Node* p = this->head;
            Node* tempp = p;
            while(p != NULL){
                if(num > p->s){
                    tempp = p;
                    p = p->Getnext();
                }
                else{
                    Node* temp;
                    temp = new Node;
                    temp->s = num;
                    temp->Setvalue(1);
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
            tempp->s = num;
            tempp->Setvalue(1);
            tempp->Setnext(NULL);
            return;
        }
    }
};

long int N,K;
int C,index;
string ss;
Nodearray *a;

int main(){
    cin >> N >> K;
    a = new Nodearray[K];
    for(int n = 0;n < N;n++){
        cin >> ss >> C;
        for(int i = 0;i < C;i++){
            cin >> index;
            a[index-1].Addnum(ss);
        }
    }
    Node *p;
    for(int k = 0;k < K;k++){
        cout << (k+1) << ' ' << a[k].total<<endl;
        p = a[k].head;
        while(p != NULL){
            cout<<p->s<<endl;
            p = p->Getnext();
        }
    }
    return 0;
}

```
# 1048：Find Coins
这道题有两种思路，**一种是将数据放在一个数组里面**，假如说输入数据是409，则数组中索引为408的那个值变为1，代表输入中存在409这个值，然后再遍历一次就行了；上面这种方法能这么做的原因是面值比较小，当面值比较大的话，数组就太大了，**所以另外一种方法是排序+双指针**，一个指向当前最前面，一个指向最后面，如果两者之和小于target，就让快指针+1，直到找到target值为止（Leetcode经典题目——两数之和）。
存放在数组里面的方法代码如下：
```cpp
/*
author : eclipse
email  : eclipsecs@qq.com
time   : Mon Jun 22 08:46:08 2020
*/
#include <bits/stdc++.h>
using namespace std;

const int MAX_SIZE = 1024;

int main(int argc, char const *argv[]) {
    int N, M;
    scanf("%d%d", &N, &M);
    vector<int> faceValues;
    faceValues.resize(MAX_SIZE);
    for (int i = 0; i < N; i++) {
        int faceValue;
        scanf("%d", &faceValue);
        faceValues[faceValue]++;
    }
    for (int i = 0; i < M; i++) {
        if (faceValues[i] && faceValues[M - i]) {
            if (i == M - i) {
                if (faceValues[i] >= 2) {
                    printf("%d %d", i, M - i);
                    return 0;
                }
            } else {
                printf("%d %d", i, M - i);
                return 0;
            }
        }
    }
    printf("No Solution");
    return 0;
}


```
双指针代码如下：
```cpp
#include<iostream>
#include<cmath>

using namespace std;
long int N;
int M;
int *a;

void HSort1(int *H,long int n);

int main(){
    cin >> N >> M;
    a = new int[N];
    for(int i = 0;i < N;i++)
        cin >> a[i];
    HSort1(a,N);
    if(N == 1 || a[N-1] * 2 < M || a[0] * 2 > M){
        cout<<"No Solution"<<endl;
        return 0;
    }
    int s = 0;
    int e = N-1;
    int temp,flag,flag2;
    flag = flag2 = 0;
    while(s < N - 1){
        if(e <= s)
            e = s + 1;
        if(a[s] * 2 > M){
            cout<<"No Solution"<<endl;
            return 0;
        }
        temp = a[s] + a[e];
        if(temp == M){
            cout<<a[s]<<' '<<a[e];
            return 0;
        }
        else if(temp > M){
            flag2 = 1;
            if((flag == 1 && flag2 == 1)||(e == s + 1)){
                flag = flag2 = 0;
                s ++;
                continue;
            }
            else
                e --;
        }
        else{
            flag = 1;
            if((flag == 1 && flag2 == 1)||(e == N - 1)){
                flag = flag2 = 0;
                s ++;
                continue;
            }
            else
                e ++;
        }
    }
    cout<<"No Solution"<<endl;
    return 0;
}

void HSort1(int *H,long int n){
    int count = 1;
    while((long int)pow(2,count) - 1 < n)
        count++;
    count--;
    long int D;
    for(int i = count;i > 0;i--){
        D = (int)pow(2,i) - 1;
        for(long int j = 0;j < D;j++){
            for(long int k = j;k < n;k += D){
                if(k == j)
                    continue;
                int t1 = H[k];
                for(long int p = j;p < k;p += D){
                    if(t1 < H[p]){
                        for(long int s = k;s > p;s -= D)
                            H[s] = H[s-D];
                        H[p] = t1;
                        break;
                    }
                }
            }
        }
    }
}
```
# 1049：Counting Ones
想清楚怎么判断有多少个1，显然不是逐个数字判断，存在某个规律（很多分类讨论）。
代码如下：
```cpp
#include<iostream>
#include<string>
#include<cmath>

using namespace std;
string s;
long int num = 0;
long int temp,tempnum;

int main(){
    cin >> s;
    for(int i = 0;i < s.size();i++){
        if(i == 0){
            if(s[i] == '1'){
                if(s.size() != 1)
                    num += stoi(s.substr(1,s.size()-1)) + 1;
                else
                    num = 1;
            }
            else
                num += (long int)pow(10,(float)(s.size()-i-1));
            //cout<<num<<endl;
            continue;
        }
        temp = (long int)pow(10,(float)(s.size()-i));
        tempnum = (stoi(s) / temp);
        if(s[i] > '1')
            num += (tempnum + 1) * (long int)pow(10,(float)(s.size()-i-1));
        else if(s[i] == '0')
            num += (tempnum) * (long int)pow(10,(float)(s.size()-i-1));
        else{
            if(i != s.size()-1)
                num += (tempnum) * (long int)pow(10,(float)(s.size()-i-1)) + stoi(s.substr(i+1,s.size()-i-1)) + 1;
            else
                num += (tempnum + 1) * (long int)pow(10,(float)(s.size()-i-1));
        }
        //cout<<num<<endl;
    }
    cout<<num;
    return 0;
}
```
# 1050：String Subtraction
代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
string s1,s2,temp;
int a[128];

int main(){
    temp = "";
    getline(cin,s1);
    getline(cin,s2);
    for(int i = 0;i < s2.size();i++){
        if(a[(int)s2[i]] == 0)
            a[(int)s2[i]] = 1;
    }
    for(int i = 0;i < s1.size();i++){
        if(a[(int)s1[i]] == 0)
            temp += s1[i];
    }
    cout<<temp;
    return 0;
}
```
# 阶段性总结
