# Build three serverless applications with Apache OpenWhisk!

- Step 0: Sign up for IBM Cloud
- Step 1: Configure your local development environment
- Step 2: Create Serverless Actions and APIs
- Step 3: Use the IBM Cloud UI to create Serverless Functions
- Step 4: (Stretch goal) Create a Weather Bot

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

   ![](images/homepage.gif)

### Actions

1. Select "Actions" from the left-hand menu panel on the homepage.

[This page](https://console.bluemix.net/openwhisk/actions) is the management page for actions. It shows actions within the chosen region, org and space.

![action details page](images/action-overview.png)

2. Select an action from the page to move to the action details page.

#### Details Overview

The action details page will show properties for the chosen action.

For supported runtimes, action source code is shown in an editor which allows users to make changes live. 

![action details page](images/action-editor.png)

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

![Invoking an action](images/invoking-action.gif)

#### Creating Actions

From the [action overview page](https://console.bluemix.net/openwhisk/actions), new actions can be created by providing the source code through the browser-based editor.

1. Select the "Create" button from the page.
2. Choose "Create Action" from the list.
3. Fill in the "Action name" and choose the "Runtime".
4. Click "Create"
5. Fill in the editor with your action source code.

![Creating an action](images/creating-action.gif)

### Triggers

1. Select "Triggers" from the left-hand menu panel on the homepage.

[This page](https://console.bluemix.net/openwhisk/triggers) is the management page for triggers. It shows triggers within the chosen region, org and space.

![Triggers Overview Page](images/triggers-overview.png)

2. Select a trigger from the page to move to the trigger details page.

#### Details Overview

The trigger details page will show properties for the chosen trigger.

![Triggers Overview Page](images/trigger-details.png)

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

![](images/create-trigger.gif)

### Monitoring

IBM Cloud Functions Web UI comes with a [comprehensive visualisation dashboard](https://console.bluemix.net/openwhisk/dashboard) for monitoring serverless applications.

This dashboard shows activations within a region, org and space. Developers can see activation results, invocation times and logging output through the dashboard. Activations displayed can be filtered by name or time window.

![](images/monitoring.png)

### APIs

HTTP endpoints for web actions can be created and managed through the IBM Cloud Functions Web UI. Using this interface is often a lot more intuitive than using the CLI tool for managing more complex APIs.

1. Select "APIs" from the left-hand menu panel on the homepage.

![API homepage](images/apis-homepage.png)

#### Details Overview

The API details page will show properties for the chosen API, including an API monitoring page showing invocations.

![API homepage](images/api-details.png)

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

![Creating an API](images/create-apis.gif)

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

# Stretch Goal: Step 4: Create a Slack Weather Bot

**Instructions:** https://github.com/IBM-Cloud/openwhisk-workshops/tree/master/bootcamp/ex6%20-%20building%20a%20weather%20bot
