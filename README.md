# EPPC-ALMWS
Repository for the Application Lifecycle Management (ALM) pre-Day workshop at European Power Platform Conference (EPPC)

## Labs
The following describes the labs you will be working on during (or after) the workshop.

### Prerequisites
- Azure DevOps or GitHub Account
- Two Dataverse environments (one for source and one for target)
- App Registration
  - Follow this instruction: https://benediktbergmann.eu/2022/01/04/setup-a-service-principal-in-power-automate/


### Prebuild Pipelines
In the `Pipelines` folder you will find all the pipelines that you will be creating during the workshop.

#### Buid Tools
The pre build pipelines are using the following build tools:

https://marketplace.visualstudio.com/items?itemName=microsoft-IsvExpTools.PowerPlatform-BuildTools

#### Connection
All the pre build pipelines are using App Registrations to connect to the Dataverse environments.
The Pipelines for ADO are using a service connection to connect to the Dataverse environments.
For GH you will need to create environment variables containing the connection information (URL, Client ID, Client Secret, Tenant ID).

#### Folder structure
The pipelines are building on a certain folder structure which is explained in the following link:

https://benediktbergmann.eu/2021/04/27/folder-structure-of-a-dataverse-project/

#### Demo Code
This repo does contain demo code for a Plugin as well as a TypeScript/JavaScript file. For both there are some UnitTests as well.

### Lab 1: Base Pipelines
Create the following pipelines:
- Export Pipeline
- Build Pipeline
- Release Pipeline

You can find the instructions for each pipeline below.

The complete pipelines can be found in the `Pipelines/Base` folder.

#### Export Pipeline
The Export Pipeline will export the solution from the source environment. It should contain the following steps:
- Publish customizations
- Export Solution - Managed
- Export Solution - Unmanaged
- Unpack Solution
- Commit changes

#### Build Pipeline
The Build Pipeline will build the solution. It should contain the following steps:
- Pack Solution
- Publish Artifact

#### Release Pipeline
The Release Pipeline will release the solution to the target environment. It should contain the following steps:
- Download Artifact
- Import Solution

### Lab 2: Advanced Pipelines
Add parameters and conditions to the pipelines.

The complete pipelines can be found in the `Pipelines/Variable-Condition-Loop` folder.

#### SolutionName
The SolutionName should be a variable (in best case in a variable file) that is used in the pipelines.

#### Update or Upgrade
Whether the solution should be updated or upgraded should be a parameter in the Release Pipeline. The parameter should be used in the Import Solution step as a condition.

#### Loop
The Export Pipeline should be able to export multiple solutions. The solutions should be defined in a variable file. The Export Solution step should be in a loop that exports all the solutions.

### Lab 3: Settings
In this lab you should create a settings file that contains the values for Environment Variables and Connection References.

The complete pipelines can be found in the `Pipelines/Settings` folder.

#### Create a settings file
- Download the Solution
- Use PAC CLI to create the settings file

#### Use the settings file
Change the Release Pipeline to use the settings file. This should be done in the Import Solution step.

### Lab 4: Solution Checker
In this lab you should create a pipeline that checks the solution for issues using the Solution checker.

Add a Solution Checker step to the Export Pipeline and use the exported unmanaged solution to check for issues.

The complete pipelines can be found in the `Pipelines/Solution-Checker` folder.

### Lab 5: Mapping
Change both the Eport and Build pipeline to use a mapping file.

Create a mapping file and add it to the unpack and pack steps.

The complete pipelines can be found in the `Pipelines/Mapp` folder.

### Lab 6: Move Data
Change the Release pipeline to import data.

Add a "import data" step at the end of the Relase pipeline.
You could also add an export step in the build pipeline.

The complete pipeline can be found in the `Pipelines/Data` folder

## Good to know
In this section I describe things that are not directly part of the course but still good to know since you could encounter them while going through the labs

### Create Pipeline
We have to navigate to the ADO project where the pipeline should run. There, we open the Pipeline
menu and select the New pipeline button on the upper-right corner of the page.

On the following screen, we select Azure Repos Git.

Thereafter, we select the correct repository to use. Every project has at least one repository that has the 
same name as the project. Often, there is only one per project, but sometimes there are more. Either 
way, we have to select the correct one.

The next step is to select either a template or an existing YAML file as the source or to create a new 
pipeline from scratch (which is what we will do).

To change the name of the file, simply click on the current name and change it.

When the pipeline is saved, it will get the same name as the repository. That means that we have 
to manually change the name to something more useful such as Export from DEV, Build 
Solution, or Release depending on which pipeline we have created. To do this, go to the list of 
all pipelines.

If you hover over the pipeline in question, three dots will appear on the right side. One of the options 
is Rename/Move. In the appearing popup, you can change the name.

### Create Service Connection
To be able to really connect from ADO to our Dataverse environments, we have to add service 
connections. Those will then be used in our pipelines to connect to the correct environments.
You will need the following information to create a connection:
- Dataverse URL: This is the URL you use to access your Dataverse (for example, https://<instancename>.crm4.dynamics.com)
- Tenant ID: Can be found on the overview page of app registration
- Client ID (or application ID): Can be found on the overview page of app registration
- Client secret (or application secret): We created this during app registration

If all the prerequisites are met, you can follow these steps:
1. Go to Project Settings of your ADO project.
2. Select Service Connections (it’s in the Pipelines group).
3. Click on New service connection
4. Select Power Platform from the list
5. Fill in all the needed information, such as the following:
- Server URL
- Tenant ID
- Application ID
- Client secret of the application ID
6. Give the connection a name and save it. That name is what you need to add to the variable files when it comes to the connections.
7. Make sure you have checked the checkbox beside Grant access permission to all pipelines.
8. Click Save

This needs to be done for every environment you’d like to use (usually DEV, Test, and Prod), even if 
the AppReg you are using is the same.

### Authenticate Pipeline for Commit
The export pipeline will push changes to our repository. To be able to do that, it needs certain 
permissions (Contribute should be set to allow) on the repository in question. To grant those, we 
have to do the following:
1. Open Project Settings.
2. Navigate to Repositories
3. Select the repository in question
4. Open the Security tab.
5. Make sure that both the user called Project Collection Build Service and <Project Name> Build Service have Allow for the Contribute permission

The mentioned permissions are repository-wide and will be reused from different pipelines within 
that given repository.

### Environments
The pipeline uses the concept of ADO environments. The idea is to make change tracking easier 
and add the possibility for pre-pipeline execution approvals. In ADO. under Pipelines, there’s an 
Environments menu item. Go there, then click the New environment button.

Here, we should create at least two environments: Test and Prod. Neither of those will have any 
resources since we neither select Kubernetes nor Virtual machines.

#### Approvals for Environments
We should add a pre-pipeline execution approval for your Prod environment. The pipeline is set up 
such that it will only deploy to Prod if the configured approval is met. If you do not add this, the 
pipeline will immediately start deploying to Prod as soon as the deployment to Test is ready.
To add an approval, follow these steps.
Open the environment in ADO and select Approvals and checks from the three-dot menu in the 
upper-right corner.

On the next page, select + in the upper-right corner. In the panel that appears, select Approvals and 
click Next.

Next, select the people that are allowed to approve the deployment. In the Advanced configuration, select 
whether all of the approvers need to approve or just a certain amount (this is only visible if you have 
more than one approver added). Also, add whether an approver is allowed to approve their own run.
We usually select that when only one approver is needed and they can approve their own run.

### Holding solution
A Holding solution is basically a newer version of an existing solution. When you import a solution as a holding solution you will get two versions of your solution in your environment. One of which is suffixed with "_Upgrade".
This can be used to do migrations within your solution before the old one get's deleted.
Upgrades do use holding solutions.

### Perform Update & Upgrade
When importing a solution with the build tools there are two ways of performing an upgrade. Either we check "Import as holding" and perform a "Apply Upgrade" step afterwords or we check the "Import holding and apply" checkbox. The second one will do the two steps in one.

Every other configuration of the import step is doing an update only.


