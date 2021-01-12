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

<img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab16/1.png?raw=true" alt="Postman create request dialog" style="zoom:50%;" />

### 2. Integrating the API into your Flutter application

1. You will need to recall how to make the HTTP request and parse JSON data from a previous lab. Set up your project so that you have a button on the screen which when pressed, calls the following URL (https://jobs.github.com/positions.json?description=mobile+apps&location=london) and prints the response to the debug console. 

   <img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab15/4.png?raw=true" alt="Flutter application with button center" style="zoom:50%;" />

2. Now alter your print statement to print the length of the JSON data. Now when you press the button it should print the number of items in the JSON object. In this example the number of items is 3.

   ```dart
   I/flutter ( 8263): 3
   ```

   

   From here we are going to populate a Card List Widget containing the title for each of these jobs as a new item in the list. From this we are going to need to create a StatefulWidget. 

3. Copy the following code into your main.dart file.

   ```dart
   import 'package:flutter/material.dart';
   import 'JSONList.dart';
   
   void main() => runApp(MyApp());
   
   class MyApp extends StatelessWidget {
     @override
     Widget build(BuildContext context) {
       return MaterialApp(
           title: 'Welcome to CO2509: Mobile Computing',
           home: Scaffold(
             appBar: AppBar(
               title: Text('Welcome to CO2509'),
             ),
             body: Center(
               child: MyJsonParser(),
             ),
           ));
     }
   }
   ```

4. Create a new dart file called JSONList.dart. 

5. Import the following packages

   ```dart
   import 'package:flutter/material.dart';
   import 'package:http/http.dart';
   import 'dart:convert' show json;
   ```

6. Create a StatefulWidget -  this is the foundations for our Card List View. 

   > **Remember:**
   >
   > Why we use Stateful over Stateless Widgets, if you are not sure go over the previous labs and lectures as this is a fundemental aspect of developing with Flutter. 

   

   ```dart
   class MyJsonParser extends StatefulWidget {
     MyJsonParser({Key key, this.title}) : super(key: key);
     final String title;
     @override
     _JSONParser createState() => new _JSONParser();
   }
   
   class _JSONParser extends State<MyJsonParser> {
     ...
   }
   ```

7. We need to create a model which holds the deserialisation of the data. Looking at the response from Postman we need can interrogate which data is returned and the naming for each key pair. Create a new class for this model. You can see we are extracting the title from the JSON response. 

   ```dart
   class JobDataModel {
     String title;
     
     JobDataModel({this.title});
     
     JobDataModel.fromJson(dynamic json) {
       title = json['title'];
     }
   }
   ```

8. We are now going to create another class which will handle the parsing of the json data into a List we can integrate. This class recieves the data from the JSON return and loops through each item in the return and adds it to the model we've just created. 

   > **Note** here we make sure the json data is not null - it's always best practice to ensure your are parsing the correct data and not introducing more bugs into your code.  

   ```dart
   class MyJSONData {
     List<JobDataModel> data;
   
     MyJSONData({this.data});
   
     MyJSONData.fromJson(dynamic json) {
       if (json != null) {
         data = new List<JobDataModel>();
         json.forEach((v) {
           data.add(new JobDataModel.fromJson(v));
         });
       }
     }
   }
   ```

9. We are now going to add the following code to the _JSONParser class. Notice here we are the two variables loc and dec which contain the Strings london and mobile+apps. These are the query parameters we use in the construction of the url. Look at how the url is consutructed with the concatenated variables using the '$' as a precursor to including the variable. 

   ```dart
   Future<List<JobDataModel>> _getJobs() async {
     String loc = "london";
     String des = "mobile+apps";
     String url =
       'https://jobs.github.com/positions.json?description=$des&location=$loc';
     Response resp = await get(url);
     List jsonArrayData = json.decode(resp.body);
     var jsonData = new MyJSONData.fromJson(jsonArrayData);
     print(jsonData.data.length);
     return jsonData.data;
   }
   ```

10. Inside this class we will now use the FutureBuilder Widget. Which tells the OS that we are waiting for something in the future and once it arrives draw it on screen. You will see the future calls the _getJobs() function which is an async method (which performs the http get request).

    ```dart
    @override
    Widget build(BuildContext context) {
      return new Scaffold(
        body: Container(
          child: FutureBuilder(
            future: _getJobs(),
            builder: (BuildContext context, AsyncSnapshot snapshot) {
              print(snapshot.data);
              if (snapshot.data == null) {
                return Container(child: Center(child: Text("Loading...")));
              } else {
                return ListView.builder(
                  itemCount: snapshot.data.length,
                  itemBuilder: (BuildContext context, int index) {
                    return ListTile(
                 			...
                      title: Text(snapshot.data[index].title),
     									...
                    );
                  },
                );
              }
            },
          ),
        ),
      );
    }
    ```



<img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab15/5.png?raw=true" alt="Mobile UI to show JSON data" style="zoom:50%;" />

### 3. Extending your data model

Now you are have the basics working. You now need to extend the model and presentation to the user. 

1. Extend the model to include the company logo and url from the JSON object.
2. Use the data from the model and present this to the user in the ListTile as:
   - CircleAvatar (company logo).
   - subtitle (url).

<img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab15/6.png?raw=true" alt="Extension of the UI to include logo and url" style="zoom:50%;" />



<div class=footer><div class=footer-text>  CO2509 Mobile Computing | LAB 15</div></div>

