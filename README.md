# Tether API Documentation / የቴዘር API ሰነድ

This document outlines the API endpoints for the Tether application, covering user authentication and vehicle management functionalities. The API uses Express.js and follows RESTful conventions. All endpoints are hosted at `tether.qedron.com`.

ይህ ሰነድ የቴዘር መተግበሪያውን የAPI መግቢያዎችን ይገልፃል፣ የተጠቃሚ ማረጋገጫ እና የተሽከርካሪ አስተዳደር ተግባራትን ይሸፍናል። APIው Express.js ይጠቀማል እና RESTful ደንቦችን ይከተላል። ሁሉም መግቢያዎች በ`tether.qedron.com` ላይ ይስተናገዳሉ።

## Company Information / የኩባንያ መረጃ

**Company / ኩባንያ**

- Qedron Technologies PLC / ቄድሮን ቴክኖሎጂስ ኃላ/የተ/የግ/ማህበር

- Meskel Flower, Jemaa Building, Addis Ababa, Ethiopia / መስቀል ፍላወር፣ ጀማኣ ህንጻ፣ አዲስ አበባ፣ ኢትዮጵያ

## Base URL / መሰረታዊ URL

```
https://tether.qedron.com/
```

## Authentication / ማረጋገጫ

Some endpoints require authentication via a JWT token passed in the `Authorization` header as `Bearer <token>`. Certain endpoints also require specific roles (e.g., `ADMIN`), enforced by the `role` middleware. Tokens are signed with `JWT_SECRET` and expire after `JWT_EXPIRES_IN_SECONDS`.

አንዳንድ መግቢያዎች በ`Authorization` ራስጌ ውስጥ እንደ `Bearer <token>` የሚተላለፍ JWT ቶከን ማረጋገጫ ይፈልጋሉ። አንዳንድ መግቢያዎች በ`role` ሚድልዌር የሚተገበሩ ልዩ ሚናዎችን (ለምሳሌ፣ `ADMIN`) ይፈልጋሉ። ቶከኖች በ`JWT_SECRET` የተፈረሙ ሲሆን በ`JWT_EXPIRES_IN_SECONDS` ሰከንዶች ውስጥ ጊዜያቸው ያበቃል።

## Endpoints / መግቢያዎች

### User Authentication Routes / የተጠቃሚ ማረጋገጫ መንገዶች

#### 1. Delete User Account / የተጠቃሚ መለያ መሰረዝ

- **Endpoint**: `DELETE /api/auth/account`
- **Description**: Deletes the authenticated user's account and associated company, if any. / የተረጋገጠውን የተጠቃሚ መለያ እና ተያያዥ ኩባንያ፣ ካለ፣ ይሰርዛል።
- **Access**: Private (requires authentication)
- **Request Body**: None
- **Responses**:
  - `200 OK`: `{ data: "success" }`
  - `400 Bad Request`: `{ msg: "User does not exist" }` or `{ msg: "Demo user cannot be deleted" }`

#### 2. Get User Data / የተጠቃሚ ውሂብ ማግኘት

- **Endpoint**: `POST /api/auth/get`

- **Description**: Retrieves the authenticated user's data and updates their device list for notifications. / የተረጋገጠውን የተጠቃሚ ውሂብ ያገኛል እና ለማሳወቂያዎች የመሣሪያ ዝርዝራቸውን ያዘምናል።

- **Access**: Private (requires authentication)

- **Request Body**:

  ```json
  {
    "fcmToken": "string",
    "deviceId": "string",
    "deviceInfo": "object"
  }
  ```

- **Responses**:

  - `200 OK`:

    ```json
    {
      "token": "string",
      "id": "string",
      "name": "string",
      "phone": "string",
      "email": "string",
      "isEmailVerified": "boolean",
      "isPhoneVerified": "boolean",
      "verifyPhone": "boolean",
      "verifyEmail": "boolean",
      "language": "string",
      "avatar": "string",
      "role": "string",
      "companyId": "string|null",
      "companyName": "string|null"
    }
    ```

  - `400 Bad Request`: `{ msg: "User does not exist" }` or `{ msg: "Something went wrong saving user devices" }`

#### 3. Edit User / ተጠቃሚን ማስተካከል

- **Endpoint**: `PUT /api/auth/edit`

- **Description**: Updates the authenticated user's information. Only the user or an admin can edit. / የተረጋገጠውን የተጠቃሚ መረጃ ያዘምናል። ተጠቃሚው ወይም አስተዳዳሪ ብቻ ማስተካከል ይችላል።

- **Access**: Private (requires authentication)

- **Request Body**:

  ```json
  {
    "name": "string",
    "passphrase": "string",
    "language": "string",
    "journalEntries": "array",
    "financialProjections": "array"
  }
  ```

- **Responses**:

  - `200 OK`: Updated user data (JSON).
  - `400 Bad Request`: `{ msg: "you're not authorised to make changed to this user." }` or `{ msg: "Something went wrong saving the user" }`

#### 4. Login User / ተጠቃሚ መግባት

- **Endpoint**: `POST /api/auth/login`

- **Description**: Authenticates a user with phone or email and password, returning a JWT token. / ተጠቃሚን በስልክ ወይም ኢሜይል እና የይለፍ ቃል ያረጋግጣል፣ JWT ቶከን ይመልሳል።

- **Access**: Public

- **Request Body**:

  ```json
  {
    "phone": "string|null",
    "email": "string|null",
    "password": "string"
  }
  ```

- **Responses**:

  - `200 OK`:

    ```json
    {
      "token": "string",
      "id": "string",
      "name": "string",
      "phone": "string",
      "email": "string",
      "isEmailVerified": "boolean",
      "isPhoneVerified": "boolean",
      "verifyPhone": "boolean",
      "verifyEmail": "boolean",
      "language": "string",
      "avatar": "string",
      "role": "string",
      "companyId": "string|null",
      "companyName": "string|null"
    }
    ```

  - `400 Bad Request`: `{ msg: "Please enter all fields" }`, `{ msg: "User does not exist" }`, `{ msg: "Invalid credentials" }`, or `{ msg: "Couldnt sign the token" }`

#### 5. Register User / ተጠቃሚ መመዝገብ

- **Endpoint**: `POST /api/users/register`

- **Description**: Registers a new user, creates a company, and sends verification email/OTP. / አዲስ ተጠቃሚ ይመዘግባል፣ኩባንያ ይፈጥራል፣እና የማረጋገጫ ኢሜይል/OTP ይልካል።

- **Access**: Public

- **Request Body**:

  ```json
  {
    "name": "string",
    "phone": "string|null",
    "email": "string|null",
    "password": "string",
    "passphrase": "string",
    "language": "string"
  }
  ```

- **Responses**:

  - `200 OK`: (Similar to Get User Data response).
  - `400 Bad Request`: `{ error: "Please enter all fields" }`, `{ error: "Email already taken" }`, `{ error: "Phone number already taken" }`, `{ error: "Something went wrong with bcrypt" }`, `{ error: "Something went wrong hashing the password" }`, `{ error: "Something went wrong saving the user" }`, or `{ error: "Company owner conflict error" }`

#### 6. Resend Verification / ማረጋገጫ እንደገና መላክ

- **Endpoint**: `POST /api/users/resend-verification`

- **Description**: Resends verification email or phone OTP to the user. / የማረጋገጫ ኢሜይል ወይም የስልክ OTP ወደ ተጠቃሚው እንደገና ይልካል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "phone": "string|null",
    "email": "string|null"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `400 Bad Request`: `{ error: "Please provide phone or email" }`, `{ error: "User does not exist" }`, `{ error: "Phone number already verified" }`, or `{ error: "Email already verified" }`

#### 7. Forgot Password / የይለፍ ቃል መርሳት

- **Endpoint**: `POST /api/users/forgot-password`

- **Description**: Initiates password reset by sending an OTP (phone) or token (email). / በስልክ OTP ወይም በኢሜይል ቶከን መላክ የይለፍ ቃል ዳግም ማስጀመርን ይጀምራል።

- **Access**: Public

- **Request Body**:

  ```json
  {
    "phone": "string|null",
    "email": "string|null"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `400 Bad Request`: `{ error: "Please enter all fields" }` or `{ error: "User does not exist" }`

#### 8. New Identifier / አዲስ መለያ

- **Endpoint**: `POST /api/users/new-identifier`

- **Description**: Initiates process to change user's phone or email by sending a verification OTP/token. / የማረጋገጫ OTP ወይም ቶከን በመላክ የተጠቃሚ ስልክ ወይም ኢሜይል መቀየር ሂደት ይጀምራል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "phone": "string|null",
    "email": "string|null"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `204 No Content`: Verification sent.
  - `400 Bad Request`: `{ msg: "Please enter data" }` or `{ error: "User does not exist" }`

#### 9. Verify Email / ኢሜይል ማረጋገጥ

- **Endpoint**: `POST /api/users/verify-email`

- **Description**: Verifies user email using a token. / በቶከን የተጠቃሚ ኢመል ያረግግጣል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "token": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `401 Unauthorized`: `{ error: "invalid" }` or `{ msg: "User not found" }`
  - `400 Bad Request`: `{ msg: "Please enter all fields" }`

#### 10. Verify Phone / ስልክ ማረጋገጥ

- **Endpoint**: `POST /api/users/verify-phone`

- **Description**: Verifies user's phone using an OTP. / በOTP የተጠቃሚ ስልክ ያረግግጣል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "otp": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `401 Unauthorized`: `{ error: "invalid" }` or `{ msg: "User not found" }`
  - `400 Bad Request`: `{ msg: "Please enter all fields" }`

#### 11. Verify Email Reset Password Token / የኢሜይል የይለፍ ቃል ዳግም ማስጀመሪያ ቶከን ማረጋግጥ

- **Endpoint**: `POST /api/users/verify-email-reset-password-token`

- **Description**: Verifies token for email-based password reset. / ለኢሜይል ላይ የተመሰረተ የይለፍ ቃል ዳግም ማስጀመሪያ ቶከን ያረግግጣል።

- **Access**: Public

- **Request Body**:

  ```json
  {
    "token": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `401 Unauthorized`: `{ error: "invalid" }`, or `{ msg: "User not found" }`
  - `400 Bad Request`: `{ msg: "Please enter all fields" }`

#### 12. Verify Phone Reset Password OTP / የስልክ የይለፍ ቃል ዳግም ማስጀመሪያ OTP ማረጋገጥ

- **Endpoint**: `POST /api/users/verify-phone-reset-password-otp`

- **Description**: Verifies OTP for phone-based password reset. / ለስልክ ላይ የተመሰረተ የይለፍ ቃል ዳግም ማስጀመሪያ OTP ያረግግታል።

- **Access**: Public

- **Request Body**:

  ```json
  {
    "otp": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `401 Unauthorized`: `{ error: "invalid" }`, or `{ msg: "User not found" }`
  - `400 Bad Request`: `{ msg: "Please enter all fields" }`

#### 13. Reset Password with Email / በኢመል የይለፍ ቃል ዳግም ማስጀመር

- **Endpoint**: `POST /api/users/email-reset-password`

- **Description**: Resets password using a verified email token. / በተረጋገጠ የኢመል ቶከን የይለፍ ቃል ዳግም ያስጀምራል።

- **Access**: Public

- **Request Body**:

  ```json
  {
    "token": "string",
    "password": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `401 Unauthorized`: `{ error: "invalid" }` or `{ msg: "User not found" }`
  - `400 Bad Request`: `{ msg: "Please enter all fields" }`, `{ msg: "Something went wrong with bcrypt" }`, or `{ msg: "Something went wrong hashing the password" }`

#### 14. Reset Password with Phone / በስልክ የይለፍ ቃል ዳግም ማስጀመር

- **Endpoint**: `POST /api/users/phone-reset-password`

- **Description**: Resets password using a verified phone OTP. / በተረጋገጠ የስልክ OTP የይለፍ ቃል ዳግም ያስጀምራል።

- **Access**: Public

- **Request Body**:

  ```json
  {
    "otp": "string",
    "password": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `401 Unauthorized`: `{ error: "invalid" }` or `{ msg: "User not found" }`
  - `400 Bad Request`: `{ msg: "Please enter all fields" }`, `{ msg: "Something went wrong with bcrypt" }`, or `{ msg: "Something went wrong hashing the password" }`

#### 15. Change Password / የይለፍ ቃል መቀየር

- **Endpoint**: `POST /api/users/change-password`

- **Description**: Changes the authenticated user's password after verifying the current password. / የአሁኑን የይለፍ ቃል ካረጋገጠ በኋላ የተረጋገጠውን የተጠቃሚ የይለፍ ቃል ይቀይራል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "password": "string",
    "newPassword": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `401 Unauthorized`: `{ msg: "User not found" }`, `{ msg: "Invalid credentials" }`, or `{ msg: "Changing password not allowed for demo user" }`
  - `400 Bad Request`: `{ msg: "Please enter all fields" }`, `{ msg: "Something went wrong with bcrypt" }`, `{ msg: "Something went wrong hashing the password" }`, or `{ msg: "Error saving new password" }`

#### 16. Change Language / ቋንቋ መቀየር

- **Endpoint**: `POST /api/auth/language`

- **Description**: Updates the user's language preference. / የተጠቃሚውን የቋንቋ ምርጫ ያዘምናል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "language": "string"
  }
  ```

- **Responses**:

  - `200 OK`: Updated user data (JSON).
  - `400 Bad Request`: `{ msg: "Unsupported language" }`, `{ msg: "User does not exist" }`, or `{ msg: "User not found" }`

#### 17. Check Phone Number / ስልክ ቁጥር መፈተሽ

- **Endpoint**: `POST /api/auth/phone`

- **Description**: Checks if a phone number is registered. / የስልክ ቁጥር መመዝገቡን ይፈትሻል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "phone": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ isRegistered: true }` or `{ isRegistered: false }`
  - `400 Bad Request`: `{ msg: "Phone is empty" }` or `{ msg: "Error occurred" }`

#### 18. Check Email Address / የኢሜይል አድራሻ መፈተሽ

- **Endpoint**: `POST /api/auth/email`

- **Description**: Checks if an email address is registered. / የኢሜይል አድራሻ መመዝገቡን ይፈትሻል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "email": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ isRegistered: true }` or `{ isRegistered: false }`
  - `400 Bad Request`: `{ msg: "Email is empty" }` or `{ msg: "Error occurred" }`

#### 19. Change Email / ኢሜይል መቀየር

- **Endpoint**: `POST /api/users/change-email`

- **Description**: Updates the user's email address after token verification. / ቶከን ከተረጋገጠ በኋላ የተጠቃሚውን ኢሜይል አድራሻ ያዘምናል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "token": "string",
    "email": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `401 Unauthorized`: `{ msg: "Error occurred" }`
  - `400 Bad Request`: `{ msg: "Please enter all fields" }`

#### 20. Change Phone / ስልክ መቀየር

- **Endpoint**: `POST /api/users/change-phone`

- **Description**: Updates the user's phone number after token verification. / ቶከን ከተረጋገጠ በኋላ የተጠቃሚውን ስልክ ቁጥር ያዘምናል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "token": "string",
    "phone": "string"
  }
  ```

- **Responses**:

  - `200 OK`: `{ data: "success" }`
  - `401 Unauthorized`: `{ msg: "Error occurred" }`
  - `400 Bad Request`: `{ msg: "Please enter all fields" }`

### Vehicle Management Routes / የተሽከርካሪ አስተዳደር መግቢያዎች

#### 1. Get Single Vehicle / ነጠላ ተሽከርካሪ ማግኘት

- **Endpoint**: `GET /api/vehicles/p`
- **Description**: Retrieves details of a vehicle by ID, including driver. / በመለያ ቁጥር የተሽከርካሪ ዝርዝሮችን ሾፌሩን ጨምሮ ያገኛል።
- **Access**: Private
- **Query Parameters**: `id` (string, required)
- **Responses**:
  - `200 OK`: Vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "No vehicles found" }`

#### 2. Get All User Vehicles / ሁሉም የተጠቃሚ ተሽከርካሪዎችን ማግኘት

- **Endpoint**: `GET /api/vehicles`
- **Description**: Retrieves all vehicles associated with the user, including logs for the past 24 hours. / ከተጠቃሚው ጋር የተያያዙ ሁሉም ተሽከርካሪን ከመጨረሻ 24 ሰዓታት መዝገቦች ጋር ያገኛል።
- **Access**: Private
- **Responses**:
  - `200 OK`: Array of vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "No vehicles registered" }`

#### 3. Edit Vehicle / ተሽከርካሪ ማስተካከል

- **Endpoint**: `PUT /api/vehicles/edit1/:vehicleId`

- **Description**: Updates vehicle details (admin only). / የተሽከርካሪ ዝርዝሮችን ያስተካክላል (አስተዳዳሪ ብቻ)።

- **Access**: Private (admin only)

- **Request Body**:

  ```json
  {
    "brand": "string",
    "model": "string",
    "type": "string",
    "tag": "string",
    "color": "string",
    "colorHex": "string",
    "note": "string",
    "canOwnerGetDisconNotif": "boolean",
    "canOwnerGetSpeedLimitNotif": "boolean",
    "canOwnerGetFenceNotif": "boolean",
    "canOwnerGetParkNotif": "boolean",
    "canDriverGetDisconNotif": "boolean",
    "canDriverGetSpeedLimitNotif": "boolean",
    "canDriverGetFenceNotif": "boolean",
    "canDriverGetParkNotif": "boolean",
    "canOwnerSpeedLimit": "boolean",
    "canOwnerFence": "boolean",
    "canOwnerPark": "boolean",
    "canOwnerKill": "false",
    "canOwnerTrack": "boolean",
    "canDriverSpeedLimit": "boolean",
    "canDriverFence": "boolean",
    "canDriverPark": "boolean",
    "canDriverKill": "false",
    "canDriverTrack": "boolean",
    "ownerTetherNotificationType": "string",
    "ownerFenceNotificationType": "string",
    "ownerSpeedNotificationType": "string",
    "ownerParkNotificationType": "string",
    "driverTetherNotifType": "string",
    "driverFenceNotifType": "string",
    "driverSpeedNotifType": "string",
    "driverParkNotifType": "string",
    "currentBalance": "number",
    "currentKMs": "number",
    "journalEntries": "array",
    "financeEntries": "array"
  }
  ```

- **Responses**:
- `200 OK`: Updated vehicle data (JSON).
- `400 Bad Request`: `{ msg: "not authorized" }` or `{ msg: "failed to save" }`

#### 4. Get Vehicle Logs / የተሽከርካሪ መዝገቦተ ማግኘት
- **Endpoint**: `GET /api/vehicles/log/:vehicleId/:start/:end`
- **Description**: Retrieves logs for a vehicle within a date range. / በተወሰነ ጊዜ ክልል ውስጥ የተሽከርካሪ መዝገቦችን ያገኛል።
- **Access**: Private
- **Parameters**: `vehicleId`, `start` (date), `end` (date)
- **Responses**:
- `200 OK`: Array of log data (JSON).
- `400 Bad Request`: No logs found

#### 5. Change Driver / ሾፌር መቀየር
- **Endpoint**: `PUT /api/vehicles/driver`
- **Description**: Assigns or removes a driver for a vehicle. / ለተሽከርካሪ ሾፌር ይመድባል ወይም ያስወግዳል።
- **Access**: Private
- **Request Body**:
```json
{
  "vehicleId": "string",
  "phone": "string"
}
```

- **Responses**:
  - `200 OK`: Updated vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "Missing fields" }`, `{ msg: "Vehicle not found" }`, `{ msg: "Subscription expired" }`, `{ msg: "Not authorized" }`, or `{ msg: "Not found" }`

#### 6. Accept Driver Assignment / የሾፌር መሰየሚያ መቀበል

- **Endpoint**: `PUT /api/vehicles/accept`

- **Description**: Accepts or declines a driver assignment. / የሾፌር መሰየሚያን ይቀበላል ወይም ውድቅ ያደርጋል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "vehicleId": "string",
    "code": "string",
    "accept": "boolean"
  }
  ```

- **Responses**:

  - `200 OK`: Updated vehicle data (JSON).

- `400 Bad Request`: `{ msg: "Missing fields" }`, `{ msg: "Vehicle not found" }`, `{ msg: "Subscription expired" }`, `{ msg: "Not assigned" }`, or `{ msg: "Invalid code" }`

#### 7. Set Parked Status / የመኪና ማቆሚያ ሁኔታ ማዘጋጀት

- **Endpoint**: `PUT /api/vehicles/park`

- **Description**: Sets the parked status for a vehicle, with optional auto-kill functionality. / ለተሽከርካሪ የማቆሚያ ሁኔታን ያዘጋጃል፣ ከአማራጭ ራስ-ሰር መግደል ተግባር ጋር።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "vehicleId": "string",
    "isParked": "boolean",
    "notificationType": "string",
    "autoKill": "boolean"
  }
  ```

- **Responses**:

  - `200 OK`: Updated vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "Missing fields" }`, `{ msg: "Not found" }`, `{ msg: "Expired" }`, or `{ msg: "Not authorized" }`

#### 8. Set Tether Status / የቴዘር ሁኔታ ማዘጋጀት

- **Endpoint**: `PUT /api/vehicles/tether`

- **Description**: Sets the tether status for a vehicle. / ለተሽከርካሪ የቴዘር ሁኔታን ያዘጋጃል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "vehicleId": "string",
    "isTethered": "boolean",
    "notificationType": "string"
  }
  ```

- **Responses**:

  - `200 OK`: Updated vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "Missing fields" }`, `{ msg: "Not found" }`, `{ msg: "Expired" }`, or `{ msg: "Not authorized" }`

#### 9. Set Engine Status / የሞተር ሁኔታ ማዘጋጀት

- **Endpoint**: `PUT /api/vehicles/engine`

- **Description**: Toggles the vehicle's engine status (fuel cut) via device socket. / በመሣሪያ ሶኬት በኩል የተሽከርካሪውን የሞተር ሁኔታ (ነዳጅ መቁረጥ) ይቀያይራል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "vehicleId": "string",
    "isFuelCut": "false"
  }
  ```

- **Responses**:

  - `200 OK`: Updated vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "Missing fields" }`, `{ msg: "User not found" }`, `{ msg: "Vehicle not found" }`, `{ msg: "Subscription expired" }`, `{ msg: "Device not found" }`, `{ msg: "Session update failed" }`, or `{ msg: "Not authorized" }`
  - `404 Not Found`: `{ msg: "Unable to send command" }`

#### 10. Set Speed Limit Status / የፍጥነት ገደብ ሁኔታ ማዘጋጀት

- **Endpoint**: `PUT /api/vehicles/speed`

- **Description**: Sets the speed limit status for a vehicle. / ለተሽከርካሪ የፍጥነት ገደብ ሁኔታን ያዘጋጃል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "vehicleId": "string",
    "isOn": "boolean",
    "notificationType": "string"
  }
  ```

- **Responses**:

  - `200 OK`: Updated vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "Missing fields" }`, `{ msg: "Not found" }`, `{ msg: "Expired" }`, or `{ msg: "Not authorized" }`

#### 11. Set Speed Limit Value / የፍጥነት ገደብ እሴት ማዘጋጀት

- **Endpoint**: `PUT /api/vehicles/speedlimit`

- **Description**: Sets the speed limit value for a vehicle. / ለተሽከርካሪ የፍጥነት ገደብ እሴትን ያዘጋጃል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "vehicleId": "string",
    "speed": "number"
  }
  ```

- **Responses**:

  - `200 OK`: Updated vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "Missing fields" }`, `{ msg: "Not found" }`, `{ msg: "Expired" }`, or `{ msg: "Not authorized" }`

#### 12. Set Geofence / ጂኦፌንስ ማዘጋጀት

- **Endpoint**: `PUT /api/vehicles/geofence`

- **Description**: Sets the geofence for a vehicle. / ለተሽከርካሪ ጂኦፌንስን ያዘጋጃል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "vehicleId": "string",
    "fence": "array"
  }
  ```

- **Responses**:

  - `200 OK`: Updated vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "Missing fields" }`, `{ msg: "Not found" }`, `{ msg: "Expired" }`, or `{ msg: "Not authorized" }`

#### 13. Get Payments / ክፍያዎችን ማግኘት

- **Endpoint**: `GET /api/vehicles/payments`
- **Description**: Retrieves all invoices for the authenticated user. / ለተረጋገጠው ተጠቃሚ ሁሉንም ደረሰኞች ያገኛል።
- **Access**: Private
- **Responses**:
  - `200 OK`: Array of invoice data (JSON).
  - `400 Bad Request`: `{ msg: "No invoices found" }`

#### 14. Confirm Payment / ክፍያ ማረጋገጥ

- **Endpoint**: `POST /api/vehicles/confirm_payment`

- **Description**: Confirms a payment for an invoice and notifies via Telegram. / ለደረሰኝ ክፍያን ያረጋግጣል እና በቴሌግራም ያሳውቃል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "invoiceId": "string",
    "bankName": "string",
    "depositedBy": "string",
    "transactionNumber": "string",
    "transactionDate": "number",
    "transactionAmount": "number"
  }
  ```

- **Responses**:

  - `200 OK`: Updated invoice data (JSON).
  - `400 Bad Request`: `{ msg: "Missing fields" }`, `{ msg: "Invoice not found" }`, or `{ msg: "Not authorized" }`

#### 15. Add Funds / ገንዘብ መጨመር

- **Endpoint**: `POST /api/vehicles/add_funds`

- **Description**: Creates a new invoice to add funds (minimum 1000 birr). / ገንዘብ ለመጨመር አዲስ ደረሰኝ ይፈጥራል (ቢያንስ 1000 ብር)።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "amount": "number"
  }
  ```

- **Responses**:

  - `200 OK`: Created invoice data (JSON).
  - `400 Bad Request`: `{ msg: "Amount less than 1000 birr" }`, `{ msg: "No owner" }`, or `{ msg: "Error occurred" }`

#### 16. Edit Vehicle / ተሽከርካሪ ማስተካከል

- **Endpoint**: `PUT /api/vehicles/edit`

- **Description**: Updates vehicle details for the authenticated user (owner only). / ለተረጋገጠው ተጠቃሚ (ባለቤት ብቻ) የተሽከርካሪ ዝርዝሮችን ያስተካካል።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "vehicleId": "string",
    "brand": "string",
    "model": "string",
    "type": "string",
    "tag": "string",
    "color": "string",
    "colorHex": "string",
    "currentBalance": "number",
    "currentKMs": "number",
    "journalEntries": "array"
  }
  ```

- **Responses**:

  - `200 OK`: Updated vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "Not authorized" }` or `{ msg: "Failed to save" }`

#### 17. Get Projections / ትንበያዎችን ማግኘት

- **Endpoint**: `GET /api/vehicles/projections`
- **Description**: Retrieves financial projections configuration. / የፋይናንስ ትንበያ ውቅረት ያገኛል።
- **Access**: Private
- **Responses**:
  - `200 OK`: Projections data (JSON).

#### 18. Add Journal Entry 

- **Endpoint**: `PUT /api/vehicles/add_entry`

- **Description**: Adds a journal entry to a vehicle (owner only). / ለተሽከርካሪ journal ይጨምራል (ባለቤት ብቻ)።

- **Access**: Private

- **Request Body**:

  ```json
  {
    "vehicleId": "string",
    "type": "string",
    "name": "string",
    "amount": "number",
    "date": "date"
  }
  ```

- **Responses**:

  - `200 OK`: Updated vehicle data (JSON).
  - `400 Bad Request`: `{ msg: "Not authorized" }` or `{ msg: "Error occurred" }`

## Error Handling

All endpoints return a `400 Bad Request` status with a JSON object containing an error message (`msg` or `error`) when an error occurs, unless specified as `401 Unauthorized`. Common errors include missing fields, unauthorized access, expired subscriptions, or resource not found.

ሁሉም መግቢያዎች ስህተት ሲከሰት `400 Bad Request` ሁኔታን ከJSON ነገር ጋር የስህተት መልእክት (`msg` ወይም `error`) ይመልሳሉ፣ ካልተገለጸ በስተቀር `401 Unauthorized` ካልሆነ በስተቀር። የተለመዱ ስህተቶች የጎደሉ መስኮች፣ ፈቃደኛ ያልሆነ መዳረሻ፣ ጊዜው ያለፈባቸው ምዝገባዎች፣ ወይም ምንጭ አለመገኘትን ያካትታሉ።

## Notes / ማስታወሻዎች

- Device management limits the number of stored devices to 100, retaining the 50 most recent. / የመሣሪያ አስተዳደር የተከማቸ መሣሪያዎች ቁጥር እስከ 100 ይወሰናል፣ በጣም የቅርብ ጊዜ 50ውን ይይዛል።
- Vehicle-related operations check for subscription status (`ACTIVE` or `EXPIRED`). / ከተሽከርካሪ ጋር የሚያያዙ ሥራዎች የምዝገባ ሁኔታን (`ACTIVE` ወይም `EXPIRED`) ይሶቃሉ።
- The `toggleEngine` function interacts with a device socket for real-time engine control. / `toggleEngine` ቀጥታ ሞተር መቆጣጠሪያ ከመሣሪያ ሶኬት ጋር ይሠራል።
- Demo users (phone: `900000000`) have restricted actions (e.g., cannot delete or change password). / የማሳያ ተጠቃሚዎች (ስልክ፡ `900000000`) የተገደቡ ተግባራት አሏቸው (ለምሳሌ፣ መሰረዝ ወይም የይለፍ ቃል መቀየር አይችሉም)
