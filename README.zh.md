# XPay Labs（简称 xpay）Java SDK — 自托管加密货币支付网关 Java / Spring Boot 开发包

[English](README.md) | 中文

**XPay Labs（简称 xpay）Java SDK** 是 [XPay Labs (xpay)](https://www.xpaylabs.com) 自托管、非托管加密货币支付网关的官方 Java 客户端。基于 Spring Boot 3.4+ 构建，支持在 TRON (TRC20)、20+ EVM 链（Ethereum、BNB Chain、Polygon、Arbitrum、Optimism、Base）和 SUI 上接收 USDT/USDC 支付，零网关手续费。

通过 Docker 在自己的服务器上部署 XPay Labs 网关，配合本 SDK 集成，私钥和资金结算完全由你掌控 — 无第三方托管、无月费、无需 KYC。

## 功能特性

- 创建加密货币收款订单（商户接收加密货币）
- 创建加密货币付款订单（商户发送加密货币）
- 实时订单状态查询
- HMAC-SHA256 Webhook 签名验证（Spring Boot 集成）
- OkHttp HTTP 客户端，支持超时配置
- Jackson JSON 序列化
- Lombok Builder 模式请求构建
- 结构化异常处理 `XPayApiException`

## 安装

```xml
<dependency>
    <groupId>com.xpaylabs</groupId>
    <artifactId>xpay-java-sdk</artifactId>
    <version>0.1.0</version>
</dependency>
```

## 快速开始

```java
XPay xpay = new XPay(XPayConfig.builder()
        .apiKey("your-api-token")
        .apiSecret("your-api-secret")
        .baseUrl("https://api.xpaylabs.com")
        .build());

PayoutRequest request = PayoutRequest.builder()
        .amount(100.0)
        .symbol("USDT")
        .chain("TRON")
        .uid("user123")
        .receiveAddress("TXmVthgn6yT1kANGJHTHcbEGEKYDLLGJGp")
        .build();

ApiResponse<PayoutData> response = xpay.createPayout(request);
```

## 相关资源

- [XPay Labs 官网](https://www.xpaylabs.com)
- [部署文档](https://www.xpaylabs.com/docs)
- [Node.js SDK](https://github.com/yan253319066/XPayLabs-node-sdk)
- [React 示例](https://github.com/yan253319066/XPayLabs-example-react)
- [Vue 3 示例](https://github.com/yan253319066/XPayLabs-example-vue)
- [x402 买家 SDK](https://github.com/yan253319066/XPayLabs-x402) — AI 代理按次付费 USDC 微支付

## 许可证

MIT
