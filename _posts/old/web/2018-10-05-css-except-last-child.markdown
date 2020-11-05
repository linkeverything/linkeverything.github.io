---
title:      "CSS select except last child"
categories: [Old]
last_modified_at:       2018-10-05
tags:
  - web
  - css
published: false
---

## 마지막 Child 제외하기

마지막 Child 를 제외하고서, 다른 것들에 대해서만 특정 CSS를 적용해야 하는 경우 아래와 같은 CSS Selector를 사용하면 됩니다. :not() 은 ()안의 조건이 아닌 :앞의 tag를 선택하라는 것이고, :last-child 는 마지막 child 라는 것이므로, 마지막 child 를 제외한 나머지를 선택하는 selector가 됩니다.

```css
ul li:not(:last-child)
```

출처: https://4urdev.tistory.com/51 [Simplify]