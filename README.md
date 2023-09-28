<p align="center">
<img alt="logo image" width="250" src="https://trapp-documentation.s3.eu-central-1.amazonaws.com/LogoMakr-7gMmq0.png"  />
</p>

# TrAPP Android Library

TrAPP is a platform that allow to manage all the translations of mobile and web apps. This repository
contains the library to integrate TrAPP with Android mobile apps.
The library handles the following features:

- Authentication
- Synchronization
- Translation

## Overview

Every development platforms comes with the support of localization, to be able to translate text based on the preferences of the user's device.</br>
The main issue is that every platform uses different formats and the localization files must be added inside the application bundles, requiring an
update every time a string is changed.</br>
Moreover, keeping the files of every platform up to date is a constant task that can be time consuming and prone to errors.</br>
TrAPP helps keeping all the translations in a single point, always up to date and available to every component of the team.</br>
This Kotlin library allows Android developers to retrieve and use the translations uploaded on the platform with a simple integration, without worrying about the technical implementation.

## Configuration

To import the project go to `settings.gradle.kts` and insert our maven repository to the `dependencyResolutionManagement.repositories` section:
``` kotlin
dependencyResolutionManagement {
    repositories {
        ...
        maven { url = uri("https://maven.pkg.github.com/zero12srl/TrAPP-lib-Android") }
    }
}
```
Then in the `gradle.settings.kts` of the module, add the dependency
``` kotlin
dependencies {
    implementation("it.zero12:trappsync:{trappsyncVersion}")
}
```
If you are using TOML, these are the settings
``` toml
[versions]
trappsync = {trappsyncVersion}

[libraries]
trappsync = { group = "it.zero12", name = "trappsync", version.ref = "trappsync" }
```

## Quick start

To access the TrAPP services you need use the `Translator` object. This is a singleton that holds the instance of `TranslatorService`. This instance than needs to be configured by using the function `init`.
``` kotlin
try {
    Translator.init(
        TranslatorConfiguration(
            apiKey = "your API Key",
            primaryLanguage = "your primary language tag"
        )
    )
} catch (e: DoubleConfigurationException) {
    // handle exception
}

```
It's suggested to initialize the translator in the `MainActivity` as it would then be accessible from everywhere in the app.

### Automatic Locale detection

The library is capable of automatically detect the device locale and adapt consequently its behavior.
To enable this feature, simply set the value `localeDetectionActive` to `true` in the `TranslatorConfiguration`.
``` kotlin
Translator.init(
    TranslatorConfiguration(
        apiKey = "your API Key",
        primaryLanguage = "your primary language tag",
        localeDetectionActive = true
    )
)
```

### Retrieve the TranslatorService instance

You can retrieve the instance by accessing it through the `Translator`, after the initialization, from everywhere in the app.
``` kotlin
val translatorService = Translator.instance
```

### Sync
To synchronize the local database with the data in the server, the `sync` function must be used.
``` kotlin
translatorService.sync()
```
The synchronization operation should be done at least once every time the app is started, possibly as first operation, to ensure that the strings are available before using them. Depending on the behavior of the app, the `sync` operation can also be done multiple time during the lifecycle of the app, without issues.

### Localization

After the synchronization, the localization keys can be translated.
``` kotlin
translatorService.translate("test.plain")
```
#### Templated Strings
A translation can contain some placeholders that need to be substitute with some values. To achieve this, the translation method accepts an array of String that contains the substitutions to the placeholders. The order of the array will be used to order the substitutions. For example the translation for the following strings will be:
``` json
"test.substring1": "Lorem {{0}} dolor sit amet, {{1}} adipiscing elit."
"test.substring2": "Lorem {{1}} dolor sit amet, {{0}} adipiscing elit."
```
``` kotlin
val string1 = translator.translate("test.substring1", "ipsum", "consectetur")
// string1 = "Lorem ipsum dolor sit amet, consectetur adipiscing elit."

val string2 = translator.translate("test.substring2", "ipsum", "consectetur")
// string2 = "Lorem consectetur dolor sit amet, ipsum adipiscing elit."
```
> **_WARNING:_** This method might throw exceptions. Check the in-code documentation for further information.

### Change language

If you want to programmatically change change the language of the translations, the function `setLanguage` will allow to set and synchronize a new language.

``` kotlin
translator.setLanguage(languageCode = "en-US")
```
> **_WARNING:_** This method might throw exceptions. Check the in-code documentation for further information.

> **_NOTE:_** If the `setLanguage` function is successful, the automatic locale detection will be disabled.

This function needs a string indicating the `languageCode` that needs to be set. This string should follow the language ISO standard, with two characters for the language, four optional characters for the script, and two characters for the country, like `en-US` or `zh-HANS-CN`.
The same language must be set also in the TrAPP online platform.

### Backup file

Since the connection to the network is not always guaranteed during the first launch of the app, there's the possibility to provide the library a `JSON` file containing the translations of all the strings.
Download this file from the web platform and add it to the module's [assets](https://stackoverflow.com/a/27673773). 
To elaborate this file to the library, you must provide, during the configuration, the parsed content of the file. To ease this process a `readFile()` function is provided by the library.

``` kotlin
val fileContent = readFile(context, "name of the file")

Translator.init(
    TranslatorConfiguration(
        apiKey = "your API Key",
        primaryLanguage = "your primary language tag",
        backupFileContent = fileContent
    )
)
```

## License

This library is released under the MIT license. See [LICENSE](LICENSE) for details.

## Issues

To report bugs go to [the issues page](https://github.com/zero12srl/TrAPP-lib-Android/issues) and check if the bug has already been reported. Otherwise, open a new issue and follow the provided reporting template.

## About

Made with ❤️ by zero12. 
The names and logo are trademarks of zero12 srl.