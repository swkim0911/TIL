# Array (배열)

- 메모리 상에 데이터를 연속하게 배치하는 자료구조이다.
- 배열의 크기는 고정 길이이므로, 추가적으로 늘릴 수 없다.
- 데이터를 연속하게 배치하기 때문에 0부터 시작하는 index 가 존재하며, index 를 활용한 특정 데이터 접근의 시간 복잡도는 O(1) 이다.
- 데이터의 삽입이나 삭제가 중간에서 이뤄지는 경우, 그 뒤의 모든 요소를 한 칸 앞 당기거나, 밀어줘야하므로 시간 복잡도는 O(N) 이다.
- cache hit rate 가 높다.
    - 캐시는 일반적으로 블록 단위로 데이터를 가져오기 때문에, 연속된 메모리 공간에 접근할 때 다음에 사용될 데이터를 미리 가져와 놓을 수 있다.

# Array vs ArrayList in Java

차이는 크기에 있다. 기존의 Array(배열) 은 고정길이로, 한 번 길이를 할당하면 더 이상 배열의 사이즈를 늘릴 수 없다. 만약 데이터를 더 추가하고 싶으면, 완전히 새로운 배열을 할당해서 직접 옮겨야 한다. 하지만 ArrayList 는 내부적으로 배열의 형태(Object[]) 로 구현되어 있지만, 고정길이가 아닌 가변길이를 갖는 자료구조이다. 

ArrayList 클래스의 add() 함수의 내부동작을 통해 가변길이에 대해 더 자세히 알아보자.

```java
/**
 * Params:e element to be appended to this list
 *        s(size) The size of the ArrayList (the number of elements it contains)
 */
private void add(E e, Object[] elementData, int s) {
    if (s == elementData.length)
        elementData = grow();
    elementData[s] = e;
    size = s + 1;
}
```

- add() 함수를 보면 내부에 저장된 요소데이터의 개수(s)와 그 요소들을 담는 배열의 길이가 같을 때 grow() 함수가 호출된다. 즉, 내부 배열의 크기가 커진다.

```java
private Object[] grow() {
        return grow(size + 1);
}

private Object[] grow(int minCapacity) {
    int oldCapacity = elementData.length;
    if (oldCapacity > 0 || elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        int newCapacity = ArraysSupport.newLength(oldCapacity,
                minCapacity - oldCapacity, /* minimum growth */
                oldCapacity >> 1           /* preferred growth */);
        return elementData = Arrays.copyOf(elementData, newCapacity);
    } else {
        return elementData = new Object[Math.max(DEFAULT_CAPACITY, minCapacity)];
    }
}

public static int newLength(int oldLength, int minGrowth, int prefGrowth) {
    // preconditions not checked because of inlining
    // assert oldLength >= 0
    // assert minGrowth > 0

    int prefLength = oldLength + Math.max(minGrowth, prefGrowth); // might overflow
    if (0 < prefLength && prefLength <= SOFT_MAX_ARRAY_LENGTH) {
        return prefLength;
    } else {
        // put code cold in a separate method
        return hugeLength(oldLength, minGrowth);
    }
}
```

- 그리고 ArraysSupport.newLength() 함수에 의해 새로운 크기(newCapacity)는 oldLength(기존 길이)의 1.5 배의 길이로 결정된다.

추가적으로 Array 는 원시 타입과 Object 타입 모두를 저장할 수 있으나, ArrayList 는 Object 타입만 저장할 수 있다. 원시 타입을 저장하는 것은 불가능하므로 저장 시 autoboxing 에 의해 원시 타입 이 들어가는 것처럼 보인다.

- autoboxing: 원시 타입의 값을 해당하는 wrapper 클래스의 객체로 바꾸는 과정을 의미

# LinkedList

- 각 노드가 데이터와 포인터를 가지고 한 줄로 연결되어 있는 방식으로 데이터를 저장하는 자료구조
- O(1) 의 시간 복잡도로 삭제와 삽입 연산을 진행할 수 있다.
- 특정 값을 찾기 위해서는 해당 연결리스트 내부의 원소를 모두 다 뒤져야 하므로 검색에 대한 시간 복잡도는 O(N) 이 소요된다.
    - Java 에서는 LinkedList 자료구조가 이중 연결 리스트로 구현되어있어 원소를 찾는 작업은 지정된 인덱스에 가까운 처음 또는 끝에서 list를 검색한다.

## 참고

- [https://hoons-dev.tistory.com/99#%F-%-F%--%A-%----%--LinkedList%---�%--%B-�%B-%B-�%A-��%-A%A-�%-A%B--](https://hoons-dev.tistory.com/99#%25F-%25-F%25--%25A-%25----%25--LinkedList%25---%EC%25--%25B-%EA%25B-%25B-%EB%25A-%AC%EC%25-A%25A-%ED%25-A%25B--)
- https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayList.html