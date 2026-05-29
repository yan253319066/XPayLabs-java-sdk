# XPay Labs Java SDK

[English](README.md) | 中文

XPay Labs 加密货币支付网关的官方 Java SDK。

## 功能特性

- 创建加密货币付款订单（商户向用户发送加密货币）
- 创建加密货币收款订单（商户从用户接收加密货币）
- 查询订单状态
- 获取支持的加密货币和链
- 验证和解析 Webhook 通知
- 兼容 Spring Boot 3.4.6

## 安装

在 Maven 项目中添加依赖：

```xml
<dependency>
    <groupId>io.xpay</groupId>
    <artifactId>xpay-java-sdk</artifactId>
    <version>0.1.0</version>
</dependency>
```

或者使用 Gradle：

```groovy
implementation 'io.xpay:xpay-java-sdk:0.1.0'
```

## 快速开始

```java
import io.xpay.sdk.XPay;
import io.xpay.sdk.XPayConfig;
import io.xpay.sdk.model.request.PayoutRequest;
import io.xpay.sdk.model.response.ApiResponse;
import io.xpay.sdk.model.response.PayoutData;

// 使用 API 凭证初始化 SDK
XPay xpay = new XPay(XPayConfig.builder()
        .apiKey("your-api-token")
        .apiSecret("your-api-secret")
        .baseUrl("https://api.xpaylabs.com")
        .build());

// 创建付款订单
try {
    PayoutRequest request = PayoutRequest.builder()
            .amount(100.0)
            .symbol("USDT")
            .chain("TRON")
            .orderId("order-" + System.currentTimeMillis())
            .uid("user123")
            .receiveAddress("TXmVthgn6yT1kANGJHTHcbEGEKYDLLGJGp")
            .build();

    ApiResponse<PayoutData> response = xpay.createPayout(request);
    System.out.println("付款订单创建成功");
} catch (Exception e) {
    System.err.println("创建付款订单失败: " + e.getMessage());
}
```

## API 参考

### 配置

```java
XPay xpay = new XPay(XPayConfig.builder()
        .apiKey("your-api-token")
        .apiSecret("your-api-secret")
        .baseUrl("https://api.xpaylabs.com")
        .connectTimeout(30000)
        .readTimeout(30000)
        .build());
```

### 付款订单

创建商户向用户发送加密货币的订单。

### 收款订单

创建商户从用户接收加密货币的订单。

### 订单状态

查询指定订单的当前状态。

### 支持的币种

获取平台支持的加密货币列表。

### Webhook

验证和解析 Webhook 通知。

## 错误处理

SDK 在 API 返回错误时会抛出 `XPayApiException`：

```java
try {
    ApiResponse<PayoutData> response = xpay.createPayout(request);
} catch (XPayApiException e) {
    System.err.println("API 错误: " + e.getMessage());
    System.err.println("状态码: " + e.getStatusCode());
    System.err.println("错误码: " + e.getErrorCode());
}
```

## 许可证

MIT
