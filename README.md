# StuV-Companion-Administration

This repository contains all the scripts and files necessary for the productive environment for the StuV Companion App which arent storeable in the front- and backend repositories.

# Dockercompose file

The main used docker-compose file for the docker host.

# .env File

The .env file which is in use in the production stage.

# The host

The host is reachable under the address root.stuv-mosbach.de, the user to use is the root user. All files are located in the directory /opt/traefik

# Build commands

## Android

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
