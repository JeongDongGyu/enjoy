4개는 부담스럽고 1개는 너무 뭉쳐있다고 느낄 때 사용하는 가장 이상적인 절충안은 2개의 파일로 구성하는 것입니다.

핵심 로직을 담당하는 '엔진' 파트와, 사용자 입력을 처리하는 '조종기' 파트로 나누는 방식입니다.

TaskManager.java (엔진 ⚙️): 할 일 데이터(Task)를 자기 안에 품고, 목록 관리와 파일 저장/불러오기까지 모든 실질적인 작업을 책임집니다.

TodoApp.java (조종기 🕹️): 사용자 명령어만 해석해서 TaskManager에게 전달하는 역할만 수행합니다.

## 1. TaskManager.java (엔진 파일)
이 파일이 기존의 Task, FileManager, TaskManager의 역할을 모두 흡수합니다.

Java

// TaskManager.java
import java.io.*;
import java.util.ArrayList;
import java.util.List;

public class TaskManager {

    // Task의 역할을 하는 private record를 내부에 품습니다.
    private record Task(String description, boolean isCompleted) {
        public String toFileString() {
            return (isCompleted ? "[X] " : "[ ] ") + description;
        }
    }

    private final String fileName;
    private final List<Task> tasks;

    // 생성될 때 파일로부터 데이터를 불러와 준비합니다.
    public TaskManager(String fileName) {
        this.fileName = fileName;
        this.tasks = loadTasks();
    }

    // --- 핵심 기능들 ---
    public void addTask(String description) {
        tasks.add(new Task(description, false));
        System.out.println("'" + description + "' 할 일이 추가되었습니다.");
        saveTasks(); // 변경 후 바로 저장
    }

    public void listTasks() {
        System.out.println("[To-Do List]");
        if (tasks.isEmpty()) {
            System.out.println("할 일이 없습니다.");
        } else {
            for (int i = 0; i < tasks.size(); i++) {
                System.out.println((i + 1) + ". " + tasks.get(i).toFileString());
            }
        }
    }

    public void completeTask(int taskNumber) {
        if (taskNumber > 0 && taskNumber <= tasks.size()) {
            Task task = tasks.get(taskNumber - 1);
            tasks.set(taskNumber - 1, new Task(task.description(), true));
            System.out.println(taskNumber + "번 할 일을 완료 처리했습니다.");
            saveTasks(); // 변경 후 바로 저장
        } else {
            System.out.println("잘못된 번호입니다.");
        }
    }

    public void deleteTask(int taskNumber) {
        if (taskNumber > 0 && taskNumber <= tasks.size()) {
            Task removed = tasks.remove(taskNumber - 1);
            System.out.println("'" + removed.description() + "' 할 일을 삭제했습니다.");
            saveTasks(); // 변경 후 바로 저장
        } else {
            System.out.println("잘못된 번호입니다.");
        }
    }

    // --- 파일 입출력 (private으로 숨김) ---
    private List<Task> loadTasks() {
        List<Task> loaded = new ArrayList<>();
        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            String line;
            while ((line = reader.readLine()) != null) {
                boolean isCompleted = line.startsWith("[X]");
                String description = line.substring(4);
                loaded.add(new Task(description, isCompleted));
            }
        } catch (IOException e) {
            // 파일이 없으면 빈 리스트로 시작
        }
        return loaded;
    }

    private void saveTasks() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(fileName, false))) {
            for (Task task : tasks) {
                writer.write(task.toFileString());
                writer.newLine();
            }
        } catch (IOException e) {
            System.err.println("파일 저장 중 오류가 발생했습니다: " + e.getMessage());
        }
    }
}
## 2. TodoApp.java (조종기 파일)
이 파일은 훨씬 간단해집니다. TaskManager를 생성하고 사용자의 명령어에 따라 필요한 메소드를 호출만 해주면 됩니다.

Java

// TodoApp.java
public class TodoApp {

    public static void main(String[] args) {
        if (args.length == 0) {
            System.out.println("사용법: java TodoApp <명령어> [내용]");
            System.out.println("명령어: list, add \"할 일\", done <번호>, delete <번호>");
            return;
        }

        // TaskManager(엔진)를 생성합니다.
        TaskManager manager = new TaskManager("tasks.txt");
        String command = args[0];

        // 명령어에 따라 엔진의 기능을 호출만 합니다.
        switch (command) {
            case "list" -> manager.listTasks();
            case "add" -> {
                if (args.length > 1) manager.addTask(args[1]);
                else System.out.println("추가할 할 일의 내용을 입력해주세요.");
            }
            case "done" -> {
                if (args.length > 1) manager.completeTask(Integer.parseInt(args[1]));
                else System.out.println("완료할 할 일의 번호를 입력해주세요.");
            }
            case "delete" -> {
                if (args.length > 1) manager.deleteTask(Integer.parseInt(args[1]));
                else System.out.println("삭제할 할 일의 번호를 입력해주세요.");
            }
            default -> System.out.println("알 수 없는 명령어입니다.");
        }
    }
}
## 이 구조의 장점
역할과 책임의 명확한 분리: TodoApp은 사용자 인터페이스만, TaskManager는 핵심 로직만 담당하여 코드를 이해하기 쉽습니다.

단순함과 구조의 균형: 파일 1개의 복잡함과 파일 4개의 부담감 사이에서 가장 적절한 균형을 이룹니다.

유지보수 용이성: 나중에 파일 저장 방식을 DB로 바꾸고 싶다면 TaskManager 파일만 수정하면 됩니다. TodoApp은 전혀 건드릴 필요가 없습니다.

실무에서도 이와 같이 역할에 따라 파일을 분리하는 2~3개 파일 구조는 매우 흔하게 사용됩니다.
