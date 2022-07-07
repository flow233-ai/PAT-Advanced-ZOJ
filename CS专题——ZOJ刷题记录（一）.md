# 1001：A+B Problem
考察知识点：简单加减法计算
代码如下：
```cpp
#include<iostream>

using namespace std;

int main(){
    int n1,n2;
    while(cin >> n1 >> n2)
        cout<<n1+n2<<endl;
    return 0;
}
```
# 1002：Fire Net
考察知识点：递归回溯（类似于八皇后）
代码如下：
```cpp
#include<iostream>

using namespace std;

int map[4][4];
char c;
int N,mini;

int Findmax();
void Set(int ind);
bool Check(int row,int col);

int main(){
    while(true){
        cin >> N;
        if(N == 0)
            break;
        for(int i = 0;i < N;i++){
            for(int j = 0;j < N;j++)
                map[i][j] = -1;
        }
        for(int i = 0;i < N;i++){
            for(int j = 0;j < N;j++){
                cin >> c;
                if(c == 'X')
                    map[i][j] = -2;
            }
        }
        cout<<Findmax()<<endl;
    }
}

int Findmax(){
    mini = -1;
    for(int i = 0;i < N*N;i++){
        int ro = i / N;
        int co = i % N;
        if(Check(ro,co) == true)
            Set(i);
        if(i == N*N-1){
            int num = 0;
            for(int i = 0;i < N;i++){
                for(int j = 0;j < N;j++){
                    if(i*N+j == map[i][j])
                        num++;
                }
            }
            if(mini == -1)
                mini = num;
            else if(num > mini)
                mini = num;
        }
    }
    return mini;
}

void Set(int ind){
    int ro = ind / N;
    int co = ind % N;
    map[ro][co] = ind;
    for(int i = ro+1;i < N;i++){
        if(Check(i,co) == true)
            map[i][co] = ind;
        if(map[i][co] == -2)
            break;
    }
    for(int i = co+1;i < N;i++){
        if(Check(ro,i) == true)
            map[ro][i] = ind;
        if(map[ro][i] == -2)
            break;
    }
    for(int i = ind;i < N*N;i++){
        int ro = i / N;
        int co = i % N;
        if(Check(ro,co) == true)
            Set(i);
        if(i == N*N-1){
            int num = 0;
            for(int i = 0;i < N;i++){
                for(int j = 0;j < N;j++){
                    if(i*N+j == map[i][j])
                        num++;
                }
            }
            if(mini == -1)
                mini = num;
            else if(num > mini)
                mini = num;
        }
    }
    map[ro][co] = -1;
}

bool Check(int row,int col){
    if(map[row][col] == -1)
        return true;
    else if(map[row][col] == -2)
        return false;
    else{
        int tempr = map[row][col] / N;
        int tempc = map[row][col] % N;
        if(map[tempr][tempc] == map[row][col])
            return false;
        else
            return true;
    }
}
```
# 1003：Crashing Balloons
考察知识点：动态规划
代码如下：
```cpp
#include<iostream>
#include<cmath>

using namespace std;
long int N1,N2;
int num[100];
int flag = 0;

void Check(long int n);
long int IsPrime(long int n);

int main(){
    while(cin >> N1){
        for(int i = 0;i < 100;i++)
            num[i] = 0;
        cin >> N2;
        flag = 0;
        Check(N2);
        if(flag == 1){
            cout<<N1<<endl;
            continue;
        }
        Check(N1);
        if(flag == 1){
            cout<<N2<<endl;
            continue;
        }
        else
            cout<<N1<<endl;
    }
    return 0;
}

void Check(long int n){
    long int temp;
    if(n <= 100 && n > 0){
        if(num[n-1] == 0){
            num[n-1] --;
            return;
        }
    }
    temp = IsPrime(n);
    //cout<<temp<<endl;
    if(temp == 1){
        flag = 1;
        return;
    }
    else{
        if(temp <= 100){
            num[temp-1] --;
            Check(n / temp);
        }
        else
            flag = 1;
    }
}

long int IsPrime(long int n){
	if(n <= 1)
		return 1;
    for (long int i = 2; i <sqrt(n); i++){
        if ((n % i) == 0 && num[i-1] == 0)
            return i;
    }
    return 1;
}

```
# 1006：Do the Untwist
考察知识点：查表翻译
代码如下：
```cpp
#include<iostream>
#include<string>

using namespace std;
int k;
int cipherc[70],plainc[70];
string cipher;
string plain;

int main(){
    while(true){
        cin >> k;
        if(k == 0)
            break;
        cin >> cipher;
        for(int i = 0;i < cipher.size();i++){
            if((int)cipher[i] - 96 >= 1 && (int)cipher[i] - 96 <= 26)
                cipherc[i] = (int)cipher[i] - 96;
            else if((int)cipher[i] == 95)
                cipherc[i] = 0;
            else
                cipherc[i] = 27;
        }
        int index,num;
        for(int i = 0;i < cipher.size();i++){
            index = (k * i) % (int)(cipher.size());
            num = cipherc[i] + i;
            num = num % 28;
            plainc[index] = num;
        }
        string plain = "";
        for(int i = 0;i < cipher.size();i++){
            if(plainc[i] == 0)
                plain += '_';
            else if(plainc[i] == 27)
                plain += '.';
            else
                plain += plainc[i] - 1 + 'a';
        }
        cout<<plain<<endl;
    }
    return 0;
}
```
