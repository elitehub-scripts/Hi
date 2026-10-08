# Build EliteHub APK from an Android phone

This project includes a GitHub Actions workflow, so you do not need Android Studio or Gradle installed on your phone.

1. Upload all project files to the root of a GitHub repository.
2. Open the repository's **Actions** tab.
3. Select **Build EliteHub APK**.
4. Tap **Run workflow**.
5. Wait for the build to finish.
6. Open the completed run and download the **EliteHub-debug-apk** artifact.
7. Extract the artifact and install the APK on Android.

The workflow installs Gradle 9.3.1 and JDK 17 automatically.

Do not upload secrets such as private signing keys or passwords to the repository.
