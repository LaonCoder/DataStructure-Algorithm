# ✍️ **1. Array and Linked List**  

</br>

## ✔️ **1. 배열**  
- 배열은 순서대로 무언가를 저장할 때는 유용
- But, 변화에 취약 (STL 벡터에서 이 문제 해결)

</br>

### **1. 삽입 정렬**
- 두 번째 원소부터 시작하여 그 앞(왼쪽)의 원소들과 비교하여 삽입할 위치를 지정한 후, 원소를 뒤로 옮기고 지정한 자리에 원소를 삽입하여 정렬하는 알고리즘
- 배열의 모든 원소를 앞에서부터 차례대로 이미 정렬된 배열 부분과 비교하여, 자신의 위치를 찾아 삽입함으로써 정렬을 완성하는 알고리즘
- 시간 복잡도 : 최선 O(N^2) / 최악 O(N)  
    ```cpp
    #include <iostream>
    #include <string>

    using namespace std;

    void insertionSort(char* A, int n) {
        for (int i = 1; i < n; i++) {
            char cur = A[i];
            int j = i - 1;
            while ((j >= 0) && (cur < A[j])) {
                A[j + 1] = A[j];
                j--;
            }
            A[j + 1] = cur;
        }
    }

    int main() {
        char* a = new char[10];
        a[0] = 'D';
        a[1] = 'E';
        a[2] = 'C';
        a[3] = 'A';
        a[4] = 'B';
        a[5] = 'F';
        a[6] = 'I';
        a[7] = 'G';
        a[8] = 'J';
        a[9] = 'H';

        insertionSort(a, 10);
        for (int i = 0; i < 10; i++) {
            cout << a[i] << ' ';
        }
        cout << endl;
    }
    ```  
    ```
    A B C D E F G H I J 
    ```  

</br>

### **2. STL 벡터로 행렬 구현**  
- `vector` 클래스 사용  
    ```cpp
    #include <iostream>
    #include <vector>

    using namespace std;

    int main(void) {
        // n x m = 3 x 4 matrix
        int n = 3;
        int m = 4; 
        int k = 5;

        int i, j;

        // 동적 할당 방식 사용 (n x m matrix) 
        int** M1 = new int*[n];
        for (i = 0; i < n; i++) {
            M1[i] = new int[m];
        }

        // 메모리 해제
        for (int i = 0; i < n; i++) 
            delete[] M1[i];
        delete[] M1;

        // 동적 할당 방식 사용 (n x m x k matrix)
        int*** M2 = new int**[n];
        for (i = 0; i < n; i++) {
            M2[i] = new int*[m];
            for (j = 0; j < m; j++) {
                M2[i][j] = new int[k];
            }
        }

        // 메모리 해제
        for (i = 0; i < n; i++) {
            for (j = 0; j < m; j++) {
                delete[] M2[i][j];
            }
            delete[] M2[i];
        }
        delete[] M2;
        

        // vector 클래스 사용 (n x m matrix)
        // -> M3()
        vector<vector<int>> M3; // n x m 행렬

        // 초기화
        if (!M3.empty()) M3.clear();

        // 할당
        M3.assign(n, vector<int>(m, 0));

        // 원소 접근
        M3[1][2] = 3;
        
        cout << M3[1][2] << endl;  // >>> 3

        // vector 클래스 사용 (n x m x k matrix)
        vector<vector<vector<int>>> M4(n, vector<vector<int>>(m, vector<int>(k, 0)));
        // 초기화
    }
    ```  

</br>

### **3. Tic-Tac-Toe**
- 배열을 활용한 틱택토 게임 구현
    ```cpp
    #include <cstdlib> 
    #include <iostream>

    using namespace std;

    #define O 1
    #define X -1
    #define EMPTY 0

    int board[3][3];
    int currentPlayer;

    void clearBoard() {
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                board[i][j] = EMPTY;
            }
        }
        currentPlayer = X;
    }

    void putMark(int i, int j) {
        board[i][j] = currentPlayer;
        currentPlayer = -currentPlayer;
    }

    bool isWin(int mark) {
        int win = mark * 3;
        return (
            (board[0][0] + board[0][1] + board[0][2]) == win 
        || (board[1][0] + board[1][1] + board[1][2]) == win
        || (board[2][0] + board[2][1] + board[2][2]) == win
        || (board[0][0] + board[1][0] + board[2][0]) == win
        || (board[0][1] + board[1][1] + board[2][1]) == win
        || (board[0][2] + board[1][2] + board[2][2]) == win
        || (board[0][0] + board[1][1] + board[2][2]) == win
        || (board[2][0] + board[1][1] + board[0][2]) == win
        );
    }

    int getWinner() {
        if (isWin(X)) return X;
        else if (isWin(O)) return O;
        else return EMPTY;
    }

    void printBoard() {
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                switch(board[i][j]) {
                    case O: 
                        cout << "O"; 
                        break;
                    case X: 
                        cout << "X"; 
                        break;
                    case EMPTY: 
                        cout << " "; 
                        break;
                }
                if (j < 2) cout << "|";
            }
            if (i < 2) cout << "\n-+-+-\n";
        }
        cout << endl;
    }

    int main() {
        clearBoard();
        putMark(0, 0);  putMark(1, 1);
        putMark(0, 1);  putMark(0, 2);
        putMark(2, 0);  putMark(1, 2);
        putMark(2, 2);  putMark(2, 1);
        putMark(1, 0);

        printBoard();

        int winner = getWinner();
        if (winner != EMPTY) 
            cout << "Winner : " << ((winner == X) ? "X" : "O") << endl;
        else
            cout << "Tie" << endl;

        return EXIT_SUCCESS;
    }
    ```  

</br>

## ✔️ **2. 링크드 리스트**  
- 배열과 달리 미리 선언되어 고정된 크기를 갖는 것이 아님
- 노드를 추가하거나 삭제함으로써 사이즈 재조정 가능

</br>

### **1. 단일 링크드 리스트**
- 노드 구현
    ```cpp
    #include <iostream>
    #include <string>

    using namespace std;

    template <typename E>
    class SLinkedList;

    template <typename E>
    class SNode {
    private:
        E elem;
        SNode<E>* next;

        // SLinkedList 클래스에서 SNode의 private 변수 접근 가능
        friend class SLinkedList<E>; 
    };
    ```  

</br>

- 링크드 리스트 구현
    ```cpp
    template <typename E>
    class SLinkedList {
    public:
        SLinkedList();
        ~SLinkedList();
        bool empty() const;
        const E& front() const;
        void addFront(const E& e);
        void removeFront();
        void printAll();
    private:
        SNode<E>* head;
    };

    // 생성자
    template<typename E>
    SLinkedList<E>::SLinkedList(): head(NULL) {};

    // 소멸자
    template<typename E>
    SLinkedList<E>::~SLinkedList() {
        while(!empty()) removeFront();
    }

    // 링크드 리스트가 비었는지 체크
    template<typename E>
    bool SLinkedList<E>::empty() const {
        return (head == NULL) ? true : false;
    }

    // header에 있는 원소 리턴
    template<typename E>
    const E& SLinkedList<E>::front() const {
        return head->elem;
    }

    // 단일 링크드 리스트 앞에 삽입
    template<typename E>
    void SLinkedList<E>::addFront(const E& e) {
        SNode<E>* newNode = new SNode<E>;
        newNode->elem = e;
        newNode->next = head;
        head = newNode;
    }

    // 단일 링크드 리스트 앞에서 삭제
    template<typename E>
    void SLinkedList<E>::removeFront() {
        if (!empty()) {
            SNode<E>* temp = head;
            head = temp->next;
            delete temp;
        }
    }

    // 링크드 리스트 내의 모든 원소 출력
    template<typename E>
    void SLinkedList<E>::printAll() {
        SNode<E>* curNode = head;
        while (curNode) {
            cout << curNode->elem << " ";
            curNode = curNode->next;
        }
        cout << endl;
    }
    ```  

</br>  

- 테스트
    ```cpp
    int main() {
        SLinkedList<string>* sll = new SLinkedList<string>();

        sll->addFront("F");
        sll->addFront("E");
        sll->addFront("D");
        sll->addFront("C");
        sll->addFront("B");
        sll->addFront("A");

        sll->removeFront();  // -> SNode("A") 삭제

        sll->printAll();
    }
    ```  
    ```
    B C D E F 
    ```  
 
</br>  

### **2. 이중 링크드 리스트**
- 단일 링크드 리스트에선 지우려는 노드의 바로 이전 노드로 바로 접근하기 위한 빠른 방법이 없기 때문에, head에서 멀리 있는 노드를 지우는 것은 많은 시간을 소모  
- 이를 해결하기 위해 양방향의 노드를 가리킬 수 있는 포인터를 갖는 이중 링크드 리스트 사용  

</br>

- 노드 구현
    ```cpp
    #include <iostream>
    #include <string>

    using namespace std;
    typedef string Elem;  // string 타입을 Elem 타입이라고 정의

    class DLinkedList;

    class DNode {
    private:
        Elem elem;
        DNode* next;
        DNode* prev;

        // DLinkedList 클래스에서 SNode의 private 변수 접근 가능
        friend class DLinkedList; 
    };
    ```  

</br>

- 이중 링크드 리스트 구현
    ```cpp
    class DLinkedList {
    public:
        DLinkedList();
        ~DLinkedList();
        bool empty() const;
        const Elem& front() const;
        const Elem& back() const;
        void addFront(const Elem &e);
        void addBack(const Elem &e);
        void removeFront();
        void removeBack();
        void printAll();
    private:
        DNode* header;
        DNode* trailer;
    protected:
        void add(DNode* v, const Elem& e);
        void remove(DNode* v);
    };

    // 생성자
    DLinkedList::DLinkedList() {
        header = new DNode;
        trailer = new DNode;
        header->next = trailer;
        header->prev = nullptr;
        trailer->prev = header;
        trailer->next = nullptr;
    };

    DLinkedList::~DLinkedList() {
        while (!empty()) removeFront();
        delete header;
        delete trailer;
    }

    bool DLinkedList::empty() const {
        return (header->next == trailer); 
    }

    const Elem& DLinkedList::front() const {
        return header->next->elem;
    }

    const Elem& DLinkedList::back() const {
        return trailer->prev->elem;
    }

    // v 노드 앞에 새로운 노드 삽입
    void DLinkedList::add(DNode* v, const Elem& e) {
        DNode* newNode = new DNode;
        newNode->elem = e;
        newNode->next = v;
        newNode->prev = v->prev;
        v->prev = v->prev->next = newNode;
    }

    // 리스트의 앞에서 추가
    void DLinkedList::addFront(const Elem& e) {
        add(header->next, e);
    }

    // 리스트의 뒤에서 추가
    void DLinkedList::addBack(const Elem& e) {
        add(trailer, e);
    }

    // v 노드 삭제
    void DLinkedList::remove(DNode* v) {
        DNode* prevNode = v->prev;
        DNode* nextNode = v->next;
        prevNode->next = nextNode;
        nextNode->prev = prevNode;
        delete v;
    }

    void DLinkedList::removeFront() {
        remove(header->next);
    }

    void DLinkedList::removeBack() {
        remove(trailer->prev);
    }

    void DLinkedList::printAll() {
        DNode* curNode = header->next;
        while (curNode != trailer) {
            cout << curNode->elem << " ";
            curNode = curNode->next;
        }
        cout << endl;
    }
    ```  

</br>  

- 리스트 반전 함수 (`listReverse`)
    ```cpp
    // 리스트 반전
    void listReverse(DLinkedList* dll) {
        DLinkedList tempDll;
        
        // dll을 tempDll에 반전
        while(!dll->empty()) {
            Elem s = dll->front();  dll->removeFront();
            tempDll.addFront(s);
        }

        // tempDll을 다시 dll로 복사
        while(!tempDll.empty()) {
            Elem s = tempDll.front();  tempDll.removeFront();
            dll->addBack(s);
        }
    }
    ```  

</br>

- 테스트
    ```cpp
    int main() {
        DLinkedList* dll = new DLinkedList;

        dll->addFront("F");
        dll->addFront("E");
        dll->addFront("D");
        dll->addFront("C");
        dll->addFront("B");
        dll->addFront("A");
        dll->removeBack();  // -> DNode("F") 삭제

        dll->printAll();

        listReverse(dll);  // 리스트 반전

        dll->printAll();

        return EXIT_SUCCESS;
    }
    ```  
    ```
    A B C D E 
    E D C B A 
    ```  

</br>

### **3. 원형 링크드 리스트**
- 원형 링크드 리스트에는 리스트를 탐색할 때 시작할 지점을 지정할 커서(Cursor) 노드 필요  

<br>  

- 노드 구현
    ```cpp
    #include <iostream>
    #include <string>

    using namespace std;

    typedef string Elem;  // string 타입을 Elem 타입이라고 정의

    class CLinkedList;

    class CNode {
    private:
        Elem elem;
        CNode* next;

        // DLinkedList 클래스에서 SNode의 private 변수 접근 가능
        friend class CLinkedList; 
    };
    ```  

</br>

- 원형 링크드 리스트 구현
    ```cpp
    class CLinkedList {
    public:
        CLinkedList();
        ~CLinkedList();
        bool empty() const;
        const Elem& front() const;
        const Elem& back() const;
        void advance();
        void add(const Elem& e);
        void remove();
        void printAll();
    private:
        CNode* cursor;
    };

    CLinkedList::CLinkedList() : cursor(nullptr) {}
    CLinkedList::~CLinkedList() {
        while(!empty()) remove();
    }

    bool CLinkedList::empty() const {
        return cursor == NULL;
    }

    // 커서 노드의 원소를 리턴한다.
    const Elem& CLinkedList::front() const {
        return cursor->elem;
    }

    // 커서 노드의 다음 노드의 원소를 리턴한다.
    const Elem& CLinkedList::back() const {
        return cursor->next->elem;
    }

    // 커서 노드를 다음으로 이동시킨다.
    void CLinkedList::advance() {
        cursor = cursor->next;
    }

    // 커서 노드의 다음에 새 노드 추가
    void CLinkedList::add(const Elem& e) {
        CNode* newNode = new CNode;
        newNode->elem = e;

        if (cursor == nullptr) {
            newNode->next = newNode;
            cursor = newNode;
        } else {
            newNode->next = cursor->next;
            cursor->next = newNode;
        }
    }

    // 커서 노드의 다음 노드 삭제
    void CLinkedList::remove() {
        CNode* temp = cursor->next;
        if (temp == cursor) {
            cursor = nullptr;
        } else {
            cursor->next = temp->next;
        }
        delete temp;
    }

    void CLinkedList::printAll() {
        CNode* curNode = cursor->next;
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
    int main() {
        CLinkedList* cll = new CLinkedList;

        cll->add("F");
        cll->add("E");
        cll->add("D");
        cll->add("C");
        cll->add("B");
        cll->add("A");

        cll->printAll();

        // 커서 노드 세 노드 전진
        cll->advance();
        cll->advance();
        cll->advance();
        cll->remove();

        cll->printAll();

        return EXIT_SUCCESS;
    }
    ```  
    ```
    A B C D E F
    E F A B C
    ```  

</br>







