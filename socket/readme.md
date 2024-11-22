
# Socket Event `orders`

The `orders` event is used for real-time order updates. It supports transaction verification using a `signature` and `auth_date` to prevent duplicate operations.

## Received Object Format

The event returns an object in the following format:

```json
{
  "amount": "1500",
  "auth_date": "2024-11-22T09:59:00Z",
  "created_at": "2024-11-22T10:00:00Z",
  "id": 1,
  "item": {
    "coins": "100",
    "name": "Name"
  },
  "order_no": "123456",
  "payment": "aeon",
  "status": "completed",
  "token": "usdt",
  "user_id": 1,
  "signature": "e4bfb86710b317a3d8f746eac8f4d4d6d6a2bc11"
}
```

### Object Fields

| Field         | Data Type | Description                                      |
|---------------|-----------|--------------------------------------------------|
| `amount`      | `string`  | The total amount for the order.                  |
| `auth_date`   | `string`  | The authorization timestamp, used to verify the transaction. |
| `created_at`  | `string`  | The timestamp when the order was created, in ISO 8601 format. |
| `id`          | `number`  | A unique identifier for the order.               |
| `item`        | `object`  | The item details in the order.                   |
| `item.coins`  | `string`  | The number of coins associated with the item.    |
| `item.name`   | `string`  | The name of the item.                            |
| `order_no`    | `string`  | The order number.                                |
| `payment`     | `string`  | The payment method. Possible values: `aeon`, etc. |
| `status`      | `string`  | The status of the order. Possible values: `completed`, `pending`, `canceled`. |
| `token`       | `string`  | The token used for payment (e.g., `usdt`).       |
| `user_id`     | `number`  | A unique identifier for the user who placed the order. |
| `signature`   | `string`  | The signature used to verify the authenticity and security of the transaction. |

## Transaction Verification

### Verification Algorithm

1. **Auth Date Validation (`auth_date`)**:  
   Ensure that the `auth_date` is not older than a set time window (e.g., 5 minutes) from the current time.

2. **Signature Verification (`signature`)**:  
   To generate the signature, the keys in the object must be sorted alphabetically, and then the signature is computed using HMAC SHA256. For example:

   ```text
   signature = HMAC(secret_key, `${amount}${auth_date}${created_at}${id}${item.coins}${item.name}${order_no}${payment}${status}${token}${user_id}`)
   ```

3. Compare the computed signature with the one provided in the object.

### JavaScript Example Code

```javascript
const crypto = require('crypto');

function verifyOrderSignature(order, secretKey) {
    const { amount, auth_date, created_at, id, item, order_no, payment, status, token, user_id, signature } = order;

    // Sorting object keys alphabetically
    const dataToSign = `${amount}${auth_date}${created_at}${id}${item.coins}${item.name}${order_no}${payment}${status}${token}${user_id}`;

    // Generate the signature
    const computedSignature = crypto
        .createHmac('sha256', secretKey)
        .update(dataToSign)
        .digest('hex');

    // Signature comparison
    return computedSignature === signature;
}

// Example verification
const order = {
    amount: "1500",
    auth_date: "2024-11-22T09:59:00Z",
    created_at: "2024-11-22T10:00:00Z",
    id: 1,
    item: {
        coins: "100",
        name: "Name"
    },
    order_no: "123456",
    payment: "aeon",
    status: "completed",
    token: "usdt",
    user_id: 1,
    signature: "e4bfb86710b317a3d8f746eac8f4d4d6d6a2bc11"
};

const secretKey = "your_secret_key";
if (verifyOrderSignature(order, secretKey)) {
    console.log("Signature verified. Order is authentic.");
} else {
    console.log("Signature verification failed. The order may be fraudulent.");
}
```

## Event Handling Example

### Client-Side

```javascript
socket.on('orders', (order) => {
    if (verifyOrderSignature(order, secretKey)) {
        console.log('New order received:', order);
    } else {
        console.warn('Signature verification failed for the order!');
    }
});
```

## Usage

Adding `auth_date` and `signature` allows you to:

- Prevent duplicate transactions.
- Ensure the authenticity of the order data.
- Enhance security by preventing unauthorized data modification.

### Possible Errors

- **Signature Verification Failed**: Ensure that the correct secret key and HMAC algorithm are used.
- **Expired Transaction**: Check if the `auth_date` is within the allowed time window from the current time.
