<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-03-29 18:21:53
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-04-14 20:14:21
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/codingtests/华为诺亚方舟实验室-CodingTest.md
-->

# Huawei - CodingTest

### 01 - 编程题01

A为一个十进制整数，k位，k<100。求B使得B为大于A的最小整数，且A各位的和等于B各位的和。请给出函数实现，测试例，并说明解题思路。

---

**思路**：

首先，以暴力算法扫描的话时间复杂度过大，我们根据规律可以发现：

- 从A的倒数第二位开始，从后向前搜索，直到找到一个符合条件的位数`A[i]`，使其满足如下条件：
  - `A[i]`不等于9并且`A[i]`之后的所有位不全为`0`
  
这样，我们将满足以上要求的数据，按一下的策略进行修改：

- `A[i]`加1, 计算后面的所有位数之和记作`sum`， 并从最后一位向前，将位数和减去`9`并改为`9`
- 如果剩余无法补`9`则继续改为`sum` 若为0则补`0` 直到修改到`A[i]`
  
此外 还会有不满足以上要求的数字，则我们在开头先补上一个`1`，再按我们的规律进行修改；

**测试样例及对拍代码**：

对拍代码即暴力解 时间复杂度高 以此来验证代码的正确性;

**对拍代码（matcher.cpp)**：

```cpp
/*
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-03-26 18:48:04
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-03-22 21:23:26
 * @FilePath: /Projects/matcher.cpp
 */

#include <cstdlib>
#include <ctime>
#include <iostream>
const int N = 100;
const std::string bf = "../bf.cpp";
const std::string algo = "../Example.cpp";

/*
封装一下 `system`，支持 ctrl + c 退出整个对拍程序
*/
int mySystem(const char* command) {
    int result = system(command);
    if (WIFEXITED(result)) {
        // printf("Exited normally with status %d\n", WEXITSTATUS(result));
    } else if (WIFSIGNALED(result)) {
        printf("Exited with signal %d\n", WTERMSIG(result));
        exit(1);
    } else {
        printf("Not sure how we exited.\n");
    }
    return result;
}

int main() {
    mySystem("g++ ../random.cpp -o random.out");
    mySystem(
        ("g++ -std=c++11 -o bf.out " + bf).c_str());
    mySystem(
        ("g++ -std=c++11 -o algo.out " + algo).c_str());
    for (int T = 1; T <= N; T++) {
        // 生成随机数据
        mySystem("./random.out > data.in");
        // 记录运行的 CPU 时间
        double st = clock();
        // 暴力解法输出正确答案
        mySystem("./bf.out < data.in > data.ans");
        double ed = clock();
        // 优化解法输出待检查答案
        mySystem("./algo.out < data.in > data.out");
        // return 0;
        if (mySystem("diff data.out data.ans")) {
            puts("\033[1;31mWrong Answer\033[0m");
            puts("\033[1;31m-----错误数据-------\033[0m");
            printf("输入: \n");
            mySystem("cat data.in");
            return -1;
        } else {
            printf("\033[1;32mAccepted\033[0m, 测试点 #%d, 用时 %.01f\n", T, ed - st);
        }
    }
    return 0;
}
```

**随机测试样例生成代码（random.cpp)**:

```cpp
/*
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-03-22 20:49:05
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-03-29 19:12:58
 * @FilePath: /Projects/random.cpp
 */

#include <iostream>
#include <cstdlib>
#include <string>
#include <sstream>
#include <time.h>
#include "sys/timeb.h"

using namespace std;

int main()
{
    struct timeb timeSeed;
    ftime(&timeSeed);
    srand(timeSeed.time * 1000 + timeSeed.millitm);  // milli time
    // 产生[a,b)的随机数，可以使用 (rand() % (b-a))+a;
    // 产生[a,b]的随机数，可以使用 (rand() % (b-a+1))+a;
    // 产生(a,b]的随机数，可以使用 (rand() % (b-a))+a+1;
    int n = (rand() % (21 - 1 + 1)) + 1; // 避免前导零
    stringstream ss;
    ss << (rand() % (10 - 0 + 1)) + 1;
    for(int i = 0; i < n; i++)
        ss << (rand() % (10 - 0 + 1)) + 0;
    cout << ss.str() << endl;
    return 0;
}
```


**暴力算法（bf.cpp)**:
```cpp
/*
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-03-22 20:48:38
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-03-29 18:54:47
 * @FilePath: /Projects/bf.cpp
 */
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;
using ULL = unsigned long long;
const int N = 20;

int n;
int fact[N];

int sumA(ULL A)
{
    int sum = 0;
    while(A)
    {
        sum += A % 10;
        A /= 10;
    }
    return sum;
}
ULL findB(ULL A)
{
    for(ULL i = A + 1; i < ULLONG_MAX; i++)
        if(sumA(i) == sumA(A)) return i;
}
int main()
{
    ULL A;
    cin >> A;
    cout << findB(A) << endl;
    return 0;
}
```

**代码（Example.cpp)**：

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
#include <sstream>
using namespace std;

vector<int> num;
string tmp;

int main() {
    stringstream ss;
    cin >> tmp;
    // 1. 高精度按位存
    for (int i = 0; i < tmp.size(); i++)
    {
        num.push_back(tmp[i] - '0');
    }

    if (num.size() == 1)
    {
        std::cout << num[0] + 9;
        return 0;
    }
    // 2. 倒序查找
    int r = num.size() - 1;
    int curSum = 0;
    
    while (curSum == 0 || num[r] == 9)
    {
        if (r == -1) break;
        r--;
        curSum += num[r + 1];
    }
    // 3. 处理输出
    if (r >= 0) {
        for (int i = 0; i < r; i++) 
            ss << num[i];
        ss << num[r] + 1;

        curSum--;
        int nine_num = static_cast<int>(curSum) / 9;
        int num_mod = curSum % 9;
        for (int i = r; i < num.size() - 1 - nine_num - 1; i++)
            ss << 0;
        ss << num_mod;
        for (int i = 0; i < nine_num; i++) 
            ss<< 9;
    } else 
    {
        ss << "1";
        curSum--;
        int nine_num = static_cast<int>(curSum) / 9;
        int num_mod = curSum % 9;
        r = 0;
        for (int i = r; i < static_cast<int>(num.size()) - nine_num - 1; i++)
            ss << 0;
        ss << num_mod;
        for (int i = 0; i < nine_num; i++)
            ss << 9;
    }
    cout << ss.str() << endl;
    return 0;
}
```

---
**测试样例：**

以下测试样例为自动生成, 分别为A和B

```
1023950553073
1023950553082
Accepted, 测试点 #1, 用时 491.0
11410238899918
11410238899927
Accepted, 测试点 #2, 用时 502.0
16099
16189
Accepted, 测试点 #3, 用时 628.0
27617
27626
Accepted, 测试点 #4, 用时 488.0
2021071096708
2021071096717
Accepted, 测试点 #5, 用时 722.0
50893
50929
Accepted, 测试点 #6, 用时 614.0
```
### 02 - 编程题02

已知有如下二维离散点：（3.1,1）（4,1.9）（4,4）（6,2.1）（9,7）（9.6,3）（11,2）（12.2,4）（14,2）（18,6），提取其中近似在一条直线上的离散点集群（要求该集群要包含最多的离散点，集群中点到集群对应直线的距离小于0.3）。输出该离散点集群以及对应的直线（以两点表示），给出函数实现，输出结果并说明解题思路（避免使用封装好的库算法）

---

本题的思路是采用RANSAC来拟合直线；

RANSAC做的一件事就是先随机的选取一对点，用这些点去计算出一个直线，然后用此模型去测试剩余的点，不断重复此过程，直到找到选取的这些数据点集达到了可以接受的程度为止，此时得到的模型便可认为是对数据点的最优模型构建。
**答案：**
```
-5.16x + 14.98y + 0.21 = 0
```

```cpp
#include <math.h>
#include <fstream>
#include <iostream>
#include <vector>
#include <random>
#include <string>
#include <time.h>
#include "sys/timeb.h"
using namespace std;

const float PI  = 3.1415926;
const float MAXD = 0.30;
string filePath = "/Users/vernon/question/question2/sample.in";

int n;
vector<float> x, y;
int max_cnt;

int getNumPoints(float x1, float y1, float x2, float y2) {
  float a, b, c, dist;
  a = y1 - y2;
  b = x2 - x1;
  c = y2 * x1 - x2 * y1;

  float ab = sqrt(a * a + b * b);
  int cnt = 0;
  for (int i = 0; i < n; i++) {
    dist = abs(a * x[i] + b * y[i] + c) / ab;
    if (dist < MAXD) cnt++;
  }
  return cnt;
}

int main() {
  FILE *fin = fopen(filePath.c_str(), "r");
  struct timeb timeSeed;
  ftime(&timeSeed);
  srand(timeSeed.time * 1000 + timeSeed.millitm);  // milli time
  n = 10;
  x.resize(n, 0);
  y.resize(n, 0);
  // 1. 读取每一个点的坐标
  for (int i = 0; i < n; i++)
  {
    float cur_x, cur_y;
    fscanf(fin, "%f %f", &cur_x, &cur_y);
    x[i] = cur_x;
    y[i] = cur_y;
  }

  vector<vector<float>> res_pairs;
  float x1, y1, x2, y2;
  int idx1, idx2, cnt;
  float r, theta;
  // 2. Ransac 迭代过程
  for (int i = 0; i < 100000; i++)
  {
    idx1 = random() % 10;
    do
      idx2 = random() % 10;
    while (idx2 == idx1);

    r = (random() % 300000) / 1000000.0;
    theta = (random() % 360000000) / 1000000.0;
    theta = theta * PI / 180.;
    x1 = x[idx1] + r * cos(theta);
    y1 = y[idx1] + r * sin(theta);

    r = (random() % 300000) / 1000000.0;
    theta = (random() % 361000000) / 1000000.0;
    theta = theta * PI / 180.0;
    x2 = x[idx2] + r * cos(theta);
    y2 = y[idx2] + r * sin(theta);
    cnt = getNumPoints(x1, y1, x2, y2);
    if (cnt > max_cnt) {
      max_cnt = cnt;
      res_pairs = {{x1, y1}, {x2, y2}};
    }
  }

  float a, b, c, dist;
  x1 = res_pairs[0][0];
  y1 = res_pairs[0][1];
  x2 = res_pairs[1][0];
  y2 = res_pairs[1][1];
  a = y1 - y2;
  b = x2 - x1;
  c = y2 * x1 - x2 * y1;
  
  // 输出最后的直线拟合方程
  string line = "直线拟合方程如下: %.2fx";
  if (b >= 0)
    line += " + %.2fy";
  else {
    line += " - %.2fy";
    b = -b;
  }
  if (c >= 0)
    line += " + %.2f";
  else {
    line += " - %.2f";
    c = -c;
  }
  line += " = 0\n";
  printf(line.c_str(), a, b, c);
  return 0;
}
```