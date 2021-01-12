<img  align="right" src="https://github.com/UCLanCSC/co2509-resources/blob/master/general/logo.jpg?raw=true" style="zoom:10%" />

# CO2509 : Mobile Computing

### Lab 16: An introduction to Firebase - developing dynamic Flutter applications with Firebase.



## Lab Summary

This lab continues the learning into making mobile applications that utilise storage platforms. In particular this lab focusses on using Google's Firebase cloud platform. In the lab you will setup your own Firebase account with your first Firebase project. From here you will learn how to integrate your Flutter application with Firebase by performing basic CRUD (Create, Read, Update, Delete) operations. This is useful as it will enable your app storage to work across devices. 



> **Remember:**
>
> This lab is extremly useful for your assignment coursework. It will enable you to provide storage options for your mobile application. 



## Aim

This lab aims to provide you with the knowledge of extending your Flutter applications to store data in the cloud. This will be achieved through integrating Google's Firebase cloud platform into our Flutter applications. 



## **Objectives**

This lab aims to achieve the following objective(s) below:

**O.1**: Learn the fundementials about adopting Firebase as a data storage solution.

**O.2**: Learn the difference between Firebase's Firestore and Realtime Database options.

**O.3**: Extend Flutter application by storing data in the cloud using Google's Firebase platform.



## Reading Material 

- Explore Realtime Database In Flutter -> https://medium.com/flutterdevs/explore-realtime-database-in-flutter-c5870c2b231f
- Flutter: Displaying Dynamic Contents using ListView.builder -> https://medium.com/@DakshHub/flutter-displaying-dynamic-contents-using-listview-builder-f2cedb1a19fb
- Firebase Firestore Tutorial -> https://www.youtube.com/watch?v=4d-gIPGzmK4&ab_channel=TheNetNinja
- Google Firebase youtube channel -> https://www.youtube.com/user/Firebase
- Using Firestore as a backend to your Flutter application -> https://www.youtube.com/watch?v=DqJ_KjFzL9I&ab_channel=GoogleDevelopers
- Introducing Firebase Realtime Database -> https://www.youtube.com/watch?v=U5aeM5dvUpA&feature=emb_title&ab_channel=Firebase



## Introduction

As with any project, the right tooling is vital and getting these tools to work for you takes time to master. In this lab you will learn about building mobile applications that wish to store data in the cloud. In the past you've had to build your own Apache or NGINX server (setup, maintain and support), choose your database storage platform (MySQL, MariaDB, MongoDB etc...) and adopt a companion scripting language in order to communicate between the client and the server's infrastracture. 

As we know, a lot of these challeneges have been removed with the invent of 'serverless' technologies. Google's Firebase is one of those options which removes these hurdles by connecting the client directly to the data store by using an SDK. 



## Firebase

These types of cloud storage options are becoming increasingly popular. Top technology companies and sports organisations will use some form of cloud data storage that is managed by either Amazon Web Services, Microsoft Azure, IBM Cloud, Oracle Cloud Infrastructure, Alibaba  Cloud etc. The reason why Google's Firebase has been adopted for this module isn't because its better than the above, but rather because Flutter and Firebase are both owned and supported by Google. So there are a few synergies in the way in which you get started and communicate, it's easy to set up and does provide decent tools for developers and it's popular amongst the developer communities.

Firebase is a set of server-side services and tools. Rememer Firebase is the entire platform, Firestore and Realtime database are the storage features within Firebase. It is important you understand the difference between these two storage options (and if you are using tutorials or code to help you, you will need to know which option the material is advising you about). For the purpose of this lab we will be using Realtime database. 

**Cloud Firestore** is Firebase's newest database for mobile app development. It builds on the successes of the Realtime Database with a new, more intuitive data model. Cloud Firestore also features richer, faster queries and scales further than the Realtime Database.

**Realtime Database** is Firebase's original database. It's an efficient, low-latency solution for mobile apps that require synced states across clients in realtime.

Google has kindly put a survey together to assist developers decide which data storage option is best for them (https://firebase.google.com/docs/database/rtdb-vs-firestore). It is worthwhile visiting this URL to just understand the various questions it prompts you to think about your choices.

You will need to understand how Firebase works (at a high level), how to set up a Firebase proejct, how this integrates with the Flutter development environment and perform CRUD operations between the application and the cloud platform. 



## Lab Activity

**Before you continue :** make sure you have signed up with a Google account and have the lecture notes (lec. 16) close by. As a lot of the initial learning is contained in that lecture. 

### 1. Creating your first Firebase Realtime Database

Log into your Firebase console (https://console.firebase.google.com/) and create your first Firebase project.

<img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab16/1.png?raw=true" alt="Firebase Realtime Database" style="zoom:50%;" />

### 2. Integrating Firebase into your Flutter application

1. First you will need to get the 'google-services.json' config file which is the main configuration between your mobile application and Firebase. The file contains all information needs to authenticate your application.  

   > **Note** This is why it's important to make sure your application ID is the same as the one you provide in the Firebase registration process - these are needed to authenticate the application.  

   <img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab16/2.png?raw=true" alt="Firebase Realtime Database" style="zoom:50%;" />

   > **Remember:**
   >
   > Everytime you include a new dependency or plugin you need to run the following command in the terminal window. These commands are used for obtaining the dependencies and plugins you need to compile your application - [get pub](https://dart.dev/tools/pub/cmd) is specific to dart and is a set of tools for manageing dart packages. pub is short for [package](https://dartcode.org/docs/commands/#pub-get-packages) Whereas, 'packages get' is the full command given and both are interchangeably used. 

   ```dart
   flutter get pub
   ```

   ```dart
   flutter packages get
   ```

   

<div class=footer><div class=footer-text>  CO2509 Mobile Computing | Lab 16</div></div>

