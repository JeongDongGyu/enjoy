# 아이디어 회의

to-do list
할 일 목록 입력, 수정, 삭제

### **📝 Git 기반 CLI To-Do 리스트 프로젝트 명세서**

#### **1. 프로젝트 개요**

  - **프로젝트 명**: TodoApp
  - **목표**: 명령줄 인터페이스(CLI)를 통해 할 일을 추가, 조회, 완료, 삭제할 수 있는 자바 애플리케이션 개발.
  - **핵심 특징**:
      - 모든 데이터는 하나의 텍스트 파일(`tasks.txt`)에 저장.
      - GUI 없이 터미널 환경에서 실행.
      - 팀원 간의 데이터 동기화는 Git (`pull`, `push`)을 통해 수동으로 진행.

-----

#### **2. 핵심 클래스 설계**

프로그램은 총 4개의 핵심 클래스로 구성됩니다.

##### **2.1. `Task` 클래스 (데이터 모델)** ⚙️

  - **설명**: 하나의 '할 일' 정보를 담는 객체입니다. 데이터의 가장 기본 단위입니다.
  - **속성 (Attributes)**:
      - `description`: 할 일의 내용을 저장 (타입: `String`)
      - `isCompleted`: 할 일의 완료 여부를 저장 (타입: `boolean`)
  - **메소드 (Methods)**:
      - `public Task(String description, boolean isCompleted)`: Task 객체를 생성하는 생성자.
      - `public String getDescription()`: 할 일 내용을 반환.
      - `public boolean isCompleted()`: 완료 여부를 반환.
      - `public void setCompleted(boolean status)`: 완료 상태를 변경.
      - `public String toFileString()`: 객체 정보를 파일에 저장할 형태의 문자열로 변환하여 반환 (예: `"[X] 자바 공부하기"`).
      - `public String toString()`: 화면에 출력할 형태의 사용자 친화적인 문자열로 변환하여 반환.

##### **2.2. `TaskManager` 클래스 (비즈니스 로직)** 🧠

  - **설명**: `Task` 객체 목록을 메모리 상에서 관리합니다. 할 일을 추가, 수정, 삭제하는 등 실질적인 프로그램의 로직을 담당합니다.
  - **속성 (Attributes)**:
      - `tasks`: `Task` 객체들을 담아두는 리스트 (타입: `List<Task>`)
  - **메소드 (Methods)**:
      - `public TaskManager(List<Task> tasks)`: 파일에서 읽어온 `Task` 리스트로 초기화하는 생성자.
      - `public void addTask(String description)`: 새로운 할 일을 `tasks` 리스트에 추가.
      - `public void completeTask(int taskNumber)`: 지정된 번호(1부터 시작)의 할 일을 완료 상태로 변경.
      - `public void deleteTask(int taskNumber)`: 지정된 번호의 할 일을 리스트에서 삭제.
      - `public List<Task> getTasks()`: 현재 관리 중인 모든 `Task` 리스트를 반환 (파일 저장 시 필요).
      - `public void listTasks()`: 모든 할 일 목록을 번호와 함께 콘솔에 출력.

##### **2.3. `FileManager` 클래스 (데이터 입출력)** 💾

  - **설명**: `tasks.txt` 파일을 읽고 쓰는 모든 작업을 전담합니다. 데이터 영속성을 담당하는 클래스입니다.
  - **속성 (Attributes)**:
      - `fileName`: 데이터를 저장할 파일의 이름 (타입: `String`)
  - **메소드 (Methods)**:
      - `public FileManager(String fileName)`: 파일 이름을 받아 초기화하는 생성자.
      - `public List<Task> loadTasks()`: `tasks.txt` 파일의 모든 줄을 읽어 `List<Task>` 객체로 변환하여 반환.
      - `public void saveTasks(List<Task> tasks)`: `TaskManager`로부터 `List<Task>`를 받아 파일(`tasks.txt`)에 덮어쓰기.

##### **2.4. `TodoApp` 클래스 (메인 실행)** 🚀

  - **설명**: 프로그램의 시작점(Entry Point)입니다. 사용자의 명령줄 입력을 받아 분석하고, 다른 클래스의 메소드를 호출하여 프로그램을 동작시킵니다.
  - **속성 (Attributes)**: 없음 (static 메소드만 포함)
  - **메소드 (Methods)**:
      - `public static void main(String[] args)`:
        1.  `FileManager`를 생성하여 `loadTasks()`로 기존 할 일을 불러온다.
        2.  불러온 `List<Task>`로 `TaskManager`를 생성한다.
        3.  `args` 배열을 분석하여 사용자의 명령어(`add`, `list`, `done` 등)를 파악한다.
        4.  명령어에 따라 `TaskManager`의 적절한 메소드를 호출한다.
        5.  작업(추가, 완료, 삭제)이 끝난 후, `TaskManager`에서 최신 `Task` 리스트를 받아 `FileManager`의 `saveTasks()`를 호출하여 파일에 저장한다.

-----

#### **3. 데이터 저장 형식 (Data Format)**

`tasks.txt` 파일에 데이터를 저장하는 규칙입니다. `FileManager`가 이 규칙에 따라 파일을 읽고 씁니다.

  - **미완료된 할 일**: `[ ] 할 일 내용`
  - **완료된 할 일**: `[X] 할 일 내용`

**예시 `tasks.txt` 파일 내용:**

```
[X] GitHub 레포지토리 생성
[ ] Task 클래스 설계
[ ] FileManager 구현
```

-----

#### **4. 역할 분담 제안**



  - **A 개발자 (데이터 및 파일 담당)**:
      - `Task` 클래스 구현.
      - `FileManager` 클래스 구현.
      - 데이터 저장 형식 확정.
  - **B 개발자 (애플리케이션 로직 담당)**:
      - `TaskManager` 클래스 구현.
      - `TodoApp` 클래스의 `main` 메소드 구현 (명령어 처리 로직).
      - A 개발자가 만든 클래스와 자신의 클래스를 통합하여 프로그램 완성.
   

---

### **🚀 4인 팀 초단기 (하루 완성) TodoApp 실행 계획**

#### **Phase 1: - 핵심 기능 동시 개발**
* **목표**: 각자 맡은 클래스의 1차 구현을 독립적으로 완료. 다른 사람의 코드를 기다리지 않습니다.

* **(10분) 킥오프 미팅**
    * **[전원]**: 계획을 5분 내로 최종 확인하고, 조장이 공유한 GitHub Repository를 클론(Clone)합니다.

* **(1시간 20분) 병렬 코딩 시작!**
    * **[개발자 1: Model/Data]**
        * **담당**: `Task` 클래스, `FileManager` 클래스
        * **임무**: `Task` 클래스를 가장 먼저 정의하고, `FileManager`가 `tasks.txt` 파일을 읽고 쓰는 핵심 기능을 구현합니다. **다른 클래스와의 연결은 신경 쓰지 않고, 오직 파일 입출력 기능 자체에만 집중합니다.**
        * **브랜치**: `feature/data`

    * **[개발자 2: Business Logic]**
        * **담당**: `TaskManager` 클래스
        * **임무**: `Task` 클래스가 아직 없다고 가정하고, **자신의 파일에 임시 `Task` 클래스를 만들어서라도** `TaskManager`의 추가, 삭제, 완료처리 로직을 구현합니다. 가짜 데이터(`List<Task>`)를 직접 만들어 테스트합니다.
        * **브랜치**: `feature/logic`

    * **[개발자 3: Application/UI]**
        * **담당**: `TodoApp` (main 메소드)
        * **임무**: 사용자의 명령줄(`args`)을 분석하는 코드에만 집중합니다. `switch` 문을 사용해 `add`, `list` 등의 명령을 구분하고, "add 명령 감지!" 와 같이 **단순 출력만 하도록 구현합니다.**
        * **브랜치**: `feature/app`

    * **[조장: 통합 준비 및 지원]**
        * **담당**: `README.md` 기본 작성, 팀원 질문 응답, 코드 통합(Merge) 준비
        * **임무**: 팀원들의 브랜치 진행 상황을 수시로 확인하며, 곧 있을 통합 과정의 순서와 계획을 미리 구상합니다. `README.md`에 프로젝트 개요와 사용법 목차를 미리 작성합니다.

---

#### **Phase 2: 통합, 디버깅, 테스트**
* **목표**: 분리된 코드들을 합치고, 실제 프로그램이 동작하도록 만듭니다. 이 단계에서는 **전원이 화면을 공유하며 페어 프로그래밍 방식**으로 진행하는 것이 효율적입니다.

* **(30분) 1차 통합 (Merge)**
    * **[조장 주도, 전원 참여]**: 조장이 화면을 공유하며 `main` 브랜치에 코드를 병합합니다.
        1.  `feature/data` 병합 (가장 기반이 되는 코드)
        2.  `feature/logic` 병합 (개발자 2가 만든 임시 `Task` 클래스는 삭제하고, 개발자 1의 `Task`를 사용하도록 수정)
        3.  `feature/app` 병합

* **(1시간 30분) 연결 및 합동 디버깅**
    * **[전원]**: 통합된 `main` 브랜치를 모두 `pull` 받습니다.
    * `TodoApp` 클래스에서 개발자 3이 만들었던 단순 출력문을 실제 `TaskManager` 메소드 호출로 변경합니다.
    * `FileManager`와 `TaskManager` 객체를 `main` 메소드 내에서 생성하고 연결합니다.
    * **다 함께 프로그램을 실행하며 발생하는 오류를 실시간으로 잡습니다.** (예: `add`는 되는데 `list`가 안 되는 문제, 파일이 저장되지 않는 문제 등)

---

#### **Phase 3: 최종 마무리**
* **목표**: 완성된 프로그램의 완성도를 높이고, 문서를 작성합니다.

* **(30분) 최종 테스트**
    * **[전원]**: 각자 돌아가면서 명령어를 실행해보며 버그가 없는지 최종 확인합니다. (예: 없는 할 일 삭제 시도, 할 일이 없을 때 목록 조회 등 예외 상황 테스트)

* **(45분) 문서화 및 코드 정리**
    * **[조장 + 개발자 3]**: `README.md`에 최종 사용법(실행 방법, 명령어 목록)을 완성합니다.
    * **[개발자 1 + 개발자 2]**: 전체 코드에 필요한 주석을 추가하고, 코드 포맷팅(IntelliJ 단축키 활용)으로 가독성을 높입니다.

* **(15분) 최종 커밋 및 프로젝트 완료**
    * **[조장]**: 완성된 프로젝트를 최종적으로 `main` 브랜치에 커밋하고 Push합니다.
    * **[전원]**: 짧게 프로젝트 소감을 나누며 마무리합니다.


