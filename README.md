# Google-Assistant-App-Actions--My-Experience
This thread talking about my experience while attempting to integrate google assistance into a flutter E-Health application

## How App Actions work

Since Google Assistant is a purely android thing, to program app actions, you have to use Kotlin/Java for the integration. If you are building a flutter application; you have to use the android folder of the project for the integration

In the res/xml/shortucts.xml file, we declare app capabilities which are a bunch of statements that can communicate with google assistant to enable declaring "intents"

![image](https://user-images.githubusercontent.com/104530599/175784672-a1b6198b-2ef1-4280-a20d-514c3509448a.png)

In the example above, we use a Built In Intent (BII) that google offers called actions.intent.START_EXCERCISE which enables the speech to text recognition model that google uses to easily identify user's speech commands depending on the capabilities that you declare in the shortcuts.xml

Inside a capability, you declare intents.
An intent is a speech that the user says that anticipates specific parameters and keywords to perform certain actions like VIEW (Opens a certain page / fragments) which will be intercepted by a target class that will constantly check if an intent was fired.

### Declaring multiple intents in 1 capability

You can declare multiple intents in 1 capability each with different number of parameters to handle each command differently

![image](https://user-images.githubusercontent.com/104530599/175785298-8c074abf-9500-4c8b-b75b-28b6e7f16986.png)

The example above contains a fallback fulfillment to be called if none of the parameters required where provided

### Declaring shortcuts for capabilities

![image](https://user-images.githubusercontent.com/104530599/175785848-5b0e554b-5d74-472c-8440-9c87cbd5211b.png)

### Declaring inline inventories
These are synonyms of a certain action/noun that maps to the intent's parameters.

Shortcuts are binded to specific capabilities that you declared for your app each has a different meaning and used to give more functionality to each capability. This happens by assigning each intent key to a list of words that you declare inside the values/arrays.xml file.

![image](https://user-images.githubusercontent.com/104530599/175789987-a33931e2-5cdc-49ee-be2a-26c87799ecaa.png)

### What happens after an intent is fired

If you have noticed, each intent we declare inside the shortcuts.xml, we specify a target class for it. So far, we chose the MainActivity kotlin class to handle all incomming intents. When the intent is fired, it calls the main activity constructor method, where we check if there was an intent and call a specific function to handle it

![image](https://user-images.githubusercontent.com/104530599/175790177-0789bb35-c786-49a0-984e-b845eadb799b.png)

Where we create an extension method called handleIntent for the "Intent" class

![image](https://user-images.githubusercontent.com/104530599/175790221-35b7b708-d38e-447e-9c22-9dd2bf155f6e.png)

![image](https://user-images.githubusercontent.com/104530599/175790232-e68daaeb-dab7-4922-8531-9da6cd973fc0.png)

#### But what does this actually mean/do?
As noticed, we define constants like the variable "START_EXCERCISE" which has a value of the intent parameter key to search for in the intent that was just fired. This is to discover which one of the intents we have was actually fired, depending on the parameter key we got.

![image](https://user-images.githubusercontent.com/104530599/175790455-fd06c43c-a00b-4dfa-8fac-625ad6fa2b49.png)

![image](https://user-images.githubusercontent.com/104530599/175790431-4840122d-3eea-4d4a-be47-b70da3af0c6d.png)

![image](https://user-images.githubusercontent.com/104530599/175790440-0b89ee0a-2a03-42e8-9fe3-16cb99f6c036.png)

Depending on the parameters that the user provided in his statement, we perform a set of instructions accordingly, where it is starting a new exercise, adding something in the database, returning an android widget component back to google assistant (more about it later), or even opening a specific page in your application.

Talking about opening specific pages,
In kotlin, we use Android JetPack compose library to control the UI. While in Flutter, you need to extend the FlutterActivity class you import by default from your flutter application which should be pretty straight forward. More about FLutter Activity in https://docs.flutter.dev/development/add-to-app/android/add-flutter-screen?tab=prewarm-engine-kotlin-tab

## Dynamic shortcuts
So far we have been talking about static shortcuts which is the simpler way for creating app actions, while google assistant enables you to create dynamic shortcuts using mainly kotlin as the user uses the application. Unfortunately, google does not provide enough documentation regarding dynamic shortcuts, however you can find more about dynamic shortcuts in this brief steps codelab which is based on a todoapp from the link below https://codelabs.developers.google.com/codelabs/appactions-dynamic-shortcuts#3

##### *ps: i did not go deep into dynamic shortcuts but it should be more complex than static ones, so if you manage to get a full static shortcut app to work, then definitely  search more about dynamic ones*

## Summary

Finally here is a diagram that well explains and summarizes the architecture behind google app actions

![image](https://user-images.githubusercontent.com/104530599/175791016-9a425eda-8f0e-4da8-9b69-a5b53da1c2cc.png)

## Requirements
1. Android development Knowledge & Jet Pack compose
2. A google developers console account
3. Sign into android studio and google play store with the same accoutn as your developers account
4. Install Google assistant plugin into android studio

## Issues Faced

As much as the overall app actions concept was fairly simple, implementing it was a total disaster.

### Codelabs Fail
Google has based its entire google app actions documentation on 2 sample applications they shared on their github both of which don't even work 

```
> git clone --branch codelab-start https://github.com/actions-on-google/appactions-fitness-kotlin.git
> git clone --branch codelab-start https://github.com/actions-on-google/appactions-common-biis-kotlin/tree/codelab-start
```

The entire codelabs documentation and super scarce practical youtube tutorials for google app actions are based on a malfunctioning premade application that has been pushed into github as a base code for the documentation (https://codelabs.developers.google.com/codelabs/appactions#2)

Not only is the code full of errors and it would take ages to debug and fix them, but there are even more internal errors that the IDE cannot not recognize! I am not sure if these errors can be debugged and solved by someone who has alot of experience in android development, but i find it shocking that google would put out such un supervised code. 

After 2 weeks of researching other projects and trying to understand anything and filtering out all the projects out there that use deprecated old google assistant actions.xml (which will no longer be supported by google) or even get any of them to work, I have came to conclusion that google just didn't care about app actions and that's why their documentation is literally incomplete, and the part they wrote is based on a complicated sample app they made that is loaded with errors! Until I checked out their previous PRs on thier repo and found out that they have recently made a completed version of the fitness sample app (https://github.com/actions-on-google/appactions-fitness-kotlin/tree/codelab-complete). it is a shame they didn't mention it in their documentation or even fixed the "start" branch that they use for their documentation.

But it doesn't get any better! even though I followed all the steps needed to run the app, it doesn't fully function! Google assistant was able to recognize that certain phrase to start an excercise and  proceeds to open the app then crashes right away! This was not only an issue i faces, but a lot of people are complaining as well and many have just lost hope :(
That is besides the fact that google assistant doesn't even recognise the speech you say and cannot find the proper intent of the app so it doesn't work! It only works with text inside the google assistant plugin tool / extension

![image](https://user-images.githubusercontent.com/104530599/175791968-ed133617-b749-42c9-8ded-017f8fc15dfb.png)


## Creating my own app

After not finding any functional resources/apps that actually work, I decided to make my own and after a ton of errors and searching, I managed to get  google assistant to recognize my speech but it does not do anything after! I tried writing code to opening the app after the speech, calling an simple api to log requests, and even printing/Log.d()! All in vain. I have spent 3 weeks on this project till this point and tried my best to replicate google's steps but it won't work

I then created a flutter application to test it there and started the process all over again and finally got it to recognize the shortcuts i created but it then says that my application is not installed, which is very confusing becuase app actions are related to the app itself via the application ID on the developers console and not to google assistant. Hence, if it recognizes the  shortcut being called, it should be able to access the application that made this shortcut

## Documentation errors
Alot of the BIIs (Built In Intents) that google provides do not work! google assistant plugin says it does not recognize the parameters i am using, even though I literally used their documentation code / shortcuts. These parameters I was using where for ordering a menu item which was a major feature in my app!

Another point you should beware of is that the documentation is not complete and not well connected. For example, in the code labs they expect you to have searched all other documentations they have and already created shortcuts.xml and other requirements that would be a major hiccup as you follow the code lab tutorial

## Creating play store releases

Another super annoying thing you have to go through is that each time you change a line of code is to creating a signed apk bundles of the app to re release it so that you can test the new version of the code. By the end of the day, you should have made ton of different releases and repeated the same process over and wasted a lot of time!

I don't know why, but after i created alot of signed bundles, android studio refused to create any signed bundles again. I don not know how is that possible, but it no longer generates signed bundles for any of my applications. If you faced the same issue before, let me know how you solved it, but i was so fed up that i didn't bother to reinstall android studio because signed bundles are most probably signed by google and it may put a limit to the number of signed apks you create a day or blocks you if you spam create a lot of them. However the unsigned apks can be generated without any problems, but you cannot use them to make releases on the developers console.


# Reflection and Advice

It may have been easier if I already had anroid development experience and It may have helped me debug some of the errors that popped up after you replicated google's tutorial. I am going to get back to this project in the future with a fresh mind and more knowledge on android development in general and I hope google takes action and improve their documentation and update all the outdated videos they put out.

Even though many people have complained from their application, I believe it is indeed possible to get this to work, but you have to be super patient and keep researching more into this huge documentation, maybe you find the solution.

If you are going to take on this challenge to integrate google assistant app actions into your android app, here are a few links that may help

```
Overview
> https://developer.android.com/guide/app-actions/overview

Creating shortcuts.xml
> https://developer.android.com/guide/app-actions/action-schema#static

Google assistant plugin
> https://developer.android.com/guide/app-actions/test-tool

BIIs
> https://developer.android.com/reference/app-actions/built-in-intents/health-and-fitness/start-exercise

BIIs properties documentation (menu item as example)
> https://schema.googleapis.com/MenuItem

Google app actions youtube codelab tutorial
> https://www.youtube.com/watch?v=usEBXnquAjY&ab_channel=TECHWITHSACH

Codelab tutorial
> https://codelabs.developers.google.com/codelabs/appactions#0

Codelab dynamic shortcut tutorial
> https://codelabs.developers.google.com/codelabs/appactions-dynamic-shortcuts#0

Control Flutter UI through Kotlin
>https://docs.flutter.dev/development/add-to-app/android/add-flutter-screen?tab=prewarm-engine-kotlin-tab

Android Widgets
> https://developer.android.com/guide/app-actions/widgets

Google app actions github repos
> https://github.com/actions-on-google

Google play console ($25)
> https://play.google.com/console
```

I hope you have benefited from this summary, if you come to have made any app with google assistant app actions, please text me and share it with us!
