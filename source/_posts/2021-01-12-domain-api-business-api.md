---
layout: post
title:  "비즈니스 중심 API vs 도메인 중심 API 설계에 대한 고찰"
date:   2021-01-12 20:00:00 +0900
categories: java
---

API 개발자로 살면서 API를 어떻게 개발해야 좋은 구조를 가지는 것인가에 대한 생각은 정말 끊임이 없었다. 나는 글은 이러한 나의 고민에 대한 결과를 남겨두려고 쓴다. 말 그대로 이 글은 저자의 주관적인 생각이 포함되어 있기 때문에 맹목적인 신뢰는 지양한다.

### 비즈니스 중심의 API

내가 회사에서 API를 만드는 경우는 기본적으로 API 제작 요청이 들어온 경우다. 즉 그말은 특정 API를 사용해야할 고객이 있다는 이야기이고 그 고객을 위한 맞춤 API를 제작한다. 이 고객은 우리 API를 사용하는 클라이언트 개발자일 것이다. 여기서 한가지 인지해야하는 점은 바로 고객이 있다는 점이다. 비즈니스 중심 API 라는 단어는 말 그대로 비즈니스에 맞추어 API를 만든다는 것이다. 만약에 비즈니스에서 API 개발자에게 A, B, C 라고 구분되는 기능을 요구할 경우, 비즈니스 중심의 API는 A, B, C를 함께 버무려서 요청한 클라이언트 개발자가 사용하기 쉽도록 해준다.

기본적으로 API를 비즈니스 기준으로 설계하는 방법은 조금은 오래된 기술로 볼 수 있다. 그 배경은 React, Vue 와 같은 프론트 쪽의 라이브러리, 프레임워크가 나타나기 전에는 Javascript의 영향력은 지극히 낮았다. Javascript를 사용하지 않고 프론트 화면을 구성하려면 기본적으로 jsp, Thymeleaf 와 같은 기술로 서버 사이드 렌더링을 해야하는데 서버 사이드 렌더링은 기본적으로 프론트 쪽으로 비즈니스 로직을 가져갈 수 없다. 그 자체로는 동적인 처리를 할 수 없기 때문이다. 그렇기 때문에 API를 설계할 때에도 A, B, C 세 기능을 요구한다면 이 클라이언트의 비즈니스에 맞게 모두 내려줄 수 밖에 없었다.

### 클라이언트 사이드 렌더링의 시작

Javascript 기술의 활용도가 급격히 증가함에 따라서 React, Vue 와 같은 라이브러리의 사용이 급증하였고, 이로 인해 변한 것은 매우 많지만 그 중 하나는 클라이언트 쪽에서 비즈니스 로직을 처리할 능력을 가지게 된 것이다. 물론 그 전에도 순수 Javascript 만으로 처리는 할 수 있었지만, 그러한 개발 방식이 대중화 되어 있지는 않았다. 프론트 쪽 프레임워크의 사용과 함께 이런 개발 패턴이 변하게 된 것이다. 

클라이언트 사이드 렌더링을 하게 되면 비즈니스 로직을 클라이언트 쪽에서 처리할 수 있음으로 API를 클라이언트의 비즈니스 요구에 맞게 맞추어서 내려보내지 않아도, 클라이언트에서 여러 API를 짜집어 사용할 수 있다. 예를 들어서 위의 경우 A, B, C의 기능을 제공하기 위해 이 세가지를 버무리고 한 API로 내려줬지만, 클라이언트 사이드 렌더링을 하는 고객이라면 A, B, C 기능을 독립적인 API로 두고 이를 클라이언트가 원하는대로 사용하게 할 수 있다.

이렇게 클라이언트의 요청에 맞추어 API 구조를 설계하는 것이 아니고, API 서비스르 하는 서버의 도메인을 기준으로 API 를 만들 수 있게 된 것 이다.

### 도메인 중심의 API

기본적으로 OpenAPI 라는 것을 개발자라면 들어본 적이 있을 것이다. 위에서 우리가 만든 API와 OpenAPI의 가장 큰 차이점은 API를 만드는 시점에 고객이 있는가 없는가의 차이다. OpenAPI는 특정 고객을 위한 API가 아닌 말 그대로 열린 API를 만드는 것이기 때문에 어떠한 비즈니스에 종속적이지 않은 구조로 API를 설계하는 것이 중요하다. 보통 이러한 구조를 만들어 내개 위해 API 서비스 도메인 중심의 설계를 한다. 

### API 설계 방식에 대한 결론

그렇다면 도메인 중심의 API가 최근의 트렌드이기 때문에 옳은가? 라는 질문은 사실 적절하지 않다. 최근에도 서버 사이드 렌더링이 가지고 있는 장점을 다시 살리기 위해서 Next.js 등의 기술들이 등장하고 있다. 즉 API 설계를 할때 위 두가지 방식이 다른 특성을 가지고 있으며, 적재적소에 알맞은 형태의 API를 구현하는 것이 결국엔 정답이다. 하지만 한가지 명확하게 둘 수 있는 것은, API 서비스 코드를 작성할 때 도메인의 원자성을 살리지 않고 비즈니스에 맞춰진 코드를 선행하여 만들게 되면, 후에 도메인 중심의 API를 만드는 것은 사실 어렵다. 이미 코드들이 특정 비즈니스에 종속이 되어있기 때문이다. 하지만 반대로 도메인 중심으로 코드를 작성한 후에 비즈니스에 맞춘 API를 구현하는 것은 상대적으로 더 쉽다. 왜냐하면 독립적인 도메인 기반 코드들을 서로 버무려주기만 하면 되니까 말이다. 

