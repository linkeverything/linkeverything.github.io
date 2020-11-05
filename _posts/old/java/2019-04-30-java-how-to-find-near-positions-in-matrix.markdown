---
title:      "How to find near positions in matrix"
categories: [Old]
last_modified_at:       2019-04-30 01:00 
tags:
  - java
  - ArrayList
  - collection
published: false
---

## 들어가며

좌표값으로 이루어진 Matrix 에서, 하나의 좌표가 주어졌을 때, 그 주변 같은 값을 가진 좌표들을 구해내는 코드를 소개합니다. 눈으로 인지하기에는 어려운 로직이 아니지만, 실제 코딩을 해 보면 복잡도가 꽤 있고, 놓치는 부분이 많아 블로그에 정리합니다. 

배열을 for 문으로 돌면서 판단하는 로직, ArrayList 의 class요소에 대해서 contains 를 동작하게 하는 방법, 잘 사용하지 않는 do-while 문을 이용하는 방법 등을 소개합니다.

## 구현방법


##### 위치 정보를 가진 배경

다음과 같은 지도 형태의 matrix 에서 같은 값을 가진 원소들을 찾아보도록 하겠습니다. 

```java
int[][] matrix = new int[][] {
    {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
    {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
    {0, 1, 0, 0, 0, 0, 2, 2, 2, 0},
    {0, 1, 0, 1, 0, 0, 2, 0, 2, 0},
    {0, 1, 0, 1, 0, 0, 2, 0, 2, 0},
    {0, 1, 1, 1, 0, 0, 2, 0, 2, 0},
    {0, 0, 0, 0, 0, 0, 2, 0, 0, 0},
    {0, 0, 0, 0, 0, 0, 2, 0, 0, 0},
    {0, 0, 0, 0, 0, 0, 2, 0, 0, 0},
    {0, 0, 0, 0, 0, 0, 0, 0, 0, 0}
};
```

##### 위치값에 대한 class생성

당연하게도, class는 별도로 생성하지 않아도 됩니다만, 클래스를 생성해두면 조금 더 가독성이 좋아지고 관리가 용이합니다. 마지막에 전체 코드에서는 별도 파일로 분리하지 않고, inner class 형태로 제공하지만, 실제에서는 별도의 파일로 분리하는 것을 권장합니다.

```java
class Position{
    
    private int index;
    
    private int row;
    
    private int col;

    
    public Position(int row, int col) {
        super();
        this.row = row;
        this.col = col;
    }

    public Position(int index, int x, int y) {
        super();
        this.index = index;
        this.row = x;
        this.col = y;
    }

    public int getIndex() {
        return index;
    }

    public void setIndex(int index) {
        this.index = index;
    }

    public int getRow() {
        return row;
    }

    public void setRow(int row) {
        this.row = row;
    }

    public int getCol() {
        return col;
    }

    public void setCol(int col) {
        this.col = col;
    }

    @Override
    public String toString() {
        return "(" + row + ", " + col + ")";
    }
    
    @Override
    public boolean equals(Object obj) {
        return row == ((Position)obj).getRow() && col == ((Position)obj).getCol();
    }
}
```

여기서 두 가지의 override 함수에 대해서 설명합니다. 

##### public String toString()

이 함수는 쉽게 말해 System.out.println 함수에서 호출됩니다. 어떤 객체를 console 에 찍어줄 때에 이 함수가 불리게 되고 따라서 override 해서 괄호를 포함한 좌표 형태로 출력되게 해 두었습니다. 이 부분을 override 하지 않으면 기본적으로 대부분의 클래스가 hashCode를 반환하게 되어 있으므로 알아볼 수 없는 외계어가 보이게 됩니다. 

##### public boolean equals(Object obj)

ArrayList 의 함수 중 contains 함수는 for 문을 돌면서 해당 element 가 있는지 여부를 가지고 판단합니다. 그런데 있는지 없는지 여부에 대해서는 element 전체에 대해서 for문을 돌면서 특정 element 가 있는지를 판단할 때 equals 함수를 통해서 같은 것인지 여부를 판단하기 때문에 ArrayList 에 들어가는 원소가 Custom Class 인 경우에는 equals 를 override 해 주어야 정상적으로 판단이 가능합니다.


##### 초기 함수

```java
private static ArrayList<Position> findGroup(int[][] matrix, Position position) {
        
    ArrayList<Position> group = new ArrayList<Position>();
    
    if(matrix[position.getRow()][position.getCol()] == 0) {
        return group;
    }
    
    // add first position to group
    group.add(position);
    
    // make source positions
    ArrayList<Position> pos = new ArrayList<Position>();
    pos.add(position);
    
    
    ArrayList<Position> nears = null;
    
    do {
        nears = findNear(matrix, pos);
        
        pos.clear();

        for (Position p : nears) {
            if(!group.contains(p)) {
                group.add(p);
                pos.add(p);
            }
        }
        
    }while(nears.size() > 0);
    
    
    return group;
}
```

위에 소개한 matrix 에 위치 정보를 가진 Position 이라는 정보를 넘기게 되면 상하좌우 값을 찾아서 동일한 값을 가진 위치들을 모아 리스트 형태로 반환하는 함수입니다. 여기서 contains 함수가 사용되었고, 위에서 정의한 equals 함수가 내부적으로 사용되었을 것입니다. 

최초에 한 번 주변 위치를 반드시 찾고(do), 그 이후에는 주변에서 찾아진 값들이 있는 경우에만 반복적으로 주변 위치들을 찾습니다. 최초 시작은 1개의 포인트이지만, 주변에서 찾은 포인트가 여러개일 수 있으므로 최초에도 함수 통일성을 위해서 배열 형태로 생성해서 함수를 호출합니다.


##### 주변 위치 찾는 함수

```java
private static ArrayList<Position> findNear(int[][] matrix, ArrayList<Position> pos) {
        
    ArrayList<Position> nears = new ArrayList<Position>();
    
    for (Position position : pos) {
        
        int num = matrix[position.getRow()][position.getCol()];
        
        if(position.getRow() - 1 > -1
                && matrix[position.getRow() - 1][position.getCol()] == num) { // up
            nears.add(new Position(position.getRow() - 1, position.getCol()));
        }
        if(position.getRow() + 1 < matrix.length
                && matrix[position.getRow() + 1][position.getCol()] == num) { // down
            nears.add(new Position(position.getRow() + 1, position.getCol()));
        }
        if(position.getCol() - 1 > -1
                && matrix[position.getRow()][position.getCol() - 1] == num) { // left
            nears.add(new Position(position.getRow(), position.getCol() - 1));
        }
        if(position.getCol() + 1 < matrix[0].length
                && matrix[position.getRow()][position.getCol() + 1] == num) { // right
            nears.add(new Position(position.getRow(), position.getCol() + 1));
        }
        
    }
    
    return nears;
}
```

전체 지도가 되는 matrix 정보와 찾을 대상들의 중심이 되는 포인트의 배열을 넘기면 포지션을 하나씩 꺼내서 상하좌우에 해당하는 포지션을 탐색합니다. 이 때에 matrix에서 벗어나는 경우를 제외하기 위해서 길이값이나 0과의 비교을 꾸준히 하면서 주변 정보를 반환합니다.

##### 전체 소스

이들을 모두 종합하면 아래와 같은 형태로 구현할 수 있습니다. 

```java
package com.jonghun.test;

import java.util.ArrayList;

public class FindNearGroupSample {

    public static void main(String[] args) {
        
        int[][] matrix = new int[][] {
            {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
            {0, 0, 0, 0, 0, 0, 0, 0, 0, 0},
            {0, 1, 0, 0, 0, 0, 2, 2, 2, 0},
            {0, 1, 0, 1, 0, 0, 2, 0, 2, 0},
            {0, 1, 0, 1, 0, 0, 2, 0, 2, 0},
            {0, 1, 1, 1, 0, 0, 2, 0, 2, 0},
            {0, 0, 0, 0, 0, 0, 2, 0, 0, 0},
            {0, 0, 0, 0, 0, 0, 2, 0, 0, 0},
            {0, 0, 0, 0, 0, 0, 2, 0, 0, 0},
            {0, 0, 0, 0, 0, 0, 0, 0, 0, 0}
        };
        
        ArrayList<Position> group = findGroup(matrix, new Position(5, 6));
        
        System.out.println(group);
        
        ArrayList<Position> group2 = findGroup(matrix, new Position(5, 2));
        
        System.out.println(group2);
    }

    private static ArrayList<Position> findGroup(int[][] matrix, Position position) {
        
        ArrayList<Position> group = new ArrayList<Position>();
        
        if(matrix[position.getRow()][position.getCol()] == 0) {
            return group;
        }
        
        // add first position to group
        group.add(position);
        
        // make source positions
        ArrayList<Position> pos = new ArrayList<Position>();
        pos.add(position);
        
        
        ArrayList<Position> nears = null;
        
        do {
            nears = findNear(matrix, pos);
            
            pos.clear();

            for (Position p : nears) {
                if(!group.contains(p)) {
                    group.add(p);
                    pos.add(p);
                }
            }
            
        }while(nears.size() > 0);
        
        
        return group;
    }

    private static ArrayList<Position> findNear(int[][] matrix, ArrayList<Position> pos) {
        
        ArrayList<Position> nears = new ArrayList<Position>();
        
        for (Position position : pos) {
            
            int num = matrix[position.getRow()][position.getCol()];
            
            if(position.getRow() - 1 > -1
                    && matrix[position.getRow() - 1][position.getCol()] == num) { // up
                nears.add(new Position(position.getRow() - 1, position.getCol()));
            }
            if(position.getRow() + 1 < matrix.length
                    && matrix[position.getRow() + 1][position.getCol()] == num) { // down
                nears.add(new Position(position.getRow() + 1, position.getCol()));
            }
            if(position.getCol() - 1 > -1
                    && matrix[position.getRow()][position.getCol() - 1] == num) { // left
                nears.add(new Position(position.getRow(), position.getCol() - 1));
            }
            if(position.getCol() + 1 < matrix[0].length
                    && matrix[position.getRow()][position.getCol() + 1] == num) { // right
                nears.add(new Position(position.getRow(), position.getCol() + 1));
            }
            
        }
        
        return nears;
    }
    
}


class Position{
    
    private int index;
    
    private int row;
    
    private int col;

    
    public Position(int row, int col) {
        super();
        this.row = row;
        this.col = col;
    }

    public Position(int index, int x, int y) {
        super();
        this.index = index;
        this.row = x;
        this.col = y;
    }

    public int getIndex() {
        return index;
    }

    public void setIndex(int index) {
        this.index = index;
    }

    public int getRow() {
        return row;
    }

    public void setRow(int row) {
        this.row = row;
    }

    public int getCol() {
        return col;
    }

    public void setCol(int col) {
        this.col = col;
    }

    @Override
    public String toString() {
        return "(" + row + ", " + col + ")";
    }
    
    @Override
    public boolean equals(Object obj) {
        return row == ((Position)obj).getRow() && col == ((Position)obj).getCol();
    }
}
```