# StuV-Companion-Administration

This repository contains all the scripts and files necessary for the productive environment for the StuV Companion App which arent storeable in the front- and backend repositories.

# Dockercompose file

The main used docker-compose file for the docker host.

# .env File

The ``.env`` file which is in use in the production stage.

# The host

The host is reachable under the address ``root.stuv-mosbach.de``, the user to use is the ``root`` user. All files are located in the directory ``/opt/traefik``

# Build process

## Frontend

### Android

Export the right env variables.
```bash
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/platform-tools
```
Copy over the necessary license files if they are missing
```bash
sudo cp -Ri ~/Android/Sdk/licenses/ /usr/lib/android-sdk/
```
Execute the build command in the root of the frontend repository
```bash
ionic cordova build android --prod --release
```
Go to the directory (frontend-repositroy/platforms/android/app/build/outputs/apk/release/) and execute the following commands to sign the app with the keystore
```bash
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore stuv_mos.keystore app-release-unsigned.apk stuv

zipalign -v 4 app-release-unsigned.apk app.apk
```
The finished "app.apk" can now be uploaded to the Google Play Console.

### iOS

Pretty much their is one requirement for the iOS build process, which is the necessity of an Mac.

To start the build process execute the following command:
```bash
ionic cordova build ios --prod --verbose
```
You can run this command on any machine, but expect it to fail on Linux and Windows with an error that xcode cannot be opened. This is fine.
To compile the app in XCode you have to do the following steps:

1. Replace the ``icon-1024.png`` in the directory ``frontend-repository/platforms/ios/StuV Companion/Images.xcassets/AppIcon.appiconset`` with the one of this repository
2. Open the project in XCode
3. Under ``File -> Workspace Settings...`` , change the Build System to ``Legacy Build System``
4. In the Projectscreen activate ``automatically manage signing`` and select the developer team
5. Change the bundle id from ``de.stuv_mosbach.stuvcompanion`` to ``de.stuv-mosbach.stuvcompanion``
6. Select ``Generic Device`` as build target
7. ``Generate -> Archive`` to build the app and then upload it to the AppStore

### Browser

Execute the following command
```node
ionic build --prod --service-worker
```
This will create the app as an deployable web project. Since we use docker for our host execute this command to build the frontend container:
```docker
docker build -t frontend:vX.X .
```
