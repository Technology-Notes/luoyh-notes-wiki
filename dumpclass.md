java -classpath ".;./bin;%JAVA_HOME%/lib/sa-jdi.jar" sun.jvm.hotspot.tools.jcore.ClassDump <PID>

java -classpath "%JAVA_HOME%/lib/sa-jdi.jar" -Dsun.jvm.hotspot.tools.jcore.filter=sun.jvm.hotspot.tools.jcore.PackageNameFilter -Dsun.jvm.hotspot.tools.jcore.PackageNameFilter.pkgList=com.fr  sun.jvm.hotspot.tools.jcore.ClassDump


sudo java -classpath "$JAVA_HOME/lib/sa-jdi.jar" sun.jvm.hotspot.HSDB



jar cvf xxx.jar .


java -jar cfr.jar xxx.jar --outputdir d:/x/y

