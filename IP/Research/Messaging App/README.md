# **The makings of a messenger app**
In this research paper I'll be looking into making a messaging application from scratch, how you can do it and the reasons as to why.

---

## **Table of contents**
* [Getting started](#getting-started)
    * [What defines a messaging app?](#what-defines-a-messaging-app)
    * [The benefits of using existing apps](#the-benefits-of-using-existing-apps)
    * [The benefits of making your own app](#the-benefits-of-making-your-own-app)
* [What do I need to make a messaging app?](#what-do-i-need-to-make-a-messaging-app)
    * [Tools](#tools)
        * [Front-end](#front-end)
        * [Back-end](#back-end)
* [The structure of your messaging app](#the-structure-of-your-messaging-app)
    * [Front-end](#front-end-1)
        * [Components](#components)
    * [Back-end](#back-end-1)
* [Sources](#sources)

---

## **Getting started**

### *What defines a messaging app?*
A messaging app is a chat application or platform that enables users to instant message and connect with each other through their computers or mobile devices. Think Facebook Messenger, WhatsApp, WeChat, or Slack (just to name a few). 

### *The benefits of using existing apps*
There are many benefits to use already existing messaging applications. 

Existing messaging applications already have all the groundwork laid out for you, it is very simple and easy to just install and use. You can already send out messages, video's images etc. with just a simple sign up and installation you can get WhatsApp, Facebook, Snapchat etc. and message your friends.
It is also more familiar to most people, so it would be easy for them to install, add you and navigate the different menus, so then why would we want to use and create our own messaging app? 

### *The benefits of making your own app*
One of the more technical reasons is so that we can learn and create our own application, put it on our portfolio, find out how a messaging exactly works and how difficult it is to make it on your own. But there are a lot more benefits if you want to make one for your friends, family or community.

A more fun reasons to make your own application is the customization, you can create your own themes, sounds, features etc. Often times when you use other messaging applications, you miss certain features, or wish some things were done in certain ways. By making your own application, you can add anything you want and make it look exactly the way you desire.

One of the more important issues with messaging apps is security, the risks involved and who you want to give your data to. I go more in depth on this topic [here on my other research subject.](https://github.com/drxxmn/Portfolio-S3/tree/main/IP/Research/Security%20online)

## **What do I need to make a messaging app?**
## *Tools*
Here will be all the tools you will be needing to create your own messaging app from scratch using a [React Native](https://reactnative.dev/) front-end and an [AWS Amplify](https://aws.amazon.com/amplify/) back-end, with a lot of control and customization.

### *Front-end*
- [React Native](https://reactnative.dev/)
- [Expo](https://expo.dev/)
- [TypeScript](https://www.typescriptlang.org/)
- [React Navigation](https://reactnavigation.org/)

We will be using [React Native](https://reactnative.dev/) for our application for many reasons, one of them being it's an industry standard and has many features that are UI oriented and good for mobile development, another is that we will be incorporating [Expo](https://expo.dev/) and [React Navigation](https://reactnavigation.org/).

#### *What is Expo?*
[Expo](https://expo.dev/) is a tool chain built around React Native to help you quickly start an app. It provides a set of tools that simplify the development and testing of React Native app and arms you with the components of users interface and services that are usually available in third-party native React Native components. With Expo, you can find all of them in Expo SDK.

One of the most useful features in Expo is that you can instantly test your application via your mobile phone by downloading their app in the Playstore/ Appstore. You can also use an emulator on your pc, for example [Android Studio](https://developer.android.com/studio) for android.

#### *React Navigation*
Mobile apps are rarely made up of a single screen. Managing the presentation of, and transition between, multiple screens is typically handled by what is known as a navigator. [React Navigation](https://reactnavigation.org/) will be helping us with that. Using [React Navigation](https://reactnavigation.org/) we can quickly and simply setup different tabs in our mobile application.

### *Back-end*
- [AWS Amplify](https://aws.amazon.com/amplify/)
- [GraphQL](https://docs.amplify.aws/cli-legacy/graphql-transformer/overview/)

#### *AWS Amplify/ GraphQL*
The primary reason to use [AWS Amplify](https://aws.amazon.com/amplify/) is that it increases your speed and agility. This is achieved by greatly reducing the amount of code you need to write and manage to achieve cloud-native functionality. It is also integrated with [GraphQL](https://docs.amplify.aws/cli-legacy/graphql-transformer/overview/) and works great with [React Native](https://reactnative.dev/).

GraphQL lets you fetch all relevant data in a single query. It allows making multiple resources request in a single query call, which saves a lot of time and bandwidth by reducing the number of network round trips to the server. It also helps to save waterfall network requests, where you need to resolve dependent resources on previous requests.

## **The structure of your messaging app**
### *Front-End*
#### *Components*
##### *Chatting List*
For [React,](https://reactnative.dev/) it's best to think in smaller components and slowly build up to a bigger program, thus you should start with making a small UI with different tabs, which we use [React Navigation](https://reactnavigation.org/) for.

Next you will have to think about the individual components that make up your application, in our case there would need to be a chat room, profile picture, name and date.
```jsx
Input (props): 
ChatRoom: 
- ChatRoomId: String 
- Users: [User] 
- lastMessage: { 
id: String 
content: String 
createdAt: String 
}
```
After you've created one of these, you can put them in a flat list and display all the chats in a tab.

##### *Chatroom*
For the chatroom, we will create a separate component for your message input box. We will also make a message component, which we will all put into the Chatroom Screen. The message must also have a check to see if it's your own message, and will display the color accordingly.

##### *User list*
The user list is somewhat similar to the chat list, you just need to get all the existing users in the system and display their status instead of their latest message.

### *Back-end*
For the back-end, we will have to create the different user models and push them to the [AWS Amplify](https://aws.amazon.com/amplify/) platform. Here are the models I ended up creating for my backend with [GraphQL](https://docs.amplify.aws/cli-legacy/graphql-transformer/overview/).
```rb
type User
@model
@auth(
  rules: [
    #{ allow: owner, operations: [create, read, update, delete] }
    { allow: owner },
    { allow: groups, groups: ["Admin"] },
    { allow: private, provider: userPools, operations: [read] }
    #{ allow: private, operations: [read] }
  ]
) {
  id: ID!
  name: String!
  imageUri: String
  status: String
  #chatRoomUser: [ChatRoomUser] @connection(keyName: "byUser", fields: ["id"])
  chatRoomUser: [ChatRoomUser] @hasMany(indexName: "byUser", fields: ["id"])
}

type ChatRoomUser
@model
#@key(name: "byUser", fields: ["userID", "chatRoomID"])
#@key(name: "byChatRoom", fields: ["chatRoomID", "userID"])
@auth(
  rules: [
    { allow: owner },
    { allow: groups, groups: ["Admin"] },
    { allow: private, provider: userPools, operations: [read] }
#    { allow: owner, operations: [create, read, update, delete] }
#    { allow: private, operations: [read] }
  ]
) {
  id: ID!
  userID: ID! @index(name: "byUser", sortKeyFields: ["chatRoomID"])
  chatRoomID: ID! @index(name: "byChatRoom", sortKeyFields: ["userID"])
  user: User @hasOne(fields: ["userID"])
  chatRoom: ChatRoom @hasOne(fields: ["chatRoomID"])
}

type ChatRoom
@model
@auth(
  rules: [
    { allow: owner },
    { allow: groups, groups: ["Admin"] },
    { allow: private, provider: userPools, operations: [read] }
#    { allow: owner, operations: [create, read, update, delete] }
#    { allow: private, operations: [read] }
  ]
) {
  id: ID!
  chatRoomUsers: [ChatRoomUser] @hasMany(indexName: "byChatRoom", fields: ["id"])
  messages: [Message] @hasMany(indexName: "byChatRoom", fields: ["id"])
  lastMessageID: ID
  lastMessage: Message @hasOne(fields: ["lastMessageID"])
}

type Message
@model
@auth(
  rules: [
    { allow: owner },
    { allow: groups, groups: ["Admin"] },
    { allow: private, provider: userPools, operations: [read] }
#    { allow: owner, operations: [create, read, update, delete] }
#    { allow: private, operations: [read] }
  ]
) {
  id: ID!
  createdAt: String!
  content: String!
  userID: ID!
  chatRoomID: ID! @index(name: "byChatRoom", sortKeyFields: ["createdAt"], queryField: "messagesByChatRoom")
  user: User @hasOne(fields: ["userID"])
  chatRoom: ChatRoom @hasOne(fields: ["chatRoomID"])
}
```
To obtain the latest data, you just need to subscribe to new messages or chatroom updates. The same goes for sending new data using [GraphQL](https://docs.amplify.aws/cli-legacy/graphql-transformer/overview/).

| <img src="https://user-images.githubusercontent.com/69758492/172170612-e550921a-3d8b-42bb-88aa-93274a8eed43.png" width="600" height="300" /> |
| :--: |
| _GraphQL subscriptions and mutations_ |

### *Final words*
Making your own application does have a lot of pro's and con's, in the end it's what you need for yourself. There are many ways to make your own application, and I only used a few tools. You could be using something entirely different like [Vue.js](https://vuejs.org/) or a different front-end/ back-end combination. 

This was not a comprehensive guide, but just research on how/ what you need to make your own messaging application.

### Sources
- https://docs.expo.dev/
- https://reactnative.dev/
- https://www.typescriptlang.org/
- https://reactnavigation.org/
- https://apiko.com/blog/expo-vs-vanilla-react-native/