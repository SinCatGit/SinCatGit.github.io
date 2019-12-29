---
layout: post
title: leetcode题解
author: sincat
date: 2019-12-03 18:57:15 +0800
category: 技术
tags: interview 

published: true
---


### 字符串

1.https://leetcode.com/problems/palindrome-number/

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        """
        Determine whether an integer is a palindrome.
        An integer is a palindrome when it reads the same backward as forward.
        :param x:
        :return:
        >>> solution = Solution()
        >>> solution.isPalindrome(121)
        True
        >>> solution.isPalindrome(-121)
        False
        >>> solution.isPalindrome(10)
        False
        """
        if x < 0:
            return False
        res, t = 0, x

        while t > 0:
            t, low_num = t // 10, t % 10
            res = res*10 + low_num
        return res == x
```

2.https://leetcode.com/problems/count-and-say/

```python
class Solution:
    def countAndSay(self, n: int) -> str:
        """
        The count-and-say sequence is the sequence of integers with the first five terms as following:
        
        1.     1
        2.     11
        3.     21
        4.     1211
        5.     111221
        1 is read off as "one 1" or 11.
        11 is read off as "two 1s" or 21.
        21 is read off as "one 2, then one 1" or 1211.
        
        Given an integer n where 1 ≤ n ≤ 30, generate the nth term of the count-and-say sequence. You can do so recursively, in other words from the previous member read off the digits, counting the number of digits in groups of the same digit.
        
        Note: Each term of the sequence of integers will be represented as a string.
        :param n:
        :return:
        >>> solution = Solution()
        >>> solution.countAndSay(1)
        '1'
        >>> solution.countAndSay(4)
        '1211'
        """
        res = "1"
        import itertools
        for _ in range(n-1):
            res = ''.join((str(len(list(g))) + digit for digit, g in itertools.groupby(res)))
        return res
```

3.https://leetcode.com/problems/implement-strstr/

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        """
        Implement strStr().
        Return the index of the first occurrence of needle in haystack,
        or -1 if needle is not part of haystack.
        :param haystack:
        :param needle:
        :return:

        >>> solution = Solution()
        >>> solution.strStr("hello", "ll")
        2
        >>> solution.strStr("aaaaa", "bba")
        -1
        >>> solution.strStr("a", "a")
        0
        """
        if needle == "":
            return 0
        length = len(needle)
        for i in range(len(haystack)):
            if haystack[i: i+length] == needle:
                return i
        return -1
```

4.https://leetcode.com/problems/valid-anagram/

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        """
        Given two strings s and t , write a function to determine if t is an anagram of s.
        :param s:
        :param t:
        :return:
        >>> solution = Solution()
        >>> solution.isAnagram("anagram", "nagaram")
        True
        >>> solution.isAnagram("rat", "car")
        False
        """
        s_hash, t_hash = [0] * 26, [0]*26

        for c in s:
            s_hash[ord(c)-ord('a')] += 1

        for c in t:
            t_hash[ord(c)-ord('a')] += 1

        return s_hash == t_hash
```

5.https://leetcode.com/problems/simplify-path/

```python
class Solution:
    def simplifyPath(self, path: str) -> str:
        """
        Given an absolute path for a file (Unix-style), simplify it.
        Or in other words, convert it to the canonical path.
        In a UNIX-style file system, a period . refers to the current directory.
        Furthermore, a double period .. moves the directory up a level.
        For more information, see: Absolute path vs relative path in Linux/Unix
        Note that the returned canonical path must always begin with a slash /,
        and there must be only a single slash / between two directory names.
        The last directory name (if it exists) must not end with a trailing /.
        Also, the canonical path must be the shortest string representing the absolute path.

        :param path:
        :return:
        >>> solution = Solution()
        >>> solution.simplifyPath('/home/')
        '/home'
        >>> solution.simplifyPath('/../')
        '/'
        >>> solution.simplifyPath('/home//foo/')
        '/home/foo'
        >>> solution.simplifyPath('/a/./b/../../c/')
        '/c'
        >>> solution.simplifyPath('/a/../../b/../c//.//')
        '/c'
        >>> solution.simplifyPath('/a//b////c/d//././/..')
        '/a/b/c'
        """
        stack = []
        for p in path.split('/'):
            if p == '..':
                if stack:
                    stack.pop()
            elif p and p != '.':
                stack.append(p)

        return '/' + '/'.join(stack)
```

6.https://leetcode.com/problems/multiply-strings/

```python
class Solution:
    def multiply(self, num1: str, num2: str) -> str:
        """
        Given two non-negative integers num1 and num2 represented as strings,
        return the product of num1 and num2, also represented as a string.
        Note:

        The length of both num1 and num2 is < 110.
        Both num1 and num2 contain only digits 0-9.
        Both num1 and num2 do not contain any leading zero, except the number 0 itself.
        You must not use any built-in BigInteger library or convert the inputs to integer directly.
        :param num1:
        :param num2:
        :return:
        >>> solution = Solution()
        >>> solution.multiply('2', '3')
        '6'
        >>> solution.multiply('123', '456')
        '56088'
        """
        if num1 == '0' or num2 == '0':
            return '0'
        num1 = [ord(c) - ord('0') for c in num1][::-1]
        num2 = [ord(c) - ord('0') for c in num2][::-1]
        ans = [0]*(len(num1)+len(num2))
        for i, n1 in enumerate(num1):
            for j, n2 in enumerate(num2):
                div, mod = divmod(ans[i+j] + n1*n2, 10)
                ans[i+j] = mod
                ans[i+j+1] += div
        pt = 0
        ans = ans[::-1]
        while pt < len(ans) and ans[pt] == 0:
            pt += 1
        return ''.join(map(str, ans[pt:]))
```

7.https://leetcode.com/problems/regular-expression-matching/

思路：

用2维布尔数组，dp[i][j]的含义是s[0:i+1] 与 s[0:j+1]是否匹配。

1.p[j] == s[i] : dp[i][j] = dp[i-1][j-1]

2.if p[j] == '.' : dp[i][j] = dp[i-1][j-1];
   
3.if p[j] == '*': 存在如下两种情况:
   
    if p[j-1] != s[i] : dp[i][j] = dp[i][j-2] // 此种情况，a*算作空字符串
    if p[j-1] == s.[i] or p[i-1] == '.':
        dp[i][j] = dp[i-1][j] // a* 算作多个a
        dp[i][j] = dp[i][j-1] // a* 算作一个a
        dp[i][j] = dp[i][j-2] // a* 算作空字符串


代码：

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        """
        Given an input string (s) and a pattern (p),
        implement regular expression matching with support for '.' and '*'.

        '.' Matches any single character.
        '*' Matches zero or more of the preceding element.
        The matching should cover the entire input string (not partial).

        Note:
        s could be empty and contains only lowercase letters a-z.
        p could be empty and contains only lowercase letters a-z, and characters like . or *.

        :param s:
        :param p:
        :return:

        >>> solution = Solution()
        >>> solution.isMatch('aa', 'a')
        False
        >>> solution.isMatch('aa', 'a*')
        True
        >>> solution.isMatch('aab', 'c*a*b')
        True
        >>> solution.isMatch('mississippi', 'mis*is*p*.')
        False
        >>> solution.isMatch('mississippi', 'mis*is*ip*.')
        True
        >>> solution.isMatch('ab', '.*c')
        False
        >>> solution.isMatch('aasdfasdfasdfasdfas', 'aasdf.*asdf.*asdf.*asdf.*s')
        True
        """
        dp = [[False for _ in range(len(p)+1)] for _ in range(len(s)+1)]
        dp[0][0] = True

        for j in range(1, len(p)+1):
            if j > 1 and p[j-1] == '*' and dp[0][j-2]:
                dp[0][j] = True

        for i in range(1, len(s)+1):
            for j in range(1, len(p)+1):
                if s[i-1] == p[j-1] or p[j-1] == '.':
                    dp[i][j] = dp[i-1][j-1]
                elif p[j-1] == '*':
                    if p[j-2] != s[i-1] and p[j-2] != '.':
                        dp[i][j] = dp[i][j-2]
                    elif p[j-2] == s[i-1] or p[j-2] == '.':
                        dp[i][j] = dp[i][j-1] or dp[i-1][j] or dp[i][j-2]

        return dp[-1][-1]
   
if __name__ == '__main__':
    import doctest
    doctest.testmod()
```

8.https://leetcode.com/problems/wildcard-matching/

思路：
有限状态机

代码：
```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        """
        Given an input string (s) and a pattern (p),
        implement wildcard pattern matching with support for '?' and '*'.

        '?' Matches any single character.
        '*' Matches any sequence of characters (including the empty sequence).
        The matching should cover the entire input string (not partial).
        :param s:
        :param p:
        :return:
        >>> solution = Solution()
        >>> solution.isMatch('aa', 'a')
        False
        >>> solution.isMatch('aa', '*')
        True
        >>> solution.isMatch('cb', '?a')
        False
        >>> solution.isMatch('adceb', '*a*b')
        True
        >>> solution.isMatch('acdcb', 'a*c?b')
        False
        """
        transfer = {}
        state = 0

        for c in p:
            if c == '*':
                transfer[state, c] = state
            else:
                transfer[state, c] = state + 1
                state += 1

        accept = state
        state = set([0])
        for c in s:
            state = set([transfer.get((at, token)) for at in state
                         for token in [c, '*', '?']])

        return accept in state
```

9.https://leetcode.com/problems/longest-palindrome/

```python

class Solution:
    def longestPalindrome(self, s: str) -> int:
        """
        Given a string which consists of lowercase or uppercase letters, find the length of the longest palindromes that can be built with those letters.

        This is case sensitive, for example "Aa" is not considered a palindrome here.

        Note:
        Assume the length of given string will not exceed 1,010.
        :param s:
        :return:

        >>> solution = Solution()
        >>> solution.longestPalindrome('abccccdd')
        7
        >>> solution.longestPalindrome('civilwartestingwhetherthatnaptionoranynartionsoconceivedandsontofthepeoplebythepeopleforthepeopleshallnotperishfromtheearth')
        115
        """
        d = {}
        for c in s:
            d[c] = d.setdefault(c, 0) + 1

        max_odd = 0
        longest_num = 0
        for v in d.values():
            if v % 2 == 0:
                longest_num += v
            else:
                longest_num += v - 1
                max_odd = 1

        return longest_num + max_odd
    
```

10.https://leetcode.com/problems/longest-palindromic-substring/

思路1：中心扩展法

中心扩展法的思路是，遍历到数组的某一个元素时，以这个元素为中心，向两边进行扩展。
如果两边的元素相同则继续扩展，否则停止扩展。如下，当遍历s='babad'到下标s[2]=b时

| b | a ｜ *b* ｜ a ｜ d ｜

1.以b中心左右扩展

| b | *a* ｜ *b* ｜ *a* ｜ d ｜

2.b两边的元素扩展

| **b** | *a* ｜ *b* ｜ *a* ｜ **d** ｜

3.扩展到两边元素不等时停止


这种算法，时间复杂度O（n^2）,空间复杂度为O（1）。


| b | a | a | b |

s='baab'是一个回文串，然而找不到对称中心，这样以一个元素为中心向两边扩展就不好用了。
这里有一种比较方便的处理方式，就是对s='baab'进行填充，比如说用#进行填充如下：

| # | b | # | a | # | a | # | b | # |

这时s='#b#a#a#b#',长度就成为奇数了，就方便中心扩展法进行处理了。

代码1：

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        """
        Given a string s, find the longest palindromic substring in s.
        You may assume that the maximum length of s is 1000.

        :param s:
        :return:

        >>> solution = Solution()
        >>> solution.longestPalindrome('babad')
        'bab'
        >>> solution.longestPalindrome('cbbd')
        'bb'
        """
        max_s = ''
        t = '#'+'#'.join(s)+'#'
        for i, c in enumerate(t):
            low, high = i, i
            while 0 <= low-1 and high + 1 < len(t) and t[low-1] == t[high+1]:
                low = low - 1
                high = high + 1

            if len(t[low:high+1]) > len(max_s):
                max_s = t[low:high+1]

        return ''.join(max_s.split('#'))
```

思路2：动态规划方法

如果用f[i][j]保存子串从i 到j是否是回文子串。
那么在求f[i][j]的时候，如果j-i>=2时，如果 f[i][j] 为回文，那么f[i+1][j-1],也一定为回文。
否则f[i][j]不为回文。如下s='abba'：

| a | b | b | a |

f[0][3] ('abba') 为True，那么f[1][2] （'bb'）一定为True


| a | b | a | a |

f[1][2] （'ba'） 为False，那么f[0][3] ('abaa') 一定为False

这样递推公式为：

1. 如果 i == j,   f[i][j] = True 
2. 如果 j == i+1, f[i][j] = (s[i] == s[j])
3. 如果 j >  i+1, f[i][j] = (s[i] == s[j] and f[i+1][j-1])


时间复杂度O（n^2）,空间复杂度O（n^2）。

```python
class Solution:
    def longestPalindromeV02(self, s: str) -> str:
        length = len(s)
        max_s = ''
        dp = [[True for _ in range(length)] for _ in range(length)]
        for i in range(length):
            for j in range(length-i):
                if i + j < length:
                    if i+j == j+1 and s[j] != s[j+i]:
                        dp[j][j+i] = False
                    elif i+j > j+1 and not(dp[j+1][j+i-1] and s[j] == s[j+i]):
                        dp[j][j+i] = False
    
                    if dp[j][j+i] and i+1 > len(max_s):
                        max_s = s[j:j+i+1]
        return max_s
```

思路3：Manacher算法

https://segmentfault.com/a/1190000003914228#comment-area

https://en.wikipedia.org/wiki/Longest_palindromic_substring

```python
class Solution:
    def longestPalindrome(self, in_s: str) -> str:
        s = '#' + '#'.join(in_s) + '#'
        s_len = len(s)
        r = [0 for _ in range(s_len)]
        pos = 0
        max_right = 0
        max_s = ''
        for i, c in enumerate(s):
            if i < max_right:
                r[i] = min(max_right-i, r[2*pos-i])
            else:
                r[i] = 1
            while i-r[i] >= 0 and r[i]+i < s_len and s[i-r[i]] == s[i+r[i]]:
                r[i] += 1

            if r[i]+i-1 > max_right:
                max_right = r[i]+i-1
                pos = i
            if len(s[i-r[i]+1:i+r[i]]) > len(max_s):
                max_s = s[i-r[i]+1:i+r[i]]

        return ''.join(max_s.split('#'))
```

11.https://leetcode.com/problems/zigzag-conversion/

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        """
        The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

        P   A   H   N
        A P L S I I G
        Y   I   R
        And then read line by line: "PAHNAPLSIIGYIR"

        Write the code that will take a string and make this conversion given a number of rows:

        string convert(string s, int numRows);

        :param s:
        :param numRows:
        :return:

        >>> solution = Solution()
        >>> solution.convert('PAYPALISHIRING', 3)
        'PAHNAPLSIIGYIR'
        >>> solution.convert('PAYPALISHIRING', 4)
        'PINALSIGYAHRPI'
        >>> solution.convert('', 1)
        ''
        >>> solution.convert('A', 1)
        'A'
        """
        if numRows == 1 or numRows >= len(s):
            return s

        L = [''] * numRows
        index, step = 0, 1

        for x in s:
            L[index] += x
            if index == 0:
                step = 1
            elif index == numRows - 1:
                step = -1
            index += step

        return ''.join(L)
```

12.https://leetcode.com/problems/length-of-last-word/

```python
class Solution:
    def lengthOfLastWord(self, s: str) -> int:
        res = s.split()
        if not res:
            return 0
        return len(res[-1])
```


13.https://leetcode.com/problems/valid-palindrome/

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        """
        Determine whether an integer is a palindrome.
        An integer is a palindrome when it reads the same backward as forward.
        :param s:
        :return:
        >>> solution = Solution()
        >>> solution.isPalindrome('A man, a plan, a canal: Panama')
        True
        >>> solution.isPalindrome('race a car')
        False
        """
        r = ''.join(e for e in s if e.isalnum()).lower()
        return r == r[::-1]


if __name__ == '__main__':
    import doctest
    doctest.testmod()
```

14.https://leetcode.com/problems/isomorphic-strings/


```python
class Solution:
    def isIsomorphic(self, s: str, t: str) -> bool:
        """
        Given two strings s and t, determine if they are isomorphic.

        Two strings are isomorphic if the characters in s can be replaced to get t.

        All occurrences of a character must be replaced with another character
        while preserving the order of characters. No two characters may map to
        the same character but a character may map to itself.

        :param s:
        :param t:
        :return:
        >>> solution = Solution()
        >>> solution.isIsomorphic('egg', 'add')
        True
        >>> solution.isIsomorphic('foo', 'bar')
        False
        >>> solution.isIsomorphic('paper', 'title')
        True
        """
        return len(set(zip(s, t))) == len(set(s)) == len(set(t))
```


15.https://leetcode.com/problems/palindromic-substrings/
```python
class Solution:
    def countSubstrings(self, s: str) -> int:
        """
        Given a string, your task is to count how many palindromic substrings in this string.

        The substrings with different start indexes or end indexes are counted as different
        substrings even they consist of same characters.

        :param s:
        :return:
        >>> solution = Solution()
        >>> solution.countSubstrings('abc')
        3
        >>> solution.countSubstrings('aaa')
        6
        """
        length = len(s)
        dp = [[False for _ in range(length)] for _ in range(length)]
        cnt = 0
        for i in range(0, length):
            for j in range(length-i):
                if j == j+i:
                    dp[j][j+i] = True
                    cnt += 1
                elif j+i == j+1 and s[j] == s[j+i]:
                    dp[j][j+i] = True
                    cnt += 1
                elif j+1 <= length and j+i-1 >= 0 and s[j] == s[j+i]:
                    dp[j][j+i] = dp[j+1][j+i-1]
                    if dp[j][j+i]:
                        cnt += 1
        return cnt
```

16.https://leetcode.com/problems/count-binary-substrings/

```python
class Solution:
    def countBinarySubstrings(self, s: str) -> int:
        """
        Give a string s, count the number of non-empty (contiguous) substrings
        that have the same number of 0's and 1's, and all the 0's and all the 1's
        in these substrings are grouped consecutively.

        Substrings that occur multiple times are counted the number of times they occur.

        :param s:
        :return:
        >>> solution = Solution()
        >>> solution.countBinarySubstrings('00110011')
        6
        >>> solution.countBinarySubstrings('10101')
        4
        >>> solution.countBinarySubstrings('00110')
        3
        """
        s = list(map(len, s.replace('01', '0 1').replace('10', '1 0').split()))
        return sum(min(a, b) for a, b in zip(s, s[1:]))
```


17.https://leetcode.com/problems/text-justification/

18.https://leetcode.com/problems/concatenated-words/

19.https://leetcode.com/problems/shortest-palindrome/

20.https://leetcode.com/problems/palindrome-pairs/

21.https://leetcode.com/problems/longest-palindromic-subsequence/

22.https://leetcode.com/problems/string-to-integer-atoi/

23.https://leetcode.com/problems/valid-palindrome-ii/

24.https://leetcode.com/problems/palindrome-linked-list/


https://leetcode-cn.com/problemset/top/

https://leetcode-cn.com/problemset/50/

### 位操作

#### 基础知识

**异或操作**

x ^ 0 = x

x ^ 1s = ~x // 1s = ~0

x ^ (~x) = 1s

x ^ x = 0

a ^ b = c -> a ^c = b, b ^ c = a

a ^ b ^ c = a ^ (b ^ c) = (a ^ b) ^ c

**常用操作**

- x&1 == 1 or == 0 判断奇偶

- x=x&(x-1) 清零最低位1

- x&-x 得到最低位的1

**复杂操作**

1. 将x最右边n位清零 x&(~0<<n)
2. 获取x的第n位值 (x>>n)&1
3. 获取x的第n位幂值 x&(1<<(n-1))
4. 仅将第n位置位1 x|(1<<n)
5. 仅将第n位置位0 x&(~(1<<n))
6. 将x最高位至第n位清零 x&((1<<n)-1)
7. 将第n位至第0位清零 x&(~((1<<(n+1))-1))


#### 题目

**分类链接**

https://leetcode.com/tag/bit-manipulation/

**必做题目**

https://leetcode.com/problems/single-number/

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        res = 0
        for n in nums:
            res ^=n
        return res
    
    def singleNumber(self, nums: List[int]) -> int:
        from functools import reduce
        return reduce(operator.xor, nums)
```


```go
func singleNumber(nums []int) int {
    res := 0
    for _, v := range nums {
        res ^= v
    }
    return res
}
```


https://leetcode.com/problems/reverse-bits

```python
class Solution:
    def reverseBits(self, n: int) -> int:
        return int(bin(n)[2:].zfill(32)[::-1], 2)

    def reverseBits01(self, n: int) -> int:
        for i in range(16):
            if (n>>i & 1) != (n >> (31-i) & 1):
                n ^= ((1<<i) | (1<<(31-i)))
        return n
        
    def reverseBits02(self, n: int) -> int:
        return int("{:032b}".format(n)[::-1], 2)
```


```golang

func reverseBits(num uint32) uint32 {
	var res uint32
	for i := 0; i < 32; i++ {
		res = (res << 1) + (num & 1)
		num = num >> 1
	}
	return res
}

func reverseBitsV01(num uint32) uint32 {
	var res uint32
	for i := 0; i < 32; i++ {
		res = res*2 + num%2
		num = num / 2
	}
	return res
}

func reverseBitsV02(num uint32) uint32 {
	num = (num >> 16) | (num << 16)
	num = ((num & 0xff00ff00) >> 8) | ((num & 0x00ff00ff) << 8)
	num = ((num & 0xf0f0f0f0) >> 4) | ((num & 0x0f0f0f0f) << 4)
	num = ((num & 0xcccccccc) >> 2) | ((num & 0x33333333) << 2)
	num = ((num & 0xaaaaaaaa) >> 1) | ((num & 0x55555555) << 1)
	return num
}
```

https://leetcode.com/problems/number-of-1-bits/

```python
class Solution(object):
    def hammingWeight(self, n):
        """
        :type n: int
        :rtype: int
        """
        res = 0
        while n != 0:
            n &= n-1
            res += 1
        return res

```

```golang
func hammingWeight(num uint32) int {
	res := 0
	for num > 0 {
		num &= num - 1
		res++
	}
	return res
}

```


https://leetcode.com/problems/power-of-four

```python
class Solution:
    def isPowerOfFour(self, num: int) -> bool:
        return num >0 and num&(num-1)==0 and len(bin(num)[3:])%2 == 0
```

```golang
func isPowerOfFour(num int) bool {
    return num > 0 && (num & (num -1) ==0) && (num-1) % 3 == 0
}
```


https://leetcode.com/problems/sum-of-two-integers

```python
class Solution:
    def getSum(self, a: int, b: int) -> int:
        MASK = 0xFFFFFFFF
        MAX = 0x7FFFFFFF
        while b != 0:
            a, b = (a ^ b) & MASK, ((a & b) << 1) & MASK
        return a if a <= MAX else ~(a^MASK)
```

```golang
func getSum(a int, b int) int {
    for b != 0 {
        a, b = a ^ b, ((a & b) << 1)
    }
    return a
}
```


https://leetcode.com/problems/convert-a-number-to-hexadecimal

```python
class Solution:
    def toHex(self, num: int) -> str:
        if num == 0:
            return '0'
        res = ''
        for i in range(8):
            res = '0123456789abcdef'[num&15] + res
            num >>= 4
        return res.lstrip('0')
```

```golang
func toHex(num int) string {
	const (
		hexStr = "0123456789abcdef"
		mask   = 0xf
	)
	var res = make([]byte, 8)
	var nonZeroIndex = 7
	for i := 7; i >= 0; i-- {
		val := num & mask
		if val > 0 {
			nonZeroIndex = i
		}
		res[i] = hexStr[val]
		num >>= 4
	}
	return string(res[nonZeroIndex:])
}
```



https://leetcode.com/problems/subsets/

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        nums_len = len(nums)
        max_num = 2**nums_len
        res = list()
        for i in range(max_num):
            b = '0'*(nums_len-len(bin(i)[2:])) + bin(i)[2:]
            t = list()
            for j, v in enumerate(b):
                if v == '1':
                    t.append(nums[j])
            res.append(t)
        return res
```

```golang
func subsets(nums []int) [][]int {
	res := make([][]int, 1)
	for _, num := range nums {
		for _, sub := range res {
			clone := make([]int, len(sub), len(sub)+1)
			copy(clone, sub)
			clone = append(clone, num)
			res = append(res, clone)
		}
	}
	return res
}
```


https://leetcode.com/problems/single-number-ii/

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        ones, twos, threes = 0, 0, 0
        for num in nums:
            twos |= ones & num
            ones ^= num
            threes = ones & twos
            ones &= ~threes
            twos &= ~threes
            
        return ones
```

```golang
func singleNumber(nums []int) int {
	ones, twos, threes := 0, 0, 0
	for i := 0; i < len(nums); i++ {
		twos |= ones & nums[i]
		ones ^= nums[i]
		threes = ones & twos
		ones = ones &^ threes
		twos = twos &^ threes
	}
	return ones
}
```

https://leetcode.com/problems/repeated-dna-sequences

```python
class Solution:
    def findRepeatedDnaSequences(self, s: str) -> List[str]:
        res = set()
        keys = set()
        for i in range(len(s)-9):
            if s[i:i+10] in keys:
                res.add(s[i:i+10])
            else:
                keys.add(s[i:i+10])
        return list(res)
```

```golang
func isExist(strs []string, s string) bool {
	for i := 0; i < len(strs); i++ {
		if strs[i] == s {
			return true
		}
	}
	return false
}

func findRepeatedDnaSequences(s string) []string {
	var key int
	res := []string{}
	keys := map[int]bool{}
    if len(s) < 10 {
        return res
    }
	for i := 0; i < 10; i++ {
		key <<= 3
		key |= int(s[i]) & 7
		key = key & 0x3fffffff
	}
	keys[key] = true
	for i := 10; i < len(s); i++ {
		key <<= 3
		key |= int(s[i]) & 7
		key = key & 0x3fffffff
		_, exist := keys[key]
		if exist && !(isExist(res, s[i-9:i+1])) {
			res = append(res, s[i-9:i+1])
		} else {
			keys[key] = true
		}
	}
	return res
}


func findRepeatedDnaSequences(s string) []string {
    rec := make(map[string]int, len(s)-9)
	res := []string{}
    if len(s) < 10 {
        return res
    }

	for i := 0; i+10 <= len(s); i++ {
        sub := s[i:i+10]
        if v, _ := rec[sub]; v == 1 {
            res = append(res, sub)
        }
        rec[sub]++
	}
	return res
}
```


https://leetcode.com/problems/bitwise-and-of-numbers-range/


```python
class Solution:
    def rangeBitwiseAnd(self, m: int, n: int) -> int:
        while m < n:
            n &= n-1
        return n
```

```golang
func rangeBitwiseAnd(m int, n int) int {
    for m < n {
        n &= n-1
    }
    return n
}
```


https://leetcode.com/problems/single-number-iii


```python
class Solution:
    def singleNumber(self, nums: List[int]) -> List[int]:
        from functools import reduce
        import operator
        xor = reduce(operator.xor, nums)

        xor = xor & (-xor) 
        a, b = 0, 0
        for num in nums:
            if num & xor:
                a ^= num
            else:
                b ^= num
        return [a, b]
```

```golang
func singleNumber(nums []int) []int {
	var res = []int{0, 0}
	diff := 0
	numLen := len(nums)
	for i := 0; i < numLen; i++ {
		diff ^= nums[i]
	}
	diff &= -diff
	for i := 0; i < numLen; i++ {
		if (diff & nums[i]) == 0 {
			res[0] ^= nums[i]
		} else {
			res[1] ^= nums[i]
		}
	}
	return res
}
```

https://leetcode.com/problems/maximum-product-of-word-lengths/

```python
class Solution:
    def maxProduct(self, words: List[str]) -> int:
        d = {sum(1<<(ord(c)-ord('a')) for c in set(w)):len(w) for w in sorted(words, key=len)}
        return max([d[i]*d[j] for i in d for j in d if not i&j] or [0])
```

```golang
func helper(word string) int {
	t := 0
	for _, c := range word {
		t |= (1 << (uint(c) - uint('a')))
	}
	return t
}
func maxProduct(words []string) int {
	wLen := len(words)
	wTable := make([]int, wLen)
	for i := 0; i < wLen; i++ {
		wTable[i] = helper(words[i])
	}
	max := 0
	for i := 0; i < wLen; i++ {
		li := len(words[i])
		for j := i + 1; j < wLen; j++ {
			if (wTable[i] & wTable[j]) == 0 {
				lj := len(words[j])
				if li*lj > max {
					max = li * lj
				}
			}
		}
	}
	return max
}
```


https://leetcode-cn.com/problems/counting-bits/

```python
class Solution:
    def countBits(self, num: int) -> List[int]:
        res = [0]
        while len(res) < num + 1:
            res += [x+1 for x in res]
        return res[:num+1]
```

```golang
func countBits(num int) []int {
	res := make([]int, num+1)
	res[0] = 0
	for i := 1; i < num+1; i++ {
		res[i] = res[i&(i-1)] + 1
	}
	return res
}
```


https://leetcode.com/problems/utf-8-validation

```python
class Solution:
    def validUtf8(self, data: List[int]) -> bool:
        count = 0
        for i in data:
            if count == 0:
                if (i>>3) == 0b11110:
                    count = 3
                elif (i>>4) == 0b1110:
                    count = 2
                elif (i>>5) == 0b110:
                    count = 1
                elif (i>>7) != 0:
                    return False
            else:
                if (i>>6) != 0b10:
                    return False
                count -= 1
        return count == 0
```

```golang
func validUtf8(data []int) bool {
	count := 0
	dataLen := (len(data))
	for i := 0; i < dataLen; i++ {
		if count == 0 {
			if (data[i] >> 3) == 0x1e {
				count = 3
			} else if (data[i] >> 4) == 0xe {
				count = 2
			} else if (data[i] >> 5) == 0x6 {
				count = 1
			} else if (data[i] >> 7) != 0 {
				return false
			}
		} else if (data[i] >> 6) == 0x2 {
			count--
		} else {
			return false
		}
	}
	return count == 0
}
```

https://leetcode.com/problems/integer-replacement/

```python
class Solution:
    def integerReplacement(self, n: int) -> int:
        cnt = 0
        while n != 1:
            if n & 1:
                if ((n >>1) & 1) and n != 3:
                    n = n+1
                else:
                    n = n-1
            else:
                n >>= 1
            cnt += 1
        return cnt
```

```golang
func integerReplacement(n int) int {
	cnt := 0
	for n != 1 {
		if n&1 == 1 {
			if n != 3 && (n&0x3) == 0x3 {
				n++
			} else {
				n--
			}
			cnt++
		} else {
			n >>= 1
			cnt++
		}
	}
	return cnt
}
```

https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/

```python
class Solution:
    def findMaximumXOR(self, nums: List[int]) -> int:
        ans = 0
        for i in range(32)[::-1]:
            ans <<= 1
            prefixes = { num >> i for num in nums}
            ans += any(ans ^ 1 ^ p in prefixes for p in prefixes)
        return ans
```

```golang
func findMaximumXOR(nums []int) int {
	ans := 0
	numsLen := len(nums)
	for i := 31; i >= 0; i-- {
		ans <<= 1
		prefixes := make(map[int]bool, numsLen)
		for j := 0; j < numsLen; j++ {
			preNum := nums[j] >> uint(i)
			prefixes[preNum] = true
		}
		for k := range prefixes {
			if prefixes[ans^1^k] {
				ans++
				break
			}
		}
	}
	return ans
}
```

https://leetcode.com/problems/total-hamming-distance

```python

class Solution:
    def totalHammingDistance(self, nums: List[int]) -> int:
        return sum([b.count('0')*b.count('1') for b in zip(*map("{:032b}".format, nums))])
```

```golang
func totalHammingDistance(nums []int) int {
	numLen := len(nums)
	sum := 0
	for j := 0; j < 32; j++ {
		ones := 0
		for i := 0; i < numLen; i++ {
			ones += (nums[i] >> uint(j)) & 1
		}
		sum += ones * (numLen - ones)
	}
	return sum
}
```
https://leetcode.com/problems/bitwise-ors-of-subarrays


```python
class Solution:
    def subarrayBitwiseORs(self, A: List[int]) -> int:
        ans = set()
        cur = {0}
        for x in A:
            cur = {y|x for y in cur} | {x}
            ans |= cur
        return len(ans)
```

```golang
func subarrayBitwiseORs(A []int) int {
	ans := map[int]bool{}
	cur := map[int]bool{0: true}
	ALen := len(A)
	for i := 0; i < ALen; i++ {
		t := make(map[int]bool, len(cur)+1)
		for k := range cur {
			t[k|A[i]] = true
			ans[k|A[i]] = true
		}
		t[A[i]] = true
		ans[A[i]] = true
		cur = t
	}
	return len(ans)
}
```

https://leetcode.com/problems/maximum-of-absolute-value-expression/

```python
class Solution:
    def maxAbsValExpr(self, arr1: List[int], arr2: List[int]) -> int:
        A, B, C, D= [], [], [], []
        for i in range(len(arr1)):
            x, y = arr1[i], arr2[i]
            A.append(x + y + i)
            B.append(x + y - i)
            C.append(x - y + i)
            D.append(x - y - i)
            
        a = max(A) - min(A)
        b = max(B) - min(B)
        c = max(C) - min(C)
        d = max(D) - min(D)        
        return max(a, b, c, d)


```
```golang
func findMaxMin(arr []int) (int, int) {
	max, min := arr[0], arr[0]
	for i := 0; i < len(arr); i++ {
		if arr[i] > max {
			max = arr[i]
		}
		if arr[i] < min {
			min = arr[i]
		}
	}
	return max, min
}

func maxAbsValExpr(arr1 []int, arr2 []int) int {
	arrLen := len(arr1)
	A, B, C, D := make([]int, arrLen), make([]int, arrLen), make([]int, arrLen), make([]int, arrLen)
	for i := 0; i < arrLen; i++ {
		x, y := arr1[i], arr2[i]
		A[i] = x + y + i
		B[i] = x + y - i
		C[i] = x - y + i
		D[i] = x - y - i
	}
	Amax, Amin := findMaxMin(A)
	Bmax, Bmin := findMaxMin(B)
	Cmax, Cmin := findMaxMin(C)
	Dmax, Dmin := findMaxMin(D)
	max, _ := findMaxMin([]int{Amax - Amin, Bmax - Bmin, Cmax - Cmin, Dmax - Dmin})
	return max
}

```

https://leetcode.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters

```python
class Solution:
    def maxLength(self, arr: List[str]) -> int:
        dp = [set()]
        for a in arr:
            if len(set(a)) < len(a): continue
            a = set(a)
            for c in dp:
                if a&c: continue
                dp.append(a|c)
        return max(len(a) for a in dp)
```

```golang

func stringToBinary(s string) (int, bool) {
	sLen := len(s)
	res := 0
	repeated := false
	for i := 0; i < sLen; i++ {
		t := 1 << (uint(s[i]) - uint('a'))
		if t&res != 0 {
			repeated = true
		}
		res |= t
	}
	return res, repeated
}

func maxLength(arr []string) int {
	dp := map[int]int{}
	arrLen := len(arr)
	for i := 0; i < arrLen; i++ {
		b, r := stringToBinary(arr[i])
		if r {
			continue
		}
		dp[b] = len(arr[i])
		for k, v := range dp {
			if k&b == 0 {
				dp[k|b] = v + len(arr[i])
			}
		}
	}

	max := 0
	for _, v := range dp {
		if v > max {
			max = v
		}
	}
	return max
}

```

https://leetcode.com/problems/smallest-sufficient-team/

```python
class Solution:
    def smallestSufficientTeam(self, req_skills: List[str], people: List[List[str]]) -> List[int]:
        n, m = len(req_skills), len(people)
        key = {v:i for i, v in enumerate(req_skills)}
        dp = {0:[]}
        
        for i, p in enumerate(people):
            skill = 0
            for k in p:
                if k in key:
                    skill |= 1 << key[k]
            if skill == 0: continue
            
            if skill in dp and len(dp[skill]) == 1: continue
            
            for k, need in list(dp.items()):
                with_him = skill | k
                if with_him == k: continue
                if with_him not in dp or len(dp[with_him]) > len(need) + 1:
                    dp[with_him] = need + [i]
        
        return dp[(1 << n) - 1]
                
```
```golang
func smallestSufficientTeam(reqSkills []string, people [][]string) []int {
	dp := map[int][]int{0: {}}
	peopleLen := len(people)
	reqMap := map[string]int{}
	reqLen := len(reqSkills)
	for i := 0; i < reqLen; i++ {
		reqMap[reqSkills[i]] = 1 << uint(i)
	}
	for i := 0; i < peopleLen; i++ {
		jLen := len(people[i])
		if jLen == 0 {
			continue
		}
		skill := 0
		for j := 0; j < jLen; j++ {
			skill |= reqMap[people[i][j]]
		}
		if len(dp[skill]) == 1 {
			continue
		}
		for k, v := range dp {
			withHim := k | skill
			if withHim == k {
				continue
			}
			if len(dp[withHim]) == 0 || len(dp[withHim]) > len(v)+1 {
				if len(v) > 0 {
					t := make([]int, len(v), len(v)+1)
					copy(t, v)
					dp[withHim] = append(t, i)
				} else {
					dp[withHim] = []int{i}
				}
			}
		}

	}

    return dp[(1<<uint(reqLen))-1]
}
```
https://leetcode.com/problems/number-of-valid-words-for-each-puzzle

```python
class Solution:
    # [1] 开始
    def getMask(self, word):
        mask = 0
        for c in word:
            mask |= 1 << (ord(c) - ord('a'))
        return mask
    # [1] 结束

    def findNumOfValidWords(self, words: List[str], puzzles: List[str]) -> List[int]:
        # [2] 开始
        words = [self.getMask(w) for w in words]
        d = {}
        for w in words:
            d[w] = d.get(w, 0) + 1
        # [2] 结束

        ans = []
        for p in puzzles:
            mask = self.getMask(p)
            sub_mask = mask
            head_mask = 1 << (ord(p[0]) - ord('a'))
            cnt = 0
            # [3] 开始
            while sub_mask:
                # [4] 开始
                if sub_mask & head_mask:
                    cnt += d.get(sub_mask, 0)
                # [4] 结束
                sub_mask = (sub_mask-1)&mask
            # [3] 结束
            ans.append(cnt)
        return ans
```

```golang
func binaryWord(word string) int {
	wordLen := len(word)
	res := 0
	for i := 0; i < wordLen; i++ {
		res |= 1 << (uint(word[i]) - uint('a'))
	}
	return res
}

func findNumOfValidWords(words []string, puzzles []string) []int {
	wordsLen := len(words)
	puzzlesLen := len(puzzles)
	ans := make([]int, puzzlesLen)
	wordMap := make(map[int]int, wordsLen)
	for i := 0; i < wordsLen; i++ {
		wordMap[binaryWord(words[i])]++
	}
	for i := 0; i < puzzlesLen; i++ {
		mask := binaryWord(puzzles[i])
		subMask := mask
		headMask := 1 << (uint(puzzles[i][0]) - uint('a'))
		for subMask != 0 {
			if (headMask & subMask) != 0 {
				ans[i] += wordMap[subMask]
			}
			subMask = (subMask - 1) & mask
		}
	}
	return ans
}

```

https://leetcode.com/problems/maximum-score-words-formed-by-letters

```python
class Solution:
    def maxScoreWords(self, words: List[str], letters: List[str], score: List[int]) -> int:
        from collections import Counter
        words_score = [sum(score[ord(c)-ord('a')] for c in word) for word in words]
        words_counter = [Counter(word) for word in words]
        self.max_score = 0

        def dfs(start, current_score, letter_counter):
            if current_score+sum(words_score[start:]) <= self.max_score:
                return
            self.max_score = max(self.max_score, current_score)

            for i, w_cnt in enumerate(words_counter[start:], start):
                if all(n <= letter_counter.get(c, 0) for c, n in w_cnt.items()):
                    dfs(i+1, current_score+words_score[i], {c:n-w_cnt.get(c, 0) for c, n in letter_counter.items()})
        
        dfs(0, 0, Counter(letters))
        return self.max_score
```


```golang

func wordCount(word string) map[byte]int {
	wordLen := len(word)
	res := map[byte]int{}
	for i := 0; i < wordLen; i++ {
		res[byte(word[i])]++
	}
	return res
}

func wordScore(word string, score []int) int {
	res := 0
	wordLen := len(word)
	for i := 0; i < wordLen; i++ {
		res += score[int(word[i])-int('a')]
	}
	return res
}

func getMinusMap(counter map[byte]int, letters map[byte]int) (map[byte]int, bool) {
	res := map[byte]int{}
	isValid := true
	for k, v := range letters {
		res[k] = v - counter[k]
		if res[k] < 0 {
			isValid = false
		}
	}
	for k := range counter {
		_, isExist := res[k]
		if !isExist {
			isValid = false
		}
	}
	return res, isValid
}

func dfs(start int, score int, letters map[byte]int, wordsLen int, wordsScore []int, wordsCounter []map[byte]int, maxScore *int) {
	tSum := 0
	for i := start; i < wordsLen; i++ {
		tSum += wordsScore[i]
	}
	if (score + tSum) <= *maxScore {
		return
	}
	if score > *maxScore {
		*maxScore = score
	}
	for i := start; i < wordsLen; i++ {
		newLetters, isValid := getMinusMap(wordsCounter[i], letters)
		if isValid {
			dfs(i+1, score+wordsScore[i], newLetters, wordsLen, wordsScore, wordsCounter, maxScore)
		}
	}

}

func maxScoreWords(words []string, letters []byte, score []int) int {
	wordsLen := len(words)
	wordsCounter := make([]map[byte]int, wordsLen)
	wordsScore := make([]int, wordsLen)
	for i := 0; i < wordsLen; i++ {
		wordsCounter[i] = wordCount(words[i])
		wordsScore[i] = wordScore(words[i], score)
	}
	maxScore := 0
	lettersMap := map[byte]int{}
	for _, v := range letters {
		lettersMap[byte(v)]++
	}
	dfs(0, 0, lettersMap, wordsLen, wordsScore, wordsCounter, &maxScore)
	return maxScore
}

```

**额外练习**


https://leetcode.com/problems/power-of-two/

```python

class Solution:
    def isPowerOfTwo(self, n: int) -> bool:
        return n > 0 and not n & (n-1)
```

```golang
func isPowerOfTwo(n int) bool {
    return n > 0 && n & (n-1) == 0
}
```

https://leetcode.com/problems/missing-number

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        return sum(range(len(nums)+1)) - sum(nums)
```

```golang
func missingNumber(nums []int) int {
    total := 0
    for i, v := range nums {
        total ^= i ^ v
    }
    total ^= len(nums)
    return total
}
```


https://leetcode.com/problems/find-the-difference/

```python
class Solution:
    def findTheDifference(self, s: str, t: str) -> str:
        from functools import reduce
        return chr(reduce(int.__xor__, map(ord, s+t)))
```

```golang
func findTheDifference(s string, t string) byte {
	var res byte
	for i := 0; i < len(s); i++ {
		res ^= s[i]
	}
	for i := 0; i < len(t); i++ {
		res ^= t[i]
	}
	return res
}
```


https://leetcode.com/problems/binary-watch/

```python
class Solution:
    def readBinaryWatch(self, num: int) -> List[str]:
        return [f"{h}:{m:02d}" for h in range(12) for m in range(60) if (bin(h)+bin(m)).count('1') == num]
```

```golang
func readBinaryWatch(num int) []string {
	var res []string
	for i := 0; i < 12; i++ {
		for j := 0; j < 60; j++ {
			h := fmt.Sprintf("%b", i)
			m := fmt.Sprintf("%b", j)
			countOne := strings.Count(h, "1") + strings.Count(m, "1")
			if countOne == num {
				res = append(res, fmt.Sprintf("%d:%02d", i, j))
			}
		}
	}
	return res
}
```


https://leetcode.com/problems/pyramid-transition-matrix

```python
class Solution:
    def pyramidTransition(self, bottom: str, allowed: List[str]) -> bool:
        from collections import defaultdict
        from itertools import product
        f = defaultdict(lambda:defaultdict(list))
        for a, b, c in allowed:
            f[a][b].append(c)
        
        def pyramid(inner_bottom):
            if len(inner_bottom) == 1:
                return True
            for i in product(*(f[a][b] for a, b in zip(inner_bottom, inner_bottom[1:]))):
                if pyramid(i):
                    return True
            return False
        
        return pyramid(bottom)
        
```

https://leetcode.com/problems/maximum-number-of-occurrences-of-a-substring/
```python
class Solution:
    def maxFreq(self, s: str, maxLetters: int, minSize: int, maxSize: int) -> int:
        length = len(s)
        from collections import defaultdict, Counter
        d = defaultdict(int)
        for i in range(length-minSize+1):
            t = s[i:i+minSize]
            c = Counter(t)
            if len(c) <= maxLetters:
                d[t] += 1
        return max(d.values()) if d else 0
```

```golang
func sLetterCnt(s string) int {
	sMap := map[rune]bool{}
	for _, c := range s {
		sMap[c] = true
	}
	return len(sMap)
}

func max(f map[string]int) int {
	m := 0
	for _, v := range f {
		if v > m {
			m = v
		}
	}
	return m
}
func maxFreq(s string, maxLetters int, minSize int, maxSize int) int {
	freqMap := map[string]int{}
	sLen := len(s)
	for i := 0; i < sLen-minSize+1; i++ {
		if sLetterCnt(s[i:i+minSize]) <= maxLetters {
			freqMap[s[i:i+minSize]]++
		}
	}
	return max(freqMap)
}
```



