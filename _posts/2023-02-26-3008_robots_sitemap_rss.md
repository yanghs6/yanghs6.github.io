---
date: 2023-02-26 22:48:00 +/-0900
title: "[Web] robots, sitemap, RSS"
categories: [Develop, web]
tags: [개발(develop), 웹(web), 블로그(blog), 검색엔진(search_engine), 웹크롤링(web_crawling), 웹스크래핑(web_scraping) robots, sitemap, rss]

---
## 개요

안녕하세요.

이번 글에서는 robots.txt, sitemap.xml, RSS의 개념을 살펴보겠습니다.

---
## 외부의 접근

블로그를 운영하다보면 언젠가 구글 등을 통해 사람들이 우르르 몰려와 글을 참고하는 장면을 꿈꾸게 됩니다.

하지만 검색 엔진은 냉혹하고 양질의 글이라도 노출이 되지 않는 경우가 허다합니다.

그렇다면 어떻게 우리 사이트를 외부에서 접근하도록 노출시킬까요?

### 검색 엔진과 크롤링

구글과 같은 검색 엔진은 수많은 웹페이지를 크롤링하고 그 결과를 분석하여 인덱스를 생성하거나 수정합니다.

검색 엔진은 생성된 인덱스를 기반으로 가장 높은 품질의 검색 결과를 보여줍니다.

위키백과와 MDN에서는 크롤링을 "조직적, 자동화된 방법으로 월드 와이드 웹을 탐색하는 컴퓨터 프로그램"과 "웹 페이지에서 데이터를 수집하기 위해 Web을 체계적으로 탐색하는 프로그램"이라고 정의하고 있습니다.

쉽게 말해 검색 엔진은 어떤 사이트가 좋은지 모르니 일단 프로그램으로 모두 가져오고 가장 우수하다고 생각되는 사이트를 보여줍니다.

### 혹은 직접 등록하기

하지만 갓 만든 사이트라면 위의 크롤러 프로그램이 크롤링하지 못 할 수 있습니다.

그래서 검색 엔진들은 각자의 사이트를 등록하는 서비스(대개 웹마스터도구라고 불림)를 제공합니다.

다음은 구글, 네이버, 빙의 웹마스터도구 URL입니다.

- 구글 서치 콘솔: [https://search.google.com/search-console?hl=ko](https://search.google.com/search-console?hl=ko)
- 네이버 서치어드바이저: [https://searchadvisor.naver.com/](https://searchadvisor.naver.com/)
- 빙 웹마스터도구: [https://www.bing.com/webmasters/about?mkt=ko-kr](https://www.bing.com/webmasters/about?mkt=ko-kr)

---
## 내 사이트를 노출하는 3가지 파일

위의 크롤링과 관련된 대표적인 파일이 바로 오늘의 주제인 robots.txt, sitemap,xml, RSS.xml 파일입니다.

### ① robots.txt: 이건 괜찮지만 저건 안 됩니다

#### 개념

![robots.txt](/assets/img/develop/3008/3008_01_robotstxt.png)
_[그림01] robots.txt_

robots.txt는 크롤러에게 접근할 수 있는 url과 접근 불가 url을 알려주는 파일입니다.

주요 목적은 크롤러의 접근 제한과 크롤링에서 발생하는 트래픽 제어입니다.

#### 형식

다음 표와 같은 형식을 보입니다.

| 항목       | 설명                    |
| ---------- | ----------------------- |
| User-agent | 크롤러 이름             |
| Allow      | 접근이 허락된 url       |
| Disallow   | 접근이 금지된 url       |
| Sitemap    | sitemap.xml 파일의 경로 |

위의 형식에 따라 작성한 robots.txt 예시 파일입니다.

```text
# 모든 크롤러에 대해 /include 접근 제한
User-agent: *
Disallow: /includes/

# example_crawler_name에 대해 /include 접근 허가
User-agent: example_crawler_name
Allow: /includes/

# sitemap.xml의 주소
Sitemap: https://yanghs6.github.io/sitemap.xml
```

추가로 주요 검색엔진의 크롤러 이름은 다음 표에 정리했습니다.

이 외의 리스트는 [https://udger.com/resources/ua-list/crawlers](https://udger.com/resources/ua-list/crawlers)에서 확인해보세요!

| 이름         | User-Agent      |
| ------------ | --------------- |
| Google       | Googlebot       |
| Google image | Googlebot-image |
| Msn          | MSNBot          |
| Naver        | Yeti            |
| Daum         | Daumoa          |

#### 주의점

우선 robots.txt는 사이트의 루트에 위치해야 합니다.

예를 들어, 현재 블로그의 robots.txt 파일은 [yanghs6.github.io/robots.txt](/robots.txt)을 통해 접근할 수 있습니다.

또한 robots.txt는 일종의 권고안으로 강제성이 없습니다.

물론 대부분의 개발자들은 robots.txt의 내용을 따르므로 robots.txt를 무시하는 행위는 피하는 것이 좋겠죠?

### ② sitemap.xml: 이 사이트는 요렇게 생겼습니다

#### 개념

![sitemap](/assets/img/develop/3008/3008_02_sitemap.png)
_[그림02] sitemap.xml_

sitemap.xml은 사이트의 구조를 xml 형식으로 나타낸 파일입니다.

#### 형식

주요 태그는 다음과 같습니다.

| 태그           | 필수여부 | 설명                                                                                               |
| -------------- | -------- | -------------------------------------------------------------------------------------------------- |
| \<urlset\>     | required | - 파일 캡슐화 <br/> - 현재 프로토콜 명시                                                           |
| \<url\>        | required | - 각 url 객체의 부모 태그                                                                          |
| \<loc\>        | required | - 페이지의 url <br/> - 프로토콜로 시작                                                             |
| \<lastmod\>    | optional | - 마지막 수정일                                                                                    |
| \<changefreq\> | optional | - 페이지 수정의 주기 <br/> - 다음 항목 존재: always, hourly, daily, weekly, monthly, yearly, never |
| \<priority\>   | optional | - url의 우선순위 <br/> - 기본값은 0.5                                                              |

sitemap.xml 형식에 따라 작성한 예시 파일입니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
   <url>
      <loc>https://yanghs6.github.io/</loc>
      <lastmod>2023-01-01</lastmod>
      <changefreq>daily</changefreq>
      <priority>0.8</priority>
   </url>
   <url>
      <loc>hhttps://yanghs6.github.io/about</loc>
      <changefreq>weekly</changefreq>
   </url>
</urlset>
```
#### 주의점

sitemap.xml 파일은 반드시 `UTF-8`로 인코딩되어야 합니다.

파일이 올바르게 인식되지 않는다면 인코딩을 확인해보세요!

또한 sitemap.xml은 `<loc>` 태그를 제외한 나머지 태그는 옵션입니다.

예를 들어 구글은 `<changefreq>`, `<priority>` 태그를 무시하고 크롤링하는 것으로 알려져있습니다.

따라서 해당 태그들에 대한 의존은 지양해야 합니다.

### ③ RSS: 새로운 글 알려드립니다

#### 개념

![rss](/assets/img/develop/3008/3008_03_rss.png)
_[그림03] rss_

RSS는 Really Simple Syndication의 줄임말로 웹 컨텐츠 신디케이션 형식입니다.

여기서 신디케이션이란 웹2.0에서 제안된 개념으로 콘텐츠 공급자와 수요자 사이를 연결해주는 사이버 콘텐츠 중개사업을 의미합니다.

다시 말해 콘텐츠를 작성한 사람이 콘텐츠의 대한 정보를 RSS로 전달하고, 콘텐츠를 원하는 사람이 RSS를 해석해서 그 정보를 얻는다고 생각할 수 있습니다.

#### 형식

RSS 역시 필수 여부에 따라 필드를 구분할 수 있습니다.

다음 설명은 원본을 요약, 해석한 것으로 보다 정확한 내용은 이 링크([https://www.rssboard.org/rss-specification](https://www.rssboard.org/rss-specification))를 확인해주세요!

필수 태그는 다음 3가지입니다.

| 요소        | 설명                |
| ----------- | ------------------- |
| title       | 채널 이름           |
| link        | 채널에 대응하는 URL |
| description | 채널 요약 정보      |

위의 3가지 태그 외의 태그들입니다.

| 요소           | 설명                                                                 |
| -------------- | -------------------------------------------------------------------- |
| language       | 채널의 언어                                                          |
| copyright      | 채널 저작권                                                          |
| managingEditor | 콘텐츠 편집자의 이메일 주소                                          |
| webMaster      | 채널 기술 관련 담당자의 이메일 주소                                  |
| pubDate        | 채널의 출판일                                                        |
| lastBuildDate  | 채널의 콘텐츠가 바뀐 마지막 수정일                                   |
| category       | 채널이 속한 1개 이상의 분류. <item> 레벨의 카테고리 수준을 따름      |
| generator      | 채널 생성에 사용된 프로그램                                          |
| docs           | RSS 파일을 사용된 형식의 문서를 가리키는 URL.                        |
| cloud          | Allows processes to register with a cloud to be notified of updates to the channel, implementing a lightweight publish-subscribe protocol for RSS feeds. (해석 실패...) |
| ttl            | time to live. 소스에서 새로 고치기 전 채널을 캐시할 수 있는 시간(분) |
| image          | 채널과 함께 표시될 수 있는 GIF, JPEG, PNG 이미지                     |
| rating         | PICS[https://www.w3.org/PICS/](https://www.w3.org/PICS/) 점수        |
| textInput      | 채널과 함께 표시될 수 있는 텍스트 입력 박스                          |
| skipHours      | 수신자가 건너뛸 수 있는 시간                                         |
| skipDays       | 수신자가 건너뛸 수 있는 일자                                         |

위의 형식에 따라 작성한 RSS 파일의 예시입니다.

```xml
<rss version="2.0">
    <channel>
        <title>Liftoff News</title>
        <link>http://liftoff.msfc.nasa.gov/</link>
        <description>Liftoff to Space Exploration.</description>
        <language>en-us</language>
        <pubDate>Tue, 10 Jun 2003 04:00:00 GMT</pubDate>
        <lastBuildDate>Tue, 10 Jun 2003 09:41:01 GMT</lastBuildDate>
        <docs>http://blogs.law.harvard.edu/tech/rss</docs>
        <generator>Weblog Editor 2.0</generator>
        <managingEditor>editor@example.com</managingEditor>
        <webMaster>webmaster@example.com</webMaster>
        <item>
            <title>Star City</title>
            <link>http://liftoff.msfc.nasa.gov/news/2003/news-starcity.asp</link>
            <description>How do Americans get ready to work with Russians aboard the International Space Station? They take a crash course in culture, language and protocol at Russia's <a href="http://howe.iki.rssi.ru/GCTC/gctc_e.htm">Star City</a>.
            </description>
            <pubDate>Tue, 03 Jun 2003 09:39:21 GMT</pubDate>
            <guid>http://liftoff.msfc.nasa.gov/2003/06/03.html#item573</guid>
        </item>
        <item>
            <description>Sky watchers in Europe, Asia, and parts of Alaska and Canada will experience a <a href="http://science.nasa.gov/headlines/y2003/30may_solareclipse.htm">partial eclipse of the Sun</a> on Saturday, May 31st.</description>
            <pubDate>Fri, 30 May 2003 11:06:42 GMT</pubDate>
            <guid>http://liftoff.msfc.nasa.gov/2003/05/30.html#item572</guid>
        </item>
        <item>
            <title>The Engine That Does More</title>
            <link>http://liftoff.msfc.nasa.gov/news/2003/news-VASIMR.asp</link>
            <description>Before man travels to Mars, NASA hopes to design new engines that will let us fly through the Solar System more quickly. The proposed VASIMR engine would do that.</description>
            <pubDate>Tue, 27 May 2003 08:37:32 GMT</pubDate>
            <guid>http://liftoff.msfc.nasa.gov/2003/05/27.html#item571</guid>
        </item>
        <item>
            <title>Astronauts' Dirty Laundry</title>
            <link>http://liftoff.msfc.nasa.gov/news/2003/news-laundry.asp</link>
            <description>Compared to earlier spacecraft, the International Space Station has many luxuries, but laundry facilities are not one of them. Instead, astronauts have other options.</description>
            <pubDate>Tue, 20 May 2003 08:56:02 GMT</pubDate>
            <guid>http://liftoff.msfc.nasa.gov/2003/05/20.html#item570</guid>
        </item>
    </channel>
</rss>
```

#### 주의점

RSS 자체에 대한 이용량이 감소하고 있습니다.

RSS 구독을 위한 RSS 리더가 필요한 점, 사용자가 RSS 주소를 직접 찾아야 하는 점, 광고 등 수익 창출이 어려운 점 등이 이유입니다.

---
## 마무리하며

이번 글에서는 robots.txt, sitemap, RSS에 대해 알아보았습니다.

앞으로 사이트를 운영하면서 어떤 방식을 통해 사이트를 노출할지 공부할 수 있었습니다!

이 글이 조금이나마 도움이 되셨으면 합니다.

감사합니다. 😀

---
## 참고 문헌

- RFC 9309 Robots Exclusion Protocol, [https://www.rfc-editor.org/rfc/rfc9309.html](https://www.rfc-editor.org/rfc/rfc9309.html)
- sitemaps.org - Protocol, [https://www.sitemaps.org/protocol.html](https://www.sitemaps.org/protocol.html)
- RSS 2.0 Specification (Current), [https://www.rssboard.org/rss-specification](https://www.rssboard.org/rss-specification)
- 웹 크롤러, [https://ko.wikipedia.org/wiki/웹_크롤러](https://ko.wikipedia.org/wiki/웹_크롤러)
- 웹 2.0, [https://ko.wikipedia.org/wiki/웹_2.0#콘텐츠_신디케이션](https://ko.wikipedia.org/wiki/웹_2.0#콘텐츠_신디케이션)
- Crawler, [https://developer.mozilla.org/ko/docs/Glossary/Crawler](https://developer.mozilla.org/ko/docs/Glossary/Crawler)
- Crawlers __ udger.com, [https://udger.com/resources/ua-list/crawlers](https://udger.com/resources/ua-list/crawlers)
- robots.txt 10분 안에 끝내는 총정리 가이드, [https://seo.tbwakorea.com/blog/robots-txt-complete-guide/](https://seo.tbwakorea.com/blog/robots-txt-complete-guide/)
