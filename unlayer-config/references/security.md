# HMAC Security — All Languages

Identity verification prevents impersonation of logged-in users. HMAC signature must be generated **server-side** using your project secret key.

**Prerequisite:** End-User Identification must be set up first.

## Server-Side Signature Generation

### Node.js

```javascript
const crypto = require('crypto');

const signature = crypto
  .createHmac('sha256', 'YOUR_PROJECT_SECRET')
  .update(String(userId))
  .digest('hex');
```

### Ruby on Rails

```ruby
require 'openssl'

signature = OpenSSL::HMAC.hexdigest(
  'sha256',
  'YOUR_PROJECT_SECRET',
  user.id.to_s
)
```

### PHP

```php
$signature = hash_hmac(
  'sha256',
  $user->id,
  'YOUR_PROJECT_SECRET'
);
```

### Python / Django

```python
import hmac
import hashlib

signature = hmac.new(
    b'YOUR_PROJECT_SECRET',
    bytes(str(request.user.id), encoding='utf-8'),
    digestmod=hashlib.sha256
).hexdigest()
```

## Client-Side Usage

```javascript
unlayer.init({
  user: {
    id: 1,                          // Required — unique user identifier
    signature: signatureFromServer,  // HMAC signature from server
    name: 'John Doe',               // Optional
    email: 'john.doe@acme.com',     // Optional
  },
});
```

## End-User Identification

```javascript
unlayer.init({
  user: {
    id: 1,           // Required — unique identifier
    name: 'John Doe', // Optional — display name
    email: 'john@acme.com', // Optional
  },
});
```

**Use cases for user identification:**
- Save personalized blocks per user
- Track file uploads per user
- Enable File Manager functionality
- HMAC security verification

> Docs: https://docs.unlayer.com/builder/security
> Docs: https://docs.unlayer.com/builder/end-user-identification
