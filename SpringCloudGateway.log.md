` chain.filter(exchange.mutate().response(logResponse(exchange)).build()); `

```

private ServerHttpResponseDecorator logResponse(ServerWebExchange exchange) {
        ServerHttpResponse origResponse = exchange.getResponse();
        //Long startTime = exchange.getAttribute(START_TIME);
        log.info("Response HttpStatus:{}", origResponse.getStatusCode());
        HttpHeaders headers = origResponse.getHeaders();
        headers.forEach((key, value) -> log.debug("[RequestLogFilter]Headers:Key->{},Value->{}", key, value));
        MediaType contentType = headers.getContentType();
        long length = headers.getContentLength();
        log.info("Response ContentType:{},Content Length:{}", contentType, length);
        //Long executeTime = (System.currentTimeMillis() - startTime);
        log.info("Response Original Path:{},Cost:{} ms", exchange.getRequest().getURI().getPath(), 0);
        DataBufferFactory bufferFactory = origResponse.bufferFactory();
        
        return new ServerHttpResponseDecorator(origResponse) {
            @Override
            public Mono<Void> writeWith(Publisher<? extends DataBuffer> body) {
                if (body instanceof Flux) {
                    Flux<? extends DataBuffer> fluxBody = (Flux<? extends DataBuffer>) body;
                    return super.writeWith(fluxBody.map(dataBuffer -> {
                        byte[] content = new byte[dataBuffer.readableByteCount()];
                        dataBuffer.read(content);
                        String bodyContent = new String(content, StandardCharsets.UTF_8);
                        log.info("Response:{}", bodyContent);
                        return bufferFactory.wrap(content);
                    }));
                }
                return super.writeWith(body);
            }
        };
    }

```