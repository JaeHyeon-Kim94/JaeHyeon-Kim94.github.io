---
categories: [algoritm]
tags: []
---

## Ch01. 기본 알고리즘

- 구조적 프로그래밍(Structured Programming)
  - 하나의 입구와 하나의 출구를 가진 구성 요소만을 계층적으로 배치하여 프로그램을 구성하는 방식
  - 제어 흐름 : 순차, 선택, 반복


- 드모르간 법칙
  - 각 조건을 부정하고 논리곱(AND)을 논리합(OR)으로, 논리합을 논리곱으로 바꾸고 다시 전체를 부정하면 원래의 조건과 같다는 법칙

<br>
<hr>
<br>

## Ch02. 기본 자료구조

- 다차원 배열 clone : 최상위의 1레벨만 수행하며, 그 아래 레벨의 배열은 참조를 공유한다.

<br>
<hr>
<br>

## Ch03. 검색

### 03-1 검색 알고리즘

  ***TODO***
  > 1. 선형 검색 : 무작위로 늘어놓은 데이터 집합에서 검색 수행.
  > 2. 이진 검색 : 일정한 규칙으로 늘어놓은 데이터 집합에서 아주 빠른 검색 수행.
  > 3. 해시법 : 추가, 삭제가 자주 일어나는 데이터 집합에서 아주 빠른 검색 수행.
    <br>&nbsp;&nbsp; <span style="color:#2D3748; background-color:#fff5b1">- 체인법 : 같은 해시 값의 데이터를 선형 리스트로 연결하는 방법</span>
    <br>&nbsp;&nbsp; <span style="color:#2D3748; background-color:#fff5b1">- 오픈 주소법 : 데이터를 위한 해시 값이 충돌할 때 재해시 하는 방법</span>

  데이터의 집합에 대한 검색, 추가, 삭제 등의 작업에 소요되는 비용을 종합적으로 판단하여 알고리즘 선택해야 한다.

<br><br>

### 03-2 선형 검색(linear search)

- 직선으로 늘어선 요소들에 대해 검색을 할 때, 원하는 요소를 찾을 때까지 맨 앞부터 순차적으로 검색.
  -  선형 검색(linear search), 순차 검색 알고리즘(sequential search)
- 검색 종료 조건
  1. 검색 값을 발견하지 못하고 배열의 끝을 지나간 경우
  2. 검색할 값과 같은 요소를 발견한 경우

  ```java
      //선형 검색 while
      static int seqSearchW(int[] arr, int n, int key){
          while(true){
              if(i == n)      return -1;
              if(a[i]==key)   return i;

              i++;
          }
      }

      static int seqSearchF(int[] arr, int n, int key){
          for(int i=0; i<n; i++){
              if(a[i] == key) return i;

              return -1;
          }
      }
  ```

  <br>

  - __보초법__

    - 선형 검색은 검색시 상기의 종료 조건 1, 2를 모두 판단하는데, 이 과정에서 소요되는 비용을 반으로 줄이는 방법.

    - 검색하고자 하는 요소값을 배열의 맨 마지막 요소로 넣는다. 이렇게 하면 종료조건 1을 무시하고 2만 판단하면 됨.

      ```java
      static int seqSearchSen(int[] arr, int n, int key){
          int i=0;

          a[n] = key;
          while(true){
              if(a[i] == key) break;
              i++;
          }

          return i == n ? -1 : i;
      }
      ```
    ***TODO***
- 판단 횟수 : 배열의 요솟수가 n개라면 <span style="color:#2D3748; background-color:#fff5b1">평균 n/2회</span>. 원하는 값이 배열에 존재하지 않는 경우 조건 1은 n+1, 2는 n회

### 03-3 이진 검색(binary search)

- 데이터가 키 값으로 이미 정렬되어 있어야 한다. 오름차순 혹은 내림차순으로 정렬된 배열에서 검색하는 알고리즘.
  
- linear search보다 검색이 좀 더 빠르다.
  
- 검색방식
  
  - 정렬된 배열의 정중앙 요소부터 검색을 시작한다. 해당 요소와 검색할 요소의 크기를 비교함으로써 범위가 반으로 줄어든다.
  
    - 오름차순으로 정렬된 배열을 기준으로, 검색 범위의 맨 앞 인덱스를 L, 맨 끝 인덱스를 R, 중앙을 C라고 할 때,
    - n개의 요소를 가진 배열의 검색 시작시 L 은 0, R은 n-1, C는 (n-1)/2로 초기화 되며
    - 배열[C] > key일때 검색 대상은 왼쪽 영역에 있는 것이 분명하므로, 배열[L] ~ 배열[C-1] 로 검색범위를 좁히며 R은 C-1로 업데이트 됨.
    - 반대로 배열[C] < key일때  검색범위는 배열[C+1] ~ 배열[R], L은 C+1
  
  - 해당 과정을 반복한다.
  - 검색 대상이 2개로 좁혀졌을 때에는 둘 중 앞의 값을 선택한다.


- 검색 종료 조건
  - 조건 1 : 배열[C]와 key가 일치하는 경우
  - 조건 2 : 검색 범위가 더이상 없는 경우
    - ex) L이 R보다 커지는 경우 혹은 R이 L보다 작아지는 경우

- 비교횟수
  - 평균값 : log n
  - 검색 실패시 log(n+1)회, 성공시 log n-1 회.

  ```java
  static int binarySearch(int[] arr, int n, int key){
    int l = 0;
    int r = n-1;

    do{
      int c = (l + r)/2;
      if(arr[c] == key) return c
      else if(arr[c] < key) l = c + 1;
      else r = c - 1;
    }while(l <= r);
    
    return -1;
  }
  ```

 - **복잡도**
   - 알고리즘의 성능을 객관적으로 평가하는 기준으로서 
  <br>__시간 복잡도__(실행에 필요한 시간)와
  <br>__공간 복잡도__(기억 영역과 파일 공간이 얼마나 필요한가) 두 가지 요소 지님.

   - 시간 복잡도
     - 단순히 값을 반환, 대입하는 일회성 실행의 경우에는 `O(1)`로 표기
      
     - 실행 횟수가 비례하여 증가하는 경우에는 복잡도를 `O(n)`으로 표기.
  
     - O(f(n))과 O(g(n))의 복잡도 계산 방법
       - `O(f(n)) + O(g(n)) = O(max(f(n), g(n)))`
         > max(a, b)는 a와 b가운데 큰 쪽을 나타내는 메서드.

       - for문을 이용한 선형 검색 코드를 기준으로 시간 복잡도를 나타내보면,
         - int i를 선언하고 할당하는 부분은 한 번만 수행되므로 __O(1)__
         - 대소 비교 부분의 평균 실행 횟수는 __n/2__
         - 증가 연산자도 __n/2__
         - a[i] == key 부분도 __n/2__
         - i 리턴은 한 번만 수행되므로 __O(1)__
         - 검색 실패시 -1 리턴은 한 번만 수행되므로 __O(1)__
         
         - 컴퓨터에게 n/2나 n이나 차이 없으므로 
       
         - O(1) + O(n) + O(n) + O(1) + O(n) + O(1) 
         <br> = O(max(1, n, n, 1, n, 1))
         <br> = O(n)
  <br>
  - __Arrays.binartSearch에 의한 이진 검색__
  
    - java.util.Array 클래스의 binarySearch 메서드는 오름차순으로 정렬된 배열 a를 가정하고, 키 값이 key인 요소를 이진검색 함. 자료형에 따라 오버로딩 되어있음.
  
      - 타입 : byte[], char[], double[], float[], int[], long[], short[], Object[], 제네릭
      - 검색 성공시 key와 일치하는 요소의 인덱스를 리턴하는데, 여러개일 경우 무작위 인덱스 반환.
      - 검색 실패시 삽입 포인트(key보다 큰 요소 중 첫 번째 요소의 인덱스)를 x라고 했을 때, -x-1 반환.
  
    - 객체의 배열에서 검색
      - `static int binarySearch(Object[] a, Object key)`
        - 자연 정렬이라는 방법으로 요소의 대소 관계를 판단. 정수 배열, 문자열 배열에서 검색시 적절.
          - 자연 정렬과 문자열 정렬<br> 문자열 정렬은 동일한 위치에 있는 문자의 대소 비교를 통해 정렬한다.
  
          문자열 정렬 | 자연 정렬
          --- | ---
          텍스트1.txt | 텍스트1.txt
          텍스트10.txt | 텍스트2.txt
          텍스트100txt | 텍스트10.txt
          텍스트2.txt | 텍스트21.txt
          텍스트21.txt | 텍스트100.txt

      - `static <T> int binarySearch(T[] a, T key, Comparator<? super T> c)`
        - 자연 순서가 아닌 순서로 줄지어 있는 배열에서 검색하거나 자연 순서를 논리적으로 갖지 않는 클래스 배열에서 검색할 때 적절.
        - Comparator : 클래스 T(또는 클래스 T의 슈퍼 클래스)로 생성한 두 객체의 대소 관계를 판단하기 위한 인터페이스. compare 메서드를 구현해 사용한다. 
        ```java
          public foo implements Comparator<T>{
            int compare(T o1, T o2){
              if(o1 > o2) return 양수;
              if(o1 < o2) return 음수;
              if(o1 == o2) return 0;
            }

            boolean equals(Object obj){
              //...
            }
          }
        ```
    
<br>
<hr>
<br>


## Ch04. 스택과 큐


### 1. 스택

 - LIFO(Last In First Out)
 - 스택에 데이터를 넣는 push와 데이터를 꺼내는 pop
 - push와 pop을 하는 위치를 꼭대기(top)이라고 하고, 스택의 가장 아랫 부분은 바닥(bottom)이라고 함.

 - 자바에서는 메서드의 호출과 실행시 스택을 사용함. 다음은 메서드 호출 및 실행과정.
<br>

  ```java
    void x(){}
    void y(){}
    void z(){
      x();
      y();
    }

    void main(){
      z();
    }
  ```
  
  > 1. push : main
  > 2. push : z
  > 3. push : x
  > 4. pop : x
  > 5. push : y
  > 6. pop : y
  > 7. pop : z
  > 8. pop : main

  - 스택 만들기

  ```java
    class MyStack<E>{
        private int max;    //스택 용량
        private int ptr;    //스택 포인터
        private E[] stk;  //스택 본체

        public MyStack(int capacity) {
            ptr = 0;
            max = capacity;
            try{
                stk = (E[])new Object[max];
            }catch (OutOfMemoryError e){
                max = 0;
            }
        }
        //push 메서드
        public E push(int x){
            if(ptr >= max) throw new OverflowMyStackException();
            return stk[ptr++] = x;
        }
        //pop 메서드
        public int pop(){
            if(ptr <= 0) throw new EmptyMyStackException();
            return stk[--ptr];
        }
        //peek 메서드
        public E peek(){
            if(ptr <=0 ) throw new EmptyMyStackException();
            return stk[ptr - 1];
        }
        //indexOf 메서드 (top to bottom 선형 검색 수행)
        public int indexOf(E x){
            for(int i=0; i<ptr; i++){
                if(stk[i].equals(x)) return i;
            }
            return -1;
        }
        //clear 메서드
        //스택의 모든 작업들은 스택 포인터(ptr)을 이용하여 이루어지기 때문에,
        //스택의 요솟값을 변경할 필요가 없다.
        public void clear(){ ptr = 0; }
        //capacity 메서드
        public int capacity(){ return max; }
        //size 메서드
        public int size(){ return ptr-1; }
        //IsEmpty 메서드
        public boolean IsEmpty(){ return ptr <= 0; }
        //IsFull 메서드
        public boolean IsFull(){ return ptr >= max; }
        //dump 메서드 (스택의 모든 데이터 표시)
        public void dump(){
            if(ptr <= 0) System.out.println("스택이 비어있음.");
            else{
                for(int i=0; i<ptr; i++){
                    System.out.println(stk[i] + " ");
                }
                System.out.println();
            }
        }


    }

    class EmptyMyStackException extends RuntimeException{
        public EmptyMyStackException() {
        }
    }

    class OverflowMyStackException extends RuntimeException{
        public OverflowMyStackException() {
        }
    }
  ```


### 2. 큐

- FIFO(First In First Out)
- 큐에 데이터를 넣는 인큐(enqueue), 데이터를 꺼내는 디큐(dequeue), 데이터를 꺼내는 쪽인 프런트(front), 데이터를 넣는 쪽인 리어(rear)
- 인큐의 경우 복잡도는 O(1), 디큐의 경우 복잡도는 O(n). 데이터를 꺼내고 다음 두 번째 요소부터 이후의 요소를 모두 앞으로 옮겨야 하기 때문.
- 링 버퍼(배열 요소를 앞으로 옮기지 않도록 하는 큐) 이용시에는 디큐시에도 복잡도 O(1)

- 배열을 이용해 큐 구현

```java
//해당 예제는 dequeue시 앞으로 옮기는 작업 없도록 구현.(링버퍼)
  public class MyQueue<E>{
    private int max;    //큐의 용량
    private int front;  //맨 앞의 커서
    private int rear;   //맨 뒤의 커서
    private int num;    //현재의 데이터 수
    private E[] que;    //큐의 본체

    class EmptyMyQueueException extends RuntimeException{
      public EmptyMyQueueException(){}
    }

    class OverflowMyQueueException extends RuntimeException{
      public OverflowMyQueueException(){}
    }

    public MyQueue(int capacity){
      num = front = rear = 0;
      max = capacity;

      try{
        que = (E[])new Object[max];
      }catch(OutofMemoryError e){
        max = 0;
      }
    }

    public E enqueue(E e){
      if(num >= max)  throw new OverflowMyQueueException();
      que[rear++] = e;
      num++;
      if(rear == max) rear = 0;
      return e;
    }

    public E enqueue(){
      if(num<=0) throw new EmptyMyQueueException();
      E e = que[front++];
      num--;
      if(front == max) front = 0;
      return e;
    }

    //큐에서 데이터를 피크
    public E peek(){
      if(num<=0) throw new EmptyMyQueueException();
      return que[front];
    }

    //indexOf
    public int indexOf(E e){
      for(int i=0; i<num; i++){
        int idx = (i + front) % max;
        if(que[idx].equals(e)) return idx;  //검색 성공
      }
      return -1;  //검색 실패
    }

    //clear
    public void clear(){
      num = front = rear = 0;
    }

    //capacity
    public int capacity(){
      return max;
    }

    //size
    public int size(){
      return num;
    }

    //isEmpty
    public boolean isEmpty(){
      return num<=0;
    }

    //isFull
    public boolean isFull(){
      return num >= max;
    }

    //dump
    public void dump(){
      if(num<=0)  System.out.println("큐가 비었습니다.");
      else{
        for(int i=0; i<num; i++)
          System.out.print(que[(i+front)%max] + " ");
        System.out.println();  
      }
    }
    
    //search
    public int search(E e){
      for(int i=0; i<num; i++){
        if(que[(i + front) % max].equals(e)) return i+1;
      }
      return 0;
    }
  }
```












<br>
<hr>
<br>

## Reference
- Do it! 자료구조와 함께 배우는 알고리즘 입문 자바 편