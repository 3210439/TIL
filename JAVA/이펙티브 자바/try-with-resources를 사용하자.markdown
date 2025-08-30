# Item 9: try-finally보다는 try-with-resources를 사용하라

자바에서 자원 관리는 매우 중요한 주제입니다. 파일, 네트워크 연결, 데이터베이스 연결 등과 같은 자원은 사용 후 반드시 적절히 닫아야 메모리 누수나 기타 문제를 방지할 수 있습니다. 자바 7부터 도입된 **try-with-resources**는 자원 관리를 간결하고 안전하게 처리할 수 있는 강력한 도구입니다. 이 글에서는 `try-finally`의 문제점과 `try-with-resources`의 장점을 살펴보고, 이를 Java 코드 예제를 통해 설명하겠습니다.

---

## try-finally의 문제점

`try-finally`는 자원을 닫기 위해 전통적으로 사용된 방식입니다. 하지만 이 방식에는 몇 가지 문제점이 있습니다.

1. **코드의 복잡성 증가**  
   자원을 닫기 위해 중첩된 `finally` 블록을 작성해야 할 때 코드가 복잡해지고 가독성이 떨어집니다. 특히 여러 자원을 다룰 경우 `finally` 블록 내에서 추가적인 예외 처리가 필요해집니다.

   ```java
   static void copy(String src, String dst) throws IOException {
       InputStream in = null;
       OutputStream out = null;
       try {
           in = new FileInputStream(src);
           out = new FileOutputStream(dst);
           byte[] buf = new byte[BUFFER_SIZE];
           int n;
           while ((n = in.read(buf)) >= 0) {
               out.write(buf, 0, n);
           }
       } finally {
           if (in != null) {
               try {
                   in.close();
               } catch (IOException e) {
                   // 예외 처리
               }
           }
           if (out != null) {
               try {
                   out.close();
               } catch (IOException e) {
                   // 예외 처리
               }
           }
       }
   }
   ```

   위 코드는 `InputStream`과 `OutputStream`을 닫기 위해 `finally` 블록에서 각각의 자원을 확인하고 닫아야 합니다. 이 과정에서 중첩된 `try-catch` 블록이 추가되어 코드가 길어지고 복잡해집니다.

2. **예외 처리의 어려움**  
   `try` 블록에서 발생한 예외와 `finally` 블록에서 발생한 예외가 겹칠 경우, 디버깅이 어려워질 수 있습니다. 예를 들어, `try` 블록에서 예외가 발생한 후 `finally` 블록에서 또 다른 예외가 발생하면, 원래 예외가 덮어씌워져 실제 문제를 파악하기 어려워집니다. 이를 **예외 마스킹(Exception Masking)**이라고 합니다.

3. **실수로 자원을 닫지 않을 가능성**  
   개발자가 실수로 `finally` 블록에서 자원을 닫는 코드를 누락하거나, 잘못된 순서로 자원을 닫으면 자원 누수가 발생할 수 있습니다.

---

## try-with-resources의 도입 배경

자바 7에서 도입된 `try-with-resources`는 이러한 문제를 해결하기 위해 설계되었습니다. 이 구문은 **AutoCloseable** 또는 **Closeable** 인터페이스를 구현한 자원을 자동으로 닫아주는 기능을 제공합니다. 이를 통해 다음과 같은 장점을 얻을 수 있습니다:

- **간결한 코드**: 자원을 명시적으로 닫는 코드를 작성할 필요가 없어 코드가 간결해집니다.
- **예외 처리의 명확성**: 자원을 닫는 과정에서 발생한 예외가 `try` 블록의 예외를 덮어씌우지 않도록 **억제된 예외(Suppressed Exception)**로 처리됩니다.
- **안전한 자원 관리**: 자원 누수를 방지하고, 자원을 올바른 순서로 닫아줍니다.

`try-with-resources`는 자원을 선언하고 초기화하는 블록을 `try` 문 안에 정의하며, 해당 자원이 스코프를 벗어나면 자동으로 `close()` 메서드가 호출됩니다.

---

## try-with-resources 사용 예제 (Java)

아래는 파일을 복사하는 예제를 Java로 작성한 코드입니다:

<xaiArtifact artifact_id="f869f716-c07b-42b5-b77a-c2470a988968" artifact_version_id="73ea88b7-6d0d-4ed2-a94d-add16052f8de" title="FileCopy.java" contentType="text/java">

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class FileCopy {
    private static final int BUFFER_SIZE = 8192;

    public static void copy(String src, String dst) throws IOException {
        try (FileInputStream in = new FileInputStream(src);
             FileOutputStream out = new FileOutputStream(dst)) {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0) {
                out.write(buf, 0, n);
            }
        }
    }
}
```