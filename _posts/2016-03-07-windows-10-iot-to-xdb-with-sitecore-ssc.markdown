---
layout: post
title: Windows 10 IoT to XDB with Sitecore SSC
date: 2016-03-07 13:41:31.000000000 +00:00
type: post
published: true
status: publish
comments: true
categories:
- IoT
- Sitecore
- Sitecore.services.client
- Web Item API
tags:
- Entity Service
- EntityService
- Sitecore.Services.Client
- SSC
- UWP
- Windows 10 Core
- Windows Universal Platform
meta:
author:
  display_name: Mike Robbins
  first_name: ''
  last_name: ''
---
Recently i've been playing with Windows 10 IoT Core, i've wondered if its possible to use the Raspberry Pi in some way with Sitecore.

The idea i've had, was to create a kiosk style application to capture a user information at an event and post this data into Sitecore's xDB.  This kiosk application could be used as a competition encouraging the users to submit the form, and allow reporting from xDB who visited the event.

Any data captured and sent to Sitecore xDB can then be used to personalise the experience for the user.

## The Frameworks

### Windows Universal Platform

On the kiosk side, this demo application is written using Windows Universal Platform (UWP) framework running on a Raspberry Pi (2 or 3) on Windows 10 core.

The power of Windows Universal Platform means we can write our code once and run the application on multiple platforms. For example this code will work on Windows 10, Windows Phone, Windows IoT devices such as the Raspberry Pi and coming soon, the Xbox One.

Windows 10 for the Raspberry Pi and instructions for setting up Windows 10 and Visual Studio 2015 for Windows Universal Platform can be found here. [http://ms-iot.github.io/content/en-US/Downloads.htm](http://ms-iot.github.io/content/en-US/Downloads.htm)

### Sitecore - Sitecore.Services.Client

For the communication between the IoT application and Sitecore it makes perfect sense to use Web API. Luckily Sitecore has its own Web API based framework under the Sitecore.Services.Client (SSC) namespace.

With Entity Service within Sitecore.Services.Client (SSC) all the authentication around access the rest end point is handled for us, along with model validation.

The power in Entity Service comes where we can implement our own logic for the controller, we still have access to the full Sitecore API and in this case the xDB APIs.

## Creating the xDB Web API end point with Entity Service (SSC)

The first thing we need for the kiosk application is to create a Entity Service controller and repository to expose and interact with data within Sitecore xDB.

The first stage is to create our model we will expose on the Entity Service controller.Here we add all the properties from xDB we want to expose. This must implement EntityIdentity, this base class contains an ID, we will use this to store the xDB identifier for the user.

Thanks to Jonathan Robbins and his [xDB posts](http://jonathanrobbins.co.uk/tag/xdb/) for working out some of this code.

{% gist komainu85/367ad1958365b0a2900c %}

Next is our repository, here we will write the implementation code for working with xDB and the methods we want to expose over the API. The IRepository gives us the methods of our Entity Service API, so we can write the implementation for each of these methods. XdbFacetRepository is a class I have created for working with xDB, and can be seen below.

{% gist komainu85/95b4ef564984ad9394bd %}

{% gist komainu85/e4bbacd23ee0b70251c3 %}

The actual controller wires up the link between the Entity (Person), and the repository.

{% gist komainu85/6d59c90e379fa1274aec %}

## Windows Universal Platform (Windows 10 IoT Core)

Now we have created the Entity Service API that allows us to post into xDB, the next step is to created a Windows Universal Platform (UWP) that can capture the data and post to our Entity Service.

As I mentioned earlier Windows Universal Platform apps are restricted to Windows 10 IoT and can run on all Windows 10 based platforms.

Below is a screen shot of the kiosk application I created, it captures a users first name, last name and email address. This has been created using the designer within Visual Studio 2015.

![Windows Universal Application - Kiosk Application]({{ site.baseurl }}/assets/kioskapplication.png)

Windows Universal Application - Kiosk Application

To get the form data into Sitecore xDB, we simply need to post to our Entity Service end point. We create a JSON string with our form data, here we are using the user's email address as the unique identifier. The JSON string can then be posted to our Entity Service end point.

The authentication code to access the Entity Service isn't shown here for simplicity. I have written another blog on Sitecore.Services.Client authentication. [Sitecore Services Client]({{ site.baseurl }}{% post_url  2016-01-14-sitecore-services-client-authentication %})

{% gist komainu85/d4f9fc7e91996ab7537d %}

The below video is an example of the finished kiosk posting data into Sitecore's xDB.

<iframe width="560" height="315" src="https://www.youtube.com/embed/zRDuu99RB0Y" frameborder="0" allowfullscreen=""></iframe>

## Up Next

In the next post I will look at creating a custom facet and updating the facet from an IoT application. I will create a VisitedEvent facet and update this facet when a user completes the kiosk form.

## GitHub Source

Full source code is available on GitHub.

[https://github.com/komainu85/WindowsIoTSitecoreSSC](https://github.com/komainu85/WindowsIoTSitecoreSSC)
