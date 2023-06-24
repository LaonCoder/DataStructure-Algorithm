# ✍️ **1. 유틸리티(Utility)**  
- 유틸리티(utility)란 특정 영역에 국한되지 않고 다양한 용도로 활용할 수 있는 도구를 말한다. 

</br>

## ✔️ **1. 유용한 함수들**  
### **1) std::min, std::max, std::minmax**
- `<algorithm>`에 정의된 `std::min`, `std::max`, `std::minmax` 함수는 값 또는 이니셜라이저(Initializer list)를 입력받아서 결과를 리턴한다.  
- `std::minmax` 함수는 결괏값을 `std::pair`로 리턴한다.  
    - 페어의 첫 번째 원소가 최솟값이고, 두 번째 원소가 최댓값이다.
- 이 함수에 적용되는 디폴트 연산자는 `<`이지만, 다른 비교 연산자를 지정할 수도 있다.
- 이 함수는 인수 두 개를 받고, 불 타입 값 하나를 리턴하는데, 이처럼 `true`나 `false`를 리턴하는 함수를 프레디케이트(predicate)라고 한다.  
    ```cpp
    #include <iostream>
    #include <algorithm>
    #include <string>

    using namespace std;

    int main(void) {
        string first{"first"};
        string second{"second"};

        cout << min(2022, 2023) << endl;
        cout << min(first, second) << endl;
        cout << min({3, 1, 2011, 2019, -5}) << endl;
        cout << min(-10, -5, [](int a, int b){ return abs(a) < abs(b); }) << endl;
        
        cout << endl;

        cout << max(2022, 2023) << endl;
        cout << max(first, second) << endl;
        cout << max({3, 1, 2011, 2019, -5}) << endl;
        cout << max(-10, -5, [](int a, int b){ return abs(a) < abs(b); }) << endl;

        cout << endl;  

        auto pairInt = minmax(2022, 2023);
        auto pairStr = minmax(first, second);
        auto pairSeq = minmax({3, 1, 2011, 2019, -5});
        auto pairAbs = minmax({3, 1, 2011, 2019, -5}, [](int a, int b){ return abs(a) < abs(b); });
        cout << "Min : " << pairInt.first << " / Max : " << pairInt.second << endl;
        cout << "Min : " << pairStr.first << " / Max : " << pairStr.second << endl;
        cout << "Min : " << pairSeq.first << " / Max : " << pairSeq.second << endl;
        cout << "Min : " << pairAbs.first << " / Max : " << pairAbs.second << endl;
    }
    ```  
    ```
    2022
    first
    -5
    -5

    2023
    second
    2019
    -10

    Min : 2022 / Max : 2023
    Min : first / Max : second
    Min : -5 / Max : 2019
    Min : 1 / Max : 2019
    ```  

</br>

### **2) std::move**
- `<utility>`에 정의된 `std::move`는 컴파일러가 리소스를 이동할 수 있게 해주는 강력한 기능이다.  
- 즉, 소스 오브젝트에 있는 값을 새로운 오브젝트로 옮기는 이동 의미론(move semantics)를 제공한다.  
- `std::move`를 사용하면 컴파일러는 소스(`arg`)를 우측값 참조(rvalue reference)로 변환한다.  
(`static_cast<std::remove_reference<decltype(arg)>::type&&>(arg)`)  
- ※ 이동이 복제보다 가볍다. (메모리를 할당했다가 해제할 필요 X)  
    ```cpp
    #include <iostream>
    #include <vector>
    #include <utility>

    using namespace std;

    int main(void) {
        std::vector<int> myBigVec(1000000, 2011);
        std::vector<int> myVec1, myVec2;

        myVec1 = myBigVec;             // 복제 의미론
        myVec2 = std::move(myBigVec);  // 이동 의미론

        cout << myVec1.front() << endl;
        cout << myVec2.front() << endl;
        // myBigVec.front() -> error! (moved)

        return EXIT_SUCCESS;
    }
    ```  

</br>

### **3) std::forward**  
- `<utility>`에 정의된 `std::forward` 함수를 이용하면 주어진 인수를 그대로 전달하는 함수 템플릿을 작성할 수 있다.  
- `std::forward`는 팩토리 함수나 생성자를 만드는 데 주로 활용한다.  
    - 팩토리 함수는 오브젝트를 생성하는 함수로서 주어진 인수를 그대로 전달한다.  
    - 생성자는 대체로 베이스(기본) 클래스의 필드 값을 초기화하는 데 주로 사용한다.
- 그래서 `std::forward`는 제네릭 라이브러리를 제작하는 데 적합하다.   
    ```cpp
    #include <iostream>
    #include <vector>
    #include <utility>

    using namespace std;

    struct MyData {
        MyData(){};
        MyData(int , double, char) {};
    };

    template<typename T, typename ...Args>
    T createT(Args&& ...args) {
        return T(std::forward<Args>(args)...);
    }

    int main() {
        int a = createT<int>();
        int b = createT<int>(1);

        string s = createT<string>("Only for testing purpose.");
        
        MyData myData = createT<MyData>();
        MyData myData2 = createT<MyData>(1, 3.14, 'a');

        typedef std::vector<int> IntVec;
        IntVec intVec = createT<IntVec>(std::initializer_list<int>({1, 2, 3, 4, 5}));

        return EXIT_SUCCESS;
    }
    ```  

</br>

### **4) std::swap**  
- `utility`에 정의된 `std::swap` 함수를 이용하면 두 오브젝트를 맞바꾸는 작업을 쉽게 처리할 수 있다.  
- C++ 표준 라이브러리에서는 제네릭을 구현하는 데 내부적으로 `std::move` 함수를 활용한다.
    ```cpp
    #include <iostream>
    #include <vector>
    #include <utility>

    using namespace std;

    template<typename T>
    void swap(T& a, T& b) {
        T tmp(std::move(a));
        a = std::move(b);
        b = std::move(tmp);
    }

    int main() {
        string a = "hello";
        string b = "teria";

        swap(a, b);
        
        cout << a << " " << b << endl;

        return EXIT_SUCCESS;
    }
    ```  

</br>

## ✔️ **2. 함수 어댑터**  
- `std::bind`는 새로운 함수 오브젝트를 동적으로 생성하고, `std::function`은 이렇게 생성된 임시 함수 오브젝트를 받아서 변수에 바인딩한다.  
- 두 함수 모두가 함수형 프로그래밍에서 도입한 강력한 도구이며, `<functional>` 헤더에 정의되어 있다.  
    ```cpp
    #include <iostream>
    #include <vector>
    #include <functional>

    using namespace std;
    using namespace std::placeholders;  // 자리 표시자(placeholder)

    double divMe(double a, double b) {
        return double(a/b);
    }

    int main() {
        cout << "1/2.0 = " <<  bind(divMe, 1, 2.0)() << endl;
        
        function<double(double, double)> myDivBindPlaceholder = std::bind(divMe, _1, _2);
        cout << "1/2.0 = " << myDivBindPlaceholder(1, 2.0) << endl;

        // argument swap
        function<double(double, double)> myDivBindPlaceholderSwap = std::bind(divMe, _2, _1);
        cout << "1/2.0 = " << myDivBindPlaceholderSwap(2.0, 1.0) << endl;

        function<double(double)> myDivBind1St = std::bind(divMe, _1, 2.0);
        cout << "1/2.0 = " << myDivBind1St(1.0) << endl;
        return EXIT_SUCCESS;
    }
    ```  
    ```
    1/2.0 = 0.5
    1/2.0 = 0.5
    1/2.0 = 0.5
    1/2.0 = 0.5
    ```

</br>  



### **1) std::bind**  
- `std::bind`를 사용하면 함수 오브젝트를 다음과 같이 다양한 방식으로 생성할 수 있다.  
    - 인수를 원하는 위치에 바인딩할 수 있다.
    - 인수의 순서를 바꿀 수 있다.
    - 인수에 자리표시자(placeholders)를 적용할 수 있다.  
    - 함수의 일부분만 평가할 수 있다.  
    - 새로 생성된 함수 오브젝트를 호출하고 STL 알고리즘에서 활용하거나 `std::function`에 저장할 수 있다.  

</br>

### **2) std::function**
- `std::function`은 임의의 Callable을 변수에 저장할 수 있으며, 일종의 다형 함수 래퍼(Polymorphic Function Wrapper)라고 볼 수 있다.  
- 이때의 Callable은 람다 함수일 수도 있고, 함수 오브젝트나 함수일 수도 있다.  
- 콜러블의 타입을 구체적으로 지정해야 한다면 `std::function`을 반드시 써야하고, `auto`로 교체할 수 없다.  
    ```cpp
    #include <iostream>
    #include <functional>
    #include <map>
    #include <utility>

    using namespace std;

    int main() {
        map<const char, std::function<double(double, double)>> dispTable;
        dispTable.insert(make_pair('+', [](double a, double b){ return a + b; }));
        dispTable.insert(make_pair('-', [](double a, double b){ return a - b; }));
        dispTable.insert(make_pair('*', [](double a, double b){ return a * b; }));
        dispTable.insert(make_pair('/', [](double a, double b){ return a / b; }));

        cout << "3.5 + 4.5 = " << dispTable['+'](3.5, 4.5) << endl;
        cout << "3.5 - 4.5 = " << dispTable['-'](3.5, 4.5) << endl;
        cout << "3.5 * 4.5 = " << dispTable['*'](3.5, 4.5) << endl;
        cout << "3.5 / 4.5 = " << dispTable['/'](3.5, 4.5) << endl;
        return EXIT_SUCCESS;
    }
    ```  

</br>