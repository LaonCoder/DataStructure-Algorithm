# ✍️ **2. Recursion**  

</br>

## ✔️ **1. 재귀 기본**  
- 재귀는 함수가 자신의 정의 내에서 자기 자신을 호출하는 것을 의미한다.

</br>

### **1. 팩토리얼**
- 일반적인 재귀
    ```cpp
    int fact(int n) {
        if (n == 0) 
            return 1;
        else 
            return n * fact(n - 1);
    }
    ```  
    - 함수를 호출할 때 함수의 입력 값(매개변수), 리턴 값, 리턴 시 복귀 위치 값 등이 스택에 저장된다.
    - 재귀함수를 사용하면 호출될 때마다 이러한 스택 공간을 사용하므로, 무리하게 호출 시 스택 오버플로우가 발생할 수 있다.  

</br>  

- 꼬리 재귀  
    ```cpp
    int tailFact(int n, int acc = 1) {
        if (n == 0) return acc;
        else return tailFact(n - 1, acc * n);
    }

    // int tailFact(int n) {
    //     function<int(int, int)> _aux_fact = [&](int n, int acc) {
    //         if (n == 0)
    //             return acc;
    //         else
    //             return _aux_fact(n - 1, acc * n); 
    //     };
    //     return _aux_fact(n, 1);
    // }
    ```  
    - **꼬리 재귀**는, 재귀 호출이 끝난 후 함수에서 추가 연산을 요구하지 않도록 하는 재귀의 형태이다.  
    - 이를 이용하면 함수 호출이 반복되어 스택이 깊어지는 문제를 컴파일러가 선형으로 처리하도록 알고리즘을 바꿔 스택을 재사용할 수 있다.  
    (더이상 값이 변할 여지가 없으므로 스택을 덮어쓸 수 있기 때문)  
    - 꼬리재귀를 사용하기 위해서는 컴파일러에서 이런 최적화 기능을 지원하는지 반드시 확인이 필요하다.   

</br>

- 테스트  
    ```cpp
    int main() {
        cout << fact(10) << endl;
        cout << tailFact(10) << endl;

        return EXIT_SUCCESS;
    }
    ```  
    ```
    3628800
    3628800
    ```  

</br>

### **2. 영국식 자 그리기**  
- 영국식 자는 간격들로 구분되고, 각 간격은 1/2 인치, 1/4 인치 등의 간격으로 놓인 표식(tick)의 집합으로 구성된다.  
- 간격의 크기가 절반으로 줄어들면서 표식의 길이가 한 개씩 줄어든다.  
- 인치의 각 부분은 또한 숫자 레이블을 갖는다.  
- 가장 긴 표식의 길이는 주요 표식 길이(major tick length)라고 한다.  

</br>  

- 코드 구현  
    ```cpp
    #include <iostream>
    #include <string>
    #include <functional>

    using namespace std;

    void drawOneTick(int tickLength, int tickLabel = -1) {
        for (int i = 0; i < tickLength; i++)
            cout << "-";
        if (tickLabel >= 0) 
            cout << tickLabel;
        cout << endl;
    }  

    void drawTicks(int tickLength) {
        if (tickLength > 0) {
            drawTicks(tickLength - 1);
            drawOneTick(tickLength);
            drawTicks(tickLength - 1);
        }
    }

    void drawRuler(int nInches, int majorLength) {
        drawOneTick(majorLength, 0);
        for (int i = 1; i < nInches; i++) {
            drawTicks(majorLength - 1);
            drawOneTick(majorLength, i);
        }
    }
    ```  

</br>

- 테스트  
    ```cpp  
    int main(void) {
        drawRuler(5, 4);
        return EXIT_SUCCESS;
    }
    ```  
    ```
    ----0
    -
    --
    -
    ---
    -
    --
    -
    ----1
    -
    --
    -
    ---
    -
    --
    -
    ----2
    -
    --
    -
    ---
    -
    --
    -
    ----3
    -
    --
    -
    ---
    -
    --
    -
    ----4
    ```  

</br> 

## ✔️ **2. 선형 재귀**  
- 호출이 발생할 때마다 최대로 하나의 재귀 호출이 이루어진다.  

</br>

### **1. 재귀적으로 배열 원소 합 구하기**  
- 코드 구현  
    ```cpp  
    #include <iostream>

    using namespace std;

    int linearSum(int arr[], int n) {
        if (n == 1) 
            return arr[n - 1];
        else 
            return linearSum(arr, n - 1) + arr[n - 1];
    }
    ```  

</br>  

- 테스트  
    ```cpp  
    int main(void) {
        int arr[] = {1,2,3,4,5};
        cout << linearSum(arr, 5) << endl;
    }
    ```  
    ```
    15
    ```  

</br>

### **2. 재귀에 의한 배열 반전**  
- 코드 구현  
    ```cpp
    #include <iostream>

    using namespace std;

    void reverseArray(int arr[], int i, int j) {
        if (i < j) {
            // swap
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            return reverseArray(arr, i + 1, j - 1);   
        }
    }
    ```  

</br>

- 테스트  
    ```cpp
    int main(void) {
        int arr[] = {1,2,3,4,5};

        reverseArray(arr, 0, 4);
        
        for (int i = 0; i < 5; i++) {
            cout << arr[i] << " ";
        }
        cout << endl;
    }
    ```  
    ```
    5 4 3 2 1 
    ```

</br>

## ✔️ **3. 이진 재귀**  
- 알고리즘이 2개의 재귀 호출을 만드는 경우  

</br>

### **1. 이진 재귀를 사용한 배열 원소들의 합**  
- 코드 구현  
    ```cpp
    #include <iostream>

    using namespace std;

    int binarySum(int arr[], int s, int e) {
        if (s == e) return arr[s];
        else {
            int mid = (s + e) / 2;
            return binarySum(arr, s, mid) + binarySum(arr, mid + 1, e);
        }
    }
    ```  

</br>

- 테스트  
    ```cpp
    int main(void) {
        int arr[] = {1,2,3,4,5,6,7,8,9,10};
        cout << binarySum(arr, 0, 9) << endl;
        return EXIT_SUCCESS;
    }
    ```  
    ```
    55
    ```  

</br>

### **2. 이진 재귀를 사용한 배열 원소들의 합**  
- 코드 구현  
    ```cpp
    #include <iostream>

    using namespace std;

    int binarySum(int arr[], int s, int e) {
        if (s == e) return arr[s];
        else {
            int mid = (s + e) / 2;
            return binarySum(arr, s, mid) + binarySum(arr, mid + 1, e);
        }
    }
    ```  

</br>

- 테스트  
    ```cpp
    int main(void) {
        int arr[] = {1,2,3,4,5,6,7,8,9,10};
        cout << binarySum(arr, 0, 9) << endl;
        return EXIT_SUCCESS;
    }
    ```  
    ```
    55
    ```  

### **3. 이진 재귀를 이용한 피보나치 수열 계산**  
- 코드 구현  
    ```cpp
    #include <iostream>
    #include <functional>
    #include <map>

    using namespace std;

    // 이진 재귀를 이용한 피보나치 수열 계산
    int fib1(int n) {
        if (n <= 1) return n;
        return fib1(n - 1) + fib1(n - 2);
    }

    // 선형 재귀를 이용한 피보나치 수열 계산
    int fib2(int n) {
        function<int(int, int, int)> _fib_aux;
        _fib_aux = [&](int curr, int prev, int i) {
            if (i == n) { return curr + prev; }
            else { return _fib_aux(curr + prev, curr, i+1); }
        };
        if (n <= 1) return n;
        else { return _fib_aux(1, 0, 2); }
    }

    // 메모이제이션을 이용한 피보나치 수열 계산
    int fib3(int n) {
        static map<int, int> idxVal;
        if (idxVal.count(n) > 0) {
            return idxVal[n];
        } else {
            if (n <= 1) {
                return n;
            } else {
                int res = fib3(n - 1) + fib3(n - 2);
                idxVal[n] = res;
                return res;
            }
        }
    }

    // n개의 연속된 피보나치 수를 출력
    void printFib(function<int(int)> fibFunc, int n) {
        for (int i = 0; i < n; i++) {
            cout << fibFunc(i) << " ";
        }
        cout << endl;
    }
    ```  

</br>  

- 테스트  
    ```cpp
    int main(void) {
        printFib(fib1, 10);
        printFib(fib2, 10);
        printFib(fib3, 10);
        return EXIT_SUCCESS;
    }
    ```  
    ```
    0 1 1 2 3 5 8 13 21 34 
    0 1 1 2 3 5 8 13 21 34
    0 1 1 2 3 5 8 13 21 34
    ```  