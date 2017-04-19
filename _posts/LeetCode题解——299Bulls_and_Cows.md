title: LeetCode题解——299Bulls_and_Cows
categories: LeetCode##分类
tags: [C++,LeetCode]##标签，多标签格式为 [tag1,tag2,...]
keywords: C++,LeetCode##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 解题记录
date: 2016/07/28 14:24:25 
---

我的4ms解法
题目地址：[Bulls and Cows](https://leetcode.com/problems/bulls-and-cows/)
题目：
You are playing the following Bulls and Cows game with your friend: You write down a number and ask your friend to guess what the number is. Each time your friend makes a guess, you provide a hint that indicates how many digits in said guess match your secret number exactly in both digit and position (called "bulls") and how many digits match the secret number but locate in the wrong position (called "cows"). Your friend will use successive guesses and hints to eventually derive the secret number.

For example:

Secret number:  "1807"
Friend's guess: "7810"
Hint: 1 bull and 3 cows. (The bull is 8, the cows are 0, 1 and 7.)
Write a function to return a hint according to the secret number and friend's guess, use A to indicate the bulls and B to indicate the cows. In the above example, your function should return "1A3B".

Please note that both secret number and friend's guess may contain duplicate digits, for example:

Secret number:  "1123"
Friend's guess: "0111"
In this case, the 1st 1 in friend's guess is a bull, the 2nd or 3rd 1 is a cow, and your function should return "1A1B".
You may assume that the secret number and your friend's guess only contain digits, and their lengths are always equal.

题目大意
在Secret串和Guess串中找出位置相同且数字相同的数量A，并且计算出位置不一样但是数字相同的数量B

<!--more-->

以下是代码
``` c++
class Solution {
public:
    string getHint(string secret, string guess) {
        int countA = 0, countB = 0;
        int sizeA = secret.size(), sizeB = guess.size();
        int pos = 0;
        int markA[10] = {0},markB[10]={0};
        for (pos=0;pos < sizeA;pos++) {
            int tmpA=secret.at(pos)-48,tmpB=guess.at(pos)-48;
            if(tmpA==tmpB){
                countA++;
                continue;
            }
            markA[tmpA]++;
            markB[tmpB]++;
        }
        //计算B的大小
        for(int i=0;i<10;i++){
            if(markA[i]>0&&markB[i]>0){
                if(markA[i]>=markB[i]){
                    countB+=markB[i];
                }else{
                    countB+=markA[i];
                }
            }
        }
        return to_string(countA)+"A"+to_string(countB)+"B";
    }
};
```