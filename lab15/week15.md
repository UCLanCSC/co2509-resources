<img  align="right" src="https://github.com/UCLanCSC/co2509-resources/blob/master/general/logo.jpg?raw=true" style="zoom:10%" />

# CO2509 : Mobile Computing

### Lab 15: Flutter and the Internet, debugging API calls and integrating into Flutter applications



## Lab Summary

This lab is a follow on lab that continues your learning about making HTTP requests from within your Flutter applications. You will also learn about how to debug making these calls using other programs - in this lab we will use Postman for this.     



> **Remember:**
>
> It's important you complete each lab sheet in order, as there is an expectation in each weeks lab that you have an understanding and can apply this to the following topics. There might be parts of the lab which won't explain a part of the code which you have already been taught, or gaps within the structure as we assume you know what you are doing at this point.



## Aim

This lab aims to provide you with the knowledge of extending your Flutter applications to make HTTP request and handling these requests by presenting the data to the user.  



## **Objectives**

This lab aims to achieve the following objective(s) below:

**O.1**: Learn about debugging APIs.

**O.2**: Extend Flutter application by making HTTP requests and storing the data inside a model to be presented to the user using a Card Tile List.



## Reading Material 

- Level up your Postman skills -> https://www.youtube.com/playlist?list=PLM-7VG-sgbtAgGq_pef5y_ruIUBPpUgNJ



## Introduction

As with any project, the right tooling is vital and getting these tools to work for you takes time to master. In this lab you will learn about building mobile applications that require an Internet connection. Moving from local apps that do not rely on a backend service to apps that provide dynamic content. In the lecture we talked about these apps, in particular mapping technologies, multiplayer games, messaging and communication platforms etc... 



## Postman

You know from previous labs that in Flutter we need to include the '**http**' library, this gives our applications the ability to make HTTP requests. Now we have an additional component to handle, we have the Flutter application and the HTTP request/response. When you build applications to parse HTTP data you need to think about where errors might occurs. Of course you might have errors in your code when parsing objects, but you now might also have errors on the server (backend). Debugging these can be an issue. Therefore, its always best practice to use other tools to debug where the issue might be coming from. For any project that requires an API, I use Postman for creating, sharing and testing an API. 

Postman allows you to create and save HTTP/s requests as well as the responses, you set up your connections, input your test criteria, debug the response and save for later. This enables you to visualise the data in a meaningful way. Which results in a more efficient and less tedious way of working. 

Of course there are other programs on the market, but I've always found Postman to do an excellent job. Postman works across platforms on:

* [Windows x32](https://dl.pstmn.io/download/latest/win32)
* [Windows x64](https://dl.pstmn.io/download/latest/win64)
* [Linux](https://dl.pstmn.io/download/latest/linux64)
* [MacOS](https://dl.pstmn.io/download/latest/osx)
* [Web](https://identity.getpostman.com/signup?continue=https%3A%2F%2Fgo.postman.co%2Fbuild)



## Lab Activity

**Before you continue :** make sure you have downloaded Postman for your chosen operating system. 

### 1. Setting up Postman to debug an API

Now you have Postman setup we are going to learn how to debug an API request/response. for this example we are going to use Githubs Jobs API which returns JSON based on a set of criteria to search their databases and returns a response. 

1. Create a new request, provide it with a sensible name and create a collection where you want the request to be saved.

     <img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab15/1.png?raw=true" alt="Postman create request dialog" style="zoom:50%;" />

2. Paste the following URL (https://jobs.github.com/positions.json?description=mobile+apps&location=london) into the "Enter request URL" textbox. You will notice the program will automatically extract the query params from the URL into a key value pair. This makes it easier for you to test. 'Send' this request and investigate the response. 

   <img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab15/2.png?raw=true" alt="Postman workspace" style="zoom:50%;" />

   - you will notice that program makes the json response 'pretty' this makes it easier for you to explore.  

   <img src="https://github.com/UCLanCSC/co2509-resources/blob/master/lab15/3.png?raw=true" alt="JSON pretty response" style="zoom:50%;" />

3. Change the values in the key value pair and observe the difference. 



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



### 3. Extending your data model

Now you are have the basics working. You now need to extend the model and presentation to the user. 

1. Extend the model to include the company logo and url from the JSON object.
2. Use the data from the model and present this to the user in the ListTile as:
   - CircleAvatar (company logo).
   - subtitle (url).





