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
  "event": "nft",
  "data": {
    "id": "string",
    "address": "string",
    "status": "string",
    "url": "string",
    "index": "number",
    "collection_address": "string",
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

#### Fields for `nft`

| Field                 | Type       | Description                                     | Required |
|-----------------------|------------|-------------------------------------------------|----------|
| `id`                 | `string`   | Unique identifier for the NFT.                | Yes      |
| `address`            | `string`   | Address of the NFT.                           | Yes      |
| `status`             | `string`   | Status of the NFT (e.g., `minted`, `sold`).   | Yes      |
| `url`                | `string`   | URL to view the NFT.                          | Yes      |
| `index`              | `number`   | Index of the NFT in the collection.           | Yes      |
| `collection_address` | `string`   | Address of the collection the NFT belongs to. | Yes      |
| `transactions`       | `array`    | List of transactions associated with the NFT. | No       |

##### Fields for `transactions` (in `nft`)

| Field            | Type       | Description                              | Required |
|------------------|------------|------------------------------------------|----------|
| `id`             | `string`   | Unique identifier for the transaction.  | Yes      |
| `address_to`     | `string`   | Recipient address.                       | Yes      |
| `address_from`   | `string`   | Sender address.                          | Yes      |
| `hash`           | `string`   | Hash of the transaction.                 | Yes      |

---

### 2. Event: `order`

#### Request Example

```json
{
  "event": "order",
  "data": {
    "id": "string",
    "amount": "number",
    "order_no": "string",
    "nft": {
      "id": "string",
      "address": "string",
      "status": "string",
      "url": "string",
      "index": "number",
      "collection_address": "string"
    },
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
| `order_no`      | `string`   | Unique order number.                          | Yes      |
| `nft`           | `object`   | Details of the related NFT.                   | Yes      |
| `transactions`  | `array`    | List of transactions associated with the order. | No       |

##### Fields for `nft` (in `order`)

Same as the fields in the `nft` event.

##### Fields for `transactions` (in `order`)

Same as the fields in the `transactions` section of the `nft` event.

---

### 3. Event: `transaction`

#### Request Example

```json
{
  "event": "transaction",
  "data": {
    "id": "string",
    "address_to": "string",
    "address_from": "string",
    "hash": "string",
  },
  "timestamp": "number"
}
```

#### Fields for `transaction`

| Field           | Type       | Description                                   | Required |
|------------------|------------|-----------------------------------------------|----------|
| `id`            | `string`   | Unique identifier for the transaction.       | Yes      |
| `address_to`    | `string`   | Recipient address.                           | Yes      |
| `address_from`  | `string`   | Sender address.                              | Yes      |
| `hash`          | `string`   | Hash of the transaction.                     | Yes      |

---

## Response Format

The server should respond with a status code to indicate the result:

- **200 OK**: The webhook processed successfully.
- **4xx/5xx**: Indicates an error (e.g., invalid payload or server issue).

---

## Notes

- Ensure the webhook URL is secured using HTTPS.
- Use authentication or signature validation to verify incoming requests.
- Handle retries in case of failed delivery.

