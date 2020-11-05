---
title:      "How to make Collection with ArrayList"
categories: [Old]
last_modified_at:       2019-04-30 00:00 
tags:
  - java
  - ArrayList
  - collection
published: false
---

## 들어가며

수학적인 개념의 조합(Collection)은 순서에 상관없이 요소들의 집합 경우의 수를 다루는 것을 의미합니다. 학창 시절 수학시간에 배운 개념인데, 이걸 코딩으로 해결해 보려고 하니 까다로움이 있어 포스팅으로 남깁니다. 

보통의 경우, 아래 경우에 대해서 System.out 등으로 로그를 찍으면 조금만 커져도 그 양이 어마어마하게 많아집니다. 적은 수로 우선 동작을 확인하고 확대된 것을 이요하시기를 권장 드립니다.

특히 ArrayList같은 경우에는 조합을 이용해서 index 의 조합을 가져오고, 이를 통해서 Arraylist 의 요소들을 꺼내 각각을 구현하는 방식을 이용했습니다. 바로 처리할 수도 있겠지만, 보통은 각각의 경우에 대해서 일련의 작업을 하고 마무리하는 것이 아니라, 아래처럼 가져온 모든 경우에 대한 특정 처리.. 최소값을 구한다거나 하는 등의 작업을 대비한 것 까지를 구현합니다. 

## 구현방법


##### 기본 방법

전체 개수와 고를 원소 수를 통해서 구합니다. 조합은 수학적으로 대문자 C 문자 옆에 좌우로 전체 수, 고를 원소 수를 나열하는 것과 동일하게 생각하면 됩니다. 결과는 배열 형태로 내보내게 되어 있으며 이는 신규로 생성해서 넣어주면 됩니다.

 - n : 전체 원소의 수
 - picked : 지금까지 고른 원소들의 번호
 - toPick : 더 고를 원소의 수

예를 들어, 1부터 5까지의 숫자 중에서 2개를 추출하여 조합하면 아래와 같은 형태로 호출할 수 있습니다. 

```java
package com.jonghun.test;

import java.util.ArrayList;

public class ArrayListCollectionSample {

    public static void main(String[] args) {
        pick(5, new ArrayList<Integer>(), 2);
    }

    private static void pick(int n, ArrayList<Integer> picked, int toPick) {
        
        if (toPick == 0) {
            System.out.println(picked);
            return;
        }

        int smallest = picked.isEmpty() ? 0 : picked.get(picked.size() - 1) + 1;

        for (int next = smallest; next < n; next++) {
            picked.add(next);
            pick(n, new ArrayList<Integer>(picked), toPick - 1);
            picked.remove(picked.size() - 1);
        }
    }

}
```

결과 (실제로는 index처럼 0부터 4까지의 숫자가 표현됩니다.)

```
[0, 1]
[0, 2]
[0, 3]
[0, 4]
[1, 2]
[1, 3]
[1, 4]
[2, 3]
[2, 4]
[3, 4]
```

##### 결과 받아오기

Printout 만 하려는 목적이라면 상관없으나, 거의 이러한 경우가 없고 저 경우의 수들을 모두 사용하고자 할 것이므로 배열 등을 넘겨서 저 결과를 받아와 보려고 합니다. 

```java
package com.jonghun.test;

import java.util.ArrayList;

public class ArrayListCollectionSample {

    public static void main(String[] args) {
        
        ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();
        
        pick(5, new ArrayList<Integer>(), 2, result);

        for (ArrayList<Integer> arrayList : result) {
            System.out.println(arrayList);
        }
    }

    private static void pick(int n, ArrayList<Integer> picked, int toPick, ArrayList<ArrayList<Integer>> collection) {
        
        if (toPick == 0) {
            //System.out.println(picked);
            collection.add(picked);
            return;
        }

        int smallest = picked.isEmpty() ? 0 : picked.get(picked.size() - 1) + 1;

        for (int next = smallest; next < n; next++) {
            picked.add(next);
            pick(n, new ArrayList<Integer>(picked), toPick - 1, collection);
            picked.remove(picked.size() - 1);
        }
    }

}
```

결과

```
[0, 1]
[0, 2]
[0, 3]
[0, 4]
[1, 2]
[1, 3]
[1, 4]
[2, 3]
[2, 4]
[3, 4]
```

##### 전체 조합 가져오기

전체 조합을 출력하여 모든 경우의 수를 가져오려면 아래와 같이 구현합니다.

```java
package com.jonghun.test;

import java.util.ArrayList;

public class ArrayListCollectionSample {

    public static void main(String[] args) {
        
        ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();
        
        for (int inx = 2; inx <= 5; inx++) {
            pick(5, new ArrayList<Integer>(), inx, result);
        }

        for (ArrayList<Integer> arrayList : result) {
            System.out.println(arrayList);
        }
    }

    private static void pick(int n, ArrayList<Integer> picked, int toPick, ArrayList<ArrayList<Integer>> collection) {
        
        if (toPick == 0) {
            //System.out.println(picked);
            collection.add(picked);
            return;
        }

        int smallest = picked.isEmpty() ? 0 : picked.get(picked.size() - 1) + 1;

        for (int next = smallest; next < n; next++) {
            picked.add(next);
            pick(n, new ArrayList<Integer>(picked), toPick - 1, collection);
            picked.remove(picked.size() - 1);
        }
    }

}
```

결과

```
[0, 1]
[0, 2]
[0, 3]
[0, 4]
[1, 2]
[1, 3]
[1, 4]
[2, 3]
[2, 4]
[3, 4]
[0, 1, 2]
[0, 1, 3]
[0, 1, 4]
[0, 2, 3]
[0, 2, 4]
[0, 3, 4]
[1, 2, 3]
[1, 2, 4]
[1, 3, 4]
[2, 3, 4]
[0, 1, 2, 3]
[0, 1, 2, 4]
[0, 1, 3, 4]
[0, 2, 3, 4]
[1, 2, 3, 4]
[0, 1, 2, 3, 4]
```


참고 : https://timpani.tistory.com/entry/java-%EB%AA%A8%EB%93%A0-%EC%A1%B0%ED%95%A9-%EB%A7%8C%EB%93%A4%EA%B8%B0