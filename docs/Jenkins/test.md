```shell
#!/bin/sh

# unity app path

UNITY_PATH=/Applications/Unity/Hub/Editor/2021.3.8f1/Unity.app/Contents/MacOS/Unity

# project path

PROJ_PATH=/Users/gamedevturbo/GitProject/FirstFantasy

echo "============== Unity Build APK Begin =============="

$UNITY_PATH -projectPath $PROJ_PATH -executeMethod BuildTools.BuildApk -logFile $PROJ_PATH/android/Android.log -batchMode -quit

echo "============== Unity Build APK Finish =============="
```





