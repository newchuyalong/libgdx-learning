This page will be updated in the folowing minutes, when its ready it will be added to the Table of Contents.

This tutorial will guide you for all the steps to add Airpush adds in your Libgdx Powered Andriod Games. You will be able to add both Banner Ads, and Smartwall Ads. The basic structure is very like the [Admob Tutorial](https://github.com/libgdx/libgdx/wiki/Admob-in-libgdx). But there are several changes and it will be more straight forward with not much text. (If you want a very detailed explanation of why every step, feel free to read that tutorial first).

You need:

1. A Libgdx Powered Android Game Project.
2. And [Airpush](http://www.airpush.com/) Account.

### Step 1. Choose SDK.-

Airpush has 2 SDKs that you will be able to use in Google Play. Standard and Bundle. Both have adantages and flaws.

**Bundle.-**

Supports less devices than Standard (around 15% less), but pays you for each install of your app in which the user accepts the EULA additional to Ad clicks.

**Standard.-**

Support the most devices, but won't pay you for users accepting the EULA. Only Ad clicks.

This tutorial will cover the 2 SDKs, any differences in their implementation will be noted.

What SDK do I recommend?. Well I use both, I make an app that implements the standard SDK with certain Version Number, lets say 1. And then I implement the Bundle and make that with a higher Version Number, lets say 2. Then I upload the 2 APKs in the same app in Google Play Console (they have same package name, don't be confused). Then Google Play automatically will install the Bundle version if supported or the Standard if not supported. The reason Bundle support less devices is because a required permission.

### Step 2. Create App in Airpush Dashboard.-

Log in to your Airpush Account, you will be taken to the Dashboard. Click the Add Application button left of the screen.

![](http://1.bp.blogspot.com/-YufTLy-nEjY/U7BXGbnwR-I/AAAAAAAAAfM/RN7vNoSU4w8/s1600/aptuto1.png)

Fill all the fields in the first tab and Click Continue. Don't worry if you haven't uploaded your app to Google Play yet, just put your package name there and it will be automatically checked when its up. But try not to take more than 48 hours to publish it.

![](http://3.bp.blogspot.com/-luFvjGASgIs/U7BYTt7-_jI/AAAAAAAAAfo/p-HjABp9WVI/s1600/aptuto4.png)

In the next tab, uncheck Push Notification ads, and Icon Ads, you don't need those and also they don't complain with Google Play policies. You will be using Standard or Bundle SDK or both, and they don't use them. Check SmartWall Ads, and Banner Ads. Also you can check "Exclude dating campaigns" if you don't want your users to see sexually suggestive ads (I always check it).

![](http://4.bp.blogspot.com/-tGxjsi1N2PI/U7BXGSj3t4I/AAAAAAAAAfc/gxv-3IHedGg/s1600/aptuto2.png)

Back in the Dashboard save your API Key and Your App ID. You will need these later.

![](http://2.bp.blogspot.com/-iUq5Q3kVa7g/U7BXGe4Vx2I/AAAAAAAAAfQ/7UX1yBkjeuE/s1600/aptuto3.png)

### Step 3. Add Google Play Services Library.-

You must import Google Play Services Library and link it to your Android Libgdx Project.

First of all, install/update it. Open Android SDK Manager and install Google Play Services under Extras.

![](http://3.bp.blogspot.com/-DnUAsVulob4/U7Bb6iahLhI/AAAAAAAAAf0/_fsMFtqGgYs/s1600/aptuto5.png)

Now go to your Android SDK Installation Folder -> extras -> google -> google_play_services -> libproject and copy "google-play-services_lib" folder to your workspace.

Now, import the project like this in eclipse: File -> import... -> Android -> Existing Android Code Into Workspace. You will end up with something like this:

![](http://3.bp.blogspot.com/--EDisIcafoQ/U7BdeDRr8xI/AAAAAAAAAgA/j-cZKMUl5gE/s1600/aptuto6.png)

Right click the Android Project, go to Properties -> Android and scroll down. Click the "Add..." button and choose the services project. Will end up like this:

![](http://4.bp.blogspot.com/-9McHVljjb2w/U7BeV-vEx5I/AAAAAAAAAgI/NZRvmOcbvJA/s1600/aptuto7.png)

### Step 4. Adding the Airpush SDK.-

Go to the Airpush Dashboard and download your SDK. Standard or Bundle. Now this is where gets tricky, every developer has a different SDK name and also SDK packages. So you must be aware you have to change these values when you are implementing them.

Unzip the rar. And copy "mraid_attrs.xml" to your Android Project 'res- > values' folder. And copy the .jar file to your Android Project 'libs' folder.

Right click your Android Project and choose 'Properties -> Java Build Path', go to 'Libraries' tab. And click 'Add JARs'. Choose the android project -> libs -> airpushsdk.jar (remember it has a different name for each developer). Now also add the Goggle Play Services jar, because even if its added as a library, android won't export the jar. click 'Add JARs' again, go to services project -> libs -> google-play-services.jar.

Switch to the 'Order and Export' tab and be sure to check both new jars. Will end up like this:

![](http://4.bp.blogspot.com/-XI9EttETTmo/U7BhQkGE7VI/AAAAAAAAAgU/aHAH0Tkguso/s1600/aptuto8.png)

Click Ok to finish.

### Step 5. Editing the Android Manifest.-

This is different for Standard and Bundle SDKs.-

**Standard.-**
