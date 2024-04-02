# ChatGPT Azure

The ChatGPT Azure package for Deep allows seamless GPT-3.5/GPT-4 integration in your projects, providing AI-powered conversations.

[![npm](https://img.shields.io/npm/v/@deep-foundation/chatgpt-azure.svg)](https://www.npmjs.com/package/@deep-foundation/chatgpt-azure) 
[![Gitpod](https://img.shields.io/badge/Gitpod-ready--to--code-blue?logo=gitpod)](https://gitpod.io/#https://github.com/deep-foundation/chatgpt-azure) 
[![Discord](https://badgen.net/badge/icon/discord?icon=discord&label&color=purple)](https://discord.gg/deep-foundation)


# Setup

## Install
Install `@deep-foundation/chatgpt-azure` package by using [`npm-packager`](https://www.npmjs.com/package/@deep-foundation/npm-packager)

## Give permissions
- Insert a `Join` link from the `@deep-foundation/chatgpt-azure` package to the admin to share admin permissions with the package
<!-- TODO: Write another instruction how to give permissions when more flexible permissions will appear -->
You can do it by using the following code by executing it right in the browser console in [DeepCase](https://github.com/deep-foundation/deepcase-app)
```javascript
const joinTypeLinkId = await deep.id("@deep-foundation/core", "Join");
const chatGPTAzureLinkId = await deep.id("@deep-foundation/chatgpt-azure", "Join");
await deep.insert([
  {
    type_id: joinTypeLinkId,
    from_id: chatGPTAzureLinkId,
    to_id: await deep.id('deep', 'admin'),
  },
]);
```

## Configure

### By using client handler (recommended for new users or non-developers) 

- Query `Manager` link by using `Query` in [`DeepCase`](https://github.com/deep-foundation/deepcase-app)
- Click on `Manager` link to open its client handler and use the form to configure the package by providing api key, model name and other required information

### Manually (recommended for developers)

#### API Key
- Create an `ApiKey` link and set its value to your ChatGPT Azure API key
- Create an `UsesApiKey` link from the `@deep-foundation/chatgpt-azure` package to the `ApiKey` link.

#### Model
- Create an `UsesModel` link from the `@deep-foundation/chatgpt-azure` package to the desired model.
- You can use the prepared models located in the `@deep-foundation/chatgpt-azure-deep` package or create model youself

# Using
<!-- TODO: Write how to use client handler -->
## By using client handler (recommended for new users or non-developers)
Client handler is not added here yet. Wait for the update or create a pull request.

## Manually (recommended for developers)

### Start a Conversation
- Insert a `Conversation` link of the `@deep-foundation/chatgpt-azure` package.
- Insert a `Message` link, set its value to the message you want to send to ChatGPT
- Insert a `Reply` link from your `Message` to `Conversation` to send the request.
#### By Using [`DeepCase`](https://github.com/deep-foundation/deepcase-app) (recommended for non-developers)
- Open context menu
- Choose `Insert` option
- Find `Conversation` link located in the `@deep-foundation/chatgpt-azure` package and insert it 
- Open context menu
- Choose `Insert` option
- Find `Message` link located in the `@deep-foundation/chatgpt-azure` package and insert it
- Open context menu of the `Message` link
- Choose `Editor` option
- Write your message in the editor
- Close the editor
- Open context menu
- Find `Reply` link located in the `@deep-foundation/messaging` package and insert it
#### By Using Code (recommended for developers)
```ts
  const containTypeLinkId = await deep.id("@deep-foundation/core", "Contain");
  const conversationTypeLinkId = await deep.id("@deep-foundation/chatgpt-azure", "Conversation");
  const {data: [{id: conversationLinkId}]} = await deep.insert({
    type_id: conversationTypeLinkId,
    in: {
      type_id: containTypeLinkId,
      from_id: deep.linkId
    }
  });
  const messageTypeLinkId = await deep.id("@deep-foundation/chatgpt-azure", "Message");
  const {data: [{id: messageLinkId}]} = await deep.insert({
    type_id: messageTypeLinkId,
    string: {
      data:{
        value: "Your message"
      }
    },
    in: {
      type_id: containTypeLinkId,
      from_id: deep.linkId
    }
  });
  await deep.insert({
    type_id: await deep.id("@deep-foundation/messaging", "Reply"),
    from_id: messageLinkId,
    to_id: conversationLinkId,
    in: {
      type_id: containTypeLinkId,
      from_id: deep.linkId
    }
  });
```

### View Answer
#### By Using [`DeepCase`](https://github.com/deep-foundation/deepcase-app) Traveller (recommended for non-developers)
- Open context menu of your message
- Choose `Traveler` option
- Choose `In` option
- Choose `Reply` option
- Open context menu of the `Reply` link that just appeared
- Choose `Traveler` option
- Choose `from` option
- Congratulations 🎉! You now see the `Message` link that is the answer from ChatGPT to your message. You can see its value by opening its editor
#### By Using Code (recommended for developers)
Query `Message` link where `Reply` link is pointing to your message
  Here is how you can do it by using code:
  ```ts
    const yourMessageId = 123;
    const replyTypeLinkId = await deep.id("@deep-foundation/messaging", "Reply");
    const messageTypeLinkId = await deep.id("@deep-foundation/chatgpt-azure", "Message");
    const {data: [messageLink]} = await deep.select({
      type_id: messageTypeLinkId,
      out: {
        type_id: replyTypeLinkId,
        to_id: yourMessageId
      }
    });
    console.log({messageLink})
  ```

### View All Messages
*Note: in the following examples you will also see `Reply` links*
#### By Using [`DeepCase`](https://github.com/deep-foundation/deepcase-app) (recommended for non-developers)
- Open context menu of the `Conversation` link
- Choose `Traveler` option
- Choose `Down` option
- Choose `messagingTree` option
- Congratulations 🎉! You see all the messages in the current conversation
#### By Using Code (recommended for developers)
**Query all the links down to the `Conversation` by `messagingTree` link**  
Here is how you can do it by using code:
```ts
  const conversationLinkId = 123;
  const messagingTreeLinkId = await deep.id("@deep-foundation/messaging", "messagingTree");
  const {data: messages} = await deep.select({
    up: {
      tree_id: {_eq: messagingTreeLinkId},
      parent_id: conversationLinkId
    }
  });
  console.log({messages})
```

### Continue the Dialogue (recommended for developers)
The process is the same as starting a conversation, but instead of replying to the `Conversation` link, you reply to the last `Message` link
#### By Using [`DeepCase`](https://github.com/deep-foundation/deepcase-app)
- Insert a `Message` link, set its value to the message you want to send to ChatGPT
- Insert a `Reply` link from your message to the last `Message` link from ChatGPT

#### By Using Code (recommended for developers)
```ts
  const lastMessageId = 123;
  const messageTypeLinkId = await deep.id("@deep-foundation/chatgpt-azure", "Message");
  const {data: [{id: newMessageLinkId}]} = await deep.insert({
    type_id: messageTypeLinkId,
    string: {
      data:{
        value: "Your message"
      }
    },
    in: {
      type_id: containTypeLinkId,
      from_id: deep.linkId
    }
  });
  await deep.insert({
    type_id: await deep.id("@deep-foundation/messaging", "Reply"),
    from_id: newMessageLinkId,
    to_id: lastMessageId,
    in: {
      type_id: containTypeLinkId,
      from_id: deep.linkId
    }
  });
```
