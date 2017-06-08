---
layout: post
title: Uploading Images with Angular and PHP - Part 1 - AngularJS
---

In my current project, an onling catering platform, there needs to be a way for users to manage products. Part of that product management is having an image to go along with the product so that customers can have a more visual way of browsing the catering menu. Image upload and hosting is a well-worn subject, but this was my approach with AngularJS, PHP, and Amazon S3. In part one I'll be going over the front-end setup using AngularJS and the [ng-file-upload](https://github.com/danialfarid/ng-file-upload) directive.


### Installing ng-file-upload

Before getting started, it is important to note that this project uses [AngularJS](https://angularjs.org/), rather than [Angular](https://angular.io/). I wish they'd make the distinction more clear in the naming of these two very different frameworks, but bad names for things in Javascript seems to be the norm. But I digress.. 

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