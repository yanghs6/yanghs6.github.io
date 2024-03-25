---
date: 2024-02-17 13:02:00 +/-0900
title: "[Jekyll] Chirpy 테마에 custom sidebar 적용하기"
categories: [Develop, web]
tags: [개발(develop), 블로그(blog), jekyll, html, liquid]
description: Jekyll의 Chirpy 테마에 custom한 sidebar를 적용하는 과정을 담았습니다.

---
## 개요
안녕하세요.

이번 글은 Jekyll의 Chirpy 테마에 custom한 sidebar를 적용하는 과정을 담았습니다.

우선 Jekyll과 liquid 문법, 그리고 HTML의 optional tags에 대해 살펴봅니다.

---
## 간단히 살펴볼 내용들

### 1. Jekyll

Jekyll은 Ruby 언어를 기반의 정적인 웹사이트 생성기입니다.

markdown으로 작성된 페이지를 HTML로 변환합니다.

또한 github pages의 기본 설정으로 제가 사용하는 이 블로그도 jekyll의 여러 테마 중 chirpy을 사용중 입니다!

Ruby 기반이지만 해당 언어를 모르더라도 설치 및 배포에 문제가 없습니다. (저 역시 여기에 포함됩니다 😅😅)

### 2. Liquid

Liquid는 전자상거래 플랫폼 기업인 Shopify에서 만든 코딩 언어 템플릿입니다.

{% raw %}실제 HTML로 값을 보여줄 때 사용하는 ```{{}}```와 liquid 자체 문법에서 사용되는  ```{%%}```를 블록을 이용합니다.{% endraw %}

- 일반 Liquid
  {% raw %}
  ```liquid
  <h1>Test</h1>
  <div>

    {% for idx in (1..5) -%}
    {%- assign isValidate = idx | modulo: 2 -%}
    <div>
        <span>{{idx}} is <b>{% if isValidate == 1 %}Odd{% else %}Even{% endif %}</b></span>
    </div>
    {% endfor %}
  </div>
  ```
  {% endraw %}
- HTML 변환 이후
  ```html
  <h1>Test</h1>
  <div>

    {% for idx in (1..5) -%}
    {%- assign isValidate = idx | modulo: 2 -%}
    <div>
        <span>{{idx}} is <b>{% if isValidate == 1 %}Odd{% else %}Even{% endif %}</b></span>
    </div>
    {% endfor %}
  </div>
  ```

샘플 코드는 Liquid playground\([link](https://liquidjs.com/playground.html)\)에서 테스트할 수 있습니다!

### 3. HTML optional tags

HTML5에서 특정 태그는 생략할 수 있습니다.(`Certain tags can be omitted.`)

실제로 다음 2개 코드는 완벽하게 동일한 코드입니다.

- 일반 HTML
  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <title>Hello</title>
    </head>
    <body>
      <p>Hello, World!</p>
  
      <p>Here is a bullet list:
        <ul>
          <li>First bullet item</li>
          <li>Second bullet item has some <em>emphasis</em> in it</li>
        </ul>
      </p>
    </body>
  </html>
  ```
- HTML 생략
  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <title>Hello</title>
    <body>
      <p>Hello, World!
  
      <p>Here is a bullet list:
        <ul>
          <li>First bullet item
          <li>Second bullet item has some <em>emphasis</em> in it
        </ul>
  ```

Optional tags는 적용한다면 페이지 크기 감소, 시인성 향상 등의 장점이 있는데요.

반대로 특수한 경우에 생략된 태그를 인식하지 못한 오류 발생 등의 단점 역시 존재합니다.

---
## Cuetomizing sidebar

### 1\) 원본 Jekyll 가져오기

최근들어 조금씩 블로그를 수정 중에 있습니다.

그 중 첫 번째로 시도한 내용이 바로 사이드바 수정입니다.

저의 경우 사이드바에 홈, 메인 카테고리 - 서브 카테고리, 페이지 소개를 두고 싶었습니다.

그래서 사이드바를 덮어쓰기 위해 제가 사용한 테마의 sidebar.html 파일\([link](https://github.com/cotes2020/jekyll-theme-chirpy/blob/v5.4.0/_includes/sidebar.html)\)를 가져와 수정하기 시작했습니다.

### 2\) 사이드바 수정하기

우선 기존 사이드바에서 다음의 home과 about을 제외한 나머지 탭을 모두 제거합니다.

- home
  {% raw %}
  ```liquid
  <li class="nav-item{% if page.layout == 'home' %}{{ " active" }}{% endif %}">
    <a href="{{ '/' | relative_url }}" class="nav-link">
      <span class="ml-xl-3 mr-xl-3">{{ site.data.locales[site.lang].tabs.home | upcase }}</span>
    </a>
  </li>
  ```
  {% endraw %}

이제 카테고리와 세부 카테고리를 보여줄 차례입니다!

크게 나눠보자면 카테고리를 순회하면서 그 내부의 세부 카테고리도 함께 순회하는 구조입니다.

다만, liquid 문법의 한계로 코드가 다소 길어진 느낌이 있습니다...

{% raw %}
```liquid
{% for category in sort_categories %}
  {% assign category_name = category | first %}
  {% assign posts_of_category = category | last %}
  {% assign first_post = posts_of_category | first %}

  {% if category_name == first_post.categories[0] %}
    {% assign sub_categories = "" | split: "" %}

    {% for post in posts_of_category %}
      {% assign second_category = post.categories[1] %}

      {% if second_category == page_subcategory %}
        {% assign page_category = category_name %}
      {% endif %}
      
      {% if second_category %}
        {% unless sub_categories contains second_category %}
          {% assign sub_categories = sub_categories | push: second_category %}
        {% endunless %}
      {% endif %}
    {% endfor %}

    {% assign sub_categories = sub_categories | sort %}
    {% assign sub_categories_size = sub_categories | size %}
    
    {% capture collapse_id %}collapse_menu_{{group_index}}{% endcapture %}
    {% capture _category_url %}/categories/{{ category_name | slugify | url_encode }}/{% endcapture %}
    <li class="nav-item{% if _category_url == page.url or category_name == page.categories[0] or category_name == page_category %}{{ " active" }}{% endif %} h-auto">
      <a class="nav-link w-100">
        <button class="nav-link w-100" data-toggle="collapse" data-target="#{{ collapse_id }}" aria-expanded="false" aria-controls="{{ collapse_id }}" style="background:none; border:none">
          {{category_name | upcase }}
          {% assign top_posts_size = site.categories[category_name] | size %}
          <span class="badge ml-xl-1 mr-xl-1 post-badge">
            {{ top_posts_size }}
          </span>
        </button>
      </a>
    </li>

    <div id="{{collapse_id}}" class="collapse {% if _category_url == page.url or category_name == page.categories[0] or category_name == page_category %} show {% endif %} list-group-item {{collapse_id}}">
      <li class="nav-item{% if _category_url == page.url %}{{ " active" }}{% endif %} h-auto">
        <a href="{{ _category_url | relative_url }}" class="nav-link">
          <span class="ml-xl-3 mr-xl-3">See all</span>
        </a>
      </li>
      {% for sub_category in sub_categories %}
        {% capture _sub_ctg_url %}/categories/{{ sub_category | slugify | url_encode }}/{% endcapture %}
        <li class="nav-item{% if _sub_ctg_url == page.url or sub_category == page.categories[1] %}{{ " active" }}{% endif %} h-auto">
          <a href="{{ _sub_ctg_url | relative_url }}" class="nav-link">
            <span class="ml-xl-3 mr-xl-3">
            {{ sub_category |  capitalize }}
            {% assign posts_size = site.categories[sub_category] | size %}
              <span class="badge ml-xl-1 mr-xl-1 post-badge">
                {{ posts_size }}
              </span>
            </span>
          </a>
        </li>
      {% endfor %}
    </div>
    {% assign group_index = group_index | plus: 1 %}
  {% endif %}
{% endfor %}
```
{% endraw %}

### 3\) 그리고,,, 오류??

로컬에서 테스트를 통과했으니 이제 배포할 차례입니다!

근데 배포 이후 사이드바가 모두 깨져서 기존 컨텐트까지 침범하는 화면이 보이네요???

아무래도 무언가 놓친 부분이 오류를 낸 것 같습니다.

![로컬 개발 환경의 화면](/assets/img/develop/3015/3015_01_local_sidebar.png)
_[그림01] 로컬 개발 환경의 화면_

![배포 이후 화면](/assets/img/develop/3015/3015_02_deploy_sidebar.png)
_[그림02] 배포 이후 화면_

### 4\) 오류 파악 및 수정

배포 환경과의 옵션을 하나하나 비교해봤는데요.

`_config.html` 파일에서 다음의 코드를 발견합니다.

```yaml
compress_html:
  clippings: all
  comments: all
  endings: all
  profile: false
  blanklines: false
  ignore:
    envs: [development]
```

해당 옵션은 html을 압축하며 개발 환경에서 무시됩니다.

아무래도 유력한 후보라는 생각에 각각의 옵션을 확인해봤습니다.

그 결과 사이드바가 깨진 원인 `endings` 옵션을 찾았습니다!

조금 더 찾아보니 `endinsg` 옵션은 HTML에서 생략할 수 있는 종료 태그를 지우는 옵션이었는데요.

해당 규칙에 따라 태그를 지우면서 커스텀 사이드바에 영향을 끼쳤습니다.

오류 해결을 위해 해당 옵션을 주석으로 변경으로 처리했구요.

그리고 마침내 블로그의 사이드바 업데이트가 마무리되었습니다!!!

---
## 마무리하며

분명 단순하게 사이드바를 바꿔보자고 시작을 했는데요.

어쩌다보니 Jekyll에 이어 Liquid 문법, 그리고 생략 가능한 HTML 태그까지 배웠습니다.

새삼스럽지만 프론트엔드 개발자 분들을 다시 한 번 존경심을 갖게된 뜻 깊은 시간이었습니다...!!

이 글이 조금이나마 도움이 되셨으면 합니다.

감사합니다. 😀

---
## 참고 문헌

- 8 The HTML syntax — HTML5, [https://www.w3.org/TR/2011/WD-html5-20110405/syntax.html#optional-tags](https://www.w3.org/TR/2011/WD-html5-20110405/syntax.html#optional-tags)
- Liquid template language, [https://shopify.github.io/liquid/](https://shopify.github.io/liquid/)
- Playground, LiquidJS, [https://liquidjs.com/playground.html](https://liquidjs.com/playground.html)
- cotes2020_jekyll-theme-chirpy at v5.4.0, [https://github.com/cotes2020/jekyll-theme-chirpy/tree/v5.4.0](https://github.com/cotes2020/jekyll-theme-chirpy/tree/v5.4.0)
