---
title: "[프로그래머스] 봉인된 주문 오답노트"
categories: algorithm
tags: c++
toc: true
author_profile: false
sidebar:
    nav: "docs"
search: true
use_math: true
---

## 첫 코테

이번에 대학 동아리에 들어가고, 코딩 테스트를 풀게 되었다.

호기롭게 프로그래머스 3단계에 도전하고 크게 꺾인 뒤, 

이 오답노트를 작성한다.

## 문제

주문 어쩌구 뭐가 말이 많은데, 요약하면 이렇다.

- 특정 규칙으로 나열된 문자열들이 있다.

- 그 문자열들에서 입력한 문자열 배열에 포함된 문자열을 제한다.

- 이렇게 제한 문자열에서 입력한 정수 번째에 올 문자열은 무엇일까?

특정 규칙이란 a부터 z까지, z 다음에는 aa, ab이고, zz 다음에는 aaa.....

이렇게 이어지는 알파벳, 즉 26진수다.

여기서 입력한 문자열 배열이 만약 a, b이고,

입력한 숫자가 4라면

a, b, c, d, e, f...... 에서

a와 b를 제한 문자열들의 4번째 문자 즉,

c, d, e, f.... 이니 출력은 f가 된다.

일단 내가 처음에 풀어본 풀이를 보자.


## Solve 1

<pre>
<code>
#include <string>
#include <vector>

using namespace std;

string solution(long long n, vector<string> bans) 
{

    string subAnswer;

    vector<short> numArray;
    numArray.resize(n / 26 + 1);

    int numCount = 1;
    int  ascii = 96;

    for (int num = 0; num < n; num++)
    {
        for (int i = 0, num1 = 0; i < 1; i++)
        {
            if (numArray[num1] >= 26)
            {
                numArray[num1] = 1;
                i--;
                num1++;
                if (num1 + 1 > subAnswer.length())
                {
                    numCount++;
                }
                continue;
            }

            numArray[num1]++;
        }
        subAnswer.clear();

        for (int i = numCount - 1; i > -1; i--)
        {
            string temp;

            temp += char(ascii + numArray[i]);

            subAnswer.append(temp);
        }

        for (int i = 0; i < bans.size(); i++)
        {
            if (subAnswer == bans[i])
            {
                num--;
            }
        }
    }
    string answer = subAnswer;
    return answer;
}
</code>
</pre>

뭔가 벌써 개판이다.....

일단 문제를 차근차근 짚어보자.

수정 테스트