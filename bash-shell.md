```

#!/bin/bash
# author: Luoyh(Roy), ro.luoyh@foxmail.com
# time: Mar 4, 2019


abs=`cd $(dirname $0); pwd -P`

if [ ! -f "$abs/server.conf" ]; then
    echo "== not found($abs/server.conf) =="
    exit 1
fi

rss=0
rpid=0
function ck_ws() {
    rss=0
    rss=$(curl -sfI $1 | head -n 1 | awk '{print $2}')
}

function ck_pid() {
    rpid=0
    rpid=$(ps aux | grep $1 | grep -v grep | wc -l)
}
function ck_timeout() {
    count=0
    pro="."
    echo "==check [$1] server"
    echo "== Ping website: $2"
    printf "\r\n"
    while true; do
        printf "[%-50s] %d\r" $pro $count
        pro=".$pro"
        ck_pid $1
        if [ $rpid -eq 0 ]; then
            printf "\r\n"
            echo "==[$1] start fail , exit=="
            exit 1
        fi
        ck_ws $2
        r=$rss
        if [[ -n $r && ($r -eq 200 || $r -eq 302) ]]; then
            printf "\r\n"
            echo "==[$1] start success=="
            break
        else
            count=$[$count+1]
            if [ $count -gt $3 ]; then
                printf "\r\n"
                echo "==[$1] start fail exit==!"
                echo 'Exit!'
                exit 1
            fi
        fi
        sleep 1
    done
}
printf "\r\n"
ns=1
for arg in "$@"; do
    if [ $arg == '--config-server' ]; then
        ns=0
    fi
done

echo "Being starting ======================================="
echo '==stop=='

count=0
printf "\r\n"
pro="."
while true; do
    printf "==stopping[%-50s] %d\r" $pro $count
    pro=".$pro"
    if [ $ns -eq 1 ]; then
        run=$(ps aux | grep java | grep -v grep | grep -v config-server | awk '{print $2}')
    else
        run=$(ps aux | grep java | grep -v grep  awk '{print $2}')
    fi
    if [ -n "$run" ]; then
        kill $run
    else
        break
    fi
    count=$[$count+1]
    sleep 1
    if [ $count -gt 7 ]; then
        printf "\r\n"
        # force kill
        echo '==stop fail force kill=='
        kill -9 $run
    fi
done
printf "\r\n"
echo '==stop success=='
echo '==================================================='
sleep 2


if [ $ns -eq 1 ]; then
    echo "ignore config-server"
else
    echo '==begin start config server=='
    sh $abs/config.sh
    echo '==config server start success=='
    echo ''
    ck_timeout config-server http://localhost:1234/configserver/admin 300
fi

count=0
while read -r line ; do
    if [ -n "$line" ] && [[ $line != '#'* ]]; then
        arr=(${line//:/ })
        app=${arr[0]}
        port=${arr[1]}
        path=${arr[2]}
        echo "==start $app ..........."
        echo "==start command:nohup java -jar $abs/$app --spring.profiles.active=online --eureka.client.serviceUrl.defaultZone=http://user:pwd@host:1111/eureka --server.port=$port >/dev/null 2>&1 &"
        nohup java -jar $abs/$app --spring.profiles.active=online --eureka.client.serviceUrl.defaultZone=http://user:pwd@host:1111/eureka --server.port=$port >/dev/null 2>&1 &
        ck_timeout $app http://localhost:$port$path 120
        echo ""
        echo ""
    fi
done < "$abs/server.conf"

echo '================Finished====================!!!!!!!'



```