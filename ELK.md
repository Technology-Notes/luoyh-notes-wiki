----------
#ELK (elasticsearch logstash kibana)

----------
## Elasticsearch

Elasticsearch + Kibana [See](https://www.elastic.co/start). 
[Book](https://www.gitbook.com/book/chenryn/elk-stack-guide-cn/details)
**Not use `root` user.**
> Configuration
```
>$ vim /elk/elasticsearch/config/elasticsearch.yml
network.host: 0.0.0.0
xpack.security.enabled: false # because x-pack only one month free use.
>$ /elk/elasticsearch/bin/elasticsearch -d
```
## Kibana

> Configuration
```
>$ vim /elk/kibana/config/kibana.yml
server.host: "0.0.0.0"
> /elk/kibana/bin/kibana
```

## Logstash
> Configuration

```
>$ vim /elk/logstash/config/logback.conf

input {
    tcp {
        port => 4560
        codec => json_lines
    }
}

output {
    elasticsearch {
	        hosts => ["127.0.0.1:9200"] # elasticsearch 
	        index => "logback-cashier-%{+YYYY.MM.dd}" # index
	        document_type => "%{type}"
	        flush_size => 20000
	        idle_flush_time => 10
	        sniffing => true
	        template_overwrite => true
	        user => elastic # elasticsearch username
	        password => changeme # elasticsearch password
    }
}

>$ elastic.conf
input {
    file {
        path => "/usr/local/nginx/logs/access.json"
        codec => json
    }
}

output {
    elasticsearch {
        hosts => ["127.0.0.1:9200"]
        index => "logstash-%{+YYYY.MM.dd}"
        document_type => "%{type}"
        flush_size => 20000
        idle_flush_time => 10
        sniffing => true
        template_overwrite => true
        user => elastic
        password => changeme
    }
}

>$ nginx.conf
input {
    file {
        path => "/usr/local/nginx/logs/access.json"
        codec => "json"
    }
}
output {
    stdout {
        codec => rubydebug
    }
}

>$ stdin.conf


input {
    stdin {
        add_field => {"key" => "value"}
        codec => "plain"
        tags => ["add"]
        type => "std"
    }
}
output {
    stdout {
        codec => rubydebug
    }
}

>$ sys.conf


input {
  syslog {
    port => "514"
  }
}
output {
        stdout {
                codec => rubydebug
        }
}

```

## logback-spring.xml

[See](https://github.com/logstash/logstash-logback-encoder)

```
<appender name="stash" 
	class="net.logstash.logback.appender.LogstashTcpSocketAppender">
      <destination>192.168.2.244:4560</destination>
      <!-- encoder is required -->
      <encoder class="net.logstash.logback.encoder.LogstashEncoder" />
</appender>


```


> `End.`

 


### logstash

> ./bin/logstash -f config/sth-log.conf

> elasticsearch-conf

```
input {
    file {
        path => "/usr/local/nginx/logs/access.json"
        codec => json
    }
}

output {
    elasticsearch {
        hosts => ["127.0.0.1:9200"]
        index => "logstash-%{+YYYY.MM.dd}"
        document_type => "%{type}"
        flush_size => 20000
        idle_flush_time => 10
        sniffing => true
        template_overwrite => true
        user => elastic
        password => changeme
    }
}

```

> nginx.conf

```
input {
    file {
        path => "/usr/local/nginx/logs/access.json"
        codec => "json"
    }
}
output {
    stdout {
        codec => rubydebug
    }
}

// nginx.conf 
   log_format json '{"@timestamp":"$time_iso8601",'
                    '"@version":"1",'
                    '"host":"$server_addr",'
                    '"client":"$remote_addr",'
                    '"size":$body_bytes_sent,'
                    '"responsetime":$request_time,'
                    '"domain":"$host",'
                    '"method":"$request_method",'
                    '"url":"$uri",'
                    '"status":"$status",'
                    '"args":"$args"}';
    access_log /usr/local/nginx/logs/access.json json;

  
```

> stdin.conf

```
input {
    stdin {
        add_field => {"key" => "value"}
        codec => "plain"
        tags => ["add"]
        type => "std"
    }
}
output {
    stdout {
        codec => rubydebug
    }
}       
```

> syslog

```
 
input {
  syslog {
    port => "514"
  }
}
output {
        stdout {
                codec => rubydebug
        }
}
~       
```
