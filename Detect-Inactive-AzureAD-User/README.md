This Power Automate has its sole purpose to **detect** inactive Azure AD users (users that didn't log-in for last 90 days).

First, we need to register app in Azure AD so that we can use REST API in Power Automate and to have needed permissions. Go to Azure AD portal (**aad.portal.azure.com**), click on **Azure Active Directory > App registrations > +New registration**. Enter the name and click on **Register**.

Once it’s registered, open the app and click on **API permissions > +Add a permission > Microsoft Graph > Application permissions**. One by one add these 3 permissions:

**AuditLog.Read.All**

**Organization.Read.All**

**User.Read.All**

After you add all 3, click on **Grant admin consent for {name of your organization}**.
As a next step, click on **Certificates & secrets > +New client secret**. Enter description and choose for how long secret will be active for this application and click **Add**. Copy **Value** field (Secret) which we will need later – note that once you leave this page, you’ll not be able to see secret again!
Click on **Overview** and copy **Application (client) ID** and **Directory (tenant) ID**.
We will need Secret, Application ID, and Tenant ID for our Power Automate.

Download template from this site (DetectInactiveAzureADUser.zip). - https://github.com/BenjiSec/Security-and-Power-Automate/blob/main/Detect-Inactive-AzureAD-User/DetectInactiveAzureADUser.zip

Now, we go to Power Platform portal (flow.microsoft.com). Choose **My flows** and then click on **Import**. Click on **Upload** and choose a template where you had download it and click on Open. In **Related resources**, click on **Select during import** and click on **+Create new** if you don’t have connection already created. If you have just choose already available connection. If you click on create new, you’ll be redirected to new site where we will be creating new connections needed.  

Click on **+New connection**, find **Office 365 Outlook** (search on right side) and click on **+ (Create) ** sign on right side in line with Office 365 Outlook. Click on **Create** and login as your admin user. Repeat steps for **Microsoft Teams**. 

Once created go to previous page where you imported template and choose created connections for each of steps Office 365 Outlook and Microsoft Teams. Once connected, click on **Import**. 

Once flow is imported, click on **My flows** and open flow we just created **(Detect Inactive Azure AD User)**. Click on **Edit** so that we are sure all connections are OK. We also need to change values of 3 variables – expand **Tenant ID Variable** and add Value that we got by creating app in Azure AD. Do the same for variables **Application-Client ID Variable** and **App Secret Variable**. 

Now expand fields to get to the Outlook and Teams connectors.

Expand **Outlook** and in **To: ** change to user/group you want to notify when user is blocked. 

Expand **Teams** and remove **Team** and **Channel** fields and choose Team where you want to post a message as well as a Channel from that Team. 

Click on **Save**.

Click on backward arrow next to the name of flow, and once you return to Flow page choose **Turn on** form upper menu. As soon as you enable flow, it will run in background.

If you want to run it again, click on **Run** and then on **Run flow** and on **Done**. If it’s all good, we will get succeed info. 

If you want to detect users that didn't log-in for 30 days or any other number of days, please change **Interval** in **Get past time** step of the Power Automate.

This Power Automate will detect Members and Guests. If you want to detect only Guest or only Members then you need to add condition in **Condition** step - **userType > is equal to > Member**. For Guests change Member with **Guest**. 

If you want to block users, you can do it manually (instructions are added in email notification) or to use Detect and Block Inactive Azure AD User flow - https://github.com/BenjiSec/Security-and-Power-Automate/tree/main/Detect-And-Isolate-Inactive-AzureAD-User
