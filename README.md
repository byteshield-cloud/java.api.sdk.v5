# ByteShield API SDK for Java

[English](README.md) | [简体中文](README.zh-CN.md)

The official Java client for calling ByteShield PR REST APIs. The SDK handles
HMAC-SHA256 request signing, JSON transport, and response parsing.

## Requirements

- Java 8 or later
- Maven 3.6 or later
- A ByteShield API App ID and App Secret
- The API base URL assigned to your account

Keep credentials outside source control. Contact ByteShield support if you do
not have credentials or an API base URL.

## Installation

To use the current repository build, clone it and install the artifact into your
local Maven repository:

```bash
git clone https://github.com/byteshield-cloud/java.api.sdk.v5.git
cd java.api.sdk.v5
mvn clean install
```

Then add the dependency to your project:

```xml
<dependency>
  <groupId>org.byteshieldapisdk</groupId>
  <artifactId>byteshieldapisdk</artifactId>
  <version>0.1</version>
</dependency>
```

## Quick start

```java
import com.alibaba.fastjson.JSONObject;
import com.sdk.Sdk;

import java.util.HashMap;
import java.util.Map;

public class Example {
    public static void main(String[] args) {
        Sdk client = new Sdk(
            System.getenv("BYTESHIELD_API_BASE_URL"),
            System.getenv("BYTESHIELD_APP_ID"),
            System.getenv("BYTESHIELD_APP_SECRET")
        );

        Map<String, Object> query = new HashMap<>();
        query.put("domain", 101153);

        Map<String, String> headers = new HashMap<>();
        headers.put("X-Lang", "en");

        JSONObject response = client.get("Web.Domain.Info", query, headers);
        if (response == null) {
            throw new IllegalStateException("ByteShield API request failed");
        }

        System.out.println("code=" + response.getInteger("code"));
        System.out.println("message=" + response.getString("message"));
        System.out.println("data=" + response.get("data"));
    }
}
```

Use the API name and parameters from the ByteShield API documentation. Query
parameters must be passed separately; do not append them to the API name.

## Client configuration

The standard constructor accepts the API base URL, App ID, and App Secret:

```java
Sdk client = new Sdk(apiBaseUrl, appId, appSecret);
```

An optional user ID can be supplied for delegated access:

```java
Sdk client = new Sdk(apiBaseUrl, appId, appSecret, userId);
```

To configure timeouts or a proxy, supply a custom OkHttp client:

```java
import java.util.concurrent.TimeUnit;
import okhttp3.OkHttpClient;

OkHttpClient httpClient = new OkHttpClient.Builder()
    .connectTimeout(10, TimeUnit.SECONDS)
    .readTimeout(30, TimeUnit.SECONDS)
    .build();

Sdk client = new Sdk(apiBaseUrl, appId, appSecret, userId, httpClient);
```

## Requests

The SDK provides `get`, `post`, `put`, `patch`, and `delete` methods. Depending
on the method, overloads accept query parameters, a JSON request body, and
custom headers.

```java
Map<String, Object> query = new HashMap<>();
Map<String, Object> body = new HashMap<>();
Map<String, String> headers = new HashMap<>();
headers.put("X-Lang", "en");

JSONObject response = client.post("example.endpoint", query, body, headers);
```

## Responses

Methods return a Fastjson `JSONObject`. The SDK preserves the API response and
also copies `status.code` and `status.message` to top-level `code` and `message`
fields. A transport failure is logged and returns `null`.

A successful HTTP request can still contain a non-success business code. Check
`code` before consuming `data`.

## Development

Compile and run the Maven test lifecycle from the repository root:

```bash
mvn test
```

## Security

- Load the App ID and App Secret from environment variables or a secret manager.
- Do not commit credentials, request signatures, or production response data.
- Rotate credentials immediately if they are exposed.

## License

This repository does not currently include a license file. Contact the
maintainers before redistributing or modifying the SDK.
