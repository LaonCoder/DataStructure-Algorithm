# ✍️ **3. Stack, Queue and Deque**  

</br>

## ✔️ **1. 스택(Stack)**  
- 스택은 **후입선출(LIFO)** 의 원리에 따라 삽입과 삭제를 수행하는 객체의 저장 매체이다.  

</br>

### **1) STL 스택**
- `size()` : 스택 내의 객체의 개수 반환한다.
- `empty()` : 스택이 비어 있으면 참을, 그렇지 않으면 거짓을 반환한다.
- `push(e)` : 객체 e를 스택의 최상위에 삽입한다.  
- `pop()` : 스택의 최상위에 있는 객체를 스택에서 제거한다.  
- `top()` : 스택의 최상위 원소의 레퍼런스를 반환한다.  

</br>

- 테스트
    ```cpp
    #include <iostream>
    #include <stack>

    using namespace std;


    void popAll (stack<int>& s) {
        cout << "stackSize : " << s.size() << endl;
        while(!s.empty()) {
            cout << "pop (" << s.top() << ")" << endl;
            s.pop();
        }
    }

    int main(void) {
        stack<int> newStack;
        newStack.push(1);
        newStack.push(2);
        newStack.push(3);
        newStack.push(4);
        newStack.push(5);

        popAll(newStack);
    }
    ```  
    ```
    stackSize : 5
    pop (5)
    pop (4)
    pop (3)
    pop (2)
    pop (1)
    ```  

</br>

### **2) 배열을 이용한 스택 구현**  
- 스택에 들어가는 아이템의 개수를 정확하게 예측할 수 있는 경우, 배열에 기반한 구현 방법이 속도와 간결성 측면에서 가장 좋다.  
- 하지만, 배열을 사용하여 스택을 구현한다면 초기 capacity의 제약을 받는다는 문제가 있다.
- 이를 해결하기 위해 임의로 용량을 제한하지 않는 구현 방법으로는 동적으로 저장 공간이 확장되는 STL vector를 사용하는 방법 등이 있다.  

</br>

- 코드 구현  
    ```cpp
    #include <iostream>
    #include <vector>
    #include "runtimeException.h"

    using namespace std;


    class StackEmpty: public RuntimeException {
    public:
        StackEmpty(const string& err) : RuntimeException(err) {}
    };

    class StackFull: public RuntimeException {
    public:
        StackFull(const string& err) : RuntimeException(err) {}
    };


    template<typename T>
    class ArrayStack {
        enum { DEF_CAPACITY = 100 };
    public:
        ArrayStack(int cap = DEF_CAPACITY);
        int size() const;
        bool empty() const;
        T top() const;
        void push(const T& e);
        void pop(); 
    private:
        T* S;          // 스택 원소들의 배열
        int capacity;  // 스택 용량
        int t;         // 스택 내 최상위 원소의 인덱스
    };

    template<typename T>
    ArrayStack<T>::ArrayStack(int cap): S(new T[cap]), capacity(cap), t(-1) {}

    template<typename T>
    int ArrayStack<T>::size() const {
        return (t + 1);
    }

    template<typename T>
    bool ArrayStack<T>::empty() const {
        return (t < 0);
    }

    template<typename T>
    T ArrayStack<T>::top() const {
        if (empty()) throw StackEmpty("Top of empty stack");
        return S[t];
    }

    template<typename T>
    void ArrayStack<T>::push(const T& e) {
        if (size() == capacity) throw StackFull("Push to full stack");
        S[++t] = e;
    }

    template<typename T>
    void ArrayStack<T>::pop() {
        if (empty()) throw StackEmpty("Pop from empty stack");
        --t;
    }
    ```  
    ```cpp
    // runtimeException.h
    #ifndef RUNTIME_EXCEPTION_H
    #define RUNTIME EXCEPTION_H

    #include <iostream>
    #include <string>

    using namespace std;

    class RuntimeException {
    private:
        string errorMsg;
    public:
        RuntimeException(const string& err) { errorMsg = err; }
        string getMessage() const { return errorMsg; }
    };

    #endif
    ```  

</br>

- 테스트  
    ```cpp
    template<typename T>
    void popAll (ArrayStack<T>& s) {
        cout << "stackSize : " << s.size() << endl;
        while(!s.empty()) {
            cout << "pop (" << s.top() << ")" << endl;
            s.pop();
        }
    }

    int main(void) {
        ArrayStack<int> newStack;
        newStack.push(1);
        newStack.push(2);
        newStack.push(3);
        newStack.push(4);
        newStack.push(5);

        popAll(newStack);
    }
    ```  
    ```
    stackSize : 5
    pop (5)
    pop (4)
    pop (3)
    pop (2)
    pop (1)
    ```  

</br>

### **3) 링크드 리스트를 이용한 스택 구현**  
- 링크드 리스트를 사용하는 경우, 배열과 같이 capacity의 제약이 존재하지 않는다.  

</br>

- 코드 구현
    ```cpp
    #include <iostream>
    #include <vector>
    #include "runtimeException.h"
    #include "slinkedlist.h"

    using namespace std;


    class StackEmpty: public RuntimeException {
    public:
        StackEmpty(const string& err) : RuntimeException(err) {}
    };

    class StackFull: public RuntimeException {
    public:
        StackFull(const string& err) : RuntimeException(err) {}
    };


    template<typename T>
    class LinkedStack {
    public:
        LinkedStack();
        int size() const;
        bool empty() const;
        T top() const;
        void push(const T& e);
        void pop(); 
    private:
        SLinkedList<T> S;  // 원소의 링크드 리스트
        int n;             // 원소의 개수
    };

    template<typename T>
    LinkedStack<T>::LinkedStack(): S(), n(0) {}

    template<typename T>
    int LinkedStack<T>::size() const {
        return n;
    }

    template<typename T>
    bool LinkedStack<T>::empty() const {
        return (n == 0);
    }

    template<typename T>
    T LinkedStack<T>::top() const {
        if (empty()) throw StackEmpty("Top of empty stack");
        return S.front();
    }

    template<typename T>
    void LinkedStack<T>::push(const T& e) {
        // if (size() == capacity) throw StackFull("Push to full stack");
        n++;
        S.addFront(e);
    }

    template<typename T>
    void LinkedStack<T>::pop() {
        if (empty()) throw StackEmpty("Pop from empty stack");
        n--;
        S.removeFront();
    }
    ```  

</br>

- 테스트  
    ```cpp
    template<typename T>
    void popAll (LinkedStack<T>& s) {
        cout << "stackSize : " << s.size() << endl;
        while(!s.empty()) {
            cout << "pop (" << s.top() << ")" << endl;
            s.pop();
        }
    }

    int main(void) {
        LinkedStack<int> newStack;
        newStack.push(1);
        newStack.push(2);
        newStack.push(3);
        newStack.push(4);
        newStack.push(5);

        popAll(newStack);
    }
    ```  

</br>

### **4) 스택을 이용한 벡터 역순화**
- 코드 구현  
    ```cpp
    template<typename T>
    void reverseVector(vector<T>& v) {
        LinkedStack<T> s;
        for (int i = 0; i < v.size(); i++) {
            s.push(v[i]);
        }
        for (int i = 0; i < v.size(); i++) {
            v[i] = s.top();
            s.pop();
        }
    }
    ```  

</br>

## ✔️ **2. 큐(Queue)**  
- 큐는 **선입선출(FIFO)** 의 원리에 따라 삽입과 삭제를 수행하는 객체의 저장 매체이다.  

</br>

### **1) STL 큐**
- `size()` : 큐에 저장된 원소의 개수를 반환한다.
- `empty()` : 큐가 비어 있으면 참을, 그렇지 않으면 거짓을 반환한다.
- `push(e)` : e를 큐의 끝에 enqueue한다.
- `pop()` : 큐의 앞에 있는 원소를 dequeue한다.
- `front()` : 큐의 앞에 있는 원소의 레퍼런스를 반환한다.  
- `back()` : 큐의 끝에 있는 원소의 레퍼런스를 반환한다.  

</br>

- 테스트
    ```cpp
    #include <iostream>
    #include <queue>

    using namespace std;


    void dequeueAll (queue<int>& q) {
        cout << "queueSize : " << q.size() << endl;
        while(!q.empty()) {
            cout << "dequeue (" << q.front() << ")" << endl;
            q.pop();
        }
    }

    int main(void) {
        queue<int> newQueue;
        newQueue.push(1);
        newQueue.push(2);
        newQueue.push(3);
        newQueue.push(4);
        newQueue.push(5);

        cout << "front : " << newQueue.front() << endl;
        cout << "back : " << newQueue.back() << endl;

        dequeueAll(newQueue);
    }
    ```  
    ```
    front : 1
    back : 5
    queueSize : 5
    dequeue (1)
    dequeue (2)
    dequeue (3)
    dequeue (4)
    dequeue (5)
    ```  

</br>

### **2) 링크드 리스트를 이용한 큐의 구현**  
- 코드 구현  
    ```cpp
    #include <iostream>
    #include <queue>
    #include "runtimeException.h"
    #include "clinkedlist.h"

    using namespace std;


    class QueueEmpty: public RuntimeException {
    public:
        QueueEmpty(const string& err) : RuntimeException(err) {}
    };


    template<typename T>
    class LinkedQueue {
    public:
        LinkedQueue();
        int size() const;
        bool empty() const;
        const T& front() const;
        void enqueue(const T& e);
        void dequeue();
    private:
        CLinkedList<T> C;
        int n;
    };

    template<typename T>
    LinkedQueue<T>::LinkedQueue(): C(), n(0) {}

    template<typename T>
    int LinkedQueue<T>::size() const {
        return n;
    }

    template<typename T>
    bool LinkedQueue<T>::empty() const {
        return (n == 0);
    }

    template<typename T>
    const T& LinkedQueue<T>::front() const {
        if (empty()) { throw QueueEmpty("Front of empty queue"); }
        return C.front();
    }

    template<typename T>
    void LinkedQueue<T>::enqueue(const T& e) {
        C.add(e);  // 노드를 큐의 맨 앞에 넣는다.
        n++;
    }

    template<typename T>
    void LinkedQueue<T>::dequeue() {
        if (empty()) { throw QueueEmpty("Dequeue of empty queue"); }
        C.remove();  // 맨 끝의 노드를 하나 삭제한다.
        n--;
    }
    ```  
    ```cpp
    // clinkedlist.h (수정된 링크드 리스트)

    #include <iostream>
    #include <string>

    using namespace std;

    template<typename T>
    class CLinkedList;

    template<typename T>
    class CNode {
    private:
        T elem;
        CNode<T>* next;

        friend class CLinkedList<T>;
    };

    template<typename T>
    class CLinkedList {
    public:
        CLinkedList();
        ~CLinkedList();
        bool empty() const;
        const T& front() const;
        const T& back() const;
        void advance();
        void add(const T& e);
        void remove();
        void printAll();
    private:
        CNode<T>* cursor;
    };

    template<typename T>
    CLinkedList<T>::CLinkedList() : cursor(nullptr) {}

    template<typename T>
    CLinkedList<T>::~CLinkedList() {
        while (!empty()) remove();
    }

    template<typename T>
    bool CLinkedList<T>::empty() const {
        return cursor == nullptr; // nullptr로 변경
    }

    template<typename T>
    const T& CLinkedList<T>::front() const {
        return cursor->next->elem;
    }

    template<typename T>
    const T& CLinkedList<T>::back() const {
        return cursor->elem;
    }

    template<typename T>
    void CLinkedList<T>::advance() {
        cursor = cursor->next;
    }

    template<typename T>
    void CLinkedList<T>::add(const T& e) {
        CNode<T>* newNode = new CNode<T>;
        newNode->elem = e;

        if (cursor == nullptr) {
            newNode->next = newNode;
            cursor = newNode;
        } else {
            newNode->next = cursor->next;
            cursor->next = newNode;
            cursor = newNode;
        }
    }

    template<typename T>
    void CLinkedList<T>::remove() {
        CNode<T>* temp = cursor->next;
        if (temp == cursor) {
            delete cursor;
            cursor = nullptr;
        } else {
            cursor->next = temp->next;
            delete temp;
        }
    }

    template<typename T>
    void CLinkedList<T>::printAll() {
        CNode<T>* curNode = cursor->next;
        while (curNode != cursor) {
            cout << curNode->elem << " ";
            curNode = curNode->next;
        }
        cout << curNode->elem << endl;
    }
    ```  

</br>

- 테스트  
    ```cpp
    int main(void) {
        LinkedQueue<int> newQueue;
        newQueue.enqueue(1);
        newQueue.enqueue(2);
        newQueue.enqueue(3);
        newQueue.enqueue(4);
        newQueue.enqueue(5);

        cout << "front : " << newQueue.front() << endl;

        dequeueAll(newQueue);
    }
    ```  
    ```
    front : 1
    queueSize : 5
    dequeue (1)
    dequeue (2)
    dequeue (3)
    dequeue (4)
    dequeue (5)
    ```

</br>

## ✔️ **3. 양방향 큐(Doubly-Ended Queue)**  
- 데크(Deque)는 front와 rear 양쪽에서 삽입과 삭제연산을 모두 지원하는 큐와 유사한 자료구조이다.  

</br>

### **1) STL 데크**
- `size()` : 데크 내에 저장된 원소의 개수를 반환한다.
- `empty()` : 데크가 비어 있으면 참을, 그렇지 않으면 거짓을 반환한다.
- `push_front(e)` : 데크의 첫 번째에 새로운 원소 e를 삽입한다.
- `push_back(e)` : 데크의 마지막에 새로운 원소 e를 삽입한다.
- `pop_front()` : 데크의 첫 번째 원소를 삭제한다.  
- `pop_back()` : 데크의 마지막 원소를 삭제한다.
- `front()` : 데크의 첫 번째 원소에 대한 레퍼런스를 반환한다.
- `back()` : 데크의 마지막 원소에 대한 레퍼런스를 반환한다.  

</br>

- 테스트  
    ```cpp
    #include <iostream>
    #include <deque>

    using namespace std;

    void dequeueFrontAll (deque<int>& q) {
        cout << "dequeSize : " << q.size() << endl;
        while(!q.empty()) {
            cout << "dequeue (" << q.front() << ")" << endl;
            q.pop_front();
        }
        cout << endl;
    }

    void dequeueBackAll (deque<int>& q) {
        while(!q.empty()) {
            cout << "dequeue (" << q.back() << ")" << endl;
            q.pop_back();
        }
        cout << endl;
    }

    int main(void) {
        deque<int> deq;
        cout << "Dequeue front" << endl;
        deq.push_front(2);
        deq.push_front(1);
        deq.push_back(3);
        deq.push_back(4);
        deq.push_back(5);
        dequeueFrontAll(deq);

        cout << "Dequeue back" << endl;
        deq.push_front(2);
        deq.push_front(1);
        deq.push_back(3);
        deq.push_back(4);
        deq.push_back(5);
        dequeueBackAll(deq);
        
        return EXIT_SUCCESS;
    }
    ```  
    ```
    Dequeue front
    dequeSize : 5
    dequeue (1)
    dequeue (2)
    dequeue (3)
    dequeue (4)
    dequeue (5)

    Dequeue back
    dequeue (5)
    dequeue (4)
    dequeue (3)
    dequeue (2)
    dequeue (1)

    ```  

</br>