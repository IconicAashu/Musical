name: Build Musical Android APK
on: [push, workflow_dispatch]

jobs:
  build-apk:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Unpack Any ZIP File Automatically
        run: |
          ls -la
          for z in *.zip; do
            if [ -f "$z" ]; then
              echo "Unzipping $z..."
              unzip -q -o "$z"
            fi
          done
          if [ -d "Musical" ] && [ -f "Musical/settings.gradle.kts" ]; then
            shopt -s dotglob
            mv Musical/* .
          fi
          ls -la

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Setup Android SDK
        uses: android-actions/setup-android@v3

      - name: Setup Gradle 8.7
        uses: gradle/actions/setup-gradle@v3
        with:
          gradle-version: '8.7'

      - name: Build Debug APK
        run: gradle assembleDebug --no-daemon

      - name: Upload Musical-debug.apk Artifact
        uses: actions/upload-artifact@v4
        with:
          name: Musical-debug-apk
          path: app/build/outputs/apk/debug/app-debug.apk
