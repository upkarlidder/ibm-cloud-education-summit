# Build three serverless applications with Apache OpenWhisk!

- Step 0: Sign up for IBM Cloud
- Step 1: Configure your local development environment
- Step 2: Create Serverless Actions and APIs
- Step 3: Use the IBM Cloud UI to create Serverless Functions
- Step 4: Create a Serverless Visual Recognition Service
- Step 5: (Stretch goal) Create a Weather Bot

# Step 0: Sign up for IBM Cloud
- [IBM Cloud Sign Up](http://bit.ly/ibm-cloud-summit-2019) - http://bit.ly/ibm-cloud-summit-2019

**Q: I'm having issues signing up for IBM Cloud during the workshop.**

**A:** This may be due to the large number of attendees signing up on the same network. Try visiting your registration link on your phone (not connected to Wi-fi) and then logging in on your laptop.

**Q: What if I already have an IBM Cloud account?**

**A:** Visit http://bit.ly/ibm-cloud-summit-2019 and log in to your account.

# Step 1: Configure your IBM Cloud local environment

### Install IBM Cloud CLI

1. Open the [IBM Cloud Docs](https://console.bluemix.net/docs/) page.
2. Open the *"[IBM Cloud Developer Tools (CLI)](https://console.bluemix.net/docs/cli/index.html#overview)"* link from the *"IBM Cloud"* section.
3. Click on the *"[Download and install IBM Cloud CLI](https://console.bluemix.net/docs/cli/reference/bluemix_cli/download_cli.html#download_install)"* link under *"HOW TO"* section in the left-hand menu.
4. Follow the steps listed under the *["Install from shell"](https://cloud.ibm.com/docs/cli/reference/bluemix_cli?topic=cloud-cli-install-ibmcloud-cli#shell_install)*  section to download and install the IBM Cloud CLI.

- MacOS: `curl -fsSL https://clis.cloud.ibm.com/install/osx | sh`
- Linux: `curl -fsSL https://clis.cloud.ibm.com/install/linux | sh`
- Windows (Powershell): `iex(New-Object Net.WebClient).DownloadString('https://clis.cloud.ibm.com/install/powershell')')`

### Log Into IBM Cloud CLI

1. Use this command to authenticate the IBM Cloud CLI with your account credentials.

   ```
   $ ibmcloud login
   ```

2. Choose an API endpoint from the list.
   ***IBM Cloud Functions is available in the following regions: `eu-de`, `eu-gb` and `us-south`. Lite account users must choose their default account region.***

   ```
   Select an API endpoint:
   1. eu-de - https://api.eu-de.cloud.ibm.com
   2. au-syd - https://api.au-syd.cloud.ibm.com
   3. us-east - https://api.us-east.cloud.ibm.com
   4. us-south - https://api.ng.cloud.ibm.com
   5. eu-gb - https://api.eu-gb.cloud.ibm.com
   6. Enter a different API endpoint
   Enter a number> 
   ```

3. Enter account credentials for your IBM Cloud account.

   ```
   Email> user@email.com

   Password>
   Authenticating...
   OK

   Select an account (or press enter to skip):
   1. John Smith's Account (xxx)
   Enter a number>

   API endpoint:     https://api.eu-gb.bluemix.net (API version: 2.92.0)
   Region:           eu-gb
   User:             user@email.com
   Account:          No account targeted, use 'bx target -c ACCOUNT_ID'
   Resource group:   No resource group targeted, use 'bx target -g RESOURCE_GROUP'
   Org:
   Space:

   ```

4. Run the following command to configure the organisation and space the CLI is targeting.

   ```
   $ ibmcloud target --cf
   Targeted org user@email.com
   Targeted space dev

   API endpoint:     https://api.eu-gb.bluemix.net (API version: 2.92.0)
   Region:           eu-gb
   User:             user@email.com
   Account:          No account targeted, use 'bx target -c ACCOUNT_ID'
   Resource group:   No resource group targeted, use 'bx target -g RESOURCE_GROUP'
   Org:              user@email.com
   Space:            dev
   ```

### Install IBM Cloud Functions CLI plugin

1. Use this command to install the Cloud Functions plugin for the IBM Cloud CLI.

   ```
   $ ibmcloud plugin install cloud-functions
   Looking up 'cloud-functions' from repository 'Bluemix'...
   Plug-in 'cloud-functions 1.0.7' found in repository 'Bluemix'
   Attempting to download the binary file...
    11.13 MiB / 11.13 MiB [=================================================================================] 100.00% 9s
   11665633 bytes downloaded
   Installing binary...
   OK
   Plug-in 'cloud-functions 1.0.7' was successfully installed into /home/user/.bluemix/plugins/cloud-functions.
   ```

*This plugin provides the [Apache OpenWhisk CLI](https://github.com/apache/incubator-openwhisk/blob/master/docs/cli.md) as a sub-command under the IBM Cloud CLI. Platform credentials are provided automatically by the IBM Cloud CLI.*

### Test IBM Cloud Functions From The CLI

1. Run the following command to invoke a test function from the command-line.

   ```
   $ ibmcloud wsk action invoke whisk.system/utils/echo -p message hello --result
   {
       "message": "hello"
   }
   ```

*If this command executes successfully, you have verified that the IBM Cloud CLI and Cloud Functions plugin have been installed and configured correctly. If this does not work, please contact the workshop organiser to provide assistance!*

# Step 2: Create Serverless Actions and APIs

This exercise will introduce the [IBM Cloud Functions Web UI](https://console.bluemix.net/openwhisk/). This application helps to manage your IBM Cloud Functions applications from a web browser, rather than using the command-line.

*Once you have completed this exercise, you will have…*

- **Understood how to find and use the IBM Cloud Functions Web UI.**

Once this exercise is finished, you will be able to use the web ui to build and manage serverless applications on IBM Cloud!

## Table Of Contents

* [Background](#background)
* [Navigating To The IBM Cloud Functions Homepage](#navigating-to-the-ibm-cloud-functions-homepage)
* [Actions](#actions)
  * [Details Overview](#details-overview)
  * [Invoking Actions](#invoking-actions)
  * [Creating Actions](#creating-actions)
* [Triggers](#triggers)
  - [Details Overview](#details-overview)
  - [Creating Triggers](#creating-triggers)
* [Monitoring](#monitoring)
* [APIs](#apis)
  - [Details Overview](#details-overview)
  - [Creating APIs](#creating-apis)

## Instructions

### Background

IBM Cloud Functions comes with a Web UI to help developers manage their serverless applications. Common development tasks such as creating actions, monitoring invocations, setting up triggers and more can all be achieved using this web application. The web application is custom to IBM Cloud and not part of the open-source Apache OpenWhisk project.

It can often be quicker to use the Web UI for certain development tasks, rather than typing repetitive CLI commands. The web UI integrates with the IBM Cloud interface, making it easy to provision and connect new cloud services to your applications.

In this exercise, we'll show you the different features of the IBM Cloud Web UI….

### Navigating To The IBM Cloud Functions Homepage

1. Open the [IBM Cloud homepage](https://console.bluemix.net).

2. Click to show the menu on the left-hand side.

3. Click "Functions" in the list to open the [IBM Cloud Functions homepage](https://console.bluemix.net/openwhisk/).

### Actions

1. Select "Actions" from the left-hand menu panel on the homepage.

[This page](https://console.bluemix.net/openwhisk/actions) is the management page for actions. It shows actions within the chosen region, org and space.

2. Select an action from the page to move to the action details page.

#### Details Overview

The action details page will show properties for the chosen action.

For supported runtimes, action source code is shown in an editor which allows users to make changes live. 

Using the menu on the left-hand side, different properties for the action can be accessed and modified.

- *"Code"* - shows action source code in editor.
- *"Parameters"* - shows default parameters for the action.
- *"Runtime"* - shows the action runtime, timeout value and memory limit.
- *"Endpoints"* - allows you to expose the action as web action.
- *"Connected Triggers"* - shows the triggers action is connected to.
- *"Enclosing Sequences"* - shows sequences which use this action.

#### Invoking Actions

1. Click the "Invoke" button to invoke an action and display the resulting activation record.

*Input parameters to invocations can be modified using the "Change Input" button.*

#### Creating Actions

From the [action overview page](https://console.bluemix.net/openwhisk/actions), new actions can be created by providing the source code through the browser-based editor.

1. Select the "Create" button from the page.
2. Choose "Create Action" from the list.
3. Fill in the "Action name" and choose the "Runtime".
4. Click "Create"
5. Fill in the editor with your action source code.

### Triggers

1. Select "Triggers" from the left-hand menu panel on the homepage.

[This page](https://console.bluemix.net/openwhisk/triggers) is the management page for triggers. It shows triggers within the chosen region, org and space.

2. Select a trigger from the page to move to the trigger details page.

#### Details Overview

The trigger details page will show properties for the chosen trigger.

Using the menu on the left-hand side, different properties for the trigger can be accessed and modified.

- *"Connected Actions"* - shows the actions this trigger is connected to.
- *"Parameters"* - shows default parameters for the action.
- *"Endpoints"* - show details on how to fire this trigger remotely.

#### Creating Triggers

From the [trigger overview page](https://console.bluemix.net/openwhisk/triggers), new triggers can be created.

1. Select the "Create" button from the page.
2. Choose "Create Trigger" from the list.
3. Choose "Trigger type" as "Custom Trigger"
4. Fill in "Trigger Name" and "Description"
5. Click "Create"

### Monitoring

IBM Cloud Functions Web UI comes with a [comprehensive visualisation dashboard](https://console.bluemix.net/openwhisk/dashboard) for monitoring serverless applications.

This dashboard shows activations within a region, org and space. Developers can see activation results, invocation times and logging output through the dashboard. Activations displayed can be filtered by name or time window.

### APIs

HTTP endpoints for web actions can be created and managed through the IBM Cloud Functions Web UI. Using this interface is often a lot more intuitive than using the CLI tool for managing more complex APIs.

1. Select "APIs" from the left-hand menu panel on the homepage.

#### Details Overview

The API details page will show properties for the chosen API, including an API monitoring page showing invocations.

Using the menu on the left-hand side, different properties for the API can be accessed and modified.

- *"Summary"* - API overview page and monitoring dashboard for API invocations.
- *"Definition"* - API configuration properties, allows updating properties live.
- *"Sharing"* - Configure exposing API with our internal users.
- *"API Explorer"* - API documentation for your endpoints.

#### Creating APIs

1. Click the "Create API" from the [APIs homepage](https://console.bluemix.net/openwhisk/apimanagement).

In this page, API details can either be filled out manually or imported from an existing Swagger file.

2. Fill out the "API name" field as "myapi"
3. Fill out the "Base path for API" field as "/api"


4. Click on the "Create Operation" button to add new HTTP endpoints to this API.
5. Fill out the "Path" field as "/hello"
6. Choose an action from the drop-down list.
7. Click the "Create" button.

Using the API management create page, security, rate limiting, CORS or oauth support can be configured.

8. Click the "Save" button to create your API.

# Step 3: This exercise will explain how to use sequences to compose new "meta-actions" from existing actions on IBM Cloud Functions.

*Once you have completed this exercise, you will have…*

- **Understood what action sequences do and how they work.**
- **Created, deployed and invoked a sample action sequence.**
- **Used deliberate errors to stop processing the action sequence.**

Once this exercise is finished, we will be able to use action sequences on IBM Cloud Functions!

## Table Of Contents

* [Background](#background)
* [Action Sequences](#action-sequences)
  * [Creating Sequence Actions](#creating-sequence-actions)
  * [Handling Errors](#handling-errors)
* [EXERCISES](#exercises)

## Instructions

### Background

Developers often want to build actions as reusable components which can be combined to build "higher-order" serverless functions.

For example, what if you have serverless functions to implement an external API and want to enforce HTTP authentication? Rather than manually replicating the same authentication code across all action handlers, you can build an authentication action which can be invoked programmatically at runtime.

Unfortunately, this approach does incur additional charges. The application will be charged twice when the authentication function is called, as the calling action has to sit idle waiting for the response from the authentication function.

*OpenWhisk has a special type of action which resolves this problem...*

### Action Sequences

OpenWhisk supports a kind of action called a "sequence". Sequence actions are created using a list of existing actions. When the sequence action is invoked, each action in executed in order of the action parameter list. Input parameters are passed to the first action in the sequence. Output from each function in the sequence is passed as the input to the next function and so on. The output from the last action in the sequence is returned as the response result.

Here's an example of defining a sequence (`my_sequence`) which will invoke three actions (`a, b, c`).

```
$ ibmcloud wsk action create my_sequence --sequence a,b,c
```

*Sequences behave like normal actions, you create, invoke and manage them as normal through the CLI.*

Using a sequence can remove the need to manually invoke actions and sit idle waiting for a response. In the example above, a sequence would be created for each serverless function in the application, combing the action doing the authentication followed by the actual request processing action.

Let's look at an example of using sequences.  

#### Node.js

1. Create the file (`funcs.js`) with the following contents:

```javascript
function split(params) {
  var text = params.text || ""
  var words = text.split(' ')
  return { words: words }
}

function reverse(params) {
  var words = params.words || []
  var reversed = words.map(word => word.split("").reverse().join(""))
  return { words: reversed }
}

function join(params) {
  var words = params.words || []
  var text = words.join(' ')
  return { text: text }
}
```

1. Create the following three actions

```
$ ibmcloud wsk action create split funcs.js --main split
$ ibmcloud wsk action create reverse funcs.js --main reverse
$ ibmcloud wsk action create join funcs.js --main join
```

#### Creating Sequence Actions

1. Test each action to verify it is working

```
$ ibmcloud wsk action invoke split --result --param text "Hello world"
{
    "words": [
        "Hello",
        "world"
    ]
}
$ ibmcloud wsk action invoke reverse --result --param words '["hello", "world"]'
{
    "words": [
        "olleh",
        "dlrow"
    ]
}
$ ibmcloud wsk action invoke join --result --param words '["hello", "world"]'
{
    "text": "hello world"
}
```

2. Create the following action sequence.

```
$ ibmcloud wsk action create reverse_words --sequence split,reverse,join
```

3. Test out the action sequence.

```
$ ibmcloud wsk action invoke reverse_words --result --param text "hello world"
{
    "text": "olleh dlrow"
}
```

Using sequences is a great way to develop re-usable action components that can be joined together into "high-order" actions to create serverless applications. 

#### Handling Errors

What if you want to stop processing functions in a sequence? This might be due to an application error or because the pre-conditions to continue processing have not been met. In the authentication example above, we only want to proceed if the authentication check passes.

If any action within the sequences returns an error, the platform returns immediately. The action error is returned as the response. No further actions within the sequence will be invoked.

Let's look at how this work...

##### Node.js

1. Create the file (`funcs.js`) with the following contents:

```javascript
function fail (params) {
  if (params.fail) {
      throw new Error("stopping sequence and returning.")
  }
    
  return params  
}

function end (params) {  
  return { message: "sequence finished." }
}
```

2. Create the following three actions

```
$ ibmcloud wsk action create fail funcs.js --main fail
$ ibmcloud wsk action create end funcs.js --main end
$ ibmcloud wsk action create example --sequence fail,end
```

3. Test out the action sequence without `fail` parameter.

```
$ ibmcloud wsk action invoke example -r
{
    "message": "sequence finished."
}
```

4. Test out the action sequence with `fail` parameter.

```
$ ibmcloud wsk action invoke example -r -p fail true
{
    "error": "An error has occurred: Error: stopping sequence and returning."
}
```



🎉🎉🎉 **Sequences are an "advanced" OpenWhisk technique. Congratulations for getting this far! Now let's move on to something all together different, connecting functions to external event sources…** 🎉🎉🎉

### EXERCISES

Let's try out your new SERVERLESS SUPERPOWERS 💪 to build a real serverless function. 

***Can you use sequence error handling to add authentication to the existing `reverse_words` serverless  function?***

#### Input

The new `reverse_words_with_password` sequence action should take two parameters, `password` and `text`. `password` is authentication value, `text` is the sentence to reverse words in.

#### Output

Return the JSON from the existing `reverse_words` action.

```json
{
  "text": "olleh dlrow"
}
```

If the `password` parameter does not match an expected value (`mysecret`), return an error.

#### Resources

Create a new action to handle checking the password. Join this with the `reverse_words` action using a new sequence.

#### Tests

Test with correct password.

```
$ ibmcloud wsk action invoke reverse_words_with_password -r -p password "mysecret" -p text "hello world"
{
    "text": "olleh dlrow"
}
```

Test with incorrect password.

```
$ ibmcloud wsk action invoke reverse_words_with_password -r -p text "hello world"
{
    "error": "An error has occurred: Error: Password incorrect."
}
```

# Step 4: Create a Visual Recognition Service

This workshop is taken from an [IBM Code Pattern linked here](https://developer.ibm.com/patterns/serverless-cloud-object-storage-image-processing/).

In this application, you upload an image to a web application that is stored in IBM Cloud Object Storage, which triggers your serverless functions to run. Those functions perform some image processing and analysis, such as charcoaling the image and running visual recognition on it. After the analysis and processing is done, the results are stored in a different Cloud Object Storage bucket, which can then be read.


## Architecture
   ![](images/architecture.png)

## Instructions
### Prerequisites
1. An [IBM Cloud Account](https://cloud.ibm.com/register)
1. An [IBM Cloud CLI](https://cloud.ibm.com/docs/cli/reference/ibmcloud?topic=cloud-cli-install-ibmcloud-cli#install_use) with the IBM Cloud Functions [Plugin](https://cloud.ibm.com/docs/openwhisk?topic=cloud-functions-cli_install) installed.

### Create Required Services on IBM Cloud
To run this application, you'll need to set up IBM Object Storage and the IBM Visual Recognition Service on IBM Cloud
1. Create a Cloud Object Storage Service Instance:
    * From the catalog select [Object Storage](https://cloud.ibm.com/catalog/services/cloud-object-storage).
    * Give your service a name, and click `Create`.
    * In the left side menu, select `Buckets`, and then `Create bucket`.
    * Give your bucket a unique name. 
    * For Resiliency, select `Cross Region`, and for Location, select `us-geo`. *Note: you can select anything here, we'll just use cross region us-geo as our example throughout*
    * Click `Create Bucket`.
    * Create another bucket, with the same name suffixed by `-processed`. If your original bucket was `my-bucket`, then your new bucket will be `my-bucket-processed`.
    * Again, ensure that you selected the same region as your first bucket, in this case `Cross Region` and `us-geo`.
    * In the left side menu, click `Service Credentials`. Click `New Credential`.
    * Check the checkbox for `Include HMAC Credential`. Click `Add`.

1. Create a Visual Recognition Service Instance
    * From the catalog select [Visual Recognition](https://cloud.ibm.com/catalog/services/visual-recognition)
    * Give your service a name, and click `Create`.
    * In the left side menu, click `Service Credentials`. If there are no service credentials created, click `New Credential`. Once your Service Credentials are created, make note of your `apikey`.

### Login and set up your IBM Cloud CLI with Functions plugin
1. Login to the IBM Cloud CLI:
    ```
    ibmcloud login
    ```

1. List the namespaces available in IBM Cloud Functions:
    ```
    ibmcloud fn namespace list
    ```

1. Set your namespace using the ID found in the previous step:
    ```
    ibmcloud fn property set --namespace <namespace_id>
    ```

### Create the Cloud Object Storage Experimental Package Binding
IBM Cloud Functions has recently created an experimental package that introduces a feed action used during trigger creation to configure bucket-specific events. We will create a package binding to make the `whisk.system/cos-experimental` package avaliable in our namespace. This will enable us to set our own parameters on the package, such as required credentials.

1. Create the package binding:
    ```
    ibmcloud fn package bind /whisk.system/cos-experimental myCosPkg
    ```

1. The service bind command requires your CloudFoundry API endpoint to be set. Let's set that now:
    ```
    ibmcloud target --cf
    ```

1. Bind your Cloud Object Storage credentials to your package binding. Make sure to include the name you chose for your Cloud Object Storage instance.
    ```
    ibmcloud fn service bind cloud-object-storage myCosPkg --instance YOUR_COS_INSTANCE_NAME
    ```

### Create Required Environment Variables and Deploy Cloud Functions
To deploy the functions required in this application, we'll use the `ibm fn deploy` command. This command will look for a `manifest.yaml` file defining a collection of packages, actions, triggers, and rules to be deployed. 
1. Let's clone the application.
    ```
    git clone git@github.com:IBM/cos-trigger-functions.git
    ```

1. Take a look at the `serverless/manifest.yaml file`. You should see manifest describing the various actions, triggers, packages, and sequences to be created. You will also notice that there are a number of environment variables you should set locally before running this manifest file.

1. Choose a package name, trigger name, and rule name and then save the environment variables.  *Note: The package you will create will hold all of the actions for this application. Do not use `myCosPkg` for the package name since this is already in use by the package binding you just created.*
    ```
    export PACKAGE_NAME=<your_chosen_package_name>
    export RULE_NAME=<your_chosen_rule_name>
    export TRIGGER_NAME=<your_chosen_trigger_name>
    ```

1. You already chose a bucket name earlier when creating your COS Instance. Save that name as your BUCKET_NAME environment variable:
    ```
    export BUCKET_NAME=<your_bucket_name>
    ```

1. You will need to save the endpoint name, which is the COS Endpoint for your buckets. Since you selected us-geo when selecting your buckets, the endpoint should be `s3.us.cloud-object-storage.appdomain.cloud`. 

    ```
    export ENDPOINT=s3.us.cloud-object-storage.appdomain.cloud
    ```

*Note: If you selected a different region, you can find your endpoint by clicking your Cloud Object Storage service in the [Resource list](https://cloud.ibm.com/resources?groups=storage), finding your bucket in the list, and then looking under Configuration for that bucket. Use the public endpoint.*

1. Finally, you will need some information from the Visual Recognition service.  You saved your apikey earlier, so use that. This application is built against the version released on `2018-03-19`, so we'll use that value for VERSION.
    ```
    export API_KEY=<your_visual_recognition apikey>
    export VERSION=2018-03-19
    ```

1. You've set up some required credentials and various parameters for your IBM Cloud Functions. Let's deploy the functions now! Change directories to the `serverless` folder, and then deploy the functions.
    ```
    cd serverless
    ibmcloud fn deploy
    ```

### Bind Service Credentials to the Created Cloud Object Storage Package
1. The deploy command created a package for you called `cloud-object-storage`. This package contains some useful cloud functions for interacting with cloud object storage. If you selected a different region for your bucket than Cross Region us-geo, let's update the `cloud-object-storage` package with this information.
    ```
    ibmcloud fn package update cloud-object-storage --param endpoint $ENDPOINT
    ```

1. Let's bind the service credentials to this package.
    ```
    ibmcloud fn service bind cloud-object-storage cloud-object-storage --instance YOUR_COS_INSTANCE_NAME
    ```

1. Congratulations! If you went directly to your cloud object storage bucket and added a file, you should see your trigger fire and some processed actions showing up in your `mybucket-processed` bucket. Let's deploy a simple application for uploading the images and showing these results.

### Deploy the Web Application
Finally, let's deploy the web application that enables our users to upload images and see the resulting images. The application is a node.js with express app, and we can deploy it to IBM Cloud using the manifest.yaml file provided in the `/app` folder.
1. Change directories to the `app` folder:
    ```
    cd ../app
    ```
  
1. Update the `config.js` file with the required configuration values: your bucket name, your processed bucket name, and your endpoint url. You should've already found these values earlier.

1. Create a file named `credentials.json` based on the `credentials_template.json` file. You can easily get the credentials by going to the cloud object storage service page, and clicking `Service Credentials`. You can copy this entire block and paste it as a child to `"OBJECTSTORAGE_CREDENTIALS":`.

1. Open up the manifest.yaml file at `app/manifest.yaml`. You should see something like this:
    ```
    ---
    applications:
    - name: <YOUR_APPLICATION_NAME>
      memory: 256M
      instances: 1
    ```

    This manifest file describes the cloud foundry application we're about to deploy. Please rename the application to anything you would like. 

1. Deploy the application:
    ```
    ibmcloud cf push
    ```

1. Finally, we'll need to update the CORS settings on our processed images bucket.  The `cloud-object-storage` package we installed earlier comes with a method to let us do that. Run the following command, but be sure to edit it first with your own `-processed` bucket name.
    ```
    ibmcloud fn action invoke cloud-object-storage/bucket-cors-put -b -p bucket myBucket-processed -p corsConfig "{\"CORSRules\":[{\"AllowedHeaders\":[\"*\"], \"AllowedMethods\":[\"POST\",\"GET\",\"DELETE\"], \"AllowedOrigins\":[\"*\"]}]}"
    ```

1. You should now be able to use the application you deployed! When the application was deployed, it should've output a URL where your application lives, something like `my-app-name.cloud.ibm.com`. You can now go there and start uploading images and seeing your results!

# Stretch Goal: Step 5: Create a Slack Weather Bot

**Instructions:** https://github.com/IBM-Cloud/openwhisk-workshops/tree/master/bootcamp/ex6%20-%20building%20a%20weather%20bot
