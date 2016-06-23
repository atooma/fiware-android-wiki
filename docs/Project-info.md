## Project dependencies

Current Gradle configuration:  

    compile 'com.android.support:support-annotations:23.2.0'
    compile 'com.android.support:support-v13:23.2.0'
    compile 'com.android.support:appcompat-v7:23.2.0'
    compile 'com.android.support:cardview-v7:23.2.0'
    compile 'com.android.support:recyclerview-v7:23.2.0'
    compile 'com.android.support:design:23.2.0'
    compile 'com.google.android.gms:play-services-location:8.3.0'
    compile 'com.jakewharton:butterknife:7.0.1'
    compile 'com.google.android.gms:play-services-base:8.3.0'
    compile "com.google.android.gms:play-services-gcm:8.3.0"
    compile 'com.google.code.gson:gson:2.5'
    compile 'com.squareup.okio:okio:1.6.0'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.google.dagger:dagger:2.0.2'

## Project architecture

Main app activity has control switches to enable/disable app functions.  
Every time a function is enabled, a subscription is made on Orion to notify Proton. A google app engine application, on atooma server, will provide needed informations updating subscribed values on Orion.  
Orion will then send a notification to Proton, that will evaluate our rules. If any rule is met, Proton will send an output json event back to google app engine application, that will finally send the same json received to our app.  
Android app will then check json content and push a notification to user.  
At app starting, current device is registered on google app engine server as {device_id, gcm_token}, where device_id is map key. Every entities then will carry its device_id that will be used as key to extract gcm_token to push final json to android app.  

For google calendar related rules, an authorization dialog is displayed.  
For external temperature at home/event location outside, first time one of them is enabled, a google maps activity will appear asking for user home location.  
Home location will then be stored and never asked again.  


![Architecture](assets/ArchitectureDiagram.png)


## Known bugs

As app is still in beta, there are some known bugs. Here listed by decreasing severity:
* If user has > 1 google accounts, selecting two different accounts for each calendar rule will make last enabled rule overwrite other calendar (ie: we will only catch calendar events from a single calendar account for now)
* We must provide a way to delete data bounded to a rule (eg: delete user home location, if user wants to update it). As of now the user is forced to go to android settings and delete application data.
* We do not have any settings for now.
* CalendarTomorrowRule must be splitted in two different Rules.
* Room/Weather temperature CEP rule has to be smarter (now it is just a rough, test driven implementation).
