# COVID-19 Office Entry App Setup Manual

**Date: August 25, 2020**

**Version: 1.0**

**Author: Solutions Centre, Transport Canada**

**Contact(s): rabih.yassine@tc.gc.ca**


## SharePoint Lists Setup

The solution uses PowerShell scripting and incorporates the SharePoint Patterns and Practices (PnP) library to populate the SharePoint environment. The first time you setup the SharePoint environment, you might first need to adjust your PowerShell environment, as described below.

### PowerShell and PnP Library Setup

1. Open a PowerShell window while logged in with an administrator account. This will require going to the folder that contains the executable for PowerShell (likely %SystemRoot%\system32\WindowsPowerShell\v1.0\), holding Control-Shift, and right-clicking to select &quot;Run as different user&quot;. Enter the administrator account credentials. Or, if you are already logged in as an administrator, you can just right-click and select &quot;Run as Administrator&quot;.
2. Run the command **Get-ExecutionPolicy**
3. If the current policy is not set to &quot;Unrestricted&quot;, run the command **Set-ExecutionPolicy Unrestricted**. \*\* Note: for safety, it is advised to switch the policy back to what it previously was, if it was not Unrestricted already, after you are done all of the required tasks in PowerShell.
4. Run this command to change the security protocol of your current session to TLS1.2: **[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**
5. Install the PnP Library (if it has never been installed before) by running this command: **Install-Module SharePointPnPPowerShellOnline**
6. Run the command **$ExecutionContext.SessionState.LanguageMode**, to see if you are in Full language mode or constrained language mode.
7. If you are in constrained language mode, you will need to switch it to full by creating an environment variable called **\_PSLockDownPolicy** and giving it a value of 0, or changing its value to 0 if it already exists (if you are in constrained language mode, this variable likely has a value of 4). This can be done by doing observing the steps in this screenshot:

![](images/EnvironmentVariables-en.png)

\*\* Note: for safety, it is advised to switch the policy back to constrained language mode (i.e. changing the variable back to 4) if it was previously set to that mode, after you are done all of the required tasks in PowerShell.

### SharePoint List Templates Creation

1. Close any PowerShell window that may be open and open a new one as an administrator, as per Step 1 of the previous section.
2. Run the command **./OfficeEntry-GetListSchemas** (note: you must be in the same directory as the script and the XML file when you run this.). If you have not yet logged into Office 365 during this session, you will be prompted with a pop-up to login to Office 365 in the usual way.
3. Open file **OfficeEntry-ApplyListSchemas.ps1** and update the variables **$SiteURL** with your target SharePoint site.
4. Note that weird results may be expected if lists of the same name already exist in the SharePoint site. It is recommended to create a new, empty, SharePoint subsite for the lists that will be used by this app. To create a new subsite:
    - select **Site contents** on the left hand side of the SharePoint screen, then Select **New --> Subsite**
    - At minimum, provide a suitable **Title** and **Web Site Address**. The Template selection can be left as the default: Team site (no Office 365 group)
5. Run the command **./OfficeEntry-ApplyListSchemas** (note: you must be in the same directory as the script and the XML file when you run this.)
6. The new lists will then be created on the target SharePoint site.
7. Configure the permissions of the SharePoint site to permit users in the organization to have access to the new lists, for example, by creating a new group containing all desired users. The permissions for the group could be: &quot;Users will only be able to add, update, and view items from a remote interface.&quot;
8. If interested, refer to file **OfficeEntry-GetListSchemas.ps1** to see how the XML file was generated. This may come in handy if you wish to create a copy of the list templates on your own site to migrate to a different environment.

### Populating SharePoint Lists

No lists need to be pre-populated in order to begin using the app, however you may wish to copy data from one environment to another in your development cycle. Sample scripts have been provided to show how to do this. Refer to **Building-GetListData.ps1** and **Building-ApplyListData.ps1** for scripts that will work with the **Building** list; scripts for other tables can be created in the same fashion.

## Power Apps Setup

### Installation

1. Navigate to the main page for Power Apps. This can be accessed at [https://make.powerapps.com](https://make.powerapps.com/). You will need to login with your Office 365 credentials if you have not already done so. Toggle to your desired environment at the top right of the window if required.
2. On the left hand menu, click **Apps**.
3. At the top menu of the webpage, select **Import canvas app**.
4. Click **Upload**, navigate to the folder that contains the saved ZIP file for the app and select it. It will upload. Once complete, click **Import**.
5. For **Review Package content**, if desired, select the wrench and change the app name to the desired name.
6. Select **Import**.

### App ID Configuration

1. On the main Apps page, click the … beside the imported app and click **Details**.
2. Copy the App ID that appears in the Details.
3. Back on the main Apps page, click the … beside the imported app and click **Edit**.
4. On the editing page, on the left hand side, click the three squares stacked on each other to bring up the **Tree View**.
5. For **Screens**, click **App** (the first item), then in the middle of the screen above the app view, refer to the code window beside the **fx** icon.
6. On the right hand side of the code window, click the down indicator to expand the window.
7. Find the section of the code that refers to **\_appID**. In the quotations, remove the text and paste the App ID that was copied in an earlier step.

###

### Data Source Linkages

All data sources have been stripped from the app prior to sharing it. Several data sources will therefore need to be added to the app as follows:

1. On the main Apps page, click the … beside the imported app and click **Edit**.
2. On the editing page, on the left hand side, click the cylinder to bring up the Data Sources menu.
3. Expand the **Connectors** submenu
4. Select **Office 365 Outlook** and then **Add a connection**. Then **Connect**.
5. Select **Office 365 Users** and then **Add a connection**. Then **Connect**.
6. Select **SharePoint** and then **Add a connection**. Ensure radio button is on **Connect directly (butt services)** and then click **Connect**. Enter the URL of the SharePoint site that contains all the lists you created, then click **Connect**. Select these lists that you previously created: AccessRequest, Building, Floor, UserSetting, VisitorLog; then click **Connect**.
7. Select **Power Apps Notification** and then **Add a connection**. For the target application, enter the App ID from the previous section, then click **Connect**.

###

### Changes to field names

The creation of the SharePoint lists maintains the default "Title" field name for the key field

Rename the following fields in the SharePoint lists:

ApplicationUser:
Change Title to msft_userid
Elevator Access:
Change Title to ElevatorAccessID
Building Floor:
Change Title to BuildingFloorText
Elevator Bank Capacity:
Change Title to BuildingID
Building:
Change Title to Text
