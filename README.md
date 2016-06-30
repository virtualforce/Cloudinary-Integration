# Cloudinary Integration -
### Introduction 

Purpose of this document is to enable the reader to quickly integrate cloudinary in his/her project. Our main focus will be on the essential features of cloudinary api. Essential features are listed below,
  
  * Fetching cloud resource and getting the corresponding image. 
  * Retaining information for future use. 
  * Upload asset to cloudinary cloud. 

Before we dig into technical details lets check out difference between alternative services out there.

### Alternative Services 

Below is a very brief difference between different services out there. 

#### [Cloudinary](http://cloudinary.com/)

  * It’s the most useful service out there with many feature and easy integration. Impressive free tier.
  * Images are delivered via CDN which can speed up thing on the client end.
  * GUI interface to manipulate your assets and manage them outside the system.
  * Documentation is excellent and fluent.
  * It uses internal storage rather than s3 kind of service. Which means it will be very hard to moving on if we use this service.
  * When you create temporary images, they stick around and do not go away automatically.
  * Suitable for low to mid level business which requires less expensive solutions.

#### [Imgix](https://www.imgix.com/)
  * Nice interface and easy to manage images.
  * Super fast image processing and CDN is up-to mark.
  * Flat rate pay as you use the service.
  * Only 10$ credit no free tier available.
  * Multiple platform easy integration and support libraries available.
  * Recommended by [rubyweekly.com](http://rubyweekly.com/).
  * Suitable for business where traffic rate can be judged and cost can be calculated.

#### [Filestack](https://www.filestack.com/)
* Images are delivered via CDN which can speed up thing on the client end.
* GUI interface to manipulate your assets and manage them outside the system.
* Various integrations to different platform available for example github,facebook and google drive etc,.  
* Little bit expensive than cloudinary. 
* Limited integrations with programming platforms. 

#### [Blitline](https://www.blitline.com/v3/home)
* It only gives you processing ability rather than a whole solution.
* You will need connect S3 for using in accordance to use this service.
* No official gem as Cloudinary gives support to multiple platforms.
* Less expensive but free tier is not useful to start the service.
* Most effective you are dealing very large images processing system.
 
Some more similar services [Filespin](https://filespin.io/) and [Kloudless](https://kloudless.com/).

### Fetch Resource

Fetching a resource from cloudinary is very simple and easy. Cloudinary store every file in there cloud with a unique id which is called public_id in there api documentation, on which you can get the image resource. Below is a simple example using in angular, ruby on rails and node cloudinary libraries.

#### Angular

Below are the steps to setup cloudinary into your angular application. 
* First acquire cloudinary via bower `bower install cloudinary_ng --save` and include the module in manifest file. Below are the files you will need to include, 
  ..* lodash/lodash.min
  ..* cloudinary-core/cloudinary-core.min 
  ..* cloudinary_ng/js/angular.cloudinary
* Include the module into your app `angular.module('appName',[cloudinary])`
* In your config block add this `cloudinaryProvider.set("cloud_name", cloud_name) ` , if you are using multi environments best to use with config.json.
* Now you can simply use the built in directive provided by the lib `<cl-image public-id="{some_public_id}" class="thumbnail inline" angle="20" format="jpg">
<cl-transformation height="150" width="150" crop="fill" gravity="north" effect="sepia" radius="20"/></cl-image>`

#### Ruby On Rails
 Below are the steps to setup cloudinary into your ruby on rails application. 
* First install the corresponding gem `gem install cloudinary` .
* Bundle install will automatically will create cloudinary.yml, you will just need to add credentials in that file. 
* For rendering resource you can do as follows, `cl_image_tag("sample.jpg", :width => 100, :height => 150, :crop => :fill)`.

#### Node.js
Below are the steps to setup cloudinary into your node.js application.
* First get the module `npm install cloudinary` .
* Then acquire the module `var cloudinary = require('cloudinary');` .
* Config the settings in your initialization block 
``` 
cloudinary.config({ 
	cloud_name: '', 
	api_key: '',
	 api_secret: '' 
	});
```
* Then you can use `cloudinary.url("sample.jpg", {width: 100, height: 150, crop: "fill"})`, you can get your resource. 
### Retain Resource
Unfortunately there is a limit on cloudinary admin api for free 500/hour and for paid subscription 2000/hour. For this reason, we will have to retain information of resource public_id on our end. So when retrieving resource, we are not hitting admin api to list down the number of resource in a given folder. So we can add a middle table which retain this information, for more dynamic solution we can add a polymorphic relationship. In next section we will see how we can upload a resource to server and then cloudinary.   
### Upload Resource
You can use number of given solution for uploading files to server but we will be using jquery file uploader because it's very convenient and well documented. Below are short steps to make it running. 
* Include jquery file uploader files steps are given [here](https://blueimp.github.io/jQuery-File-Upload/).
* Now create a http route in your application which will receive file or you can directly post files to cloudinary and then store the public in return but we will be doing the preceding approach. 
* Below is short and basic snapshot of codebase. 
```
$('#fileupload').fileupload({
dataType: 'json',
progressall: function(e, data) {
    var progress;
    progress = parseInt(data.loaded / data.total * 100, 10);
    $('#progress .bar').css('width', progress + '%');
    return $('#progress .bar').text(progress + '%');
    }
 });

<input id="fileupload" type="file" class="upload" name="files[]" data-url="path_to_server" multiple>
```
* On that path post the file to cloudinary, below are some example
```
  For Rails
  Cloudinary::Uploader.upload(url,:use_filename => true, :folder => folder_path)
  For Node.js	
  cloudinary.uploader.upload('my_image.jpg', function(result) { console.log(result) }, { public_id: "sample_id" });
```	
* Folder is the path where image will be stored for more structure on cloudinary admin interface, for this you will need to turn on option in user settings in cloudinary Settings located under upload section. 
* Now store the public id or full information returned by cloudinary. 
