# Android Auto-Build APK

This is an example of how to build an APK using GitHub Actions.
Here, we created a simple Android application that displays a Hello World!.
We then created a workflow that builds the APK and uploads it as a new release.

| Hello World! | 
| :---: | 
| ![](screenshots/1.png) | 

## How to use

```yml
name: Build project after push

on:
  push:
    branches:
      - main
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: set up JDK 11
        uses: actions/setup-java@v3
        with:
          java-version: '11'
          distribution: 'temurin'
          cache: gradle

      - name: Grant execute permission for gradlew
        run: chmod +x gradlew

      - name: Build with Gradle
        run: ./gradlew build

      - name: Build debug APK
        run: bash ./gradlew assembleDebug --stacktrace

      - name: Check files
        run: ls -al app/build/outputs/apk/debug

      - name: Create Release
        id: create_release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.TOKEN }}
        with:
          tag_name: ${{ github.run_number }}
          release_name: ${{ github.event.repository.name }} v${{ github.run_number }}

      - name: Upload Release APK
        id: upload_release_asset
        uses: actions/upload-release-asset@v1.0.1
        env:
          GITHUB_TOKEN: ${{ secrets.TOKEN }}
        with:
          upload_url: ${{ steps.create_release.outputs.upload_url }}
          asset_path: app/build/outputs/apk/debug/app-debug.apk
          asset_name: ${{ github.event.repository.name }}.apk
          asset_content_type: application/vnd.android.package-archive
```

Check out the [workflow](.github/workflows/build.yml) for more details.
