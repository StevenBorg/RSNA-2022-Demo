# End-to-End RSNA Demo
A journey of a thousand miles... Taking your first steps to the cloud

## Demo Summary
Getting to the cloud shouldn't be difficult, and can be done incrementally, safely, and without major disruptions to existing workflows.

This hands-on lab / demo highlights how an organization with an existing on-prem radiologist infrastructure can take the first steps to intelligently moving their data to the cloud, _without_ disruptions to the current workflow. 

The demo begins inside of a simulated on-prem environment containing a PACS (Orthanc), an X-ray modality (simulated), and a user's personal computer. We then integrate the Azure Health Data Services DICOM service and the medDream zero-footprint viewer, both hosted 100% in the cloud.
- First, we look at Orthanc and as an on-premises PACS system, and we note how the existing modalities are pushing studies directly to the PACS.
- Next, we integrate an intelligent DICOM router (Qvera QIE) into the flow. A router provides logic and customizations to how we choose to deal with the DICOM moving through our system. To integrate the cloud, we configure several new channels inside Qvera QIE: 
    
    1) QIE to Cloud: We create a pathway for Qvera to receive DICOM DIMSE requests and pass those to the Azure DICOM service. 
    2) QIE to Orthanc: We also create a pathway for connunication to and from Orthanc.
    3) Modalities to QIE: Finally, we create a channel that can accept DICOM messages directly from the modalities. Any DICOM sent here will be copied to both Orthanc and the Azure DICOM service.

- We now have a system where the on-prem PACS can continue to be used as it always has, but new data flowing from the modalities is copied to both the on-premises PACS and the cloud. This unlocks several benefits, summarized below. _TODO_
- To demonstrate the push to both on-prem PACS and the cloud, we 'run' our modality again. This time the resulting studies appear in both locations, which we verify.
- Next we migrate the oldest DICOM files from on-prem to the cloud, keeping the local PACS light and performant. 
- We now verify that, using our PACS workstation, we can get immediate access to priors stored in the Azure DICOM service. (For an advanced scenario, we can also configure Qvera QIE to automatically retrieve prior imaging from the cloud to the PACS.)
- Finally, we validate two important capabilities of this solution.

   1) If internet connectivity is lost at the hospital system, we can still use the Orthanc PACS system without issues, and
   2) If our on-prem infrastructure suffers an outage or falls victim to a ransomware attack, we can immediately shift to our cloud-hosted viewer, backed by DICOM stored directly in Azure.

## The Setup
There are a few core steps that need to be accomplished prior to deploying the infrastructure.

### App Registration
To allow Azure Active Directory to be used as an identity provider to secure access to the DICOM service, we must create an App Registration. By creating an App Registration we are telling Azure AD that we will have an application that will be speaking to our DICOM service and needs to use Azure AD. In our case, the App Registration will be re-used by two different applications: Qvera QIE and the Softneta medDream viewer. In a production environment, you'll likely want two different app registrations so access can be fine-tuned by application.

To create an App Registration, follow the steps here: [Add App Registration](add-app-reg.md)

You should now have several key pieces of data which will be used in the upcoming steps:
- Application (client) ID
- Directory (tenant) ID
- Client secret
- Principal Object ID of the App Registration

### Deploying the infrastructure
Creating the cloud and on-prem environments in Azure can be done in one of two ways. You can use the Bicep command line, which is easiest if you are running this repo multiple times for development. Or you can use the Azure Portal user experience, which is easiest for a one-off deployment. Both create identical environments.

#### Deploy using the Azure Portal
To use reasonable default values, simply click [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FStevenBorg%2FRSNA-2022-Demo%2Fmain%2Fenvironments%2Fall-up-demo-deployment.json)

Fill in required information in the template: 
![Steps to deploy using the Portal](readme-images/steps-deploy-full-infra-using-portal.png "Steps to deploy using the Portal")
1. Select an Azure subscription.
2. Create a new Resource Group.  Creating a new resource group makes it easy to delete the entire group when you are done with the demo environment.  
3. Select an Azure region.
4. Choose a complex administrator password that will be used in key places in the demo script.
5. Provide a user login name (_student_ is used in the demo script).
6. Provide a globally unique name for the AHDS workspace (which will be part of the DICOM URL).
7. Provide a DICOM service name (which need only be locally unique, but cannot be simply 'dicom').
8. Enter the Tenant ID you captured earlier.
9. Enter the App Registration ID you captured earlier.
10. Enter the App Client Secret you captured earlier.
11. Enter the Principal Object ID of the App Registration you captured earlier.
12. Click the `Review and Create` button.

### Retrieving a Qvera QIE license
The Qvera QIE DICOM router requires a license key to run. A 90-day trial license is easily available at https://www.qvera.com/hl7-interface-engine/#get-started-section. Scroll down to click the **Start a Free 90-day Trial** button, enter some information, and a key will be emailed immediately to your account.

## Running the Demo
Check out the [demo instructions](demo-instructions.md) for detailed steps to configure and try out the environment.  
