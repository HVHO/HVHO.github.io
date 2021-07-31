---
layout: post
title : 한눈에 들어오는 깃허브 만들기
description: Readme.md 를 이용하여 깃허브 프로필을 예쁘게 꾸며보자
comments: true
tags: github
---


요새는 직장을 구할 때 깃허브 프로필도 함께 제출하라는 요청이 흔해졌고, 이를 위해 깃허브 프로필을 Resume 용도로 사용하는 사람이 많아졌다. 
깃허브에서도 이 트렌드를 눈치챘는지 얼마 전 부터 깃허브 프로필을 꾸밀수 있는 이스터에그 성격의 기능을 제공하게 되었다.  
이를 이용하여 깃허브 프로필을 꾸미는 방법을 알아보자

---

# 프로필용 Repository 만들기
깃허브 프로필을 사용하기 위하여 먼저 새로운 Repository 를 만들어야 한다.  
이 때 Repository 의 이름을 꼭 **자신의 username** 으로 설정해야 한다.

![create-repo](https://user-images.githubusercontent.com/25953981/127731395-1342f5b3-aaba-443c-9590-ff795782d00c.png)
<br>
그러면 위 사진과 같이 github profile 을 꾸밀수 있는 ester egg 가 적용된다.  
해당 문구에 나와있는 것 처럼, **공개범위를 public**, **readme.md와 함께 생성** 옵션을 선택하여 생성해주자.

![remaining-options](https://user-images.githubusercontent.com/25953981/127731498-184b5397-c515-4a98-b86c-d56c8400696c.png)
<br>
해당 작업을 끝내고 나의 프로필에 들어가보면, 프로필에 기본 문구가 설정되어 있는 것을 확인할 수 있다.
![default-profile](https://user-images.githubusercontent.com/25953981/127731544-749a7ece-b6dd-4fc2-8a61-6e44c907e8cf.png)*Hello World!*

---

# Readme 채우기

이제 프로필용 Repository 를 무사히 생성하였으니, 본격적으로 프로필을 꾸며보자!  
프로필을 꾸미는 행위는 모두 위에서 생성했던 Readme.md 파일에서 작업되며, 이를 꾸미기 위해 많은 개발자들이 third-party tool을 개발해 두었다.

## 프로필에 뱃지 추가하기

먼저 프로필에 뱃지를 추가하는 법을 알아보자.

![profile-badges](https://user-images.githubusercontent.com/25953981/127731740-04526635-a799-49f7-aa9b-3f71b45c7955.png){:width="50%" height="50%"}

이 뱃지들은 모두 [shield.io](https://shields.io)를 통해서 생성한 것 들인데,
shield.io 의 base url에 내가 원하는 parameter 을 추가하여 url 형태로 이미지를 만들 수 있다.

url 기본  폼은 아래와 같다.  
`https://img.shields.io/badge/<LABEL>-<MESSAGE>-<BACKGROUND-COLOR>?logo=<LOGO>&logoColor=<LOGO-COLOR>&style=<STYLE>`
  
변수들을 설명하면,


### Path  Parameter

- `LABEL : 레이블에 표시할 텍스트 ( Optional )` 
- `MESSAGE : 본체에 표시할 텍스트`
- `BACKGROUND-COLOR : 본체의 배경 색깔( white 나 #000000 두 형태 모두 표현 가능)`

예를 들어, `LABEL=label, MESSAGE=message, BACKGROUND-COLOR=white` 이면,  
url 은 `https://img.shields.io/badge/label-message-white` 이 되고,  
이미지는 아래와 같아진다.
![default-shield](https://img.shields.io/badge/label-message-white)
<br>
여기서 `LABEL` 은 `Optional` 이기 때문에 `message-white` 형태로만 표현할 수도 있는데, 이 경우 아래처럼 label 이 없는 상태로 나타난다.
![default-shield-without-label](https://img.shields.io/badge/message-white)


### Query Parameter (Optional)

- `LOGO : 뱃지에 설정할 로고`
- `LOGO-COLOR : 로고 색깔 ( white 나 #000000 두 형태 모두 표현 가능 )`
- `STYLE : 뱃지 스타일`
<br>
<br>

`LOGO` 변수에 사용할 수 있는 로고의 종류는, [simple-icons](https://simpleicons.org)에서 찾을 수 있으며, 해당 사이트에서 찾은 이름이 적용이 되지 않으면, `LOGO` 변수와 매핑이 다를 수 있으므로 [매핑 정보](https://github.com/simple-icons/simple-icons/blob/develop/slugs.md) 에서 확인한 이름으로 넣으면 된다. ( C++ 가 실제로는 cplusplus 로 매핑되어 있더라 )
<br>
<br>

`STYLE` 변수에 사용할 수 있는 종류는 5가지가 있다.
<br>
<table>
<tr>
<td><code>plastic</code></td>
<td><img src="https://img.shields.io/badge/label-message-green?style=plastic" 
style="margin: 0 0 0 0;" vertical-align:middle/> </td>
</tr>

<tr>
<td><code>flat</code></td>
<td><img src="https://img.shields.io/badge/label-message-green?style=flat" 
style="margin: 0 0 0 0;" vertical-align:middle/></td>
</tr>

<tr>
<td><code>flat-square</code></td>
<td><img src="https://img.shields.io/badge/label-message-green?style=flat-square" 
style="margin: 0 0 0 0;" vertical-align:middle/></td>
</tr>

<tr>
<td><code>for-the-badge</code></td>
<td><img src="https://img.shields.io/badge/label-message-green?style=for-the-badge" 
style="margin: 0 0 0 0;" vertical-align:middle/></td>
</tr>

<tr>
<td><code>social</code></td>
<td><img src="https://img.shields.io/badge/label-message-green?style=social" 
style="margin: 0 0 0 0;" vertical-align:middle/></td>
</tr>
</table>

<br>

### 프로필에 적용하기
자기가 원하는 스타일을 정했으면, url 을 만들어서 `readme.md` 에 추가하는 일만 남았다. 

`readme` 에 추가할 때는 `markdown` 의 문법 (`![image-name](source-link)`) 형태로 할 수 도 있고, `html` 의 `img` 태그 ( `<img src="souce-link"/>` ) 를 이용할 수 있으나 나는 편의를 위해서 html 태그를 이용했다.

하는 방법은 단순하다. 위에서 만든 url를 `img` 태그에 추가하여 `readme.md` 에 적기만 하면 된다. 

![badge-source](https://user-images.githubusercontent.com/25953981/127733319-ad83bdfb-3244-4a18-afab-f7eea03eb17b.png)*이렇게 readme 에 추가하면*
![badge-rendered](https://user-images.githubusercontent.com/25953981/127733364-8c036fe1-f5e2-4f1d-a35f-6e9b06cb3a4e.png)*이렇게 프로필에 나타난다!*

### 위 과정이 귀찮은 여러분을 위해
<details>
<summary>접기/펼치기</summary>

<table>
<tr>
<td><img src="https://img.shields.io/badge/Rust-000000?style=flat-square&logo=Rust" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/Rust-000000?style=flat-square&logo=Rust"</code></td>
</tr>

<tr>
<td><img src="https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=Python&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=Python&logoColor=white"</code></td>
</tr>

<tr>
<td><img src="https://img.shields.io/badge/C++-00599C?style=flat-square&logo=cplusplus&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/C++-00599C?style=flat-square&logo=cplusplus&logoColor=white"</code></td>
</tr>

<tr>
<td><img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=JavaScript&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=JavaScript&logoColor=white"</code></td>
</tr>

<tr>
<td><img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=TypeScript&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=TypeScript&logoColor=white"</code></td>
</tr>


<tr>
<td><img src="https://img.shields.io/badge/Java-007396?style=flat-square&logo=java&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/Java-007396?style=flat-square&logo=java&logoColor=white"</code></td>
</tr>


<tr>
<td><img src="https://img.shields.io/badge/Go-00ADD8?style=flat-square&logo=Go&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/Go-00ADD8?style=flat-square&logo=Go&logoColor=white"</code></td>
</tr>

<tr>
<td><img src="https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=Docker&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=Docker&logoColor=white"</code></td>
</tr>

<tr>
<td><img src="https://img.shields.io/badge/Terraform-7B42BC?style=flat-square&logo=Terraform&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/Terraform-7B42BC?style=flat-square&logo=Terraform&logoColor=white"</code></td>
</tr>

<tr>
<td><img src="https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=Kubernetes&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=Kubernetes&logoColor=white"</code></td>
</tr>

<tr>
<td><img src="https://img.shields.io/badge/React-61DAFB?style=flat-square&logo=React&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/React-61DAFB?style=flat-square&logo=React&logoColor=white"</code></td>
</tr>

<tr>
<td><img src="https://img.shields.io/badge/Spring-6DB33F?style=flat-square&logo=Spring&logoColor=white" style="margin: 0 0 0 0;"></td>
<td><code>"https://img.shields.io/badge/Spring-6DB33F?style=flat-square&logo=Spring&logoColor=white</code></td>
</tr>

</table>
</details>

<br>
<br>


## Github Stat 패널 추가하기

자신의 깃허브 통계를 패널로 추가할 수 있는 [github-readme-stat](https://github.com/anuraghazra/github-readme-stats) 서비스가 있다.

해당 서비스에서는 나의 깃허브 활동에 관련된 여러 패널을 제공하는데, 대표적으로 쓰이는 것 두가지만 소개하겠다.

### GitHub Stats Card

`https://github-readme-stats.vercel.app/api?username={username}&hide={hidecontents}`

![hvho's GitHub stats](https://github-readme-stats.vercel.app/api?username=HVHO&hide=contribs,prs)


### Top Languages Card

`https://github-readme-stats.vercel.app/api/top-langs/?username={username}&layout=compact`

![hvho's Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=HVHO&layout=compact)


<!-- 이 서비스도 동일하게 url 을 만들어 `img` 태그로 `readme.md` 에 추가하면 된다 -->

<br>
<br>

## Solved.ac 패널 추가하기

백준에서 문제 난이도와 자신의 랭킹을 확인할 수 있는 solved.ac 에 자신의 아이디가 등록되어 있다면,
이를 깃허브 프로필에 표시할 수 있는 [mazassumnida](https://github.com/mazassumnida/mazassumnida?fbclid=IwAR16rjEw6GyKdTVMfxas7t9vlFR5Ah790V9mYuVcVqYKoA_UacXrFIFcISA) ( 한글로 마자씀니다.. ) 서비스가 있다. 

<!-- 이 서비스도 동일하게 url 을 만들어 `img` 태그로 `readme.md` 에 추가하면 된다 -->

해당 서비스에서 4가지 종류의 패널을 제공하고 있는데, 

### v1
`http://mazassumnida.wtf/api/generate_badge?boj={username}`
![v1](http://mazassumnida.wtf/api/generate_badge?boj=hvho1119)

### v2
`http://mazassumnida.wtf/api/v1/generate_badge?boj={username}`
![v2](http://mazassumnida.wtf/api/v2/generate_badge?boj=hvho1119)

### pastel
`http://mazassumnida.wtf/api/pastel/generate_badge?boj={username}`
![pastel](http://mazassumnida.wtf/api/pastel/generate_badge?boj=hvho1119)


### mini
`http://mazassumnida.wtf/api/mini/generate_badge?boj={username}`
![mini](http://mazassumnida.wtf/api/mini/generate_badge?boj=hvho1119)


위 종류 중 맘에 드는 것을 골라 PS 실력을 뽐내자!

# 완성!

![my-profile](https://user-images.githubusercontent.com/25953981/127730857-c6ac82bb-4cb1-4136-8314-10a7630fe09f.png)*예뻐진 깃허브 프로필*

지금까지 깃허브 프로필을 꾸미는 방법에 대한 글이였습니다.
<br>
<br>
사실 깃허브 프로필 자체는 개발 실력이랑 하나도 상관없지만,
보기 좋은 떡이 먹기도 좋다는 말처럼 자신의 실력을 보기좋게 잘 포장하는 것도 중요한 생각합니다.  
프로필을 잘 꾸며 인사담당자한테 플러스 점수 좀 받아 보자구요~~

# References
- [github 공식 문서]("https://docs.github.com/en/github/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme")
- https://butter-shower.tistory.com/142



