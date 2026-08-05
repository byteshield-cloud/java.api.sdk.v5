# ByteShield Java API SDK

[English](README.md) | [简体中文](README.zh-CN.md)

ByteShield PR REST API 的官方 Java 客户端。SDK 负责 HMAC-SHA256 请求签名、
JSON 传输和响应解析。

## 环境要求

- Java 8 或更高版本
- Maven 3.6 或更高版本
- ByteShield API App ID 和 App Secret
- 为账号分配的 API 基础地址

请勿将凭证写入代码或提交到版本库。如尚未获得凭证或 API 基础地址，请联系
ByteShield 技术支持。

## 安装

如需使用当前仓库中的版本，请克隆仓库并将构件安装到本地 Maven 仓库：

```bash
git clone https://github.com/byteshield-cloud/java.api.sdk.v5.git
cd java.api.sdk.v5
mvn clean install
```

然后在项目中添加依赖：

```xml
<dependency>
  <groupId>org.byteshieldapisdk</groupId>
  <artifactId>byteshieldapisdk</artifactId>
  <version>0.1</version>
</dependency>
```

## 快速开始

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
        headers.put("X-Lang", "zh");

        JSONObject response = client.get("Web.Domain.Info", query, headers);
        if (response == null) {
            throw new IllegalStateException("ByteShield API 请求失败");
        }

        System.out.println("code=" + response.getInteger("code"));
        System.out.println("message=" + response.getString("message"));
        System.out.println("data=" + response.get("data"));
    }
}
```

接口名称和参数以 ByteShield API 文档为准。查询参数必须单独传递，不要拼接到
接口名称中。

## 客户端配置

标准构造方法接收 API 基础地址、App ID 和 App Secret：

```java
Sdk client = new Sdk(apiBaseUrl, appId, appSecret);
```

委托访问场景可以额外传入用户 ID：

```java
Sdk client = new Sdk(apiBaseUrl, appId, appSecret, userId);
```

如需配置超时或代理，请传入自定义 OkHttp 客户端：

```java
import java.util.concurrent.TimeUnit;
import okhttp3.OkHttpClient;

OkHttpClient httpClient = new OkHttpClient.Builder()
    .connectTimeout(10, TimeUnit.SECONDS)
    .readTimeout(30, TimeUnit.SECONDS)
    .build();

Sdk client = new Sdk(apiBaseUrl, appId, appSecret, userId, httpClient);
```

## 请求

SDK 提供 `get`、`post`、`put`、`patch` 和 `delete` 方法。不同重载方法可以
接收查询参数、JSON 请求体和自定义请求头。

```java
Map<String, Object> query = new HashMap<>();
Map<String, Object> body = new HashMap<>();
Map<String, String> headers = new HashMap<>();
headers.put("X-Lang", "zh");

JSONObject response = client.post("example.endpoint", query, body, headers);
```

## 响应

方法返回 Fastjson `JSONObject`。SDK 保留原始 API 响应，并将 `status.code`
和 `status.message` 复制到顶层 `code` 和 `message` 字段。传输失败时会记录日志
并返回 `null`。

HTTP 请求成功时，业务状态码仍可能表示失败。读取 `data` 前应检查 `code`。

## 开发与测试

在仓库根目录运行 Maven 测试生命周期：

```bash
mvn test
```

## 安全建议

- 从环境变量或密钥管理服务读取 App ID 和 App Secret。
- 不要提交凭证、请求签名或生产环境响应数据。
- 凭证泄露后应立即轮换。

## 许可证

本仓库当前未包含许可证文件。重新分发或修改 SDK 前，请联系维护者确认授权。
