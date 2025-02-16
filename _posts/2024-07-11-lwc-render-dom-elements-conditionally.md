---
title: LWC 조건에 따라 모달 요소 노출하기 - 조건부 렌더링 사용법, 최신 가이드 - if:true, lwc:if 변경
author: Dynamo J
date: 2024-07-11 00:34:00 +0900
categories: [Salesforce, LWC]
tags: [lwc]
---

# LWC 조건부 렌더링 구현하기: 사용법 및 최신 가이드

## LWC의 컨디셔널 컴포넌트

LWC(Lightning Web Components)은 Salesforce 플랫폼에서 최신 웹 표준을 사용하여 사용자 인터페이스를 구축하는 강력한 프레임워크입니다. LWC는 컴포넌트 기반 아키텍처를 통해 코드의 재사용성과 유지보수성을 크게 향상시킵니다.

최근에 이전의 사용 방법( `if:true` , `if:false` 방식)에서 최근 업데이트 된 방식으로 코드를 수정하는 작업을 했어요. 이번 포스팅에서는 LWC에서 조건부 렌더링을 통해 컴포넌트의 표시 여부를 제어하는 방법에 대해 최근 업데이트 내용을 포함하여 알아보겠습니다.

### 1\. 조건부 렌더링이란?

조건부 렌더링은 특정 조건에 따라 컴포넌트를 렌더링할지 말지를 결정하는 것을 의미합니다. LWC에서는 JavaScript 표현식을 활용하여 HTML 템플릿 내에서 조건부 렌더링을 쉽게 구현할 수 있습니다. 이는 사용자의 입력이나 애플리케이션 상태에 따라 동적으로 UI를 업데이트하는 데 유용합니다.

### 2\. 디렉티브

최근 업데이트에서는 `if:true`와 `if:false` 디렉티브 대신 `lwc:if | elseif | else` 디렉티브를 사용하는 것이 권장됩니다. 이는 향후 `if:true`와 `if:false`가 폐기될 예정이기 때문입니다.

``` html
<template>
    <template lwc:if={isVisible}>
        <p>This paragraph is visible.</p>
    </template>
    <template lwc:else>
        <p>This paragraph is hidden.</p>
    </template>
</template>
```

위 예제에서 `isVisible`이 `true`일 경우 첫 번째 `<p>` 요소가 렌더링되고, 그렇지 않을 경우 두 번째 `<p>` 요소가 렌더링됩니다.

### 3\. 다중 조건 렌더링

LWC에서 다중 조건을 사용하여 복잡한 조건부 렌더링도 가능합니다. `lwc:if`, `lwc:elseif`, `lwc:else` 디렉티브를 조합하여 사용할 수 있습니다.

``` html
<template>
    <template lwc:if={isFirstCondition}>
        <p>First condition is true.</p>
    </template>
    <template lwc:elseif={isSecondCondition}>
        <p>Second condition is true.</p>
    </template>
    <template lwc:else>
        <p>Both conditions are false.</p>
    </template>
</template>
```

이 예제에서는 `isFirstCondition`이 `true`일 때 첫 번째 문장이, `isSecondCondition`이 `true`일 때 두 번째 문장이, 두 조건 모두 `false`일 때 세 번째 문장이 렌더링됩니다.

### 4\. 조건부 렌더링의 활용 예제

조건부 렌더링은 사용자 인터페이스의 다양한 부분에서 활용될 수 있습니다. 실제로 저는 Spinner를 보여주고 끄는 시점을 조정하기 위해 정말 정말 많이 사용하고 있습니다. 다른 예를 들면, 사용자의 로그인 상태에 따라 다른 메뉴를 보여주는 경우를 생각해 볼 수 있습니다.

``` html
<template>
    <template lwc:if={isLoggedIn}>
        <p>Welcome, user!</p>
        <button onclick={handleLogout}>Logout</button>
    </template>
    <template lwc:else>
        <p>Please log in.</p>
        <button onclick={handleLogin}>Login</button>
    </template>
</template>
```

여기서 `isLoggedIn` 변수에 따라 다른 메시지와 버튼이 렌더링됩니다. 이는 사용자 경험을 크게 향상시킬 수 있습니다.

### 5\. 결론

LWC의 조건부 렌더링 기능을 활용하면, 동적이고 반응적인 웹 애플리케이션을 쉽게 구축할 수 있습니다. `lwc:if|elseif|else` 디렉티브를 통해 다양한 조건에 따라 컴포넌트를 렌더링하여 사용자에게 최적화된 UI를 제공할 수 있습니다. 이러한 기능을 적절히 활용하여 보다 나은 사용자 경험을 제공하는 LWC 애플리케이션을 개발해 보시기 바랍니다.

자세한 내용과 예제 코드는 [LWC 조건부 렌더링 가이드](https://developer.salesforce.com/docs/platform/lwc/guide/create-conditional.html)에서 확인하실 수 있습니다. 질문이나 추가적인 정보가 필요하시면 댓글로 남겨주세요!

---

#### 참고 자료

-   [Render DOM Elements Conditionally](https://developer.salesforce.com/docs/platform/lwc/guide/create-conditional.html "Render DOM Elements Conditionally")