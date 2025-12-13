# frp-Android
A frp client for Android  
一个Android的frp客户端

[简体中文](README.md) | English

<div style="display:inline-block">
<img src="./image/image1_en.png" alt="image1_en.png" width="200">
<img src="./image/image2_en.png" alt="image2_en.png" width="200">
</div>

## Compilation Methods

If you wish to customize the frp kernel, you can compile it via Github Actions or through Android Studio.

### (Recommended) Compiling via Github Actions

1. Convert your APK signing key file to base64; here's a Linux example:
```shell
base64 -w 0 keystore.jks > keystore.jks.base64
```
2. Fork this project.
3. Navigate to this page of the Github project: Settings > Secrets and variables > Actions > Repository secrets.
4. Add the following four environment variables:
```KEY_ALIAS``` ```KEY_PASSWORD``` ```STORE_FILE``` ```STORE_PASSWORD```  
The content for ```STORE_FILE``` should be the base64 from step 1, while you should fill in the other environment variables according to your key file.
5. A push commit will automatically trigger compilation, or you can manually trigger it on the Actions page.

### Compiling via Android Studio

1. Create an APK signing key configuration file named ```keystore.properties``` at the root directory of the project, referencing the existing ```keystore.example.properties``` file at the same level.
2. Refer to the [script instructions](./scripts/README.md) to run the `update_frp_binaries` script to obtain the latest frp kernel files, or manually download and place them in the appropriate directories.
3. Compile and package using Android Studio.

## FAQs
### Where does the frp kernel (libfrpc.so) of the project come from?
It is obtained directly by extracting the corresponding ABI Linux version archive from [frp's release](https://github.com/fatedier/frp/releases), renaming frpc to libfrpc.so.  
The project does not invoke methods from the so file within its code but treats the so as an executable file, executing the corresponding command through shell.  
Due to Golang's zero-dependency characteristic, the executable file can be run directly through shell in Android.

### Connection Retry
Add `loginFailExit = false` to the frpc configuration to prevent exiting after the first login failure, enabling multiple retry attempts.  
This is useful in scenarios such as auto-start on boot, where the network may not be ready when frpc starts to connect and fails. Without this option, frpc will exit immediately after a failed attempt.

### DNS Resolution Failure
Starting from v1.3.0, devices with the arm64-v8a architecture use the android type frp kernel to solve DNS resolution issues.  
Devices with armeabi-v7a and x86_64 architectures still use the linux type frp kernel, which may have DNS resolution problems. It is recommended to specify a DNS server using the `dnsServer` option in the configuration file.

### Start at Boot and Background Keep-Alive
The app is designed according to the native Android specification. However, some custom Android systems have stricter background management. Please manually enable the relevant options in the system settings. For example, on ColorOS 16, the connection may be disconnected when the app is sent to the background. After enabling [App Settings -> Power Management -> Fully Allow Background Activity], it will work normally.