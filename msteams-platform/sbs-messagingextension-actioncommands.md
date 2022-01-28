### YamlMime:Tutorial
title: Messaging extensions 
metadata:
  title: Messaging extensions 
  description: In this tutorial, you'll learn to set up  Messaging extensions for Teams.
  audience: Developer
  level: Beginner
  ms.date: 12/06/2021
  ms.topic: interactive-tutorial
  nextTutorialHref: apps-in-teams-meetings/enable-and-configure-your-app-for-teams-meetings.md
  nextTutorialTitle: Read more to enable and configure apps for meetings
  ms.custom: mvc
  ms.localizationpriority: high
items:
- durationInMinutes: 1
  content: |
   Messaging extensions allow the users to interact with your web service through buttons and forms in the Microsoft Teams client.

    This step-by-step guide illustrates how to build an Action-based Messaging Extension.
   
    You'll see the following output:

       :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png alt-text="output" "border=true":::

- title: Prerequisites
  durationInMinutes: 1
  content: |
    Ensure you install the following tools and set up your development environment:  

    * [.NET Core SDK](https://dotnet.microsoft.com/download) version 3.1
    * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
    * [ngrok](https://ngrok.com/download) latest version (only for devbox testing) or any equivalent tunneling solution
    
      > [!NOTE]
      > After downloading ngrok, sign up and install [authtoken](https://ngrok.com/download).
    
    * [Microsoft Teams](https://teams.microsoft.com/) with valid account
    * [SignalR](https://docs.microsoft.com/en-us/aspnet/signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc) to update agenda in real-time

    > [!NOTE]
    > Use version 1.7.0 or later of [Teams SDK](/javascript/api/overview/msteams-client?view=msteams-client-js-latest&preserve-view=true), as versions prior to it do not support meeting sidepanel.

- title: Set up local environment
  durationInMinutes: 1
  content: |
   Clone `BotBuilder-Samples` repository to your local GitHub:  

    1. Open [Microsoft Teams Samples](https://github.com/OfficeDev/Microsoft-Teams-Samples).
    1. Select **Code**.
    1. From the dropdown menu, select **Open with GitHub Desktop**.

        :::image type="content" source="assets/images/sbs-messagingextension-action/output-card.png alt-text="output" "border=true":::

    1. Select **Clone**. 

- title: Create and register your bot in Azure AD portal
  durationInMinutes: 5
  content: |
    To create and register your bot in Azure Active Directory, create a tunnel using ngrok, and add messaging endpoint, perform the following steps:

    * Create Azure Bot resource to register bot with Azure Bot Service.
    * Create client secret to enable SSO authentication of the bot.
    * Add Microsoft Teams channel to deploy the bot to a Teams channel.
    * Use ngrok to create a tunnel to your web server's endpoints.
    * Add messaging endpoint to the ngrok tunnel you created.

    **To create Azure Bot resource**

    1. Go to the [Azure portal](https://portal.azure.com/).
    1. Select **Create a resource**.
    1. In the search box, enter **Azure Bot**.
    1. Select **Enter**.
    1. Select **Azure Bot**.

     :::image type="content" source="./assets/images/sbs-messagingextension-action/azure-bot.png" alt-text="select.":::

    1. Select **Create**.
    1. Enter required bot handle name in **Bot handle**.
    1. From the **Subscription** dropdown list, select **msteams.nonprod.pub.msft.aplt**.
    1. From the **Resource group** dropdown list, select your existing resource group. 
    
         :::image type="content" source="./assets/images/sbs-messagingextension-action/create-azure-bot.png" alt-text="create.":::
    
       You can also create a new resource group (select **Create new** > enter resource name > select **OK**).
    
    1. In the **Microsoft App ID** section, by default **Create new Microsoft App ID** is selected. 
    
       You can either select **Use existing app registration** and enter **Existing app ID** and **Existing app password**, or select **Create new Microsoft App ID**.

       > [!NOTE]
       > You can't create more than one bot with the same **Microsoft App ID**.

    1. Select **Review + create**.

        ![Create Microsoft App ID]

         :::image type="content" source="assets/images/sbs-messagingextension-action/create-microsoft-id.png"alt-text="createmicrosoftid.":::
        

    1. If the validation passes, select **Create**. 

        It takes a few moments for your bot service to be provisioned. 

    1. Select **Go to resource**. 

        :::image type="content" source="./assets/images/sbs-messagingextension-action/resource-file.png" alt-text="resource.":::

        Your Azure bot is created.

         :::image type="content" source="./assets/images/sbs-messagingextension-action/created-azure-bot.png" alt-text="created.":::

    **To create client secret**

      Perform the following steps if you have created a new **Microsoft App ID**:

    1. In the left panel, select **Configuration**. 

       > [!TIP]
       > Save the **Microsoft App ID** or **Client ID** for future reference.

    1. Next to **Microsoft App ID**, select **Manage**.

    1. Select **Multitenant** under **App Type**.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/created-azure-bot.png" alt-text="created.":::

    1. In the **Client secrets** section, select **New client secret**. 

        :::image type="content" source="./assets/images/sbs-messagingextension-action/adding-client-secret.png" alt-text="adding client secret.":::

       The **Add a client secret** window appears.  

    1. Enter **Description**.
    
    1. Select **Add**.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/add-a-clientsecret.png" alt-text="enter client id.":::

    1. In the **Value** column, select **Copy to clipboard**.

         :::image type="content" source="./assets/images/sbs-messagingextension-action/clientvalue.png" alt-text="clientvalue.":::

       > [!TIP]
       > Save the **Client secrets** value or app password for future reference.

    **To add the Microsoft Teams channel**

    1. Select **Home**.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/select-home.png" alt-text="select home.":::

    1. Select your bot from **Recent resources**.

    1. Select **Channels** in the left pane. 

    1. Select **Microsoft Teams** <img src="~/assets/images/bots/teamsicon.png" alt="Teams icon" width="20"/>.

    1. Select the checkbox to accept the **Terms of Service**.
    
    1. Select **Agree**.

           :::image type="content" source="./assets/images/sbs-messagingextension-action/agree.png" alt-text="agree.":::

    1. Select **Save**.

           :::image type="content" source="./assets/images/sbs-messagingextension-action/configure-ms.teams.png" alt-text="configuration."::: 
    
    **To create tunnel for local web server**

    Use ngrok to create a tunnel to your locally running web server's publicly available HTTPS endpoints. Run the following command in ngrok:

     ```bash
     ngrok http -host-header=localhost 3978
     ```

    > [!TIP]
    > If you encounter **ERR_NGROK_4018**, follow the steps provided in the command prompt to sign-up and authenticate ngrok. Then run the `ngrok http -host-header=localhost 3978` command.

    **To add messaging endpoint**

    1. From ngrok, copy the HTTPS URL (https to io).

        ![ngrok HTTPS URL](~/assets/images/sbs-messagingextension-action/ngrok1.png)

        > [!NOTE]
        > The HTTPS URL in your ngrok is your fully qualified domain name.
        > The `WebAppDomain` is a fully qualified domain name that does not include `https://` in it.

    1. In **Settings** for the Azure bot that you created, select **Configuration**.

    1. In **Messaging endpoint**, use the HTTPS URL available from ngrok and at the end of the URL add **/api/messages**.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/configuration-file.png" alt-text="configuration file":::

    1. Select **Apply**.

        You have successfully set up a bot in Azure Bot Service.


- title: Update the Azure AD app registration
  durationInMinutes: 1
  content: |
    **To register your app through the Azure AD portal**  

    1. Go to the [Azure portal](https://portal.azure.com/).

    1. Select **Azure Active Directory**.

    1. In the left navigation panel, select **App Registrations**.

    1. Select your bot.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/app.-registerations.png" alt-text="registeration.":::

    1. Under **Manage**, select **Expose an API**.

    1. Select **Set**.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/set-exposeanapi.png" alt-text="Expose an api.":::

    1. Set the **Application ID URI** in the form of `api://{AppID}`.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/set-applicationid.png" alt-text="set.":::

    1. Insert the `WebAppDomain` value between `api://` and `/{AppID}`.</br>
        `api://ae57****.ngrok.io/{AppID}`</br>
        
       The following image shows the domain name:
        
        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

        > [!NOTE]
        > If you're using a tunneling service such as ngrok, ensure you update the value whenever your ngrok subdomain changes.
        > `For example: api://f631****.ngrok.io/92c11075-c629-4a1e-ab58-02b4fd4204c2`, where `f631****.ngrok.io` is the new ngrok subdomain name.

    1. Select **Add a scope**. 

       :::image type="content" source="./assets/images/sbs-messagingextension-action/addascope.png" alt-text="add a scope.":::
    
    1. In the panel that appears, enter `access_as_user` as the **Scope name**.
  
    1. Set **Who can consent?** to `Admins and users`.
  
    1. To configure the admin and user consent prompts with appropriate values for `access_as_user` scope, provide the following information in the fields:</br>
    
         * Enter `Teams can access the user’s profile` as **Admin consent display name**.

         * Enter `Allows Teams to call the app’s web APIs as the current user` as **Admin consent description**.

         * Enter `Teams can access the user profile and make requests on the user’s behalf` as **User consent display name**.

         * Enter `Enable Teams to call this app’s APIs with the same rights as the user` as **User consent description**.
  
    1. Ensure that **State** is set to **Enabled**.
  
    1. Select **Add scope** to save.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

        > [!NOTE]
        > The **Scope name** should match with the **Application ID** URI with `/access_as_user` appended at the end.</br>
           `api://ae57****.ngrok.io/00000000-0000-0000-0000-000000000000/access_as_user`

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::
  
    1. In the **Authorized client applications** section, identify the applications that you want to authorize for your app’s web application. 
    
    1. Select **Add a client application**. 

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Enter **Client ID**: `1fec8e78-bce4-4aaf-ab1b-5451cc387264` for Teams mobile or desktop application. 

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

       You can enter **Client ID**: `5e3ce6c0-2b1f-4285-8d4b-75ee78787346` for Teams web application.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Select **Authorized scopes**.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

       The following image displays the client IDs:

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::
  
    1. In the left panel, select **API Permissions**. 

       > [!NOTE]
       > Users need to consent to these permissions only if the Azure AD app is registered in a different tenant.

    1. Select **Add a permission**.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Select **Microsoft Graph**.

    1. Select **Delegated permissions**.

        By default, **User.Read** is selected.

         :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Add the following permissions:</br>
         * **email**
         * **offline_access**
         * **OpenId**
         * **profile**

    1. Select **Add permissions**.

         :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::
s
    1. From the left panel, select **Authentication** to set a redirect URI. 

       > [!NOTE]
       > If an app is not granted IT admin consent, users must provide consent the first time they use an app.
               
         1. Select **Add a platform**.
         1. Select **Web**.

            :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

         1. Enter the redirect URI for your app by appending `auth-end` to fully qualified domain name:</br> 
           `https://ae57****.ngrok.io/auth-end`. </br>

         1. Enable **Implicit grant and hybrid flows** by selecting the following checkboxes:
             * **ID tokens**
             * **Access tokens**
   
         1. Select **Configure**.

            :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::


- title: Set up app settings and manifest files
  durationInMinutes: 1
  content: |
    1. Navigate to **appsettings.json** in cloned repository.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Open **appsettings.json** in **Visual Studio 2019** and update the following information:  

       * Set `"MicrosoftAppId"` to your bot's **Microsoft App ID**.
       * Set `"MicrosoftAppPassword"` to your bot's client secret ID value.
       * Set `"BaseUrl"` to the fully qualified domain name.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Navigate to **manifest.json** in cloned repository.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Open **manifest.json** in **Visual Studio 2019** and make the following changes:

         * Replace all occurrences of `<<Your_Domain_URL>>` with your fully qualified domain name.
         * Replace all occurrences of `<<Microsoft-App-ID>>` with your bot's **Microsoft App ID**.

        :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

- title: Build and run the service
  durationInMinutes: 1
  content: |
    **To build and run the service using Visual Studio 2019 or Command line**

    # [Visual Studio 2019](#tab/vs2019)

       1. Launch **Visual Studio 2019**.
       1. Navigate to **File** > **Open** > **Project/Solution**.
    
          :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

       1. Select **SidePanel.sln** file from **csharp** folder.

          :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

       1. Press **F5** to run the project.
    
       1. Select **Yes** if the following dialog appears:

         :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

          A webpage opens with a message **Your bot is ready!**.

          :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

        
    # [Command line](#tab/cli)

    Navigate to **samples > meetings-sidepanel > csharp > Side Panel** in a Command Prompt window and enter the following command:

    ```bash
    dotnet run
    ```
   
    :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::
      
- title: Add Meetings SidePanel app to Teams
  durationInMinutes: 1
  content: |
    **To create a Teams meeting and add Meetings SidePanel App**

    1. In your cloned repository, navigate to **csharp > Side Panel > Manifest**.

    1. Create a .zip with the following files that are present in the **Manifest** folder: 
       * manifest.json
       * icon-outline.png
       * icon-color.png

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::
    
    1. Create a meeting with a few presenters and attendees.
   
    1. After the meeting is created, go to the meeting details page and select **Add an app**.

      :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::
   
    1. In the pop-up that opens, select **Manage apps**.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::
   
    1. Select **Upload a custom app**. 

      :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Select **Open** to upload the .zip file that you created in the **Manifest** folder.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Select **Add**.

      :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

       The **Manage apps** section displays the list of applications.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Go to Teams meeting.
    
    1. Select **Add an app**. 
    
       In the app selection page, the app displays as **Side Panel**.
  
       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Select the **Side Panel** app.
    
    1. Select **Save**.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::
   
       The app is visible in the meeting SidePanel.         

- title: Interact with the app in Teams
  durationInMinutes: 1
  content: |
    Let's interact with the app in Teams!

    1. Select **Create Card** command from the compose box command list.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Enter your information in the modal popup.

      :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Select **Submit**.

      :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Select ... overflow menu from an existing message.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Point to **More actions**.

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Select **Share Message**.

      :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::

    1. Select the checkbox if you need to include image and submit.

      :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::


- title: Complete challenge
  durationInMinutes: 1
  content: |
    Did you come up with something like this?

       :::image type="content" source="./assets/images/sbs-messagingextension-action/applicationurl.png" alt-text="application url.":::


- content: |
    You've completed the tutorial to get started with a **Side Panel** app!

