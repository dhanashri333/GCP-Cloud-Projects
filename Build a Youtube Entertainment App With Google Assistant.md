# Build a Youtube Entertainment App With Google Assistant

# ***Overview***

*[Google Assistant](https://assistant.google.com/#?modal_active=none) is a personal voice assistant that offers a host of actions and integrations. From making appointments and setting reminders, to ordering coffee and playing music, the 1 million+ actions available suit a wide range of voice command tasks. Google Assistant is offered on Android and iOS, but it can even be integrated with other devices like smartwatches, Google Homes, and Android TVs.*

![https://miro.medium.com/max/1400/1*dPpvrNz7H08IozctGjlYsw.png](https://miro.medium.com/max/1400/1*dPpvrNz7H08IozctGjlYsw.png)

[Actions](https://developers.google.com/actions/extending-the-assistant) are the central platform for developing Google Assistant applications. The Actions platform integrates with human-computer interaction suites, which simplifies conversational app development. The most widely used suite is [Dialogflow](https://dialogflow.com/), which uses an underlying machine learning (ML) and natural language understanding (NLU) schema to build rich Assistant applications. The Actions platform also integrates with [Cloud Functions](https://cloud.google.com/functions/), which lets you run backend fulfillment code in response to events triggered by Dialogflow requests.

# **Objectives**

- Build an Assistant application that consists of an Actions project, a Dialogflow agent with custom intents and entities, a webhook, and a Cloud Function to handle fulfillment.
- Generate the proper authentication credentials and install necessary dependencies to use the Youtube Data API.
- Add fulfillment logic to the Cloud Function to handle Youtube Data API calls.
- Deploy your application and test it with the Actions Simulator.

# **Prerequisites**

- Google Cloud Account
- Pair of headphones or turning the volume up on your computer is recommended.

# **Let’s Begin**

# **Create an Actions project**

Regardless of the Assistant application, you’re building, you will always have to create an Actions project so your app has an underlying organizational unit.

Open the [Actions on Google Developer Console](http://console.actions.google.com/) in a new tab. You should be looking at a clean Actions console that resembles the following (If you are a new user (:

![https://miro.medium.com/max/1400/1*b8AZokwlt0lFrbRNYsDeog.png](https://miro.medium.com/max/1400/1*b8AZokwlt0lFrbRNYsDeog.png)

Click **Add/import project** and agree to Actions on Google’s terms of service when prompted.

Click into the Project Name field and Add the project name **Youtube Entertainment**

Once the project is created you will see a welcome screen like below

![https://miro.medium.com/max/1400/1*fKW30EQKA-QCvURHPtWWHg.png](https://miro.medium.com/max/1400/1*fKW30EQKA-QCvURHPtWWHg.png)

Now click **Actions on Google** in the top left corner to return to the homepage. Then click on the project you just created

# **Build an Action**

An [action](https://developers.google.com/actions/glossary#A) is an interaction you build for the Google Assistant. An action supports a specific [intent](https://developers.google.com/actions/glossary#intent) (a goal or task that users want to accomplish), which is carried out by a corresponding [fulfillment](https://developers.google.com/actions/glossary#fulfillment) (logic that handles an intent and carries out the corresponding Action.)

From the left-hand menu click **Build** > **Actions** > **ADD YOUR FIRST ACTION**. Then select **Custom Intent** > **BUILD**

[https://miro.medium.com/max/1164/0*eLFRIxojm5RWs0xp](https://miro.medium.com/max/1164/0*eLFRIxojm5RWs0xp)

This will take you to the Dialogflow console. click **Allow** when Dialogflow prompts you for permission to access your Google Account.

When you land on the Dialogflow account settings page, check the box next to **Yes, I have read and accept the agreement** and click **Accept**. You will now be brought to a Dialogflow agent creation page. Click **CREATE**

![https://miro.medium.com/max/1326/1*1Lmmk9vtmd1_IdUr6zE1ZQ.jpeg](https://miro.medium.com/max/1326/1*1Lmmk9vtmd1_IdUr6zE1ZQ.jpeg)

An [agent](https://dialogflow.com/docs/agents) is an organizational unit that collects information needed to complete a user’s request, which it then forwards to a service that provides fulfillment logic.

You will now build the basic framework for fulfillment logic. This will be handled (later) by a Cloud Function, which will return a response with a youtube video.

Click **Fulfillment** from the left-hand menu. Move the slider for **Webhook** to the right, setting it to **Enabled**.

Now enter the temporary URL `https://google.com` in for the URL field. You will update this URL when you build your Cloud Function. Your page should resemble the following:

[https://miro.medium.com/max/1400/0*3OJNx7G5KGoffvL7](https://miro.medium.com/max/1400/0*3OJNx7G5KGoffvL7)

Scroll down and click **Save** in the bottom right corner. Then click **Intents from** the left-hand menu and select **Default Welcome Intent**:

[https://miro.medium.com/max/1400/0*cuide4Ik5dSt_Fbt](https://miro.medium.com/max/1400/0*cuide4Ik5dSt_Fbt)

You will now build the main entry point into your application by configuring the default welcome intent.

# **Conversational design**

Before you begin creating an Assistant application, it’s always a good idea to map out a conversation by writing a sample dialog(s). These dialogs give you a good reference point when you eventually start building your app. Here is a sample dialog for the application you’re building:

- **App**: *Hello there and welcome to Youtube Entertainment! What artist are you searching for?*
- **User**: Arijit Singh
- **App**: *Fetching your request… Okay, the top video from Arijit singh is “*[Ve Maahi](https://www.youtube.com/watch?v=j6muwUGdvXw&list=PLl7FgM8EYUapPTRkdVoRX2QJE3BxKYvG9)*” Here is a link to the video. See you next time!*

**Conversational design tips:** This example shows only a couple of the dialogs that you could write for your own apps. See this [Design Walkthrough](https://developers.google.com/actions/design/walkthrough#write_dialogs) for more examples of dialogs.

# **Configure the default welcome intent**

When you created the Dialogflow agent, a **Default Welcome intent** is automatically created. This intent represents the main entry point into your conversation and the main *action* of your app. Your app must have a Default Welcome Intent defined so that Actions on Google knows how to invoke your app. See [Invocation and Discovery](https://developers.google.com/actions/discovery/) for more information on how these invocation models work.

Make sure that you are in the Default Welcome Intent. Here are some things to notice about the default settings:

- The **Events** section of the intent specifies a Welcome event, which signifies that this intent is the default entry point into your app. The Google Assistant uses this event to trigger your app when users invoke your app by name, such as with *“Ok Google, talk to Youtube Entertainment”*.
- In the **Responses** section, there are default, static text responses. The **Default Welcome Intent** contains pre-populated responses that you should remove to add your own.

Now that you know the Default Welcome Intent’s purpose, you will modify some fields. Scroll down to the **Responses** section and click the **trash icon** to scrap all of the default text responses:

[https://miro.medium.com/max/1400/0*SdWAT2zk0rgAiAk9](https://miro.medium.com/max/1400/0*SdWAT2zk0rgAiAk9)

Now click on **ADD RESPONSES** > **Text response**, and type in the following: *Hello there and welcome to Youtube Entertainment! What artist are you searching for?*

![https://miro.medium.com/max/1400/1*CZk2tvw4wKp2LVffYU6dHA.png](https://miro.medium.com/max/1400/1*CZk2tvw4wKp2LVffYU6dHA.png)

Now scroll up and click **Save** in the top-right corner. Now, when users invoke your app, they know that they’re entering your app’s experience and what to say next.

# **Build a Custom Intent**

Dialogflow intents define your conversation’s grammar and what tasks to carry out when users speak specific phrases. You will now create an intent that parses user input for fields that are required by the Youtube Data API.

From the left-hand menu, click on the **+** icon by the **Intents** menu item. For the **Intent name** field, enter `youtube`.

In the **Training phrases** field click **Add Training Phrases**. Then in the Add user expression field enter in `arijit singh`and hit **Enter**.

Select Arijit Singh and assign it the `sys.any` entity:

![https://miro.medium.com/max/1178/1*ta_vwxpZj6Eh8NPAUPUVtA.jpeg](https://miro.medium.com/max/1178/1*ta_vwxpZj6Eh8NPAUPUVtA.jpeg)

You will now add some more user expressions in the **Training phrases** section, and assign any of those phrases the `@sys.any` entity. Add the following training phrases by entering them in the user expression field:

- shreya ghoshal
- ved sharma

Your Training phrases section should now resemble the following — note the highlighted fields, which have been assigned the `@sys.any` entity

![https://miro.medium.com/max/1184/1*KklpUEtmM3wuNdtwSl41LA.jpeg](https://miro.medium.com/max/1184/1*KklpUEtmM3wuNdtwSl41LA.jpeg)

Now that your training phrases have been added, scroll down and expand the **Actions and parameters** section.

Now check the **REQUIRED** checkbox for the `any` parameter. This tells Dialogflow to not trigger the intent until the parameter is properly provided by the user.

![https://miro.medium.com/max/1172/1*YgY7u1CPJzDQ2c49Zv2ljg.png](https://miro.medium.com/max/1172/1*YgY7u1CPJzDQ2c49Zv2ljg.png)

Build better voice apps. Get more articles & interviews from voice technology experts at [voicetechpodcast.com](https://voicetechpodcast.com/)

Now click on **Define prompts** for the number parameter (right-hand side) and provide a re-prompt phrase. Enter in `Who do you want to look up?`in the prompt field and then click **Close**

[https://miro.medium.com/max/1400/0*qQOv0hQBbVw5O7qs](https://miro.medium.com/max/1400/0*qQOv0hQBbVw5O7qs)

Now scroll to the top of the page and click **Save**.

Now scroll down to the **Fulfillment** section. Click the dropdown arrow and click **Enable fulfillment**. Then click the **Enable webhook call for this intent** slider

[https://miro.medium.com/max/1400/0*YF6k31lFuuUH-GyD](https://miro.medium.com/max/1400/0*YF6k31lFuuUH-GyD)

This tells Dialogflow to call your fulfillment to generate a response to the user instead of using Dialogflow’s response feature.

In the **Responses** section right above fulfillment, click **ADD RESPONSE**. Then click on the Google Assistant tab.

Move the toggle for **Set this intent as end of conversation**. This tells Dialogflow to relinquish control back to the Google Assistant after your fulfillment returns a response to the user.

[https://miro.medium.com/max/1400/0*L5OnFvyMhCo4qsKt](https://miro.medium.com/max/1400/0*L5OnFvyMhCo4qsKt)

Then scroll to the top of the intent and click **SAVE** once more to save the entire intent.

You have now successfully declared your conversation’s grammar with Dialogflow intents. You have also used Dialogflow’s built-in response feature to return a static response to the user when they invoke your app. You also created an intent that uses fulfillment to return a response, which you will build with Cloud Functions.

# **Enable the API**

**Open GCP Console for this step**. To make Youtube Data API requests from your Cloud Function, you will need to enable the Youtube Data API in your GCP Project.

Open the Navigation menu and select **APIs & Services** > **Library**. Then in the search bar, enter **Youtube Data API v3** and select it from the results page.

Then click **Enable**. Click the back button in your browser **twice** and ensure that your page resembles the following with the Youtube Data API enabled

[https://miro.medium.com/max/1400/0*1j555GQeYaS_ehWd](https://miro.medium.com/max/1400/0*1j555GQeYaS_ehWd)

# **Get an API Key**

To make Youtube Data API requests from your Cloud Function, you will need an API key to properly authenticate calls. Return to the GCP Console and open the Navigation menu.

From there, select **APIs & Services** > **Credentials**. Then click on the **Create credentials** dropdown and select **API Key**. You should be presented with the following panel

[https://miro.medium.com/max/1400/0*vUXOUbGZ188nVyJq](https://miro.medium.com/max/1400/0*vUXOUbGZ188nVyJq)

**Copy** the API key and save it somewhere for later use, like a text editor or notepad. Then click **Close**.

# **Initialize and configure a Cloud Function**

You will now build a Cloud Function to handle your fulfillment logic. Your fulfillment takes the extracted user input that Dialog flow parsed and responds with the user’s silly name. **Return to the GCP Console for this step**.

Open the Navigation menu and select **Cloud Functions**, which is located under the compute header. Then click **Create function**.

This will open a template to create a new Cloud Function. Your page will resemble the following

![https://miro.medium.com/max/1056/1*At9uiVKUODz_z4zqYRXURg.png](https://miro.medium.com/max/1056/1*At9uiVKUODz_z4zqYRXURg.png)

For the Cloud Function’s **Name** field, enter in `youtube`.

Now scroll down and find the inline editor for index.js and package.json. Make sure that the index.js tab is open. This file defines your fulfillment logic and is used to create and deploy a Cloud Function.

**Remove** the boilerplate code from the file. Then **copy and paste** the following code into index.js

**Replace** `<YOUR_API_KEY_HERE>` (line 12) with the API key you generated in the previous step.

Now open the package.json tab. This file declares package dependencies for your fulfillment, including the Actions client library. Replace the contents of the file with the following:

Once you have those files configured, scroll down and find the **Function to execute** field. Enter in `youtube` for the value.

Now click the **Create** button below. It will take about a minute for your function to be built. When the creation completes, your overview page will resemble the following:

![https://miro.medium.com/max/1288/1*b-1oZxroQMXgpBcsycC9Aw.jpeg](https://miro.medium.com/max/1288/1*b-1oZxroQMXgpBcsycC9Aw.jpeg)

Now click on the `youtube` function to get more details about it's configuration. Then click on the **trigger** tab. You will see a function URL that resembles the following:

![https://miro.medium.com/max/862/1*WPX7RyBsmc3SDu9QYWiO3g.jpeg](https://miro.medium.com/max/862/1*WPX7RyBsmc3SDu9QYWiO3g.jpeg)

**Copy** the function URL. You will use it as the URL for the Dialogflow webhook, which is configured in the next section.

# **Configure the Webhook**

Return to the Dialogflow console and click on the **Fulfillment** menu item from the left hand navigation menu.

In the URL field, replace `https://google.com` with the function URL you generated in the previous step.

Your webhook should now resemble the following:

![https://miro.medium.com/max/1400/1*Zu5sXMgR6d8R0zDrXgXpCA.jpeg](https://miro.medium.com/max/1400/1*Zu5sXMgR6d8R0zDrXgXpCA.jpeg)

Scroll down to the bottom of the page and click **Save** in the lower right corner.

# **Test your Assistant Application with the Actions Simulator**

Now that your Cloud Function has been deployed and your webhook has been properly set up, you can preview the app in the Actions simulator.

# **Check your Google Permission Settings**

In order to test the silly name maker that you built over the course of this lab, you need to enable the necessary permissions.

Open a new tab in your browser and visit the [Activity Controls page](https://myaccount.google.com/activitycontrols). Sign in with your Google credentials if prompted.

Ensure that the following permissions are enabled by sliding the toggles and selecting **TURN ON** for the following cards:

- Web & App Activity
- Device Information
- Voice & Audio Activity

Now **close** the Activity Controls page.

# **Test the application with the simulator**

Return to the Actions console. Then from the left-hand menu, under the TEST header click **Simulator**. You should now be on the following page:

[https://miro.medium.com/max/1016/0*yoCNGmUkB5D3GCe2](https://miro.medium.com/max/1016/0*yoCNGmUkB5D3GCe2)

The Actions simulator allows you to test your applications without any hardware like a Google Home.

Enter `Talk to my test app` in the **Input** area. You should receive a similar response:

[https://miro.medium.com/max/1400/0*HR8iuRm7_88yshmO](https://miro.medium.com/max/1400/0*HR8iuRm7_88yshmO)

Now enter a music artist (for example, Rahman.) You should receive a similar output in the right-hand panel, which includes the song name, link, and thumbnail

![https://miro.medium.com/max/1400/1*UG4EiN8_7XbbpKSbA9n7ZQ.png](https://miro.medium.com/max/1400/1*UG4EiN8_7XbbpKSbA9n7ZQ.png)

and you can listen to your [response](https://firebasestorage.googleapis.com/v0/b/reactflashcards-45cbc.appspot.com/o/download.mp3?alt=media&token=ed6069d2-2e35-46f9-85c7-e63a28d8c49a)

Try talking with your test app with new artists!

![https://miro.medium.com/max/544/1*bf_5gneDTxjEsua4R422QA.png](https://miro.medium.com/max/544/1*bf_5gneDTxjEsua4R422QA.png)