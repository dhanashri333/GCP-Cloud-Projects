# Build a Restaurant Locator with the Places API.md

# **Overview**

*[Google Assistant](https://assistant.google.com/#?modal_active=none) is a personal voice assistant that offers a host of actions and integrations. From sending texts and setting reminders, to ordering coffee and playing music, the 1 million+ actions available suit a wide range of voice command needs.*

![https://miro.medium.com/max/1400/1*IaBg3ABIM9G9oCj0HdqKRQ.png](https://miro.medium.com/max/1400/1*IaBg3ABIM9G9oCj0HdqKRQ.png)

[Google Cloud Functions](https://cloud.google.com/functions/docs/) is a lightweight compute solution for developers to create single-purpose, stand-alone functions that respond to Cloud events without the need to manage a server or runtime environment.

The [Places API](https://developers.google.com/places/web-service/intro) is a service that returns information about points of interest by using HTTP requests. More specifically, you will take advantage of the [Place Details](https://developers.google.com/places/web-service/details) and [Place Photos](https://developers.google.com/places/web-service/photos) services to receive detailed information and photos of establishments.

By utilizing Cloud Functions and the Places API, you will build an Assistant application that takes in a user’s current location and restaurant preferences to generate the ideal restaurant for them to visit, complete with names, addresses, and photos.

# **Objectives**

- Build an Assistant application pipeline that consists of an Actions project, a Dialog flow agent with custom intents and entities, a webhook, and a Cloud Function to handle fulfillment.
- Generate the proper authentication credentials and install necessary dependencies to use the Places API.
- Add fulfillment logic to the Cloud Function to handle Places API calls.
- Deploy your application and test it with the Actions Simulator.

# **Prerequisites**

- Google Cloud Account
- Pair of headphones or turning the volume up on your computer is recommended.

# **Create an Actions project**

Regardless of the Assistant application, you’re building, you will always have to create an Actions project so your app has an underlying organizational unit.

Open the [Actions on Google Developer Console](http://console.actions.google.com/) in a new tab. You should be looking at a clean Actions console that resembles the following (If you are a new user (:

![https://miro.medium.com/max/1400/1*b8AZokwlt0lFrbRNYsDeog.png](https://miro.medium.com/max/1400/1*b8AZokwlt0lFrbRNYsDeog.png)

Click **Add/import project** and agree to Actions on Google’s terms of service when prompted.

Click into the Project Name field and Add the project name **Restaurant Locator**

Once the project is created you will see a welcome screen like below

![https://miro.medium.com/max/1400/1*fKW30EQKA-QCvURHPtWWHg.png](https://miro.medium.com/max/1400/1*fKW30EQKA-QCvURHPtWWHg.png)

Now click **Actions on Google** in the top left corner to return to the homepage. Then click on the project you just created

# **Build an Action**

From the left-hand menu, select **Build > Actions > Add your first action**. Then select **Custom Intent > BUILD**

[https://miro.medium.com/max/1164/0*55diJ6Htz0YFfqHw](https://miro.medium.com/max/1164/0*55diJ6Htz0YFfqHw)

This will take you to the Dialogflow console.

click **Allow** when Dialogflow prompts you for permission to access your Google Account.

When you land on the Dialogflow account settings page, check the box next to **Yes, I have read and accept the agreement** and click **Accept**. You will now be brought to a Dialogflow agent creation page. Click **CREATE**

![https://miro.medium.com/max/1318/1*Abt3Yc2pLDV3aiDqKqG30g.jpeg](https://miro.medium.com/max/1318/1*Abt3Yc2pLDV3aiDqKqG30g.jpeg)

An [agent](https://dialogflow.com/docs/agents) is an organizational unit that collects information needed to complete a user’s request, which it then forwards to a service that provides fulfillment logic.

You will now build the basic framework for fulfillment logic. This will be handled (later) by a Cloud Function, which will return a response with restaurant suggestions.

Click **Fulfillment** from the left-hand menu. Move the slider for **Webhook** to the right, setting it to **Enabled**.

Now enter the temporary URL `https://google.com` in for the URL field. You will update this URL when you build your Cloud Function. Your page should resemble the following:

[https://miro.medium.com/max/1400/0*3OJNx7G5KGoffvL7](https://miro.medium.com/max/1400/0*3OJNx7G5KGoffvL7)

Scroll down and click **Save** in the bottom right corner. Then click **Intents from** the left-hand menu and select **Default Welcome Intent**:

[https://miro.medium.com/max/1400/0*cuide4Ik5dSt_Fbt](https://miro.medium.com/max/1400/0*cuide4Ik5dSt_Fbt)

You will now build the main entry point into your application by configuring the default welcome intent.

# **Conversational design**

Before you begin creating an Assistant application, it’s always a good idea to map out a conversation by writing a sample dialog(s). These dialogs give you a good reference point when you eventually start building your app. Here is a sample dialog for the application we’re building

- ***App**: Hello there and welcome to Restaurant Locator! What is your location?*
- ***Use**r: Lakshmi mills junction, Coimbatore*
- ***App**: Okay. How far are you willing to travel?*
- ***Use**r: About 3 miles.*
- ***App**: What type of food or cuisine are you looking for?*
- ***User**: Chinese food.*
- ***App**: Fetching your request… Okay, the restaurant name is “X” and the address is “Y”. The following photo from a Google Places user might whet your appetite!*

> Conversational design tips: This example shows only a couple of the dialogs that you could write for your own apps. See this Design Walkthrough for more examples of dialogs.
> 

# **Build the Default Welcome Intent**

Make sure that you are in the Default Welcome Intent. Scroll down to the **Responses** section and click the **trash icon** to scrap all of the default text responses:

[https://miro.medium.com/max/1400/0*wYC7cQDtxhS_4YIY](https://miro.medium.com/max/1400/0*wYC7cQDtxhS_4YIY)

Now click on **ADD RESPONSES** > **Text response**, and type in the following *Hello there and welcome to Restaurant Locator! What is your location?*

[https://miro.medium.com/max/1400/0*V2aGNvXlJaj8oAty](https://miro.medium.com/max/1400/0*V2aGNvXlJaj8oAty)

Now scroll up and click **Save** in the top-right corner. Now, when users invoke your app, they know that they’re entering your app’s experience and what to say next.

# **Build the Custom Intent**

Dialogflow intents define your conversation’s grammar and what tasks to carry out when users speak specific phrases. You will now create an intent that parses user input for fields that are required by the Places API.

From the left-hand menu, click on the **+** icon by the **Intents** menu item. For the **Intent name** field, enter `get_restaurant`.

In the Training phrases field click **Add Training Phrases**. Then in the Add user expression field enter in `45 Bandara Street` and hit **Enter**.

The address should be highlighted and assigned the `@sys.location`entity:

![https://miro.medium.com/max/1144/1*lIhp5H9I_hberDPWD_R0Dg.png](https://miro.medium.com/max/1144/1*lIhp5H9I_hberDPWD_R0Dg.png)

By assigning this portion of the User says to be an entity, Dialogflow can extract parameters from the user input, validate the parameter and provide it to your fulfillment as a variable.

You will now add some more user expressions in the **Training phrases section**. Add the following training phrases by entering them in the user expression field:

- *60 GopalBagh, Coimbatore, Tamilnadu*
- *20 W, R.S.Puram, Coimbatore-641002*

Your Training phrases section should now resemble the following — note the highlighted addresses, which align with the `@sys.location` entity:

![https://miro.medium.com/max/1172/1*Sk1EworRxgqdHORQYq5JhQ.png](https://miro.medium.com/max/1172/1*Sk1EworRxgqdHORQYq5JhQ.png)

If one of these values weren’t assigned the `@sys.location` entity, click on the address and manually assign it so that it resembles the above screenshot.

Now that your training phrases have been added, scroll down and expand the **Actions and parameters** section.

Now check the **REQUIRED** checkbox for the `@sys.location` entity. This tells Dialogflow not to trigger the intent until the parameter is properly provided by the user.

Now click on **Define prompts** for location (right-hand side) and provide a re-prompt phrase. Enter in `What is your address?` in the prompt field and then click **Close**

[https://miro.medium.com/max/1400/0*SlPbOhNuOHwm_wQH](https://miro.medium.com/max/1400/0*SlPbOhNuOHwm_wQH)

This phrase will be spoken to the user repeatedly until Dialogflow detects an address in the user input.

Still in the Action and parameters section, click **+ New Parameter** and add the following information:

- **Required** — Select the checkbox
- **Parameter name** — proximity
- **Entity** — @sys.unit-length
- **Value** — $proximity
- **Prompts** — How far are you willing to travel?

Once again, click **+ New Parameter** and add the following information

- **Required** — Select the checkbox
- **Parameter name** — cuisine
- **Entity** — @sys.any
- **Value** — $cuisine
- **Prompts** — What type of food or cuisine are you looking for?

Build better voice apps. Get more articles & interviews from voice technology experts at [voicetechpodcast.com](https://voicetechpodcast.com/)

Your actions and parameters section should now resemble the following

[https://miro.medium.com/max/1400/0*CnGXNeXIv5W7Ydej](https://miro.medium.com/max/1400/0*CnGXNeXIv5W7Ydej)

Now scroll to the top of the page and click **Save**.

Now scroll down to the **Fulfillment** section. Click the dropdown arrow and click **Enable fulfillment**. Then click the **Enable webhook call for this intent** slider:

[https://miro.medium.com/max/1400/0*0Yrh6TtVar2xUN4U](https://miro.medium.com/max/1400/0*0Yrh6TtVar2xUN4U)

In the **Responses** section right above fulfillment, click **ADD RESPONSE**. Then click on the Google Assistant tab.

Move the toggle for **Set this intent as end of conversation**. This tells Dialogflow to relinquish control back to the Google Assistant after your fulfillment returns a response to the user.

[https://miro.medium.com/max/1400/0*ERXBfn2CVA5o1RFp](https://miro.medium.com/max/1400/0*ERXBfn2CVA5o1RFp)

Then scroll to the top of the intent and click **SAVE** once more to save the entire intent.

# **Get an API Key**

To make Places API requests from your Cloud Function, you will need an API key to properly authenticate calls.

Go visit the [maps platform page](https://cloud.google.com/maps-platform/?apis=maps,places). Once there click **Getting Started** and check the boxes for **Maps** and **Places**. Then click **Continue**

![https://miro.medium.com/max/1400/1*SEYRjoxPaOdZ-jZHlZxzhg.png](https://miro.medium.com/max/1400/1*SEYRjoxPaOdZ-jZHlZxzhg.png)

Then from the dropdown select your Project name(restaurant locator)and click **Next**. Click **Next** when you’re brought to the page to enable your APIs. You will now be brought to the following page that displays your API key

![https://miro.medium.com/max/1362/1*aXlFh8Kctg1yCjKsiUmrCA.jpeg](https://miro.medium.com/max/1362/1*aXlFh8Kctg1yCjKsiUmrCA.jpeg)

**Copy** the API key and save it somewhere for later use, like a text editor or notepad. Then click **Done** and close the tab.

# **Initialize and configure a Cloud Function**

You will now build a Cloud Function to handle your fulfillment logic. Your fulfillment takes the extracted user input that Dialog flow parsed and responds with the user’s silly name. **Return to the GCP Console for this step**.

Open the Navigation menu and select **Cloud Functions**, which is located under the compute header. Then click **Create function**.

This will open a template to create a new Cloud Function. Your page will resemble the following

![https://miro.medium.com/max/1056/1*At9uiVKUODz_z4zqYRXURg.png](https://miro.medium.com/max/1056/1*At9uiVKUODz_z4zqYRXURg.png)

For the Cloud Function’s **Name** field, enter in **restaurant_locator**

Now scroll down and find the inline editor for index.js and package.json. Make sure that the index.js tab is open. This file defines your fulfillment logic and is used to create and deploy a Cloud Function.

**Remove** the boilerplate code from the file. Then **copy and paste** the following code into index.js

**Replace** `<YOUR_API_KEY_HERE>` (line 18) with the API key you generated in the previous step.

**Replace** `<YOUR_REGION>` (line 27) with your ccTLD (country code top-level domain). [This wikipedia page](https://en.wikipedia.org/wiki/List_of_Internet_top-level_domains#A) lists all ccTLDs. This table will help you with conversions

![https://miro.medium.com/max/1168/1*PJJTwFtB6qF-9k6JUlKfdA.jpeg](https://miro.medium.com/max/1168/1*PJJTwFtB6qF-9k6JUlKfdA.jpeg)

Once your API key and region fields are properly filled out, open the package.json tab. This file declares package dependencies for your fulfillment, including the Actions client library. Replace the contents of the file with the following

Once you have those files configured, scroll down and find the **Function to execute** field. Enter in `get_restaurant` for the value.

Now click the **Create** button below. It will take about a minute for your function to be built. In the meantime, let’s break down the code found in index.js.

When the creation completes, your overview page will resemble the following

![https://miro.medium.com/max/1400/1*2OIRWKsUQDwQiBrjTiBhug.png](https://miro.medium.com/max/1400/1*2OIRWKsUQDwQiBrjTiBhug.png)

Now click on the `restaurant_locator` function to get more details about it's configuration. Then click on the **trigger** tab. You will see a function URL that resembles the following

![https://miro.medium.com/max/1400/1*3z9YcOzlInjBcYqyheLRRQ.jpeg](https://miro.medium.com/max/1400/1*3z9YcOzlInjBcYqyheLRRQ.jpeg)

**Copy** the function URL. You will use it as the URL for the Dialogflow webhook, which is configured in the next section.

# **Configure the webhook**

Return to the Dialogflow console and click on the **Fulfillment** menu item from the left hand navigation menu. In the URL field, replace `https://google.com` with the function URL you generated in the previous step. Your webhook should now resemble the following:

![https://miro.medium.com/max/1288/1*zmUElHNPjN3PEJQqki-Gbg.jpeg](https://miro.medium.com/max/1288/1*zmUElHNPjN3PEJQqki-Gbg.jpeg)

Scroll down to the bottom of the page and click **Save** in the lower right corner.

Now that your Cloud Function has been deployed and your webhook has been properly set up, you can preview the app in the Actions simulator.

# **Change your Google permission settings**

In order to test the application that you built you need to enable the necessary permissions.

Open a new tab in your browser and visit the [Activity Controls page](https://myaccount.google.com/activitycontrols). Sign in with your Google credentials if prompted.

Ensure that the following permissions are enabled by sliding the toggles and selecting **TURN ON** for the following cards:

- Web & App Activity
- Device Information
- Voice & Audio Activity

Now **close** the Activity Controls page.

# **Test the application with the Actions simulator**

Return to the Actions console. Then from the left-hand menu, under the TEST header click **Simulator**. You should now be on the following page

[https://miro.medium.com/max/1016/0*9GVlbDCKWu9OuaWG](https://miro.medium.com/max/1016/0*9GVlbDCKWu9OuaWG)

The Actions simulator allows you to test your applications without any hardware like a Google Home.

Enter `Talk to my test app` in the **Input** area. You should receive a similar response

[https://miro.medium.com/max/1400/0*WGD5RWOippIjRjuv](https://miro.medium.com/max/1400/0*WGD5RWOippIjRjuv)

Now enter a valid address (for example, “Lakshmi mills junction, Coimbatore”.) You should then receive the following output:

![https://miro.medium.com/max/672/1*oaP6HkIx58hs6MOAVMMONg.png](https://miro.medium.com/max/672/1*oaP6HkIx58hs6MOAVMMONg.png)

Now enter in a distance (for example, 3 miles away.) You should receive a similar output

![https://miro.medium.com/max/668/1*SU-adojbe6O5nUcqZ0nt4A.png](https://miro.medium.com/max/668/1*SU-adojbe6O5nUcqZ0nt4A.png)

Enter in a cuisine like “Chinese” or “Thai food”. Soon after you will receive a similar response fitted with a picture in the right-hand panel

![https://miro.medium.com/max/556/1*VWSBvTehlN8aFpXiYQ5zjg.png](https://miro.medium.com/max/556/1*VWSBvTehlN8aFpXiYQ5zjg.png)

Try talking with your test app with new addresses, proximities, and cuisines/types of food!

#
