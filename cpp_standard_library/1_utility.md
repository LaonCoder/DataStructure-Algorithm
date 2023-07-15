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

## ✔️ **3. 페어**  
- `std::pair`를 이용하면 원하는 타입으로 구성된 쌍을 만들 수 있다.  
- `std::pair`는 클래스 템플릿이며, `<utility>`에 정의되어 있고, 복제 생성자와 이동 생성자를 디폴트로 제공한다.  
- `std::swap(pair1, pair2)`로 페어 오브젝트끼리 맞바꿀 수도 있다.  
- 어떤 페어 `p`의 원소를 구하려면 직접 접근하거나 인덱스를 이용한다.  
    - `p.first`, `p.second`, `std::get<0>(p)`, `std::get<1>(p)`와 같이 작성한다.  
- 페어는 비교 연산자인 `==`, `!=`, `<`, `>`, `>=`, `<=`도 지원한다.  

</br>

### **1) std::make_pair**  
- C++은 페어를 생성하기 위한 헬퍼함수인 `std::make_pair`를 제공하는데, 타입을 지정하지 않아도 컴파일러가 자동으로 추론한다.  
    ```cpp
    #include <iostream>
    #include <utility>

    using namespace std;

    template<typename T1, typename T2>
    pair<T2, T1> swapElems(pair<T1, T2> pr) {
        return pair<T2, T1>{pr.second, pr.first};
    }

    int main() {
        pair<const char*, double> charDoub("String1", 3.14);
        pair<const char*, double> charDoub2 = make_pair("String2", 3.14);
        pair<const char*, double> charDoub3 = {"String3", 3.14};
        auto charDoub4 = make_pair("String4", 3.14);

        cout << charDoub.first << ", " << charDoub.second << endl;

        charDoub.first = "SSString";
        cout << get<0>(charDoub) << ", " << get<1>(charDoub) << endl;

        swap(charDoub2, charDoub3);
        cout << charDoub2.first << ", " << charDoub2.second << endl;

        auto charDoub4Swapped = swapElems(charDoub4);
        cout << get<0>(charDoub4Swapped) << ", " << get<1>(charDoub4Swapped) << endl;
    }
    ```  
    ```
    String1, 3.14
    SSString, 3.14
    String3, 3.14
    3.14, String4
    ```  

</br>

## ✔️ **4. 튜플**  
- `std::tuple`을 이용하면 원하는 길이와 타입으로 튜플을 생성할 수 있다. 
-  `<tuple>`을 포함시켜야 사용할 수 있으며, `std::tuple`은 `std::pair`를 일반화한 것이다.  
- 페어와 마찬가지로 복제와 이동 생성자를 디폴트로 가지며, `std::swap` 함수로 튜플끼리 맞바꿀 수 있다.  
- 튜플에서 인덱스가 $i$인 원소는 함수 템플릿 `std::get<i>(t)`로 참조할 수 있다.  
- `std::get<type>(t)`를 사용하면 타입이 `type`인 원소를 직접 참조할 수 있다.  
- 튜플도 비교 연산자인 `==`, `!=`, `<`, `>`, `>=`, `<=`를 지원한다.  
    - 튜플 두 개를 비교할 때는 각 원소를 사전 순서로 비교하며, 비교 연산은 인덱스가 0인 원소부터 시작한다.  

</br> 

### **1) std::make_tuple**  
- C++은 튜플를 생성하기 위한 헬퍼함수인 `std::make_tuple`을 제공하는데, 타입을 지정하지 않아도 자동으로 추론된다.  
    ```cpp
    #include <iostream>
    #include <tuple>
    #include <string>

    using namespace std;

    int main() {
        auto t1 = make_tuple(1, 2, 3);
        tuple<string, int, float> t2 = make_tuple("abc", 1, 3.14f);
        tuple<string, int, float> t3 = {"ghi", 2, 6.28f};

        cout << "tup1 : (" << get<0>(t1) << ", " 
                        << get<1>(t1) << ", " 
                        << get<2>(t1) << ")" << endl;

        get<0>(t2) = "ghi";
        cout << get<string>(t2) << endl;
    }
    ```  

</br>

### **2) std::tie와 std::ignore**  
- `std::tie`는 원소에 대한 레퍼런스로 튜플을 생성한다.  
- `std::ignore`를 이용하면 튜플의 원소를 명시적으로 무시할 수 있다.  
    ```cpp
    #include <iostream>
    #include <tuple>
    #include <string>

    using namespace std;

    int main() {
        int first = 1;
        int second = 2;
        int third = 3;
        int fourth = 4;

        auto tup = tie(first, second, third, fourth) = make_tuple(0, 1002, 1003, 1004);
        first = 1001;
        cout << "tup : (" 
            << get<0>(tup) << ", " 
            << get<1>(tup) << ", " 
            << get<2>(tup) << ", " 
            << get<3>(tup) << ")" << endl;
            
        int a;
        int b;
        
        // 두 번째와 네 번째 인수를 각각 a와 b에 바인딩한다.
        tie(ignore, a, ignore, b) = tup;
        cout << "a: " << a << endl;
        cout << "b: " << b << endl;

        tie(a, b) = make_pair(3001, 3002);
        cout << "a: " << a << endl;
        cout << "b: " << b << endl;
    }
    ```  
    ```
    tup : (1001, 1002, 1003, 1004)
    a: 1002
    b: 1004
    a: 3001
    b: 3002
    ```  

</br>

## ✔️ **5. 레퍼런스 래퍼**  
- 레퍼런스 래퍼는 `type&` 오브젝트에 대한 래퍼로서, 복제 생성 가능하고, 복제 대입 가능하며, `<functional>` 헤더에 정의되어 있다.  
    - 표준 템플릿 라이브러리에 있는 컨터이너에서 사용할 수 있다.  
    (`std::vector<std::reference_wrapper<int>> myIntRefVector`)  
    - `std::reference_wrapper` 오브젝트를 가진 클래스 인스턴스를 복제할 수 있다.  
    (일반 레퍼런스로는 불가능하다.)  
    ```cpp
    #include <functional>
    ...
    void foo() {
        std::cout << "Invoked" << std::endl;
    }

    int main() {
        typedef void CallableUnit();
        reference_wrapper<callableUnit> refWrap(foo);
        refWrap();  // 여기서 호출된다.
    }
    ```  

</br>

### **1) std::ref와 std::cref**  
- 헬퍼 함수인 `std::ref`와 `std::cref`를 이용하면 변수에 대한 레퍼런스 래퍼를 간편하게 만들 수 있다.  
- `std::ref`는 상수가 아닌 레퍼런스 래퍼를 생성하고, `std::cref`는 상수 레퍼런스 래퍼를 생성한다.  
    ```cpp
    #include <iostream>
    #include <functional>
    #include <utility>

    void invokeMe(const std::string& s) {
        std::cout << s << ": const " << std::endl;
    }

    template<typename T>
    void doubleMe(T t) {
        t *= 2;
    }

    int main() {
        std::string s{"string"};
        invokeMe(std::cref(s));

        int i = 1;
        std::cout << "i: " << i << std::endl;

        doubleMe(i);
        std::cout << "doubleMe(i): " << i << std::endl;  

        doubleMe(std::ref(i));
        std::cout << "doubleMe(std::ref(i)): " << i << std::endl;  

        int a{2011};
        auto tup = std::make_pair(a, std::ref(a));
        // (tup.first, tup.second): (2011, 2011);
        std::cout << "(tup.first, tup.second): (" << tup.first << ", " << tup.second << ")" << std::endl;
        
        a = 2014;
        // (tup.first, tup.second): (2011, 2014);
        std::cout << "(tup.first, tup.second): (" << tup.first << ", " << tup.second << ")" << std::endl;
    }
    ```  
    ```
    string: const 
    i: 1
    doubleMe(i): 1
    doubleMe(std::ref(i))2
    (tup.first, tup.second): (2011, 2011)
    (tup.first, tup.second): (2011, 2014)
    ```  

</br>

## ✔️ **6. 스마트 포인터**  
- 스마트 포인터는 RAII 원칙에 따라 리소스를 관리한다.  
    - 따라서 스마트 포인터가 스코프를 벗어나면 자동으로 해당 리소스를 해제한다.  
    - RAII란 '리소스 획득 = 초기화'를 의미하는 'Resource Acquisition is Initialization'의 줄임말이며, C++에서 리소스 획득 및 해제와 오브젝트의 수명이 연동되는 잘 알려진 기법이다.  
    - 결국 스마트 포인터는 생성자에서 메모리를 할당했다가 소멸자에서 해제한다는 것을 의미한다.  
    - C++에서 이 기법을 적용하면 오브젝트가 스코프를 벗어날 때 소멸자가 호출된다.  
    
</br>

### **1) std::unique_ptr** 
- `std::unique_ptr`는 리소스를 독점적으로 관리한다.  
- 스코프를 벗어나면 리소스를 자동으로 해제하고, 복제 의미론이 필요 없다면 표준 템플릿 라이브러리의 컨테이너나 알고리즘에서 사용할 수 있다.  
- `std::unique_ptr`는 특수한 삭제자(deleter)를 사용하지 않는다면 기본 포인터만큼 빠르고 오버헤드도 적다.  
- `std::unique_ptr`에서 제공하는 메서드는 다음과 같다.  
    |이름|설명|
    |---|----|
    |get|리소스에 대한 포인터를 리턴한다.|
    |get_deleter|리소스에 대한 포인터를 리턴하고 그 리소스를 해제한다.|
    |release|리소스에 대한 포인터를 리턴하고, 그 리소스를 해제한다.|
    |reset|리소스를 리셋한다.|
    |swap|리소스를 맞바꾼다.|  
    ```cpp
    #include <iostream>
    #include <memory>

    using namespace std;

    struct MyInt {
        MyInt(int i): i_(i) { cout << "Hello " << i_ << endl; }
        ~MyInt() {
            cout << "Good by from " << i_ << endl;
        }
        int i_;
    };

    int main() {
        std::unique_ptr<MyInt> uniquePtr1{ new MyInt(1998) };                // Hello 1998
        std::cout << "uniquePtr1.get(): " << uniquePtr1.get() << std::endl;  // uniquePtr1.get(): 0x786ce0

        std::unique_ptr<MyInt> uniquePtr2{ std::move(uniquePtr1) };          
        std::cout << "uniquePtr1.get(): " << uniquePtr1.get() << std::endl;  // uniquePtr1.get(): 0
        std::cout << "uniquePtr2.get(): " << uniquePtr2.get() << std::endl;  // uniquePtr2.get(): 0x786ce0

        std::unique_ptr<MyInt> localPtr{ new MyInt(2023) };                  // Hello 2023

        uniquePtr2.reset( new MyInt(2011) );                                 // Hello 2011

        MyInt* myInt = uniquePtr2.release();                                 // Good by from 1998
        delete myInt;                                                        // Good by from 2011

        std::unique_ptr<MyInt> uniquePtr3{ new MyInt(2017) };                // Hello 2017
        std::unique_ptr<MyInt> uniquePtr4{ new MyInt(2022) };                // Hello 2022

        std::cout << "uniquePtr3.get(): " << uniquePtr3.get() << std::endl;  // uniquePtr3.get(): 0xeb6ce0
        std::cout << "uniquePtr3.get(): " << uniquePtr4.get() << std::endl;  // uniquePtr3.get(): 0xeb6f40

        swap(uniquePtr3, uniquePtr4);

        std::cout << "uniquePtr3.get(): " << uniquePtr3.get() << std::endl;  // uniquePtr3.get(): 0xeb6f40
        std::cout << "uniquePtr3.get(): " << uniquePtr4.get() << std::endl;  // uniquePtr3.get(): 0xeb6ce0
    }  

    // Good by from 2017
    // Good by from 2022
    // Good by from 2023
    ```  

</br>  



