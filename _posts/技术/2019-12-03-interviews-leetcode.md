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

