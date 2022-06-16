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

|  |   | |
| :------------ |:---------------:| -----:|
| <img src="https://user-images.githubusercontent.com/69758492/173639444-7184303e-a7d6-4db5-9d18-459027fd074e.png" width="" height="500" />   |  <img src="https://user-images.githubusercontent.com/69758492/173639455-b64073a0-7233-4ed4-9676-33f2ea804721.png" width="" height="500" /> |  <img src="https://user-images.githubusercontent.com/69758492/173640596-7849fe67-d9b4-4068-84bc-380650a619d3.png" width="" height="500" /> |




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

In the group code review we came together and first reviewed all the front-end code in our project. We took the HTML- and Javascript files and told eachother about what each section is supposed to do, and we shared our opinions on how the code is created. We did the same thing for the back-end and additionally discussed the different back-end layers. We didn't end up changing much to our code, but it was very educational and we learned a lot from eachother. 

We didn't do too many code reviews in our group. A good way to increase the amount of times we do it is to put it inside a "Definition of Done". This way we won't forget to do a code review, and we can learn a lot more from eachother. If there are any problems in the code we can also immediately fix it before we work on the next.
#### ***SonarCloud***
In order to verify the quality of the written code, I used Sonarcloud. 
I integrated this server with the CI/CD pipeline, and it is triggered with every single push to my git. Sonarcloud scans the code for vunerabilities, bugs and small issues and gives possible solutions.

 <img src="https://user-images.githubusercontent.com/69758492/173780247-27f1e470-3f7e-412d-9b2a-0543c87c8e8b.png" width="" height="400" />

#### ***Security***
For the security of my application I decided to add authentication to the front-end application. By requiring the user to authenticate themselves, I can properly setup private accounts and ID's so the users can message each other in my application, I used the built in authentication from [AWS Amplify](https://aws.amazon.com/amplify/). The front-end application sends the user to a login page when they have to login for the first time, there they can login or register. After the user has authenticated once, they are sent to the main screen and they don't have to ever login again. The application remembers the user and allows them to forget about the login process.

#### ***Testing***
### ***Types of testing***
There are different types of testing that can be organized in two types: functional testing and non-functional testing.

To name a few (but definitely not all):
| Functional	| Non Functional 	|
|---	|---	| 
| Unit Testing 	| Performance Testing 	| 
| Integration Testing 	| Load Testing 	| 
| Regression Testing 	| Security Testing 	| 
| Acceptance Testing 	| Usability Testing 	| 
| End to end testing (e2e) 	| Compatibility Testing 	| 

Which types of testing you need depend a lot on the type of project, and on the wishes and necessities of your customer.

### ***Testing with GraphQL***
As I used GraphQL and AWS Amplify for my back-end, I looked into ways that I can test GraphQL and why it would be useful.

My application is very reliant on the GraphQL subscriptions because all the data that I get and send are through GraphQL, the front-end mainly displays the GraphQL content.

### ***Mutations and queries***
In GraphQL, there are only two types of operations you can perform: queries and mutations.

While we use queries to fetch data, we use mutations to modify server-side data.

If queries are the GraphQL equivalent to GET calls in REST, then mutations represent the state-changing methods in REST (like DELETE, PUT, PATCH, etc).

```jsx
describe('Queries', () => {
			let tester
			beforeAll(() => {
			    tester = new EasyGraphQLTester(schemaCode)
			})
			
			test('Should get friends with a nested query', () => {
	        const query = `
	            query GET_FRIENDS($userId: ID!){
	                user(id: $userId) {
	                    id
	                    friends {
	                        id
	                        firstname
	                        lastname
	                    }
	                }
	            }
	        `
	        tester.test(true, query, { userId: '0' })
	    })
})
```

Here is an example test for a GraphQL query. Using such a test I can for example check if a user has a profile picture, how many chatrooms they're in and their names. 
This kind of test would also be useful for me to check if the data exists or is sending properly. If it's all empty or returns an error I would know there's something wrong on with my data in the back-end.

```yml
query chatRooms {
  listChatRooms{
    items{
      id
      chatRoomUsers{
        items{
          user{
            imageUri
          }
        }
      }
    }
  }
}
```
Here is a query that I would be able to use to pull out all the user data in my back-end, I can modify it to check if the imageUri is empty or not for a test. I can also use it to check all other data about the user.

### Resolvers
A resolver is a function that's responsible for populating the data for a single field in your schema. It can populate that data in any way you define, such as by fetching data from a back-end database or a third-party API.

Here's an example resolver that will get the friends of a user:
```jsx
it('Should get friends of user from their id', () => {
      const userId = 0
      const friends = getFriends(db, userId)
      expect(friends.length).toBe(1)
      expect(friends[0]).toHaveProperty('id')
      expect(friends[0]).toHaveProperty('firstname')
      expect(friends[0].firstname).toBe('Eduardo')
})
```
Using a resolver I can test if I got the correct data from my database. I can also create a test chatroom that exists in the system and always test if I'm getting the correct data back. This way I can test if there is any issues happening with the communication between the back-end and the front-end.

### ***Unit testing and integration testing***
*What's the unit testing and integration testing, and what's the difference between them?* <br>
**Unit testing** means testing individual modules of an application in isolation (without any interaction with dependencies) to confirm that the code is doing things right.

**Integration testing** means checking if different modules are working fine when combined together as a group.

To explain it in a very simple and basic manner (in the scope of a phone):

***Unit testing:*** The phone is separated into small components. It checks the battery life/ capacity, and checks the sim card slot for its activation.

***Unit testing:*** The battery and sim card are integrated together. They're assembled to start up the phone.

These types of tests can be used in any appication. Examples of ways it can be used in my own program would be:

***Unit testing***:
- Checking the field length of the message.
- Testing if the inputted values are valid.
- If there's no message being typed the "camera" button shows.
- If a message is being typed the "enter" button shows.

***Integration testing***:
- The user gets brought to the chatlist screen when they're logged in.
- The user gets prompted with a login screen if they've never logged in before.
- If the user sends an invalid message, an error screen will pop up.

### ***Regression testing***
Regression testing is a software testing practice that ensures an application still functions as expected after any code changes, updates, or improvements.

Regression testing is responsible for the overall stability and functionality of the existing features. Whenever a new modification is added to the code, regression testing is applied to guarantee that after each update, the system stays sustainable under continuous improvements. 

Regression testing is very important and should be used in any program. This ensures that you won't publish something full with new bugs or publish a major issue that could leak to problems. I have applied this to my CI/CD so it checks if all my tests are working when I push something new to my git.

### ***End-to-end testing***
End to end (E2E) testing is a software testing method that tests an application’s flow from beginning to end. The goal of this testing is to simulate a real-user scenario and validate the system under the testing process, check its data integrity, and overall integration.

End to end testing will help you simulate the use of your program so you don't have to manually test the flow of the application every time you change something.

The way end to end testing would be useful in my own application would be:
- Testing the login and redirects
- Testing creating chats between users
- Testing sending messages between 2 mock users.

This way I can quickly identify where the problems are within my system. I can also check if the data is being properly received and sent using my GraphQL subscriptions and mutations.

### ***Performance testing***
Performance Testing is a software testing process used for testing the speed, response time, stability, reliability, scalability and resource usage of a software application under particular workload. The main purpose of performance testing is to identify and eliminate the performance bottlenecks in the software application. 

The focus of Performance Testing is checking a software program’s

- Speed – Determines whether the application responds quickly
- Scalability – Determines maximum user load the software application can handle.
- Stability – Determines if the application is stable under varying loads

Performance testing is useful to provide stakeholders with information about their application regarding speed, stability, and scalability. They also tell you what needs to be improved before the product gets published.

One of such performance tests is called **load testing**.<br>
Load testing checks the application’s ability to perform under anticipated user loads. The objective is to identify performance bottlenecks before the software application goes live.

Although it's useful for applications that are bigger in size and needs to be scaled up. For my own project it is not too important, as I only wanted to use it for a small group of friends or a small community.

Most of the tests in **performance testing* are like this, because I'll be using it with a small group, the amount of stress on the servers/ system will not be high at all. So it's not suitable to spend a lot of time in creating this type of test.

### ***Security testing***
Security Testing is a type of Software Testing that uncovers vulnerabilities of the system and determines that the data and resources of the system are protected from possible intruders. It ensures that the software system and application are free from any threats or risks that can cause a loss.

Although security testing is very important in many cases, in my own application it is not as important. Although if I wanted to add end-to-end encryption to my app in the future it would be good to test if that functions correctly. Right now the AWS Login system provided by amazon does it's job very well and I don't have to worry about that too much.

### ***Compatibility testing***
Compatibility Testing is a type of Software testing to check whether your software is capable of running on different hardware, operating systems, applications, network environments or Mobile devices.

It's very important to test your program on all devices you would want to use it on. As I didn't have a good way to test my program on IOS devices, I mainly focused on android and web. Because I use Expo to publish my application I can run it everywhere where Expo is supported, although there can be problems on platforms I can't test on.


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