---
layout: post
title: Uploading Images with Angular and PHP - Part 2 - PHP
---

In the last post I went over the front end code for my image upload solution. You can see that post [here](https://jmathieu3289.github.io/Uploading-Images-With-Angular-And-PHP-Part-1/). In this part I'll be going over the PHP code, image compression, and uploading to S3.


### Getting the image from POST data

The API for this project is a standard REST framework, specifically SlimPHP. I like Slim because of it's low overhead and ease of set up. Getting the data from a POST request is straightforward. The first thing to do is to check the `$_FILES` array for the file I passed in earlier. 

{% highlight PHP %}
if(!empty($_FILES['file'])){
    $im = $_FILES['file']['tmp_name'];
}
{% endhighlight %}

Here, `tmp_name` is the filepath on the server to the uploaded image. Typically you could simply move this temporary file to a more permanent location on the server and pass back the final url to the application for storing in the DB with the associated product. In the beginning, this is exactly what I did. However after uploading a new version of the application to Elastic Beanstalk I had a realization that had me putting palm to face hard. The application is based on a docker configuration, meaning every time the EC2 instance is restarted/replaced, there goes all the data not associated with the docker image, including the images that have been uploaded.

It was time to move to a more permanent solution, instead uploading the images to S3.

### AWS S3 Installation and Setup

To install the AWS sdk for PHP, I added the following to my composer.json file:

{% highlight PHP %}
{
    "require": {
        "aws/aws-sdk-php": "^3.28"
    }
}
{% endhighlight %}

Once installed, configuration involves creating a configuration object and passing it as a parameter to a new instance of the SDK. After this, an S3 client can be created.

{% highlight PHP %}
$sharedConfig = [
        'region' => 'us-east-1',
        'version' => 'latest',
        'credentials' => [
            'key' => 'YOUR_KEY_HERE',
            'secret' => 'YOUR_SECRET_HERE'
        ]
    ];

$sdk = new Aws\Sdk($sharedConfig);

$client = $sdk->createS3();
{% endhighlight %}

In a production environment you would **absolutely not want to keep your key and secret in your main PHP file**, instead using EC2 environment variables. 

Once the client has been instantiated, it's time to upload the file.


### File Uploading

The S3 client object has a `putObject` method for handling image uploads, and requires a bucket, key, sourcefile, content type, and ACL.

{% highlight PHP %}
$result = $client->putObject([
    'Bucket' => 'website.com',
    'Key' => 'images/'.$_FILES['file']['name'],
    'SourceFile' => $_FILES['file']['tmp_name'],
    'ContentType' => 'image/*',
    'ACL' => 'public-read'
]);
{% endhighlight %}

In the above, I am storing the image in an S3 bucket I've created ahead of time named after the website. I want to store all images in a subfolder, images, which I've passed before the file name in the key. The sourcefile is the temporary file on the server, as mentioned above. `ContentType` lets S3 what type of file is being uploaded, and `ACL => 'public-read'` allows the file to be accessed publicly. 

Finally, we need to create a string of the URL to this file so that we can persist the location in the DB.

`$destination = 'https://s3.amazonaws.com/website.com/images/'.$_FILES['file']['name'];`

In a previous version of this project we've run into issues with slow loading of images. One way to solve the problem would be to get the art department to compress images for web, but less work is always better, and doing some automatic compression on the server before upload ensures that the site will stay snappy while maintaining good image quality.


### Image Compression with ImageMagick

After doing some research on image compression, I settled on using the PHP library [ImageMagick](http://php.net/manual/en/book.imagick.php). Another option would have been to compress the images on the client side using the ng-file-upload directive from part one, but results were mixed to downright bad, and I wanted to ensure images blown up to full-screen on the website still looked sharp. 

Installing ImageMagick involved some slight modifications to the project's Dockerfile:

{% highlight %}
RUN apt-get update -y
RUN apt-get install -y php-xml php-imagick
{% highlight %}

Here, `apt-get update -y` ensures we reading the newest packages, and `php-xml' is required for `php-imagick`. Once installed, only 4 lines of code were needed to get up and running: 

{% highlight PHP %}
//Compress the image
$im = new Imagick($_FILES['file']['tmp_name']);
$im->setImageCompression(Imagick::COMPRESSION_JPEG);
$im->setImageCompressionQuality(50);
$im->writeImage();
{% endhighlight %}

Here, we're taking the original temp file on the server, setting the image compression type (the only accepted file upload type is JPEG so we can ensure we're dealing with JPEGs), setting the compression quality, and then writing back the changes to the file.

After playing around with different levels of compression I found that 50 was fine for our purposes. Most of the time customers will be looking at small versions of the pictures along with the descriptions, but for those who want to blow up the image to full-screen the quality holds up well enough. At 50 the images are also very small in filesize and the site loads much quicker than the previous versions. 

And that's it! The image is now compressed at the original temp location and can be used with the above S3 code, no modifications needed!


### Wrapping Up

With that, the image is uploaded, compressed, uploaded to S3, and the reference to the image is stored to the DB for the product. Here is a picture of the finished product on the website!

![alt text](https://jmathieu3289.github.io/images/image-upload.png "images on the website")