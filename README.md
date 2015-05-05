##Conference Endpoints Project
This is the fourth final project for the Udacity Full Stack NanoDegree.

## Setup Instructions
1. Update the value of `application` in `app.yaml` to the app ID you
   have registered in the App Engine admin console and would like to use to host
   your instance of this sample.
1. Update the values at the top of `settings.py` to
   reflect the respective client IDs you have registered in the
   [Developer Console][1].
1. Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
1. (Optional) Mark the configuration files as unchanged as follows:
   `$ git update-index --assume-unchanged app.yaml settings.py static/js/app.js`
1. Run the app with the devserver using `dev_appserver.py DIR`, and ensure it's running by visiting your local server's address (by default [localhost:8080][2].)
1. Deploy your application.

#### Products
- [App Engine][3]

#### Language
- [Python][4]

#### APIs
- [Google Cloud Endpoints][5]

### Implementation Explanation:
- Added Session model
	- Session is a child of conference - all sessions in a conferenced can be easily queried with ancestor query.

- Added SessionForm as well as SessionForms for returning multiple sessions.

**Choices for Session model:**

1. ***name*** is required
1. ***speaker*** is required (must have a speaker for each session and will be queried on later)
	- Could have used a full fledged entity (new model/forms) for speakers, 
	- Left speaker as string for proof of concept.
1. ***duration*** - duration of the session. Opted to go for time in minutes (this will be query-able later on)
1. ***typeOfSession*** - I wasn't exactly sure how to implement this... I went with an enum class, much like the TeeShirtSize in profile.
	- Can only specify certain types
	- Can call the class as a list with to_to check if values valid
1. ***date*** - Stored in the DB as a date object, presented as string
1. ***startTime*** - Stored in the DB as time object, presented as string (will be queried on later)
1. ***websafeKey*** - not stored in db, but computed with `urlsafe` in SessionForm.

### TASK 3 Questions:
Additional queries:
- getSessionsByType - query all sessions accross all conferences by type
	
    `sessions = Session.query(Session.typeOfSession == 'WORKSHOP')`
- getConferencesByCategory - query for all conferences with a specific category
	
    `sessions = Session.query(Session.category == 'Programming Languages')`

Query Problem:

- Only one inequality filter is supported with google datastore.

Possible Fixes:
- Can query by typeOfSession equal to everything but WORKSHOP ex:

      Session.query(
				ndb.AND(
					ndb.OR(Session.typeOfSession == 'NOT_SPECIFIED',
						Session.typeOfSession == 'KEYNOTE',
						Session.typeOfSession == 'FREEFORM',
						Session.typeOfSession == 'LECTURE'
						), Session.startTime < datetime.strptime('19:00', '%H:%M').time())
				)

- Could programatically exclude sessions with type of 'WORKSHOP'

[1]: https://console.developers.google.com/
[2]: https://localhost:8080/
[3]: https://developers.google.com/appengine
[4]: http://python.org
[5]: https://developers.google.com/appengine/docs/python/endpoints/