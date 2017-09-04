



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

### Java 8+ download HTTP images

```
/**
 *
 * @author luoyh(Roy) - Jul 3, 2017
 */
public class Dl {
	static int idx = 16;
	
	public static void main(String[] args) {
		String srcs = "https://s3.amazonaws.com/123rf-chrome/images/67028130_xl.jpg,https://s3.amazonaws.com/123rf-chrome/images/36909280_xl.jpg,https://images.unsplash.com/photo-1477290813395-11a3d65976fe?ixlib=rb-0.3.5&q=99&fm=jpg&crop=entropy&cs=tinysrgb&w=2048&fit=max&s=2124d2dede0611bfa552c57947eb8adb";
		Stream.of(srcs.split(",")).parallel().forEach(Dl::dl);
		System.out.println(idx++);
	}
	
	static void dl(String src) {
		try (InputStream in = new URL(src).openConnection().getInputStream()) {
			Files.copy(in, Paths.get("C:/Users/roy/Desktop/imgs/" + (idx ++) + ".jpg"));
		} catch (MalformedURLException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

}

```