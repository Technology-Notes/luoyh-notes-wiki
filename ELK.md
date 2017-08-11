
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
