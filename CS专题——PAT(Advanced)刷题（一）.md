这是我开的一个新的系列。有读者可能要问：为什么无视Basic难度的题目？因为——确确实实太简单了，基本上属于编程基本功的难度，比较适合刚上手C/C++的小伙伴结合《C++程序设计基础》去做，而Advanced难度比较适合正在学习《算法与数据结构》的小伙伴去做。笔者最近在学习浙大《数据结构》，所以开的这个系列就直接从PAT(Advanced)开始记录把！
关于PTA(Basic)，可以参考我语雀的这篇文章：
[https://www.yuque.com/beifengchuisanzhiyue/lz5gx3/fmvrl9](https://www.yuque.com/beifengchuisanzhiyue/lz5gx3/fmvrl9)
# 1001：A+B Format
这道题算是开胃菜了，整体难度不难，但是也算对于PAT（Advanced）的引入了，用一个堆栈就可以实现。思路：逆序扫描字符串——正序输出——正反——堆栈。
此题代码如下：
```cpp
#include<iostream>
#include<string>
#include<vector>

using namespace std;
long int a,b;
string s;

int main(){
    cin >> a >> b;
    a = a + b;
    s = to_string(a);
    if(s[0] == '-'){
        cout<<'-';
        s = s.substr(1,s.size()-1);
    }
    vector<char> stack;
    for(int i = 0;i < s.size();i++){
        stack.push_back(s[s.size()-i-1]);
        if(i % 3 == 2 && i != s.size()-1)
            stack.push_back(',');
    }
    while(stack.size() != 0){
        cout<<stack.back();
        stack.pop_back();
    }
    return 0;
}
```
# 1002：A+B for Polynominals
这题算是有点Advanced的味道了，用一个快慢指针或者链表都可以实现，难度尚可。思路：数量较少——节点一个接一个——链表。
此题代码如下：
```cpp
#include<iostream>
#include <bits/stdc++.h>
#define Maxsize 1001

using namespace std;

class Node{
public:
    float xishu;
    int zhishu;
};

int K1,K2;
Node *a,*b;
Node c[Maxsize];
int cnum = 0;

int main(){
    cin >> K1;
    a = new Node[K1];
    for(int i = 0;i < K1;i++)
        cin >> a[i].zhishu >> a[i].xishu;
    cin >> K2;
    b = new Node[K2];
    for(int i = 0;i < K2;i++)
        cin >> b[i].zhishu >> b[i].xishu;
    int start1,start2;
    start1 = start2 = 0;
    while(start1 != K1 && start2 != K2){
        if(a[start1].zhishu > b[start2].zhishu && a[start1].xishu != 0){
            c[cnum].zhishu = a[start1].zhishu;
            c[cnum++].xishu = a[start1++].xishu;
        }
        else if(a[start1].zhishu < b[start2].zhishu && b[start2].xishu != 0){
            c[cnum].zhishu = b[start2].zhishu;
            c[cnum++].xishu = b[start2++].xishu;
        }
        else{
            if(a[start1].xishu != - b[start2].xishu){
                c[cnum].zhishu = b[start2].zhishu;
                c[cnum++].xishu = a[start1].xishu + b[start2].xishu;
            }
            start1++;start2++;
        }
    }
    while(start1 != K1){
        if(a[start1].xishu != 0){
            c[cnum].zhishu = a[start1].zhishu;
            c[cnum++].xishu = a[start1].xishu;
        }
        start1++;
    }
    while(start2 != K2){
        if(b[start2].xishu != 0){
            c[cnum].zhishu = b[start2].zhishu;
            c[cnum++].xishu = b[start2].xishu;
        }
        start2++;
    }
    cout<<cnum;
    for(int i = 0;i < cnum;i++)
        cout<<" "<<c[i].zhishu<<" "<<fixed<<setprecision(1)<<c[i].xishu;
    return 0;
}
```
# 1003：Emergency
这题需要用一个迪杰斯特拉最短路算法（进阶版）和一些图论的知识，难度尚可。
此题代码如下：
```cpp
#include<iostream>
#define Maxsize 500
#define num 99999999

using namespace std;
int N,M,C1,C2;
int min1,u;
int a[Maxsize][Maxsize];
int *rescue,*vis,*dis,*disnum,*rescuet;

void dijkstra(int input);

int main(){
    cin >> N >> M >> C1 >> C2;
    int c1,c2,length;
    rescue = new int[N];
    vis = new int[N];
    dis = new int[N];
    rescuet = new int[N];
    disnum = new int[N];
    for(int i = 0;i < N;i++)
        cin >> rescue[i];
    if(C1 == C2){
        cout<<"1 "<<rescue[C1];
        return 0;
    }
    for(int i = 0;i < N;i++)
        for(int j = 0;j < N;j++)
            a[i][j] = num;
    for(int i = 0;i < M;i++){
        cin >> c1 >> c2 >>length;
        a[c1][c2] = a[c2][c1] = length;
    }
    //最短路算法
    for (int p = 0;p < N;p++)
        vis[p] = rescuet[p] = 0;
    rescuet[C1] = rescue[C1];
    for (int j = 0; j < N; j++)
        dis[j] = a[C1][j];
    for (int j = 0; j < N; j++){
        if(dis[j] != num){
            disnum[j] = 1;
            rescuet[j] = rescuet[C1] + rescue[j];
        }
    }
    vis[C1] = 1;
    dijkstra(C1);
    cout<<disnum[C2]<<" "<<rescuet[C2];
    return 0;
}

void dijkstra(int input){
    for (int i = 0; i < N - 1; i++){
        min1 = num;
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
            if (a[u][v] < num){
                // 如果当前的dis[v]不满足三角形不等式，那么进行松弛操作
                if (dis[v] > dis[u] + a[u][v]){
                    dis[v] = dis[u] + a[u][v];
                    rescuet[v] = rescuet[u] + rescue[v];
                    disnum[v] = disnum[u];
                }
                else if(dis[v] == dis[u] + a[u][v]){
                    disnum[v] += disnum[u];
                    if(rescuet[v] < rescuet[u] + rescue[v])
                        rescuet[v] = rescuet[u] + rescue[v];
                }
            }
        }
    }
}

```
# 1004：Counting Leaves
我感觉这道题难度挺大的，正确率挺高的啊，难道是我没用对方法吗？我主要是用到一个链表、队列还有树的知识，主要是层序遍历，感觉难度不小，加油。思路：树——按层输出——层序遍历。
此题代码如下：
```cpp
#include<iostream>
#include<string>
#include<queue>
#define Maxsize 100

using namespace std;

class Node{
private:
    int value;
    Node* next;
public:
    Node(){
        value = 0;
        next = NULL;
    }
    int Getvalue() { return this->value; }
    Node* Getnext() { return this->next; }
    void Setvalue(int num) { value = num; }
    void Setnext(Node* a) { next = a; }
};

class Nodearray
{
public:
    Node* head;
    Node* tail;
    Nodearray(){
        head = NULL;
        tail = NULL;
    }
    void Addnum(int num){
        if (head == NULL){
            head = new Node;
            head->Setvalue(num);
            head->Setnext(NULL);
            tail = head;
        }
        else{
            tail->Setnext(new Node);
            (tail->Getnext())->Setvalue(num);
            (tail->Getnext())->Setnext(NULL);
            tail = tail->Getnext();
        }
    }
    void Display(){
        Node* p = head;
        if (head == NULL) return;
        else{
            while (p != NULL){
                cout << p->Getvalue() << " ";
                p = p->Getnext();
            }
        }
        cout << endl;
    }
    void Getnum(int pos){
        int count = 1;
        Node* p = head;
        while (p != NULL){
            if (count == pos){
                cout << "找到了！ ";
                cout << p->Getvalue() << endl;
                return;
            }
            p = p->Getnext();
            count++;
        }
        cout << "没找到！" << endl;
    }
    void Delnum(int num){
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

int N,M,K;
string s;
Nodearray *a;
int layernum = 0;
int leaf = 0;

int main(){
    int index,temp;
    cin >> N >> M;
    a = new Nodearray[N];
    if(M == 0){
        cout<<"1";
        return 0;
    }
    for(int m = 0; m < M;m++){
        cin >> s >> K;
        index = stoi(s);
        for(int i = 0;i < K;i++){
            cin >> s;
            temp = stoi(s);
            a[index].Addnum(temp);
        }
    }
    queue<int> q;
    if(a[1].head == NULL){
        cout<<'1';
        return 0;
    }
    else{
        cout<<'0';
        Node *p = a[1].head;
        while(true){
            if(p == NULL)
                break;
            q.push(p->Getvalue());
            layernum++;
            p = p->Getnext();
        }
    }
    int ind;
    int templayernum;
    while(layernum != 0){
        //cout<<layernum<<endl;
        templayernum = layernum;
        leaf = layernum = 0;
        for(int i = 0;i < templayernum;i++){
            ind = q.front();
            q.pop();
            if(a[ind].head == NULL)
                leaf++;
            else{
                Node *p = a[ind].head;
                while(true){
                    if(p == NULL)
                        break;
                    q.push(p->Getvalue());
                    layernum++;
                    p = p->Getnext();
                }
            }
        }
        cout<<' '<<leaf;
    }
    return 0;
}
```
# 1005：Spell It Right
太简单了，就是在线处理的方法，不多说了。
此题代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
string s,s2;
int num = 0;

int main(){
    cin >> s;
    for(int i = 0;i < s.size();i++)
        num += (int)s[i] - 48;
    s2 = to_string(num);
    int count = 0;
    for(int i = 0;i < s2.size();i++){
        if(count == 0)
            count++;
        else
            cout<<" ";
        if(s2[i] == '0')
            cout<<"zero";
        else if(s2[i] == '1')
            cout<<"one";
        else if(s2[i] == '2')
            cout<<"two";
        else if(s2[i] == '3')
            cout<<"three";
        else if(s2[i] == '4')
            cout<<"four";
        else if(s2[i] == '5')
            cout<<"five";
        else if(s2[i] == '6')
            cout<<"six";
        else if(s2[i] == '7')
            cout<<"seven";
        else if(s2[i] == '8')
            cout<<"eight";
        else if(s2[i] == '9')
            cout<<"nine";
    }
    return 0;
}
```
# 1006：Sign In and Sign Out
这道题也是在线处理，跟在序列中求最大值是一样的，难度较小。
此题代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
int M;
string s1,s2,s3;
long int maxnum = -1;
string maxs,mins;
long int minnum = -1;

int main(){
    cin >> M;
    int pos1,pos2,pos3,pos4;
    int num1,num2,num3,num4,num5,num6;
    for(int m = 0;m < M;m++){
        cin >> s1 >> s2 >> s3;
        pos1 = s2.find_first_of(':');
        pos2 = s2.find_last_of(':');
        pos3 = s3.find_first_of(':');
        pos4 = s3.find_last_of(':');
        num1 = stoi(s2.substr(0,pos1));
        num2 = stoi(s2.substr(pos1+1,pos2-pos1-1));
        num3 = stoi(s2.substr(pos2+1,s2.size()-pos2-1));
        num4 = stoi(s3.substr(0,pos3));
        num5 = stoi(s3.substr(pos1+1,pos4-pos3-1));
        num6 = stoi(s3.substr(pos2+1,s3.size()-pos4-1));
        if(maxnum == -1 && minnum == -1){
            minnum = num1 * 3600 + num2 * 60 + num3;
            maxnum = num4 * 3600 + num5 * 60 + num6;
            maxs = mins = s1;
        }
        else{
            if(num1 * 3600 + num2 * 60 + num3 < minnum){
                minnum = num1 * 3600 + num2 * 60 + num3;
                mins = s1;
            }
            if(num4 * 3600 + num5 * 60 + num6 > maxnum){
                maxnum = num4 * 3600 + num5 * 60 + num6;
                maxs = s1;
            }
        }
    }
    cout<<mins<<' '<<maxs;
    return 0;
}
```
# 1007：Maximum Subsequence Sum
这道题难度挺大的，主要是首先要想到“在线处理”的方法，从而达到$O(N)$的时间复杂度，其次还要根据题目的需要做一些改变，这道题浙大《数据结构》课堂上有讲过，但是这道题比课上讲得更难，做题时脑子要清楚，不要成一团浆糊。
此题代码如下：
```cpp
#include<stdio.h>
#define Maxsize 100000

int main(){
    //这题就是上一题的PLUS版本
    //需要我们记录头部和尾部
    //同样大小的和要找头下标最小的子列
    //且需要考虑很多极端情况
    int K;
    scanf("%d",&K);
    int a[Maxsize];
    int thissum,maxsum,maxfirst,maxlast,thisfirst,thislast;
    thissum = maxsum = 0;
    maxfirst = maxlast = 0;
    thisfirst = thislast = 0;
    int flag = 0;
    int flag2 = 0;
    int flag3 = 0;
    int start,end;
    for(int i=0;i<K;i++){
        scanf("%d",&a[i]);
        if(a[i] > 0)
            flag = 1;
        else if(a[i] == 0)
            flag2 = 1;
        if(flag3 == 0){
            start = i;
            thisfirst = a[i];
            flag3 = 1;
        }
        thislast = a[i];
        end = i;
        thissum += a[i];
        //length++;
        if(thissum > maxsum){
            maxsum = thissum;
            maxfirst = thisfirst;
            maxlast = thislast;
        }
        if(thissum < 0){
            thissum = 0;
            flag3 = 0;
        }
        //printf("%d",thissum);
    }
    if(flag == 1)
        printf("%d %d %d",maxsum,maxfirst,maxlast);
    else{
        if(flag2 == 1)
            printf("%d %d %d",maxsum,0,0);
        else
            printf("%d %d %d",maxsum,a[0],a[K-1]);
    }
    return 0;
}
```
# 1008：Elevator
没什么可说的，5分钟AC的题目。
此题代码如下：
```cpp
#include<iostream>

using namespace std;
int N;

int main(){
    cin >> N;
    int floor,temp,total;
    temp = total = 0;
    for(int n = 0;n < N;n++){
        cin >> floor;
        if(floor > temp)
            total += (floor-temp) * 6 + 5;
        else
            total += (temp-floor) * 4 + 5;
        temp = floor;
    }
    cout<<total;
    return 0;
}
```
# 1009：Product of Polynominals
这道题跟1002差不多，但是多了很多遍历和判断的过程，难度一般。
此题代码如下：
```cpp
#include<iostream>
#include<math.h>
using namespace std;
double a[1010],b[1010],f[2010];
int  main(){
	int  k1=0,k2=0,index,max1= -1,max2 = -1,cnt=0;

		cin>>k1;
		for(int i = 0; i < k1; i++){
			cin>>index;
			cin>>a[index];
			max1 = max(index,max1);  //最高次 
		}

		cin>>k2;
	
		for(int i = 0; i < k2;i++){
			cin>>index;
			cin>>b[index];
			max2 =max(index,max2);   //最高次 
		}
		//cout<<"max:"<<max1<<" "<<max2<<endl;
		for(int i = 0; i <= max1; i++){
			for(int j = 0; j <= max2; j++){
					int n = i+j;     //指数相加    
					if(a[i]*b[j]!=0)f[n] += a[i]*b[j];   //系数相乘 
				}
		}
    	int t = max1 + max2;
		for(int i = 0; i<=t;i++){
        	//cout<<i<<" "<<f[i]<<" "<<endl;
			if(f[i]!=0) cnt++;
        }
		cout<<cnt;
		for(int i = t; i >= 0; i--){
				if(f[i])  printf(" %d %.1f",i,f[i]);
		}

	return 0;
}

```
# 阶段性总结
这是我ECE/CS/AI的新的主题，我发现PAT(Advanced)难度的题目开始更多地需要做题人多去使用算法和数据结构方面的知识，整体难度相较于Basic上升较大，对于编程的基本功的要求也更高，所以**希望自己也能够从拼题上的PAT(Advanced)和浙大《数据结构》的课程题目和视频相结合**，对于提升编程、算法和数据结构的能力非常有帮助。毕竟纸上得来终觉浅，绝知此事要躬行嘛！
欢迎对ECE/CS/AI感兴趣的小伙伴关注我，如果你对我的内容有什么建议或想法的话，如果你对算法和数据结构感兴趣的话，也欢迎在评论区留下你的声音。
