# # BotFramework-WebChat Integration Notes


### DirectLine and DirectLine Token
The BotFramework-WebChat component requires as *DirectLine Token*. This token can either be generated with the `Secret` MS provides for the `web` channel of the Azure bot service, or it can be generated through the following `POST` request:
```
POST https://directline.botframework.com/v3/directline/tokens/generate 
Authorization: Bearer <SECRET>
BODY: 
{"user": { "id": "string", "name": "string" }, "trustedOrigins": [ "string" ] }
```

For security, it is not advised to authenticate directly with the `Secret`. To facilitate this, Wysdom will expose a service that generates and returns the `token` to the caller. Most likely, this will be a serverless function on Azure.

The service will most likely be invoked as below example:
```
const res = await fetch('https://wellness-ai-functions.azurewebsites.net/api/token', { method: 'GET' });
const { token } = await res.json();
```

## In Your Implementation
In the BotFramework-WebChat SDK you will pass the `token` value above to the `createDirectLine()` function, as below:
```
const  directLine = createDirectLine({ token });
```

The `directLine` const from above is then passed as a prop to either your `Composer` component, or the `WebChat` - depending on your integration.


### Passing Data To the Bot

From a high-level, the bot will receive data from the parent page through `DirectLine Events`. Directline has a few different `Activity` types, the most obvious being `Message` and then also `Event`. Passing a `message` activity would result in text being shown within the chat pane, whereas an `event` is "silent".

The most straightforward way of doing this is through the `useSendEvent` hook included in the BotFramework-WebChat SDK.

For example, the hook can be used as follows:
```
const sendEvent = useSendEvent();

const handleButtonClick = useCallBack(() => sendEvent('<EVENT NAME'>, {userId: "<UUID VALUE>", productId: "<PRODUCT ID VALUE>"}), [sendEvent]);
```

In the above example, we are assuming a button click - which is likely not a relevant scenario in the Wellness VA world, but the pattern can be followed.

More applicably, this can be used in the `useEffect` hook within React to have this `Event` data sent when the component is first mounted. 

In brief, this hook should be used when you want to begin a conversation.
The EVENT NAME value(s) will be provided by Wysdom.

### Simple Styling
Styling within the chat pane can be controlled with a `styleOptions` object. For the full list of options, follow this link: [style options](https://github.com/microsoft/BotFramework-WebChat/blob/master/packages/api/src/defaultStyleOptions.ts)

For example:
```
const styleOptions = {
	botAvatarInitials: 'BF',
	userAvatarInitials: 'WC',
	hideUploadButton: true
}

<Webchat directLine=directLine styleOptions=styleOptions />
```
