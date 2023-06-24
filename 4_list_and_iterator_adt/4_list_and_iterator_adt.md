# ✍️ **4. List and Iterator Adt**  

</br>

## ✔️ **1. 벡터(Vector)**  
- 원소의 인덱스에 의하여 원소에 접근할 수 있도록 지원해주는 시퀀스를 벡터(vector)라고 한다.  
- 벡터는 객체의 집단을 저장하는 자료구조인 **컨테이너(Container)** 라 불리는 클래스 집합 중의 하나이다.  

</br>

### **1) STL 벡터**
- 개별 원소의 타입을 벡터의 기본 타입(base type)이라고 하는데, 각 원소는 기본 타입의 디폴트 값으로 초기화 된다.  
- 배열과 같이 벡터 객체의 개별 원소들은 일반적으로 인덱스 연산자("[]")로 인덱스될 수 있다.  
- 각 원소들은 또한 멤버함수 `at`으로도 접근될 수 있는데, 이 함수를 통해 인덱스 범위를 벗어난 경우 예외를 발생시킬 수 있다.  
- C++ 배열과 달리, STL vector는 동적으로 크기가 변하고, 배열의 끝에 원소를 효과적으로 추가하거나 삭제할 수 있다.  
- 클래스 객체의 STL vector가 삭제될 때 각 원소의 소멸자가 자동으로 호출된다.  
(C++ 배열에서는 프로그래머가 직접 이러한 작업을 해야 한다.)  
- STL vector는 각 개별 원소 뿐 아니라 전체 벡터에 동작하는 유용한 함수들을 제공한다. 예를 들어 벡터의 전체 또는 일부 원소들을 다른 벡터에 복사하거나, 두 벡터의 내용을 비교하거나, 또는 여러 개의 원소를 삽입 또는 삭제할 수 있다.  
- STL vector의 기본 타입이 클래스일 때, 모든 원소의 복사(예를 들어, `push_back`에서)는 해당 클래스의 복사 생성자를 호출하여 수행된다.  
- 또한, 요소들이 소멸될 때(예를 들어, 소멸자나 멤버 함수 `pop_back`을 호출하여), 각 삭제된 원소에 대해 클래스의 소멸자가 호출된다.  

</br>  

- `vector(n)` : $n$ 원소의 공간을 갖는 벡터를 생성한다. (만약 인수가 없으면 빈 벡터를 생성한다.)
- `size()` : 벡터 내 원소의 개수를 반환한다.
- `empty()` : 벡터가 비어있으면 참을, 그렇지 않으면 거짓을 반환한다.
- `resize(n)` : $V$의 크기를 변경하여 $n$ 원소의 공간을 갖도록 한다.
- `reserve(n)` : $n$ 원소를 저장할 수 있는 저장 공간을 확보하도록 요청한다. 
- `operator[i]` : $V$의 $n$번째 원소에 대한 레퍼런스를 반환한다.  
- `at(i)` : $V[i]$와 같으나, 만약 $i$가 범위를 벗어났으면, (즉, $i < 0$ 또는 $i > V.size()$이면) 예외를 발생시킨다.  
- `front()` : $V$의 첫 번째 원소에 대한 레퍼런스를 반환한다.  
- `back()` : $V$의 마지막 원소에 대한 레퍼런스를 반환한다.  
- `push_back(e)` : 원소 $e$의 복사본을 $V$의 끝에 추가하고, 크기를 1 증가시킨다.  
- `pop_back()` : 벡터의 마지막 요소를 제거하고 크기를 1 감소시킨다.  

</br>

- 테스트
    ```cpp
    #include <iostream>
    #include <vector>

    using namespace std;

    template<typename T>
    void printAll(const vector<T>& v) {
        for (int i = 0; i < v.size(); i++) {
            cout <<  v.at(i) << " ";
        }
        cout << endl;
    }


    int main(void) {
        vector<int> v;
        v.reserve(5);

        v.push_back(1);
        v.push_back(2);
        v.push_back(3);
        v.push_back(4);
        v.push_back(5);
        v[3] = 10;

        v.pop_back();

        printAll(v);
        return EXIT_SUCCESS;
    }
    ```  
    ```
    1 2 3 10 
    ```  

</br>

### **2) 확장 가능한 배열을 이용한 벡터 구현**
- 코드 구현
    ```cpp
    #include <iostream>
    #include "runtimeException.h"

    using namespace std;


    class IndexOutOfBounds: public RuntimeException {
    public:
        IndexOutOfBounds(const string& err) : RuntimeException(err) {}
    };


    template<typename T>
    class ArrayVector {
    public:
        ArrayVector();
        int size() const;
        bool empty() const;
        T& operator[](int i);
        T& at(int i);
        void erase(int i);               // i번째 인덱스의 원소를 삭제한다. 
        void insert(int i, const T& e);  // i번째 인덱스에 원소를 삽입한다.
        void reserve(int n);             // N개의 공간을 확보한다.
        // 유지 보수 함수 생략
    private:
        int capacity;  // 배열의 크기
        int n;         // 벡터에 저장된 원소들의 수
        T* A;          // 원소 저장 배열
    };

    template<typename T>
    ArrayVector<T>::ArrayVector(): capacity(0), n(0), A(nullptr) {}

    template<typename T>
    int ArrayVector<T>::size() const {
        return n;
    }

    template<typename T>
    bool ArrayVector<T>::empty() const {
        return (n == 0);
    }

    template<typename T>
    T& ArrayVector<T>::operator[](int i) {
        return A[i];
    }

    template<typename T>
    T& ArrayVector<T>::at(int i) {
        if (i < 0 || i >= n) { throw IndexOutOfBounds("Illegal index in function at()"); }
        return A[i];
    }

    template<typename T>
    void ArrayVector<T>::erase(int i) {
        for (int j = i + 1; j < n; j++) {
            A[j - 1] = A[j];
        }
        n--;
    }

    template<typename T>
    void ArrayVector<T>::reserve(int N) {
        if (capacity >= N) return;
        T* B = new T[N];
        for (int j = 0; j < n; j++) {
            B[j] = A[j];
        }
        if (A != nullptr) delete[] A;
        A = B;
        capacity = N;
    }

    template<typename T>
    void ArrayVector<T>::insert(int i, const T& e) {
        if (n >= capacity) {                // 오버플로우(Overflow)가 발생한 경우 
            reserve(max(1, 2 * capacity));  // 더블링(Doubling)을 통해 배열을 확장한다.
        }
        for (int j = n - 1; j >= i; j--) {
            A[j + 1] = A[j];
        }
        A[i] = e;
        n++;
    }
    ```  

</br>

- 테스트  
    ```cpp
    int main(void) {
        ArrayVector<int> av;
        av.insert(0, 1);
        av.insert(1, 2);
        av.insert(2, 3);
        av.insert(3, 4);
        av.insert(4, 5);
        av.erase(2);
        for (int i = 0; i < av.size(); i++) {
            cout << av.at(i) << " ";
        }
        cout << endl;
    }
    ```  
    ```
    1 2 4 5
    ```

</br>

## ✔️ **2. 리스트(List)**  
- 만약 링크드 리스트로 구현된 리스트 $S$가 있다면, 리스트를 액세스하고 갱신하는 곳을 지정하는 수단으로 인덱스를 사용하는 것 대신에 **노드(node)** 를 사용하는 것이 더욱 자연스럽고 효율적일 것이다.  

</br>

### **1) STL 리스트**  
- STL 리스트는 `list`라 불리는 리스트의 구현을 제공한다.  
- STL vector와 같이 STL list는 STL 컨테이너의 한 예로, 이중 링크드 리스트로 구현되어 있다.  
- STL 리스트의 기본 타입이 클래스일 때, 모든 원소의 복사(예를 들어, `push_back`에서)는 그 클래스의 복사 생성자를 호출하여 수행된다.  
- 또한 원소들이 소멸될 때(예를 들어, 소멸자나 멤버 함수 `pop_back`을 호출하여), 각 삭제된 원소에 대해 클래스의 소멸자가 호출된다.  

</br>

- `list(n)` : $n$ 원소의 공간을 갖는 리스트를 생성한다. 만약 인수가 없으면 빈 리스트를 생성한다.
- `size()` : $L$에 있는 원소의 개수를 반환한다.  
- `empty()` : $L$이 비어있으면 참을, 그렇지 않으면 거짓을 반환한다.  
- `front()` : $L$의 첫 번째 원소에 대한 레퍼런스를 반환한다.  
- `back()` : $L$의 마지막 원소에 대한 레퍼런스를 반환한다.  
- `push_front(e)` : 객체 $e$의 복사본을 $L$의 처음에 삽입한다.
- `push_back(e)` : 객체 $e$의 복사본을 $L$의 마지막에 삽입한다.  
- `pop_front()` : 벡터의 첫 번째 원소를 제거한다.  
- `pop_back()` : 벡터의 마지막 원소를 제거한다.  

<br>

- 테스트  
    ```cpp
    #include <iostream>
    #include <list>
    #include <iterator>

    using namespace std;

    int main(void) {
        list<int> li;
        li.push_front(3);
        li.push_front(2);
        li.push_front(1);
        li.push_back(4);
        li.push_back(5);
        li.push_back(6);
        li.push_back(7);

        li.pop_front();
        li.pop_back();

        list<int>::iterator iter = li.begin();
        while (iter != li.end()) {
            cout << *iter << " ";
            iter++;
        }
        cout << endl;
        return EXIT_SUCCESS;
    }
    ```
    ```
    2 3 4 5 6 
    ```  

</br>

### **2) 이중 링크드 리스트 구현**  
- 코드 구현
    ```cpp
    #include <iostream>

    using namespace std;


    template<typename T>
    class NodeList;

    template<typename T>
    struct Node {
        T elem;
        Node<T>* prev;
        Node<T>* next;
    };


    /*
    * 이중 링크드 리스트를 위한 반복자 클래스 Iterator
    */
    template<typename T>
    class Iterator {
    public:
        T& operator*();                            // 원소를 레퍼런스한다. 
        bool operator==(const Iterator& p) const;  // 위치를 비교한다.
        bool operator!=(const Iterator& p) const;
        Iterator& operator++();                    // 다음 위치로 이동한다.
        Iterator& operator--();                    // 이전 위치로 이동한다.
        friend class NodeList<T>;                     
    private:
        Node<T>* v;
        Iterator(Node<T>* u);                         
    };

    template<typename T>
    Iterator<T>::Iterator(Node<T>* u) {
        v = u;
    }

    template<typename T>
    T& Iterator<T>::operator*() {
        return v->elem;
    }

    template<typename T>
    bool Iterator<T>::operator==(const Iterator& p) const {
        return v == p.v;
    }

    template<typename T>
    bool Iterator<T>::operator!=(const Iterator& p) const {
        return v != p.v;
    }

    template<typename T>
    Iterator<T>& Iterator<T>::operator++() {
        v = v->next;   // 다음 위치로 이동하여
        return *this;  // 이터레이터 자신을 리턴한다.
    }

    template<typename T>
    Iterator<T>& Iterator<T>::operator--() {
        v = v->prev;
        return *this;
    }


    template<typename T>
    class NodeList {
    public:
        NodeList();
        int size() const;
        bool empty() const;
        Iterator<T> begin() const;
        Iterator<T> end() const;
        void insertFront(const T& e);
        void insertBack(const T& e);
        void insert(const Iterator<T>& p, const T& e);  // p의 앞에 e를 삽입한다.
        void eraseFront();
        void eraseBack();
        void erase(const Iterator<T>& p);
        // 유지 보수 함수 생략
    private:
        int n;          // 원소 개수
        Node<T>* header;   // 리스트 앞에 있는 센티널
        Node<T>* trailer;  // 리스트 뒤에 있는 센티널
    };

    template<typename T>
    NodeList<T>::NodeList() {
        n = 0;
        header = new Node<T>;
        trailer = new Node<T>;
        header->next = trailer;
        trailer->prev = header;
    }

    template<typename T>
    int NodeList<T>::size() const {
        return n;
    }

    template<typename T>
    bool NodeList<T>::empty() const {
        return (n == 0);
    }

    template<typename T>
    Iterator<T> NodeList<T>::begin() const {
        return Iterator<T>(header->next);
    }

    template<typename T>
    Iterator<T> NodeList<T>::end() const {
        return Iterator<T>(trailer->prev);
    }

    // p가 가리키는 위치 앞에 원소 e를 삽입한다.
    template<typename T>
    void NodeList<T>::insert(const Iterator<T>& p, const T& e) {
        Node<T>* w = p.v;
        Node<T>* u = w->prev;
        Node<T>* v = new Node<T>;
        v->elem = e;
        v->prev = u; u->next = v;
        v->next = w; w->prev = v;
        n++;
    }

    template<typename T>
    void NodeList<T>::insertFront(const T& e) {
        insert(begin(), e);
    }

    template<typename T>
    void NodeList<T>::insertBack(const T& e) {
        insert(end(), e);
    }

    // p가 가리키는 위치에 있는 노드를 삭제한다.
    template<typename T>
    void NodeList<T>::erase(const Iterator<T>& p) {
        Node<T>* v = p.v;
        Node<T>* w = v->next;
        Node<T>* u = v->prev;
        u->next = w; w->prev = u;
        delete v;
        n--;
    }

    template<typename T>
    void NodeList<T>::eraseFront() {
        erase(begin());
    }

    template<typename T>
    void NodeList<T>::eraseBack() {
        erase(end());
    }
    ```  

</br>

- 코드 구현
    ```cpp
    int main(void) {
        NodeList<int> nlist;
        nlist.insertFront(5);
        nlist.insertFront(4);
        nlist.insertFront(3);
        nlist.insertFront(1);

        Iterator<int> i = nlist.begin();
        ++i;
        nlist.insert(i, 2);

        Iterator<int> itr = nlist.begin();
        while((itr != nlist.end())) {
            cout << *itr << " ";
            ++itr;
        }
        cout << *itr << endl;
    }
    ```  
    ```
    1 2 3 4 5
    ```  

</br>

## ✔️ **3. STL 컨테이너(Containers)와 반복자(Iterators)**  

</br>

### **1) STL 컨테이너(Containers)**  
- 컨테이너(Container)는 원소의 모음을 저장하는 자료구조이다.  

</br>  

- STL 컨테이너 종류  
    |STL 컨테이너|설명|  
    |-----------|----|
    | vector | 벡터 |
    | deque | 양방향 큐(데크) |
    | list | 리스트 |
    | stack | 스택 |
    | queue| 큐 |
    | priority_queue| 우선순위 큐 |  
    | set (multiset) | 집합 (다중 집합) |
    | map (multimap) | 맵 (다중 맵) |  

<br>

### **2) STL 반복자(Iterators)**  
- 모든 STL 컨테이너 클래스는 반복자라 불리는 특수한 연관 클래스를 정의한다.  
- 반복자는 컨테이너 내부의 위치(position)을 명시하고 있으며, 다른 위치로 항해하는 기능을 부여받은 객체이다.  
- 만약 p가 컨테이너 내부의 위치를 참조하는 반복자라면, `*p`는 연관된 원소에의 레퍼런스가 된다. 
- 컨테이너의 다음 원소로 전진하기 위해선 반복자를 증가시키면 된다.  
- 각 STL 컨테이너 클래스는 두 개의 멤버 함수 `begin()`과 `end()`를 제공하는데, 이들은 이 컨테이너의 반복자를 반환한다.  
- 각 STL 컨테이너 클래스는 두 개의 멤버 함수 `begin`과 `end`를 제공하는데, 이들은 이 컨테이너의 반복자를 반환한다.  
- 앞의 것은 컨테이너의 첫 번째 원소를 포인트하는 반복자를 반환하고, 뒤의 것은 컨테이너의 마지막 원소의 바로 뒤에 있는 가상 원소를 포인트하는 반복자를 반환한다.  

</br>  

- 테스트
    ```cpp
    #include <iostream>
    #include <vector>

    using namespace std;


    int vectorSum1(vector<int> V) {
        typedef vector<int>::iterator Iterator;  // 반복자 타입
        int sum = 0;
        for (Iterator p = V.begin(); p != V.end(); p++) {
            sum += *p;
        }
        return sum;
    }


    int main(void) {
        vector<int> vec = {1,2,3,4,5};
        cout << "vector sum : " << vectorSum1(vec) << endl;
        return EXIT_SUCCESS;
    }
    ```  

</br>

- 대부분의 STL 컨테이너(리스트, 셋, 그리고 맵 포함)는 앞으로 뿐만 아니라, 뒤로 이동하는 기능도 제공한다.  
    - 그러한 컨테이너에는 감소 연산자 `--p` 또는 `p--`가 그들의 반복자에 정의되어 있다.  
    - 이는 **양방향(Bidirectional) 반복자** 라고 불린다.  

</br>

- 소수의 STL 컨테이너(벡터와 큐 포함)는 정수를 빼고 더하는 것을 허용하는 추가 기능을 지원한다.  
    - 예를 들어, 그런 반복자 $p$에 대해 $p + 3$은 컨테이너에서 $p$보다 세 위치 뒤에 있는 원소를 가리킨다.  
    - 이것을 **임의-접근(Random-access) 반복자** 라 한다.  

</br>

### **3) Const 반복자(Iterators)**  
- 위와 같이 함수에 벡터 $V$를 **값으로(by value)** 전달하게 되면, 시스템이 실제 인수 전체를 별도로 만들어야 하기 때문에 매우 비효율적이다.  
- 이 함수가 $V$를 수정하지 않기 때문에, 가장 좋은 방법은 인수를 상수 레퍼런스로, 즉 `const vector<int>&`과 같이 선언하는 것일 것이다.  
- 그러나 만약 이러한 벡터에 반복자를 선언하면 문제가 발생한다.  
    - 많은 STL 구현은 일반 반복자를 상수 벡터 레퍼런스와 사용하려고 하면 에러 메시지를 발생시킨다.  
    - 왜냐하면, 그러한 반복자가 벡터의 내용을 변경하려고 시도할 수 있기 때문이다.  
- 해결책은 **const 반복자** 라 불리는 특별한 읽기-전용 반복자이다.  
    - const 반복자를 사용하면 반복자를 디레퍼런스하여 컨테이너의 값을 읽을 수는 있지만, 컨테이너의 값을 수정하지는 못한다.  

</br>

- 테스트
    ```cpp
    #include <iostream>
    #include <vector>

    using namespace std;


    int vectorSum2(vector<int>& V) {
        typedef vector<int>::const_iterator ConstIterator;  // 반복자 타입
        int sum = 0;
        for (ConstIterator p = V.begin(); p != V.end(); p++) {
            sum += *p;
        }
        return sum;
    }


    int main(void) {
        vector<int> vec = {1,2,3,4,5};
        cout << "vector sum : " << vectorSum2(vec) << endl;
        return EXIT_SUCCESS;
    }
    ```  

</br>

### **4) STL 반복자-기반 컨테이너**  
- STL 반복자는 STL 컨테이너의 원소에 접근하기 위한 유연하고 균일한 방법을 제공한다.  
- 많은 멤버 함수와 STL 컨테이너와 작업하는 내장 알고리즘들이 반복자를 인수로 사용한다.  
- $V$는 주어진 기본 타입의 STL 벡터이고, $e$는 이 기본 타입의 객체, $p$와 $q$는 이 기본타입에 대한 반복자로서 같은 컨테이너에서 나왔다고 할 때,  
    - `vector(p, q)` : $p$와 $q$ 사이를 반복하면서 그들 원소를 새 벡터에 복사하여 넣는다.  
    - `assign(p, q)` : $V$의 내용을 삭제하고, $p$와 $q$ 사이를 반복하면서 그들 원소를 $V$의 새 내용으로 넣는다.  
    - `insert(p, q)` : $e$를 복사하여 $p$에 의해 주어진 위치의 바로 앞에 삽입하고, 이후의 원소들은 한 위치씩 오른쪽으로 이동한다.  
    - `erase(p)` : $p$에 의해 주어진 위치에 있는 $V$의 원소를 삭제하고, 이후의 원소들은 한 위치씩 왼쪽으로 이동한다.  
    - `erase(p, q)` : $p$와 $q$ 사이를 반복하면서 그들 원소를 삭제하고 이후의 원소들은 이동하여 공백을 채운다.  
    - `clear()` : $V$의 모든 원소를 삭제한다.  
- 반복자의 범위를 나타낼 때 반복자의 범위는 $p$에서 시작하여 $q$의 '바로 앞'까지이다.  

</br>

- 생성자와 할당 함수에서 반복자 $p$와 $q$가 $V$와 같은 타입의 컨테이너에서 만들어질 필요는 없고, 단지 $V$와 같은 베이스 타입을 갖는 컨테이너에서 만들어지면 된다.  
    - 예를 들어, $L$이 정수 STL List 컨테이너라 할 때, 다음과 같이 $L$을 복사하여 STL 벡터 $V$로 만들 수 있다.  
        ```cpp
        list<int> L = {1,2,3,4,5};
        // ... 
        vector<int> V(L.begin(), L.end());
        ```  
    - 반복자-기반 형태의 생성자는 매우 편리한데, 표준 C++ 배열로부터 STL 컨테이너의 내용을 초기화하는 간단한 방법을 제공하기 때문이다.  
    - 예를 들어, 아래와 같이 포인터 연산을 사용하여 C++ 배열로부터 벡터를 초기화할 수 있다.  
        ```cpp
        int arr[5] = {1, 2, 3, 4, 5};
        // ... 
        vector<int> V(arr, arr + 5);
        ```  

</br>

## ✔️ **4. 시퀀스(Sequenece)**  
- 시퀀스(Sequence)는 벡터와 리스트 ADT를 일반화하는 ADT이다.  
- 시퀀스 ADT는 인덱스와 위치, 양쪽 모두를 사용하여 원소에 접근하는 것을 제공하는 다양한 응용을 위한 융통성 있는 ADT이다.  

</br>

### **1) 이중 링크드 리스트로 시퀀스 구현**  
- 코드 구현  
    ```cpp
    template<typename T>
    class NodeSequence : public NodeList<T> {
    public:
        Iterator<T> atIndex(int i) const;
        int indexOf(const Iterator<T>& p) const;
    };

    template<typename T>
    Iterator<T> NodeSequence<T>::atIndex(int i) const {
        Iterator p = begin();
        for (int j = 0; j < i; j++) ++p;
        return p;
    }

    template<typename T>
    int NodeSequence<T>::indexOf(const Iterator<T>& p) const {
        Iterator<T> q = begin();
        int j = 0;
        while (q != p) {
            q++; j++;
        }
        return j;
    }
    ```  
    - 위와 같이 링크드 리스트로 시퀀스 ADT를 구현할 경우 인덱스 접근 연산에 $O(n)$이 걸린다.  

</br>

