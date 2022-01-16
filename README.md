# Git Repo Apk Builder
Build and sign the apk(s) for any android project on Github.
[Workflow](https://github.com/Yanndroid/grab/actions/workflows/build-apk.yml)

## Usage
1. fork this repo
2. open the ```actions``` tab and select ```build apk``` in the left pane
3. click ```Run workflow``` and fill in what you need:
    - ```git clone url```: the git url of the repo you want to build the apk from. ex: ```https://github.com/Yanndroid/OneUI-Design-Library.git```
    - ```commit```: optional, if you want the apk of a particular commit (also from a different branch). ex: ```cf3fef4``` or ```cf3fef46ad0db897a270ac4a25ce6b4372f2655f```
    - ```dir```: optional, if the repo contains multiple projects (don't forget the **'/'**). ex: [```/ActivitySceneTransitionBasic```](https://github.com/android/animation-samples/tree/main/ActivitySceneTransitionBasic)
    - ```module```: the module you want to build (default: ```app```). ex: ```Application```
    - ```google-services.json```: optional, if the project uses firebase and requires a google-services.json file, you can add the content of the file here.
    - ```local.properties```: optional, if the project needs variables from local.properties to build, add it's content here.
    - ```sign apk```: whether to sign the apk or not.

    the content you add in ```google-services.json``` and ```local.properties``` stays private and isn't shown in the log.
4. get your apk(s) after the run in the summary under ```Artifacts```.

## Notes
- If the run fails in ```build``` or ```build & sign``` there is a problem with the project and there's nothing I can do, otherwise kindly report the problem. Also check if you selected the right ```module``` and filled in the additional files if needed.
- For your own app's security, do not sign it with this key for publishing, as the password is publicly available.