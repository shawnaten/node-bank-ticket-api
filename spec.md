# API Specification

Some endpoints need authorization headers. For each you add an "Authorization" header key and some value. For basic auth the key is "Basic ENCODED_CREDENTIALS" where ENCODED_CREDENTIALS is the base64 encoding string "email:password". For token auth the key is "Bearer TOKEN" where TOKEN is the string returned from the auth endpoint. The auth endpoint should be thought of as authenticating a device for access. Your app should save the token and not require the user to login (make a call to auth) each time. In the examples below curl is used so we do not manually apply the authorization headers. You may have to depending on what tools you use in Android however.

```
# Copy and paste these into the terminal first
# Replace the values with whatever you like
# Emails can be accessed at https://maildrop.cc/

BASE_URL='http://'
EMPLOYEE_FIRST='Daniel'
EMPLOYEE_MIDDLE='Addison'
EMPLOYEE_LAST='Wurzbach'
EMPLOYEE_EMAIL='daniel.addison.wurzbach@maildrop.cc'

CUSTOMER_FIRST='Isabella'
CUSTOMER_MIDDLE='London'
CUSTOMER_LAST='Perrin'
CUSTOMER_EMAIL='isabella.london.perrin@maildrop.cc'

PASSWORD='password'
DEVICE_NAME='Test'
TOPIC='Test'
TEXT='Test'
```

# User Endpoints

## Create User

POST /user/create?first=FIRST&middle=MIDDLE&last=LAST&email=EMAIL&password=PASSWORD

```
# Create an employee and customer account

curl -X POST "$BASE_URL/user/create?first=$EMPLOYEE_FIRST&middle=$EMPLOYEE_MIDDLE&last=$EMPLOYEE_LAST&email=EMPLOYEE_EMAIL&password=PASSWORD"

curl -X POST "$BASE_URL/user/create?first=$CUSTOMER_FIRST&middle=$CUSTOMER_MIDDLE&last=$CUSTOMER_LAST&email=CUSTOMER_EMAIL&password=PASSWORD"
```

## Verify User
*The link will be provided in the email.*

GET /user/verify?token=TOKEN

## Delete User
*Needs basic authorization header.*

POST /user/delete

```
# Delete both users

curl -u $EMPLOYEE_EMAIL:$PASSWORD -X POST "$BASE_URL/user/delete"

curl -u $CUSTOMER_EMAIL:$PASSWORD -X POST "$BASE_URL/user/delete"
```

# Auth Endpoints

## Get Auth Token
*Needs basic authorization header.*
*Device name must be unique.*

GET /auth?device_name=DEVICE_NAME

```
# Save access token for first account

EMPLOYEE_TOKEN=`curl -u $EMPLOYEE_EMAIL:$PASSWORD "$BASE_URL/auth?device_name=$DEVICE_NAME" | perl -pe 's/{"access_token":"(.+)"}/$1/'`

CUSTOMER_TOKEN=`curl -u $EMAIL_2:$PASSWORD "$BASE_URL/auth?device_name=$DEVICE_NAME" | perl -pe 's/{"access_token":"(.+)"}/$1/'`
```

# Chat Endpoints
*All of these need bearer authorization header.*

## Customer Opens Ticket
POST /chat/support/start?topic=TOPIC

```
CHAT_ID_1=`curl -X POST -H "Authorization: Bearer $CUSTOMER_TOKEN" "$BASE_URL/chat/support/start?topic=$TOPIC" | perl -pe 's/{"id":"(.+)"}/$1/'`
```

## Employee or Customer Lists Tickets
GET /chat/support/list

```
curl -H "Authorization: Bearer $CUSTOMER_TOKEN" "$BASE_URL/chat/support/list"
```

## Employee or Customer Leaves Chat
POST /chat/support/remove

```
curl -X POST -H "Authorization: Bearer $CUSTOMER_TOKEN" "$BASE_URL/chat/support/remove"
```

# Message Endpoints
*All of these need bearer authorization header.*

## Send Message
POST /message/post?id=ID&text=TEXT

```
curl -X POST -H "Authorization: Bearer $CUSTOMER_TOKEN" "$BASE_URL/message/post?id=$CHAT_ID_1&text=$TEXT"
```

## List Messages
GET /message/list?id=ID

```
curl -H "Authorization: Bearer $CUSTOMER_TOKEN" "$BASE_URL/message/list?id=CHAT_ID_1"
```

# Broadcast Message Endpoints
*All of these need bearer authorization header.*

## Employee Sends Broadcast
POST /broadcast/post?topic=TOPIC&text=TEXT

```
curl -X POST -H "Authorization: Bearer $EMPLOYEE_TOKEN" "$BASE_URL/broadcast/post?topic=$TOPIC&text=$TEXT"
```

## List Messages
GET /message/list?id=ID

```
curl -H "Authorization: Bearer $CUSTOMER_TOKEN" "$BASE_URL/broadcast/list"
```
