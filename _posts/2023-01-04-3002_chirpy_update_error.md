---
date: 2023-01-04 20:38:00 +/-0900
title: "[Gibhub Action] Chirpy 업데이트 및 오류 해결"
categories: [Develop, distribute]
tags: [개발(develop), 블로그(blog), 배포(distribute), 깃헙액션(github_action), 깃헙페이지(github_page)]

---
## 개요
현재 블로그의 테마인 Chirpy의 버전을 v5.0.2에서 v5.4.0으로 업데이트하는 과정과 오류에 대한 글입니다.

github action과 github page에 대해 간단히 살펴보겠습니다.

---
## 발단: 테마 업데이트를 해보자!

> 'Chirpy 버전을 업데이트해봐야겠다'

늘 그렇듯 시작을 간단했습니다.

업데이트의 이유는 다음과 같았습니다.

1. 기존 페이지의 여러 오류
    - 기존 페이지의 경우, search.json이 없다는 오류와 함께 검색이 올바르게 되지 않았습니다.
    - mathjax의 지원 사실을 최근에서야 확인하였습니다. 이전에는 _layout/와 _include/를 통해 is_math가 설정되어 있을 때, 해당 스크립트를 가져오도록 설정하였습니다.

2. 페이지의 생성 시간 저하와 생산성 감소
    - 1번 항목과 연결되는 내용으로 생성 시간이 지나치게 느려졌습니다.
    - 결과적으로 블로그의 글을 작성할 때마다 조금씩 생산성이 감소하게 됩니다.

---
## 전개: Jekyll 블로그의 장점 = _post만 옮기면 글이 유지된다!

Jekyll은 모든 글이 _post/에 마크다운 형식으로 저장됩니다.

따라서 블로그의 테마를 변경하거나 마크다운을 지원하는 다른 플랫폼으로 이동할 때 용이합니다.

[chirpy-starter](https://github.com/cotes2020/chirpy-starter)을 활용해 23.01.02 기준 최신 버전인 v5.4.0으로 업데이트를 진행합니다.

---
## 위기: 배포의 실패

그렇게 순조롭게 로컬과 Github 저장소에서 테스트를 마친 뒤 push를 하고 기다렸습니다.

그런데 어찌된 영문인지 빌드부터 실패합니다.

![첫 실패](/assets/img/develop/3002/3002_01_first_fail.png)
_[그림01] 첫 실패_

다시 블로그를 운영하고자 github actions의 로그를 확인하면서 push를 반복하게 됩니다.

---
## 절정: Fail, Fail, Fail, ...

### 1. Liquid Exception: Could not locate the included file

처음 만난 오류는 ```Liquid Exception: Could not locate the included file 'lang.html' ...``` 오류입니다.

mathjax의 도입을 위해 생성한 lang.html을 제거하는 과정에서 발생한 오류였습니다.

해결하기 위해 _layouts/에서 lang.html 파일을 올바르게 삭제하고 github에 push 했습니다.

### 2. HTML-Proofer found XXXXX failures!

2번째 만난 오류는 ```HTML-Proofer found XXXXX failures!```입니다.

이는 리소스를 찾아오지 못해 발생한 오류였습니다.

_config.yml 파일에서 base_url 항목을 수정하여 해결하였습니다.

### 3. Failed to create deployment (status: 400) with build version XXXXX. Responded with: Invalid deployment branch and no branch protection rules set in the environment. Deployments are only allowed from gh-pages

2번째 오류를 끝으로 build는 성공합니다.

하지만 deploy에서 또다시 문제가 발생합니다.

3번째 오류는 ```Error: Failed to create deployment (status: 400) with build version b3f58e0ffc7dfdbeeaffe32dffdd327cf52935a3. Responded with: Invalid deployment branch and no branch protection rules set in the environment. Deployments are only allowed from gh-pages```입니다.

이 오류는 chirpy 테마의 버전이 올라가면서 이전과 deploy 방식이 달라진 것이 원인이었습니다.

![chirpy deploy 변화](/assets/img/develop/3002/3002_02_chirpy_deploy_change.png)
_[그림02] deploy 방식 변화_

이는 pages 설정에서 source를 **<u>GitHub Actions</u>**로 수정하여 해결하였습니다.

![github pages의 2가지 방식](/assets/img/develop/3002/3002_03_github_pages_source.png)
_[그림03] github pages의 2가지 방식_

2가지 방식의 차이를 찾아본 결과를 다음 표로 작성하였습니다.

|구분|Branch|GitHub Actions workflow|
|---|---|---|
|제어|전체 컨텐츠와 게시 타이밍 제어 가능|workflow에 의존적이므로 정확한 게시 타이밍의 제어 어려움|
|유연성|비교적 떨어짐|workflow 구성을 통해 유연성 확보|
|협업|branch가 생성되므로 직접 접근하여 merge 등 가능|workflow의 완료까지 접근 불가|
|시인성|branch를 통해 확인|workflow의 세팅에 따라 다르지만 지연 발생|

정리하자면 branch의 이점을 살리는 방식과 workflow의 유연성을 살리는 방식으로 나뉘었습니다.

Github Actions에 대한 자세한 설명은 [다음 공식 문서 링크](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)를 참조해주세요!

---
## 결말: 배포 완료, 안정화가 되다

![안정화된 github action](/assets/img/develop/3002/3002_04_github_action_result.png)
_[그림04] 안정화된 github action_

3개의 오류를 해결하고서야 배포가 안정화되었습니다.

이렇게 오류를 해결하면서 github actions에 대해 조금이나마 살펴보고 공부할 수 있었습니다.

현재 글은 공부 과정에서 남긴 글로 오류가 있을 가능성이 높습니다! 조언과 댓글은 언제나 환영입니다.

그럼 다음 글에서 뵙겠습니다.

감사합니다. 😀

---
## 참고 문헌
- Understanding GitHub Actions, [https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)
- Configuring a publishing source for your GitHub Pages site, [https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
