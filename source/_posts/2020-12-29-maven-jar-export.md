---
layout: post
title:  "오픈소스 Jar 파일 커스터마이징 과정에 대한 고찰"
date:   2020-12-29 20:00:00 +0900
categories: java
---

일반적인 상황은 아니지만 유명하지 않거나, 크게 지원되지 않는 라이브러리를 사용할 때 라이브러리 자체에 결함이 존재할 수도 있다. 이렇게 되면 이러한 에러를 해결하기 위한 방법을 모색해야 하는데, 이 중에 권장되는 방법은 아니지만 우리 프로젝트에 맞추기 위해서 오류가 있는 부분의 소스를 수정하고 재 빌드하는 방법이 있다. 저작권과 관련된 이슈는 저자는 크게 잘 모르기 때문에 이부분에 대해서는 생략하며, 단지 필요하다면 오픈소스로 공개된 라이브러리에 대해서만 이러한 작업을 하길 바란다.

### jad를 활용해 직접 소스파일을 구하자

내가 오류를 발견한 라이브러리 파일에 대해서 jar 파일만 가지고 있고, 실제 소스 파일을 구할 수 없는 경우에는 jad 라는 프로그램을 활용해서 .class 파일을 .java 파일로 디컴파일 시켜야 한다.

[Jad Download](/res/2020-12-29-maven-jar-export/jad158g.win.zip)

Jad 프로그램은 패키지 단위로 .class 파일을 디컴파일 시킬수 있고 파일 단위로도 디컴파일이 가능하다. 디컴파일된 .java 파일을 원하는 방식으로 수정을 하고 난 후에 javac 명령어를 실행해서 다시 컴파일을 하자.

여기서 문제점은 현대 대부분의 프로젝트들은 gradle, maven과 같은 빌드 툴을 사용하고 있는데 여기에는 의존성 관리 기능이 포함된다. javac를 활용해서 빌드를 하게 될 경우 의존성들이 로컬에 존재하지 않기 때문에 라이브러리가 아니기 때문에 컴파일 오류가 나는 객체들이 많을 것이다. 이런 경우에는 gradle, maven 빌드 툴을 활용하여 빌드하여야 한다.

그러나 만약 라이브러리 사이즈가 크다면 이 빌드 툴을 설정하는 것 자체가 굉장히 큰 작업이 된다. 그래서 직접 소스파일을 디컴파일하여 소스를 수정하는 것은 현실적으로는 한계가 있다. 그래서 대안으로는 github 과 같은 사이트에서 실제로 운영되고 있는 해당 소스를 구하는 것이다. 대부분의 라이브러리들이 오픈소스로서 공개되어 있기 떄문에 사이트를 검색하다 보면 공식 소스들을 쉽게 구할 수 있다. 

#### 외부에서 공식 소스를 구해서 프로젝트를 만들자.

대부분 git으로 형상관리를 하기 때문에 github를 찾아보면 공식 소스를 찾을 수 있다. 여기서는 예제로 jira-client을 활용해보겠다. 내가 이 글을 써야겠다라고 마음 먹게된 계기의 라이브러리이다.

https://github.com/rcarz/jira-client

위 사이트가 소스 공식 사이트이다. 여기서 클론을 하던 각자가 원하는 방식으로 해서 로컬에 프로젝트를 구축하자. 나와 같은 경우  intellij 에서 github 소스를 바로 import하는 기능이 있어 이를 활용해서 로컬에 구축하였다.

그리고 나서 수정을 원하는 부분에 가서 소스를 변경하면 된다.

#### 빌드 툴을 활용하여 빌드하자.

intellj는 오른쪽 창들 중에 maven 관련 창이 있다 이 곳에서 Lifecycle -> install 을 클릭하면 자동으로 jar 파일을 떨군다. 콘솔을 확인하면 어느 경로에 jar 파일이 떨어지는 지 확인이 가능하다. 

빌드 설정이 어떻게 되어 있느냐에 따라서 바로 jar 파일이 성공적으로 만들어질 수도 있고 아닐수도 있다. 어떤 오류를 던지는지 잘 확인하고 오류에 맞추어서 빌드 설정을 바꿔주어야 하는 작업이 필요할 수도 있다.

나의 경험으로는 테스트 코드를 통과하지 못하는 경우가 있어서 이를  skip 하는 설정을 추가한 상황도 있고, parent 에 해당하는 의존성을 가져오지 못해 parent 부분을 제거해본 적도 있다. 모든 방법이 다 상황에 따라서 답이 아닐 수 있음으로 오류를 잘 읽어서 빌드 설정을 적절히 변경시켜주는게 중요하다.

#### 각 빌드 툴 저장소에 올리기

각 빌드 툴은 로컬 저장소나, 원격 저장소에 해당 의존성을 올릴 수 있는 기능을 제공한다. 이 기능을 활용하여 업로드 시키고, 본인 작업 프로젝트에서 의존성을 불러와서 사용하면 된다. 보다 옛스럽지만 간편한 방법으로는 해당 라이브러리를 로컬로 가지고 불러와도 된다.
