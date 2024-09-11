# Github Actions for Unity
 

#### Activating a license file[​](https://game.ci/docs/github/activation/#activating-a-license-file "Direct link to Activating a license file")

1. Install [Unity Hub](https://unity.com/download) on your local machine.
2. Log into Unity Hub with the Unity account that you are using for CI. Make sure you log into the correct account or you may not activate the correct license.
3. When prompted, activate your personal license. If you are not prompted, go to `Unity Hub` > `Preferences` > `Licenses` > `Add` and choose `Get a free personal license`. You do not need to install an editor on your local machine.
4. Depending on the host you are using, find the `.ulf` file which is your Unity license in the following paths:
    - Windows: `C:\ProgramData\Unity\Unity_lic.ulf`
    - Mac: `/Library/Application Support/Unity/Unity_lic.ulf`
    - Linux: `~/.local/share/unity3d/Unity/Unity_lic.ulf`
    If you have issues locating the file, ensure you've logged into Unity Hub AND activated your license. Otherwise the file will not be present. Keep in mind these folders might be hidden by default, you may need to reveal hidden files to see them.
    
    > **Note:** The host platform you use to activate the license does not matter. You only need to get the ulf from the platform that is most convenient to you. For example, using Windows to activate your license and building on Ubuntu on Github is perfectly fine.
    
5. Open `Github` > `<Your repository>` > `Settings` > `Secrets and Variables` > `Actions`.

![](media/Actions%20secrets%20%C2%B7%20949886SRP.png)

#### Personal

| Key            | secrets                    |
| -------------- | -------------------------- |
| UNITY_EMAIL    |  Your unity account email.             |
| UNITY_PASSWORD | Your unity account password.                   |
| UNITY_LICENSE  | Copy all contents of `Unity_lic.ulf` file. |
- Windows: `C:\ProgramData\Unity\Unity_lic.ulf`
- Mac: `/Library/Application Support/Unity/Unity_lic.ulf`
- Linux: `~/.local/share/unity3d/Unity/Unity_lic.ulf`


`.github\workflows\main.yml` 

```yaml
name: Build project

on: [push, pull_request]

jobs:
  buildForAllSupportedPlatforms:
    name: Build for ${{ matrix.targetPlatform }}
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        targetPlatform:
          - StandaloneOSX # Build a macOS standalone (Intel 64-bit).
          - StandaloneWindows # Build a Windows standalone.
          - StandaloneWindows64 # Build a Windows 64-bit standalone.
          - StandaloneLinux64 # Build a Linux 64-bit standalone.
          - iOS # Build an iOS player.
          - Android # Build an Android .apk standalone app.
          - WebGL # WebGL.
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          lfs: true
      - uses: actions/cache@v3
        with:
          path: Library
          key: Library-${{ matrix.targetPlatform }}
          restore-keys: Library-
      - if: matrix.targetPlatform == 'Android'
        uses: jlumbroso/free-disk-space@v1.3.1
      - uses: game-ci/unity-builder@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
          UNITY_EMAIL: ${{ secrets.UNITY_EMAIL }}
          UNITY_PASSWORD: ${{ secrets.UNITY_PASSWORD }}
        with:
          targetPlatform: ${{ matrix.targetPlatform }}
      - uses: actions/upload-artifact@v3
        with:
          name: Build-${{ matrix.targetPlatform }}
          path: build/${{ matrix.targetPlatform }}
```

```yaml
name: Build project

on: [push, pull_request]

jobs:
  buildForAllSupportedPlatforms:
    name: Build for ${{ matrix.targetPlatform }}
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        targetPlatform:
          # - StandaloneOSX # Build a macOS standalone (Intel 64-bit).
          # - StandaloneWindows # Build a Windows standalone.
          - StandaloneWindows64 # Build a Windows 64-bit standalone.
          # - StandaloneLinux64 # Build a Linux 64-bit standalone.
          # - iOS # Build an iOS player.
          - Android # Build an Android .apk standalone app.
          - WebGL # WebGL.
    steps:
      # Checkout
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          lfs: true

      # Cache
      - uses: actions/cache@v3
        with:
          path: Library
          key: Library-${{ matrix.targetPlatform }}
          restore-keys: Library-
      - if: matrix.targetPlatform == 'Android'
        uses: jlumbroso/free-disk-space@v1.3.1

      # Build
      - uses: game-ci/unity-builder@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
          UNITY_EMAIL: ${{ secrets.UNITY_EMAIL }}
          UNITY_PASSWORD: ${{ secrets.UNITY_PASSWORD }}
        with:
          targetPlatform: ${{ matrix.targetPlatform }}

      # Output
      - uses: actions/upload-artifact@v3
        with:
          name: Build-${{ matrix.targetPlatform }}
          path: build/${{ matrix.targetPlatform }}
```


#### Professional

| Key            | secrets                        |
| -------------- | ------------------------------ |
| UNITY_EMAIL    |  Your unity account email.             |
| UNITY_PASSWORD | Your unity account password.                   |
| UNITY_SERIAL   | X-XXXX-XXXX-XXXX-XXXX-XXXX |


Professional license:

```yaml
name: Build project

on: [push, pull_request]

jobs:
  buildForAllSupportedPlatforms:
    name: Build for ${{ matrix.targetPlatform }}
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        targetPlatform:
          # - StandaloneOSX # Build a macOS standalone (Intel 64-bit).
          # - StandaloneWindows # Build a Windows standalone.
          - StandaloneWindows64 # Build a Windows 64-bit standalone.
          # - StandaloneLinux64 # Build a Linux 64-bit standalone.
          # - iOS # Build an iOS player.
          - Android # Build an Android .apk standalone app.
          - WebGL # WebGL.
    steps:
      # Checkout
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          lfs: true

      # Cache
      - uses: actions/cache@v3
        with:
          path: Library
          key: Library-${{ matrix.targetPlatform }}
          restore-keys: Library-
      - if: matrix.targetPlatform == 'Android'
        uses: jlumbroso/free-disk-space@v1.3.1

      # Build
      - uses: game-ci/unity-builder@v4
        env:
          UNITY_SERIAL: ${{ secrets.UNITY_SERIAL }}
          UNITY_EMAIL: ${{ secrets.UNITY_EMAIL }}
          UNITY_PASSWORD: ${{ secrets.UNITY_PASSWORD }}
        with:
          targetPlatform: ${{ matrix.targetPlatform }}

      # Output
      - uses: actions/upload-artifact@v3
        with:
          name: Build-${{ matrix.targetPlatform }}
          path: build/${{ matrix.targetPlatform }}
```

