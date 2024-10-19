# Git Repo Apk Builder. 

### Build and sign the apk(s) for any android project on Github.

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
- **For your own app's security, do not sign it with this key for publishing, as the password is publicly available.**


```
name: build apk

on:
  workflow_dispatch:
    inputs:
      repo-url:
        description: 'git clone url'
        required: true
      repo-commit:
        description: 'commit'
      repo-dir:
        description: 'dir ("/..")'
      module:
        description: 'module'
        required: true
        default: 'app'
      googleservices:
        description: 'google-services.json (content)'
      localproperties:
        description: 'local.properties (content)'
      

jobs:
  main:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v2
        
      - name: clone
        run: |
          git clone ${{ github.event.inputs.repo-url }}
          echo "projectName=$(basename -s .git ${{ github.event.inputs.repo-url }})" >> $GITHUB_ENV
          echo "dirName=$(echo ${{ github.event.inputs.repo-dir }} | sed -r 's/[/]+/ - /g')" >> $GITHUB_ENV
        
      - name: select commit
        if: ${{ github.event.inputs.repo-commit != '' }}
        run: |
          cd ${{ env.projectName }}
          git checkout ${{ github.event.inputs.repo-commit }}
          
      - name: add google-services.json
        if: ${{ github.event.inputs.googleservices != '' }}
        run: |
          content=$(jq -r '.inputs.googleservices' $GITHUB_EVENT_PATH)
          echo "::add-mask::$content"
          echo "$content" > ${{ env.projectName }}${{ github.event.inputs.repo-dir }}/${{ github.event.inputs.module }}/google-services.json
        
      - name: add local.properties
        if: ${{ github.event.inputs.localproperties != '' }}
        run: |
          content=$(jq -r '.inputs.localproperties' $GITHUB_EVENT_PATH)
          echo "::add-mask::$content"
          echo "$content" > ${{ env.projectName }}${{ github.event.inputs.repo-dir }}/local.properties
        
      - name: setup
        uses: actions/setup-java@v2
        with:
          distribution: "zulu"
          java-version: 11
          cache: "gradle"
          
      - name: build
        run: |
          cd ${{ env.projectName }}${{ github.event.inputs.repo-dir }}
          chmod +x gradlew
          ./gradlew ${{ github.event.inputs.module }}:assembleRelease

      - name: upload
        uses: actions/upload-artifact@v2
        with:
          name: ${{ env.projectName }}${{ env.dirName }} - ${{ github.event.inputs.module }}
          path: ${{ github.workspace }}/${{ env.projectName }}${{ github.event.inputs.repo-dir }}/${{ github.event.inputs.module }}/build/outputs/apk/release/*.apk 
          
```

------  
[![APK Build Action (new)](https://github.com/vaginessa/grab-n-build-APK-action/actions/workflows/build-apk.yml/badge.svg?branch=master)](https://github.com/vaginessa/grab-n-build-APK-action/actions/workflows/build-apk.yml)


