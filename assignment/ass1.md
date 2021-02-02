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
    userId: "",
    password: "",
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
  // https://github.com/openfoodfacts/openfoodfacts-dart/blob/documentation/DOCUMENTATION.md
	String barcode = "0000000000000";
	String productName = "Mark's Doughnuts";

	var newProduct = Product(barcode: barcode, productName: productName);

	Status result =
		await OpenFoodAPIClient.saveProduct(UserConstants.USER, newProduct);

  if (result.status != 1) {
  	throw new Exception("product could not be added: " + result.error);
  }

  return result;
}
```

If you try running the following code you will be presented with an error. This error lies in the SDK. To correct this see you will need to Right click on saveProduct > select Go to definition > this will open up 'openfoodfacts.dart' replace the current saveProduct method with the following:

```dart
/// Add the given product to the database.
/// Returns a Status object as result.
static Future<Status> saveProduct(User user, Product product) async {
  var parameterMap = new Map<String, String>();
  parameterMap.addAll(product.toData());

  var productUri =
  Uri(scheme: URI_SCHEME, host: URI_HOST, path: '/cgi/product_jqm2.pl');

  Response response =
  await HttpHelper().doPostRequest(productUri, parameterMap, user);
  var status = Status.fromJson(json.decode(response.body));
  return status;
}
```

I have reported the bug, but at the time of writing this it was still apparent.

### 5. Adding a Product Image endpoint

There are some issues with the adding product image endpoint. This is out of scope for this module.

## Conclusion

You should now be able to implement the Open Food Facts API into your assessments. If you have any problems use the Microsoft Teams channel for Assignment Support to request help. 





<div class=footer><div class=footer-text>  CO2509 Mobile Computing | Ass 1</div></div>