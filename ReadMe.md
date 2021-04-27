# Google Sign In With JS
To start with, all you need is an index.html file.
```HTML
<!DOCTYPE html>
<html>

<head>
  <title>Google Auth Demo</title>
</head>
<body>
  <h1>Welcome to the Demo</h1>
</body>
</html>
```
## Add the Google Sign-In Button
Before adding in the button, you first need to create a client ID/secret which is a way of identifying that you, the developer, are allowing users to get the identity information from Google and delivered to your website.
### Creating credentials for Sign-In
* Go to the [Google API Console](https://console.developers.google.com/apis/dashboard)
* Create a new project, or use an existing project if you already have one set up.
* Then click on Credentials -> Create Credentials -> OAuth Client ID <br>
* Fill the fields  
  Name: google-auth-demo <br>
  Authorized Javascript Origins: http://localhost:portNumber <br>
  Authorized Redirect URIs: empty <br>
  
### Add the library + credentials + button to your HTML
In your index.html page, add the following to your HTML page:
```HTML
<head>
  ...
  <meta name="google-signin-client_id" content="your-client-id-goes-here">
  <script src="https://apis.google.com/js/platform.js" async defer></script>
</head>
<body>
  <h1>Welcome to the Demo</h1>
  <div class="g-signin2"></div>
</body>
```
The script tag grabs the library from Google that reads your <meta> tag to use as the Client ID, and then automatically re-styles the button with the CSS class .g-signin2.

At this point, if you refresh your page, you should see a pretty Sign-In button. If you click it, you'll go through your Google Login flow in a popup, and you'll finally land back on your site, and the button will say, "Signed In".

### Identify the user
Update the g-signin2 div to include the data-onsuccess attribute:
```HTML
<div class="g-signin2" data-onsuccess="onSignIn"></div>
```
That attribute contains the name of the function that will be called once a user has successfully logged in with Google.

* Create a function called "onSignIn".
```HTML
<body>
  ...
  <script>
    function onSignIn(googleUser) {
      // get user profile information
      console.log(googleUser.getBasicProfile())
    }
  </script>
</body>
```
Your onSignIn function will be called with an argument containing the information provided by Google.

If you open up your javascript console, you'll see the users information printed:
```javascript
{
  Eea: "108716981921401766503"
  Paa: "https://lh3.googleusercontent.com/-y_ba58pC4us/AAAAAAAAAAI/AAAAAAAAAYE/wMGKOxlWR90/s96-c/photo.jpg"
  U3: "*****@gmail.com"
  ig: "**** ****"
  ofa: "*****"
  wea: "****"
}
```
You can either get the data directly by doing
```javascript
googleUser.getBasicProfile()['U3']
 ```
or use the more human-readable approach by using the functions like 
```javascript
googleUser.getBasicProfile().getName()
 ```
or
```javascript
googleUser.getBasicProfile().getEmail(). 
```
For more details visit [Javscript Client API reference docs](https://developers.google.com/identity/sign-in/web/reference#googleusergetid)

### SignOut
Add a sign out button after the user has signed in by adding a button in your index.html and the click handler in your javascript.
```HTML
<body>
...
  <button onclick="signOut()">Sign out</button>
</body>
```
```javascript
function signOut() {
  gapi.auth2.getAuthInstance().signOut().then(function() {
    console.log('user signed out')
  })
}
```
Now we want to display the user information on the page on Login and the information should
vanish on logout
* Create a new div for displaying user details
 ```HTML
 <div id="userDetailsDiv"></div>
 ```
* update the onSignIn function
```javascript
function onSignIn(googleUser) {
        // get user profile information
        console.log(googleUser.getBasicProfile());
        let userDetails=googleUser.getBasicProfile();
        //reference the userDetails Div
        let userDetailsDiv = document.getElementById('userDetailsDiv');
        //Create a new paragraph element
        let name=document.createElement('p');
        // Create a new Text Node and store the username
        let nameText = document.createTextNode(userDetails.getName());
        // Append the text node to the paragraph element
        name.appendChild(nameText);
        // Append the paragraph element to the userDetails Div
        userDetailsDiv.appendChild(name);
        //Append image
        let img = document.createElement("img");
        img.src = userDetails.getImageUrl();
        userDetailsDiv.appendChild(img);
        //Append email
        let email=document.createElement('p');
        let emailText=document.createTextNode(userDetails.getEmail());
        email.appendChild(emailText);
        userDetailsDiv.appendChild(email);
        // Append sign out button . We are displaying it only when the user is signed in.
        let signOutButton = document.createElement("BUTTON");
        signOutButton.innerHTML = "Sign Out";
         signOutButton.onclick=signOut;
        userDetailsDiv.appendChild(signOutButton);
        document.body.appendChild(userDetailsDiv);
    }
```
* update the signOut function
```javascript
function signOut() {
        gapi.auth2.getAuthInstance().signOut().then(function() {
            console.log('user signed out');
            // Reference the userDetails Div
            let userDetailsDiv = document.getElementById('userDetailsDiv');
            // Remove all the child Nodes of the Div
            userDetailsDiv.innerHTML="";

        })
    }
```
Here is the complete code
```javascript
<html lang="en">
<head>
    <meta name="google-signin-scope" content="profile email">
    <meta name="google-signin-client_id" content="***********************************.apps.googleusercontent.com">
    <script src="https://apis.google.com/js/platform.js" async defer></script>
    <link rel="stylesheet" href="styles.css">
    <title>Google Sign In</title>
</head>
<body >

<h1>Welcome to the Demo of Google Sign In</h1>
<hr>
<div class="g-signin2" data-onsuccess="onSignIn"></div>
<div id="userDetailsDiv"></div>
<script>

    function onSignIn(googleUser) {
        // get user profile information
        console.log(googleUser.getBasicProfile());
        let userDetails=googleUser.getBasicProfile();
        let userDetailsDiv = document.getElementById('userDetailsDiv');
        let name=document.createElement('p');
        let nameText = document.createTextNode(userDetails.getName());
        name.appendChild(nameText);
        userDetailsDiv.appendChild(name);
        let img = document.createElement("img");
        img.src = userDetails.getImageUrl();
        userDetailsDiv.appendChild(img);
        let email=document.createElement('p');
        let emailText=document.createTextNode(userDetails.getEmail());
        email.appendChild(emailText);
        userDetailsDiv.appendChild(email);
        let signOutButton = document.createElement("BUTTON");
        signOutButton.innerHTML = "Sign Out";
         signOutButton.onclick=signOut;
        userDetailsDiv.appendChild(signOutButton);
        document.body.appendChild(userDetailsDiv);
    }
    function signOut() {
        gapi.auth2.getAuthInstance().signOut().then(function() {
            console.log('user signed out');
            let userDetailsDiv = document.getElementById('userDetailsDiv');
            userDetailsDiv.innerHTML="";

        })
    }
</script>

</body>


</html>
```

