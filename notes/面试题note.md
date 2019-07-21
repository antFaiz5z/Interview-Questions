##### linux下查DNS

```shell
$> cat /etc/resolv.conf
nameserver  172.16.0.250
```

  

### 数据结构和算法

#### 排序算法

| 算法   | 时间复杂度                 | 稳定性  |
| ---- | --------------------- | ---- |
| 冒泡排序 | O(n2),最好情况O(n)        | 稳定   |
| 选择排序 | O(n2)                 | 不稳定  |
| 插入排序 | O(n2)，最好情况O(n)        | 稳定   |
| 希尔排序 | O(n1.5),最好情况O(nlog2n) | 不稳定  |
| 基数排序 |                       | 稳定   |
| 归并排序 | O(nlogn)              | 稳定   |
| 堆排序  | O(nlogn)              | 不稳定  |
| 快速排序 | O(nlogn)              | 不稳定  |

设待排序列为n个记录，d个关键码，关键码的取值范围为radix，则进行链式基数排序的[时间复杂度](https://baike.baidu.com/item/%E6%97%B6%E9%97%B4%E5%A4%8D%E6%9D%82%E5%BA%A6)为O(d(n+radix))

##### 谈谈排序算法的改进

#### 分治和动态规划有什么异同

都是把一个大问题划分为多个子问题，分治的话子问题之间相互独立。动态规划的话，子问题之间存在交集，不相互独立。先求最优子结构，然后自顶向下或者自下向顶的求出结果。

##### 汉诺塔问题

#### 冒泡排序

```c++
void sort(int *a,int n){
    for(int i=n-1;i>0;--i){
        for(int j=0;j<i;++j)
        {
            if(a[j]>a[j+1]){
                swap(a[j],a[j+1]);
            }
        }
    }
}
```

#### 选择排序

```c++
void sort(int *a,int n){
    for(int i=0;i<n-1;++i){
      	int min=i;
        for(int j=i+1;j<n;++j){
            if(a[j]<a[min]){
                min=j;
            }
        }
      if(min!=i){
          swap(a[min],a[i]);
      }
    }
}
```

##### 动态规划的原理

#### 用两个队列实现一个栈

```c++
class Stack {
public:
    // Push element x onto stack.
    void push(int x) {
        if (!q1.empty())
        {
            q2.push(x);
            while (!q1.empty())
            {
                q2.push(q1.front());
                q1.pop();
            }
        }
        else
        {
            q1.push(x);
            while (!q2.empty())
            {
                q1.push(q2.front());
                q2.pop();
            }
        }
    }

    // Removes the element on top of the stack.
    void pop() {
        if (q1.empty()&&q2.empty())
            throw new exception("stack is empty");
        else if (!q1.empty()) q1.pop();
        else q2.pop();
    }

    // Get the top element.
    int top() {
        if (!q1.empty()) return q1.front();
        else return q2.front();
    }

    // Return whether the stack is empty.
    bool empty() {
        return (q1.empty()&&q2.empty());
    }

private:
    queue<int> q1, q2;
};
```



#### 求数组中的逆序对

归并排序的方法

#### K路链表的归并

#### LRU CACHE

```C++
class LRUCache {
public:
    LRUCache(int capacity) {
        size=capacity;
    }
    
    int get(int key) {
        if(hashtable.find(key)!=hashtable.end()){
            auto node=hashtable[key];
            l.splice(l.begin(),l,node);
            hashtable[key]=l.begin();
            return (*node)->value;
        }else{
            return -1;
        }      
    }
    
    void put(int key, int value) {
        if(hashtable.find(key)==hashtable.end()){
            if(l.size()==size){
                auto it=l.back();
                l.pop_back();
                hashtable.erase(it->key);
                delete it;
            }
            cacheNode* node=new cacheNode(key,value);
            l.push_front(node);
            hashtable[key]=l.begin();
        }else{
            auto it=hashtable[key];
            (*it)->value=value;
            l.splice(l.begin(),l,it);
            hashtable[key]=l.begin();
        }
            
    }
private:
    struct cacheNode{
        int key;
        int value;
        cacheNode(int x,int y):key(x),value(y){
            
        }
    };
    int size;
    typedef list<cacheNode*>::iterator listIter;
    list<cacheNode*> l;
    unordered_map<int,listIter> hashtable;
};
```



##### 数字转换机

```c++
#include <iostream>
using namespace std;

int main(){
	int a, b, A, B;
	cin >> a >> b >> A >> B;
	if (!((A / a == B / b) && (A % a == B % b))){
		cout << -1;
		return 0;
	}

	int count = 0;
	int temp = A;
	while (temp > a){
		if (1 == temp % 2){   //奇数
			temp--;
			count++;
		}
		else if (temp / 2 > a){   //偶数
			temp /= 2;
			count++;
		}
		else{
			count += (temp - a);
			break;
		}
	}

	cout << count << endl;

	return 0;
}

```



##### 归并排序

```c++
void merge(int *a,int begin,int mid,int end,int *b){
    int i=begin,j=mid+1,k=begin;
  	while(i<=mid&&j<=end){
        if(a[i]<a[j]){
            b[k]=a[i];
            k++,i++;
        }else{
            b[k]=a[j];
          	k++,j++;
        }
    }
  	while(i<=mid){
        b[k]=a[i];
        k++,i++;
    }
    while(j<=end){
        b[k]=a[j];
        k++,j++;
    }
  	for(int index=begin;index<=end;++index){
        a[index]=b[index];
    }
}
void mergeSort(int *a,int begin,int end,int *b){
  	if(a ==NULL || b == NULL)
      return;
    if(begin<end){
        int mid=(begin+end)/2;
      	mergeSort(a,begin,mid,b);
      	mergeSort(a,mid+1,end,b);
      	merge(a,begin,mid,end,b);
    }
}
```

##### 归并非递归

```c++
void Merge(int arr[], int tmp[], int left, int mid, int right)  
{  
        int i = left;  
        int j = mid+1;  
        int idt = left;  
       
        while(i <= mid && j <= right)  // 合并两个序列 按升序  
        {  
            if (arr[i] <= arr[j])  
            {  
                tmp[idt++] = arr[i++];  
            }  
            else  
            {  
                tmp[idt++] = arr[j++];  
            }  
        }  
       
        while(i <= mid)  // 合并剩余序列  
        {  
            tmp[idt++] = arr[i++];  
        }  
       
        while(j <= right)// 合并剩余序列  
        {  
            tmp[idt++] = arr[j++];  
        }  
}  
  
void MergePass(int arr[], int tmp[], int step, int n)  
{  
    int i = 0;  
    while (n - i >= 2 * step)  
    {   // 将两两相邻的有序子序列合并成一个2倍长度的子序列  
        Merge(arr, tmp, i, i+step-1, i+2*step-1);  
        i = i + 2 * step;  
    }  
  
    // 若剩余序列超过一个序列长度将再次合并不足两个序列长度的两个序列  
    if (n - i > step)      
        Merge(arr, tmp, i, i+step-1, n-1);  
    else   // 将剩下来的不大于一个序列长度的剩余元素并入  
        for (int j = i; j < n; j++)  
            tmp[j] = arr[j];  
      
}  
  
void MergeSort(int arr[], int n)  
{  
    int step = 1;  
    int* tmp = new int[n];  // 申请额外的辅助空间  
      
    while(step < n)  
    {     
        // 将arr的元素按照step归并到tmp  
        MergePass(arr, tmp, step, n);  
        step *= 2;  
        // 将tmp的元素按照新step归并到arr  
        MergePass(tmp, arr, step, n);  
        step *= 2;    
    }  
  
    delete tmp;  
}  
```

##### 堆排序

```c++
#include <iostream>
#include <vector>
#include <iterator>
#include <iostream>
using namespace std;

void adjust_heap(vector<int>& vec,int i,int len){
	int left=2*i+1;
	int right=2*i+2;
	int max=i;
	while(1){
		if(left<len&&vec[left]>vec[max]){
			max=left;
		}
		if(right<len&&vec[right]>vec[max]){
			max=right;
		}
		if(max!=i){
			swap(vec[max],vec[i]);
			i=max;
			left=2*i+1;
			right=2*i+2; 
		}else{
			break;
		}
	}	
}

void make_heap(vector<int>& vec){
	int len=vec.size();
	for(int i=len/2-1;i>=0;--i){
		adjust_heap(vec,i,len);
	}
}

void heap_sort(vector<int>& vec){
	int len=vec.size();
	while(len>1){
		swap(vec[0],vec[len-1]);
		len--;
		adjust_heap(vec,0,len);
	}
}

int main()
{
	vector<int> vec;
	int input;
	while(cin>>input){
		vec.push_back(input);
	}
	
	make_heap(vec);
	heap_sort(vec);
	ostream_iterator<int> iter(cout," ");
	copy(vec.begin(),vec.end(),iter);
	cout<<endl;
	return 0;	
} 
```

##### 链表归并排序

```c++
class Solution {
public:
    ListNode *sortList(ListNode *head) {
        if(head == NULL)
            return head;
        return MergeSort(head);
        
    }
    
    ListNode* Merge(ListNode* lhead,ListNode* rhead){
        ListNode* head;
        if(lhead == NULL)
            return rhead;
        if(rhead == NULL)
            return lhead;
        if(lhead->val<=rhead->val){
            head=lhead;
            head->next=Merge(lhead->next,rhead);
        }else{
            head=rhead;
            head->next=Merge(lhead,rhead->next);
        }
        return head;
        
    }
    
   ListNode* MergeSort(ListNode* head){
       if(head->next == NULL)
           return head;
        ListNode* slow=head,*fast=head,*pre=NULL;
        while(slow!=NULL&&fast!=NULL){
            pre=slow;
            slow=slow->next;
            fast=fast->next;
            if(fast)
                fast=fast->next;
        }
        pre->next=NULL;
        ListNode* lhead=head,*rhead=slow;
        lhead=MergeSort(lhead);
        rhead=MergeSort(rhead);
        return Merge(lhead,rhead);
    } 
};
```

#### 将有序数组转换成二叉排序树

```c++
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        if(nums.empty())
            return NULL;
        int low=0,high=nums.size()-1;
        return subFunc(nums,low,high);
    }
private:
    TreeNode* subFunc(vector<int>& nums,int low,int high)
    {
        if(low<=high){
            int mid=(low+high)>>1;
            TreeNode* root=new TreeNode(nums[mid]);
            root->left=subFunc(nums,low,mid-1);
            root->right=subFunc(nums,mid+1,high);
            return root;
        }else{
            return NULL;
        }   
        
    }
};
```

#### 求链表的中间结点

快慢指针

##### 最短路径算法

```c++
/***************************************
* About:    有向图的Dijkstra算法实现
* Author:   Tanky Woo
* Blog:     www.WuTianQi.com
***************************************/
 
#include <iostream>
using namespace std;
 
const int maxnum = 100;
const int maxint = 999999;
 
// 各数组都从下标1开始
int dist[maxnum];     // 表示当前点到源点的最短路径长度
int prev[maxnum];     // 记录当前点的前一个结点
int c[maxnum][maxnum];   // 记录图的两点间路径长度
int n, line;             // 图的结点数和路径数
 
// n -- n nodes
// v -- the source node
// dist[] -- the distance from the ith node to the source node
// prev[] -- the previous node of the ith node
// c[][] -- every two nodes' distance
void Dijkstra(int n, int v, int *dist, int *prev, int c[maxnum][maxnum])
{
	bool s[maxnum];    // 判断是否已存入该点到S集合中
	for(int i=1; i<=n; ++i)
	{
		dist[i] = c[v][i];
		s[i] = 0;     // 初始都未用过该点
		if(dist[i] == maxint)
			prev[i] = 0;
		else
			prev[i] = v;
	}
	dist[v] = 0;
	s[v] = 1;
 
	// 依次将未放入S集合的结点中，取dist[]最小值的结点，放入结合S中
	// 一旦S包含了所有V中顶点，dist就记录了从源点到所有其他顶点之间的最短路径长度
         // 注意是从第二个节点开始，第一个为源点
	for(int i=2; i<=n; ++i)
	{
		int tmp = maxint;
		int u = v;
		// 找出当前未使用的点j的dist[j]最小值
		for(int j=1; j<=n; ++j)
			if((!s[j]) && dist[j]<tmp)
			{
				u = j;              // u保存当前邻接点中距离最小的点的号码
				tmp = dist[j];
			}
		s[u] = 1;    // 表示u点已存入S集合中
 
		// 更新dist
		for(int j=1; j<=n; ++j)
			if((!s[j]) && c[u][j]<maxint)
			{
				int newdist = dist[u] + c[u][j];
				if(newdist < dist[j])
				{
					dist[j] = newdist;
					prev[j] = u;
				}
			}
	}
}
 
// 查找从源点v到终点u的路径，并输出
void searchPath(int *prev,int v, int u)
{
	int que[maxnum];
	int tot = 1;
	que[tot] = u;
	tot++;
	int tmp = prev[u];
	while(tmp != v)
	{
		que[tot] = tmp;
		tot++;
		tmp = prev[tmp];
	}
	que[tot] = v;
	for(int i=tot; i>=1; --i)
		if(i != 1)
			cout << que[i] << " -> ";
		else
			cout << que[i] << endl;
}
 
int main()
{
	freopen("input.txt", "r", stdin);
	// 各数组都从下标1开始
 
	// 输入结点数
	cin >> n;
	// 输入路径数
	cin >> line;
	int p, q, len;          // 输入p, q两点及其路径长度
 
	// 初始化c[][]为maxint
	for(int i=1; i<=n; ++i)
		for(int j=1; j<=n; ++j)
			c[i][j] = maxint;
 
	for(int i=1; i<=line; ++i)  
	{
		cin >> p >> q >> len;
		if(len < c[p][q])       // 有重边
		{
			c[p][q] = len;      // p指向q
			c[q][p] = len;      // q指向p，这样表示无向图
		}
	}
 
	for(int i=1; i<=n; ++i)
		dist[i] = maxint;
	for(int i=1; i<=n; ++i)
	{
		for(int j=1; j<=n; ++j)
			printf("%8d", c[i][j]);
		printf("\n");
	}
 
	Dijkstra(n, 1, dist, prev, c);
 
	// 最短路径长度
	cout << "源点到最后一个顶点的最短路径长度: " << dist[n] << endl;
 
	// 路径
	cout << "源点到最后一个顶点的路径为: ";
	searchPath(prev, 1, n);
}
```



#### 随机数组求乘积最大的子数组

#### 最大子数组的和

#### 二叉树后序遍历

```c++
vector<int> postTravel(TreeNode* root){
  stack<TreeNode*> s;
  TreeNode* pNode=root,*pre=NULL;
  vector<int> result;
  while(p || !s.empty()){
    if(p){
      pre=p;
      s.push_back(p);
      p=p->lchild;
    }else{
      p=s.top();
      p=p->rchild;
      if(p&&p!=pre){
        s.push_back(p);
        pre=p;
        p=p->lchild;
      }else{
        p=s.top();
        result.push_back(p->val);
        s.pop();
        pre=p;
        pNode=NULL;
      }
    }
  }
  return result;
}
```

#### 一个数组中，只有一个出现1次，其他数都出现了3次，求出这个出现1次的数

```c++
int singleNumber(int A[],int n){
  if(A == NULL || n <=0)
    throw new exception("Invalid input");
  int ones=0;
  int twos=0;
  int threes;
  for(int i=0; i<n;++i){
    int t=a[i];
    twos |= ones&t;
    ones ^= t;
    threes = ones&twos;
    ones &= ~threes;
    twos &= ~threes;
  }
  return ones;
}
```

#### 求两个字符串的最长公共子串，最长公共子序列

##### 堆排序

```c++
#include <iostream>
#include <vector>
#include <iterator>
#include <iostream>
using namespace std;

void adjust_heap(vector<int>& vec,int i,int len){
	int left=2*i+1;
	int right=2*i+2;
	int max=i;
	while(1){
		if(left<len&&vec[left]>vec[max]){
			max=left;
		}
		if(right<len&&vec[right]>vec[max]){
			max=right;
		}
		if(max!=i){
			swap(vec[max],vec[i]);
			i=max;
			left=2*i+1;
			right=2*i+2; 
		}else{
			break;
		}
	}	
}

void make_heap(vector<int>& vec){
	int len=vec.size();
	for(int i=len/2-1;i>=0;--i){
		adjust_heap(vec,i,len);
	}
}

void heap_sort(vector<int>& vec){
	int len=vec.size();
	while(len>1){
		swap(vec[0],vec[len-1]);
		len--;
		adjust_heap(vec,0,len);
	}
}

int main()
{
	vector<int> vec;
	int input;
	while(cin>>input){
		vec.push_back(input);
	}
	
	make_heap(vec);
	heap_sort(vec);
	ostream_iterator<int> iter(cout," ");
	copy(vec.begin(),vec.end(),iter);
	cout<<endl;
	return 0;	
} 
```

#### 快速排序方法1

```c++
int Partition(int *a,int begin,int end){
  int value=a[begin];
  int i=begin,j=i+1;
  while(j<=end){
    if(a[j]<value){
      i++;
      swap(a[i],a[j]);
    }
    j++;
  }
  swap(a[begin],a[i]);
  return i;
}
void QuickSort(int *a,int begin,int end){
  if(begin<end){
    int ivt=Partion(a,begin,end);
    QuickSort(a,begin,ivt-1);
    QuickSort(a,ivt+1,end);
  }
}
```

#### 快速排序方法2

```c++
int partition(int *a,int begin,int end){
    int temp=a[begin];
    int i=begin,j=end;
  	while(i<j){
        while(i<j&&a[j]>=temp){
            --j;
        }
      	if(i<j){
            a[i++]=a[j];
        }
        while(i<j&&a[i]<temp){
            ++i;
        }
        if(i<j){
            a[j--]=a[i];
        }
    }
  	a[i]=temp;
  	return i;
}

void quickSort(int* a,int begin,int end){
    if(begin<end){
        int ivt=partition(a,begin,end);
      	quickSort(a,begin,ivt-1);
      	quickSort(a,ivt+1,end);
    }
}
```

#### 单链表反转

```c++
ListNode* ReverseList(ListNode* pHead){
  ListNode* pReverseHead=NULL;
  ListNode* pNode=pHead;
  ListNode* pPrev=NULL;
  while(pNode){
    ListNode* pNext=pNode->next;
    if(!pNext){
      pReverseHead=pNode;
    }
    pNode->next=pPrev;
    pPrev=pNode;
    pNode=pNext;
  }
  return pReverseHead;
}
```

#### 求字符串的全排列

```c++
void subfunc(vector<string>& result,string str,int index,int length){
  if(index==length){
    result.push_back(str);
    return;
  }
  for(int i=index;i<length;++i){
      swap(str[i],str[index]);
      subfunc(result,str,index+1,length);
      swap(str[i],str[index]);
    }
}

vector<string> perm(string str){
  if(str.empty())
    return vector<string>();
  int len=str.length();
  vector<string> result;
  subfunc(result,str,0,len);
  return result;
}
```

#### 求两个排序数组的中位数

```C++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int total=nums1.size()+nums2.size();
        if(total&0x1){
            return subfind(nums1,nums2,nums1.size(),nums2.size(),total/2+1);
        }else{
            return (subfind(nums1,nums2,nums1.size(),nums2.size(),total/2)+subfind(nums1,nums2,nums1.size(),nums2.size(),total/2+1))/2.0;
        }
        
    }
    int subfind(vector<int>& num1,vector<int>& num2,int len1,int len2,int index){
        int index1=0,index2=0;
        for(int i=0;i<index-1;++i){
            if(index1<len1&&index2<len2){
                if(num1[index1]<num2[index2]){
                    index1++;
                }else{
                    index2++;
                }
            }else if(index1<len1){
                index1++;
            }else if(index2<len2){
                index2++;
            }
        }
        if(index1>=len1){
            return num2[index2];
        }else if(index2>=len2){
            return num1[index1];
        }else{
            return min(num1[index1],num2[index2]);
        }
    }
};
```

#### 最长连续回文子串

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        if(s.empty())
            return string();
        int len=s.size();
        vector<vector<bool>> dp;
        for(int i=0;i<len;++i){
            vector<bool> temp(len);
            dp.push_back(temp);
        }
        int left=0,maxlen=1;
        for(int i=0;i<len;++i){
            for(int j=0;j<len;++j){
                if(i>=j)
                    dp[i][j]=true;
                else{
                    dp[i][j]=false;
                }
            }
        }
        
        for(int k=1;k<len;++k){
            for(int i=0;i+k<len;++i){
                int j=i+k;
                if(s[i]==s[j]){
                    if(dp[i+1][j-1]){
                        dp[i][j]=true;
                        if((k+1)>maxlen){
                            maxlen=k+1;
                            left=i;
                        }
                    }
                }else{
                    dp[i][j]=false;
                }
            }
        }
        return s.substr(left,maxlen);
        
    }
};
```

#### 不用算术运算符实现两个数的加法(按位异或)

```c++
int add(int a,int b){
 if(b==0)
   return a;
  int sum=a^b;
  int carry=(a&b)<<1;
  return add(sum,carry);
}
```

#### 2sum

##### 求最长递增子序列

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> vec;
        for(auto val:nums){
            if(vec.empty()){
                vec.push_back(val);
            }else{
                if(val>vec.back()){
                    vec.push_back(val);
                }else{
                    int i=0,j=vec.size()-1;
                    if(vec.size()==1){
                        vec[0]=val;
                    }else{
                        int insert=BinSearch(vec,vec.size(),val);
                        vec[insert]=val;
                    }
                }
            }
        }
        return  vec.size();
        
    }
    
    //修改的二分查找算法，返回数组元素需要插入的位置。  
    int BinSearch(vector<int>& vec, int len, int w)  
    {  
        int left = 0, right = len - 1;  
        int mid;  
        while (left <= right)  
        {  
            mid = left + (right-left)/2;  
            if (vec[mid] > w)  
                right = mid - 1;  
            else if (vec[mid] < w)  
                left = mid + 1;  
            else    //找到了该元素，则直接返回  
                return mid;  
        }  
        return left;//数组b中不存在该元素，则返回该元素应该插入的位置  
    }  
};
```

#### sqrt

```c++
class Solution {
public:
    int mySqrt(int x) {
        if(x<2)
            return x;
        int low =1,high=x/2,mid;
        while(low<=high){
            mid=(low+high)/2;
            if(mid==x/mid)
                return mid;
            else if(mid<x/mid){
                low=mid+1;
            }
            else
                high=mid-1;
        }
        return high;
        
    }
};
```

#### 手写代码，在有序数组中，找出两个数，使其和为给定值

```c++

bool search(int *arr,int n,int *first,int *second, int value){
  if(!arr || !first || !second || n <2){
    return false;
  }
  int i=0,j=n-1;
  while(i<j){
    if((arr[i]+arr[j])==value){
      *first=arr[i];
      *second=arr[j];
      return true;
    }
    else if((arr[i]+arr[j])>value){
      j--;
    }
    else
      i++;
  }
  return false;
  
}
```

#### 哈希表的冲突解决方式

线性探测，二次探测，再hash,链地址法，建立公共溢出区

#### 红黑树和跳表对比

```
在server端，对并发和性能有要求的情况下，如何选择合适的数据结构（这里是跳跃表和红黑树）。
如果单纯比较性能，跳跃表和红黑树可以说相差不大，但是加上并发的环境就不一样了，
如果要更新数据，跳跃表需要更新的部分就比较少，锁的东西也就比较少，所以不同线程争锁的代价就相对少了，
而红黑树有个平衡的过程，牵涉到大量的节点，争锁的代价也就相对较高了。性能也就不如前者了。
在并发环境下skiplist有另外一个优势，红黑树在插入和删除的时候可能需要做一些rebalance的操作，这样的操作可能会涉及到整个树的其他部分，
而skiplist的操作显然更加局部性一些，锁需要盯住的节点更少，因此在这样的情况下性能好一些。
```

#### 哈希表在桶固定的情况下，时间复杂度。怎么优化

O（1），当桶里面元素大于一个阈值时候，可以将list转换为一个红黑树。

#### 多线程中哈希表保证线程安全

给每个桶单独配一把锁。读写的时候，先hash到对应桶上，然后申请该桶对应的锁。读写结束后，释放锁。

#### 哈希表特别大，桶特别多的时候怎么加锁。

CAS?

##### 红黑树和AVL的相关特性

##### leetcode 525题 包含0和1的序列

##### int转字符串

```c++
string to_string(int n){
	bool negtive=n<0?true:false;
  	vector<char> vecStr;
  	int temp=n<0?-n:n;
  	if(n == 0)
      vecStr.push_back(0+'0');
  	while(temp){
      vecStr.push_back((temp%10)+'0');
      temp/=10;
  	}
  	if(negative)
      vecStr.push_back('-');
  	return string(vecStr.rbeign(),vecStr.rend());
}
```

##### 手写memcpy

```c++
void* memcpy(void* dest,const void *src, int n){
  if(dest == NULL || src == NULL)
    return NULL;
  char* destaddr=(char*)dest;
  char* srcaddr=(char*)src;
  if(dest<=src){
  	while(n){
    	*destaddr++=*srcaddr++;
    	n--;
  	}
  }else{
      while(n){
          *(destaddr+n-1)=*(srcaddr+n-1);
            n--;
      }
  }
  return dest;  
}
```

##### 大数据问题，如果去除一个字符串中重复的字符

先用一个128个int的数组当hash表，统计下所有字符出现的次数。然后依次遍历原字符串，用2个指针，一个指向插入位置，一个指向当前遍历位置。遍历到一个不重复的字符，就将该字符插入到插入指针指向的位置，然后插入指针后移。继续遍历。遍历完成后，插入指针指向的位置置为'\0'.

#### 求二叉树两个结点的最低公共祖先

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root || !p || !q)
            return NULL;
        if(root == p || root == q)
            return root;
        if((p->val < root->val && root->val < q->val) || (p->val > root->val && root->val > q->val))
            return root;
        else if(root->val > p->val && root->val > q->val)
            return lowestCommonAncestor(root->left,p,q);
        else
            return lowestCommonAncestor(root->right,p,q);
        
    }
};
```

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == NULL || p == NULL || q == NULL)
            return NULL;
        if(root == p || root== q)
            return root;
        TreeNode* lcaLeft = lowestCommonAncestor(root->left,p,q);
        TreeNode* lcaRight = lowestCommonAncestor(root->right,p,q);
        if(lcaLeft&&lcaRight)
            return root;
        return lcaLeft?lcaLeft:lcaRight;
        
    }
};
```

#### 查找交叉链表的公共结点，求有环链表的环的起点

先求出依据相遇点，慢指针再走一圈求环的长度，然后front指针先从head走len步，back指针再从head开始同步走，相遇点即为环的入口点

#### TOPk问题，海量日志文件，找出前k个key ,文件对16取模（为什么取16大家自己百度一下），然后存入hashmap,然后利用小顶堆的性质

#### 非递减数组，找出等于或者超过1半的数字

```c++
 int MoreThanHalfNum_Solution(vector<int> numbers) {
        if(numbers.empty()){
            return 0;
        }
        int cur=numbers[0],total=1;
        for(int i=1;i<numbers.size();++i){
            if(total==0){
                cur=numbers[i];
                total++;
            }
            else if(cur==numbers[i]){
                total++;
            }
            else{
                total--;
            }
        }
        int c=count(numbers.begin(),numbers.end(),cur);
        if(c*2>=numbers.size()){
            return cur;
        }
        return 0;
    
    }
```

#### 一致性哈希算法

顺时针迁移，虚拟结点。

“虚拟节点”的hash计算可以采用对应节点的IP地址加数字后缀的方式。例如假设NODE1的IP地址为192.168.1.100。引入“虚拟节点”前，计算 cache A 的 hash 值：

Hash(“192.168.1.100”);

引入“虚拟节点”后，计算“虚拟节”点NODE1-1和NODE1-2的hash值：

Hash(“192.168.1.100#1”); // NODE1-1

Hash(“192.168.1.100#2”); // NODE1-2

#### strtok

```c++
char* mystrtok(char s[], const char* delim){
  static char* cur=s;
  if(cur==NULL || *cur == '\0')
    return NULL;
  char* ret=cur;
  while(*cur!='\0' && *cur!= *delim){
    cur++;
  }
  if(*cur == *delim){
    *cur='\0';
    cur++;
  }
  return ret;
}
```

#### 手写代码查找树的每层最右结点（层次遍历）

#### 手写字符串拷贝strcpy,二分查找

```c++
char* strcpy(char* dest,const char* src)
{
  assert(dest != NULL);
  assert(src != NULL);
  char *ret=dest;
  while((*dest++=*src++)!='\0');
  return ret;
}
```

```c++
int binary_search(int a[],int n,int value)
{
  int low=0,high=n-1;
  while(low<=high){
    int mid=(low+high)/2;
    if(a[mid] == value){
      return mid;
    }
    else if(a[mid]>value){
      high=mid-1;
    }
    else{
      low=mid+1;
    }
  }
  return -1;
}
```

#### 字典排序算法

```c++
/*bool compare(const char* s1,const char* s2){
  return strcmp(s1,s2)<0;
}
int Partion(char(*words)[100],int begin,int end){
  char value[100];
  strcpy(value,words[begin]);
  while(begin<end){
    while(begin<end&&!compare(words[end],value))
    	end--;
	if(begin<end){
		 strcpy(words[begin],words[end]);
    	 begin++;
	}
    while(begin<end&&compare(words[begin],value))
      begin++;
    if(begin<end){
    	strcpy(words[end],words[begin]);
    	end--;
	}
    
  }
  strcpy(words[end],value);
  return end;
}
void QuickSort(char(*words)[100],int begin,int end){
  if(begin<end){
    int partion = Partion(words,begin,end);
    QuickSort(words,begin,partion-1);
    QuickSort(words,partion+1,end);
  }
}
void dictSort(char(*words)[100], int n){
  if(words == NULL || n<=0)
  	return;
  QuickSort(words,0,n-1);
}*/

bool compare(const char* s1,const char* s2){
  return strcmp(s1,s2)<0;
}
int Partion(vector<string>& words,int begin,int end){
  string value=words[begin];
  while(begin<end){
    while(begin<end&&!compare(words[end].c_str(),value.c_str()))
    	end--;
    if(begin<end){
		words[begin]=words[end];
    	begin++;
	}
    while(begin<end&&compare(words[begin].c_str(),value.c_str()))
      begin++;
    if(begin<end){
    	words[end]=words[begin];
    	end--;	
	}
    
  }
  words[end]=value;
  return end;
}
void QuickSort(vector<string>& words,int begin,int end){
  if(begin<end){
    int partion = Partion(words,begin,end);
    QuickSort(words,begin,partion-1);
    QuickSort(words,partion+1,end);
  }
}
void dictSort(vector<string>& words, int n){
  if(words.empty() || n<=0)
  	return;
  QuickSort(words,0,n-1);
}
```

#### 单链表快速排序算法

##### 求无序数组中最近邻的两个值的最大差值

```c++
#include <iostream>
#include <vector>
#include <utility>
using namespace std;
/*
*一个无序的实数数组，求它们最近邻的两个值的差
**/
double maxDiff(double a[], int n){
	double max = a[0];
	double min = a[0];
	for (int i=1; i<n; ++i){
		if (max < a[i]){
			max = a[i];
		}
		if (min > a[i]){
			min = a[i];
		}		
	}
	double bar = (max - min)/(n-1);
	int pos;
	//pair<first,second> : first表示桶的左边界index，second表示桶的右边界index
	vector< pair<int,int> > buckets(n,make_pair(-1,-1));
	//这里桶内存相应数据的下标，而不是相应的数据，方便后面的数据计算，以免有精度损失。
	for (int i=0; i<n; i++){
		pos = (int)((a[i] - min)/bar);
		if ((buckets[pos].first == -1) && (buckets[pos].second == -1)){ //下标比较，若为double型比较注意精度问题
			buckets[pos].first = buckets[pos].second = i;
		}else{
			if (a[buckets[pos].first] > a[i])
				buckets[pos].first = i;
			if (a[buckets[pos].second] < a[i])
				buckets[pos].second = i;
		}
	}
	int lastIx=0;
	double max_diff = 0;
	double tmp_diff = 0;
	for (int i=1; i<n; ++i){ //计算桶之间的距离
		if ((buckets[i].first == -1) && (buckets[i].second == -1)){ 
			//桶为空的标志,不处理
		}else{
			tmp_diff = a[buckets[i].first] - a[buckets[lastIx].second];
			if (tmp_diff > max_diff){
				max_diff = tmp_diff;
			}
			lastIx = i;//lastIx指上一个非空桶的index，且第一个桶和最后一个桶肯定非空。
		}
	}
	return max_diff;
}
int main(){
	double a[]={2,4,8,16,19.0,7,7,30};
	cout<<maxDiff(a,8)<<endl;
	return 0;
}
```



#### 区间合并

考虑先将区间按照start进行升序排序，然后合并back.start<=front.end情况的区间。

#### 翻转单词顺序和左旋转单词

#### 非递归求二叉树的深度

用层次遍历的方法

#### KMP算法

#### 手写atoi

```c++
int myAtoi(const char* str){
  if(str==NULL)
    return 0;
  long long result=0;
  bool negative=false;
  int index=0;
  while(str[index]== ' ')
    index++;
  if(str[index]=='-'){
    negative=true;
    index++;
  }
  else if(str[index]=='+'){
    index++;
  }
  while(str[index]>='0'&&str[index]<='9'){
    result+=result*10+str[index]-'0';
    index++;
  }
  if(negative)
    result=-result;
  if(result>0x7FFFFFFF || result<0x80000000)
    return 0;
  return result;
}
```

#### 字典树

根节点不保存数据，然后每个结点存储路径值，以及关于本结点数据的次数信息。来表示到此路径是否存储数据以及数据出现的频率。用来做词频统计，或者字典排序。

##### 二叉树镜像

```c++
void Mirror(TreeNode* root){
  if(!root)
    return;
  if(!root->left&&!root->right){
    return;
  }
  swap(root->left,root->right);
  if(root->left)
    Mirror(root->left);
  if(root->right)
    Mirror(root->right);
}
```

##### 青蛙跳台阶问题，一只青蛙从第一级台阶跳到n级，每次可以跳任意级，共有多少种跳法，并写出递推式

$$
f(n)=1+f(n-1)+f(n-2)+...+f(1) ，当n>1;
f(n)=n,   当0<=n<=1;
$$

#### 统计一篇文章出现频率最高的单词，几亿个1到1000的数，让找出出现频率最高的前10个数。

先用一个1000的int数组，遍历统计一遍所有数出现的次数。然后建立TOPK小顶堆。以频率作为排序依据，数据为数组下标+1。然后从头到尾遍历数组中保存的频率。先插入10个元素到堆中。后面如果有频率大于堆顶。pop_heap，再插入新的数据。遍历完，堆中数据的data字段为频率最高的10个数。

#### 一个1G文件里面都是字符串，每一行只有一个字符串，字符串最大16个字节，实现不超过1M空间下找出重复次数最多的前100个字符串

先hash到分成小文件。

### 操作系统

#### 浮点数的内存表示，大小端对浮点数有什么影响

#### 怎么查找一个文件的后100行

```shell
tail -n 100 xxx.txt
```

##### 早期的小数表示采用的固定小数点的方式，比如规定在32位二级制数字当中，哪几位表示整数部分，其余的表示小数部分，这样表示的数据范围有限，后来采用的是小数点浮动变化的表示方式，也就是所谓的浮点数。

###### 浮点数采用的是IEEE的表示方式，最高位表示符号位，在剩余的31位中，从左往右8位表示的是科学计数法的指数部分，其余的表示整数部分。例如我们将12.25f化为浮点数的表示方式：

　　首先将它化为二进制表示1100.01，利用科学计数法可以表述为:1.10001 * 2^3

　　分解出各个部分：指数部分3 + 127= 011 + 0111111、尾数数部分：10001

　　需要注意的是：因为用科学计数法来表示的话，最高位肯定为1所以这个1不会被表示出来

　　　　　　　　　指数部分也有正负之分，最高位为1表示正指数，为0表示负指数，所以算出来指数部分后需要加上127进行转化。

　　将这个转化为对应的32位二级制，尾数部分从31位开始填写，不足部分补0即：0 | 10000010 | 10001 |000000000000000000，隔开的位置分别为符号位、指数位，尾数位。

##### 大小端对浮点数的影响

字节序不一样机器存储浮点数时，二进制存储表示不同。大端机器是低位高字节，小端是低位低字节。

#### unix信号

##### 信号处理函数如何执行起来的

编号为1 ~ 31的信号为传统UNIX支持的信号，是不可靠信号(非实时的)，编号为32 ~ 63的信号是后来扩充的，称做可靠信号(实时信号)。不可靠信号和可靠信号的区别在于前者不支持排队，可能会造成信号丢失，而后者不会。

##### SIGUSR1 SIGTERM SIGINT

SIGUSR1留着用户程序自定义的信号,SIGTERM 软件中止进程, SIGINT,CTRL+C中止进程

##### 不可捕获的信号

SIGSTOP和SIGKILL

#### 进程调度算法

1. 短作业优先
2. 先来先服务
3. 高优先级优先（抢占式和非抢占式）
4. 高响应比优先
5. 时间片轮转
6. 多级反馈队列（分优先级进行时间片轮转调度，优先级高的时间片小，每次获取时间片之后，若未完成，调到上一级优先级队列的尾端）

##### CFS调用算法

完全公平(CFS)调度算法

一个调度算法中最重要的两点就是调度哪个进程以及该被调度进程的运行时间是多少，下面我们分别来讨论

###### 调度哪个进程

O(1)算法是根据进程的优先级来选择调度进程的，而CFS是根据进程的虚拟运行时间来进行调度的，当然，该虚拟运行时间也会受到优先级的影响，但不全是，下面看看什么是虚拟运行时间，以及如何根据它来选择下一个调度进程。

CFS算法的初衷就是让所有进程同时运行在一个CPU上，例如两个进程都需要运行10ms的时间，则CFS算法下，连个进程同时运行在CPU上，且时间为20ms，而不是每个进程分别运行10ms。但是这只是一种理想的运行方式，CFS为了近似这种运行算法，就提出了虚拟运行时间(vruntime)的概念。vruntime记录了一个可执行进程到当前时刻为止执行的总时间（需要以进程总数n进行归一化，并且根据进程的优先级进行加权）。根据vruntime的定义可以知道，vruntime越大，说明该进程运行的越久，所以被调度的可能性就越小。所以我们的调度算法就是每次选择vruntime值最小的进程进行调度，内核中使用红黑树可以方便的得到vruntime值最小的进程。至于每个进程如何更新自己的vruntime？内核中是按照如下方式来更新的：vruntime +=  delta* NICE_0_LOAD/ se.weight;其中：

NICE_0_LOAD是个定值，及系统默认的进程的权值；se,weight是当前进程的权重(优先级越高，权重越大)；
delta是当前进程运行的时间；我们可以得出这么个关系：vruntime与delta成正比，即当前运行时间越长vruntime增长越快
vruntime与se.weight成反比，即权重越大vunruntime增长越慢。简单来说，一个进程的优先级越高，而且该进程运行的时间越少，则该进程的vruntime就越小，该进程被调度的可能性就越高。

###### 调度进程的运行时间

现在知道了如何调度进程了，但是当该进程运行时，它的运行时间是多少呢？

CFS的运行时间是有当前系统中所有可调度进程的优先级的比重来确定的，假如现在进程中有三个可调度进程A、B、C，它们的优先级分别为5,10,15，则它们的时间片分别为5/30，10/30，15/30。而不是由自己的时间片计算得来的，这样的话，优先级为1，2的两个进程与优先级为50,100的两个进程分的时间片是相同的。简单来说，CFS采用的所有进程优先级的比重来计算每个进程的时间片的，是相对的而不是绝对的。

这样就从以上两个方面来分析了CFS进程调度算法

#### 虚拟地址空间的作用

1.让每个进程有独立的地址空间（进程间安全）2.VA到PA的映射给内存的分配和释放带来方便 3.多个进程分配的内存之和可以大于实际可用的物理内存。

#### 虚拟地址是什么，通过什么转换成物理地址。

##### 逻辑地址

段号+段内偏移，然后根据GDT（全局段描述符表）(gdtr控制寄存器)查段表的地址，查段表获得段起始地址。

##### 线性地址（虚拟地址）

线性地址=段起始地址+段内偏移

每一个32位的线性地址被划分为三部分，页目录索引（10位）+页表索引（10位）+页内偏移（12位）  页目录地址存在cr3寄存器中。

物理地址=页地址+页内偏移。先通过寄存器获取页目录的地址，然后通过页目录索引找到页表的地址，然后通过页表索引获取页地址，页起始地址+页内偏移就是物理地址。

#### Linux内存管理

#### 虚拟内存工作原理

局部性原理

##### 页面置换算法

1. 先进先出置换算法
2. 最佳置换算法
3. 最近最久未被使用置换算法LRU
4. 最久未被使用置换算法NRU（状态位，访问位）

#### LINUX线程

轻量级进程,do_fork,clone(CLONE_VM | CLONE_FS | CLONE_FILES | CLONE_SIGHAND),表示共享内存、共享文件系统访问计数、共享文件描述符表，以及共享信号处理方式。

#### 进程和线程的区别，了解协程吗

进程是资源分配的基本单位，线程是调度的基本单位。同一进程内的线程共享进程的地址空间。创建线程的开销比创建进程的开销要小很多。

##### 区别

###### 系统开销：创建和销毁进程的系统开销比创建线程和销毁线程的线程开销要大的多。

###### 资源管理：一个进程崩溃后，在保护模式下对其他的进程不会产生直接的影响。一个线程崩溃等于整个进程崩溃了。

###### 通信方式：进程间通信要借助其他的进程间通信手段，效率较低。同一个进程的线程共享进程的地址空间，所以可以借助全局变量来进行线程间通信，效率更高。

#### 协程

###### poll vs run

poll只执行已经就绪的IO事件，然后返回。而wait会阻塞到有所有注册的IO事件都完成。

###### strand

在多线程中，多个io对象的handler要访问同一块临界区，此时可以使用strand来保证这些handler之间的同步。

协程是用户级线程，内核不知道协程的存在，由用户级调用器实现调度。

线程受栈空间限制，而协程的栈空间由用户控制，而且实现协程需要的辅助数据结构很少，占用的内存少，那么就会有各大的容量，比如可以轻松开10W个协程。

###### 协程的分类

stackful以及stackless,非对称协程以及对称协程。

**stackful协程可以被嵌套使用，stackless协程只有顶层的函数可以被暂停，不允许嵌套使用协程。**

###### 协程极大的优化了程序员的编程体验，同步编程风格的，但是有异步的性能，协程可以做到异步状态的保存与恢复是自动的。

##### 微信libco

把协程的让出与恢复操作作为异步网络IO中的一次事件注册与回调。事件的注册与回调实质上是协程的让出与恢复执行。

###### 架构图

| 层次        | 说明                             |
| --------- | ------------------------------ |
| libco接口层  | 协程原语，协程信号量，协程私有变量              |
| 系统函数hook层 | socket族函数，gethostbyname,setenv |
| 事件驱动层     | epoll/kqueue，定时器               |

1. 接口层实现了协程的基本原语，co_create，co_resume负责携程的创建与恢复。co_cond_signal类接口可以在协程间创建一个协程信号量，可用于协程间的同步通信
2. 系统函数hook层，负责系统中同步api到异步执行的转换。对于常用的同步网络接口，hook层会把本次网络请求注册为异步事件，然后等待事件驱动层的唤醒执行。
3. 事件驱动层实现了一个简单高效的异步网络框架。

###### 协程和线程进行对比

1. 协程的创建和调度代价相对于线程来说，代价小很多。
2. 协程间的通信和同步可以是无锁的，任一时刻可以保证只有本协程在操作线程内的资源。

###### 协程运行栈  

从堆内存分配一个固定大小的内存作为该协程的运行栈，默认的每个协程独占128k栈空间。

###### 协程共享栈

libco新增的共享栈模式，若干个协程共享同一个运行栈。**同一个共享栈下的协程间切换的时候，需要把当前运行栈内容拷贝到协程私有内存中，写时拷贝。**

###### 协程私有变量

协程可能会导致线程私有变量不可重入，将线程私有变量改成协程私有变量可以解决这个问题。

#### fork从父进程继承了哪些，没有继承哪些

##### 继承

1. 打开文件描述符
2. 缓冲区
3. 地址空间，包括数据段，代码段，堆栈，环境变量等
4. ...

##### 没有继承

1. 内存锁
2. 记录锁
3. alarm,timer_create等创建的定时器
4. 阻塞信号集
5. 异步输入和输出
6. .....

#### 操作系统如何读取文件，讲讲具体操作流程

发起读文件请求->根据文件描述符定位到VFS的打开文件列表对应的表项->调用该文件对应的do_read系统调用->找到文件对应的inode结点->通过偏移量计算出要读取的页->通过inode结点找到address_space，再访问页缓存树，如果页缓存命中，直接返回页文件内容，如果页缓存没有命中，产生缺页异常，创建一个页缓存，把磁盘上的该页的内容填充到页缓存，然后再查找页缓存树，返回该页内容，文件读写成功。

#### 操作系统的内存管理，段页式，然后扩展下对应的算法

###### 在为某个段分配物理内存的时候。可以采用首次适配法，循环首次适配法，最佳适配法。

###### 想死回收某个段所占用的空间时，要注意将回收的空间与相邻空间的合并。

###### 段式和页式存储管理，为获得一条指令或数据，须两次访问内存；而段页式需要3次访问内存。

#### 死锁的定义，死锁产生的原因，死锁产生的必要条件，如何预防死锁，如何避免死锁，如何解除死锁

##### 死锁的定义

两个或两个以上的进程，在执行过程中，因争夺资源而造成一种相互等待的现象，若无外力作用，它们都将无法继续执行下去。

##### 死锁的必要条件

1. 互斥
2. 占有且等待
3. 不可抢占
4. 循环等待

##### 死锁的解决办法

###### 检测死锁

建立资源分配表和进程等待表

##### 死锁的预防

1. 资源一次性分配
2. 可剥夺资源
3. 资源按序分配

##### 死锁的避免

银行家算法

###### 解除死锁

1. 剥夺资源：从其他进程剥夺足够多的资源给死锁进程，以解除死锁状态；
2. 撤销进程：可以直接撤销死锁进程，撤销代价最小的进程，直到死锁状态消除为止；

#### int ,long,long long 字节数

| operation | int  | long |
| --------- | ---- | ---- |
| win64     | 4    | 4    |
| win32     | 4    | 4    |
| linux32   | 4    | 4    |
| linux64   | 4    | 8    |

#### Linux内核文件系统的架构，采用了什么数据结构

task_struct结构中有一个files_struct结构的指针，files_struct结构里面有files结构的指针数组，files结构中有file_operations结构，其中封装了对应文件的支持的文件操作及其函数指针，是一个函数跳转表。

#### Linux文件系统层次

Application(用户空间)->(系统调用层->VFS->具体的文件系统（EXT,NTFS,FAT）->通用设备块层（封装IO接口）->驱动层)(内核空间)->存储设备（硬件）

#### 什么时候发生用户态到内核态的切换，切换时做了什么工作

系统调用，异常，中断，保存用户态进程上下文，进入内核执行内核中的处理程序，恢复进程上下文，返回用户态。

#### linux文件系统日志的作用

### coding综合

#### C++篇

##### c++的构造函数和析构函数可以抛出异常吗？



##### virtual函数能声明为内联吗？为什么？

通常不行。inline是编译期决定的，virtual是运行期决定的。这个问题不太好。

##### 指向数组的引用

```c++
int a[3];
int(&aref)[3]=a;
int c[3][3];
int (&cref)[3][3]=c;
```

##### auto关键字

如果希望推断出的auto类型是一个顶层const或者引用类型，需要明确指出。

```c++
const int a=1;
const auto b=a;
int d=2;
int &c=d;
auto &f=c;
```

##### decltype

decltype处理顶层const和引用的方式与auto有些许不同。可以推断出顶层const和引用类型。

##### std::string 思考

引用计数型string用的是copy on write技术，现代string有用的是sso（small string optimization）技术，足够小的字符串直接放在对象本身的栈内存中，避免了向Heap动态请求内存的开销。因为程序中会存在大量短小的字符串，所以sso高效。

###### std::string是线程安全的吗？

stackoverflow上回答是又不是。STL容器的线程安全：线程安全意味着同步，同步意味着性能损失，贸然地保证线程安全必然违背了C++的哲学。但从不同线程中操作”独立“的string对象来看，std::string必须是线程安全的。咋一看这似乎不是要求，但COW的实现使两个逻辑上独立的string对象在物理上共享同一片内存，因此必须实现逻辑层面的隔离。**简单说来就是：你瞒着用户使用共享内存是可以的（比如用COW实现string），但你必须负责处理可能的竞态条件**，而COW实现中避免竞态条件的关键在于：1.只对引用计数进行原子增减;2.需要修改时，先分配和复制，后将引用计数-1（当引用计数为0时负责销毁）。

##### new跟malloc的区别

1. 返回类型的安全性，malloc返回的是void*,new返回的是指定类型的指针
2. new分配失败默认会抛出bad_alloc异常，malloc返回NULL
3. malloc分配的内存后没有进行初始化，new的对象是内置类型，且没有指定初始化列表的话也不会进行初始化，而new的对象是自定义类型时候会调用默认构造函数完成对象的构造。
4. new可以重载，malloc不可以重载。
5. new申请小于128B的内存的时候，可以不用进行内存动态分配，直接使用allocator的2级内存分配器从内存池中取已经分配好的内存。
6. ....

##### C++类型转换

###### static_cast

具有明确定义的类型转换，只要不包含底层const,都可以使用static_cast（用于相关类型之间的转换，不能去除const属性）

###### const_cast

用于改变运算对象的底层const，只能转换引用或指针。

###### reinterpret_cast

通常为运算对象位模式上的提供较低层次上的重新解释（即重新解释，用于不相关类型之间的转换）。

###### dynamic_cast

一种运行时检测的类型转换，主要用于类型向下的转换和继承之间的转换。（若继承没有多态性，即没有虚函数不能使用）。

##### 虚继承，虚继承的内部实现

虚继承是为了解决多重继承条件下的菱形继承问题下的公共基类成员二义性问题。解决办法是派生类对象只保存一份虚基类的实体，并有一个虚指针指向该虚基类实体信息表。

###### 虚继承+虚函数混合的继承体系内存布局

```c++
#include <iostream>
#include <stdio.h>

using namespace std;

typedef void(*pfunc)();

class Base{
public:
	int base=1;
	virtual void func()
	{
		cout << "Base::func" << endl;
	}
};

class A :virtual public Base{
public:
	int a = 2;
	virtual void funcA()
	{
		cout << "A::funcA" << endl;
	}
};

class B :virtual public Base{
public:
	int b = 3;
	virtual void funcB()
	{
		cout << "B::funcB" << endl;
	}
};

class C :public A, public B{
public:
	int c = 4;
	virtual void funcC()
	{
		cout << "C::funcC" << endl;
	}
};

int _tmain(int argc, _TCHAR* argv[])
{
	C ex;
	cout << sizeof(C) << endl;
	int* p=reinterpret_cast<int*>(&ex);
	cout <<p<<" "<< *(p + 0) << endl;
	cout << p+1 << " " << ((int**)(p + 1))[0][0] << endl;
	//cout << hex << *(p + 1) << endl;
	cout <<p+2<<" "<<*(p + 2) << endl;
	cout << p+3 << " " << *(p + 3) << endl;
	//cout << p + 4 << " " <<((int**)(p + 4))[0][3] << endl;
	//cout << hex << *(p + 4) << endl;
	cout << p+5 << " " << *(p + 5) << endl;
	cout << p+6 << " " << *(p + 6) << endl;
	cout << p+7 << " " << *(p + 7) << endl;
	cout << p+8 << " " << *(p + 8) << endl;
	pfunc f = (pfunc)(((int**)(p+7))[0][0]);
	f();
	f = (pfunc)(((int**)(p + 0))[0][0]);
	f();
	f = (pfunc)(((int**)(p + 0))[0][1]);
	f();
	f = (pfunc)(((int**)(p + 3))[0][0]);
	f();
	cout << hex << 10<<dec <<" "<< 12<<endl;
	cout << 3 << endl;
	return 0;
}
```

###### 32位程序内存布局表

| 成员名称       | 说明             |
| ---------- | -------------- |
| VptrA1     | 指向A的虚函数表的指针    |
| VptrA2     | 指向虚基类信息表的指针    |
| A::a       | A的数据成员a        |
| VptrB1     | 指向B的虚函数表的指针    |
| VptrB2     | 指向虚基类信息表的指针    |
| B::b       | B的数据成员b        |
| C::c       | C的数据成员c        |
| VptrBase   | 指向Base的虚函数表的指针 |
| Base::base | Base的数据成员base  |

###### 虚函数表布局

###### VptrA1

| 成员       | 说明         |
| -------- | ---------- |
| A::funcA | A的虚函数funcA |
| C::funcC | C的虚函数funcC |

###### VptrB1

| 成员       | 说明         |
| -------- | ---------- |
| B::funcB | B的虚函数funcB |

###### VptrBase

| 成员         | 说明           |
| ---------- | ------------ |
| Base::func | Base的虚函数func |

##### 智能指针的实现

###### shared_ptr

```c++
template<typename T>
class shared_Ptr{
public:
  shared_Ptr()
    :ptr_(NULL),
  	 info_(new sharedPtr_Info)
  {}
  shared_Ptr(T* ptr)
  	:ptr_(ptr),
  	 info_(new sharedPtr_Info)
  {
    info_->use_count_++;
  }
  ~shared_Ptr(){
    deconstruct();
  }
  shared_Ptr(const shared_Ptr& rhs)
    :ptr_(rhs.ptr_),
  	 info_(rhs.info_)
  {
  	cout<<"shared_Ptr::copy construct"<<endl;
   	info_->use_count_++;
  }
  
  shared_Ptr& operator=(const shared_Ptr& rhs){
  	cout<<"shared_Ptr::operator= 1"<<endl;
    shared_Ptr temp(rhs); 
    std::swap(temp.info_,this->info_);
    std::swap(temp.ptr_,this->ptr_);
    temp.deconstruct();
    this->info_->use_count_++;
    cout<<"shared_Ptr::operator= 2"<<endl;
    return *this;
  }
  
  int use_count() const
  {
    return info_->use_count_;
  }
  
  T& operator*() const
  {
  	if(ptr_)
	  return *ptr_;	
  }
  
  T* operator->() const
  {
  	cout<<"operator->"<<endl;
  	return ptr_;	
  }
  
  T* get() const
  {
  	return ptr_;
  }
  
private:
  struct sharedPtr_Info{
  unsigned int use_count_;
  sharedPtr_Info()
    :use_count_(0)
    {}
  };	
	
  void deconstruct()
  {
    if(info_->use_count)
    	info_->use_count_--;
    if(info_->use_count_ == 0){
      if(ptr_){
      	delete ptr_;
        ptr_=NULL;
      }
      if(info_){
      	delete info_;
        info_=NULL;
      }
    }
  }
private:
  T* ptr_;
  sharedPtr_Info* info_;
};
```

##### 已知某类,问A a=1是否正确，如果正确，那么它调用了哪些函数

```c++
class A{
  public:
  	A(int x):x_(x)
  	{ }
  private:
  	int x_;
};
```

因为构造函数没有声明explicit关键字，是正确的。

1. 如果编译器没有做优化，先将1强制转换为A类型（构造函数），再调用A的拷贝构造函数构造a；
2. 如果编译器做了优化的，直接用1来构造a（构造函数）。

##### Vector是深拷贝还是浅拷贝

如果是自定义类型，自定义类型自己定义拷贝构造函数是深拷贝，则是深拷贝。如果定义的拷贝构造函数是浅拷贝或者没有定义拷贝构造函数，则调用系统合成的拷贝构造函数，是浅拷贝。内置类型则是值拷贝。

##### 重载，覆盖和隐藏的区别

1. 重载是参数个数和顺序不同，函数名相同的函数;
2. 覆盖是指派生类覆写了父类的相同签名的虚函数
3. 派生类定义了一个与父类中函数签名一致的普通函数，这个函数不是虚函数

##### functor实现，作用

仿函数不是函数，是个类，是通过重载（）运算符模拟函数行为的类。

1.面向对象的模板机制对functor完全适用，因此用函数对象可以和STL完全兼容，函数指针必须指定参数以及返回类型，而STL属于泛型编程，函数指针违反泛型思想的本质。

2.函数对象可以使用类中封装的所有数据，而函数指针只能使用全局变量，functor可以达到封装的目的。

3.functor往往是轻量级代码，因此，可以完美内联化。

##### STL的unordered_map和map的区别,使用场景

###### 区别

一个底层实现是哈希表，一个是红黑树，一个查找时间复杂度是O(1),一个是O(logN)，unordered_map无序,map有序。

###### 使用场景

个人感觉：unordered_map适用于一开始就建立好hash table,然后数据的插入不频繁，对查找性能要求较高的场景。而map试用与数据的插入删除较多，且对查找性能要求较高的场景。

##### C怎么实现C++多态（this指针和虚函数表）

##### 委托构造函数

一个类的构造函数调用其他的构造函数完成构造过程。

```c++
class A{
  public:
  	A(int a):a_(a){}
    A():A(0) { }  //委托构造函数  
  private:
  	int a_;
};
```

##### 虚函数的作用，纯虚函数的作用

虚函数在C++中用于实现多态机制。即通过基类的指针或引用访问派生类定义的函数；

纯虚函数相当于一个接口，起到一个规范作用，规范继承这个类的程序员，如果想实例化派生类，必须实现这个函数。同时纯虚函数也是虚函数，实现了多态机制。

#### STL篇

##### 关联容器和顺序容器区别

关联容器(Associative [Container](http://lib.csdn.net/base/docker))与顺序容器(Sequential Container)的本质区别在于：关联容器是通过键(key)存储和读取元素的，而顺序容器则通过元素在容器中的位置顺序存储和访问元素。 

##### STL sort

数据少于16用插入排序，递归深度超过2lgn用堆,其他使用快速排序

##### STL排序相关的选择

1. partial_sort 
2. nth_element
3. partion，用来划分，返回最后一个不符合的位置

##### STL迭代器分类

1. 输入迭代器
2. 输出迭代器
3. 双向迭代器
4. 前向迭代器
5. 随机访问迭代器

#### STL适配器类型

1. 队列
2. 栈
3. 优先级队列

#### priority_queue,优先队列的时间复杂度，堆的维护时间复杂度

优先级队列是利用一个max-heap完成的，时间复杂度O（logN），后者是一个以vector表现的完全二叉树。堆的维护时间复杂度O(logN)

#### STL六大组件

容器，算法，迭代器，空间配置器，配接器，仿函数。

#### STL中的内存管理allocator的原理，以及如何让它线程安全

STL提供的默认allocator是一个由两级分配器构成的内存管理器，当申请的内存大小大于128byte时，就启动第一级分配器通过malloc直接向系统的堆空间分配，如果申请的内存大小小于128byte,就启动第二级分配器，从一个预先分配好的内存池中取一块内存交付给用户，这个内存池由16个不同大小（8的倍数，8-128byte）的空闲块列表组成，allocator会根据（将这个大小round up成8的倍数）从对应的**空闲块列表**取表头块给用户。

##### 这个做法的优点

###### 小对象的快速分配。小对象是从内存池分配的，这个内存池是系统调用调用一次malloc分配一块足够大的区域给程序备用，当内存池耗尽时再向系统申请一块新的区域。

###### 避免了内存碎片的生成。程序中小对象的分配极易造成内存碎片，给操作系统的内存管理带来了很大的压力，系统中的碎片的增多不但会影响内存分配的速度，而且会极大地降低内存的利用率。以内存池组织小对象的内存，从系统的角度看，只是一大块内存池，看不到小对象内存的分配和释放。

#### Boost篇

#### Linux 相关

##### singalfd,timerfd,eventfd

1. signalfd：传统的处理信号的方式是注册信号处理函数；由于信号是异步发生的，要解决数据的并发访问，可重入问题。signalfd可以将信号抽象为一个文件描述符，当有信号发生时可以对其read，这样可以将信号的监听放到select、poll、epoll等监听队列中。
2. timerfd：可以实现定时器的功能，将定时器抽象为文件描述符，当定时器到期时可以对其read，这样也可以放到监听队列的主循环中。
3. eventfd：实现了线程之间事件通知的方式，eventfd的缓冲区大小是sizeof(uint64_t)；向其write可以递增这个计数器，read操作可以读取，并进行清零；eventfd也可以放到监听队列中，当计数器不是0时，有可读事件发生，可以进行读取。

**三种新的fd都可以进行监听，当有事件触发时，有可读事件发生。**

##### 信号量和互斥量有哪些区别

互斥量必须由上锁的线程解锁，而信号量并不要求挂出操作由本线程来捕获。互斥量要么被锁住，要么解开状态。而信号量有二值信号量和计数信号量。

##### 内存泄露的定位方法

###### 静态分析

包括手动检测和静态工具分析，分析coredump文件

###### 动态运行检测

实时检测工具valgrind

##### Linux下有哪些分配内存的方法，分别是什么

###### 静态分配内存

全局变量和静态变量

###### 动态分配内存

malloc，mmap

###### 分配栈内存

##### Linux编译选项下有哪些，举几个例子具体说明

-g加入调试程序 -Wall 生成所有警告信息 -w 不生成警告信息  -l 链接动态库  -static 强制生成静态链接的程序 -shared编译成共享库文件

##### 优化代码的等级分别优化了哪些内容

###### -O0

不进行任何优化

###### -O1

尝试减少代码体积和代码运行时间

###### -O2

并不执行循环展开和“内联优化操作”

###### -O3

##### cpp文件的编译过程

预处理、编译、优化、汇编、链接

##### tcpdump

```shell
tcpdump  -i eth0 -n tcp dest host x.x.x.x port 22 and src host x.x.x.x port 65533

```

##### 写一个判断大小端的程序

```c++
void judgeByteOrder(){
  union{
    short x;
    char c[sizeof(short)];
  } test;
  test.x=0x0102;
  if(test.c[0] == 0x02 && test.c[1]==0x01){
    cout<<"small enddian"<<endl;
  }else{
    cout<<"big enddian"<<endl;
  }
}
```

#### linux并发相关

##### linux内核锁篇

阻塞型同步和非阻塞型同步。mutex和semaphore（阻塞型同步）

###### 非阻塞型同步实现方案

1. Wait-free

   Wait-free 是指任意线程的任何操作都可以在有限步之内结束，而不用关心其它线程的执行速度。 Wait-free 是基于 per-thread 的，可以认为是 starvation-free 的。非常遗憾的是实际情况并非如此，采用 Wait-free 的程序并不能保证 starvation-free，同时内存消耗也随线程数量而线性增长。目前只有极少数的非阻塞算法实现了这一点。

2. Lock-free

   Lock-Free 是指能够确保执行它的所有线程中至少有一个能够继续往下执行。由于每个线程不是 starvation-free 的，即有些线程可能会被任意地延迟，然而在每一步都至少有一个线程能够往下执行，因此系统作为一个整体是在持续执行的，可以认为是 system-wide 的。所有 Wait-free 的算法都是 Lock-Free 的。

3. Obstruction-free

   Obstruction-free 是指在任何时间点，一个孤立运行线程的每一个操作可以在有限步之内结束。只要没有竞争，线程就可以持续运行。一旦共享数据被修改，Obstruction-free 要求中止已经完成的部分操作，并进行回滚。 所有 Lock-Free 的算法都是 Obstruction-free 的。

###### Linux 内核中的无锁分析

1. 内核无锁第一层级 — 少锁（Double check lock）
2. 内核无锁第二层级 — 原子锁(atomic变量)
3. 内核无锁第三层级 — Lock-free，（spin lock&& seq lock&& read copy update)。seqlock 实现原理是依赖一个序列计数器，当写者写入数据时，会得到一把锁，并且将序列值加 1。当读者读取数据之前和之后，该序列号都会被读取，如果读取的序列号值都相同，则表明写没有发生。反之，表明发生过写事件，则放弃已进行的操作，重新循环一次，直至成功;read copy update,允许读操作在任何时候无阻碍的运行，换句话说，就是通过延迟写来提高同步性能;
4. 内核无锁第四层级 — 免锁,只有一个消费者和只有一个生产者的情形下，循环缓冲队列。

##### 多线程环境如何交叉输出A,B

```c
semaphore A=1,B=0;
threadA(){
  p(A);
  print 'A';
  V(B);
}
threadB(){
  P(B);
  print 'B';
  V(A);
}
```

##### 一个线程作为生产者往队列里面添加消息，一个线程作为消费者从队列里面取消息，除了CAS方法，还有其他方法lock-free的方法吗？

环形缓冲区是生产者和消费者模型中常用的数据结构。生产者将数据放入数组的尾端，而消费者从数组的另一端移走数据，当达到数组的尾部时，生产者绕回到数组的头部。

如果只有一个生产者和一个消费者，那么就可以做到免锁访问环形缓冲区（Ring Buffer）。写入索引只允许生产者访问并修改，只要写入者在更新索引之前将新的值保存到缓冲区中，则读者将始终看到一致的数据结构。同理，读取索引也只允许消费者访问并修改。

![](http://ougydqono.bkt.clouddn.com/lock_free,circularbuffer.png)

如图所示，当读者和写者指针相等时，表明缓冲区是空的，而只要写入指针的下一个位置是读取指针时，表明缓冲区已满。

##### CAS队列

```c++
void enqueue(int value){
    q=new node(value);
  	do{
        p=tail;
    }while(!cas(p->next,null,q));
    cas(tail,p,q);//若没有更新tail,其他cas操作会失败，因为此时tail->next不为空
}

int dequeue()
{
  	do{
        p=head;
        if(p->next==NULL){
            return EEmpty;
        }
    }while(!cas(head,p,p->next));
    return p->next->value;
}
```

###### CAS  ABA问题

什么是ABA问题？调包问题

考虑如下操作：

并发1（上）：获取出数据的初始值是A，后续计划实施CAS乐观锁，期望数据仍是A的时候，修改才能成功

并发2：将数据修改成B

并发3：将数据修改回A

并发1（下）：CAS乐观锁，检测发现初始值还是A，进行数据修改

上述并发环境下，并发1在修改数据时，虽然还是A，但已经不是初始条件的A了，中间发生了A变B，B又变A的变化，

此A已经非彼A，数据却成功修改，可能导致错误，这就是CAS引发的所谓的ABA问题。

###### 如何解决ABA问题

给数据加上版本号，打标签。double words CAS

##### 如何设计一个线程池以及一个系统中的线程池应该设置多少个合理

线程池的数量设置应该满足阻抗原理，CPU密集型还是IO密集型，以及计算机的核心数。定义P=CPU任务所占比例/（IO任务比例+CPU任务比例），核心数C，那么N=C/P;如果P<0.2，即IO密集型，那么这个公式就不适用了，应该应该设置一个固定值，比如N=5*C；

```c++
class ThreadPool{
  public:
  void start();
  void stop();
  void run(const Task& cb);
  private:
  	Task take();
  	void runInThread();
  	deque<Task> qeuue_;
  	Mutex mutex_;
  	Condition notEmpty_;
  	ptr_vector<Thread> threads_;
};
```

##### 设计一个进程池

考虑进程池的父子进程的通信方式，通过socketpair创建的全双工管道，进程池大小，CPU数目+1

##### BUFF满了怎么处理

个人理解，开一个应用层Buffer，应用层Buffer设置为动态增长的。能够不断接收socket接收的数据。如果应用层Buffer满了话就通知对方不要再发送数据过来了，等应用层Buffer有一定大小的空间的时候，再通知发送方继续发送。

##### 进程间通信的方式，以及每种方式的使用场景

###### 进程间通信方式

1. 信号
2. 信号量
3. 管道以及有名管道
4. Unix域套接字
5. 消息队列
6. 共享内存
7. socket通信

###### 使用场景

##### 线程间通信与同步方式

1. 互斥锁
2. 读写锁
3. 自旋锁
4. 条件变量
5. 信号量
6. 屏障

##### CPP文件怎么运行起来的，编译生成的二进制文件计算机是怎么识别的

##### 实现一个循环队列

front,tail初始化为0,tail指向下一个可用的位置。

```c++
int front=tail=0;
ValueType queue[Size];
bool empty(){
  return front==tail;
}
bool full(){
  return (tail+1)%Size == front;
}
void in(const ValueType& value){
  if(full())
    throw "Full queue";
  queue[tail]=value;
  tail=(tail+1)%Size;
}
const ValueType& out(){
  if(empty())
    throw "Empty queue";
  ValueType ret(queue[front]);
  front=(front+1)%Size;
  return ret;
}
```

##### 写一个队列的生产者消费者模式

```c++
deque<T> queue;
Mutex mutex;
Condition notEmpty(mutex);
T take(){
  mutex.lock();
  while(queue.empty()){
    notEmpty.wait();
  }
  T ret(queue.front());
  queue.pop_front();
  mutex.unlock();
  return ret;
}

void put(const T& something){
  mutex.lock();
  queue.push_back(something);
  notEmpty.notify();
  mutex.unlock();
}
```

##### 删除文件（可能为文件夹，写伪代码）（ListFile(),File数组，delete）

```c++
void delete(F f)
{
  if(f.isDir){
    File files[] = ListFile(f);
    for(auto file:files){
      delete(file);
    }    
  }
  else if(f.isFile)
    removeFile(f);
}
```

##### 设计模式篇

###### 单例模式

```c++
template<typename T>
class Singleton<T>{
  public:
  static T* instance(){
    pthread_once(&once,&init);
    return value_;
  }
  private:
  	Singleton();
    ~Singleton();
  	Singleton(const Singleton&);
  	Singleton& operator=(const Singleton&);
  private:
  	static pthread_once_t once;
  	static T* value_;
  	static void init(){
      value_ = new T;
    } 	
};
template<typename T>
pthread_once_t Singleton<T>::once=PTHREAD_ONCE_INITIALIZER;
template<typename T>
  T* Singleton<T>::once=NULL;
```

###### 单例模式的应用场景

1. 读取配置文件的对象
2. 日志文件对象
3. windows任务管理器

###### 单例模式的优缺点

1. 优点：避免了对共享资源的多重占用；由于在系统内存中只存在一个对象,因此可以节约系统资源,当需要频繁创建和销毁的对象时单例模式无疑可以提高系统的性能;提供了对唯一实例的受控访问；
2. 缺点：单例模式没有抽象层,扩展性差;单例模式不能保存状态,不适用于对象带多个状态的场景;单例类的职责过重,在一定程度上违背了“单一职责原则”。

##### 工厂模式

###### 简单工厂方法

```c++
class Item{};
class Soap:public Item{};
class Apple:public Item{};
class Factory{
  public:
  	Item* produce(enum ItemType){
      switch(ItemType){
        case SOAP:
          return new Soap();
          break;
        case Apple:
          return new Apple();
      }
  	}
};
```

###### 工厂模式

```c++
class Item{
  virtual void func()=0;
  virtual ~Item(){}
};
class Soap:public Item{
  virtual void func(){
  }
  virtual ~Soap(){}
};
class Apple:public Item{
   virtual void func(){
  }
  virtual ~Apple(){}
};
class Factory{
  public:
  	 virtual Item* produce()=0;
     virtual ~Factory(){}
};
class AppleFactory:public Factory{
  public:
   virtual Item* produce(){
     return new Apple();
   }
   virtual ~AppleFactory() {}
}
class SoapFactory:public Factory{
  public:
   virtual Item* produce(){
     return new Soap();
   }
   virtual ~SoapFactory() {}
}
```

###### 观察者模式

```c++
class Observer {
  public:
  	Observer(){};
    virtual ~Observer(){};
  	virtual void update()=0;
};
class Blog{
  public:
  	Blog(){};
  	virtual ~Blog() {};
  	void notify(){
      
  	}
  	void register(Observer* ob){
      observers_.push_back(ob);
  	}
    void remove(Observer* ob){
      observers_.remove(ob);
    }
  	void notify(){
      for(auto val:observers_){
        val->update();
      }
  	}
  private:
  	list<Observer*> observers_;
};
```

###### 策略模式

```c++
class AbstractStrategy{
  public:
  	virtual void func()=0;
};
class  ConcretStrategyA:public AbstractStrategy {
  public:
  	void func(){
      cout<<"A";
  	}
};

class  ConcretStrategyB:public AbstractStrategy {
  public:
  	void func(){
      cout<<"B";
  	}
};

class Owner{
  public:
  void func(){
    if(s_){
      s->func();
    }
  }
  void setS(AbstractStrategy* s){
    s_ = s;
  }
  private:
  AbstractStrategy* s_;
}

```

###### 责任链模式

避免请求发送者与接收者耦合在一起，让多个对象都有可能接收请求，将这些对象连接成一条链，并且沿着这条链传递请求，直到有对象处理它为止。

感觉try,catch,throw就是一种责任链模式

```java
public abstract class AbstractLogger {
   public static int INFO = 1;
   public static int DEBUG = 2;
   public static int ERROR = 3;

   protected int level;

   //责任链中的下一个元素
   protected AbstractLogger nextLogger;

   public void setNextLogger(AbstractLogger nextLogger){
      this.nextLogger = nextLogger;
   }

   public void logMessage(int level, String message){
      if(this.level <= level){
         write(message);
      }
      if(nextLogger !=null){
         nextLogger.logMessage(level, message);
      }
   }

   abstract protected void write(String message);
	
}
public class ConsoleLogger extends AbstractLogger {

   public ConsoleLogger(int level){
      this.level = level;
   }

   @Override
   protected void write(String message) {		
      System.out.println("Standard Console::Logger: " + message);
   }
}
public class ErrorLogger extends AbstractLogger {

   public ErrorLogger(int level){
      this.level = level;
   }

   @Override
   protected void write(String message) {		
      System.out.println("Error Console::Logger: " + message);
   }
}
public class FileLogger extends AbstractLogger {

   public FileLogger(int level){
      this.level = level;
   }

   @Override
   protected void write(String message) {		
      System.out.println("File::Logger: " + message);
   }
}
```

###### 状态模式 

自动机

###### 适配器模式

```c++
class Program
    {
        static void Main(string[] args)
        {
            China china = new Adapter();
            china.Request();

            Console.Read();
        }
    }

    //国内供电
    class China
    {
        public virtual void Request()
        {
            Console.WriteLine("the voltage is 220v");
        }
    }

    //用电器供电特殊需求
    class Foreign
    {
        public void SpecificalRequest()
        {
            Console.WriteLine("the voltage is 110v");
        }
    }

    //电源适配器
    class Adapter : China
    {
        private Foreign foreign = new Foreign();

        public override void Request()
        {
            foreign.SpecificalRequest();
        }
    }
```

### 数据库

#### 为什么关系型数据库使用行存储

因为列存储不好加锁

#### 什么时候设置了索引但是无法使用

1. 以%开头的like语句，模糊匹配
2. not in,not exist
3. 数据类型出现隐式转换
4. 索引列上使用了计算
5. 单独引用联合索引里非第一位置的索引列
6. ...

#### 数据库的锁

1. 表锁
2. 行锁
3. 乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性，不能解决脏读的问题。
4. 悲观锁：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。

#### 数据库范式

1. 第一范式：数据库的每一列都是不可分割的原子数据项。
2. 第二范式：要求实体的属性完全依赖于主关键字，所谓完全依赖是指不能存在仅依赖于主关键字一部分的属性。
3. 第三范式：任何非主属性不能依赖于其他非主属性。消除传递依赖。
4. BCNF
5. 第四范式
6. 第五范式（完美范式）

##### 一般来说数据库只需满足第三范式就行了。

#### 数据库查询优化

1. 用union all代替union,union会对结果去除，可能会造成磁盘排序；
2. 用EXISTS替代IN、用NOT EXISTS替代NOT IN，NOT IN子句将执行一个内部的排序和合并；
3. 避免在索引列上使用计算，这样会导致无法使用索引；
4. 优化group by，提高GROUP BY 语句的效率, 可以通过将不需要的记录在GROUP BY 之前过滤掉；
5. 用Where子句替换HAVING子句，避免使用HAVING子句, HAVING 只会在检索出所有记录之后才对结果集进行过滤. 这个处理需要排序,总计等操作；
6. ....

##### 联合索引

范围列可以用到索引（必须是最左前缀），但是范围列后面的列无法用到索引。同时，索引最多用于一个范围列，因此如果查询条件中有两个范围列则无法全用到索引。

#### 如何做分页显示

##### 如果只支持下一页和上一页的话

按id降序排列显示的话，如果一页显示20条记录，当前页为第10页的话id最小2000，最大2019.

###### 本页

```sql
select * from table where id>=2000 ordered by id asc limit 20;
```

###### 上一页

```sql
select * from table where id <2000 ordered by id desc limit 20;
```

###### 下一页

```sql
select * from table where id >2019 ordered by id asc limit 20;
```

##### 如果要支持跳转到某一页

###### 本页

```sql
select * from table where id>=2000 ordered by id asc limit 0,20;
```

###### 上一页的上一页

```sql
select * from table where id<2000 ordered by id dec limit 20,20;
```

###### 下一页的下一页

```sql
select * from table where id>2019 ordered by id asc limit 20,20;
```

#### 如何删除一个表中大部分数据

可以考虑创建一个新表，insert要保留的数据，然后切换表，删除旧表

#### 在表中插入10000条数据，如何提高性能

个人理解，要分存储引擎，要是MyISAM可以考虑禁用索引，插入数据后，再重启索引。要是Innodb的话估计只能把插入的数据按主键排序插入。

1.数据表中有索引，可以考虑先删除索引，待到导入数据后再重新建立索引

2.写一个事务用来完成这个插入操作。

3.如果插入的数据是再索引的后面，可以按主键顺序插入数据。

#### 数据库中表与类的关系，如何存储对象

表中的字段就好比类中的数据成员。

#### 索引的分类

1. 聚簇索引（Innodb默认的主索引，索引的data就是存放的表的行数据）
2. 全文索引（MyISAM支持，用来做关键字查询的，搜索引擎）
3. B树索引（Innodb b+树，MyISAM b树）
4. 联合索引（多个字段的联合索引，最左匹配）
5. 覆盖索引（索引的data段包含了查询需要的所有字段的数据）
6. 倒排索引
7. 哈希索引
8. 压缩索引（MyISAM支持）

##### 怎么创建索引

```sql
create index name on table(key1,key2);
```

#### SQL语法

1. join  关联查询，分为左外连接，右外连接，全外连接，内连接
2. union 联合查询，把2次查询的结果合并成一个结果，2次查询所包含的字段必须一致。默认是union unique会进行去重，效率低，可以使用union all加快查询速度，不用排序再去重
3. 子查询  在子查询结果中查询 select * from table1 where exist (select a from table1 where a>10)
4. having  过滤，类似where的用法
5. group by 对数据进行进行分组，可以用多个字段表示分组的依据。

#### sql查一天各种类型的新增用户

```sql
select usr_id from table where create_time>yestoday.time group by user_type;
```

#### drop,delete,truncate三者的区别

##### truncate通过drop table和create table语句来实现高效清空数据。

1. delete只删除数据不删除表的结构。drop删除表的结构以及被依赖的约束，触发器和索引。truncate会重建表。
2. delete是DML(数据操作语言)，事务提交后才可以生效，可以回滚；drop和truncate是DDL（数据定义语言），隐式提交，不能回滚。
3. 从速度上来说，drop>truncate>delete

#### char和varchar的最大长度

非空char 255,非空varchar 65533,可空char 254,可空varchar 65532。

#### 事务的特性

##### 原子性

事务内的语句，要么全部执行成功，要么全部执行失败。

##### 一致性

事务执行的结果必须是使数据库从一个一致性状态变到另一个一致性状态。

一致写：事务执行的数据变更只能基于上一个一致的状态，且只能体现在一个状态中。T(n)的变更结果只能基于C(n-1)，C(n-2), ...C(1)状态，且只能体现在C(n)状态中。也就是说，一个状态只能有一个事务变更数据，不允许有2个或者2个以上事务在一个状态中变更数据。至于具体一致写基于哪个状态，需要判断T(n)事务是否和T(n-1)，T(n-2),...T(1)有依赖关系。

一致读：事务读取数据只能从一个状态中读取，不能从2个或者2个以上状态读取。也就是T(n)只能从C(n-1），C(n-2)... C(1)中的一个状态读取数据，不能一部分数据读取自C(n-1)，而另一部分数据读取自C(n-2)。

##### 隔离性

通常来说，一个事务所做的修改在最终提交以前，对其他事务是不可见的。

##### 持久性

一旦事务提交，则其所做的修改就会永久保存到数据库中。

#### 隔离级别

1. 未提交读 （指未提交的事务对数据库所做的修改对其他事务可见，存在问题：脏读）
2. 提交读 (事务只能看见已提交的事务对数据库所做的修改，存在问题：不可重复读)
3. 可重复读（同一个事务2次读取的同样的数据保持一致,存在问题:幻读，指其他事务向查询范围内插入了一条新的数据，2次查询的结果可能会不同，第二次查询会引入那条新插入的数据）
4. 可串行化（事务串行化。读写每一条记录都要加锁，存在大量的锁争用问题。可以解决幻读问题）

##### INNODB默认的隔离级别：可重复读，其MVCC控制可以解决幻读问题

###### MVCC

事务看到的数据是基于某个时间点的快照，每行数据后面隐藏2个字段，创建时间和删除时间，内部是用版本号来标记这2个时间，每创建一个事务，就给事务赋予一个版本号，当事务提交时候，系统的版本号设置为刚提交的事务版本号，版本号随着时间的增加是递增的。事务通过比较自己的事务版本号是否在创建版本号和删除版本号中间来确定数据对于本事务是否有效。

#### mysql各个存储引擎的区别，以及各个存储引擎的优缺点

###### 区别

InnoDB:支持外键和事务，以及支持表锁跟行锁，能缓存数据和索引。

MyISAM:不支持外键和事务，只支持表锁，支持全文索引，只会缓存索引不会缓存数据。

NDBCluster：分布式存储引擎，支持事务。内存需求量大，索引以及被索引的数据必须存放在内存中。

###### 优缺点

InnoDB:具有非常高效的缓存特性，既可以缓存数据，又可以缓存索引。支持4个事务隔离级别。（优点：支持事务，支持外键，并发量大，适合大量update，缺点：不适合大量select）

MyISAM:并发性相对较低。数据一致性要求不是非常高。读写互相阻塞，读取的时候阻塞写入。(优点：查询数据相对较快，适合大量select,可以全文索引,索引是有压缩的，能加载更多索引。缺点：不支持事务，不支持外键，并发量较小，不适合大量update。)

#### Redis集群中，一个节点挂了，其他结点怎么知道的

#### Redis有什么数据类型，key的快速查找是怎么实现的。

字符串，集合，列表，散列表，有序集合。

#### A/B表有一个相同的字段M/N，写出A.M-B.N的SQL语句，及取出属性M中而属性N没有的元组

```sql
select M from A minus select N from B;
```

#### B树和B+树，B树，B+树有什么优点

##### 相对于二叉查找树

1. 存储相同多的数据，B树和B+的高度低得多，大大减少磁盘IO的存取次数;
2. 一个结点可以存更多的k,v。cache命中率更高;

##### B+相对于B树

1. 内结点相当于存储索引信息帮助搜索叶节点，内节点不包含数据部分信息，使内节点相对B树更小，磁盘上一个扇区可以存储更多内节点，也就可以顺序获取更多的索引信息。相对来说磁盘IO次数就少了。
2. B+树相对于B树查找效率更加稳定。必须从根结点查找叶子结点。
3. B+树的叶子结点相连。更方便扫库和范围查找。而B树则要通过中序遍历方式按序扫库。

#### 数据库左连接和右连接

###### 左连接，会把左表中不符合连接条件的记录显示出来。

###### 右连接，会把右表中不符合连接条件的记录显示出来。

##### 数据库索引原理，数据库联合索引ABC,只查询AC可以吗？

索引是一种满足特定查找算法的数据结构，这些数据结构以某种方式指向数据，这样就可以在这些数据结构上实现高级查找算法。

### 网络

#### IP头部

不带选项20字节

标识符（2字节），校验和（2字节），源IP（4字节），目的IP（4字节），首部长度（4位），片偏移。TTL（1字节），版本号（4位），协议类型（1字节），标志位（3字节，允许分片，是否分片，片号？）总长度(2字节，包含头部和数据)，服务类型（1字节）

##### TTL字段

TTL字段的目的是防止数据报在选路时无休止地在网络中流动。

##### Traceroute

Traceroute程序发送一份UDP数据报给目的主机，但它选择一个不可能的值作为UDP端口。Traceroute程序所要做的就是区分接收到的ICMP报文是超时还是端口不可达，以判断什么时候结束。

###### 当路由器收到一份IP数据报，如果其TTL字段是0或1,则路由器不转发该数据报（目的主机可以将它交给应用程序，这是因为不需要转发该数据报。但是在通常情况下，系统不应该接收TTL字段为0的数据报）。

#### TCP头部信息

不加选项20字节，加选项最多60字节

序列号（4字节），确认号（4字节），源端口（2字节），目的端口（2字节），首部长度（4位，半字节），标志位（FIN,ACK,SYN,RST,PUSH,URG，6位，保留6位），窗口大小（2字节），紧急指针（2字节），校验和（2字节）

#### UDP头部信息

8字节

UDP长度（2字节，包括首部和数据），源端口（2字节），目的端口（2字节），校验和（2字节）

#### TCP滑动窗口协议

用于网络数据传输时的流量控制，以避免拥塞的发生。发送方滑动窗口左边是已经发送并确认的，右边是还未发送的，滑动窗口中的是已经发送但是还未被确认的。

#### DNS

#### ARP

#### Epoll

##### EPOLL边缘触发，EPOLLONSHOT

对于注册了EPOLLONESHOT事件的文件描述符，操作系统最多触发其上注册的一个可读，可写，或者异常事件，且只触发一次，除非我们使用epoll_ctl函数重置该文件描述符上注册EPOLLONESHOT事件。

##### EPOLL为什么更高效

1. EPOLL在内核维护了一个事件列表（红黑树结构），我们可以通过epoll_ctl直接添加，更改或者删除我们需要操作的fd。而无须每次epoll的轮询都从用户空间拷贝关注事件列表到内核空间。
2. 当有就绪事件时候，内核调用对应fd上的回调函数将fd及其就绪事件添加到就绪列表中，然后唤醒wait的进程。通过epoll_wait获取的事件数组中都是就绪的事件。我们只需线性扫描就绪事件数组。而poll需要在poll返回后线性扫描之前的整个事件数组中是否发生了关注的事件。

##### 当监听的fd数较多，而活跃连接数较少时，epoll更高效

#### UNIX域套接字

##### socketpair

###### read  ret<=0意味着什么？

read返回0表示对端已经关闭。

#### TCP/IP 5层网络模型，各层作用

##### 应用层

##### 传输层

##### 网络层

##### 链路层

##### 物理层

#### 一个IP配置多个域名，靠什么识别

#### 什么是NAT，主要是做什么的？

网络地址转换，IP数据包在通过路由器或者防火墙时候用来更改源IP和目的IP的技术。用于进行公网IP和内网IP之间的转换。

#### HTTP相关

##### Get和Post方法比较

get用于从指定的资源请求数据，post用于向指定的资源提交数据。

###### Get

1. get请求可被缓存
2. get请求可以保留在浏览器历史纪录里面
3. get请求可被收藏为书签
4. get请求有长度限制

###### Post

1. post请求不会被缓存
2. post请求不会保留在浏览器历史记录中
3. post不能收藏为书签
4. post请求对数据长度没有要求

##### http状态码403,404,304,500,502，503

###### 302 Redirect

重定向，暂时性转移(Temporarily Moved )

###### 304 Not Modified

服务器端资源未改变，可直接使用客户端未过期的缓存。

###### 403   Forbidden

请求资源的访问被服务器拒绝。

###### 404 Not Found

服务器上没有请求的资源。

###### 500 Internal Server Error

服务器内部发生了错误

###### 502  Bad gateway

而是上游服务器和网关/代理不同意的协议交换数据。

###### 503  Service Unavailable

服务器暂时处于超负载或正在停机维护，现在无法处理请求。

##### cookie和session的区别

1. cookie存放在客户端
2. session存放在服务器端



##### http如何做断点续传

1. 客户端HTTP请求报文头部指定range字段，告诉请求的范围（0-801）
2. 服务器端响应报文头部字段指定content-range字段，告诉响应的范围以及总长度（0-800/801）
3. If-Range = “If-Range” “:” ( entity-tag | HTTP-date ),IF-Range头部需配合Range，如果没有Range参数，则If-Range会被视为无效。

###### 如果If-Range匹配上(可以通过文件的MD5值匹配)，那么客户端已经存在的部分是有效的，服务器将返回缺失部分，也就是Range里指定的，然后返回206（Partial content)，否则证明客户端的部分已无效（可能已经更改），那么服务器将整个实体内容全部返回给客户端，同时返回200OK

#### HTTPS

##### https的优势

1. 身份认证
2. 数据完整性校验
3. 数据加密

##### 由http升级为https需要做哪些工作

##### 客户端为什么要信任第三方证书

为了用来解密服务器发过来的带公钥的证书的签名，进行身份认证和完整性校验。

##### HTTPS 心脏流血漏洞

OpenSSL的心跳处理逻辑没有检测心跳包中的长度字段是否和后续的数据字段相符合，攻击者可以利用这点，构造异常的数据包，来获取心跳数据所在的内存区域的后续数据。可能包含私钥，用户名和密码，用户邮箱等敏感信息。

#### TCP为什么是3次握手，而不是2次或4次

原则上任何数据传输都无法确保绝对可靠，3次握手只是确保可靠的基本要求

3次握手可以保证双方发送数据和接收数据功能正常。如果2次握手，服务器端就无法知道客户端能接收数据。

##### TCP三次握手中，第三次握手失败怎么办

第三次握手失败时，失败的服务器并不会重传SYN+ACK，而是发送RST，这样做的目的是防止SYN洪泛攻击

#### 带外数据

带外数据如果没有读取，不阻塞后续的普通数据的读取。

#### Socket什么时候可读

1. 接收缓冲区数据多于读低水位值
2. 有外来连接
3. 对端关闭写，收到FIN
4. 带外数据到达或者连接发生错误

#### 内核如何解决惊群现象

##### 新版内核的accept解决了惊群问题

系统内核只会唤醒所有正在等待此事件的队列的第一个。

##### EPOLLEXCLUSIVE选项

##### NGINX通过一个accpet_mutex，多个进程根据当前的负载情况来判断是否竞争accept_mutex,只有获取accecpt_mutex的锁才会将listenfd添加到自己的epoll事件列表中。

##### UDP模拟TCP

1. 序列号
2. 超时重传
3. 确认应答
4. 流量控制

##### UDP如何实现流量控制

模拟滑动窗口算法，当丢包率超过一个比例的时候，我们就减小发送窗口。

#### TCP的慢启动，拥塞避免和快速重传和快速恢复

##### 慢启动

1.慢启动门限值设置为65535

2.cwnd设置为1个mss，每收到一个ack，cwnd+本次对方一共确认的字节数，指数增长

##### 拥塞避免

当cwnd达到慢启动门限值时候，保证一个RTT内cwnd最多增加一个mss。线性增长

##### 拥塞检测

1. 重传定时器超时
2. 连续收到3个重复的ACK

##### 重传定时器超时

慢启动门限值设置为当前cwnd的一半，然后cwnd设置为1mss，重新进入慢启动阶段并重传丢失的报文

##### 快速重传和快速恢复

1.慢启动门限值设置为当前cwnd一半

2.cwnd设置为新的慢启动门限值+3mss

3.重传丢失的报文，每收到一个重复的ack,cwnd+一个mss

4.接收到新的ack,cwnd设置为新的慢启动门限值，进入拥塞避免状态

#### TCP如何保证可靠性

1.校验和

2.超时重传

3.重复报文的丢弃

4.失序报文的重组

5.流量控制

6.ack机制

7.TCP分段机制，将应用进程数据切分成最合适的数据块发送出去，避免IP分片

#### timewait

##### 作用

1. 实现可靠的终止TCP全双工连接，如果最后一个ack丢失的话，对方可以重传FIN.
2. 使旧的重复的分节在网络中流逝，不影响下一个此连接的化身。

##### 危害

占用服务器的性能和资源（描述符，端口）

###### shutdown相对于close有哪些好处

1. shutdown不需要等待fd引用计数为0才执行关闭连接。
2. shutdown允许半关闭连接。

#### socket选项

##### SACK选项

TCP通信时，如果发送序列中间某个数据包丢失，TCP会通过重传最后确认的包开始的后续包，这样原先已经正确传输的包也可能重复发送，急剧降低了TCP性能。为改善这种情况，发展出SACK(Selective Acknowledgment, 选择性确认)技术，使TCP只重新发送丢失的包，不用发送后续所有的包，而且提供相应机制使接收方能告诉发送方哪些数据丢失，哪些数据重发了，哪些数据已经提前收到等。

##### write低水位标记，read低水位标记

tcp,udp的默认write低水位标记2048，read低水位标记1

##### SO_REUSEADDR和SO_REUSEPORT

###### SO_REUSEADDR

单播情况下：

1.如果设置了改选项，如果当前bind的<id,port>处于timewait状态，则可以bind成功，若处于其他状态，则失败

2.如果有一个socket bind的是<INADDR_ANY,port>,则之后的<ip,port>若要bind成功，则需要设置此选项。或者有一个socket bind的是<ip,port>,之后的socket要bind<INADDR_ANY,port>或<ip2,port>(ip2≠ip)，若要成功，则需要设置此选项。

绑定的成功与否只会检查当前bind的socket是否开启了这个标志，不会查看其它的socket。

3.补充一点，当设置了SO_REUSEADDR选项时，UDP协议支持同样的IP地址和端口再捆绑到另外一个套接字上（UNP 166页），**试验后不行。**

SO_REUSEPORT（如果设置该选项生效，允许重复bind到相同的<ip,port>二元组）

场景：服务端多个 listen socket （多进程/线程）可以 bind 重复的 ip/port。当多线程/进程阻塞在 epoll_wait/poll/select ，此时来了新 connect，只会有一个 最后一个创建的listen fd 收到事件通知。实现了内核级别的负载均衡。

如果第一个绑定某个地址和端口的socket没有设置SO_REUSEPORT，那么其他的socket无论有没有设置SO_REUSEPORT都无法绑定到该地址和端口直到第一个socket释放了绑定。

当有个socket绑定后处在TIME_WAIT状态(释放时)时，为了使得其它socket绑定相同地址和端口能够成功，需要设置SO_REUSEADDR或者在这两个socket上都设置SO_REUSEPORT。

###### 多播情况下：

 对多播地址来说，SO_REUSEADDR的含义发生了改变，因为它允许多个socket绑定到完全一样的多播地址和端口，也就是说，对多播地址SO_REUSEADDR的行为与SO_REUSEPORT对单播地址完全一样。事实上，对于多播地址，对SO_REUSEADDR和SO_REUSEPORT的处理完全一样，对所有多播地址，SO_REUSEADDR也就意味着SO_REUSEPORT。

#### TCP数据校验怎么做的

每16位取反求和，填入checksum字段，接收方也进行每16位取反求和，由于接收方计算过程中包含了检验和，如果传输过程中，数据没有被修改，则计算结果为全1

#### 子网掩码的作用

用来划分网络地址和主机地址，子网掩码不能单独存在，它必须结合IP地址一起使用。

#### ICMP哪一层？交换机和路由器的区别？

ICMP工作在网络层（IP头部）。交换机工作在链路层，路由器工作在网络层（网络层网关），交换机转发广播，路由器不转发广播。交换机一次路由，多次转发。路由器一次路由一次转发。

#### 怎么判断客户端关闭tcp,udp分别说明

tcp关闭，read返回0.udp的话，服务器与客户端的udp套接字必须是connected的，**当客户端关闭了udp时，如果服务端继续read或write数据，会收到异步错误ECONNREFUSED。**

#### 如果发送了紧急指针，对方会做出什么反应。接收到带push标志的数据，对方会做出什么反应

###### 带URG标志

对方应用程序会收到内核产生的SIG_USR信号，我们可以通过设置信号处理函数，用带MSG_OOB标志的recv来接收紧急指针指向的那一位数据。或者通过IO复用函数，接收到描述符上的异常事件。

**即使接收方的通告窗口为0，发送方仍然可以发送不带任何数据的紧急报文。**

###### 带push标志

对方会立即将接收缓冲区里面已有的数据以及带push标志的数据立即交付给应用进程，而不等待判断是否还有额外的数据到达。**TCP依据当前将要被发送的包是不是发送缓冲区中最后一个未被发送的包，如果是那么这个包将标志push。**

#### 发送窗口怎么才能知道自己不能再发了？怎么通知的？

通过对方通知的窗口大小

#### 让你设计如何提高TCP的传输效率，Google对TCP的改进

1. 重传算法的改进
2. 从WIFI切换到移动网络的能否不重新建立连接
3. 从建立连接消耗时间跟传输数据时间方面做优化

##### GOOGLE做了哪些工作

1. Google改进了TCP快速重传快速恢复算法的改进 TCP BBR算法
2. QUIC(Quick UDP Internet Connections)协议

#### 可靠的UDP

##### 云风的blog

一条路是寄希望于业务逻辑上允许信息丢失：比如，在同步状态中，如果状态是有实效性的，那么过期的状态信息就是可丢失的。这需要每次或周期性的全量状态信息同步，每个新的全量状态信息都可以取代旧的信息。或者在同步玩家在场景中的位置时可以用这样的策略。不过在实际操作中，我发现一旦允许中间状态丢失，业务层将会特别难写。真正可以全量同步状态的场合也非常少。

那么，不允许信息丢失，但允许包乱序会不会改善? 一旦所有的包都一定能送达，即丢失的包会用某种机制重传，那么事实上你同样也可以保证次序。只需要和 TCP 一样在每个包中加个序号即可。唯一有优势的地方是，即使中间有包晚到了，业务层有可能先拿到后面的包处理。

什么情况下是包次序无关的呢？最常见的场合就是一问一答的请求回应。采用这种方式的， UDP 在互联网上最为广泛的应用，就是 DNS 查询了。

最后一个实现出来的版本是这样的：

通讯是双向的，每边都可以是数据生产方 P 或数据消费方 C。

每个逻辑包都有一个 16bit 的序号，从 0 开始编码，如果超过 64K 则回到 0 。通讯过程中，如果收到一个数据包和之前的数据包 id 相差正负 32K ，则做一下更合理的调整。例如，如果之前收到的序号为 2 ，而下一个包是 FFFF ，则认为是 2 这个序号的前三个，而不是向后一个很远的序号。

若干逻辑包可以打包在一个物理包内，但一个物理包尽可能的保证在 512 字节内，超过则分成多个包。但每个逻辑包都不会分拆在不同的物理包中。

如果需要生产方 P 重发一个特定序号的包，消费方 C 可以发起一个请求。多个请求可以打包在同一个物理包内，也可以和待发送的逻辑包打包在一起。

这里采用请求机制，而不是 TCP 那样的确认机制，是因为在特定条件下，请求机制实现更简单。正常网络状况下，无论是缺少包（发现收到的逻辑包序号不连续）再向对端请求，还是让消费方 C 去确认收到了哪些包，生产方 P 发现未请求的包主动重发；都是极其稀少的事情，其差别可以忽略。

主要区别在于，采用请求重发机制要求 P 方尽可能的保留已发出的数据，正常通讯条件下， 缺少确认机制会导致 P 不敢随意丢弃过去发出的数据。但在这里，我们可以依据超时来清理过期的数据，也就回避了这个问题。

除此之外，我们还需要在没有数据时，有可以维持心跳的空包，以及发生异常时通知对方异常的机制。

最终，有四类固定格式的数据：

- 0 心跳包
- 1 连接异常
- 2 请求包 (+2 id)
- 3 异常包 (+2 id)

后两种数据需要跟上两字节的序号（采用大端编码）

普通的数据包可以直接采取长度 + id + 数据的方式。

这五类数据均可以统一采用 tag + 数据的方式编码。如果是前四种数据，就在 tag 部分直接编码 0~3 ，如果是最后一种数据包，则将 tag 编码为编码 (数据长度 + 4)

tag 采用 1 或 2 字节编码。如果 tag < 127 编码为 1 字节，tag 是 128 到 32K 间时，编码为两字节；其中第一字节高位为 1 。tag 不能超过 32K 。

顺便说一句，纸上谈兵谁都会，例如固定MTU。但是可以很负责的告诉你，很多路由特别是企业路由，仅允许每次通过512字节的UDP。所以QQ是会在登录期间测试这个值。

##### UDT QUIC,ENET

###### QUIC 

1. 减少握手次数 1到2次握手

2. 内置TLS栈

3. 避免前序包阻塞

4. 向前纠错（QUIC协议有一个非常独特的特性，称为向前纠错（Forward Error Correction），每个数据包除了它本身的内容之外，还包括了部分其他数据包的数据，因此少量的丢包可以通过其他包的冗余数据直接组装而无需重传。)

5. 会话重启和并行下载（底层协议切换到UDP协议之后的另一大好处是，连接不再依赖于来源IP。 

   对于TCP协议来说，标识一个TCP连接需要4个参数，既来源IP、来源端口、目的IP和目的端口。其中的任一参数改变，TCP连接就需要重新创建。 

   这对于传统网络来说影响不大，因为来源和目的IP相对固定。但是在无线网络中，情况就大不相同了。设备在移动过程中，可能会因为网络切换（如从WIFI网络切换到4G网络环境），导致TCP连接需要重新创建。 

   QUIC协议使用了UDP协议，不再需要这四元组参数。同时QUIC协议实现了自己的会话标记方式，称为连接UUID。当设备网络环境切换时，连接UUID不会发生变化，因此无需重新进行握手。 

   该特性除了可以减少无谓的连接重连之外，还可以充分利用设备的不同网络接口，进行资源的并行下载。因为虽然这些网络接口有不同的IP，但只要他们能够共享连接UUID，就能够并行的从服务器下载数据。）

#### select,poll,epoll的区别

##### 在一个浏览器里面输入一个网址，后回车，在这后面发生了什么

首先进行DNS域名解析出IP地址，然后ARP获得目的MAC地址，然后进行TCP三次握手过程，通过HTTP协议发送请求与接收响应，浏览器解析出响应中的content，最后呈现出页面。

##### HTTP的长连接和短连接是什么，各有什么优缺点，然后使用场景。

长连接，数据传输完成之后保持TCP不断开。长连接避免了大量TCP短连接每次建立/释放产生的各种开销

### Others

#### 负载均衡，高并发，高可用的架构

##### 负载均衡算法

###### 随机算法

###### 轮询法

**NGINX负载均衡平滑加权轮询算法**

每个服务器都有两个权重变量：a：weight，配置文件中指定的该服务器的权重，这个值是固定不变的；b：current_weight，服务器目前的权重。一开始为0，之后会动态调整。

 每次当请求到来，选取服务器时，会遍历数组中所有服务器。对于每个服务器，让它的current_weight增加它的weight；同时累加所有服务器的weight，并保存为total。遍历完所有服务器之后，如果该服务器的current_weight是最大的，就选择这个服务器处理本次请求。最后把该服务器的current_weight减去total。

###### 服务调用时延

###### 一致性哈希

###### 粘滞连接

#### redis缓存，redis的集群部署，热备份，主从备份，主从数据库，hash映射找到知道指定节点

#### docker和虚拟机的区别

1. 从框架上来说，VM比Docker多了一层guest OS(客户机操作系统),Docker守护进程可以直接跟主操作系统进行通信，为各个Docker容器分配资源，以及保证隔离性。
2. VM会对硬件资源进行虚拟化，docker直接使用硬件资源


##### Redis跟memcache的区别

1. Redis支持持久化和主从复制
2. Redis支持5种数据类型
3. Redis单线程，Memcache多线程
4. Redis采用动态申请内存方式，Memcache采用的是预分配内存，内存池

##### Redis事务

采用事务队列的方式，MULTI开始，EXEC结束

##### 内核参数调优

1.net.ipv4.tcp_syncookies

2.net.ipv4.tcp_syn_retries  #syn重传次数

3.net.ipv4.sack=1  selective Acknowledgment

4.net.ip.tcp_max_syn_backlog=8192

5.net.ipv4.tcp_tw_reuse = 1

6.net.ipv4.tcp_tw_recycle = 1

7.net.ipv4.tcp_max_tw_buckets = 10000  #表示系统同时保持TIME_WAIT套接字的最大数量

8.net.inet.udp.checksum=1 #启动UDPchecksum检查

#### linux系统命令

###### 如何查看路由表

```shell
netstat -rn
```




