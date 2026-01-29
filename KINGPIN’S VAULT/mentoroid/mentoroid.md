firebase-id : mentoroid-315b1
backend : npm install firebase-admin
frontend : npm install firebase

```
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: "AIzaSyBOuqk_TVPfYjogKJtmdQzR_q0gEPJ7El0",
  authDomain: "mentoroid-315b1.firebaseapp.com",
  projectId: "mentoroid-315b1",
  storageBucket: "mentoroid-315b1.firebasestorage.app",
  messagingSenderId: "705699761412",
  appId: "1:705699761412:web:541bf7abb6e36be3df9451",
  measurementId: "G-CNW2ZNV9B6"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);
```

forntend: firebase.js is required ---> its for getting that popup and for stroing the credentials 
backend : firebaseadmin.js is required ---> for retriving the data from the .env

==providing permission for the website== ---> authentication -> signin methods -> google

URI-pass: IWcvTjOxyTKIoCqn