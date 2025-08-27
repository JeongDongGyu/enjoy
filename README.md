# 아이디어 회의

to-do list
할 일 목록 입력, 수정, 삭제

네, 그럼요. 말씀하신 기준에 맞춰 Git 기반 CLI To-Do 리스트의 구체적인 프로젝트 명세서를 작성해 드리겠습니다. 이 계획서를 바탕으로 팀원들과 회의하면 각자 무엇을 개발해야 할지 명확하게 파악하고 역할을 분담할 수 있을 겁니다.

-----

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

이 명세서를 기반으로 다음과 같이 역할을 나눌 수 있습니다.

  - **A 개발자 (데이터 및 파일 담당)**:
      - `Task` 클래스 구현.
      - `FileManager` 클래스 구현.
      - 데이터 저장 형식 확정.
  - **B 개발자 (애플리케이션 로직 담당)**:
      - `TaskManager` 클래스 구현.
      - `TodoApp` 클래스의 `main` 메소드 구현 (명령어 처리 로직).
      - A 개발자가 만든 클래스와 자신의 클래스를 통합하여 프로그램 완성.
