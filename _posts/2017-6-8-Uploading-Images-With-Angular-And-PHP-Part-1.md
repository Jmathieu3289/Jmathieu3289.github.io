---
layout: post
title: Uploading Images with Angular and PHP - Part 1 - AngularJS
---

In my current project, an onling catering platform, there needs to be a way for users to manage products. Part of that product management is having an image to go along with the product so that customers can have a more visual way of browsing the catering menu. Image upload and hosting is a well-worn subject, but this was my approach with AngularJS, PHP, and Amazon S3. In part one I'll be going over the front-end setup using AngularJS and the [ng-file-upload](https://github.com/danialfarid/ng-file-upload) directive.


### Installing ng-file-upload

Before getting started, it is important to note that this project uses [AngularJS](https://angularjs.org/), rather than [Angular](https://angular.io/). I wish they'd make the distinction more clear in the naming of these two very different frameworks, but bad names for things in Javascript seems to be the norm. I digress.. 

To start out, I first needed to reference the ng-file-upload files in my project. [Installation instructions](https://github.com/danialfarid/ng-file-upload#install) are pretty straight forward, and I chose to manually download the necessary files and reference them. After adding the files to the project's /js/vendor directory and referencing them, it looked something like this:

{% highlight HTML %}
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.1/angular.min.js"></script>
<script src="/js/vendor/ng-file-upload.min.js"></script>
<script src="controller.js"></script>
{% endhighlight %}

In the controller file, to require the ng-file-upload directive I added it to the module declaration...

{% highlight Javascript %}
var mainApp = angular.module('mainApp', ['ngFileUpload']);
{% endhighlight %}

..and then passed it into the controller as Upload:

{% highlight Javascript %}
mainApp.controller('MainCtrl', function ($rootScope, $scope, $http, Upload) {
{% endhighlight %}

With that out of the way, installation is done and it was time to move on to setting up the HTML!


### HTML Setup

Using the ng-file-upload directive, the HTML work is fairly minimal. The first thing I had to do was set up an `img` element to show the picture being uploaded:

{% highlight HTML %}
<img ngf-src="file || '/media/placeholder.png'"/>
{% endhighlight %}

Here the important thing is ngf-src. The documentation is [here](https://github.com/danialfarid/ng-file-upload#file-preview). A simple conditional allowed me to show a placeholder if a user has not yet uploaded an image.

The second part was to set up a button which allows the user to choose a file for upload:

{% highlight HTML %}
<button ngf-select ng-model="file" ngf-resize="{width: 1000, height: 1000, type: 'image/jpeg'}" ngf-pattern="'.jpg'" ngf-accept="'image/jpeg'">choose image (1000x1000 .jpg)</button>
{% endhighlight %}

There is a lot going on here, so I'll break down each part individually. Documentation can be found [here](https://github.com/danialfarid/ng-file-upload#file-select-and-drop).

**`ngf-select`**   - Tells our program that this button will act as our choose file dialog activation.
**`ng-model`**     - When the user chooses a file it will be bound to this variable.
**`ngf-resize`**   - After choosing the file, applies some basic resizing/cropping/quality. Useful for doing quick image compression (although I'll talk more about that later).
**`ngf-pattern`**  - Controls which file types are filtered in the open file dialog.
**`ngf-accept`**   - Converts to the standard HTML accept attr.

When a user selects a file, it now gets resized to 1000x1000 and displayed in the image tag automatically since we are binding to `file`. With that all out of the way it was time to move on to the file upload code.


### File Uploading

After everything on the HTML side is good to go, the last step was to get the file uploaded. The ng-file-upload directive comes with a service for just that. Similar to the Angular $http service, a url, method, and the data to be sent must all be provided, and a promise is returned. The code looked like this:

{% highlight Javascript %}
Upload.upload({
    url: $scope.selectedItem.id==null?"/api/item/save":"/api/item/save/"+$scope.selectedItem.id,
    method: $scope.selectedItem.id==null?"POST":"POST",
    data: {
        file: $scope.file,
        data: $scope.selectedItem
    }
}).then(function (response) {
    if(response.data.errors == null){
        $scope.selectedItem = null;
        getItems();
    }else{
        swal("Error updating item.", "", "error");
    }
});
{% endhighlight %}

The url passed depends on whether the item being saved has an existing id. If it doesn't, we can assume that this is a new item, and we pass it to our save REST endpoint. If it does, this item needs to be patched instead. Having some difficulty getting the data out of a PATCH request using Slim (the project's PHP microframework), I created a second POST endpoint for updating existing items. Technically for method I could simplify this down to just POST, but at some point in the future it would be best practice to switch over to a PATCH route for updating.

The data being passed in is pretty straightforward, the image which has been bound to the variable `file` and the item data. The REST endpoints return a standard envelope with any errors, so if the errors are null then the item has been successfully saved/updated. 


### Wrapping Up

With that, the front-end code is complete. In the next post I'll talk about the server side code for receiving the image, doing image compression, and uploading to S3!

