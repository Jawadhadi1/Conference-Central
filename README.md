# **Conference Central Application**

### **About**
This project is a google cloud-based API server to support a web-based application that can be used for organizing conferences. The key functionalities supported by the API are as follows:

1. User Authentication (through Google Accounts)

2. User Profiles

3. Creating, Storing and Retrieving information related to Conferences

4. Creating, Storing and Retrieving information related to the Sessions within a Conference

5. Updating and data retrieval from User Wishlists.  


### **Language**
Python

### **APIs**
Google Cloud Endpoints

# **Requirements**

### **Project Motivation and Overview**

Currently the Conference Central application is pretty limited - conferences have just name, description and date when the conference happens. But usually conferences have more than that - there are different sessions, with different speakers, maybe some of them happening in parallel! Your task in this project is to add this functionality. Some of the functionality will have well defined requirements, some of it will more open ended and will require for you think about the best way to design it.
You do not have to do any work on the frontend part of the app to successfully finish this project. All your added functionality will be testable via APIs Explorer.
You will have to submit your app ID, and text for the parts of the project that require explanation.

### **Task 1: Add Sessions to a Conference**

**Overview**

Sessions can have speakers, start time, duration, type of session (workshop, lecture etc…), location. You will need to define the Session class and the SessionForm class, as well as appropriate Endpoints.
You are free to choose how you want to define speakers, eg just as a string or as a full fledged entity.

**Define the following Endpoints methods**

*	*getConferenceSessions(websafeConferenceKey)* -- Given a conference, return all sessions
*	*getConferenceSessionsByType(websafeConferenceKey, typeOfSession)* -- Given a conference, return all sessions of a specified type (eg lecture, keynote, workshop)
*	*getSessionsBySpeaker(speaker)* -- Given a speaker, return all sessions given by this particular speaker, across all conferences
*	*createSession(SessionForm, websafeConferenceKey)* -- open only to the organizer of the conference

**Define Session class and SessionForm**

In the SessionForm pass in:

*	Session name
*	highlights
*	speaker
*	duration
*	typeOfSession
*	date
*	start time (in 24 hour notation so it can be ordered).

Ideally, create the session as a child of the conference.

**Explain your design choices**

Explain in a couple of paragraphs your design choices for session and speaker implementation.

### **Task 2: Add Sessions to User Wishlist**

**Overview**

Users should be able to mark some sessions that they are interested in and retrieve their own current wishlist. You are free to design the way this wishlist is stored.

**Define the following Endpoints methods**

*	*addSessionToWishlist(SessionKey)* -- adds the session to the user's list of sessions they are interested in attending

You can decide if they can only add conference they have registered to attend or if the wishlist is open to all conferences.

*	*getSessionsInWishlist()* -- query for all the sessions in a conference that the user is interested in

### **Task 3: Work on indexes and queries**

**Create indexes**

Make sure the indexes support the type of queries required by the new Endpoints methods.

**Come up with 2 additional queries**

Think about other types of queries that would be useful for this application. Describe the purpose of 2 new queries and write the code that would perform them.

**Solve the following query related problem**

Let’s say that you don't like workshops and you don't like sessions after 7 pm. How would you handle a query for all non-workshop sessions before 7 pm? What is the problem for implementing this query? What ways to solve it did you think of?

### **Task 4: Add a Task**

**Overview**

When a new session is added to a conference, check the speaker. If there is more than one session by this speaker at this conference, also add a new Memcache entry that features the speaker and session names. You can choose the Memcache key.

**Define the following Endpoints method**

*	*getFeaturedSpeaker()*




### **Tasks/Functions/Requirements Implemented**
**Task 1: Add Sessions to a Conference**

I successfully implemented all the required endpoint methods into the Conference Central program.
In the session model I added the *organizerUserId* property from the Conference model class, in addition to the properties already mentioned in the requirements document. 
In order to facilitate one conference to many sessions mapping, ancestor-child relationship was implemented between these two types of classes. This would allow the program to query sessions using their ancestor (conference). 
In order to speed up the process of session queries, sessions were Memcached. 

**Task 2: Add Sessions to User Wishlist**

I added a property in the Profile model class by the name *sessionsToAttend*. This property is then used as the user’s wishlist to store the sessions he/she is interested in attending. 

**Task 3: Work on Indexes and Queries**

The two new endpoint methods (queries) that I added are:

*getConferencesWithMissingInfo*: This method would return all the existing conferences which have some important missing information.

*getSessionsWithMissingInfo*: This method would return all the existing sessions of a conference which have some important missing information.

Using the above functions the creator of a conference/session can get notified to update any missing critical information. 

To implement the last query required in this task, I created the endpoint method *getBefore7pmNonWorkshopSessions*. 

**Problem**: In order to successfully return the desired results (sessions that take place before 7 pm and are non-workshop type) I would need to apply more than one inequality filter in the query, which is not allowed in Google Datastore. 

**Solution**: First I performed an ndb query that returned all the sessions happening before 7 pm, then by using a loop (for), I filtered out all the non-workshop type sessions from the resulting list. 

**Task 4: Add a Task**

I added some new code in the *createSession* endpoint method (which was created during Task 1) to check if this speaker is also featuring in any other sessions of the conference.  If this is true then the speaker and session names are added to the memcache with the key ‘featured_speaker’.

The endpoint method *getFeaturedSpeaker* retrieves the speaker data which was stored earlier in the memcache, and then returns it. 


### **Setup Instructions**
In order to deploy this API server locally, first download the [Google App Engine SDK for Python](https://cloud.google.com/appengine/downloads) and then install it. 

1.	Clone this repository or download it as a zip file.

2.	Open the Google App Engine Launcher.
  *	Open the File menu and then click on Add Existing Application.
  * Browse to the parent directory that contains all the application files which you just downloaded. 
  * Click on the Run button to run the application locally and then navigate to your local server’s address (by default localhost:8080.)
  * To deploy the application on the Google Cloud Platform, click on the Deploy button in the Google App Engine Launcher, and navigate to [Conference Central Application](https://flash-bloom-101317.appspot.com/#/).
  
3.	To test the functionality of the different API endpoint methods in the application, go to [Google API explorer](https://apis-explorer.appspot.com/apis-explorer/?base=https://flash-bloom-101317.appspot.com/_ah/api).
