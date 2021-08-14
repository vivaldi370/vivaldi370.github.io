#### **1487C Minimum Ties-找规律**

https://codeforces.com/problemset/problem/1487/C

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210805100352725.png" alt="image-20210805100352725" style="zoom: 80%;" />

这题是有$n$个球队，对于任意一对球队，如果他们平局，各得1分；否则，赢的队伍得3分，输的队伍得0分。要求尽量减少平局的数量，使得所有队伍的总分均相同。输入是样例$t$的个数+$n$，输出是$\frac{n(n-1)}{2}$​行的两两$x,y$比赛的结果。$x$赢了输出1，$y$赢了输出-1，否则输出0。

首先想到，如果所有球队的得分都相同，那么他们赢的场次，平局的场次都一样。一共打了$\frac{n(n-1)}{2}$场比赛，那么均摊到每个人，就是有$\frac{n-1}{2}$的场次。如果$n$为奇数，那么每人都赢那么多场，否则，嗯……试了一下$n=4$，果然大家全平局吧（。唔 这样应该可行，我试试。

然后就是关于怎么构造出答案了，试了一下$n=5$，果然顺序构造就可以了。开始写吧

啊 wa了。那应该是偶数出问题了吧，再想想$n=4$，唔，好像多出来的场次平局就好了。啊啊我傻了，平局各得1分而不是得0分orz。喜提2wa。“那么他们赢的场次，平局的场次都一样”这句话应该在偶数情况下是不对的，因为可能$x$赢了1场，而$y$​平局了3场，emmm不过这种情况……说不上来的感觉怪怪的。是不满足最少平局数量吗？总感觉这句话还是没毛病。继续考虑$n=4$，各赢一场，平局一场就ok了。因为每个队伍可以打$\frac{n-1}{2}$的比赛，那就是我少了一个$if$的判断，嗯，应该是$n$为偶数的时候再打一场平局就可以了，同样顺序输出，加上就AC了。

最近做题目的时候很容易走神，等神回来的时候就过去蛮长时间了orz。啊哈？tags里咋还有dfs？哦，题解是把平局=排成一个圈，我的办法相当于直接按照构造中的顺序排成了一个圈orz（所以dfs在哪里）

ac代码：

```c++
int n;
const int Max_n = 100 + 10;
int num[Max_n], aver[Max_n];

void init(){
	for (int i=1;i<=n;++i){
		num[i] = 0;
		aver[i] = 0;
	}
}

void solve(){
	scanf("%d",&n);
	init();
	int turns = (n-1)*n/2, win = 0;
	if (n%2 == 0){
		win = turns/n;
		for (int i=1;i<n;++i){
			for (int j=i+1;j<=n;++j){
				if (num[i]<win){
					num[i]++;
					printf(i==n-1 ? "1\n" : "1 ");
				}
				else if (aver[i]==0 && aver[j]==0){
					aver[i]++, aver[j]++;
					printf(i==n-1 ? "0\n" : "0 ");
				}
				else if (num[j]<win){
					num[j]++;
					printf(i==n-1 ? "-1\n" : "-1 ");
				}
			}
		}
		return;
	}
	win = (n-1)/2;
	for (int i=1;i<n;++i){
		for (int j=i+1;j<=n;++j){
			if (num[i]<win){
				num[i]++;
				printf(i==n-1 ? "1\n" : "1 ");
			}
			else{
				num[j]++;
				printf(i==n-1 ? "-1\n" : "-1 ");
			}
		}
	}
}
```



#### **1475C Ball in Berland-二分图**

https://codeforces.com/problemset/problem/1475/C

有女孩和男孩跳舞，有$a$个女生$b$个男生和$k$对舞伴，从中选2对在舞池跳舞，有几种选法，同一个人不能在一次选择中出场2次。呃，没思路，看了看样例，发现最朴素的想法，每个男孩有几种配对方式相乘然后减掉重复出场的女舞伴。复杂度肯定不行，pass。那就是假设男1选了女2，女2有的人的数量全部去掉，剩下的数量就是$<男1，女1>$对可以匹配的对的数量。唔，$O(N)$算法应该可以。试试，啊，ac了。这么简单的吗？所以到底哪里有图论了啊喂

啊哈，题目把它看成了一个二部图，一个男舞伴和女舞伴跳舞，就相当于在他们之间画上一条边，要求找到这个图中不相交的两条边有几种取法。设$deg(x)$为顶点$x$包含的边的数量，那么从第一条边$(a,b)$开始迭代，它将使得$deg(a)+deg(b)-1$条边被阻塞，无法相连，剩下的边即为可以继续连接的边。

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210805105600175.png" alt="image-20210805105600175" style="zoom:80%;" />

ac代码：

```c++
ll A,B,k;
const ll Max_n = 2e5 + 5;
ll a[Max_n],b[Max_n],numb[Max_n],numa[Max_n];

void init(){
	for (int i=1;i<=B;++i){
		numb[i] = 0;
	}
	for (int i=1;i<=A;++i){
		numa[i] = 0;
	}
}

void solve(){
	A = read(), B = read(), k = read();
	ll res = 0;
	init();
	for (int i=1;i<=k;++i){
		a[i] = read();
		numa[a[i]]++;
	}
	for (int i=1;i<=k;++i){
		b[i] = read();
		numb[b[i]]++;
	}
	for (int i=1;i<=k;++i){
		res = res + (k-numb[b[i]]-numa[a[i]]+1);
	}
	printf("%lld\n", res/2);
}
```



#### **1472C Long Jumps-dfs**

https://codeforces.com/problemset/problem/1472/C

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210805132612960.png" alt="image-20210805132612960" style="zoom:80%;" />

一个数组，可以从第$1-n$个的范围内开始，加上$a[i]$​的值，跳到数字总和大于$n$结束。一开始把数字可以到达的下一个数字连边，然后每条路径跑一遍找$max$​，然后超时啦！可恶我为啥会写这种看上去就会超时的算法啊。

换了一种思路，建个树，用dp的办法写，就像前几天做到的那题水坝一样。啊，AC了。这题我连了一个虚拟终点$n+1$，然后从终点往回递归找最大值。

```c++
int n;
const int Max_n = 2e5 + 10;
ll a[Max_n], dp[Max_n];
vector<ll> ve[Max_n];

void init(){
	for (int i=1;i<=n+1;++i){
		ve[i].clear();
		dp[i] = 0;
	}
}

ll dfs(ll inx){
	if (dp[inx]){
		return dp[inx];
	}
	int vlen = ve[inx].size();
	if (vlen == 0){
		return 0;
	}
	for (int i=0;i<vlen;i++){
		dp[inx] = max(dp[inx], dfs(ve[inx][i])+a[ve[inx][i]]);
	}
	return dp[inx];
}

void solve(){
	n = read();
	init();
	for (ll i=1;i<=n;++i){
		a[i] = read();
		if (a[i]+i <= n){
			ve[a[i]+i].push_back(i);
		}
		else{
			ve[n+1].push_back(i);
		}
	}
	dfs(n+1);
	printf("%lld\n", dp[n+1]);
}
```

看题解看题解ing。啊哈？题解在写啥0.0

题解：首先，设$score(i)$为选择了$i$作为初始位置的答案，然后假设我们从$j$开始，那么得到的$score(j)$一定大于$score(j+a[j])+a[j]$。唔姆，废话。然后计算所有的$score$存在数组中，从尾部开始遍历，然后在位置$i$我们会知道所有的$score(j)>score(i)$，利用上述公式，在一次操作中计算$score(i)$，然后选择所有$score(i)$中的最大值。

```c++
vector<int> dp(n);
  for (int i = n - 1; i >= 0; i--) {
    dp[i] = a[i];
    int j = i + a[i];
    if (j < n) {
      dp[i] += dp[j];
    }
  }
  cout << *max_element(dp.begin(), dp.end()) << endl;
```

哦哦，和我的意思差不多，只不过他没有虚拟的终点，直接找所有的$max(dp_i)$。



#### **1428B Belted Room-找环,思维**

https://codeforces.com/problemset/problem/1428/B

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210805145013183.png" alt="image-20210805145013183" style="zoom:80%;" />

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210805145029290.png" alt="image-20210805145029290" style="zoom:80%;" />

$n$个房间连成了一个类似环的东西，判断有多少个房间可以从它出发再回到这个房间（即一个环），一开始我没有考虑清楚，直接记录了每个顶点的入度和出度的差，然后如果它连了一条无向边就跳过，忽略了这种情况，然后wa on test 15

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210805145508659.png" alt="image-20210805145508659" style="zoom: 67%;" />

如图，2其实是到不了它自己的。然后直接删了if语句里面连了一条无向边就跳过，改成了直接判断出度入度的情况，然后理所当然的又wa了，还是test2。冷静了一下，然后直接跑2遍图，顺时针逆时针都来一下，不连通就输出无向边连着的顶点数，连通就输出$n$​，然后ac了。有时候人还是不能偷懒QAQ，复杂度不超过就算冗余一点也可以使劲造（。**这题，浪费了很多时间。**可以一遍过而我却懒得跑2遍图，导致花了一些时间。wa了一发之后没有仔细想想，而是直接删了一个语句直接交了，导致我吃了一发罚时。这么简单的题，不应该花时间的。

```c++
int n;
const int Max_n = 3e5 + 5;
bool cir[Max_n];
bool indeg[Max_n], outdeg[Max_n], edge[Max_n];
bool res1, res2;
 
void init(){
	for (int i=0;i<=n;++i){
		cir[i] = false;
		indeg[i] = false;
		outdeg[i] = false;
		edge[i] = false;
	}
	res1 = false, res2 = false;
}
void dfs(int inx, int step){
	if (step == n){
		res1 = true;
		return;
	}
	if (outdeg[inx] || edge[inx]){
		dfs((inx+1)%n, step+1);
	}

}
void antidfs(int inx, int step){
	if (step == n){
		res2 = true;
		return;
	} 
	if (indeg[inx] || edge[inx]){
		antidfs((inx-1+n)%n, step+1);
	}
}
void solve(){
	string s;
	cin >> n >> s;
	init();
	for (int i=0;i<n;++i){
		if (s[i] == '-'){
			cir[i] = true;
			cir[(i+1)%n] = true;
			edge[i] = true;
		}
		else if (s[i] == '>'){
			outdeg[i] = true;
			indeg[(i+1)%n] = true;
		}
		else{
			indeg[i] = true;
			outdeg[(i+1)%n] = true;
		}
	}
	int cirnum = 0;
	for (int i=0;i<n;++i){
		if (cir[i]){
			cirnum++;
		}
	}
	dfs(0, 0);
	antidfs(0, 0);
	if (res1 || res2){
		cout << n << endl;
	}
	else{
		cout << cirnum << endl;
	}
}
```

题解的思路和我一样，但是实现要更简洁orz。



#### **1529D-dp思维**

首先，假设长度$x\leq n$，此时只有所有的$pair$​长度都相等才满足要求。那么这种情况下，可以划分的串的数量为$n$的因子数量。

假设长度$x>n$​。以$n=2*4$​为例，那么长度可能是5, 6, 7。长度为5时，还剩2个点，相当于$n=1$的时候，答案是多少。当长度为6时，还剩4个点，相当于$n=2$​的时候，答案是多少。以此类推，设$n$的因子数量为$D(n)$，答案为$dp_n$，那么

$$dp_n = D(n) + \sum\limits_{i=1}^{n-1}dp_i$$

然后这题有个小问题，就是用$logn$​的复杂度求$n$​的因子数量。用$\sqrt{n}$​的复杂度会超时。题解是用了埃氏筛的思想，把2的倍数，3的倍数……一个一个加到$dp_i$​上。我这里是提前把所有的$dp_i$​设置成等于1，即都有1做因子。

```c++
int n;
const int Max_n = 1e6 + 10;
const ll MOD = 998244353;
ll sumd;
ll dp[Max_n];

void init(){
	for (int i=0;i<=n;++i){
		dp[i] = 1;
	}
}

void solve(){
	cin >> n;
	sumd = 0;
	init();
	for (int i=1;i<=n;++i){
		for (int j=i+i;j<=n;j+=i){
			dp[j]++;
		}
	}
	for (int i=1;i<=n;++i){
		dp[i] = (dp[i] + sumd) % MOD;
		sumd = (sumd + dp[i]) % MOD;
	}
	cout << dp[n] << endl;
}

int main(void){
	solve();
	return 0;
}
```

#### **1540A-负环贪心**

首先，假如给定的数组是 0 2 4 2，那么，应该构造一个最大的正边权，然后对于其他的边权，直接连负边。就像这样：

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210804201941625.png" alt="image-20210804201941625" style="zoom:50%;" />

然后每个点可以连到比它权值更小的点，就像这样。

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210804202543132.png" alt="image-20210804202543132" style="zoom:50%;" />

但是直接连，会超时，所以会想到排个序，然后用梯形公式。

但是我想了想，没用梯形公式，用了一个前缀和。

```c++
int n;
const int Max_n = 1e5 + 10;
ll a[Max_n],pre[Max_n];

void solve(){
	n = read();
	for (int i=1;i<=n;++i){
		a[i] = read();
	}
	sort(a+1, a+1+n);
	for (int i=1;i<=n;++i){
		pre[i] = pre[i-1] + a[i];
	}
	ll res = 0;
	for (int i=1;i<n;++i){
		res = res + (n-i)*a[i]-(pre[n]-pre[i]);
	}
	res += a[n];
	printf("%lld\n", res);
}
```

然后把今天学长告诉我的另外几题已经ac的题目的思路想了想，然后发了亿会呆。然后又想了想思路。然后又发了亿会呆。

然后在cf上开始开新题，练图论

#### 新题目 **1534C-连通块**

https://codeforces.com/problemset/problem/1534/C

![image-20210804213533939](C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210804213533939.png)

看了题目，题意是有两行全排列，可以交换任何一列的数字，要求每行每列的数字都不相同。问从一个初始可行解开始，共有几种解法。输入初始可行解，输出要求答案mod 1e9 + 7.

看完题面之后一瞬间：没有想法，大脑一片空白，然后看了一下样例，想到对于一个初始解，那么交换第$i$​​列，然后再交换引起冲突的那一列，直到冲突解决，就可以是一种解决方案。再进一步想到，可以检查如果只移动一列，会有多少列会被它带着动，就相当于做检查连通块。假如连通块数量是k， 然后方案数量就是$2^k$​，嗯，样例也能对得上，动手写一写试试。

啊写的时候发现要搞一下矩阵快速幂，嗯，搞好了，通过样例了，看起来没问题，数据范围没问题，很好，交！AC了wwww

啊好顺利的AC啊，有点不敢相信0.0，我能这么快做完Div2的C题的吗？看看题解思路ing

唔 题解用的dfs找环的数量 唔 差不多，下一题

这是代码

```c++
int n;
const int Max_n = 4e5 + 10;
int a[2][Max_n], rt[Max_n];
const ll MOD = 1e9 + 7;

int findx(int x){
	if (x==rt[x]){
		return x;
	}
	rt[x] = findx(rt[x]);
	return rt[x];
}
void merge(int x, int y){
	int fx = findx(x), fy = findx(y);
	rt[fy] = fx;
}
void init(){
	for (int i=1;i<=n;++i){
		rt[i] = i;
	}
}
ll power(ll a, ll k){
    ll ans = 1;
    while (k){
        if (k % 2) ans = (ans*a)%MOD;
        a = (a*a) % MOD;
        k = k/2;
    }
    return ans;
}
void solve(){
	n = read();
	init();
	for (int i=1;i<=n;++i){
		a[0][i] = read();
	}
	ll cnt = n;
	for (int i=1;i<=n;++i){
		a[1][i] = read();
		int fx = findx(a[1][i]), fy = findx(a[0][i]);
		if (fx != fy){
			merge(a[1][i], a[0][i]);
			cnt--;
		}
	}
	ll res = power(2,cnt) % MOD;
	printf("%lld\n", res);
}
```



#### **1510K King's Task-找规律**

https://codeforces.com/problemset/problem/1510/K

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210804214604353.png" alt="image-20210804214604353" style="zoom:67%;" />

给了一个$2\times n$​的排列，可以进行2种操作。1操作：交换1,2的位置，3,4的位置……2操作：交换1，n+1的位置，2，n+2的位置……输入是这个全排列，输出是最少的操作数量，通过这两种操作使得这个排列排好序。

看完题目一瞬间：没啥思路，先想想如果不行是什么情况吧。感觉好像如果1,3,5,7的奇偶性不同（或者说2,4,6,8的奇偶性不同），那应该就是-1吧。因为无论咋样都没办法改变个别下标的奇偶性。然后试了1432，发现也不行，然后又想到是不是如果$a[i]=i$，但是此时其他不是正确排序就不行。因为改变是整体改变的，当一个放在那不动的时候就不能把其他排列好。继而又想到是不是可以直接随便挑一个数字，看它被排列到正确位置的时候其他数字是否能被正确排序变成sort后的样子，如果能的话输出次数，不能输出-1。嗯……试一试。

准备开始写的时候又发现其实显然不能重复操作2遍同样操作，否则相当于啥也没做。而且最多也就是交换$2n$​次，一个数就可以遍历所有位置了0.0。那就做一个121212……的操作，再做2121……的操作，取min。反正做两遍也不会超时0.0这样保险orz（遇事不决min）

啊 竟然也对了 emmmm惊讶，这两题咋都一发过了啊。这一题我为了学习C语言，用指针写的orz，语法苦手的倔强。

```c++
int n;
const int Max_n = 1000 + 10;
const int INF = 0x3f3f3f3f;
int a[Max_n<<1], b[Max_n<<1];

void op1(int *A){
	for (int i=1;i<=n+n-1;i+=2){
		swap(*(A+i), *(A+i+1));
	}
}
void op2(int *A){
	for (int i=1;i<=n;++i){
		swap(*(A+i), *(A+i+n));
	}
}
int findAns(int *A, bool flag){
	int tmp = *(A+1), num = 0;
	while (tmp != *(A+tmp) && num<=2*n){
		if (flag){
			op1(A);
		}
		else{
			op2(A);
		}
		num++;
		flag = flag^true;
	}
	if (tmp != *(A+tmp)){
		return INF;
	}
	return num;
}
void check(int *A, int num){
	for (int i=1;i<=n+n;++i){
		if (*(A+i) != i){
			printf("-1\n");
			return;
		}
	}
	printf("%d\n", num);
}
void solve(){
	scanf("%d",&n);
	int cnt = 0;
	for (int i=1;i<=n+n;++i){
		scanf("%d",&a[i]);
		b[i] = a[i];
		if (a[i] == i){
			cnt++;
		}
	}
	if (cnt == n+n){
		printf("0\n");
		return;
	}
	if (cnt > 0){
		printf("-1\n");
		return;
	}

	int res1 = findAns(a, true);
	int res2 = findAns(b, false);
	
	if (res1 > res2){
		check(b, res2);
		return;
	}
	check(a, res1);
}
```

但是，但是图论在哪里捏 看看题解www

啊啊，原来$max(2n,4)$​的操作就能找到排好序的序列了诶，不过差不多，复杂度是够的，这里我想到2n就停止了QAQ。下次比完可以多想想。

#### **1476C Longest Simple Cycle-(实际可以算DP)**

https://codeforces.com/contest/1476/problem/C

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210806165449807.png" alt="image-20210806165449807" style="zoom:80%;" />

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210806165503389.png" alt="image-20210806165503389" style="zoom: 50%;" />

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210806165546961.png" alt="image-20210806165546961" style="zoom:80%;" />

这道题，emmmm，dp没d出来，然后看题解之后开始自己写，结果一直对不上样例，后来发现是一开始的时候输入写错了，改完就对了（调了一上午）

分析：

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210806165822193.png" alt="image-20210806165822193" style="zoom:80%;" />

首先，每一个环都有一条链作为它的右半部分，最大的环也是如此。那么，这题的答案就是线性DP，枚举所有的环作为右半部分的情况，然后找出最大的环的顶点数是多少。

对于某一条链$c_i$，设以它为右半部分的环的最大值为$maxlen_i$，它的连接有两种情况。

* $a_i = b_i$，此时$c_i$​​​和左边的一个点组成一个环，此时答案为$c_i+1$
* $a_i \neq b_i$，此时又分为两种情况
  * $c_i$​和左边的链组成环，此时答案为$c_i+|a_i-b_i|+1$
  * $c_i$​和更左边的若干链组成环，要注意的一点是，它的长度为$c_{i-1}$​​​​为右半部分的环和$c_i$​​合并。此时答案为$c_i+maxlen_{i-1}-(|a_i-b_i|-1)$​

<img src="C:\Users\KZ\AppData\Roaming\Typora\typora-user-images\image-20210806183302198.png" alt="image-20210806183302198" style="zoom: 50%;" />

AC代码：

```c++
int n;
const int Max_n = 1e5 + 10;
ll a[Max_n], b[Max_n], c[Max_n], maxlen[Max_n];

void solve(){
	n = read();
	for (int i=1;i<=n;++i){c[i] = read();}
	for (int i=1;i<=n;++i){a[i] = read();}
	for (int i=1;i<=n;++i){b[i] = read();}
	ll cur = 0, ans = 0;
	for (int i=2;i<=n;++i){
		maxlen[i] = c[i] + abs(a[i]-b[i]) + 1;
		if (a[i] != b[i]){
			maxlen[i] = max(maxlen[i], c[i]+maxlen[i-1]-abs(a[i]-b[i])+1);
		}
		ans = max(ans, maxlen[i]);
	}
	printf("%lld\n", ans);
}
```

