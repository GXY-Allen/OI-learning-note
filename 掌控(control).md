# 掌控(control)
> ### 题面描述
> 公元 2044 年，人类进入了宇宙纪元。L 国有 $n$个星球，分别编号为1到$n$，每一星球上有一个球长。有些球长十分强大，可以管理或掌控其他星球的球长，具体来说，第$i$个星球的球长管理$k_i+1$个星球的球长，分别是$a_1,a_2,a_3,a_4$……$a_{k_i}$和自己，但若想要掌控一个星球的球长，就没那么容易了，第$i$个星球的球长掌控第$j$个星球的球长当且仅当他管理的所有球长都掌控第$j$个星球的球长，当然，所有球长都掌控他自己。
>
> 这些球长要召开$q$次会议，每次会议由$t_i$个球长召开，所有被他们掌控的球长都会参加，你作为宇宙会议室室长，需要知道每次会议有多少个球长参加。
> ### 输入
> 第一行一个数$n$，表示星球的个数；
> 接下来$n$行，每一行首先给出一个$k_i$（可能为0），接下来$k_i$个数，描述第$i$个星球的球长管理的球长。保证没有重复；
> 接下来一行，给出一个数$q$，表示询问的个数；
> 接下来$q$行，每一行描述一个询问：格式同上文的格式。不保证没有重复（重复的球长当做只出现了一次）
>
> ### 输出
> 输出共$q$行，第$i$行输出第$i$次询问的答案。
> ### 样例输入
>
> ```
> 7
> 0
> 1 1
> 1 1
> 1 2
> 2 2 3
> 0
> 2 2 6
> 3
> 2 2 3
> 2 3 5
> 2 4 5
> ```
>
> ### 样例输出
>
> ```
> 3
> 3
> 4
> ```
>
> ### 样例解释
> 对于第一个询问，2、3号球长都掌控1号球长，所以总共有3个球长参会，编号分别为1，2，3；
> 对于第二个询问，3、5号球长都掌控1号球长，所以总共有3个球长参会，编号分别为1，3，5；
> 对于第三个询问，4号球长掌控第1、2号球长，所以总共有4个球长参会，编号分别为1，2，4，5；
> 特别说明：第5号球长没有掌控球长2，因为$3\in k_5$ ,但2不属于$k_3$。但球长4掌控球长2，因为球长掌控自己。
> ![](https://img-blog.csdnimg.cn/img_convert/0545ae7277461e8797796fca3673ac79.png)
> 图片说明： u->v表示$u$管理$v$
> 数据范围
> 对于$20\%$的数据$n,q\leqslant20$
> 对于$40\%$的数据$n,q\leqslant200$
> 对于另外$20\%$的数据$k_i\leqslant1,q\leqslant1000$
> 对于$100\%$的数据$n,q\leqslant200000, \Sigma k_i,\Sigma t_i\leqslant2000000$

## 思路

语文不好的我，读题都读了半天（这题意有点难懂）。