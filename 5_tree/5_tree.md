# ✍️ **5. Tree**  

</br>

## ✔️ **1. 일반 트리(Tree)**  
- 트리(tree)는 원소들을 **계층적(hierarchical)으로** 저장하는 ADT이다.  
- 최상위 원소를 제외하고, 각각의 원소는 하나의 부모(parent)와 0 또는 여러 개의 자식(children) 원소를 갖는다.  
- 정규적인 트리 $T$는 다음의 속성들을 따르며, 원소들이 **부모-자식(parent-child)** 의 관계로 저장되는 노드들의 집합이다.  
    - 만약 비어 있지 않다면, $T$는 $T$의 **루트(root)** 라 불리는 특별한 노드 $r$을 갖는다.  
    (상위(부모) 노드를 갖지 않는다.)  
    - $2$에서 루트가 아닌 각 노드 $v$는 부모 노드 $w$를 갖는다.  
    - 부모 노드 $w$를 갖는 모든 노드는 $w$의 자식(child)이다.  
- 위의 정의에 따르면 트리는 비어 있거나 노드 $r$($T$의 루트)과 $r$의 자식인 트리들의 집합(공집합도 가능)으로 이루어진다.  
- 동일한 부모를 가진 노드를 형제(siblings)라고 한다.  
- 자식이 없는 노드는 **외부(external, leaf)** 노드, 하나 또는 여러 개의 자식을 가진 노드는 **내부(internal)** 노드라고 한다.
- 각 노드의 자식들을 위한 선형 순서(linear ordering)가 정의되어 있다면, 즉, 한 노드의 자식을 첫 번째, 두 번째, 세 번째 등으로 식별할 수 있다면 그 트리는 **순서화(ordered)** 되어 있다고 한다.  
    - 이러한 순서는 트리를 만들 때 원하는 목적에 의해 결정되고, 트리를 그릴 때 일반적으로 그 순서에 따라 형제노드를 왼쪽에서부터 오른쪽으로 배치하여 표시한다.
- 트리를 사용하면 리스트, 벡터, 시퀀스와 같은 선형 자료구조보다 더 빠른 알고리즘의 구현이 가능하다.  

</br>

## ✔️ **1. 이진 트리(Tree)**  
- 이진 트리(Binary Tree)는 모든 노드가 **최대 두 개의 자식 노드** 를 갖는 순서화된 트리이다.  
    - (1) 각 노드가 최대 2개의 자식을 갖는다.  
    - (2) 각 노드의 자식을 왼쪽 자식(left child) 또는 오른쪽 자식(right child)으로 분류한다.  
    - (3) 왼쪽 자식이 오른쪽 자식보다 먼저 오도록 순서화된다.  
- 내부 노드의 왼쪽 또는 오른쪽 자식을 루트로 갖는 서브트리를 왼쪽 서브트리(left subtree) 또는 오른쪽 서브트리(right subtree)라고 한다.  
- 각 노드가 0개 또는 2개의 자식을 가지면 이진트리가 프로퍼(proper)하다고 한다.  
(경우에 따라 그런 트리를 **풀(full) 이진 트리** 라고 한다.)  

</br>

### **1) 이진 트리의 특성**  
- 트리 $T$에서 같은 깊이 $d$를 갖는 모든 노드의 집합을 $T$의 레벨(level) $d$라고 한다.  
- 이진 트리에서 레벨 1은 하나의 노드를 갖고(루트), 레벨 2은 많아야 두 개의 노드, 레벨 $d$는 최대 $2^{d-1}$개의 노드를 갖는다.  
- $T$를 이진 트리라 하고, $n$, $n_E$, $n_I$, 그리고 $h$가 각각 노드의 개수, 외부 노드의 개수, 내부 노드의 개수, 그리고 $T$의 높이를 의미한다고 할 때, $T$는 다음과 같은 속성들을 갖는다.  
    - (1) $h + 1 \leq n \leq 2^{h+1}-1 $  
    - (2) $1 \leq n_E \leq 2^h$
    - (3) $h \leq n_I \leq 2^h - 1$
    - (4) $\log{(n+1)} - 1 \leq h \leq n - 1$

</br>  

### **2) 링크드 구조로 구현한 이진 트리**  
- 코드 구현
    ```cpp
    #include <iostream>
    #include <list>
    using namespace std;

    template<typename T>
    class LinkedBinaryTree {
    protected:
        // 이진 트리의 노드를 구현하는 구조체
        template<typename E>
        struct Node {
            E elem;
            Node<E>* parent;
            Node<E>* left;
            Node<E>* right;
            Node(): elem(), parent(nullptr), left(nullptr), right(nullptr) {}
        };
    public:
        // 이진 트리에서 위치를 구현하는 클래스
        template<typename E>
        class Position {
        private:
            Node<E>* v;  // 노드를 가리키는 포인터
        public:
            Position(Node<E>* _v = nullptr): v(_v) {}
            T& operator*() { return v->elem; }
            Position<E> left() const { return Position<E>(v->left); }
            Position<E> right() const { return Position<E>(v->right); }
            Position<E> parent() const { return Position<E>(v->parent); }
            bool isRoot() const { return v->parent == nullptr; }
            bool isExternal() const { return (v->left == nullptr && v->right == nullptr); }
            friend class LinkedBinaryTree<E>;
        };

        template<typename E>
        using PositionList = std::list<Position<E>>;
    public:
        LinkedBinaryTree();
        int size() const;
        bool empty() const;
        Position<T> root() const;
        PositionList<T> positions() const;                      // 노드의 리스트
        void addRoot();                                         // 빈 트리에 루트를 추가
        void expandExternal(const Position<T>& p);              // 외부 노드를 확장
        Position<T> removeAboveExternal(const Position<T>& p);  // p와 부모를 삭제
        // 유지 보수 함수들은 생략
    protected:                                              
        void preorder(Node<T>* v, PositionList<T>& pl) const;   // 전위 순회 함수
    private:
        Node<T>* _root;                                         // 루트로의 포인터
        int n;                                                  // 노드의 개수
    };

    template<typename T>
    LinkedBinaryTree<T>::LinkedBinaryTree() : _root(nullptr), n(0) {}

    template<typename T>
    int LinkedBinaryTree<T>::size() const {
        return n;
    }

    template<typename T>
    bool LinkedBinaryTree<T>::empty() const {
        return (n == 0);
    }

    template<typename T>
    typename LinkedBinaryTree<T>::template Position<T> LinkedBinaryTree<T>::root() const {
        return Position<T>(_root);
    }

    template<typename T>
    void LinkedBinaryTree<T>::addRoot() {
        _root = new Node<T>;
        n = 1;
    }

    // 두 개의 새로운 외부 노드를 생성하면서 그들을 각각 p의 왼쪽과 오른쪽 자식 노드로 만들어
    // 외부 노드 p를 내부 노드 p로 변환한다. 만약 p가 내부 노드이면 에러를 반환한다.
    template<typename T>
    void LinkedBinaryTree<T>::expandExternal(const Position<T>& p) {
        Node<T>* v = p.v;
        v->left = new Node<T>;
        v->left->parent = v;
        v->right = new Node<T>;
        v->right->parent = v;
        n += 2;
    }

    // 외부 노드 p를 부모 q와 함께 제거하고, q 대신 p의 형제(sibling)으로 대체한다.  
    // 만약 p가 내부 노드이거나 p가 루트이면, 이 연산자는 에러 조건을 생성한다.
    template<typename T>
    LinkedBinaryTree<T>::Position<T> 
    LinkedBinaryTree<T>::removeAboveExternal(const Position<T>& p) {
        Node<T>* n = p.v;
        Node<T>* np = n->parent;
        Node<T>* sib = (n == np->left ? np->right : np->left);
        if (np == _root) {
            _root = sib;
            sib->parent = nullptr;
        } else {
            Node<T>* gpar = np->parent;
            if (np == gpar->left) gpar->left = sib;
            else gpar->right = sib;
            sib->parent = gpar;
        }
        delete n;
        delete np;
        n = np = nullptr;
        n -= 2;
        return Position<T>(sib);
    }

    template<typename T>
    void LinkedBinaryTree<T>::preorder(Node<T>* v, PositionList<T>& pl) const {
        pl.push_back(Position<T>(v));
        if (v->left != nullptr) 
            preorder(v->left, pl);
        if (v->right != nullptr) 
            preorder(v->right, pl);
    }

    template<typename T>
    LinkedBinaryTree<T>::PositionList<T> LinkedBinaryTree<T>::positions() const {
        PositionList<T> pl;
        preorder(_root, pl);
        return pl;
    }
    ```  

</br>

- 테스트  
    ```cpp
    int main() {
        LinkedBinaryTree<int> lbTree;
        lbTree.addRoot();
        lbTree.expandExternal(lbTree.root());
        lbTree.expandExternal(lbTree.root().left());
        lbTree.expandExternal(lbTree.root().right());
        *(lbTree.root()) = 1;
        *(lbTree.root().left()) = 2;
        *(lbTree.root().right()) = 5;
        *(lbTree.root().left().left()) = 3;
        *(lbTree.root().left().right()) = 4;
        *(lbTree.root().right().left()) = 6;
        *(lbTree.root().right().right()) = 7;
        
        LinkedBinaryTree<int>::PositionList<int> positions = lbTree.positions();

        for (auto iter = positions.begin(); iter != positions.end(); iter++) {
            cout << *(*iter) << " ";
        }
        cout << endl;

        return 0;
    }
    ```    
    ```
    1 2 3 4 5 6 7 
    ```  

</br>


