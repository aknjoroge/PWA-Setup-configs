## (PWA) Progressive Web App Setup configs

---

#### Brief

#### Bring native-like capabilities and reliability to your web applications.

#### installed apps by anyone, anywhere, on any device with a single codebase.

---

### Key Features

1. Install your website app accross all platforms Desktop/android/iOS
2. System Push Notifications
3. Website can load while offline

### Steps

#### 1. You need three files

- A javascript where the service worker code will be
- A html to show when the user is offline
- A manifest containing your app definition

#### NOTE : PWA will work for users whos browser supports them

---

## Base

In your index.html file / main file being loaded by the server, you need to register the service worker.

`index.html`

```js
if ("serviceWorker" in navigator) {
  // Check if the client browser supports serviceWorker
  window.addEventListener("load", () => {
    // initialize serviceWorker on page load
    navigator.serviceWorker
      .register("./serviceworker.js") //This is where you define your serviceWorker.js file
      .then((reg) => console.log("Success: ", reg.scope))
      .catch((err) => console.log("Failure: ", err));
  });
}
```

---

### Service Worker configuration

#### Copy everything and modify only the fields stated, Trust me everything works

- For details view [this video](https://www.youtube.com/watch?v=IaJqMcOMuDM)

create your config javascript file, the one you registered in this line of code `navigator.serviceWorker.register("./serviceworker.js")` mine is called `serviceworker.js`.
<br/>
Copy code below

`serviceworker.js`

```js
const CACHE_NAME = "V.1.0"; //this is an Identifier of your cache, you should update it once you update your website and redeploy to example V.1.1
const filesToCache = ["index.html", "logo.png", "offline.html"]; //contains the file to cache, this is relative to where this serviceworker.js file is located, thus can also have const filesToCache = ["assets/style.css"]

const self = this;

// Install EVENT
self.addEventListener("install", (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => {
      console.log("Opened cache");

      return cache.addAll(filesToCache); //adding our files to cache
    })
  );
});

// Listen for requests EVENT
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then(() => {
      return fetch(event.request).catch(() => caches.match("offline.html")); // This line will load our offline page when the client has no internet connection, assets to be used with this page should also be cached
    })
  );
});

// Activate EVENT
// This will remove old versions of the cache from the website, this is why we should update the version number after an update
self.addEventListener("activate", (event) => {
  const cacheWhitelist = [];
  cacheWhitelist.push(CACHE_NAME);
  event.waitUntil(
    caches.keys().then((cacheNames) =>
      Promise.all(
        cacheNames.map((cacheName) => {
          if (!cacheWhitelist.includes(cacheName)) {
            return caches.delete(cacheName); //remove old cache
          }
        })
      )
    )
  );
});
```

---

### Offline HTML View Config

We cache an `offline.html` page that we show to the client when they dont have an internet connection.

The page is delivered in this line `return fetch(event.request).catch(() => caches.match("offline.html"))`

Thus edit this file to display an offline warning to the user

#### Note: Preferably dont use external css cause that will just increase the files to cache, write the styles in <head>

`offline.html`

```html
<div>
  <h2>
    <span>Hi! You are offline, please connect to the internet.</span>
  </h2>
</div>
```

### Manifest Config

#### Edit the manifest file providing you details

### Note

- Put correct details about the images in the `icons` array, confirm the image dimensions in the properties tab of your operating system

```json
{
  "short_name": "aknjoroge App", //short name to be viewed when installed

  "name": "aknjoroge | Client Portal", //full name
  "icons": [
    //define your images
    {
      "src": "favicon.ico",
      "sizes": "1082x1082",
      "type": "image/x-icon"
    },

    {
      "src": "logo.png",
      "type": "image/png",
      "sizes": "1082x1082"
    }
  ],
  "start_url": ".",
  "display": "standalone",
  "theme_color": "#000000", //define theme color
  "background_color": "#ffffff" //define backround color
}
```

---

### Done

#### Those are the basic configs for setting up a PWA

1. initialize the service worker and register the worker javascript file
2. configure the serviceWorker
3. configure the manifest.json file

---

### Push Notifications

#### Send notifications to a client

### Basic steps

#### 1.Ask for permision

`asking for permision`

```js
window.addEventListener("load", function () {
  // asking for permision when the browser loads, can use other events
  Notification.requestPermission().then((result) => {
    if (result === "granted") {
      aFunctionExample(); //if permision is granted, call this function
    }
  });
});
```

#### 2. Send the notification

```js
function aFunctionExample() {
  const notifTitle = "Test Notification";
  const notifBody = `Working with akNjoroge.`;
  const notifImg = `logo.png`;
  const options = {
    body: notifBody,
    icon: notifImg,
  };
  new Notification(notifTitle, options);
}
```

### To use Push notifications refere to [this article in MDN](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Re-engageable_Notifications_Push)

ex

```js
self.addEventListener("push", function (event) {
  const payload = event.data ? event.data.text() : "no payload";
  event.waitUntil(
    self.registration.showNotification("ServiceWorker Cookbook", {
      body: payload,
    })
  );
});
```
