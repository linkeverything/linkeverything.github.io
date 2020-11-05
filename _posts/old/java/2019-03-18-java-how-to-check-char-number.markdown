---
title:      "How to check character and number in java"
categories: [old]
last_modified_at:       2019-03-18 00:00 tags:
  - java
published: false
---

## 들어가며

자바에서 문자열과 숫자를 구분하는 방법을 검색 해 보면, 거의 대부분 Double.parseDouble 로 해서 Exception을 발생시키고 catch 문에서 false, 정상 처리된 경우 true를 내보내는 형태로 구현한다. 

하지만 성능 측면을 고려했을 때, try-catch 는 성능상 문제를 일으킬 수 있고, 따라서 가급적 사용하지 않고도 판단할 수 있는 방법이 있을 것 같아 다음의 방법을 찾아냈다.

## 구현방법

자바에서는 유니코드를 사용한다고 한다. 하지만 255까지는 아스키코드 값과 동일하다는 점을 이용한다.

숫자 0 은 유니코드 값으로 49, 숫자 9는 유니코드값으로 57에 해당하며, 아스키코드 값과 동일하다.

```java

	public boolean CheckNumber(String str){
		char check;
		
		if(str.equals(""))
		{
			//문자열이 공백인지 확인
			return false;
		}
		
		for(int i = 0; i<str.length(); i++){
			check = str.charAt(i);
			if( check < 48 || check > 58)
			{
				//해당 char값이 숫자가 아닐 경우
				return false;
			}
			
		}		
		return true;
	}
```

출처 : http://egloos.zum.com/hiiq/v/1820209