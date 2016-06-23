As previously stated, fiware-android relies upon a server side application, that is responsible of doing part of app job.


## What does gae-fiware do?

Gae fiware has various endpoints that let app lighten its job:

* an OpenWeatherMap endpoint, to retrieve weather conditions
* a calendars endpoint that will retrieve user calendar's events
* an "user notify" endpoint that is Proton's events recipient, and that will deliver Proton's events to the right device
* an "user online" endpoint that will tell gae-fiware that device received the notification, and thus it is online.

It is responsible for pushing data to Orion too. It indeed does much more, as explained later.

Calendars and Weather endpoints will be ran on a cron basis.


## How does fiware-android communicate with gae-fiware?

It is indeed quite complex.  
As soon as app starts, an entity "User" is created/updated on gae-fiware.  
This entity holds lots of information about current user:
* its device_id that is our unique key to retrieve this user
* a gcm token, to send notifications to device
* home location, as coordinates
* current user location, as coordinates
* enabled rules
* temperature delta, needed to let user set up his/her Weather Rule preferences
* cb entities, that will be wiped out when user requests a "delete data" from app
* user timezone, needed to know when "in an hour" or "tomorrow" calendar events should start
* online boolean, that tells server if user is currently online

### Online boolean:
Every time this entity is updated/created, "online" boolean is set to True.  
When a notification is delivered to a device, gae-fiware will set user's online status to "False".  
Then the device has to make a call to "online" endpoint, as previously stated, to be marked as "online" again.  
If user is not online, no checks are performed for him/her.  

### Home location and current location:
First time user enables Weather or CalendarLocation rule, he will be asked for his/her home location, that will be stored on gae-fiware.  
If CalendarLocation is enabled, current user location will be sent from device to gae-fiware too.

### Enabled rules:
Server needs to know which checks it has to perform for current user, so he needs to know which rules are enabled.

### Calendar:
Moreover, a "Calendar" entity is present too, that holds information about a gcalendar account.  
It is linked to its user through ndb user.key.

Every calendar has an id that is string name of the calendar (eg: xyz@gmail.com).  
It has a list of enabled rules too, because we need to know for every calendar which rules it is associated to, to know which checks to perform on it.  
Finally, every Calendar has its access and refresh tokens.  
As we're using "CrossClientAuth", gae-fiware has to refresh access token, that is the token needed to access a calendar, and refresh token is needed for that purpose.  
Access token lasts about 1h.  
From device, we only send an auth token first time an account is enabled, then server will retrieve refresh and access tokens, storing them.  
While refresh token should never expire, an user can indeed revoke it.  
In that case, a message is sent to the device, requiring a new auth token.

## gae-fiware's flow
![Architecture](https://github.com/atooma/fiware-android-wiki/blob/master/assets/gae-fiware-diagram.png)
