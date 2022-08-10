---
categories : [Java]
tags : [comparator, comparable]
---

## Comparator와 Comparable

Comparator와 Comparable은 둘 다 인터페이스로서, 이를 사용하고자 한다면 내부에 선언되어있는 추상메서드를 반드시 구현해야 한다. 

Arrays.sort 메서드를 보면 원시타입의 배열 외에도 참조 타입의 배열이 들어갈 수 있도록 매개변수를 Object[]를 받는 경우가 있는데, 위 두 인터페이스의 필요성은 참조 타입의 정렬이 어떤 기준으로 이루어지는지에 대한 의문부터 시작한다.

<br>

```java
    class Student{
        int studentId;
        String name;
        int age;
    }
```

<br>

원시타입의 경우에는 부등호를 통해 단순한 대소비교가 가능하지만 학생이라는 객체의 배열을 정렬하기 위해선 기준점이 필요한데, 이를 Comparable 혹은 Comparator를 통해 지정해주는 것. 

 - 용법

```java
//Comparable
 class Student implements Comparable{
    int studentId;
    String name;
    int age;

    @Override
    public int compareTo(Student o) {
        return Integer.compare(this.studentId, o.studentId);
    }
 }

 //Comparator
 class foo{
    
    private static Comparator<Student> idComparator = ((o1, o2)->{
        return Integer.compare(o1.studentId, o2.studentId);
    });

    public void studentSort(Student[] arr){
        Arrays.sort(arr, idComparator);
    }
 }

```

<br>

CompareTo메서드는 자기 자신과 인자로 들어오는 다른 객체를 비교하는 반면,

Compare 메서드는 인자로 들어오는 두 객체를 비교하게 됨. 