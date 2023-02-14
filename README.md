# devops

## Android Continues Deployment (CICD)

  `Android_Deployment.yml` file inside github workflows contains CICD implementation of Android deployment process.

### Version Update

  We need new version code on each release there for we use [chkfung/android-version-actions](https://github.com/chkfung/android-version-actions). Each workflow run we get workflow number and assign it as `Version Code`.

### Install node modules

 Before we run the project we need to install node modules.

### Run Tests (CI)

  Before we build the project make sure there are no errors. 

### Setting up Gradle cache

  We will cache our Gradle dependencies and wrapper to keep our builds faster. For that We use Github cache actions.

### Make Gradle executable

  Some Linux environments, Gradle is not executable by default. Just in case We make sure it's execuatable.

### Generating the Android release build

  This part is bit tricky. We need `ANDROID_SIGNING_KEY`, `ANDROID_SIGNING_ALIAS`, `ANDROID_SIGNING_STORE_PASSWORD` and `ANDROID_SIGNING_KEY_PASSWORD`.

  - _ANDROID_SIGNING_ALIAS_ : Alias name you provide when you create the `Key`.
  - _ANDROID_SIGNING_STORE_PASSWORD_ : Password you provide as store password when you create the `Key`.
  - _ANDROID_SIGNING_KEY_PASSWORD_ : Password you provide as key password when you create the `Key`.
  - _ANDROID_SIGNING_KEY_ : Base 64 encorded `signing key` In order to convert your releaseKey.jks to base64 encoded one you gonna need `Openssl`.
    wirh below command you can create your base64encoded key file.

         openssl base64 < some_signing_key.jks | tr -d '\n' | tee some_signing_key.jks.base64.txt 

    Then you have to store all the values as github secrets.

### Upload Artifact

   We will save your signed artifact as a backup.

### Deploy to Play Store
  
   Before we do that we need to setting up the Google Play Developer API and attaching it with Play Console. You can follw [this guide](https://developers.google.com/android-publisher/getting_started) in order to complete it.
    Also We use this [this plugin](https://github.com/r0adkll/upload-google-play) for this. you can refer it's documentation as well.

  - _ANDROID_SERVICE_ACCOUNT_ : Go to [Google Cloud](https://console.cloud.google.com/) page and select `Service Accounts` then select your account then select `KEYS` Tab there you can see `ADD KEY` button. Add Key with Json format. Then you will get a file. save content of this file as `ANDROID_SERVICE_ACCOUNT` in secrets.
  - _packageName_ : should be your package name.
  - _track_ : One of `production`, `beta`, `alpha`, `internalsharing`, `internal`, or a custom track name
  - _status_ : One of `completed`, `inProgress`, `halted`, `draft`. Cannot be null.

### Notify deploy to Rollbar

    Finally we let `Rollbar` know that our deployment is success.

  - _ROLLBAR_ACCESS_TOKEN_ : Go to `Project Access Token` inside your project settings then create new `post_server_item` as `ROLLBAR_ACCESS_TOKEN` paste it's key inside github secrets.
  - _environment_ : Mention your environment. `Production` or `Test`.

FYI - If you face an error while building the APP you may want to comment `signingConfig signingConfigs.debug` line on build.gradle file.
