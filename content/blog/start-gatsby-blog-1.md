---
title: 'Gatsby(개츠비) 블로그 시작하기 1 - 개츠비란 무엇인가?'
date: 2019-11-21 10:37:00
category: 'Tech'
---

기존 블로그는 Ghost라는 CMS를 사용한 블로그였다. 그러나 내가 블로그를 사용하면서 하는 일은 markdown 문서를 먼저 만들고 그걸 ghost의 편집기에 붙여넣어서 포스팅을 하는 일 뿐이었다. 이게 전부라면 굳이 CMS를 쓰지 않고 정적 HTML만 생성하고 AWS가 아닌 다른 무료 호스팅 서비스를 이용해도 될 것이었다. 그래서 이 참에 블로그를 바꾸기로 결심하였고, Gatsby로 만드는 블로그를 Netlify를 통해 배포하였다. 블로그는 https://github.com/Fleta/fleta.log 레포지토리를 통해 관리되며 이는 [다른 유저](https://github.com/JaeYeopHan)분이 Gatsby-starter-blog를 이용해 만들어주신 블로그 템플릿을 fork 하여 만들었다. 이 과정에서 Gatsby에 대한 이해가 좀 부족한 것 같아 찾아보았고, 나름 정리한 내용을 블로그에 남겨보고자 한다.


## 1. 정적 사이트 생성기

가장 널리 알려진 정적 사이트 호스팅 서비스는 [Github Pages](https://pages.github.com/)인데, markdown을 html 형태로 바꿔서 Github Pages로 배포하기 위해서 사용하는 툴 중 하나가 [Jekyll](https://jekyllrb-ko.github.io/)이고, 이런 툴들을 보고 정적 사이트 생성기라고 부른다. 

## 2. Gatsby

이 글에서 Gatsby라고 부르는 [Gatsby JS](https://www.gatsbyjs.org/)는 React.js와 GraphQL을 사용한 정적 웹사이트 생성기이다. Javascript로 개발을 하고, Gatsby 자체에서 API를 제공하며, Markdown 이라는 Markup 언어를 사용할 수 있다. 이런 Stack을 보고 JAM Stack라고들 부르더라. Gatsby에 대한 내용을 찾아보면서 꽤 자주 보였던 단어였다.

![how-gatsbyjs-works](images/start-gatsby-blog/how-gatsby-works.png)

Gatsby의 경우 위의 구조와 같이 되어있는데, Datasource로부터 데이터를 가져오는데 GraphQL을 이용하고 있다. 이를 통해 가져온 데이터를 이용해 React를 통해 웹사이트를 만든다.

## 3. 대충 어떻게 사용하는지

```
npm install -g gatsby-cli
gatsby new hello-world https://github.com/gatsbyjs/gatsby-starter-hello-world
npm start
```

를 통해서 샘플 페이지를 시작해 볼 수 있다. 

[Gatsby가 제공하는 API 문서](https://www.gatsbyjs.org/docs/api-reference/)를 참고하여 개발을 할 수 있다. 

개발환경은 보통 `http://localhost:8000/` 으로 띄워질텐데, `http://localhost:8000/___graphql` 로 graphql query tool을 사용할 수 있다. 

markdown을 렌더링 할 때는 별도의 플러그인을 사용한다. 예를 들면 아래의 플러그인을 사용할 수 있다.

```
npm install --save gatsby-transformer-remark
```


---


Gatsby를 가지고 노는 건 조금 나중의 일로 하고, 다음 포스트에서 gatsby-starter-bee 레포를 포크해서 Gatsby 블로그를 Netlify, AWS Amplify 등으로 배포한 경험에 대해서도 소개하겠다. 