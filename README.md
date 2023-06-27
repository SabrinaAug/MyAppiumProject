# MyAppiumProject
Projet pour apprendre à utiliser l'outil d'automatisation de tests sur Android, IOS ...

![](http://appium.io/docs/en/2.0/assets/images/appium-logo-horiz.png)

## Description:
Ce repository sert à l'apprentissage de l'outil d'automatisation de test "Appium".
Vous trouverez ci-dessous des ressources et indications pour installer et utiliser l'outil.

* 1ère version: release-sabrina-0.0.1

## :rotating_light: Requirements:

* Être sur MacOS, Linux, ou Windows
* Node.js (version comprise entre ^14.17.0 || ^16.13.0 || >=18.0.0)
* NPM version >= 8

## :wrench: Installation:

_Références du site officiel_
* [Pour l'installation d'Appium](http://appium.io/docs/en/2.0/quickstart/install/)
* [Pour l'insatllation du driver UiAutomator2 Driver](http://appium.io/docs/en/2.0/quickstart/uiauto2-driver/)

_Pour Android_:
* [Android SDK platform tools.](https://developer.android.com/studio/releases/platform-tools?hl=fr)
* [Android Studio](https://developer.android.com/studio)
### Installation d'Appium

```shell
npm i -g appium@next
```
La commade installe Appium globalemnt sur votre système afin que vous puissiez y accéder depuis la ligne de commande en exécutant simplement la commande appium.
Maintenant exécutez :

```shell
appium
```
Vous devriez voir s'afficherun résultat qui commence par une ligne comme ça :

```shell
[Appium] Welcome to Appium v2.0.0
```
Si vous obtenez ce type de sortie, le serveur Appium est opérationnel.
Vous pouvez l'arrêtez (CTRL-C).

### Installation du driver :  UiAutomator2 Driver

###### Configuration des exigences d'automatisation d'Android
1. Télécharger [Android SDK platform tools.](https://developer.android.com/studio/releases/platform-tools?hl=fr) (Il est conseillé de télécharger [Android Studio](https://developer.android.com/studio) pour faciliter la gestion des SDK)
2. Définissez une variable d'environnement pointant vers le répertoire sur le disque où les outils Android sont installés. Nous devons définir et conserver la variable d'environnement en tant que ANDROID_HOME
3. Utilisez le gestionnaire de SDK Android pour télécharger la plate-forme Android que vous voulez automatiser
4. Installez le JDK Java
5. Une fois le JDK installé, vous devrez trouver le chemin d'accès au répertoire de base du JDK tel qu'il a été installé sur votre système. Ce sera le répertoire qui contiendra les répertoires bin, include et autres. Le chemin doit être persistant en tant que variable d'environnement nommée JAVA_HOME, afin qu'Appium puisse trouver l'outil Java approprié requis pour fonctionner avec la plate-forme Android.
6. Utilisez Android Studio pour créer et lancer un appareil virtuel Android (un AVD, autrement appelé émulateur).
7. Avec l'émulateur ou l'appareil connecté, vous pouvez exécuter l'appareil adb  (via le binaire situé dans $ANDROID_HOME/platform-tools/adb) pour vérifier que votre appareil apparaît comme connecté.

### Installation du driver

```shell
appium driver install uiautomator2
```
Cela devrait afficher le résultat suivant:

```shell
Attempting to find and install driver 'uiautomator2'
✔ Installing 'uiautomator2' using NPM install spec 'appium-uiautomator2-driver'
Driver uiautomator2@2.0.5 successfully installed
- automationName: UiAutomator2
- platformNames: ["Android"]
```
L'exécution de cette commande localisera et installera la dernière version du pilote UiAutomator2, la rendant disponible pour l'automatisation.

Maintenant, redémarrez le serveur Appium ```run appium``` et vous devriez voir que le pilote nouvellement installé est répertorié comme disponible :

```shell
[Appium] Available drivers:
[Appium]   - uiautomator2@2.0.5 (automationName 'UiAutomator2')
```

## :memo: Ecrire un test en Java

* [Références du site](http://appium.io/docs/en/2.0/quickstart/test-java/)
* [Add Appium java client to your test framework](https://github.com/appium/java-client#add-appium-java-client-to-your-test-framework)
* Exemples d'utilisation :
  UiAutomator2
```java
UiAutomator2Options options = new UiAutomator2Options()
        .setUdid('123456')
        .setApp("/home/myapp.apk");
        AndroidDriver driver = new AndroidDriver(
        // The default URL in Appium 1 is http://127.0.0.1:4723/wd/hub
        new URL("http://127.0.0.1:4723"), options
        );
        try {
        WebElement el = driver.findElement(AppiumBy.xpath, "//Button");
        el.click();
        driver.getPageSource();
        } finally {
        driver.quit();
        }
```
## :memo: Ecrire un test en JS

* [Références du site](http://appium.io/docs/en/2.0/quickstart/test-js/)
* Utilisation de la librairie [WebdriverIO](https://webdriver.io/)
* Créez un nouveau répertoire de projet quelque part sur votre ordinateur, puis initialisez-y un nouveau projet Node.js :
```shell
npm init
```
Maintenant, installez le package webdriverio via NPM :
```shell
npm i --save-dev webdriverio
```
Une fois fait, votre fichier ```package.json`` devrait inclure une section comme celle-ci :

```json
{
  "devDependencies": {
    "webdriverio": "7.31.1"
  }
}
```
Il est maintenant temps de taper le test lui-même. Créez un nouveau fichier appelé ```test.js``` avec le contenu suivant :

```javascript
const {remote} = require('webdriverio');

const capabilities = {
  platformName: 'Android',
  'appium:automationName': 'UiAutomator2',
  'appium:deviceName': 'Android',
  'appium:appPackage': 'com.android.settings',
  'appium:appActivity': '.Settings',
};

const wdOpts = {
  host: process.env.APPIUM_HOST || 'localhost',
  port: parseInt(process.env.APPIUM_PORT, 10) || 4723,
  logLevel: 'info',
  capabilities,
};

async function runTest() {
  const driver = await remote(wdOpts);
  try {
    const batteryItem = await driver.$('//*[@text="Battery"]');
    await batteryItem.click();
  } finally {
    await driver.pause(1000);
    await driver.deleteSession();
  }
}

runTest().catch(console.error);
```
Avant d'exécuter le test, assurez-vous qu'un serveur Appium est en cours d'exécution dans une autre session de terminal, sinon vous obtiendrez une erreur indiquant que vous ne pourrez pas vous connecter à l'un d'entre eux.
Ensuite, vous pouvez exécuter le script:

```shell
node test.js
```
