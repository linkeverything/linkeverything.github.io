---
title:      "Selected Row for jqGrid"
categories: [old]
last_modified_at:       2018-10-05
tags:
  - web
  - javascript
  - jqgrid 
---

## 들어가며

jqGrid 라이브러리를 사용하다 보면, 당연하게도 선택된 Row 에 대한 정보를 가져와야 하는 경우가 있습니다. 여러가지 방법으로 가져올 수 있고, 버튼을 달고, 체크박스를 넣어서 이벤트를 걸어도 되고 다 가능한 방법이긴 하지만, 기본적으로 현재 선택된 Row에 대한 정보를 가져와야 하는 경우 다음과 같은 방법으로 가져올 수 있습니다. 

## 정리된 내용

##### Single selection 이 적용된 경우

```js
$( "{gridtableid}" ).getGridParam( "selrow" );
$( "{gridtableid}" ).jqGrid('getGridParam', "selrow" );  
```

이렇게 하면 결과로 해당 Row id 값이 넘어오게 됩니다. 0부터 시작이 아닌 1부터 시작이었던 것 같은데 확인해봐야 할 것 같습니다. 

##### Multi selection 이 적용된 경우

```js
$( "{gridtableid}" ).jqGrid('getGridParam', 'selarrrow');
$( "{gridtableid}" ).getGridParam('selarrrow');
```

이렇게 하면 선택된 Row id 들이 배열로 return 되어 돌아옵니다. 

##### Row Data 가져오기

위에서 얻어온 row id 들을 가지고 해당 Row의 데이터를 json object 형태로 받고 싶다면 아래와 같이 구현하면 됩니다.

```js
$( "{gridtableid}" ).jqGrid('getRowData', rowid);
$( "{gridtableid}" ).getRowData( rowid );
```


##### 기타 자주 사용하게 되는 function 들 정리

```js
// clear
$grid.jqGrid('clearGridData');
 
// reload
$grid.trigger('reloadGrid');
 
// get id
var ids    = $grid.jqGrid('getDataIDs');
var rowIds = $grid.getDataIDs();
 
// get data []
var rowDatas = $grid.jqGird('getRowData');
var rowDatas = $grid.getRowData();
 
// get data {}
var obj = $grid.jqGird('getRowData', rowid);
var obj = $gird.getRowData(rowid);
 
// add
$grid.jqGrid('addRowData', row.id, data);
$grid.addRowData(rowid, data, position, srcrowid);
$grid.addRowData(rowid, data);
 
// update
$grid.jqGrid('setRowData', rowid, data, {cssProp});
$grid.setRowData(rowId, data, {cssProp});
 
// delete
$grid.jqGird('delRowData', rowid);
$grid.delRowData(rowid);
 
// getGridParam
var selRowId = $Grid.getGridParam('selrow');
var selRowIds = $Grid.getGridParam('selarrrow');
var selRowId = $Grid.jqGrid('getGridParam','selrow');
var selRowIds = $Grid.jqGrid('getGridParam','selarrrow');
var postData = $Grid.jqGrid('getGridParam','postData');
 
// setGridParam
$Grid.setGridParam({'postData' : postData}).trigger('reloadGrid');
$Grid.jqGrid('setGridParam', {'postData' : postData}).trigger('reloadGrid');
 
// setSelection(rowid, onSelectRowEvent)
$Grid.setSelection('001', false);
$Grid.jqGrid('setSelection', '001', false);
 
// resetSelection()
$Grid.resetSelection();
$Grid.jqGrid('resetSelection');
```

## 출처

http://www.trirand.com/jqgridwiki/doku.php?id=wiki:methods

http://javafactory.tistory.com/1161

http://pyungyi.blogspot.com/2011/09/jqgrid-get-selected-row.html


출처: https://4urdev.tistory.com/50 [Simplify]