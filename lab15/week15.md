<img  align="right" src="https://github.com/UCLanCSC/co2509-resources/blob/master/general/logo.jpg?raw=true" style="zoom:10%" />

# CO2509 : Mobile Computing

### Lab 13: Lab Catch up -> Coursework support



## Lab Summary

This lab is a follow on lab that continues your learning about making HTTP requests from within your Flutter applications. You will also learn about how to debug making these calls using other programs - in this lab we will use Postman for this.     



> **Remember:**
>
> It's important you complete each lab sheet in order, as there is an expectation in each weeks lab that you have an understanding and can apply this to the following topics. There might be parts of the lab which won't explain a part of the code which you have already been taught, or gaps within the structure as we assume you know what you are doing at this point.



## Aim

This lab aims to provide you with the knowledge of extending your Flutter applications to make Internet calls 



## **Objectives**

This lab aims to achieve the following objective(s) below:

**O.1**: x



## Reading Material 



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
2. Paste the following URL (https://jobs.github.com/positions.json?description=mobile+apps&location=london) into the "Enter request URL" textbox. You will notice the program will automatically extract the query params from the URL into a key value pair. This makes it easier for you to test. 'Send' this request and investigate the response. 
   - you will notice that program makes the json response 'pretty' this makes it easier for you to explore.  
3. Change the values in the key value pair and observe the difference. 





