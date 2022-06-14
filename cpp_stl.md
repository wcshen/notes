## 1.自定义sort函数

在使用vector容器时经常要进行排序，使用排序函数sort非常方便，但是之前都是简单调用sort(v.begin(), v.end());没有自定义排序规则使用sort函数的额第三个参数，下面对sort总一个简单总结。

头文件：`#include <algorithm>`

第三个参数compare，是个自定义的比较函数的指针，名字可以随便命名；原型如下：

 `bool cmp （const Type1 ＆a，const Type2 ＆ b ）;`

简单说来就是：

> 比较a和b，如果是想升序，那么就定义当a<b的时候返回true；
>
> ​					如果是想降序，那么就定义当a>b的时候返回true；

代码举例

```C++
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

bool cmp(const int& a, const int& b)
{
	return a > b; //从大到小排序
}

int main()
{
	int a[10] = {2, 3, 30, 305, 32, 334, 40, 47, 5, 1};
	vector<int> nums(a, a + 10);

    sort(nums.begin(), nums.end(), cmp);

    for(auto x : nums)
        cout << x;
    
    cout << endl;
    system("pause");
    return 0;
}
```

