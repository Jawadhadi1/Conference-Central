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
In order to deploy this API server locally, first download the Google App Engine SDK for Python and then install it. 

1.	Clone this repository or download it as a zip file.

2.	Open the Google App Engine Launcher.
  *	Open the File menu and then click on Add Existing Application.
  * Browse to the parent directory that contains all the application files which you just downloaded. 
  * Click on the Run button to run the application locally and then navigate to your local server’s address (by default localhost:8080.)
  * To deploy the application on the Google Cloud Platform, click on the Deploy button in the Google App Engine Launcher, and navigate to Conference Central Application.
  
3.	To test the functionality of the different API endpoint methods in the application, go to Google API explorer.
