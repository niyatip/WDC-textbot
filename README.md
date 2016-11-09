# Text Bot [![Build Status](https://travis-ci.org/watson-developer-cloud/text-bot.svg?branch=master)](https://travis-ci.org/watson-developer-cloud/text-bot)

<p align="center">
  <img src="http://g.recordit.co/mfjBM2ZJEn.gif" alt="demo" />
</p>

# Building Text Message Chat Bot using Watson Application Starter Kit 
# Type: Hands-on Lab

## Overview:

IBM Watson it is a cognitive technology platform that uses Natural Language Processing, Vision, Machine and Deep Learning to reveal insights from large amounts of unstructured data.  Attendees, of all skill levels, will learn how to build a Bot application powered by Watson, gaining experience using cognitive and deep learning technologies.

In this lab, attendees will start building their application using a Watson Application Starter Kit called Text Message Bot to jumpstart the development of the Bot. For more information on this, refer to URL [Text Message Bot]()

##Prerequisite:
- [Sign up for BlueMix](http://www.ibm.com/cloud-computing/bluemix/)
- [Install GIT](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [Install CLOUD FOUNDRY] (https://github.com/cloudfoundry/cli)

##Introduction:
IBM Watson offers a broad set of services to enable developers to build cognitive solutions. Cognitive Computing Systems are defined as systems that __understand__, __reason__, and __learn__ to assist humans in making better decisions by penetrating the complexity of Big Data. 

This application uses Watson Conversation and the AlchemyLanguage APIs. To help with the task of understanding natural language, Alchemy Language services are designed to extract keywords, entities, relations, sentiment, and concepts expressed in text. In this lab, we will step through the process of building a [Node.js](https://nodejs.org/en/) app that uses Alchemy Language to extract entities and intents from the Conversation API results that are then transmitted to the Weather API to extract weather forecast data for a city in the US.

## Bluemix App
To leverage any of the Watson Developer Cloud services (including Alchemy Language), you need to create a service instance in Bluemix, which, upon success, returns credentials that you can use in accessing the service.

To start, we will build a simple app using node.js runtime and host it on Bluemix, IBM’s platform-as-a-service solution. You should have signed up for Bluemix account already. If not, go to [Bluemix](http://bluemix.net) and sign up for a Bluemix account.

## Installation

This is a Starter Kit (SK), which is designed to get you up and running quickly with a common industry pattern, and to provide information and best practices around Watson services and bots. This application demonstrates how the Conversation service can be used with different bot kits such as those from Facebook and Twilio to enable users to have an intuitive and natural conversation with the bots. This use case for this starter kit is around discussing the weather. The starter kit uses the WeatherInsights service to gather weather data and provide forecasts.

A running instance of the application in this Starter Kit is available as a [demo](https://text-bot.mybluemix.net). The demo does not include Facebook or Twilio integration. Running the application using the Facebook and Twilio bots requires credentials to access their services. For information about obtaining credentials and integrating Twilio and Facebook bots, see [Installing the bots](#bot-installation).

## Getting Started

This application is written in [Node.js](http://nodejs.org/) and uses the [npm](https://www.npmjs.com/), the Node Package Manager, command to install a software development kit (SDK) for the Watson Developer Cloud services, as well as to satisfy other dependencies. The following instructions include Instructions for downloading and installing these.

1. Log into GitHub and clone [the repository for this application](https://github.com/niyatip/WDC-textbot). Change to the folder that contains your clone of this repository.

2. [Create a Bluemix Account](sign_up) if you do not already have one, or use an existing account.

3. If it is not already installed on your system, download and install the [Cloud-foundry command-line interface (CLI)][cloud_foundry] tool.

4. If it is not already installed on your system, install [Node.js](http://nodejs.org/). Installing Node.js will also install the `npm` command. Make sure to use node version 4.2.1 or later, as specified in `package.json`, or you may run into problems when installing other mandatory Node.js packages.

5. Open a terminal window

6. mkdir $WORKDIR

7. cd $WORKDIR

8. git clone https://github.com/watson-developer-cloud/WDC-textbot.git
 
9. cd to your WDC-textbot directory.

10. <a name="edityml">Edit the `manifest.yml` file</a> in the folder that contains your clone of the repository and replace `application-name` with a unique name for your copy of the application. The name that you specify determines the application's URL, such as `application-name.mybluemix.net`. The relevant portion of the `manifest.yml` file looks like the following:

        declared-services:
        conversation-service:
          label: conversation
          plan: free
        weatherinsights-service:
          label: weatherinsights
          plan: Free-v2
        cloudantNoSQLDB-service:
          label: cloudantNoSQLDB
          plan: Lite
        applications:
          - services:
            - conversation-service
            - weatherinsights-service
            - cloudantNoSQLDB-service
          name: weather-conversation-bot
          command: npm start
          path: .
          memory: 512M

6. Connect to Bluemix by running the following commands in a terminal window:

     ```bash
     cf api https://api.ng.bluemix.net
     cf login -u <your-Bluemix-ID> -p <your-Bluemix-password>
     ```

7. Create instances of the services that are used by the application. If you already have instances of the Alchemy Language service, you can use that instances and its API key:

     - Create an instance of the [Conversation](http://www.ibm.com/watson/developercloud/doc/conversation/) service by running the following command:

        cf create-service conversation free conversation-service

    - If you do not already have one, create an instance of the [Alchemy Language](http://www.ibm.com/watson/developercloud/alchemy-language.html) service by running the following command:

        cf create-service alchemy_api free alchemy-language-service

        **IMPORTANT:** This application performs best when you use an Alchemy API key that supports high transaction limits. The free Alchemy API key has a limit of 1000 transactions per day.  You can upgrade to the Standard or Advanced Plan of the Alchemy API service to obtain a key that supports more than 1000 transactions per day. To upgrade to the Standard or Advanced plans, you will need to add a credit card to your Bluemix account. For plan and pricing information, click [here](https://console.ng.bluemix.net/catalog/services/alchemyapi/).

    - Create an instance of the [Weather Insights service](https://console.ng.bluemix.net/docs/services/InsightsWeather/index.html) by running the following command:

        cf create-service weatherinsights Free-v2 weatherinsights-service

    - Create an instance of the [Cloudant NoSQL database](https://cloudant.com/) service by running the following command:

        cf create-service cloudantNoSQLDB Lite cloudantNoSQLDB-service

8. Create and retrieve service keys to access the Conversation service:

        cf create-service-key conversation-service myKey
        cf service-key conversation-service myKey

9. Create and retrieve service keys to access the WeatherInsights service:

        cf create-service-key weatherinsights-service myKey
        cf service-key weatherinsights-service myKey

    **IMPORTANT:** The Free plan for the WeatherInsights service provides a username and password as part of its service key. To take advantage of the full capabilities of the application in this starter kit, you should use an instance of the WeatherInsights service that supports high transaction limits. To be able to reach those limits, you will need to upgrade to the service's Standard or Premium plans because the pricing model for this service is based on the maximum number of API calls that you can make to the service per minute. The Premium plan is ideal. If you upgrade to the Standard or Premium plans, you will receive and API key rather than username and password credentials. To upgrade to the Standard or Premium plans, you will need to add a credit card to your Bluemix account. For plan and pricing information, click [here](https://console.ng.bluemix.net/catalog/services/weather-company-data/).

10. Create and retrieve service keys for the Alchemy Language service:

        cf create-service-key alchemy-language-service myKey
        cf service-key alchemy-language-service myKey

11. Create and retrieve service keys for the Cloudant service:

        cf create-service-key cloudantNoSQLDB-service myKey
        cf service-key cloudantNoSQLDB-service myKey

12. A file named `.env` file is used to provide the service keys for your service instances to the application. Create a `.env` file in the root directory of your clone of the project repository by copying the sample `.env.example` file using the following command:

        cp .env.example .env

    You will update the `.env` with the information you retrieved in steps 8 - 11.

    The `.env` file will look something like the following:

      USE_WEBUI=true
      ALCHEMY_API_KEY=

      #CONVERSATION
      CONVERSATION_URL=https://gateway.watsonplatform.net/conversation/api
      CONVERSATION_USERNAME=
      CONVERSATION_PASSWORD=
      WORKSPACE_ID=

      #WEATHER
      WEATHER_URL=https://twcservice.mybluemix.net/api/weather
      WEATHER_USERNAME=
      WEATHER_PASSWORD=


      #CLOUDANT
      CLOUDANT_URL=

      #FACEBOOK
      USE_FACEBOOK=false
      FACEBOOK_ACCESS_TOKEN=
      FACEBOOK_VERIFY_TOKEN=

      #TWILIO
      USE_TWILIO=false
      USE_TWILIO_SMS=false
      TWILIO_ACCOUNT_SID=
      TWILIO_AUTH_TOKEN=
      TWILIO_API_KEY=
      TWILIO_API_SECRET=
      TWILIO_IPM_SERVICE_SID=
      TWILIO_NUMBER=


13. For Cloudant, use the Cloudant DB console to create a new database called **"botdb"**. 

    1.  Login to [Bluemix](https://console.ng.bluemix.net/)
    
    2.  Login using your Bluemix credentials by selecting the “Log In” button in the top right
    
    3.  After logging in, you will land on a page displaying your current application and service instances. Scroll down to the “All Services” section and find the line for “cloudantNoSQLDB-service”
    
    4.  Click on the “cloudantNoSQLDB-service” line to open the service details page
    
    5.  Once on the service details page, click the green “Launch” button. This will take you to the Cloudant web UI to manage your databases.
    
    6.  Once the web UI loads, click “Databases” on the left hand nav bar
    
    7.  On the “Databases” page, click the “Create Database” button in the top right corner
    
    8.  Name the database “botdb” and select “Create”

14. For Weather Lookup, replace the Weather URL value in your .env with https://twcservice.mybluemix.net/api/weather 

15. The Conversation service must be trained before you can successfully use this application. The training data is provided in the file `resources/conversation-training-data.json` in your checkout of the repository. To train the model used by the Conversation service for this SK, do the following:

    1. Login to Bluemix

    2. Navigate to upper left hand side and click on the 3 parallel lines and select **Dashboard** from the left hand navigation panel.

    3. Scroll down and under "All Services" - select the instance of the Conversation service that you are using

    4. Once on the Service details page, scroll down (if necessary) and click green **Launch tool** button on the right hand side of the page. (You may be asked to log in again. or you may see a blank screen - give it a few minutes and refresh the screen). This will launch the tooling for the Conversation service, which allows you to build dialog flows and train your chatbot. This should take you to your workspace in the Conversation service which represents a unique set of chat flows and training examples. This allows you to have multiple chatbots within a single instance of the Conversation service. 

    5. Once on the page, you will see the option to either “Create” a new workspace, or “import” an existing one. We are going to “import” a premade chatbot for this example, so select “Import".
    
    6. Click **Choose a file**, navigate to the `resources` directory of your clone of the repository for this project, and select the file `conversation-training-data.json`.  Once the file is selected, ensure that the “Everything (Intents, Entities, and Dialog” option is selected.
    
    7. Click **Import** to upload the `.json` file, create a workspace, and train the model used by the Conversation service.

    To find your workspace ID once training has completed, click the three vertical dots in the upper right-hand corner of the Workspace pane, and select **View details**.  Once the upload is complete, you will see a new workspace called “Weather Bot ASK”. In order to connect this workspace to our application, we will need to include the Workspace ID in our environment variables file “.env”.  

    Go back into your “.env” file, and paste the workspace ID next to the “WORKSPACE_ID=” entry.

##Deploying your Bot to Bluemix    

14. Using command line or terminal, push the updated application live by running the following command from your working directory:Push the updated application live by running the following command:

    cf login https://api.ng.bluemix.net

    cf push

15. Make sure the cf push command completes successfully using your Bluemix login.  You should go back into your Conversation Service in Blue mix to make sure the training is done. Do the following:

  a.  Login to Bluemix

  b.  Select Dashboard as you did earlier

  c.  Select the instance of the Conversation service that you are using

  d.  Scroll down (if necessary) on the “manage” tab and click Launch tool (you may be asked to log in again or refresh screen)
  
  e.  Select the name of your app

16. On the next window – you will see a graphic with three dots on the far upper-right-hand side – if you click that you will see if it is done training or not. Once that is complete you can actually see content when you run your applications (assuming you have pushed the application to Bluemix)

17. The final message should indicate if the application is deployed successfully and it is started without errors.

## Testing your bots

After completing the steps above, you are ready to test your application. Start a browser and enter the URL of your application.

                  <your application name>.mybluemix.net

You can also find your application name when you click on your application in Bluemix.

Begin entering questions such as “what is the weather for Austin, Texas?” If you don’t enter a state, it will ask you to clarify what state.

Note about the Weather Conversation Pattern:  this Starter Kit uses weather information from the WeatherInsights service to demonstrate how to integrate a bot with the Conversation service available on Bluemix. It provides extension points for integration botkits from vendors such as Facebook and Twilio.
For a given input (e.g. question about weather **for** Austin, TX), a trained Conversation service instance responds with weather forecast. The service is composed of a model trained with a set of intents and entities (in this case, the intent is to get the forecast and the entities are cities that the user might want to know the forecast for). The dialog agent of the Conversation service is responsible for interacting with the user and keeping track of the context of the conversation so that it can provide answers to follow up questions about the same topic.


## 1. Installing the Bots to work with Facebook Messenger or Twilio

1. Edit the `.env` file to add credentials for Facebook and Twilio. See the following links for information about where you can get the credentials required by the botkit for each service:

    * [Facebook](https://github.com/howdyai/botkit/blob/master/readme-facebook.md#getting-started)
    * [Twilio](https://github.com/howdyai/botkit/blob/master/readme-twilioipm.md#getting-started)

2. If you are integrating with Twilio, set the `USE_TWILIO` and `USE_TWILIO_SMS` variables in your `.env` file to `true`. If you are integrating with Facebook, set the `USE_FACEBOOK` variable in your `.env` file to `true`.

3. Modify the file `lib/bot/bot.js` to include your own bot handling code. If you would like to use a separate bot messaging service (such as `wit.ai`, `converse.ai`, and so on ), you can add the middleware to each bot instance that you'd like for that service to use, and configure it with the single `bot.js` file.

## About the Weather Conversation pattern

This Starter Kit uses weather information from the WeatherInsights service to demonstrate how to integrate a bot with the Conversation service available on Bluemix. It provides extension points for integration botkits from vendors such as Facebook and Twilio.

For a given input *(e.g. question about weather in Austin, TX)* , a trained Conversation service instance responds with weather forecast. The service is composed of a model trained with a set of intents and entities (in this case, the intent is to get the forecast and the entities are cities that the user might want to know the forecast for). The dialog agent of the Conversation service is responsible for interacting with the user and keeping track of the context of the conversation so that it can provide answers to follow up questions about the same topic.
