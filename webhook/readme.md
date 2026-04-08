# Webhook API Documentation

## Overview

This documentation provides detailed information about the Webhook API, which listens for POST requests related to events like `nft`, `order`, and `transaction`. Below are the specifications for the endpoints, request formats, and data structures.

---

## Endpoint

**URL:**  
`https://yourdomain.com/webhook`

**Method:**  
`POST`

**Content-Type:**  
`application/json`

---

## General Request Structure

The webhook accepts a JSON payload with the following general structure:

```json
{
  "event": "string",
  "data": { ... },
  "timestamp": "number"
}
```

### Fields

| Field       | Type       | Description                                     | Required |
|-------------|------------|-------------------------------------------------|----------|
| `event`     | `string`   | The type of event triggering the webhook.      | Yes      |
| `data`      | `object`   | Contains event-specific details.               | Yes      |
| `timestamp` | `number`   | Unix timestamp (in seconds) when the event occurred. | Yes      |

---

## Events

### 1. Event: `nft`

#### Request Example

```json
{
  "event": "nfts",
  "data": {
    "nfts": [
      {
        "id": "string",
        "address": "string",
        "status": "string",
        "url": "string",
        "index": "number",
        "collection": {
          "address": "string"
        }
      }
    ],
    "transactions": [
      {
        "id": "string",
        "address_to": "string",
        "address_from": "string",
        "hash": "string",
        "network": "string"
      }
    ]
  },
  "timestamp": "number"
}
```

#### Fields for `nfts` (in `data`)

| Field        | Type   | Description                               | Required |
| ------------ | ------ | ----------------------------------------- | -------- |
| `id`         | string | Unique NFT identifier                     | Yes      |
| `address`    | string | NFT address                               | Yes      |
| `status`     | string | NFT status (`success`, `deploying`, etc.) | Yes      |
| `url`        | string | NFT URL                                   | Yes      |
| `index`      | number | Index in the collection                   | Yes      |
| `collection` | object | Collection data                           | Yes      |

#### Fields for `nft.collection`
| Field     | Type   | Description        | Required |
| --------- | ------ | ------------------ | -------- |
| `address` | string | Collection address | Yes      |

##### Fields for `transactions` (in `data`)

| Field          | Type   | Description           | Required |
| -------------- | ------ | --------------------- | -------- |
| `id`           | string | Transaction ID        | Yes      |
| `address_to`   | string | Recipient address     | Yes      |
| `address_from` | string | Sender address        | Yes      |
| `hash`         | string | Transaction hash      | Yes      |
| `network`      | string | Network (`ton`, etc.) | Yes      |


---

### 2. Event: `order`

#### Request Example

```json
{
  "event": "order",
  "data": {
    "id": "string",
    "amount": "number",
    "status": "string",
    "order_no": "string",
    "nft": "boolean",
    "transactions": [
      {
        "id": "string",
        "address_to": "string",
        "address_from": "string",
        "hash": "string",
      }
    ]
  },
  "timestamp": "number"
}
```

#### Fields for `order`

| Field           | Type       | Description                                    | Required |
|------------------|------------|------------------------------------------------|----------|
| `id`            | `string`   | Unique identifier for the order.              | Yes      |
| `amount`        | `number`   | Total amount for the order.                   | Yes      |
| `status`        | `string`   | created, cancelled,  confirmed, paid, failed, checke  | Yes      |
| `order_no`      | `string`   | Unique order number.                          | Yes      |
| `nft`           | `object`   | Details of the related NFT.                   | Yes      |
| `transactions`  | `array`    | List of transactions associated with the order. | No       |

##### Fields for `transactions` (in `order`)

Same as the fields in the `transactions` section of the `nft` event.

## Response Format

The server should respond with a status code to indicate the result:

- **200 OK**: The webhook processed successfully.
- **4xx/5xx**: Indicates an error (e.g., invalid payload or server issue).

---

## Notes

- Ensure the webhook URL is secured using HTTPS.
- Use authentication or signature validation to verify incoming requests.
- Handle retries in case of failed delivery.

