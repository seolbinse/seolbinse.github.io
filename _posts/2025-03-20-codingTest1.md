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


## Solve_1

```c++
#include <string>
#include <vector>

using namespace std;

string solution(long long n, vector<string> bans) 
{
    string answer; // 정답을 입력할 문자열

    vector<short> numArray; // 가상 26진수로 사용할 배열
    numArray.resize(n / 26 + 1); // 배열의 크기 정의

    int numCount = 1; // 알파벳 자릿수
    int ascii = 96; // 매직넘버 지양용

    // 소문자 a는 97이나 가상 26진수의 비어있는 자리를 구분하기 위해 
    // 0을 1로 사용했기에 기본적으로 1을 뺌

    for (int num = 0; num < n; num++) // 입력된 정수만큼 반복문
    {
        for (int i = 0, num1 = 0; i < 1; i++) // 1번만 실행하는 for문
        { // num1은 현재 
            if (numArray[num1] >= 26) // 현재 자릿수가 26일 때,
            {
                numArray[num1] = 1; // 그 배열의 숫자를 1로 바꾸고,
                i--; // 반복문 반복을 위해 조건문의 숫자 i를 빼 줌
                num1++; // 다음 자릿수 검사로 넘어감
                if (num1 + 1 > answer.length()) 
                { // 만약 문자열의 길이보다,
                  // 현재 검사할 자릿수의 길이가 더 길다면,
                    numCount++; // 자릿수 변수에 1을 더함
                }
                continue; // 현재 자릿수가 26일 수도 있으니 컨티뉴로 넘어감
                // (근데 반복문 조건은 이상이라 의미 없음)
            }

            numArray[num1]++; // 1번만 반복할 시, 현재 자릿수에 1을 더하기
        }
        answer.clear(); // 이전에 입력한 값을 전부 지움

        for (int i = numCount - 1; i > -1; i--) // 자릿수만큼 반복
        { // 문자열 입력의 순서가 앞자리부터이기에,
          // 반복문의 조건을 자릿수로 시작함
            answer += char(ascii + numArray[i]);
            // 현재 자릿수에 해당하는 가상 26진수 배열의 원소에서 값을 가져와
            // 아스키코드를 대입해 문자열에 값을 추가함
        }

        for (int i = 0; i < bans.size(); i++) 
        { // 제외 문자열 검사
            if (answer == bans[i])
            {
                num--;
                // 제외할 문자열과 완성된 문자열이 일치하면,
                // for문을 1번 더 반복
            }
        }
    }

    return answer;
    // 전체 for 문이 다 끝나고 나면 출력과 일치하는 문자열이 남는다
}
```

뭔가 벌써 개판이다.....

변수 이름은 정답 맞추고 고쳐야지 했는데 결국 못 고쳤다.

일단 문제를 차근차근 짚어보자.

## 분석_1

일단 이 코드의 작동 방식은 이러하다.

- 정수 배열을 가상 26진수로 생각한다.

- 배열의 0번째 인덱스가 26이라면 1으로 만들고 다음 인덱스에 1을 더한다.

- 이렇게 1을 더한 다음 인덱스도 26이라면 그 인덱스를 0으로 만들고 다음 인덱스를 검사한다.

- 다음 26진수를 구했다면 이를 아스키코드에 대입해 문자열을 생성한다.

- 해당 문자열이 제외 문자열 배열의 원소와 겹치면, 전체 반복문에서 1을 빼 한번 더 반복한다.

- 이하 반복, 반복문이 끝난다면 제외할 문자열들을 제외했을 때 n번째 오는 문자열이 남는다.

이러한 구조이다.

우선 for 문이 너무 많다.......

```c++
for (int num = 0; num < n; num++) 
// 입력값에 따라서 일정하게 늘어나는 선형 구조
{ // O(n)
    for (int i = 0, num1 = 0; i < 1; i++) 
    // 특정 상황에서만 연산량이 늘어났다가 줄어드는 구조
    { // 대부분의 연산이 O(1)

    }

    for (int i = numCount - 1; i > -1; i--) 
    // 계속 늘어나지만 특정 조건에서만 연산량이 증가하는 로그 구조
    { // O(logn)

    }

    for (int i = 0; i < bans.size(); i++) 
    // 입력값에 따라서 일정하게 늘어나는 선형 구조 
    { // O(m) 맨 위와 표기가 겹치기에 일시적으로 m이라 표현

    }
}
```

BigO 표기법으로 정리하면 이러하다.

그러니 총 시간 복잡도는 O(n * logn * m)이 되는데,

코테에서는 10만을 넘어가는 입력도 테스트하기에 적절한 복잡도는 아니다.

특정 상황에서는 늘어나기도 하고.....

우선 시간 복잡도부터 줄여보자.

## 해시 테이블 사용

우선 큰 것부터 줄여보자.

O(m)의 경우, 배열 전체가 입력되고, 그 배열 내에서 원하는 값을 찾는 형식이다.

그 과정 외에는 별다른 연산이 없지만, 선형 구조이기에 꽤나 부담이다.

이 과정을 줄이기 위해, 예전에 DirectX를 배우며 사용했던 해시 테이블을 사용했다.

해시 테이블은 원하는 자료를 검색하는 과정의 시간 복잡도가 O(1)이기에,

이 부분만 바꿔도 전체 복잡도가 O(n * logn)로 줄어든다.

아 과정에서 해시 테이블은 STL에 내장되어 있는 unordered_set을 전처리하여 사용했다.

```c++
unordered_set<string> banHash(bans.begin(), bans.end());
// bans string 배열을 unordered 해시테이블에 넣어 시간복잡도를 O(1)로 만듦

// ... //

while (n > 0)
    // 해시테이블을 통한 접근이므로 시간복잡도가 O(1)
    if (banHash.find(answer) == banHash.end()) 
        n--;
```


이렇게 원하는 자료를 찾는 과정을 O(n)에서 O(1)로 줄였다.

## 불필요한 연산 제거

해시 테이블을 쓰고 머리를 조금 굴려 봤는데

굳이 26진수를 만들고 이걸 대입하는 게 아니라,

그냥 문자열을 순차적으로 증가시키는 형태로 만드는 게 낫지 않나 하는 생각이 들었고,

바로 그렇게 만들어 보았다.

```c++
// 다음 문자열 생성 알고리즘
int i = answer.length() - 1;
// 현재 자릿수를 변수로 만듦
while (i >= 0) 
{
    if (answer[i] < 'z')
    { // 현재 자릿수의 알파벳이 z보다 작다면 다음 알파벳으로
        answer[i]++;
        break;
    }
    else 
    { // z보다 크다면 현재 자릿수를 a로 만들고 다음 자릿수로 넘어감
        answer[i] = 'a';
        i--;
    }
}

if (i < 0) 
{ // i가 -1이라면 가장 높은 자릿수의 알파벳이 a로 바뀐 것이기에
  // 다음 자릿수를 추가해줌
    answer = "a" + answer;
}
```

이렇게 되었다.

완성본은 이러하다.

```c++
#include <string>
#include <vector>
#include <unordered_set>

using namespace std;

string solution(long long n, vector<string> bans) 
{
    unordered_set<string> banHash(bans.begin(), bans.end());  
    // bans string 배열을 unordered 해시테이블에 넣어 
    // 시간복잡도를 O(1)로 만듦
    string answer = "a"; 
    // 첫 번째 문자부터 시작해서 문자열 만들기(26진수에서의 1)


    while (n > 0) {
        // 해시테이블을 통한 접근이므로 시간복잡도가 O(1)
        if (banHash.find(answer) == banHash.end())
        {
            n--;
            if (n == 0) break; // n이 감소하고 조건 체크
        }

        // 다음 문자열 생성 알고리즘
        int i = answer.length() - 1;
        // 현재 자릿수를 변수로 만듦
        while (i >= 0) 
        {
            if (answer[i] < 'z')
            { // 현재 자릿수의 알파벳이 z보다 작다면 다음 알파벳으로
                answer[i]++;
                break;
            }
            else 
            { // z보다 크다면 현재 자릿수를 a로 만들고 다음 자릿수로 넘어감
                answer[i] = 'a';
                i--;
            }
        }

        if (i < 0) 
        { // i가 -1이라면 가장 높은 자릿수의 알파벳이 a로 바뀐 것이기에
          // 다음 자릿수를 추가해줌
            answer = "a" + answer;
        }
    }

    return answer;
}
```

확실히 간소화되었다.

BigO로 봤을 때는 해시 테이블만 줄여도 똑같이 O(n * logn)이지만,

이 표기법은 단순히 최악의 상황을 계산해주는 것이기에, 세부적으로는 불필요한 계산을 많이 줄였다.

그렇다면 이게 정답일까?

아니다.

여기서 조금 당황하긴 했다.

정답이 안 나와서가 아니라 원래 시간 복잡도부터 해결하고 공간 복잡도로 넘어가려 했으나,

시간 복잡도를 손봤는데 공간 복잡도 문제가 생기지 않고,

오히려 시간 복잡도를 더 손 봐야 했다.

<img data-action="zoom" src='{{ "/assets/images/250320_3.png" | relative_url }}' alt='absolute'>

이 정도면 접근하는 방식 자체가 잘못된 게 아닐까 싶었고, 곧 깨닫게 되었다.

애초에 이 문제에서 원하는 건 "특정 순서에 오는 문자열" 이다.

제외할 문자열을 뺀 모든 문자열을 구하는 문제가 아니다.

즉, 저 무수히 많은 n을 하나하나 계산한다는 것 자체가 시간 복잡도를 내다 버리는 일이다.

한참을 멀리 돌아가고 잘못 왔다는 걸 깨달은 뒤, 다시 풀어보도록 하자.

## 정답

```c++
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

const int MAX = 12; // 계산 한계 설정(최적화)
long long power26[MAX] = { 1 }; // 26의 거듭제곱을 저장, 첫 자리는 1

long long strtonum(const string& spell) 
{
    // 기본 값 0
    long long value = 0;

    // 주문 문자열의 알파벳만큼 반복
    for (char character : spell) {
        // value(현재 값)에 26을 곱해서 각 자릿수를
        // 다음 자릿수로 미룸
        // character - 'a'를 더해 1의 자리를 채움
        value = value * 26 + (character - 'a');
    }

    // 완성된 문자열->숫자 변환을 리턴
    return value;
}

string numtostr(long long number, int length) 
{
    // length는 문자열의 자릿수
    string spell_string(length, 'a');
    // 자릿수의 역순으로 문자열을 조합하여 순서 맞추기
    for (int i = length - 1; i >= 0; i--) 
    {
        // 현재 자릿수에 나누기의 나머지를 입력해서 알파벳 입력
        spell_string[i] = static_cast<char>('a' + (number % 26));
        // 26으로 나눠서 다음 자릿수 검사
        number /= 26;
    }

    // 완성된 문자열 출력
    return spell_string;
}

string solution(long long n, const vector<string> bans) 
{
    // 26의 거듭제곱을 배열에 넣음, 그 배열의 맨 첫번째는 1
    for (int i = 1; i < MAX; i++) 
    {
        power26[i] = power26[i - 1] * 26;
    }

    vector<vector<long long>> banNum(MAX);
    for (const string& spell : bans) 
    {
        // 금지 문자열의 크기(문자가 몇 개인지)
        // 문자의 개수별로 구분해 배열에 저장
        // banNum 1번 배열에는 1자릿수 금지 문자열만 담기 등...
        int len = spell.size();
        // 그 개수의 위치에서부터 숫자로 변환한 문자열을
        // 배열의 뒤에 밀어넣음
        banNum[len].emplace_back(strtonum(spell));
    }

    for (int i = 1; i < MAX; i++) 
    {
        // 각 저장 배열 내의 금지 문자열을 정렬
        sort(banNum[i].begin(), banNum[i].end());
    }

    for (int len = 1; len < MAX; len++) 
    {
        // 현재 자릿수 범위의 26진수 숫자(26의 거듭제곱)
        long long total = power26[len];
        // 현재 자릿수의 금지 문자열 크기
        long long blocked = banNum[len].size();
        // 자릿수 범위에서 금지 문자열 빼서
        // 현재 자릿수에서 가능한 숫자 개수 계산
        long long available = total - blocked;

        // 출력할 순서 숫자가 현재 자릿수에서 가능한 숫자들보다 크다면
        // n에서 가능한 숫자 조합을 빼고 다음 자리로 넘김
        if (n > available) 
        {
            n -= available;
            continue;
        }

        // 여기로 넘어왔다면 가능한 범위에 문자 조합이 있다는 뜻
        
        // 현재 자릿수 기준으로 n번째 숫자가 찾으려는 문자열
        long long remaining = n;
        // prev는 이전에 봤던 번호, selected는 선택한 번호
        long long prev = -1, selected = -1;

        // 현재 자릿수의 금지 숫자열 순회
        for (long long banNumber : banNum[len]) 
        {
            // 현재 금지 숫자와 현재 검사하고 있는 숫자의 차이
            long long gap = banNumber - (prev + 1);
            // 남아있는 검사할 숫자가 차이보다 작다면
            if (remaining <= gap) 
            {
                // 현재 선택된 문자열에 이전에 검사할 문자열과
                // 남아있는 문자열의 숫자를 더함
                selected = prev + remaining;
                break;
            }

            // 남은 숫자에 차이를 빼서 다음 숫자 검사
            remaining -= gap;
            // 이전에 검사한 번호를 현재 검사한 금지 숫자로
            prev = banNumber;
        }

        // 현재 자릿수에 금지할 숫자열이 없다면 수행
        if (selected == -1) 
        {
            selected = prev + remaining;
        }

        // 출력된 결과를 문자열로 바꿔서 리턴
        return numtostr(selected, len);
    }
}
```

문제의 결과는 이러하다.

찾을 순서의 숫자 내에 금지 문자열이 존재하는가 판단하고, 순서를 수정한 뒤

그 순서를 문자열로 변환하는 알고리즘이다.

순서가 많아지면 연산량이 비례해서 늘어나던 방식과 달리,

연산량이 많은 함수들의 시간복잡도가 log n이다.

즉, 연산량이 기하급수적으로 늘어나더라도 연산하는 속도는 크게 달라지지 않는다.

이상으로 코테 오답노트를 마친다.