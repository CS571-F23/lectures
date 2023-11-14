---
marp: true
footer: CS571 Building User Interfaces | Cole Nelson | Lecture 19: React Native 3
paginate: true
theme: uncover
style: |
  section {
    text-align: left
  }
  .center-info {
    text-align: center
  }
---


<br>

# **React Native 3**
### CS571: Building User Interfaces


<br>

#### Cole Nelson & Yuhang Zhao

---

### Today's Warmup

Check out [tomorrow's distinguished lecture](https://today.wisc.edu/events/view/186249)? 11 am @ CS1240. On the origins of JS & the web from a compilers/security perspective.



<div>

 - Download and import [today's Postman collection](https://github.com/CS571-F23/week11-rn3-example)!
 - Replace `ENTER_YOUR_BID` with your Badger ID.
 - Toy around with it! What's different from the HW6 API? Also, [read the docs](https://github.com/CS571-F23/hw9-api/blob/main/API_DOCUMENTATION.md).

</div>

---


### What will we learn today?

<div>

 - How to pass data using React Navigation?
 - How to perform "switch" navigation?
 - How to use overlays (modals)?
 - How to make our apps accessible?
 - How to store secrets on mobile devices?
 - How to do advanced gestures and animations?
 - How to use sensors?
 - How to deploy our apps?

</div>

---


### React Navigation

Last time we covered...

<div>

 - Stack Navigation
 - Tabs Navigation
 - Drawer Navigation

</div>

###

How do we pass data down?

<div>

 - Using context
 - Using [render callbacks](https://reactnavigation.org/docs/hello-react-navigation#passing-additional-props)

</div>

![bg right:33% w:350](figures/rn_nav.svg)

---

### Passing Data Down

**Problem:** Create a drawer with many tools in it.

```javascript
  const [tools, setTools] = useState([
    { name: 'hammer', weight: 2, dangerous: false },
    { name: 'screwdriver', weight: 1, dangerous: false },
    { name: 'sawzall', weight: 6, dangerous: true }
  ]);
```

![bg right:25%](figures/drawer.png)

---

### Passing Data Down

Displaying the tool data.

```jsx
export default function ToolScreen(props) {
  return <View>
    <Text>I am a {props.name}</Text>
    <Text>I weigh {props.weight} pounds</Text>
    {
      props.dangerous ?
        <Text>I am dangerous</Text> :
        <Text>I am not dangerous</Text>
    }
  </View>
}
```

![bg right:33% w:350](figures/drawer_tool.png)

---

### Creating Navigator

```jsx
    <NavigationContainer>
      <ToolDrawer.Navigator>
        <ToolDrawer.Screen name="Landing" component={LandingScreen}/>
        <ToolDrawer.Screen name="Hammer" component={ToolScreen}/>
        <ToolDrawer.Screen name="Screwdriver" component={ToolScreen}/>
        <ToolDrawer.Screen name="Sawzall" component={ToolScreen}/>
      </ToolDrawer.Navigator>
    </NavigationContainer>
```

❌ hardcoding tool names
❌ not passing tool data

[Snack Example](https://snack.expo.dev/@ctnelson1997/tooldrawer-static)

---

```jsx
<NavigationContainer>
  <ToolDrawer.Navigator>
    <ToolDrawer.Screen name="Landing" component={LandingScreen}/>
    {
      tools.map(tool => {
        return <ToolDrawer.Screen
          key={tool.name}
          name={tool.name}
          component={ToolScreen}
        />
      })
    }
  </ToolDrawer.Navigator>
</NavigationContainer>
```

Similar Solution: [BadgerChat](https://github.com/CS571-F23/hw6/blob/main/src/components/structural/BadgerApp.jsx)

❌ not passing tool data


---

```jsx
<NavigationContainer>
  <ToolDrawer.Navigator>
    <ToolDrawer.Screen name="Landing" component={LandingScreen}/>
    {
      tools.map(tool => {
        return <ToolDrawer.Screen key={tool.name} name={tool.name}>
          {(props) => <ToolScreen {...props} {...tool}/>}
        </ToolDrawer.Screen>
      })
    }
  </ToolDrawer.Navigator>
</NavigationContainer>
```

✅ correct way!

[Snack Example](https://snack.expo.dev/@ctnelson1997/tooldrawer-dynamic)

---

### Switch Navigation

There is a fourth, "informal" navigation.

⚠️ Do not use the one from React Navigation! It is very out-of-date. [It existed in React Navigation < 4.x](https://reactnavigation.org/docs/4.x/switch-navigator/).

---

### "Switch" Navigation

```javascript
isSignedIn ? (
  <>
    <Stack.Screen name="Home" component={HomeScreen} />
    <Stack.Screen name="Profile" component={ProfileScreen} />
    <Stack.Screen name="Settings" component={SettingsScreen} />
  </>
) : (
  <>
    <Stack.Screen name="SignIn" component={SignInScreen} />
    <Stack.Screen name="SignUp" component={SignUpScreen} />
  </>
);
```

[React Navigation AuthFlow](https://reactnavigation.org/docs/auth-flow/)

---

### "Switch Navigation"

**Premise**: perform a conditional render.

**If** the user is signed in, show them their needs feed.

**Else** give the user the option to sign in or sign up.


[Snack Solution](https://snack.expo.dev/@ctnelson1997/switch-navigation)

---

# HW9 Demo
Switch navigation and data passing.

---

### Modal

A secondary window.

```jsx
import { Modal } from 'react-native'
```

###

[Find My Badgers](https://snack.expo.dev/@ctnelson1997/findmybadgers-animated-modal)

![bg left](figures/modal_close.png)
![bg right](figures/modal_open.png)

---

### Modal

What you need...

<div>

 1. Something to open the modal
 2. Some content inside the modal
 3. Something to close the modal

</div>

###

We often manage whether the modal is open or closed using a state variable, e.g.

```jsx
const [modalVisible, setModalVisible] = useState(false);
```

---

### Modal

A modal is nothing more than a secondary overlay! You will need to style it.

```jsx
const styles = StyleSheet.create({
  modalView: {
    margin: 20,
    alignItems: 'center',
    shadowColor: '#000',
    shadowOpacity: 0.25,
    shadowRadius: 4,
    elevation: 5,
    // ...
```

---

### Modal Properties

 - `animationType`: 'slide', 'fade', 'none'
 - `onShow`: callback func
 - `onRequestClose`: callback func
 - `transparent`: true/false
 - `visible`: true/false often handled by a state variable

[Modal Docs](https://reactnative.dev/docs/modal) | [Modal Snack](https://snack.expo.dev/@ctnelson1997/modal-example)

---

# Accessibility

Making our apps usable for everyone.

---

<div class="center-info">

![w:800](figures/talkback.png)
[TalkBack - Accessibility on Android](https://www.youtube.com/watch?v=_1yRVwhEv5I)

</div>

---

<div class="center-info">

![w:800](figures/voiceover.png)
[VoiceOver - Accessibility on iOS](https://www.youtube.com/watch?v=qDm7GiKra28)

</div>

---

### Accessibility

`accessible` attribute indicates whether the component is an accessibility element and, if so, groups its children in a single  selectable element.

```jsx
<View accessible={false}>
  <Text>text one</Text>
  <Text>text two</Text>
</View>
```

[More OS-Specific Functionality](https://reactnative.dev/docs/accessibility)

---

### Accessibility

The `accessibilityLabel` attribute defines the screen reader description of the component whereas the `accessibilityHint` attribute helps the user understand what will happen if they perform an action.

```jsx
<Text
 accessible={true}
 accessibilityLabel={"Welcome to Find My Badgers!"}
 accessibilityHint={"Click on a badger to learn more about them."}
>
 Welcome to Find My Badgers!
</Text>
```

---

### Your turn!

Turn VoiceOver or TalkBack on and try to...

<div>

 - Send a message to a friend
 - Browse the news
 - Use an app/snack that we made
   - **Hint:** You'll do this in HW10! :)

</div>

###

[Accessible FindMyBadgers](https://snack.expo.dev/@ctnelson1997/findmybadgers-accessible)

---

# Secure Storage
Storing secrets (asynchronously!)

---

### What's so secret?

A JSON Web Token (JWT)!

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJpZCI6MjgsInVzZXJuYW1lIjoidGVzdDEyN
DU2NyIsImlhdCI6MTY5OTkxNDQxMiwiZXhwIj
oxNjk5OTE4MDEyfQ.Z0WiskxiQUTzEhHocjzg
4y5QaFEOCgg4Zz8sLszSMB0
```

What is this? A signed access token good for a set period of time.

---

### JSON Web Token

Base64 *encoded* (this is **NOT** *encryption*) segments separated by periods. These consist of a "header", "claims", and "signature"

What is Base64? An alphabet (much like binary) of 64 characters: `[A-Za-z0-9]`, `+`, `/`. `=` as a suffix.

[base64encode.org](https://www.base64encode.org/) | [base64decode.org](https://www.base64decode.org/)

---

### JSON Web Token

**Header**

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`

**Claims**

`eyJpZCI6MjgsInVzZXJuYW1lIjoidGVzdDEyNDU2NyIsImlhdCI6MTY5OTkxNDQxMiwiZXhwIjoxNjk5OTE4MDEyfQ`

**Signature**

`Z0WiskxiQUTzEhHocjzg4y5QaFEOCgg4Zz8sLszSMB0`

---

### JSON Web Token

**Header**

`{"alg": "HS256", "typ": "JWT"}`

**Claims**

`{"id": 28, "username": "test124567",`
`"iat": 1699914412, "exp": 1699918012 }`

**Signature**

Signed using `alg` with some server secret.

---

### Authentication

JWT is just one form of authentication, known as *stateless* authentication.

Session UUIDs are another popular way to handle authentication, e.g.

`642e4f9a-0d7a-44e5-ba60-e86822b4a8ac`

This is *stateful*, a database maps this UUID to the corresponding claims.

---

### Authentication

**Web Development**

<div>

 - Interface with the *user's browser.*
 - Prefer `HTTP-Only` cookies.

</div>

<br/>

**Mobile Development**

<div>

 - Interface with the *user's operating system.*
 - Prefer OS-level secure encrypted storage.

</div>

---

### Authentication

**Web:** Specify *option* `include: 'credentials'`

```javascript
fetch("https://example.com/api/submit", {
  method: "POST",
  credentials: "include",
  // ...
```


---

### Authentication

**Mobile**: Specify...

<div>

 - *header* `Authorization`
 - *value* `Bearer <JWT>`

</div>

###

```javascript
fetch("https://example.com/api/submit", {
  method: "POST",
  headers: {
    "Authorization": "Bearer eyJhbGciOiJIUzI1NiIs..."
  // ...
```

If the request has a body, don't forget `Content-Type`!

###

---


# HW9 Demo
Secrets and `Authorization`

Download and import today's [Postman collection](https://github.com/CS571-F23/week11-rn3-example).

---


### Secure Storage

**Uh oh!** We have to store a JWT?

 - React has no built-in way to handle credentials!😮
 - [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
   - Only works on Android and iOS -- not web!
 - Stores key/value pairs to *persistent* storage.
 - Up to 2KB of data per value (small!)
 - Cannot store emojis 😔
 - Additional options for passcode 🔒 and biometrics 👉

---

### Secure Storage
Secure storage is asynchronous in nature.

<div>

 - `getItemAsync(key)`
 - `setItemAsync(key, val)`
 - `deleteItemAsync(key)`

</div>

<br/>

**Note:** Key refers to a storage key (think HashMap) *not* an encryption key!

---

### Secure Storage

[expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/) shows an example of `async`/`await`... What is this?...

###

Same thing as `then` and `catch` on a `Promise` (think `fetch`)... just a slightly different syntax!

###

We'll cover `async` / `await` in DialogFlow!

---

# Secure Storage
[CS571-ifying](https://snack.expo.dev/@ctnelson1997/secure-store) the expo-secure-store example.

**Use your phone,** not the web!

---

# Advanced Gestures
Making use of diverse mobile inputs ✌ ☝ 🤚 👌 👍

---

![bg 70%](figures/gestures.jpg)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

[Image Source](https://cdn0.tnwcdn.com/wp-content/blogs.dir/1/files/2015/11/Screen-Shot-2015-11-09-at-14.36.39-520x340.jpg)

---

### Gestures
React Native [provides methods](https://reactnative.dev/docs/gesture-responder-system) to detect when and where the user's fingers move.

Higher-level gesture response libraries...

<div>

 - react-native's [PanResponder](https://reactnative.dev/docs/panresponder)
   - [See S22 Example](https://snack.expo.dev/@yuhangzhao/notifications-swipe)
 - [react-native-gesture-handler](https://www.npmjs.com/package/react-native-gesture-handler)
   - [See S23 Example](https://snack.expo.dev/@ctnelson1997/react-native-gesture-handler)
 - component libraries, e.g. [react-native-paper](https://reactnativepaper.com/)

</div>


--- 

### Component Library Gestures

**react-native**'s Button `onPress`

**react-native-paper**'s Card `onPress` and `onLongPress`

**react-native-elements**' Slider `onSliding`

**react-native-maps**' Marker `onDrag`

**react-navigation**'s Drawer gesture

---

### Sensors

Use `expo-sensors` instead of `react-native-sensors`

<div>

 - [Accelerometer](https://snack.expo.dev/@ctnelson1997/basic-accelerometer-usage)
 - [Barometer](https://snack.expo.dev/@ctnelson1997/basic-barometer-usage)
 - Gyroscope
 - [LightSensor](https://snack.expo.dev/@ctnelson1997/basic-light-sensor-usage)
 - Magnetometer
 - Pedometer

</div>

Not all devices have all sensors!

---

### Other Sensors

<div>

 - `expo-camera`
 - `expo-battery`
 - `expo-haptics`
 - `expo-av`
 - `expo-brightness` 

</div>

<br/>

Beware of [permissions](https://docs.expo.dev/guides/permissions/)!

---

# Deployment
Getting your app to a production environment.

---

### Deployment

iOS vs Android Market Share

| Region | iOS | Android | Other |
| --- | --- | --- | --- |
| USA | 54% | 45% | 1% |
| North America | 52% | 47% | 1% |
| Asia | 16% | 83% | 1% |
| Worldwide | 28% | 71% | 1% | 

Source: [GlobalStatCounter](https://gs.statcounter.com/os-market-share/mobile/worldwide)

---

### Deployment

Use Expo Application Services (EAS)

```bash
npm install -g eas-cli
```

```bash
eas build -p android
eas build -p ios
```

An `.apk` gets deployed to the Google Play Store

An `.ipa` gets deployed to the iOS App Store

---

### Deployment

**No web server for deployment!** Hosted on Google Play Store or iOS App Store.

App goes through a review process.

<div>

 - **Google Play Store:** Hours to Days
 - **iOS App Store:** Days to Weeks

</div>


---

### Deployment Considerations

<div>

 - Reliability
 - Performance
 - Monitoring
 - Business Value of Delivery
 - App Store Optimization (ASO)

</div>


---

### What did we learn today?

<div>

 - How to pass data using React Navigation?
 - How to perform "switch" navigation?
 - How to use overlays (modals)?
 - How to make our apps accessible?
 - How to store secrets on mobile devices?
 - How to do advanced gestures and animations?
 - How to use sensors?
 - How to deploy our apps?
 
</div>

---

# Questions?
