# TwilioChat_iOS
Twilio iOS SDK Implementaion  

Chat one-one  
Chat One-Many (Group) 
- Add Participant 
- Remove Participant   

Send Attachment Image  

Android - iOS Tested 
iOS - iOS Tested 
iOS - Android Tested  

React to Message, 
Delete a Message 
Read, Delivered, Sent  

Delete a Conversation  
Unread Messages Filter  

- Token URL = You need to take this from Twilio Console &lt;Keep It Secret> (You can edit it in Environment Variables in Edit Scheme) the variable name is `ACCESS_TOKEN_SERVICE_URL`


## Getting Started

Welcome to the Conversations Demo application. This application demonstrates a basic conversations client application with the ability to create and join conversations, add other participants into the conversations and exchange messages.

What you'll minimally need to get started:
- Clone this repo
- You need to take this from Twilio Console &lt;Keep It Secret> (You can edit it in Environment Variables in Edit Scheme) the variable name is `ACCESS_TOKEN_SERVICE_URL`


### Set the value of `ACCESS_TOKEN_SERVICE_URL`

Set the value of `ACCESS_TOKEN_SERVICE_URL` in scheme settings to point to a valid Access-Token server.
It should be an URL of the backend function (see below) that would generate access tokens for your app.
This function will be given `identity` and `password` as query parameters.

```
ACCESS_TOKEN_SERVICE_URL=https://some.token-generator.url/
# The resulting url will be https://some.token-generator.url/?identity=user&password=user_password
```

This token generator should return HTTP 401 if case of invalid credentials.

### For testing purposes it is possible to create a simple token generator using twilio function:

1. Create a new function in [Twilio functions](https://www.twilio.com/console/functions/manage) using template `Blank`
2. On the next line add `/token-service` to the `PATH`. Copy the whole `PATH` and use it as `ACCESS_TOKEN_SERVICE_URL` (see above)
3. Uncheck the **Check for valid Twilio signature** checkbox
4. Insert the following code:
```
// If you do not want to pay for other people using your Twilio service for their benefit,
// generate user and password pairs different from what is presented here
let users = {
    user00: "", !!! SET NON-EMPTY PASSWORD AND REMOVE THIS NOTE, THIS GENERATOR WILL NOT WORK WITH EMPTY PASSWORD !!!
    user01: ""  !!! SET NON-EMPTY PASSWORD AND REMOVE THIS NOTE, THIS GENERATOR WILL NOT WORK WITH EMPTY PASSWORD !!!
};

let response = new Twilio.Response();
let headers = {
    'Access-Control-Allow-Origin': '*',
  };

exports.handler = function(context, event, callback) {
    response.setHeaders(headers);
    if (!event.identity || !event.password) {
        response.setStatusCode(401);
        response.setBody("No credentials");
        callback(null, response);
        return;
    }

    if (users[event.identity] != event.password) {
        response.setStatusCode(401);
        response.setBody("Wrong credentials");
        callback(null, response);
        return;
    }

    let AccessToken = Twilio.jwt.AccessToken;
    let token = new AccessToken(
      context.ACCOUNT_SID,
      context.TWILIO_API_KEY,
      context.TWILIO_API_SECRET, {
        identity: event.identity,
        ttl: 3600
      });

    let grant = new AccessToken.ChatGrant({ serviceSid: context.SERVICE_SID });
    grant.pushCredentialSid = context.PUSH_CREDENTIAL_SID; 
    token.addGrant(grant);

    response.setStatusCode(200);
    response.setBody(token.toJwt());
    callback(null, response);
};
```
5. Save the function
6. Open [Configure](https://www.twilio.com/console/functions/configure) page and setup values for the following `Environment Variables`:
7. SERVICE_SID
- Open [Conversational Messaging](https://www.twilio.com/console/conversations/configuration/defaults)
- Select `View Service` near the `Default Conversation Service`
- Copy the `Service SID`
- Also navigate to `Push configuration` and enable all types of notifications for receiving FCM messages 
8. TWILIO_API_KEY and TWILIO_API_SECRET
- Create an API KEY [here](https://www.twilio.com/console/chat/project/api-keys)
9. PUSH_CREDENTIAL_SID
- Create new push credentials [here](https://www.twilio.com/console/conversations/push-credentials)

### Optionally setup Firebase Crashlytics

If you want to see crashes reported to crashlytics:
1. [Set up Crashlytics in the Firebase console](https://firebase.google.com/docs/crashlytics/get-started?platform=ios#setup-console)

2. Login into application and navigate to `Menu -> Simulate crash in` in order to check that crashes coming into Firebase console.

## Testing

Prepare Mockingbird

```
$ xcodebuild -resolvePackageDependencies
$ DERIVED_DATA=$(xcodebuild -showBuildSettings | pcregrep -o1 'OBJROOT = (/.*)/Build')
$ (cd "${DERIVED_DATA}/SourcePackages/checkouts/mockingbird" && make install-prebuilt)
```

Download starter pack

```
$ mockingbird download starter-pack
```

If you want to know more about testing framework please refer to the repo of [Mockingbird](https://github.com/birdrides/mockingbird#installation).

## Future Enhancements
- One to One Chat message
- Images Sharing
- Videos Sharing
- PDF sharing
- Message Status
- User Status
- Storing messages in local cache
- Firebase Notifications


## License

MIT
