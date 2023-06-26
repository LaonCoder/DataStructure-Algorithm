# ✍️ **1. Pitfalls**  

</br>

### ✔️ **cout 소수점 출력**  
- c++에서 `cout`을 사용하여 원하는 자릿수의 소수점을 출력하고 싶으면 다음과 같이 하면 된다.  
    ```cpp
    #include <iostream>

    using namespace std;
    
    int main() {
        const double kdNumber = 4321.123456789;
        
        cout << kdNumber << endl;

        cout.precision(5); // 5자리로 설정
        cout << kdNumber << endl;

        cout.precision(3);
        cout << kdNumber << endl;
        
        cout << fixed;
        cout.precision(6);
        cout << kdNumber << endl;
        
        cout << scientific;
        cout << kdNumber << endl;

        cout.unsetf(ios::scientific);
        cout << kdNumber << endl;

        cout.setf(ios::fixed);
        cout << kdNumber << endl;

        return 0;
    }
    ```  
    ```
    4321.12
    4321.1
    4.32e+03
    4321.123457
    4.321123e+03
    4321.12
    4321.123457
    ```  

</br>

### ✔️ **begin(), end() 그리고 cin.ignore()**
- `begin()`은 리스트의 시작 주소를, `end()`는 리스트의 끝 주소를 리턴하는데, **마지막 값보다 한 칸 뒤 위치의 값을 리턴한다.**
- `cin`을 사용할 경우, 버퍼에 엔터(`\n`)가 그대로 남아있기 때문에 연이은 `getline()` 함수 사용에 문제를 일으킬 수 있다.  
    - 이를 해결하려면 `cin.ignore()`라는 함수를 사용하면 된다.
    ```cpp
    #include <iostream>
    #include <string>

    using namespace std;

    int main() {
        int n;
        cin >> n;
        cin.ignore();
        for (int i = 0; i < n; i++) {
            string s;
            getline(cin, s);
            cout << *(s.begin()) << *(--(s.end())) << endl;
        }
    }
    ```  

</br>

### ✔️ **배열을 사용하기 전에...**  
- 무조건 초기화를 하는 습관을 들이자.  
    ```cpp
    int a[N][M] = {0};  // 0으로 모든 원소를 초기화
    ```  

</br>

