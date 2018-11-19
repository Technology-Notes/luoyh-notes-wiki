
```

public class Test {
    public static void main(String[] args) throws Exception {
        File file = new File("/root/hub/");
        URL[] urls = new URL[1];
        URLStreamHandler streamHandler = null;
        urls[0] = new URL(null, new URL("file", null, file.getCanonicalPath()).toString(), streamHandler);
        URLClassLoader loader = new URLClassLoader(urls);
        Class<?> clazz = loader.loadClass("Test$SubMap");
        SubMap instance = (SubMap)clazz.newInstance();
        instance.put("id", "1234");
        instance.put("name", "Roy");
        instance.put("msg", "Hello Wrold!");
        System.out.println(instance.get("msg"));
        System.out.println(instance);
    }

    static class SubMap extends HashMap<String, String> {

        public void fn() {
            // pass;
        }

        public SubMap() {
            System.out.println("Init SubMap!!!");
        }

    }

}


```