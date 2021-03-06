---
template: page-sidebar
title: "Optimizely Classic Android SDK Guide"
---

# Getting started with Optimizely's Classic Android SDK

<div class="attention attention--warning push--bottom">
Optimizely Classic will sunset on September 30, 2018. Please [transition to Optimizely X Full Stack](https://help.optimizely.com/Set_Up_Optimizely/Make_the_transition_to_Optimizely_X_Full_Stack). Have questions? Our support team is here to help.
</div>

## SDK Download

You can download the Android SDK through gradle or install it through Fabric.

<a href="https://fabric.io/kits/android/optimizely"><img src="/assets/img/mobile/fabric_button.png" style="width: 150px; height: 58px;"/></a>

[JAR](https://github.com/optimizely/Optimizely-Android-SDK/raw/master/Optimizely-1.3.2-SNAPSHOT.jar) | [GitHub](https://github.com/optimizely/Optimizely-Android-SDK)

[Releases](https://github.com/optimizely/Optimizely-Android-SDK/) | [Change History](https://github.com/optimizely/Optimizely-Android-SDK/raw/master/Changelog.md)

##### *Android SDK Install Video*
<iframe src="//fast.wistia.net/embed/iframe/poqkf6ok6s" allowtransparency="true" frameborder="0" scrolling="no" class="wistia_embed" name="wistia_embed" allowfullscreen mozallowfullscreen webkitallowfullscreen oallowfullscreen msallowfullscreen width="720" height="450"></iframe>
<script src="//fast.wistia.net/assets/external/E-v1.js" async></script>

<h2 id="sdk-installation">1. SDK Installation</h2>

To use Optimizely for Android you must first integrate the SDK into your app using [Gradle](#using-gradle).

**We strongly recommend using a version control system (such as [Git](http://git-scm.com/)) and checking in your app before installing Optimizely.**

### Using Gradle

Your project must be set up to build with Gradle. Refer to [Gradle Getting Started](http://developer.android.com/sdk/installing/studio-build.html) if you haven't yet configured your project to work with Gradle.

Our SDK only supports Android API 14 (Ice Cream Sandwich) and above, so please make sure your `build.gradle` specifies a [minimum sdk](https://developer.android.com/tools/building/configuring-gradle.html#buildFileBasics) of 14 (or above). Then, add the Optimizely-Android-SDK repository as a maven repository to your project's top-level `build.gradle` file:

```groovy
allprojects {
  repositories {
    jcenter()
    maven {
      url 'http://dl.bintray.com/optimizely/optimizely'
    }
  }
}
```
Make sure you add the repository to the `allprojects` configuration, *not* the `buildscript` configuration.

Then, add Optimizely as a dependency in your app's `build.gradle`:

```groovy
dependencies {
    compile('com.optimizely:optimizely:+@aar') {
        transitive = true
    }
}
```

<h2 id="create-an-android-project">2. Create an Android Project</h2>

To create an Android project, select "New Project" from the left drawer in your [Optimizely Home](https://app.optimizely.com/projects/):

   <img src="/assets/img/android/create-project.png" alt="Create Project Dialog" style="width: 80%;"/>


<a name="project-code"></a>Once you've created a project, please take a look at the `Implementation` section under the `Settings` tab to find your project ID and API key which you will use during integration:

![Project Code Dialog](/assets/img/android/project-code.png)

<h2 id="activate-optimizely">3. Activate Optimizely</h2>

Now, you're ready to add some code so your app can connect with your Optimizely account.

Optimizely assigns each project a unique identifier known as an API token. Your app identifies itself to the Optimizely Android SDK by initializing the SDK with this token.

Open the Java source file corresponding to your app's main activity (the one declared as a launcher activity in your `AndroidManifest.xml`), and add the following import:

  ```java
  import com.optimizely.Optimizely;
  ```

Add the following to the end of your main activity's `onCreate()` function. The code can be copied from your `Implementation Settings`, which you can find by selecting on the appropriate Android Project in your [Optimizely Home](https://app.optimizely.com/projects).  For more details, you can refer back to [Step 2: Create an Android project](#accountcreation).
   
<div class="attention attention--warning push--bottom">Optimizely must be started from your main launcher activity's `onCreate()` method. Starting anywhere else, such as `Application#onCreate()` will not be supported and may lead to unpredictable behavior.</div>


```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    // more create logic

    // Crash reporting tool initialization

    // You can find the following code snippet in your project code.
    Optimizely.startOptimizelyWithAPIToken("<API Token>", getApplication());
}
```

This call will block until Optimizely is started. For a non-blocking call, use the following version which takes a callback listener as the third argument. If you are not interested in the callback, you can pass in `null`:

```java

private static void mOptimizelyEventListener = new DefaultOptimizelyEventListener() {
    @Override onOptimizelyStarted() {}
    @Override onOptimizelyFailedToStart(String message) {}
};

@Override
protected void onCreate(Bundle savedInstanceState) {
    // More startup code here
    Optimizely.startOptimizelyAsync("<API Token>", getApplication(), mOptimizelyEventListener);
}
```

*Note:
   startOptimizelyAsync must be initialized on the UI (main) thread to coordinate with the activity lifecycle.*

Optimizely needs to connect to the internet to allow you to use our online editor and to download new experiments once you've published them. If you don't already have the `INTERNET` permission declared in your `AndroidManifest.xml`, you will need to add the following permission line:

```xml
 <uses-permission android:name="android.permission.INTERNET" />
 ```

 For more information on Android permissions, see the [Android Manifest Intro](http://developer.android.com/guide/topics/manifest/manifest-intro.html#perms).

 Starting with version 1.1, Optimizely's Android SDK uses a unique URL scheme handler to help you edit and test your experiments.

   Add the following intent filter to the MainActivity entry of your AndroidManifest.xml

   ```xml
   <activity
     android:name=".MainActivity">
     <intent-filter>
       <!-- Existing intent-filters -->
     </intent-filter>
     <intent-filter>
       <action android:name="android.intent.action.VIEW" />
       <category android:name="android.intent.category.DEFAULT" />
       <category android:name="android.intent.category.BROWSABLE" />
       <data android:scheme="optly[PROJECT_ID]" />
     </intent-filter>
   </activity>
   ```

   *Note:
   The scheme includes a unique identifier.  Be sure to replace [PROJECT_ID] with your project id (e.g. optly123456). Also, we recommend that url handlers are not registered in the production version of your app.  You should only register it in the `AndroidManifest.xml` in the `src/debug` source set.  This way browsing to a short link to enter edit and preview mode will only work for your debug builds.*


At this point you should run your application to register the SDK installation. The Optimizely SDK will register itself in the background. You will see your project overview display light up, allowing you to create an experiment:

   <img src="/assets/img/android/sdk-detected.png", style="width: 70%"/>

<h2 id="create-an-experiment">4. Create an Experiment</h2>

After creating an Android project and installing the SDK, click the `Create Experiment` button in the upper right hand side of your project overview.

#### Custom View Tagging

The Optimizely Android SDK identifies Views within your application using the view hierarchy of the view-- in some cases you may need to specify your own ID or mark a specific view in a collection view as unique.

See this section on [configuring the visual editor](/android/reference/index.html#visual-editor-configuration).

<h2 id="qa">5. QA</h2>

### Preview Mode

Preview mode allows you to force your app into a certain variation for a given experiment in order to check that your app and the experiment are both running smoothly. To enter preview mode, connect your device to the editor, select your desired variation, open the variation settings drawer, and click `Preview`

<img src="/assets/img/mobile/launch-preview.png" alt="Enter Preview Mode" />

### Programmatically Enable Preview Mode

While preview mode can be enabled from the dashboard it can also be enabled from code.  This allows you to preview variations across all of your experiments without needing to connect to the editor.  Preview mode has UI that allows you easily switch variations and view event logs.

```java
Optimizely.enablePreview();
Optimizely.startOptimizelyWithAPIToken(getOptimizelyToken(), getApplication());
```

### Pre-launch Checklist

Now that you've created an experiment and successfully installed the Optimizely Android SDK, below is a checklist to go through prior to releasing your app to the app store with the SDK:

1. In order to set up your app such that you can QA experiments (beyond using Preview), we recommend either having a separate [Project](#accountcreation) for development and production or inserting [Custom Tags](#customtags), which are only set for certain QA devices.  If you decide to go with setting up 2 separate projects, we recommend setting up an `if (DEBUG)` to ensure that only one project code snippet is defined at any given time.

2. Were you able to connect to Optimizely's Visual Editor?  Before your release to the app store, you will want to make sure that `Optimizely.setEditGestureEnabled(false)` is called before `Optimizely.startOptimizelyWithAPIToken`.

3. (Optional) If you have a separate project for development and production, you can run your experiments in your development environment to check that results are updating and that you are seeing the different variations are being seen.
    - A useful debugging tool is to enable logging (be sure to disable this feature when your app is live in the Play Store) `Optimizely.setVerboseLogging(true);` For each event that is triggered, you will see a log statement. Be sure to check that verboseLogging is not enabled in production.
    - You will want to make sure that each experiment does not make changes to the same element (otherwise only one of the experiments will run).
    - Optimizely tracks unique visitors, so that we make sure that the same user sees the same experience.  If you would like to check that you are getting a random experience, you will need to delete the app to be counted as a new visitor.
    - By default, Optimizely sends network calls every 2 minutes or upon backgrounding. (You can find more details about modifying the SDK network settings [here](#networksettings)). In order to check that your event data is being updated in the Optimizely Results Page as expected, you can either:
       1. Trigger events in the app and keep the app foregrounded for 2 minutes
       2. Background the app so that events are sent to our servers.

4. Once you've checked all these steps, you're ready to release to the Play Store!

## Advanced Setup

Once you have run your first few visual editor experiments or tried out Optimizely's SDK, you may find you would like to include programmatic experiments, additional tracking calls, or analytics integrations.  For advanced setup, below are a subset of advanced features we recommend utilizing prior to releasing to the Play Store:

1. [Live Variables](../reference/index.html#register-live-variables)
2. [Code Blocks](../reference/index.html#code-blocks)
3. [Custom Tags](../reference/index.html#custom-tags)
4. [Track Event](../reference/index.html#track-event) (for key metrics you would like to track in your app)
5. [Revenue Tracking](../reference/index.html#revenue-tracking)
6. [Analytics Integration](../reference/index.html#analytics-integrations)
7. [Optimizely Event Listeners](../reference/index.html#optimizely-debug)

For a comprehensive list of all additional methods available in the SDK you can refer to the [Reference](../reference/index.html) section or the [Java Docs](/android/help/reference/packages.html).
