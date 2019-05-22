# outbound-dialing-backend

This project is a demo backend service that compliments the front end outbound dialpad solution [here](https://github.com/jhunter-twilio/plugin-flex-outbound-dialpad)

It exposes a websocket that can be used to:
  - trigger a call from a client
  - push updates of the call status back to that client
  - hang up a call for a given client
  
 It also exposes two end points that are called by twilio when:
   - providing updates to the status of calls generated by this server
   - twilio retrieves a twiml document to describe how to handle a call when it is answered

# Dependencies

Before setting up this server you must first created a dedicated TaskRouter workflow for outbound calls.  You can do this [here](https://www.twilio.com/console/taskrouter/dashboard). Make sure it is part of your **Flex Task Assignment** workspace.

- ensure there is the following matching workers expression for the only filter on the workspace
  - task.targetWorker==worker.contact_uri
- ensure the priorty of the filter is set to 1000 (or at least the highest in the system)
- make sure the filter matches to a queue with Everyone on it. The default Everyone queue will work but if you want to seperate real time reporting for outbound calls, you should make a dedicated queue for it with a queue expression
  - 1==1

![alt text](https://raw.githubusercontent.com/jhunter-twilio/outbound-dialing-backend/master/screenshots/workflow-config.png)

# Setup

You can now setup the server, you can either deploy to heroku (which is free, you just need a login) or you can setup locally and expose via ngrok

# Deploying to heroku

1. Use this link to begin [![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/jhunter-twilio/outbound-dialing-backend/tree/master)

2. Populate the given variables when prompted
    - TWILIO_OUTBOUND_WORKFLOW_SID - the SID of the workflow you just created - used for creating tasks
    - TWILIO_ACCOUNT_SID - the account sid of your twilio account - used for calling Twilio APIs
    - TWILIO_AUTH_TOKEN - the auth token of your twilio account - used for calling Twilio APIs
    - EXTERNAL_HOST - the host that exposes this service - used for telling Twilio where to make callbacks when calling the Twilio APIs.  Should be of the form https://<HEROKU_APP_NAME>.herokuapp.com
    
3. You're all set, the backend is ready.  You can access it on https://<HEROKU_APP_NAME>.herokuapp.com and you can create a websocket on wss://<HEROKU_APP_NAME>.herokuapp.com/websocket 
  - https://www.websocket.org/echo.html is an excellent web page for testing the websocket is exposed correctly.
  
# Deploying locally

1. Clone repository using git clone 
2. run ```npm install```
3. run ```ngrok http 3000```
3. start server using ```TWILIO_OUTBOUND_WORKFLOW_SID=<WORKFLOWSID> TWILIO_ACCOUNT_SID=<ACCOUNTSID> TWILIO_AUTH_TOKEN=<AUTHTOKEN> EXTERNAL_HOST=https://<NGROK-ENDPOINT> DEBUG=outbound-dialing-backend:* npm start```

# change log

v1.0 - initial release

# TODOs

1. change websocket endpoint to something more descriptive for outbound calls
2. consolidate backend server with other plugin solutions that require event push to client from backend.  (e.g. real time dashboard)
