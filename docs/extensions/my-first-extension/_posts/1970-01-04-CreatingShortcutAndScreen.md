---
layout: doc
permalink: /docs/extensions/my-first-extension/shortcut-and-screen
title: Creating screen and shortcut
section: My first extension
---

# Creating screen and shortcut
<hr />

Extension can have multiple screens in the app. Screens are [React components](https://facebook.github.io/react/docs/react-component.html) that represent a mobile screen. We want our extension to have 2 screens: one for the list of the restaurants and another for the details of one particular restaurant.

Since our app needs to know which screen to open first for the extension, we need to create a ***shortcut*** alongside our screen. This shortcut is the link to the starting screen of an extension. It's the item in the app navigation which opens the starting screen when tapped by a user.

The restaurant list is going to be the first screen, so let's create it ***and*** a shortcut:

```ShellSession
$ shoutem screen add List --shortcut Restaurants
Enter shortcut information:
Title: Restaurants

Screen `List` is created in file `app/screens/List.js`!
Shortcut `Restaurants` is created.
Shortcut `Restaurants` opens `List` screen.
File `app/extension.js` was modified.
File `extension.json` was modified.
```

Your `extension.json` was just modified:

```json{7-14}
#file: extension.json
{
  "name": "restaurants",
  "version": "0.0.1",
  "platform": "1.0.*",
  "title": "Restaurants",
  "description": "List of restaurants",
  "screens": [{
    "name": "List"
  }],
  "shortcuts": [{
    "name": "Restaurants",
    "title": "Restaurants"
    "screen": "@.List"
  }]
}
```

The screen and shortcut were added to `extension.json` inside arrays. The `name` property uniquely identifies these extension parts and the shortcut's `title` property is what will be shown in our app's navigation.

The `screen` property inside the `shortcuts` array references the screen that should open when a user taps on the shortcut. When referencing any extension part, we need to specify which extension it came from. The full name of the extension part follows this structure: `<developer-name>.<extension-name>.<extension-part-name>` (e.g. `{{ site.example.devName }}.restaurants.List)`. For extension parts within the same extension, use `@.<extension-part-name>` instead (e.g. `@.List`). Characters `@.` stands for `<developer-name>.<extension-name>.` of the current extension.

Shoutem CLI also created `app/screens/` which contains `List.js`:

```javascript
#file: app/screens/List.js
import React, {
  Component
} from 'react';

import {
  StyleSheet,
  Text,
  View
} from 'react-native';

export default class List extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.text}>Hello World!</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  text: {
    fontSize: 20,
  },
});
```

In React, `Component` specifies its UI in the `render` method.

## Extension in the app

The `app` folder from this extension will be bundled into our app with the rest of our extensions. If the extension is an `npm` package, make sure to install it in the `app` directory. Below is an example of installing [React Native swiper](https://github.com/leecade/react-native-swiper) (note the following two commands are just an example).

Locate the `app` folder and install the package by saving the dependency in `package.json`:

```ShellSession
$ cd app/
$ npm install --save react-native-swiper
```

This package would be installed upon bundling your extension into the app. You should be able to access it from within any file in the `app` directory.

## Exporting extension parts

Our app expects its extensions to export their parts (i.e. screens) from `app/index.js` (as is standard JS practice). Extensions act like libraries, meaning that other extensions can use these exported components. The convention is that `app/index.js` is the public API of an extension and shouldn't be changed very often.

Currently, `index.js` looks like this:

```JSX
#file: app/index.js
// Reference for app/index.js can be found here:
// http://shoutem.github.io/docs/extensions/reference/extension-exports

import * as extension from './extension.js';

export const screens = extension.screens;
```

Since `app/extension.js` is managed by the CLI, we shouldn't have to change it. When creating a screen, the CLI writes the screen's location in `app/extension.js`, which is then exported into `app/index.js`.

Upload your extension:

```ShellSession
$ shoutem push
Uploading `Restaurants` extension to Shoutem...
Success!
```

In Shoutem Builder, go to `Custom` within the `Add Screen` modal. You should see your `Restaurants` starting screen (shortcut) there. 

<p class="image">
<img src='{{ site.url }}/img/my-first-extension/add-modal-shortcut.png'/>
</p>

> #### Note
> If you don't see it, try refreshing your browser. 

Add your new extension to the navigation by clicking on `Restaurants`. 

Let's preview our app now. We can preview it in the Builder, but it might take some time to load. Every time you update an extension, we need to rebundle the whole app to include the change. It's much faster to use the **Shoutem Preview** app (available for [iOS]({{ site.shoutem.previewAppiOS }}) and [Android]({{ site.shoutem.previewAppAndroid }})) alongside Shoutem CLI, which can quickly bundle the extension updates.

Since the app is managed through the Builder, we need to `push` the extension to Shoutem after creating our screen and shortcut in order to add them to our app's navigation. Alternatively, if we're only changing our app's code, we can use `shoutem link` to bundle just the local code of our extension.

```ShellSession
$ shoutem link
Extension successfully linked. Please, kill the packager before running the app.
```

Once the extension is linked, run the app again to start [React Native packager](https://github.com/facebook/react-native/tree/master/packager):

```ShellSession
$ shoutem run
Select your app: Restaurants ({{ site.example.appId }})
Creating the bundle for your app...
...
```

This will output the QR code which you can scan with the Shoutem Preview app to preview the app on your device. 

> #### Note
> Here we're using the in-browser preview, but we highly recommend using a physical device. That way, when you edit your code, you can just shake your device while using the Shoutem Preview app and tap the "Reload" button. And if you `link` your extension, you won't need to `shoutem push` everytime you change the app code.

Here's the result:

<p class="image">
<img src='{{ site.url }}/img/my-first-extension/extension-hello-world.png'/>
</p>

Our app only has a simple  _Hello World_ screen, so let's add some UI components to the screen.
