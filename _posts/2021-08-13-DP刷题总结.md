### 1551E Fixed Points

https://codeforces.com/problemset/problem/1551/E

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210811121240202.png" alt="image-20210811121240202" style="zoom:80%;" />

状态：设$dp[i][j]$​表示到第$i$​个元素为止，保留下来（没有删除的数量）为$j$​​，此时元素值等于下标的数量。

首先，把数组置为0。然后，将$i$从$0-n$循环，$j$从$0-i$​循环。对于元素$a_{i+1}$，我们考虑删除或者不删除它。

$dp[i+1][j]=max(dp[i+1][j],\ dp[i][j])$

$dp[i+1][j+1]=max(dp[i+1][j+1],\ dp[i][j]+(a_{i+1}=j+1))$​​

如果我们删除了$a_{i+1}$​​，那么元素值等于下标的数量不会增加，并且没有被删除的元素数量也不会增加。这意味着$dp[i+1][j]$可以用$dp[i][j]$​来更新。因为我们要求的是没有删除的元素数量最大（即删除的元素数量最小），所以我们只在$dp[i][j]>dp[i+1][j]$的时候更新。

假设我们不删除$a_{i+1}$，那么现在到$i+1$为止，我们就有了$j+1$个不被删除的元素，$j$就变成了$j+1$​，现在要更新的答案就是$dp[i+1][j+1]$。当$a_{i+1}=j+1$时，也就是新加入的这个元素的值等于目前的下标$j+1$，$dp[i+1][j+1]=dp[i][j] + 1$，如果不相等，那么$dp[i+1][j+1]=dp[i][j]$。

现在我们来构造答案，我们需要删除最少的元素（保留最多的元素不被删除），使得元素值等于下标的数量至少为$k$​​。$\forall dp[i][j], dp[i+1][j]\geq dp[i][j]$​，即到$n$​为止，统计出来的元素值等于下标的数量肯定比其他的都多。那么让$j$​​从$n$​到$0$​​循环，被删除的元素数量就是$n-j$​​，当我们发现$dp[n][j]\geq k$​时，循环终止，输出答案，否则输出-1。

AC代码：

```c++
void init(){
	for (int i=0;i<=n;++i){
		for (int j=0;j<=n;++j){
			dp[i][j] = 0;
		}
	}
}

void solve(){
	n = read(), k = read();
	for (int i=1;i<=n;++i){
		a[i] = read();
	}
	init();
	for (int i=0;i<n;++i){
		for (int j=0;j<=i;++j){
			dp[i+1][j] = max(dp[i+1][j], dp[i][j]);
			dp[i+1][j+1] = max(dp[i+1][j+1], dp[i][j]+(a[i+1]==j+1 ? 1 : 0));
		}
	}
	for (int j=n;j>=0;--j){
		if (dp[n][j] >= k){
			cout << n-j << endl;
			return;
		}
	}
	cout << -1 << endl;
} 
```



### 1547E Air Conditioners

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812225332614.png" alt="image-20210812225332614" style="zoom:80%;" />

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812225359507.png" alt="image-20210812225359507" style="zoom:80%;" />

首先我们可以发现，温度是随着距离空调的远近递增的。那么其实空调在哪都无所谓，我们只要关注每个位置的温度来依次更新就可以了。空调可以向左或者向右发出冷气，那么就从前向后更新一次，再从后向前更新一次。

AC代码：

```c++
int n,k;
const int Max_n = 3e5 + 10;
ll a[Max_n], t[Max_n], dp[Max_n];
const ll INF = 0x3f3f3f3f3f3f3f3f;
 
void init(){
	for (int i=0;i<=n+1;++i){
		dp[i] = INF;
	}
}
void solve(){
	n = read(), k = read();
	init();
	for (int i=1;i<=k;++i){
		a[i] = read();
	}
	for (int i=1;i<=k;++i){
		t[i] = read();
		dp[a[i]] = t[i];
	}
	for (int i=1;i<=n;++i){
		dp[i] = min(dp[i-1]+1, dp[i]);
	}
	for (int i=n;i>=1;--i){
		dp[i] = min(dp[i+1]+1, dp[i]);
	}
	for (int i=1;i<=n;++i){
		printf(i==n?"%d\n":"%d ", dp[i]);
	}
}
```



### 1537E1&E2 Erase and Extend

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812230118024.png" alt="image-20210812230118024" style="zoom:80%;" />

有一个字符串$s$，可以对它进行2种操作任意次：1. 删掉最后一个字符；2. 复制这个字符串，即$s+=s$。通过上述两种操作，找到满足长度为$k$​的，字典序最小的字符串。

我的思路：要是$s[i]<s[0]$，那没啥好说的，加上肯定更优，直接加。当$s[i]=s[0]$时，那就将$s[i]$后面的元素$s[i+1],...$和$s[1],s[2],..,s[i-1]$​​​比较，要是后面的元素大于前面元素，那构造直接结束了，如果比到$s[i-1]$都没结束的话，一切重新开始，拿新的$s[i]$和$s[0]$比较。需要注意的是，如果$s[slen-1]=s[0]$，构造直接结束，因为加上$s[slen-1]$​一定不会有正收益。如果比到中间结束了，那么就加上前面那部分，后面的不加了，就这样结束。

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812231118358.png" alt="image-20210812231118358" style="zoom:67%;" /><img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812231832702.png" alt="image-20210812231832702" style="zoom:67%;" />

AC代码：

```c++
int n,k;
string s;
string res;

void solve(){
	cin >> n >> k;
	cin >> s;
	int slen = s.size();
	res.clear();
	res += s[0];
	for (int i=1;i<slen;++i){
		if (s[i] > s[0]){
			return;
		}
		else if (s[i] == s[0]){
			if (i==slen-1){
				return;
			}
			string sub;
			for (int j=1;j<i&&i+1<slen;++j){
				if (s[j] == s[i+1]){
					if (s[i+1]==s[0] && i+1==slen-1){
						return;
					}
					sub += s[i+1];
					i++;
				}
				else if (s[j] > s[i+1]){
					break;
				}
				else{
					return;
				}
			}
			res += s[0];
			res += sub;
		}
		else{
			res += s[i];
		}
	}
}
 
int main(void){
	std::ios::sync_with_stdio(false);
	cin.tie(0);
	cout.tie(0);
	solve();
	int rlen = res.size();
	for (int i=0;i<k;++i){
		cout << res[i%rlen];
	}
	cout << endl;
	
	return 0;
}
```



### 1536C Diluc and Keaya

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812233336164.png" alt="image-20210812233336164" style="zoom:80%;" />

一个字符串$s$​，只由D和K构成。要求每一块的D和K的比例相同，问最多可以分成多少块。首先，假如每一块，$num(D):num(K)=1:2$​​，那么总的比例也会是1:2。所以当扫描到$s[i]$的时候，看一下到了第$i$块的时候，比例是多少，假设为$x:y$在$map$里寻找上一个$x:y$的时候可以分成多少块，假设为$a$块，那么$res[i]=a+1$。

AC代码如下：

```c++
int n,k;
const int Max_n = 5e5 + 10;
vector<int> ve[Max_n];
char s[Max_n];
int dp[Max_n], pred[Max_n], prek[Max_n];
map<pii,int> ma;
 
void init(){
	ma.clear();
}
 
void solve(){
	cin >> n >> s+1;
	init();
	for (int i=1;i<=n;++i){
		pred[i] = pred[i-1] + (s[i]=='D');
		prek[i] = prek[i-1] + (s[i]=='K');
	}
	for (int i=1;i<=n;++i){
		int tmp = gcd(pred[i],prek[i]), nd = pred[i]/tmp, nk = prek[i]/tmp;
		if (ma.count(pii(nd,nk))==0){
			ma[pii(nd,nk)] = 1;
		}
		else{
			ma[pii(nd,nk)]++;
		}
		dp[i] = ma[pii(nd,nk)];
	}
	for (int i=1;i<=n;++i){
		cout << dp[i] << (i==n?'\n':' ');
	}
}
```



### 1535D Playoff Tournament

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812234024857.png" alt="image-20210812234024857" style="zoom:80%;" />

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812234047203.png" alt="image-20210812234047203" style="zoom:80%;" />

$2^k$个人，一共进行$2^k-1$​次比赛，给了字符串$s$​，记录了$2^k-1$​​​次比赛的结果，然后进行$q$​次修改+查询，问每次修改相应值之后，可能获胜的人数为多少。

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812235037792.png" alt="image-20210812235037792" style="zoom:67%;" />![image-20210812235657078](C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210812235657078.png)

我的想法是建一棵树，给每次比赛标号为$dp[i]$，然后序号小的那一个的结果就是它的左孩子，大的那个就是它的右孩子，它又是根的结果的基础。

$$dp_i = \begin{cases} dp_i.le, &s_i='0' \\ dp_i.ri, &s_i='1' \\ dp_i.le + dp_i.ri, &s_i='?'\end{cases}$$​

这样去建立树然后更新就好啦！（感觉建树是最麻烦的）

AC代码：

```c++
int k, q, n;
const int Max_n = 3e5 + 10;
char s[Max_n];
struct node{
	int val;int le;int ri;int rt;
};
node dp[Max_n];
 
void build(int inx, int part){
	if (part == 0){
		return;
	}
	int cnt = inx - (part<<1);
	for (int i=inx;i<part+inx;++i){
		dp[i].le = cnt;
		dp[cnt].rt = i; cnt++;
		dp[i].ri=cnt;
		dp[cnt].rt = i; cnt++;
		if (s[i]=='1'){
			dp[i].val = dp[dp[i].ri].val;
		}
		else if (s[i]=='0'){
			dp[i].val = dp[dp[i].le].val;
		}
		else{
			dp[i].val = dp[dp[i].le].val + dp[dp[i].ri].val;
		}
	} 
	build(inx+part, part/2);
}
 
void init(){
	n = (1<<k);
	dp[0].val = 1;
	for (int i=1;i<=n/2;++i){
		if (s[i] == '?'){
			dp[i].val = 2;
		}
		else{
			dp[i].val = 1;
		}
	}
	build(n/2+1, n/4);
}
 
void update(int inx){
	if (inx == 0){
		return;
	}
	if (s[inx] == '0'){
		dp[inx].val = dp[dp[inx].le].val;
	}
	else if (s[inx] == '1'){
		dp[inx].val = dp[dp[inx].ri].val;
	}
	else{
		dp[inx].val = dp[dp[inx].le].val + dp[dp[inx].ri].val;
	}
	update(dp[inx].rt);
}
 
void solve(){
	cin >> k >> s+1;
	init();
	cin >> q;
	int inx; char ch;
	for (int i=1;i<=q;++i){
		cin >> inx >> ch;
		s[inx] = ch;
		update(inx);
		cout << dp[n-1].val << endl;
	}
}
```

题解反转了$s$​，代码变得好简洁QAQ，不过核心思想没变，推导公式也没变，下次注意灵活处理ww

```c++
int main() {
  ios_base::sync_with_stdio(false);
  cin.tie(NULL); 
  
  int k;
  cin >> k;
  string s;
  cin >> s;
  reverse(s.begin(), s.end());
  
  int n = 1 << k;
  vector<int> cnt(2 * n, 1);
  
  auto upd = [&](int i) {
    return cnt[i] = (s[i] != '0' ? cnt[i * 2 + 1] : 0) + (s[i] != '1' ? cnt[i * 2 + 2] : 0);
  };
  
  for (int i = n - 2; i >= 0; i--) {
    upd(i);
  }
  
  int q;
  cin >> q;
  while (q--) {
    int p;
    char c;
    cin >> p >> c;
    p = n - p - 1;
    s[p] = c;
    while (p) {
      upd(p);
      p = (p - 1) / 2;
    }
    cout << upd(0) << '\n';
  }
}
```

