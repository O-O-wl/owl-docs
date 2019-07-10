# Xcode Debug

---

### LLDB : 디버거

- ### 실행 제어

  - **Continue**

  - ```swift
    (lldb) continue
    (lldb) c
    "계속해서 진행, 정지된 프로그램 진행"
    ```

  - **Step Over **

  - ```swift
    (lldb) next
    (lldb) n
    "다음 줄 로 진행 ,현재 Frame 에서 다음 단계로- F6"
    ```

  - **Step In**

  - ```swift
    (lldb) step
    (lldb) s
    "현재 Method 내부로 이동 - F7"
    ```

  - **Step Out**

  - ```swift
    (lldb) finish
    (lldb) fin
    "현재 커서 Method 에서 탈출 - F8"
    ```

- ### 변수 검사

  - **전역 변수 검사**

  - ```swift
    (lldb) target variable [변수명]
    (lldb) ta v [변수명]
    "특정 변수 출력하기"
    (lldb) target variable
    (lldb) ta v
    "static/global 전역변수 출력"
    ```

  - **로컬 변수 검사**

  - ```swift
    (lldb) frame variable [변수명]
    (lldb) fr v [변수명]
    "특정 지역 변수 출력"
    (lldb) frame variable
    (lldb) fr v 
    "특정 지역의 모든 변수 출력"
    ```

- ### 스레드 

  - **back trace**

  - ```swift
    (lldb) thread backtrace
    (lldb) bt
    "현재 Thread 의 stack frame 출력"
    
    (lldb) frame info
    (lldb) fr info
    "현재 스레드에서 현재 선택된 frame의 정보 출력하기"
    ```

- ### 표현식

  - **변수 선언**

  - ```swift
    (lldb) expr var $abc = 0
    (lldb) expr print($abc)
    0
    (lldb) expr $abc += 1
    (lldb) expr print($abc)
    1
    ```

  - **출력하기**

  - ```swift
    (lldb) p [변수명]
    "원시 값 출력"
    (lldb) po [변수명]
    "Print Object / description 출력"
    ```

  - **단축**

  - ```swift
    (lldb) command alias [단축어] [명령어]
    ```

    