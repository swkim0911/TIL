# 외부 정렬 (External Sort)
외부 정렬(External Sort)은 대량의 데이터를 처리할 수 있는 정렬 알고리즘이다. 정렬되는 전체 데이터가 컴퓨팅 장치의 메인 메모리(RAM)에 맞지 않고 느린 외부 메모리(디스크 드라이브)에 있어야 하는 경우 외부 정렬이 필요하다.
- external memory algorithm: 데이터가 너무 커서 컴퓨터의 메인 메모리에 한 번에 들어갈 수 없는 데이터를 처리하도록 설계된 알고리즘

외부 정렬은 일반적으로 외부 병합 정렬(External Merge Sort)을 사용한다. 정렬 단계에서는 주 메모리에 들어갈 만큼 작은 데이터 덩어리를 읽고 정렬하고, 임시 파일에 기록한다. 병합 단계에서는 정렬된 하위 파일들이 하나의 큰 파일로 결합된다.


# 외부 병합 정렬 (External Merge Sort)
외부 정렬의 한 가지 예는 K-way 병합 알고리즘을 사용하는 외부 병합 정렬 알고리즘이다. RAM 크기에 맞는 청크를 정렬한 다음 정렬된 청크들을 모두 병합한다.

예를 들어, 100MB의 RAM만 사용하여 900MB의 데이터를 정렬한다면
1. 메인 메모리에서 100MB의 데이터를 읽고 퀵 정렬과 같은 일반적인 방법으로 정렬한다.
2. 정렬된 데이터를 디스크에 쓴다.
3. 모든 데이터가 정렬된 100MB 청크(900MB / 100MB = 9개 청크)에 포함될 때까지 1단계와 2단계를 반복한다. 이제 이를 하나의 단일 출력 파일로 병합해야 한다.
4. 정렬된 각 청크의 처음 10MB(*9 청크)를 주 메모리의 입력 버퍼로 읽고 나머지 10MB를 출력 버퍼에 할당한다.(총 100MB = 90MB + 10MB)
5. 9-way merge를 수행하고 결과를 출력 버퍼에 저장한다. 출력 버퍼가 채워질 때마다 이를 최종 파일에 쓰고 출력 버퍼를 비운다. 9개의 입력 버퍼 중 하나가 비워질 때마다, 청크에서 더 이상 데이터를 사용할 수 없을 때까지 사용된 100MB 크기의 정렬된 청크의 다음 10MB를 채운다.

마지막 병합하는 과정을 보면 k개의 정렬된 청크가 있다면, 병합 과정에서 한 번에 k개의 최소 요소만 메모리에 유지하고, 병합된 결과를 출력 파일에 기록한다. 이 과정은 필요한 각 청크를 순차적으로 한 번만 읽기 때문에 청크 전체를 한 번에 메모리에 로드할 필요가 없다.


# k-way merge algorithm

## 2-way merge
오름차순으로 정렬된 크기가 n인 A[1..n], B[1..n] 두 배열과 결과를 담을 배열 C[1..2n]가 있다고 가정하다. 인덱스 i,j,k 각각을 A,B,C 배열에 저장한다. 처음에 i,j,k 는 배열의 idx 1을 가리킨다. A[i] < B[j] 인 경우, A[i]를 C[k]에 복사하고 i와 k를 증가시킨다. 그렇지 않은 경우, B[j]를 C[k]에 복사하고 j와 k를 증가시킨다. i 또는 j가 A 또는 B의 끝에 도달한 경우에는 B 또는 A의 나머지 요소를 C에 복사하고 종료된다.

## k-way merge (Iterative 2-way merge)
최종 하나의 배열만 남을 때까지 2-way merge 를 사용하여 k 배열 중 두 개를 반복적으로 병합하면 문제가 해결될 수 있다. 전체 데이터가 n 개 일 때 시간 복잡도는 O(nlogk) 가 된다.

최소 힙을 사용하는 방법도 있지만 시간 복잡도는 동일하며 k 개의 chunk 사이즈가 일정하지 않은 경우 유용하다.


# 참고
- [External Sorting - geeksforgeeks](https://www.geeksforgeeks.org/external-sorting/)
- [External Sorting - wikipedia](https://en.wikipedia.org/wiki/External_sorting)
- [k-way merge algorithm - wikipedia](https://en.wikipedia.org/wiki/K-way_merge_algorithm)
- [Merge k sorted arrays - geeksforgeeks](https://www.geeksforgeeks.org/merge-k-sorted-arrays/)

