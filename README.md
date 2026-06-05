# XPay Labs (xpay) Java SDK — Self-Hosted Crypto Payment Gateway for Java / Spring Boot

English | [中文](README.zh.md)

**XPay Labs (xpay) Java SDK** is the official Java client for [XPay Labs (xpay)](https://www.xpaylabs.com) — the self-hosted, non-custodial crypto payment gateway. Built for Spring Boot 3.4+, it enables Java developers to accept USDT/USDC payments on TRON (TRC20), 20+ EVM chains (Ethereum, BNB Chain, Polygon, Arbitrum, Optimism, Base), and SUI with zero gateway fees.

Deploy the XPay Labs gateway on your own infrastructure via Docker, integrate with this SDK, and maintain full control of private keys and settlement — no third-party custody, no monthly fees, no KYC.

| Feature | XPay Labs | BitPay | Coinbase Commerce |
|---------|-----------|--------|-------------------|
| Transaction Fees | **0%** (gas only) | 1% per tx | 0.8% + $25/mo |
| Custody Model | **Non-custodial** | Custodial | Custodial |
| Supported Chains | **TRON, EVM, SUI** (20+) | BTC, ETH, LTC | ETH, Base |
| Spring Boot Support | **✅ Native** | ❌ | ❌ |
| Deployment | **Self-hosted** (Docker) | Cloud (SaaS) | Cloud (SaaS) |
| Webhook Security | **HMAC-SHA256** | IPN | Basic |
| License | **MIT** | Proprietary | Proprietary |

## Features

- Create cryptocurrency collection orders (merchant receives crypto)
- Create cryptocurrency payout orders (merchant sends crypto)
- Real-time order status queries
- HMAC-SHA256 webhook verification with Spring Boot integration
- OkHttp-based HTTP client with configurable timeouts
- Jackson JSON serialization
- Lombok-based builder pattern for requests
- `XPayApiException` for structured error handling

## Installation

Add the dependency to your Maven project:

```xml
<dependency>
    <groupId>io.xpay</groupId>
    <artifactId>xpay-java-sdk</artifactId>
    <version>0.1.0</version>
</dependency>
```

Or for Gradle:

```groovy
implementation 'io.xpay:xpay-java-sdk:0.1.0'
```

## Quick Start

```java
import io.xpay.sdk.XPay;
import io.xpay.sdk.XPayConfig;
import io.xpay.sdk.model.request.PayoutRequest;
import io.xpay.sdk.model.response.ApiResponse;
import io.xpay.sdk.model.response.PayoutData;

XPay xpay = new XPay(XPayConfig.builder()
        .apiKey("your-api-token")
        .apiSecret("your-api-secret")
        .baseUrl("https://api.xpaylabs.com")
        .build());

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
    System.out.println("Payout created: " + response.getData().getOrderId());
} catch (XPayApiException e) {
    System.err.println("API Error: " + e.getMessage());
}
```

## API Reference

### Configuration

```java
XPay xpay = new XPay(XPayConfig.builder()
        .apiKey("your-api-token")
        .apiSecret("your-api-secret")
        .baseUrl("https://api.xpaylabs.com")
        .connectTimeout(30000)
        .readTimeout(30000)
        .build());
```

### Payout Orders

#### Create a payout order (merchant sends crypto to user)

```java
PayoutRequest request = PayoutRequest.builder()
        .amount(100.0)
        .symbol("USDT")
        .chain("TRON")
        .orderId("order-123")
        .uid("user123")
        .receiveAddress("TXmVthgn6yT1kANGJHTHcbEGEKYDLLGJGp")
        .build();

ApiResponse<PayoutData> response = xpay.createPayout(request);
```

### Collection Orders

#### Create a collection order (merchant receives crypto from user)

```java
CollectionRequest request = CollectionRequest.builder()
        .amount(50.0)
        .symbol("USDT")
        .chain("TRON")
        .orderId("order-123")
        .uid("user123")
        .build();

ApiResponse<CollectionData> response = xpay.createCollection(request);
```

### Order Status

#### Get order status

```java
ApiResponse<OrderDetails> response = xpay.getOrderStatus("order-123");
```

### Supported Symbols

#### Get supported symbols

```java
ApiResponse<List<SupportedSymbol>> allSymbols = xpay.getSupportedSymbols();
ApiResponse<List<SupportedSymbol>> tronSymbols = xpay.getSupportedSymbols("TRON", null);
ApiResponse<List<SupportedSymbol>> tronUsdt = xpay.getSupportedSymbols("TRON", "USDT");
```

### Webhooks

#### Verify and parse webhook (Spring Boot)

```java
@RestController
public class WebhookController {

    private final XPay xpay;

    public WebhookController(XPay xpay) {
        this.xpay = xpay;
    }

    @PostMapping("/webhook")
    public ResponseEntity<String> handleWebhook(@RequestBody String body) {
        WebhookEvent event = xpay.parseWebhook(body);

        if (event == null) {
            return ResponseEntity.badRequest().body("Invalid webhook signature");
        }

        switch (event.getNotifyType()) {
            case ORDER_SUCCESS:
                OrderWebhookData orderData = (OrderWebhookData) event.getData();
                System.out.println("Order " + orderData.getOrderId() + " completed!");
                break;
            case COLLECT_SUCCESS:
                CollectWebhookData collectData = (CollectWebhookData) event.getData();
                System.out.println("Collection completed! Amount: " + collectData.getCollectAmount());
                break;
        }

        return ResponseEntity.ok("Webhook received");
    }
}
```

## Error Handling

```java
try {
    ApiResponse<PayoutData> response = xpay.createPayout(request);
} catch (XPayApiException e) {
    System.err.println("API Error: " + e.getMessage());
    System.err.println("Status: " + e.getStatusCode());
    System.err.println("Error Code: " + e.getErrorCode());
} catch (Exception e) {
    System.err.println("General Error: " + e.getMessage());
}
```

## Related Resources

- [XPay Labs Website](https://www.xpaylabs.com)
- [Deployment Guide](https://www.xpaylabs.com/docs)
- [Pricing — 0% Transaction Fees](https://www.xpaylabs.com/pricing)
- [Node.js SDK](https://github.com/yan253319066/XPayLabs-node-sdk)
- [React Example](https://github.com/yan253319066/XPayLabs-example-react)
- [Vue 3 Example](https://github.com/yan253319066/XPayLabs-example-vue)
- [x402 Buyer SDK](https://github.com/yan253319066/XPayLabs-x402) — Pay-per-call USDC micropayments for AI agents
- [BitPay Alternative](https://www.xpaylabs.com/alternatives/bitpay)
- [Coinbase Commerce Alternative](https://www.xpaylabs.com/alternatives/coinbase-commerce)
- [NowPayments Alternative](https://www.xpaylabs.com/alternatives/nowpayments)
- [OpenNode Alternative](https://www.xpaylabs.com/alternatives/opennode)
- [CoinGate Alternative](https://www.xpaylabs.com/alternatives/coingate)

## License

MIT
