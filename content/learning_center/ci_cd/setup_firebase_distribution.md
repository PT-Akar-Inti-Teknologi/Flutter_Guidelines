# Setup Firebase Distribution Using Firebase Fastlane

if your project template is already having fastlane directory with it's child you can jump straigh into "Configure fastlane"

## Install Ruby

there are various method for installing ruby you can find the methods that suit your worstation
[in here](https://www.ruby-lang.org/en/documentation/installation/#managers)

## Installing Fastlane and Bundler

It is recommended that you use Bundler and Gemfile to define your dependency on fastlane. This will clearly define the fastlane version to be used and its dependencies, and will also speed up fastlane execution.

- Install Bundler by running gem install bundler
- Create a ./Gemfile in the <project_app>/android with the content

```
source "https://rubygems.org"

gem "fastlane"
```

> <project_app> here is the path where you usually run your flutter project (eg pakuwon_project/app/pakuwon)

- Run bundle update
- Every time you run fastlane, use bundle exec fastlane <laneMethodName>
- run `fastlane init` you'll be asked to confirm that you're ready to begin, and then for a few pieces of information. To get started quickly:

  - Provide the package name for your application when asked (e.g. io.fabric.yourapp)
  - Press enter when asked for the path to your json secret file
  - Answer 'n' when asked if you plan on uploading info to Google Play via fastlane (we can set this up later)
  - That's it! fastlane will automatically generate a configuration for you based on the information provided.
  - You can see the newly created ./fastlane directory, with the following files:
    - Appfile which defines configuration information that is global to your app
    - Fastfile which defines the "lanes" that drive the behavior of fastlane

- delete Gemfile.lock and then move the fastlane directory and the Gemfile, into <project_app>. this is neccessary since we want to put it in app directory and not in android but if we do it here at the start we will have some trouble when running fastlane init since it will also want us to generate for IOS

## Configuring Fastlane

- on fastlane directory fin Fastfile
- create new lane with this line of code

```
desc "Deploy Firebase Distribution"
      lane :distributeDev do
          firebase_app_distribution(
                app: "1:73826049315:android:14e9ab14d0205bd95c4096",
                service_credentials_file: "firebase_credential.json",
                apk_path: "build/app/outputs/flutter-apk/app-release.apk",
                release_notes_file: "appDistConf/releaseNote.txt",
		testers_file: "appDistConf/tester.txt",
          )
      end

```

> app: is firebase id, you can find it at firebase console => project settings => general => look for App ID.

> service_credentials_file: you could get it from firebase console => project settings => Service accounts => Generate new priate key => copy the downloaded json file into your project and put the path here.

> apk_path: is where your flutter app outputs will be, to find it try to build an app, then copy the path provided at the end of the build process.

> release_notes_file: will be a path to file where you put your release note. remember to update the release note before you release a new version

> testers_file: will be a list of email that will getting the email of the app Distribution

## Run fastlane

run and distribute your fastlane using

```
bundle exec fastlane [lane]
```

> [lane] here is the method name that you write on fastfile. so if you copying the snippet from Configuring fastlane you could run it with
> bundle exec fastlane distributeDev
