<img  align="right" src="https://github.com/UCLanCSC/co2509-resources/blob/master/general/logo.jpg?raw=true" style="zoom:10%" />

# CO2509 : Mobile Computing

### Lab 16.1: SQLlite as a local storage for mobile applications.



## Lab Summary

This lab continues the learning into making mobile applications that utilise storage platforms. In particular this lab focusses on using SQLite. In the lab you will learn how to set up your applications to store data locally using SQLite. From here you will learn how to integrate your Flutter application with SQLite by performing basic CRUD (Create, Read, Update, Delete) operations. This is useful as it will enable your app storage to work across devices. 



## Aim

This lab aims to provide you with the knowledge of extending your Flutter applications to store data locally. This will be achieved through including SQLite into your applications packaged dependencies. 



## **Objectives**

This lab aims to achieve the following objective(s) below:

**O.1**: Learn the fundamentals about adopting SQLite as a local storage option

**O.2**: Learn CRUD operations for storing, reading, updating and deleting records



## Reading Material 

- What is [SQLite](https://www.sqlite.org/index.html)?
- [SQFLite](https://pub.dev/packages/sqflite) package
- [Using SQLite in Flutter](https://medium.com/flutter-community/using-sqlite-in-flutter-187c1a82e8b)



## Introduction

As with any project, the right tooling is vital and getting these tools to work for you takes time to master. In this lab you will learn about building mobile applications that wish to store data locally. Storing data locally is great for providing an effective user experience. If there is no valid connection that user should still be able to use your application to some extent or if the connection is slow you can retrieve information faster as its stored locally rather than in the cloud. 

As in your assignment you should be thinking about what elements of your application needs persistence and in particular what type of persistence should be considered (Shared Object, local storage or cloud/server storage). 

As a companion to the main lab, this lab will teach you about SQLite and therefore it's important to understand the difference. With Firebase you are creating a storage option that is in the cloud. Although Firebase handle offline persistence (when enabled) by queuing data for when an active connection is available. it is recommended you build you own local database to demonstrate your understanding of how you can read, write, update and delete data. 

SQLite is used amongst many mobile development environments in many guises. For example, you might hear a difference name be mentioned for storage but ultimately at a lower level it's SQLite that stored the data. SQLite is similar to MySQL by providing similar queries to interface with the data and many of the same operations (joins, migrations, updates and many more). Like MySQL, SQLite is also a relational database which is different to Firebase and you  



## SQLite

### 1. Getting Started

Either continue with a previous application or create a new project. Add the SQFLite package to your pubspec.yaml file (check which is the current version at the time of writing this 1.3.2 was the most recent). You are also going to need the [path](https://pub.dev/packages/path) library adding to the dependency list.

```dart
dependencies:
  ...
  sqflite: ^1.3.2
```

> **Remember:**
>
> When adding a new dependency you will need to perform 'flutter pub get' in the terminal.

If you are creating a new project use the following boilerplate code to start to you off. 

*main.dart*

```dart
import 'package:flutter/material.dart';
//this is the the view where I will demonstrate SQLite capabilities
import 'SQLite.dart';

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
            //the home for the application will load the view inside the SQLite.dart file
            child: MySQLiteWidget(),
          ),
        ));
  }
}
```

*SQLite.dart*

```dart
import 'package:flutter/material.dart';

class MySQLiteWidget extends StatefulWidget {
  MySQLiteWidget({Key key}) : super(key: key);

  @override
  _MySQLiteWidgetState createState() => _MySQLiteWidgetState();
}

class _MySQLiteWidgetState extends State<MySQLiteWidget> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(body: Container());
  }
}
```

Like other packages you need to import the dart package inside the view you want to use the SQLite database operations. 

```dart
//sqlite library
import 'package:sqflite/sqflite.dart';
//path library
import 'package:path/path.dart' as p;
```

### 2. Creating and Opening the Database

When the application first loads, you will want to create the database, the next time it loads you just want to open the database and not create another instance of it. 

First let's create the database helper file. This will contain the code needed to create a database and the tables you need for your application. In this example the Students table is being created with three columns (ID, Name and Email).

*DatabaseHelper.dart*

```dart
import 'package:path/path.dart';
import 'package:sqflite/sqflite.dart';
import 'Student.dart';

class DatabaseHelper {
  static final _databaseName = "myDb.db";
  static final _databaseVersion = 1;

  static final table = 'Students';

  static final columnId = 'id';
  static final columnName = 'name';
  static final columnEmail = 'email';

  DatabaseHelper._privateConstructor();
  static final DatabaseHelper instance = DatabaseHelper._privateConstructor();

  static Database _database;
  Future<Database> get database async {
    if (_database != null) return _database;
    _database = await _initDatabase();
    return _database;
  }

  _initDatabase() async {
    String path = join(await getDatabasesPath(), _databaseName);
    return await openDatabase(path,
        version: _databaseVersion, onCreate: _onCreate);
  }

  // SQL code to create the database table
  Future _onCreate(Database db, int version) async {
    await db.execute('''
          CREATE TABLE $table (
            $columnId INTEGER PRIMARY KEY AUTOINCREMENT,
            $columnName STRING NOT NULL,
            $columnEmail STRING NOT NULL
          )
          ''');
  }

  Future<int> insert(Student s) async {
    Database db = await instance.database;
    var res = await db.insert(table, s.toMap());
    return res;
  }

  Future<List<Map<String, dynamic>>> queryAllRows() async {
    Database db = await instance.database;
    var res = await db.query(table, orderBy: "$columnId DESC");
    return res;
  }

  Future<int> update(Student s) async {
    Database db = await instance.database;
    return await db
        .update(table, s.toMap(), where: "$columnId = ?", whereArgs: [s.id]);
  }

  Future<int> delete(int id) async {
    Database db = await instance.database;
    return await db.delete(table, where: '$columnId = ?', whereArgs: [id]);
  }

  Future<void> drop() async {
    Database db = await instance.database;
    return await db.rawQuery('DROP TABLE IF EXISTS $table');
  }

  Future<void> clearTable() async {
    Database db = await instance.database;
    return await db.rawQuery("DELETE FROM $table");
  }
}
```

You will notice there is a class named Student.dart, this is our data model. 

*Student.dart*

```dart
class Student {
  final int id;
  final String name;
  final String email;

  Student({this.id, this.name, this.email});

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'name': name,
      'email': email,
    };
  }
}

```

### 3. Extending to include CRUD

Every database needs to have CRUD operations; Create, Read, Update and Delete. In the below example you will update the DatabaseHelper.dart file by extending its capabilities to insert, edit, update and remove data. 

```dart
Future<int> insert(Student s) async {
  Database db = await instance.database;
  var res = await db.insert(table, s.toMap());
  return res;
}
Future<List<Map<String, dynamic>>> queryAllRows() async {
  Database db = await instance.database;
  var res = await db.query(table, orderBy: "$columnId DESC");
  return res;
}
Future<int> delete(int id) async {
  Database db = await instance.database;
  return await db.delete(table, where: '$columnId = ?', whereArgs: [id]);
}
Future<void> clearTable() async {
  Database db = await instance.database;
  return await db.rawQuery("DELETE FROM $table");
}
```

These methods can now be used inside your application by calling the DatabaseHelper file. 

### 4. Reading from an SQLite Table

You are now ready to read data from the database. Inside the SQLite.dart file you are to create an initial state that reads any data in the database in particular from the Students table.

You want to initialise your app with what ever data is inside the database. For this, you will need to query all rows at the start of the apps lifecycle. Override the initState in the _MySQLiteWidgetState class and add the following code.

```dart
 @override
  void initState() {
    super.initState();

    DatabaseHelper.instance.queryAllRows().then((value) {
      setState(() {
        value.forEach((element) {
          print(element['id'] +
              " - " +
              element["name"] +
              " - " +
              element["email"]);
        });
      });
    }).catchError((error) {
      print(error);
    });
  }
```

 This code will be ran when the setState method is called when the view is loaded. This is useful if you want to populate data in a List. 

> **Remember:** 
> You will need to use a Future Builder or List Builder for achieving this  

Moving on, we are going to change the recently added initState() remove lines so you are left with the following.

```dart
@override
  void initState() {
    super.initState();
		// if you want to do anything when the view is loaded in the initial state you 				should wrap your
    // algorithm inside the setState() method

    setState(() {
      // you cant make setState an async call but you can call another method and 				define this as async
      dbOperations();
    });
  }
```

Create the following dbOperations() method outside of the initState(). 

```dart
void dbOperations() async {
}
```

Now let's add the functionality to perform reading operations.

```dart
Future<List<dynamic>> _queryAllRows() async {
  var res = await DatabaseHelper.instance.queryAllRows();
	return res;
}
```

This method queries all data in the table and returns that data. However you will want to query a specific item in the table.

Create a new method similar to this one, that queries only one item. It should be defined as 'Future<List<Map<String, dynamic>>>' and follow the same structure as the previous. However you should call queryItem and pass the int ID.

> **Hint:** 
> You will need to also add more code to the DatabaseHelper file. that performs this query.



```dart
Future<List<Map<String, dynamic>>> queryItem(int id) async {
  Database db = await instance.database;
  List<Map> results = await db.query("$table",
     columns: ["$columnId", "$columnName", "$columnEmail"],
     where: '$columnId = ?',
     whereArgs: [id]);

  if (results.length > 0) {
    return results;
  }

	return null;
}
```

Now within your dbOperations you can call the method you've created. As you can see the _getItem is called by passing the ID to the method and the response is returned contained the queried item.

```dart
var res3 = await _getItem(id);
print("query ID $id" + res3.toString());
```

### 5. Creating an item in an SQLite Table

Add the following snippet outside of dbOperations(). This code will call the insert method and pass the name and email data to it.

```dart
Future<int> _insertItem(String name, String email) async {
	var id =
		await DatabaseHelper.instance.insert(Student(name: name, email: email));
	return id;
}
```

Inside the dbOperations() add the following code. This will call the _insertItem you've just created and pass two Strings to it. As the _insertItem is of type Future Int, an int ID will be returned which can be printed to the console to make sure the item was added.

```dart
// performs the insert of Student and returns the ID of the inserted row
var id = await _insertItem("Mark Lochrie", "MLochrie@uclan.ac.uk");
print("ID " + id.toString());
```

### 6. Updating an item in an SQLite Table

Updating an item is similar to an insert. However, you need to supply the query with a key to search for the item you want to update. The following code should be added outside the dbOperations method. Look over the update method inside the DatabaseHelper file you created at the start to understand how this works.

```dart
Future<int> _updateItem(int id, String name, String email) async {
  var res = await DatabaseHelper.instance
  		.update(Student(id: id, name: name, email: email));
  return res;
}
```

As this is a Future<int> type method an integer is returned when called. Add the following code inside your dbOperations() method. This will pass the id of the item along with the new Strings to the updateItem method.

```
var updateRes =
    await _updateItem(id, "Ashley Lochrie", "ALochrie@uclan.ac.uk");
print("Res " + updateRes.toString());
```

### 7. Deleting an item in an SQLite Table

Like before we first need to add the method that interfaces with the DatabaseHelper main method. In its current form, this method doesn't return anything, you might want to make it return a boolean to know if the deletion has been successful. 

```dart
void _deleteItem(int id) async {
  await DatabaseHelper.instance.delete(id);
}
```

Likewise in the dbOperations() method we need to pass the id we want to delete. 

```dart
// items can also be deleted by passing the id of the row
_deleteItem(id);
```

### 8. Testing Mobile Applications that use an SQLite Database

When you are implementing persistence inside your mobile applications - the whole purpose is to create some form of storage solution and memory. The problem with this is that when you are testing your apps you will have data that you might want to remove. Its always useful to have  some methods that enables you to clear the application when it loads. 

```dart
void _emptyTable() async {
  await DatabaseHelper.instance.clearTable();
}
```

```dart
_emptyTable();
```

There is another method to clear the cache of your apps. This is achieved by interfacing with the mobile device (even the emulator). Find the app you want to clear the cache from. 

<div class=footer><div class=footer-text>  CO2509 Mobile Computing | Lab 16.1</div></div>