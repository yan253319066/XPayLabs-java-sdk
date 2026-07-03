# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

XPayLabs crypto payment gateway Java SDK (Maven package `com.xpaylabs:xpay-java-sdk` v0.1.0). Java 17 + OkHttp + Jackson.

## Commands

```bash
mvn clean install    # Compile and install to local repo
mvn clean package    # Package only
mvn deploy           # Publish to Maven Central (requires GPG + Sonatype credentials)
```

## Architecture

### Core Classes

| Class | Description |
|---|------|
| `XPay` | Main SDK entry point, wraps all API operations and HMAC signing |
| `XPayConfig` | Configuration (Builder pattern): `apiKey`, `apiSecret`, `baseUrl`, timeouts |
| `ApiClient` | OkHttp wrapper: GET/POST, `X-API-TOKEN` auth, error handling |
| `SignatureUtil` | HMAC-SHA256 signature generation and verification |
| `XPayApiException` | Custom exception (status code + error code + data) |

### API Methods
- `createPayout()` / `createCollection()` — create orders
- `getOrderStatus()` / `getSupportedSymbols()` — queries
- `getMerchantBalance()` / `getCryptoAddress()` — merchant features
- `verifyWebhook()` / `parseWebhook()` — webhook verification

### Model Structure
- `model/request/` — PayoutRequest, CollectionRequest, MerchantBalanceRequest, etc.
- `model/response/` — ApiResponse\<T\>, PayoutData, CollectionData, OrderDetails, etc.
- `model/webhook/` — WebhookEvent, OrderWebhookData, CollectWebhookData, etc.
- `model/` — enums: OrderStatus, OrderType, WebhookNotifyType

## Testing

**No standard test framework.** No JUnit/TestNG dependencies and no `src/test` directory.

Alternatives:
```bash
# Run signature test (main method)
java -cp target/classes com.xpaylabs.sdk.util.SignatureUtilTest

# Run comprehensive example (main method)
java -cp target/classes com.xpaylabs.sdk.example.XPayExample
```

## Notes

- Maven Central publishing plugins configured (maven-source-plugin, maven-javadoc-plugin, maven-gpg-plugin, central-publishing-maven-plugin)
- Lombok is compile-time only (`provided` scope)
- Only 3 runtime dependencies (jackson-databind, okhttp, jackson-core)
