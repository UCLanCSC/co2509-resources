<img  align="right" src="https://github.com/UCLanCSC/co2509-resources/blob/master/general/logo.jpg?raw=true" style="zoom:10%" />

# CO2509 : Mobile Computing

### Assessment 1: Understanding the Open Food Facts API 



## Summary

This work will guide you through implementing the Open Food Facts API into your assessment. It will not provide you the building blocks for implementing but will be prompts to help you get the basic reading and writing features of the API working. You should demonstrate your understanding by advancing this in your assignment.



## Aim

The aim of this worksheet is to assist in integrating the basic functionality of the Open Food Facts API into your assignment. The purpose of putting this worksheet together is based on widely available instructions and sample code online brought together into one readme. This worksheet will not guide you through integrating it into your projects, you are expected to do this yourself.



## **Objectives**

This worksheet aims to achieve the following objective(s) below:

**O.1**: Provide additional knowledge on how to integrate the open food facts API into your projects



## Reading Material 

- [Open Food Facts API](https://world.openfoodfacts.org/) Account
- [Github repo](https://github.com/openfoodfacts/openfoodfacts-dart) for Open Food Facts API for Dart



## Introduction

If you are implementing route b of the assessment. You will need to implement the Open Food Facts API. You could do this by calling the raw URL endpoints or by using the Open Food Fact API package. Either method is acceptable for your assessment. However, you might want to consider using the package as a lot of the complexity is handled for you.



## Open Food Facts API Implementation

### 1. Getting Started

First, you will need to include the dependency for [open food facts](https://pub.dev/packages/openfoodfacts) 

```dart
dependencies:
  openfoodfacts: ^0.3.14+1
```

> **Remember:**
>
> When adding a new dependency you will need to perform 'flutter pub get' in the terminal.

You will need to create your own account on [Open Food Facts](https://world.openfoodfacts.org/) keep your username and password safe as you will use these credentials to perform the requests. As these details are constants you might want to keep these in a separate dart file 

*UserModel.dart*

```dart
import 'package:openfoodfacts/model/User.dart';

class UserConstants {
  static const User USER = const User(
    // you need to use your account user id and not email address here
    userId: "",
    password: "",
    // add a comment to your user profile something relevant to your profile
    comment: "dummy text",
  );
}
```

Like other packages you need to import the dart package inside the view you want to use the API operations. 

```dart
//openfoodfacts libraries
import 'package:openfoodfacts/openfoodfacts.dart';
import 'package:openfoodfacts/model/parameter/TagFilter.dart';
import 'package:openfoodfacts/interface/Parameter.dart';
import 'package:openfoodfacts/model/SearchResult.dart';
import 'package:openfoodfacts/model/Nutriments.dart';
import 'package:openfoodfacts/utils/LanguageHelper.dart';
import 'package:openfoodfacts/utils/ProductFields.dart';
import 'package:openfoodfacts/utils/ProductSearchQueryConfiguration.dart';
```

### 2. Get All Products endpoint

The Get All Products method is of type Future<SearchResult>, SearchResult is a Open Food Facts API predefined type. Not here its set as an async method, this means that that application can expect some data to appear at some point in the lifecycle.  If you are unsure about async/await you should go back through the lecture notes.

The below method requests 20 items of type breakfast cereals in a JSON format, sorted by product name. Once the API returns the data you can used it within your application.

*getAllProducts()*

```dart
Future<SearchResult> getAllProducts() async {
  var parameters = <Parameter>[
    const OutputFormat(format: Format.JSON),
    const PageSize(size: 20),
    const SearchSimple(active: true),
    const SortBy(option: SortOption.PRODUCT_NAME),
    const TagFilter(
        tagType: "categories", contains: true, tagName: "breakfast_cereals"),
		];

  ProductSearchQueryConfiguration configuration =
      ProductSearchQueryConfiguration(
          parametersList: parameters,
          fields: [ProductField.ALL],
          language: OpenFoodFactsLanguage.ENGLISH);

  // the UserConstants used here are your user account details provided in the UserModel.dart file
  SearchResult result = await OpenFoodAPIClient.searchProducts(
      UserConstants.USER, configuration);

  return result;
}
```

If you are to call this method you will want to ensure your return is stored in type 'SearchResults' and is handled as 'jsonProducts'.

### 3. Get Product endpoint

The next endpoint you will need is to get a singular product. In the example below I've hardcoded the barcode '0048151623426' which should return Maryland Choc Chip cookies. 

*getProduct()*

```dart
/// request a product from the OpenFoodFacts database
Future<Product> getProduct() async {
  var barcode = "0048151623426";

  ProductQueryConfiguration configuration = ProductQueryConfiguration(barcode, language: OpenFoodFactsLanguage.ENGLISH, 	fields: [ProductField.ALL]);
	ProductResult result = await OpenFoodAPIClient.getProduct(configuration);
  
  if (result.status == 1) {
    return result.product;
  } else {
    throw new Exception(
      "product not found, please insert data for " + barcode);
  }
}
```

If you are to call this method you will want to ensure your return is stored in type 'Product' and its format is JSON.

### 4. Adding a Product endpoint 

You will need to add more attributes for your product see [documentation](https://github.com/openfoodfacts/openfoodfacts-dart/blob/documentation/DOCUMENTATION.md)

*addNewProduct()*

```dart
Future<Status> addNewProduct() async {
    // define the product to be added.
    // more attributes available ...
    String barcode = "0000000000001";
    String productName = 'BreadAhead Doughnuts';
    var newProduct = Product(barcode: barcode, productName: productName);
    Status result =
        await OpenFoodAPIClient.saveProduct(UserConstants.USER, newProduct);
    if (result.status != 1) {
      throw new Exception("product could not be added: " + result.error);
    }
    return result;
  }
```

Try this with your own product and barcode and see if it appears in your "products you've added or edited" you can see the above example [here](https://world.openfoodfacts.org/product/1212121212/breadahead-doughnuts-null)  

<img src="https://github.com/UCLanCSC/co2509-resources/blob/master/assignment/1.png?raw=true" alt="Open Food Facts Account" style="zoom:50%;" />5. Adding a Product Image endpoint

*addProductImage()*



```dart
/// add a new image for an existing product of the OpenFoodFacts database
Future<Status> addProductImage() async {
  File f = await getImageFileFromAssets('doughnuts.jpg');

  // define the product image
  // set the uri to the local image file
  // choose the "imageField" as location / description of the image content.
  SendImage image = new SendImage(
    lang: OpenFoodFactsLanguage.ENGLISH,
    barcode: "0000000000000",
    imageField: ImageField.INGREDIENTS,
    imageUrl: Uri.parse(f.path));

  // query the OpenFoodFacts API
  Status result = await OpenFoodAPIClient.addProductImage(UserConstants.USER, image);

  if (result.status != "status ok") {
  	throw new Exception("image could not be uploaded: " +
  	result.error + " " + result.imageId.toString());
  }

  return result;
}
```

I have a file called doughnuts.jpg that is stored in a directory assets in the root of my application. Also in the pub spec.yaml file I have added the reference to it:

```dart
assets:
     - assets/doughnuts.jpg
```

Furthermore as you can't directly access the URI of the assets bundle once you app is compiled, you need to create a copy of the image and store it in a temporary directory which you then can upload to the endpoint. You will need to include some more imports along with the path_provider package (path_provider: ^1.6.27).

```dart
import 'package:flutter/services.dart' show rootBundle;
import 'package:path_provider/path_provider.dart';
import 'dart:async';
import 'dart:io';
```

The following method will create a temporary image from the asset bundled image:

*getImageFileFromAssets()*

```dart
Future<File> getImageFileFromAssets(String path) async {
  final byteData = await rootBundle.load('assets/$path');

  final file = File('${(await getTemporaryDirectory()).path}/$path');
  await file.writeAsBytes(byteData.buffer
  .asUint8List(byteData.offsetInBytes, byteData.lengthInBytes));

  return file;
}
```

The line 'File f = await getImageFileFromAssets('doughnuts.jpg');' calls the getImageFileFromAssets() method and supplies it with the image name. The method returns a new file in the temporary directory which you then can get access to the path by calling f.path. As you can see, a successful request will result in returning the following message:

```dart
I/flutter (24571): body {comment: some comment, user_id: mlochrie-uclan-ac-uk, password: ******, lang: OpenFoodFactsLanguage.ENGLISH, code: 0000000000000, imagefield: ingredients}
I/flutter (24571): files {imgupload_ingredients: /data/user/0/com.example.openfoodfacts_api/cache/doughnuts.jpg}

```

You will notice the temporary file was created in the 'cache' directory. 

## Conclusion

You should now be able to implement the Open Food Facts API into your assessments. If you have any problems use the Microsoft Teams channel for Assignment Support to request help. 





<div class=footer><div class=footer-text>  CO2509 Mobile Computing | Ass 1</div></div>