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

You will need to understand how Firebase works (at a high level), how to set up a Firebase project, how this integrates with the Flutter development environment and perform CRUD operations between the application and the cloud platform. 



## Lab Activity

**Before you continue :** make sure you have signed up with a Google account and have the lecture notes (lec. 16) close by. As a lot of the initial learning is contained in that lecture. 

### 1. Creating your first Firebase Realtime Database

1. Log into your Firebase console (https://console.firebase.google.com/) and create your first Firebase project.


<img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab16/1.png?raw=true" alt="Firebase Realtime Database" style="zoom:50%;" />

2. Create a new Realtime Database instance. For the purpose of this module I would not worry about secrutiy rules and set your database to start in test mode. You can alter this at a later date. The test mode will provide you with full access for 30 days as standard.

   ```
   {
     "rules": {
       ".read": "now < 1613001600000",  // 2021-2-11
       ".write": "now < 1613001600000",  // 2021-2-11
     }
   }
   ```

   You can alter this by changing the following rules once the database has been created (in order to change these rule you will need to access the rules tab within the Realtime Database instance).

   ```
   {
   	"rules": {
   		".read": true,
    		".write": true,
   	}
   }
   ```

   For more information on rules Julio Marin has a nice write up of [10 Firebase Realtime Database Rule Templates](https://medium.com/@juliomacr/10-firebase-realtime-database-rule-templates-d4894a118a98) and Google's [Get started with Firebase Security Rules](https://firebase.google.com/docs/database/security/get-started).

### 2. Integrating Firebase into your Flutter application

1. First you will need to get the 'google-services.json' config file which is the main configuration between your mobile application and Firebase. The file contains all information needs to authenticate your application.  

2. Create a new Flutter Application project. Remove all the unnessary boilerplate code, so you have the bare minimum to compile with a Stateless Widget (one you are comfrotable working with).

   > **Note** This is why it's important to make sure your application ID is the same as the one you `provide` in the Firebase registration process - these are needed to authenticate the application.  

   <img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab16/2.png?raw=true" alt="Firebase Realtime Database" style="zoom:50%;" />

   > **Remember:**
   >
   > Every time you include a new dependency or plugin you need to run the following command in the terminal window. These commands are used for obtaining the dependencies and plugins you need to compile your application - [get pub](https://dart.dev/tools/pub/cmd) is specific to dart and is a set of tools for managing dart packages. pub is short for [package](https://dartcode.org/docs/commands/#pub-get-packages) Whereas, 'packages get' is the full command given and both are interchangeably used. 

   ```dart
   flutter get pub
   ```

   ```dart
   flutter packages get
   ```

   #### Set up your environment to work with the Firebase Realtime Database

3. Open build.gradle file from the root of the android folder and add the following dependency after the last classpath.

   ```dart
   entry: classpath 'com.google.gms:google-services:4.3.3’
   ```

4. Open build.gradle file from the android > app directory and add the following plugin to the bottom.

   ```dart
   apply plugin: 'com.google.gms.google-services’
   ```

5. Add the following dependency to your pubspec.yaml file.

   ```dart
   firebase_database: ^3.1.6
   
   firebase_core: ^0.4.4+3
   
   intl: ^0.15.8
   ```

6. Run the following command in the terminal to clean your flutter projects.

   ```dart
   flutter clean
   ```

7. Run the following command in the terminal

   ```dart
    flutter packages get
   ```

8. Rebuild the application by running the following command in the terminal.

   ```dart
   flutter run
   ```

   > **Note** Sometimes you might have a compile error when importing the packages into your code, to resolve this you need to change the default minSdkVersion to 23. This is Android's way of notifying which version of the OS the app is supported on. You set a minimum OS and target version. 

### 3. Developing a LightSwitch Application

You are going to build an application that simulates the switching on/off of lights from one mobile device to another. you should always break down your code to make it simpler to debug and manage. In the example below, we have the main method in the main.dart file and the lightswitch interaction inside the lightswitch.dart file. 

***main.dart***

```dart
import 'package:flutter/material.dart';
import 'lightswitch.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyLightSwitch(),
    );
  }
}
```

***lightswitch.dart***

```dart
import 'package:flutter/material.dart';

class MyLightSwitch extends StatefulWidget {
  UpdateLightSwitch createState() => UpdateLightSwitch();
}

class UpdateLightSwitch extends State {
 
  @override
  void initState() {
    super.initState();
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('LightSwitch'),
      ),
      body: Center(
          child: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: <Widget>[
          ],
        ),
      )),
    );
  }
}
```

Use the lightswitch boilerplate code to begin with. You will now build up the Widget to communicate with the Firebase Realtime Database and provide interaction to the user. You will notice that we are using a State Widget. Having previously learnt the diference between State and Stateless in earlier labs and lectures. You should think what part of this application requires State to be known and kept. 

1. Add the following package to your class. This package will import all the functionality in order to communicate with the Firebase Database (Realtime).

   ```dart
   import 'package:firebase_database/firebase_database.dart';
   ```

2. Inside the UpdateLightSwitch method add the following reference to the FirebaseDatabase. 

   ```dart
   final databaseReference = FirebaseDatabase().reference().child("smarthome");
   ```

   At this point there is nothing inside our Realtime Database and certainly no reference to the smarthome child. Therefore, go to your Firebase console and create the database with the following fields (smarthome > lightswitch: false). This creates a document called smarthome with one key value pair of lightswitch and a bool false.

   <img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab16/3.png?raw=true" alt="Firebase Realtime Database Setup" style="zoom:50%;" />

3. Now to build the Widgets. Add the following code inside the children property of the Column Widget. 

   ```dart
   Text("Light Switch",
   	style: TextStyle(
   	color: Colors.blueGrey,
   	fontSize: 30,
   	fontFamily: 'Open Sans',
   )),
   Spacer(),
   RaisedButton(
   	child: status ? Text("LIGHTS ON") : Text("LIGHTS OFF"),
   	onPressed: () => toggleSwitch(),
   ),
   ```

   The status ? Text(...) : Text(...) is referred to as a conditional statement within a child attribute called a ternary operator. Which consists of a condition ? true : false 

4. Write the toggleSwitch method inside the UpdateLightSwitch class but outside of the Widget build method. This method needs to be a async method and does not return anything. 

5. Paste the following code inside the method. This will toggle between the status when the RaiseButton is pressed. This updates the database with the bool (false/true) for the lightswitch key.

   ```dart
   if (status) {
   	await databaseReference.update({'lightswitch': false}).then((_) {
   	print('lights on');
   });
   } else {
   	await databaseReference.update({'lightswitch': true}).then((_) {
   	print('lights off');
   });
   }
   ```

6. At present you will have some problems with your code. This relates to the status variable not being delcared. Add the status bool variable as a public variable inside the UpdateLightSwitch method. Initialise the bool as false. Now you should see the error go away. Debug your application and see what is currently happening. What is happening?

7.  Within a State Widget we can perform a call once an event is fired. In this case when the Widget loads we can initiate the State at load time. You are now going to write a further method that checks the current status of the lightswitch and stores the state within the application. Inside the initState() method add the following call.

   ```dart
   checkCurrentStatus();
   ```

8. You now need to build this method inside the UpdateLightSwitch class (below the toggleSwitch method). Add the following code to listen to new value changes within the database. 

   ```dart
   databaseReference.onValue.listen((event) {}
   ```

9. Within the body of this event listener you will need to store the result from the server. Declare a variable of snapshot which stores the event.snapsop as a response from the server. To access the value from the lightswitch pair. You will need to access it directly. Overwrite the value of the status variable with the new response. 

   ```dart
   var snapshot = event.snapshot;
   status = snapshot.value['lightswitch'];
   ```

10. Add the following condition statement to test if your application is working propertly.

    ```dart
    if (status) {
    	print('lights on');
    } else {
    	print('lights off');
    }
    ```

    You should notice that the button changes its name from LIGHTS ON to LIGHTS OFF when pressed. The database also updates itself passing the value from the client to the server. 

11. At this point to prove that the application works across devices, you could start a new instance of an emulator or ask a friend to load your application on their machine to see if you can pass the lights on/off variable across devices. 

12. You can extend this work by actually simulating the turning on lights. To simulate this, we are going to change the colour of the background of the app from white (lights on) to black (lights off). Add the following public variable to your UpdateLightSwitch method. 

    ```dart
    Color bgColorOnOff = Colors.black;
    ```

13. Set the property to this variable in the Widget build > Scaffold (outside the appBar and body). Save your code and the hot reload should now have changed the colour of your application background from white to black.

    ```dart
    backgroundColor: bgColorOnOff,
    ```

14. In the checkCurrentLightStatus method, inside the conditional statement for status you should set the state that you want to update. In this instance, you should change the colour of the background variable. You will need to use two setState() methods inside each of the conditions of status. You should change the colours to white (on) and black (off).

    ```dart
    setState(() {
    	//change background colour variable here
    });
    ```

4. ### Developing a Simple Chat Application

<div class=footer><div class=footer-text>  CO2509 Mobile Computing | Lab 16</div></div>

