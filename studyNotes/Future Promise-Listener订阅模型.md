简单的Demo
```java
import java.util.concurrent.CompletableFuture;

class MyTask {
    private CompletableFuture<String> future = new CompletableFuture<>();

    public CompletableFuture<String> getFuture() {
        return future;
    }

    public void execute() {
        new Thread(() -> {
            try {
                Thread.sleep(1000);
                future.complete("Task Completed!");
            } catch (InterruptedException e) {
                future.completeExceptionally(e);
            }
        }).start();
    }
}

class MyListener {
    public void subscribe(CompletableFuture<String> future) {
        future.whenComplete((result, throwable) -> {
            if (throwable != null) {
                onFailure(throwable);
            } else {
                onSuccess(result);
            }
        });
    }

    public void onSuccess(String result) {
        System.out.println("Success: " + result);
    }

    public void onFailure(Throwable throwable) {
        System.out.println("Error: " + throwable.getMessage());
    }
}

public class FuturePromiseListenerDemo {
    public static void main(String[] args) {
        MyTask task = new MyTask();
        MyListener listener = new MyListener();

        // Listener订阅Future的状态变化
        listener.subscribe(task.getFuture());

        // 执行异步任务
        task.execute();

        System.out.println("Main thread continues to execute...");
    }
}


```