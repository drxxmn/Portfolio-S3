# Learning Outcomes IP
Learning outcomes for my individual project

## Table of contents
* [1. Web application](#1-web-application)
  - [Front-end projcet](#front-end-project)
  - [Back-end projcet](#back-end-project)
* [2. Software quality](#2-software-quality)
  - [Code reviews](#code-reviews)
  - [Security](#security)
  - [Testing](#testing)
* [4. CI/CD](#4-cicd)
  - [Development pipeline](#development-pipeline)
  - [Production pipeline](#protuction-pipeline)

### 1. Web application
For my individual project I will be making a messaging app. I'll be using a [React native](https://reactnative.dev) front-end with an [AWS Amplify](https://aws.amazon.com/amplify/) back-end. I go more in-depth into my project in my separate research [here](https://github.com/drxxmn/Portfolio-S3/tree/main/IP/Research/Messaging%20App).

#### ***Front-end project***
For my front-end I have used [React native](https://reactnative.dev), [Expo](https://expo.dev), [TypeScript](https://www.typescriptlang.org/) and [React Navigation](https://reactnavigation.org/).

I have chosen [React native](https://reactnative.dev) because it's the industry standard, and I would like to familiarize myself with the workflow so I can apply it in the future in jobs. It also has a lot of resources and tutorials online I can follow. Using [React Navigation](https://reactnavigation.org/). I can quickly setup tabs and create a simple front-end for my messaging application.

I've found [Expo](https://expo.dev) while searching on the internet for mobile app development tools, and it looked really promising. [Expo](https://expo.dev) allows you to test your application in real-time and instantly see what errors there are and how your front-end looks. You can load the application directly on your phone using their application or using an android emulator.


 <img src="https://user-images.githubusercontent.com/69758492/173644459-aac027f7-6e79-4bbe-9481-9b32ec13334c.png" width="" height="400" />

Using the metro bundler of [Expo](https://expo.dev) you have a console that you can access and see all the console errors in. You can also select where  you want to open your application on.

 <img src="https://user-images.githubusercontent.com/69758492/173639444-7184303e-a7d6-4db5-9d18-459027fd074e.png" width="" height="500" />
 <img src="https://user-images.githubusercontent.com/69758492/173639455-b64073a0-7233-4ed4-9676-33f2ea804721.png" width="" height="500" />
 <img src="https://user-images.githubusercontent.com/69758492/173640596-7849fe67-d9b4-4068-84bc-380650a619d3.png" width="" height="500" />


 For the UI I've made a simple design based on WhatsApp first, so I can make sure that everything I want in the application works fine. If I have time in the future I'm planning to modify it and customize the UI.

 You can interact with the different tabs on the main page, but I don't have any other pages set up for that, it'll just show you blank template pages.
 But you can click on the chats and it'll show you all the messages in the chatroom, you can also click on the floating message button on the main screen to see all the users within the app currently to message them and create a new chatroom.

#### ***Back-end project***
For my back-end I used [AWS Amplify](https://aws.amazon.com/amplify/) and [GraphQL](https://docs.amplify.aws/cli-legacy/graphql-transformer/overview/). The reason I chose these tools is because AWS Amplify lets you securely sync and store data seamlessly between applications with the help of Amazon AppSync and Amazon S3. It also allows for easy offline synchronization. Due to the serverless nature of AWS Amplify, making changes to back-end related functions has gotten much easier.

The integrated authentication and GraphQL let's me set things up much easier than if I would use other tools. The authentication system only takes a few lines of code to set-up. GraphQL also allows me to send and request more custom data from the back-end, which works great for my application.

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
Here are all the data models I used for the GraphQL schema. Because it's a many-to-many type situation, I used a join table to manage the data.

### 2. Software quality
The goal of this learning outcome is to use a variety of tools to monitor the quality. This includes, testing, static code analisys and security.

#### ***Code Reviews***
As a group we've done a code review together, everyone had taken the code they made during the group project and we showed eachother exactly what we had made and what all the code is intended for. We also tended to do a lot of over the shoulder type code reviews, whenever we made something new we would show and ask the other groupmates for their opinions.

#### ***Security***
For the security of my application I decided to add authentication to the front-end application. By requiring the user to authenticate themselves, I can properly setup private accounts and ID's so the users can message each other in my application, I used the built in authentication from [AWS Amplify](https://aws.amazon.com/amplify/). The front-end application sends the user to a login page when they have to login for the first time, there they can login or register. After the user has authenticated once, they are sent to the main screen and they don't have to ever login again. The application remembers the user and allows them to forget about the login process.

#### ***Testing***


### 4. CI/CD
The goal of this learning outcome is to design and implement a CI/CD pipeline that is speficically made for this project capable of performing both integration and deployment. 

#### ***Development pipeline***
The development pipeline triggers whenever a push is made to the main branch in github. The pipeline first builds and tests all my code, this is done by checking out the branch and setting up a build environment. An Ubuntu Linux system is used for this due to the differences in efficiency and speen between Ubunto and Windows. Linux works much better for this.

```yaml
name: CI

on:
  push:
    branches:
      - production
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - name: Expo GitHub Action
        uses: expo/expo-github-action@7.2.0

      - name: 🏗 Setup repo
        uses: actions/checkout@v2
        with:
          fetch-depth: 0
```
After the build environment has been setup, the pipeline will start setting up the Node packages in my project.

```yaml
- name: 🏗 Setup Node
        uses: actions/setup-node@v2
        with:
          node-version: 16.x
          cache: yarn
```
Once that step is done it will setup Expo within my project. This uses an Expo token that has been generated to authenticate me, so I wouldn't have to put in my username and password.

```
- name: 🏗 Setup Expo
        uses: expo/expo-github-action@v7
        with:
          expo-version: latest
          token: ${{ secrets.EXPO_TOKEN }}
          expo-cache: true
```
after Expo has been successfully installed I'll install all the dependencies inside my project and run all my Jest tests.

```yaml
- name: 📦 Install dependencies
        run: yarn install

      - name: run jest tests
        run: npm run workflow
```

If everything finishes successfully, the pipeline will then start the Sonarcloud section. This will allow Sonarcloud to to check and scan all my code. It authenticates me using a SONAR_TOKEN that was generated on the Sonarcloud website, which I then put into a Github secret.

```yaml
- name: SonarCloud Scan
        uses: SonarSource/sonarcloud-github-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  # Needed to get PR information, if any
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```
#### ***Protuction pipeline***
There's not much different inside of the production pipeline, the only difference is that I'll be adding an extra step that publishes my project onto the Expo servers. This allows me to use my app on my phone immediately using the Expo app.
```yaml
- name: 🚀 Publish app
        run: expo publish --non-interactive

```