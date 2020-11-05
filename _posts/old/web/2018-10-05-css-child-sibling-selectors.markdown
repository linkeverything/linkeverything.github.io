---
title:      "Child & Sibling Selectors"
categories: [Old]
last_modified_at:       2018-10-05 23:59
tags:
  - web
  - css
published: false
---

## 들어가며

웹을 구현하다보면, 정확한 답이없이 그때그때 잘 맞춰서 구현하는게 맞다는 생각이 많이 듭니다. 그런데 개발을 진행하다보면, 또 검색을 하다보면, 답은 아니지만 이상적인 방향이라고 생각하는 부분이 분명하게 존재합니다. 

서버와 클라이언트의 하드웨어 성능이 좋아짐에 따라, 조금은 비 효율적인 방법을 사용하더라도 사용자 입장에서의 체감은 거의 없는 수준이 되었으나, 이러한 부분이 쌓이다 보면 전체적인 성능에 영향을 주게 되므로, 항상 염두에 두고 개발을 진행하는 것이 좋겠습니다. 

CSS영역 역시 비 효율적인 방법 - class를 부여하거나, id를 주거나, 여러 selector를 혼합해서 사용하거나 하는 등 - 을 이용해서도 얼마든지 개발할 수 있으나, 성능은 물론, CSS파일의 사이즈를 늘려 전체 성능에 분명히 영향을 주게 됩니다. 따라서 여기서는 조금 더 효율적으로 CSS selector를 구현하려는 노력의 일환으로 Child / Sibling 선택자에 대한 부분을 되짚어보고자 합니다. (사실 '선택자' 라는 단어를 무척 싫어하나 어쩔 수 없이 사용하겠습니다. 이하 selector로 통일)



## 구현하기

```css
ul li { margin: 0 0 5px 0; }
ul > li { margin: 0 0 5px 0; }
```

위 코드는 흔히 혼용하는 예제입니다. 일반적인 구조에서 저 코드는 동일하게 동작하는 것 처럼 보일 수도 있습니다. 구조가 복잡해지거나 하면 위 두 가지 코드가 왜 다른지 알 텐데, 거의 대부분의 경우에 중첩된 구조를 가급적 피해서 작업해서인지, 저 둘간의 차이를 경험으로 알아내기란 쉽지 않습니다. 

위 두 개의 selector는 모두 unordered-list 하위에 있는 list-item 들을 골라내서 margin 을 조정하는 selector이다. 겉으로 보기엔 똑같아 보이나, 실질적으로는 Children Selector와 Descendants Selector 의 개념이 숨어 있다. 아래 그림을 보고 더 상세하게 설명하겠습니다. 

![](/assets/images/posts/old/img/post/2018-10-05-css-child-sibling-selectors/css-child-sibling-selectors-00001.png)

### Descendants selector

위의 첫 번째 항목은 descendants selector 이입니다. 이것은 unordered list 하위에 있는 어떠한 list-item 들이든 모두 찾아내게 됩니다.. 즉, 중첩된 tag들 안에 있는 List-item 들도 모두 이 selector에 해당이 된다. 따라서 위 그림에서 처럼, ul 아래에 ol 이 있고, 그 안에 li tag가 있어, 서로 다른 성격의 li 인데도 불구하고 모두 이 selector에 해당되게 되는 것입니다.

### Children selector

위의 두 번째 selector는 children selector입니다. 이 selector는 지정된 하나의 레벨에 대해서만 대상을 찾아 CSS를 적용합니다. 따라서 ul tag 에 대해 한 level 아래에 있는 li tag 만을 찾아 CSS를 적용합니다. 

이 설명만으로도 이러한 selector에 대해서는 어느 정도의 이해가 갔을 것이라고 생각하지만, 정식 명칭으로서 다음 세 개의 selector style 에 대해서 더 알아보는 것으로 합니다. 

- child combinator
- adjacent sibling combinator
- general sibling combinator

##### Child combinator

Child Combinator 는 앞서 설명한 '>' 가 있는 selector에 대한 이야기입니다. 이 방법을 사용하기에 가장 좋은 것은 하나의 CSS 적용건이 그 하위에 있는 tag에까지 cascading down 되는 것을 막을 수 있습니다. 만약 바깥쪽에 있는 tag에서 전체적인 UI만을 잡고, 안에는 별도의 CSS를 적용하려고 한다면 아주 좋은 방안이 될 수 있습니다.

##### Adjacent sibling combinator

직역하면 '인접한 이웃'에 대한 selector입니다. 아래 예시를 보면 더욱 이해가 빠를 것입니다. 이 selector 는 '+' 로 연결된 두 tag가 연속으로 인접해야 selector의 대상이 됩니다. 즉, 이웃한 두 tag인데, 조합이 +로 연결된 대상과 일치한다면 selector의 대상이 됩니다. 아래 그림을 보면 이해가 더욱 쉽습니다.

```css
/* Selects all paragraphs that follow another paragraph */
p + p { font-size: smaller; } 
/* Selects an unordered list that directly follows the element with ID title */
#title + ul { margin-top: 0; } 
```
![](/assets/images/posts/old/img/post/2018-10-05-css-child-sibling-selectors/css-child-sibling-selectors-00002.png)

##### General sibling combinator

이 selector는 두 태그가 인접하지 않아도 이웃하고 있기만 하면 selector의 대상이 됩니다. 아래 그림에서 p ~ p 는 연속된 p, p tag나, 중간에 div가 있는 p, p tag 나 모두 그 대상이 됩니다. div ~ p 역시 연속이므로 이것도 selector의 대상이 됩니다.

![](/assets/images/posts/old/img/post/2018-10-05-css-child-sibling-selectors/css-child-sibling-selectors-00003.png)

출처: 

https://css-tricks.com/child-and-sibling-selectors/

https://4urdev.tistory.com/52 [Simplify]