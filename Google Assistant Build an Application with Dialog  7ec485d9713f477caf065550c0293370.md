# Google Assistant: Build an Application with Dialog flow and Cloud Functions

# ***Overview***

*[Google Assistant](https://assistant.google.com/#?modal_active=none) is a personal voice assistant that offers a host of actions and integrations. From making appointments and setting reminders, to ordering coffee and playing music, the 1 million+ actions available suit a wide range of voice command tasks. Google Assistant is offered on Android and iOS, but it can even be integrated with other devices like smartwatches, Google Homes, and Android TVs.*

[https://miro.medium.com/max/1400/0*-w3Xhth86dexDdSg](https://miro.medium.com/max/1400/0*-w3Xhth86dexDdSg)

Photo by [Charles 🇵🇭](https://unsplash.com/@charlesdeluvio?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

[Actions](https://developers.google.com/actions/extending-the-assistant) are the central platform for developing Google Assistant applications. The Actions platform integrates with human-computer interaction suites, which simplifies conversational app development. The most widely used suite is [Dialogflow](https://dialogflow.com/), which uses an underlying machine learning (ML) and natural language understanding (NLU) schema to build rich Assistant applications. The Actions platform also integrates with [Cloud Functions](https://cloud.google.com/functions/), which lets you run backend fulfillment code in response to events triggered by Dialogflow requests.

You will get hands-on practice with the Actions platform, the Dialogflow suite, and Cloud Functions by building a “Silly Name Maker” application, which returns a user with a silly name after they have entered in a lucky number and favorite color. You will build a Dialogflow agent that intelligently parses user input for specific information. The agent will be supplemented with a webhook, which will trigger a Cloud Function that handles fulfillment logic and returns your user with their silly name.

# **Objectives**

- Create an Actions project and build an Action.
- Create a Dialogflow agent and configure the default welcome intent.
- Build a custom intent with entities.
- Initialize a Cloud Function.
- Add fulfillment logic and packages to your Cloud Function.
- Add a webhook to your Action.
- Test your Assistant application with the Actions simulator on expected and unexpected conversational paths.

# **Prerequisites**

- Google Cloud Account
- Pair of headphones or turning the volume up on your computer is recommended.

# **Let’s Begin**

# **Create an Actions project**

Regardless of the Assistant application, you’re building, you will always have to create an Actions project so your app has an underlying organizational unit.

Open the [Actions on Google Developer Console](http://console.actions.google.com/) in a new tab. You should be looking at a clean Actions console that resembles the following (If you are a new user (:

![https://miro.medium.com/max/1400/1*b8AZokwlt0lFrbRNYsDeog.png](https://miro.medium.com/max/1400/1*b8AZokwlt0lFrbRNYsDeog.png)

Click **Add/import project** and agree to Actions on Google’s terms of service when prompted.

Click into the Project Name field and Add the project name **SillyNameMaker**

Once the project is created you will see a welcome screen like below

![https://miro.medium.com/max/1400/1*fKW30EQKA-QCvURHPtWWHg.png](https://miro.medium.com/max/1400/1*fKW30EQKA-QCvURHPtWWHg.png)

Now click **Actions on Google** in the top left corner to return to the homepage. Then click on the project you just created

# **Build an Action**

An [action](https://developers.google.com/actions/glossary#A) is an interaction you build for the Google Assistant. An action supports a specific [intent](https://developers.google.com/actions/glossary#intent) (a goal or task that users want to accomplish), which is carried out by a corresponding [fulfillment](https://developers.google.com/actions/glossary#fulfillment) (logic that handles an intent and carries out the corresponding Action.) You will now build an Action that supports silly name generation.

From the left-hand menu click **Build** > **Actions** > **ADD YOUR FIRST ACTION**. Then select **Custom Intent** > **BUILD**

[https://miro.medium.com/max/1164/0*eLFRIxojm5RWs0xp](https://miro.medium.com/max/1164/0*eLFRIxojm5RWs0xp)

This will take you to the Dialogflow console. click **Allow** when Dialogflow prompts you for permission to access your Google Account.

When you land on the Dialogflow account settings page, check the box next to **Yes, I have read and accept the agreement** and click **Accept**. You will now be brought to a Dialogflow agent creation page. Click **CREATE**

![https://miro.medium.com/max/1296/1*VX4GikO7mMnvirb1j7g9Xg.png](https://miro.medium.com/max/1296/1*VX4GikO7mMnvirb1j7g9Xg.png)

An [agent](https://dialogflow.com/docs/agents) is an organizational unit that collects information needed to complete a user’s request, which it then forwards to a service that provides fulfillment logic.

You will now build the basic framework for fulfillment logic. This will be handled (later) by a Cloud Function, which will return a response with a user’s silly name.

Click **Fulfillment** from the left-hand menu. Move the slider for **Webhook** to the right, setting it to **Enabled**.

Now enter the temporary URL `https://google.com` in for the URL field. You will update this URL when you build your Cloud Function. Your page should resemble the following:

[https://miro.medium.com/max/1400/0*3OJNx7G5KGoffvL7](https://miro.medium.com/max/1400/0*3OJNx7G5KGoffvL7)

Scroll down and click **Save** in the bottom right corner. Then click **Intents from** the left-hand menu and select **Default Welcome Intent**:

[https://miro.medium.com/max/1400/0*cuide4Ik5dSt_Fbt](https://miro.medium.com/max/1400/0*cuide4Ik5dSt_Fbt)

You will now build the main entry point into your application by configuring the default welcome intent.

# **Configure the default welcome intent**

When you created the Dialogflow agent, a **Default Welcome intent** is automatically created. This intent represents the main entry point into your conversation and the main *action* of your app. Your app must have a Default Welcome Intent defined so that Actions on Google knows how to invoke your app. See [Invocation and Discovery](https://developers.google.com/actions/discovery/) for more information on how these invocation models work.

Make sure that you are in the Default Welcome Intent. Here are some things to notice about the default settings:

- The **Events** section of the intent specifies a Welcome event, which signifies that this intent is the default entry point into your app. The Google Assistant uses this event to trigger your app when users invoke your app by name, such as with *“Ok Google, talk to Silly Name Maker”*.
- In the **Responses** section, there are default, static text responses. The **Default Welcome Intent** contains pre-populated responses that you should remove to add your own.

Now that you know the Default Welcome Intent’s purpose, you will modify some fields. Scroll down to the **Responses** section and click the **trash icon** to scrap all of the default text responses:

[https://miro.medium.com/max/1400/0*SdWAT2zk0rgAiAk9](https://miro.medium.com/max/1400/0*SdWAT2zk0rgAiAk9)

Now click on **ADD RESPONSES** > **Text response**, and type in the following: *Hi! Welcome to Silly Name Maker! Let’s get started. What is your lucky number?*

[https://miro.medium.com/max/1400/0*9eDSxp2fJNmU03Z-](https://miro.medium.com/max/1400/0*9eDSxp2fJNmU03Z-)

Now scroll up and click **Save** in the top-right corner. Now when users invoke your app, they know that they are entering your app’s experience and what they should say next.

# **Design the conversation**

Before you begin building out your Assistant application’s language path, it’s always a good idea to map out a conversation by writing sample dialogs. For the Silly Name Maker app, we’ve provided a couple of simple dialogs, which are described below:

# **Expected path**

- **App**: Hi! Welcome to Silly Name Maker! Let’s get started. What is your lucky number?
- **User**: 5
- **App**: What is your favorite color?
- **User**: Yellow
- **App**: Alright, your silly name is Yellow 5! I hope you like it. See you next time.

# **Unexpected path**

- **App**: Hi! Welcome to Silly Name Maker! Let’s get started. What is your lucky number?
- **User**: Dog
- **App**: Hmm that doesn’t sound like a number to me. What’s your lucky number again?
- **User**: 22
- **App**: What is your favorite color?
- **User**: Blue
- **App**: Alright, your silly name is Blue 22! I hope you like it. See you next time.

**Conversational design tips:** This example shows only a couple of the dialogs that you could write for your own apps. See this [Design Walkthrough](https://developers.google.com/actions/design/walkthrough#write_dialogs) for more examples of dialogs.

Now that you have a better understanding of the type of application you’ll be building, you will develop your custom intent to parse and extract colors and numbers from user input.

# **Configure a custom intent**

Dialogflow intents define your conversation’s grammar and what tasks to carry out when users make specific requests. Since Dialogflow includes a natural language understanding (NLU) engine, you don’t need to be exhaustive when defining phrases. Dialogflow automatically expands on the phrases you provide and understands many more variations of them.

You will now create an intent that defines how users need to provide their lucky number and favorite color to generate a silly name.

From the left hand menu, click on the **+** icon by the **Intents** menu item. For the **Intent name** field, enter `make_name`

[https://miro.medium.com/max/1400/0*R7cr83_FDVB6Bc-p](https://miro.medium.com/max/1400/0*R7cr83_FDVB6Bc-p)

In the **Training phrases** field click **Add Training Phrases**. Then in the Add user expression field enter in `My lucky number is 23` and hit **Enter**. The number 23 is highlighted, and assigned the `@sys.number` "entity", which will be reflected in the Action and parameters section:

[https://miro.medium.com/max/1400/0*ebAmc5QcFZQgiqVR](https://miro.medium.com/max/1400/0*ebAmc5QcFZQgiqVR)

[Entities](https://dialogflow.com/docs/entities) extract specific data from user expressions and store them in accessible variables. By assigning this portion of the user says to be an entity, Dialogflow can extract parameters from the user input, validate the parameter and provide it to your fulfillment as a variable.

Without assigning entities, you would have to parse the input yourself and find the parameters you need. If you expand the **Actions and parameters section**, you will see that a parameter was automatically created for `sys.number` (you'll modify this later):

[https://miro.medium.com/max/1400/0*pW3qrAuAjwjqotc0](https://miro.medium.com/max/1400/0*pW3qrAuAjwjqotc0)

You will now add some more user expressions in the **Training phrases section**, and assign any number within those phrases the `@sys.number`entity (if it isn't automatically set.) Add the following training phrases by entering them in the "Add user expression" field

- *23*
- *The luckiest number I have is 12*
- *My lucky number is 18*

Your Training phrases section should now resemble the following — note the highlighted numbers, which align with the `@sys.number` entity

[https://miro.medium.com/max/1400/0*wCfPnQx_yoGpxLzm](https://miro.medium.com/max/1400/0*wCfPnQx_yoGpxLzm)

Now that your training phrases have been added, scroll down and expand the **Actions and parameters** section.

Check the **REQUIRED** checkbox for the number parameter. This tells Dialogflow to not trigger the intent until the parameter is properly provided by the user.

Now click on **Define prompts** for the number parameter (right-hand side) and provide a re-prompt phrase. Type in *What’s your lucky number?* in the prompt field and then click **Close**

[https://miro.medium.com/max/1400/0*o3rg0bjI7YxCQysT](https://miro.medium.com/max/1400/0*o3rg0bjI7YxCQysT)

Build better voice apps. Get more articles & interviews from voice technology experts at [voicetechpodcast.com](https://voicetechpodcast.com/)

This phrase will be spoken to the user repeatedly until Dialogflow detects a number in the user input.

In the Action and parameters section, add a parameter with the following information:

- **Required** — Select the checkbox
- **Parameter name** — color
- **Entity** — `@sys.color`
- **Value** — `$color`
- **Prompts** — What’s your favorite color?

This additional parameter uses Dialogflow’s built-in “slot-filling” feature, which allows you to obtain additional input parameters from the user without having to create an intent for each one and without having to make the user speak all the required input in one phrase.

Slot filling also lets you set parameters as being required so that your agent doesn’t process the input until the user provides all required parameters. Your actions and parameters should now resemble the following

[https://miro.medium.com/max/1400/0*b1fewMsHwTx9CBNM](https://miro.medium.com/max/1400/0*b1fewMsHwTx9CBNM)

Now scroll to the top of the page and click **Save**.

Now scroll down and expand the **Fulfillment** section and click **Enable fulfillment**. Then click the **Enable webhook call for this intent** slider

[https://miro.medium.com/max/1400/0*wPEINQTUsAAmy-TT](https://miro.medium.com/max/1400/0*wPEINQTUsAAmy-TT)

This tells Dialogflow to call your fulfillment to generate a response to the user instead of using Dialogflow’s response feature.

In the **Responses** section right above Fulfillment, click **ADD RESPONSE**. Then click on the **Google Assistant** tab.

Move the toggle for **Set this intent as end of conversation**. This tells Dialogflow to relinquish control back to the Google Assistant after your fulfillment returns a response to the user.

[https://miro.medium.com/max/1400/0*WEIAXfOV84kzQ4RS](https://miro.medium.com/max/1400/0*WEIAXfOV84kzQ4RS)

Then scroll to the top of the intent and click **SAVE** once more to save the entire intent.

You have now successfully declared your conversation’s grammar with Dialogflow intents. You have also used Dialogflow’s built-in response feature to return a static response to the user when they invoke your app. You also created an intent that uses fulfillment to return a response, which you will now implement with a Cloud Function.

# **Initialize and configure a Cloud Function**

You will now build a Cloud Function to handle your fulfillment logic. Your fulfillment takes the extracted user input that Dialog flow parsed and responds with the user’s silly name. **Return to the GCP Console for this step**.

Open the Navigation menu and select **Cloud Functions**, which is located under the compute header. Then click **Create function**.

This will open a template to create a new Cloud Function. Your page will resemble the following

![https://miro.medium.com/max/1056/1*At9uiVKUODz_z4zqYRXURg.png](https://miro.medium.com/max/1056/1*At9uiVKUODz_z4zqYRXURg.png)

For the Cloud Function’s **Name** field, enter in `silly-name-maker`.

Now scroll down and find the inline editor for `index.js` and `package.json`. Make sure that the `index.js` tab is open. This file defines your fulfillment logic and is used to create and deploy a Cloud Function. Here are some specifics on its basic functioning

- When Dialogflow intents are triggered, the intent’s action name (declared in the action area of the intent) is provided to you in the request to your fulfillment. You use this action name to determine what logic to carry out.
- Within every request to your fulfillment, if Dialogflow parsed parameters from the user input, you can access the parameter by name. Here, you declare the names of the parameters so you can access them later.
- This function fulfills the action by generating a silly name. It’s called whenever the `make_name` intent is triggered. It uses the parameters from the user input to generate the name.

Now that you have a better understanding of `index.js`, you will build out the function's fulfillment logic. Remove the boilerplate code from the file. Then copy and paste the following code into `index.js`

```
'use strict';const {dialogflow} = require('actions-on-google');
const functions = require('firebase-functions');const app = dialogflow({debug: true});app.intent('make_name', (conv, {color, number}) => {
  conv.close(`Alright, your silly name is ${color} ${number}! ` +
    `I hope you like it. See you next time.`);
});exports.sillyNameMaker = functions.https.onRequest(app);
```

Now open the `package.json` tab. This file declares package dependencies for your fulfillment, including the Actions client library. Replace the contents of the file with the following:

```
{
  "name": "silly-name-maker",
  "description": "Find out your silly name!",
  "version": "0.0.1",
  "author": "Google Inc.",
  "engines": {
    "node": "8"
  },
  "dependencies": {
    "actions-on-google": "^2.0.0",
    "firebase-admin": "^4.2.1",
    "firebase-functions": "1.0.0"
  }
}
```

Once you have those files configured, scroll down and find the **Function to execute** field. Enter in `sillyNameMaker` for the value.

Now click the **Create** button below. It will take about a minute for your function to be built. When the creation completes, your overview page will resemble the following

![https://miro.medium.com/max/1400/1*IpKaRYXu_Kn1qaA-SxxsjA.png](https://miro.medium.com/max/1400/1*IpKaRYXu_Kn1qaA-SxxsjA.png)

Now click on the `silly-name-maker` function to get more details about it's configuration. Then click on the **trigger** tab. You will see a function URL that resembles the following

![https://miro.medium.com/max/1056/1*TZvTbfngVtPHOy83XjBi1Q.jpeg](https://miro.medium.com/max/1056/1*TZvTbfngVtPHOy83XjBi1Q.jpeg)

**Copy** the function URL. You will use it as the URL for the Dialogflow webhook, which is configured in the next section.

# **Configure the webhook**

Return to the Dialogflow console and click on the **Fulfillment** menu item from the left hand navigation menu.

In the URL field, replace `https://google.com` with the function URL you generated in the previous step.

Your webhook should now resemble the following:

![https://miro.medium.com/max/1320/1*QDdJax-IwCFnPbyBofC9lg.png](https://miro.medium.com/max/1320/1*QDdJax-IwCFnPbyBofC9lg.png)

Scroll down to the bottom of the page and click **Save** in the lower right corner.

# **Test your Assistant application with the Actions simulator**

Now that your Cloud Function has been deployed and your webhook has been properly set up, you can preview the app in the Actions simulator.

# **Check your Google permission settings**

In order to test the Silly Name Maker, you need to enable the necessary permissions.

Open a new tab in your browser and visit the [Activity Controls page](https://myaccount.google.com/activitycontrols). Sign in with your Google Account if requested.

Ensure that the following permissions are enabled by sliding the toggles to **TURN ON** the following cards:

- Web & App Activity
- Device Information
- Voice & Audio Activity

Now **close** the Activity Controls page.

# **Test the application with the simulator**

Return to the Actions console. Then from the left-hand menu, under the **TEST** header, click **Simulator**. You should now be on the following page

[https://miro.medium.com/max/1016/0*umGjXiHHKePO-xRJ](https://miro.medium.com/max/1016/0*umGjXiHHKePO-xRJ)

To invoke the Action, hit the enter key in the **Talk to my test app** box near the bottom of the simulator console. You should be presented with a similar response:

[https://miro.medium.com/max/1400/0*TE9yzytdg8XT9p6M](https://miro.medium.com/max/1400/0*TE9yzytdg8XT9p6M)

Now enter in a number. When prompted to enter in a favorite color, enter in a color. You should be returned with a silly name built from the number and color you entered in

[https://miro.medium.com/max/1400/0*jqi8ci3J-eiwG0PZ](https://miro.medium.com/max/1400/0*jqi8ci3J-eiwG0PZ)

Now try entering in some input that follows the unexpected path. Enter `Talk to my test app` in the **Input** area once more. Then provide a number when asked to enter in your lucky number.

When asked for your favorite color, try entering in a word that isn’t a color. The reprompt phrase should kick in, asking you for your favorite color again

[https://miro.medium.com/max/1232/0*9gOX1U4ra3CDH_pi](https://miro.medium.com/max/1232/0*9gOX1U4ra3CDH_pi)

Once you provide the correct input, your silly name will be generated and the conversation will end.

#