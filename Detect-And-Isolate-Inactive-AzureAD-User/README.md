This Power Automate has its sole purpose to block inactive Azure AD users (users that didn't log-in for last 90 days) – please note that users synced from AD to Azure AD will not be blocked using this flow. For those users we need to use Azure automation and PowerShell. Also this Power Automate will block only Members and not Guests in your environment. 

You will need Power Automate Premium license - if you don't have one you can buy it, start free trila, or to use flow that is only detecting inactive users.

First, we need to register app in Azure AD so that we can use REST API in Power Automate and to have needed permissions. Go to Azure AD portal (**aad.portal.azure.com**), click on **Azure Active Directory > App registrations > +New registration**. Enter the name and click on **Register**.

Once it’s registered, open the app and click on **API permissions > +Add a permission > Microsoft Graph > Application permissions**. One by one add these 3 permissions:

**AuditLog.Read.All**

**Organization.Read.All**

**User.Read.All**

After you add all 3, click on **Grant admin consent for {name of your organization}**.
As a next step, click on **Certificates & secrets > +New client secret**. Enter description and choose for how long secret will be active for this application and click **Add**. Copy **Value** field (Secret) which we will need later – note that once you leave this page, you’ll not be able to see secret again!
Click on **Overview** and copy **Application (client) ID** and **Directory (tenant) ID**.
We will need Secret, Application ID, and Tenant ID for our Power Automate.

Download template from this site (DetectAndIsolateInactiveAzureADUser.zip).
Now, we go to Power Platform portal (flow.microsoft.com). Choose **My flows** and then click on **Import**. Click on **Upload** and choose a template where you had download it and click on Open. In **Related resources**, click on **Select during import** and click on **+Create new** if you don’t have connection already created. If you have just choose already available connection. If you click on create new, you’ll be redirected to new site where we will be creating new connections needed. 	

Click on **+New connection**, find **Azure AD** (search on right side) and click on **+ (Create) ** sign on right side in line with Azure AD. Click on **Create** and login as your admin user. Repeat steps for Office 365 Outlook** and **Microsoft Teams**. 

Once created go to previous page where you imported template and choose created connections for each of steps, Azure AD, Outlook, and Teams. Once connected, click on **Import**. 

Once flow is imported, click on **My flows** and open flow we just created **(Isolate inactive Azure AD User)**. Click on **Edit** so that we are sure all connections are OK. We also need to change values of 3 variables – expand **Tenant ID Variable** and add Value that we got by creating app in Azure AD. Do the same for variables **Application-Client ID Variable** and **App Secret Variable**. 

Now expand fields to get to the Outlook and Teams connectors.

Expand **Outlook** and in **To: ** change to user/group you want to notify when user is blocked. 

Expand **Teams** and remove **Team** and **Channel** fields and choose Team where you want to post a message as well as a Channel from that Team. 

Click on **Save**.

Click on backward arrow next to the name of flow, and once you return to Flow page choose **Turn on** form upper menu.

Now click on **Run** and then on **Run flow** and on **Done**. If it’s all good, we will get succeed info. If there is any error, we will get Failed and we need to analyse where the error is. Note that you’ll get failed error message also if there are user who are admins and they are inactive for specified time.

**_One note for the end_ is that if you have _inactive admin user, that user will not be blocked_, and you will be getting Failed status (users that are not admins will be blocked). But if you have inactive admin user for 90 days, failed Power Automate is least of your problems. Check your failed status and on which users it failed, and you can go to Azure AD admin portal and manually block user.**

If you want to block users that didn't log-in for 30 days or any other number of days, please change **Interval** in **Get past time** step of the Power Automate.

If you want to block only Guest or Guest and Members then you need to change first condition in **Condition** step - **userType > is equal to > Member**. For Guests change Member with **Guest**. If you want to block Members and Guests then you need to delete this condition by clicking on 3 dots and choosing Delete.