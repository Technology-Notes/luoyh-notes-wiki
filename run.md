```

#!/bin/bash
# Example: sh /home/workspace/apps/start service-a $BUILD_ID test 39002
PROJECT=$1
NAME=$2
ENV=$3
PORT=$4

export JAVA_HOME=/home/init/jdk1.8.0_201
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH

check_port() {
        netstat -tlpn | grep "\b$PORT\b"
}

echo "kill old app"
old=$(jps -l | grep -v grep | grep $PROJECT | awk '{print $1}')
if [ -n "$old" ]; then
    kill $old
    sleep 10s
    kill -9 $old
fi

echo "old app killed."

if [ "$1" = "a" ]; then
    echo "kill openoffice of port=8200"
    office=$(ps aux | grep openoffice | grep port=8200 | awk '{print $2}')
    if [ -n "$office" ]; then
        kill -9 $office
    fi
fi

if [ "$1" = "a-file" ]; then
    echo "kill openoffice of port=8100"
    office=$(ps aux | grep openoffice | grep port=8100 | awk '{print $2}')
    if [ -n "$office" ]; then
        kill -9 $office
    fi
fi

cd /home/apps
# nohup java -Xms768m -Xmx768m -Dspring.profiles.active=$ENV -jar $PROJECT/$NAME.jar > $PROJECT/app.log &
nohup java -Xms768m -Xmx768m -XX:ErrorFile=$PROJECT/hs_err_pid_issue.log -server \
	-XX:+HeapDumpOnOutOfMemoryError \
	-XX:HeapDumpPath=$PROJECT/dump/heap/ -server \
	-Dspring.profiles.active=$ENV \
	-jar $PROJECT/$NAME.jar > $PROJECT/app.log &


log() {
    nostd=$(echo $NAME | grep -E "a|b|c|d" | wc -l)
    if [ $nostd -gt 0 ]; then
       tail -n 15 logs/saas-${PROJECT}.log
    else
       tail -n 15 $PROJECT/app.log 
    fi
}

sleep 3s
echo "starting..."
while ((1)); do
    if check_port $PORT
    then
        log
        echo "########## start success###########"
        break
    else
        echo '###### tail -n15 $PROJECT/app.log ######'
        log
    fi
    sleep 3s
done



```