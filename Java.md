



```
// Exception stack trace to String
try (StringWriter sw = new StringWriter()) {
	exception.printStackTrace(new PrintWriter(sw, true));
	String stringStackTrace = sw.getBuffer().toString();
}


// File operate
package java.nio.file.*;
Paths/Files.


Java 8 news.

Stream.of(xx)
.filter(x -> x == y) // 过滤
.map(x-> y) // 1:1, 把stream的每一个元素(x)转换成另外一个元素(y).
.flatMap(x -> y) // 1:N 如果stream的元素是一个集合类的, 应返回处理成stream, 这样就变成了一个新的stream只有原始的数据.
.collect(Collectors.toList()) // 搜集成一个list
.forEach(System.out::println) // 循环
```