# Instagram4Android

[![](https://jitpack.io/v/charlieAndroidDev/Instagram4Android.svg?style=flat-square)](https://jitpack.io/#charlieAndroidDev/Instagram4Android)  [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e7/Instagram_logo_2016.svg/1024px-Instagram_logo_2016.svg.png" alt="Drawing" width="200" height="200"/>

This library directly talks to the private Instagram API, anything (well quite a lot) you can do on the IG app you can replicate with this library.  You also bypass the public API's rate limits. 

## Credit
This was originally a port of a popular [Java repository](https://github.com/brunocvcunha/instagram4j) that provides access to Instagrams' private API.  This has been changed around to use OkHttp and various other fixes have been made to make it work with Android.  I will be updating this with more endpoints and so on as time goes on, stories will be added soon.

Project not in any way afilliated with Instagram.

## Fair Use
Do NOT use this to spam anyone at all.<br/>
Do NOT use this for any illegal activities.

## Installation

Add the following to your root ```build.gradle``` file:
```Gradle
allprojects {
    repositories {
	...
	maven { url 'https://jitpack.io' }
    }
}
```

Then add the following to your app level ```build.gradle``` file, use the version number of the latest release:
```Gradle
dependencies {
    compile 'com.github.charlieAndroidDev:Instagram4Android:v0.1.17'
}
```

## Instructions

### Login
```Java
Instagram4Android instagram = Instagram4Android.builder().username(username).password(password).build();
instagram.setup();
instagram.login();
```

You must call ```setup()``` before ```login()``` otherwise an exception is thrown.

Below is my implementation of logging in to Instagram in my presenter (MVP) using RxJava2 and RxAndroid:

```Java
private void loginToInstagram(final String username, final String password) {

        Instagram4Android instagram = Instagram4Android.builder().username(username).password(password).build();

        attemptLogin(instagram, username, password)
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(instagramLoginResult -> {

                    if(instagramLoginResult.getStatus().equals(view.getStringResource(R.string.login_success))) {
                        view.loginFinished(instagram);
                    } else {
                        view.loginFailed(instagramLoginResult.getMessage());
                    }

                });

    }

private Observable<InstagramLoginResult> attemptLogin(final Instagram4Android instagram) {

        Observable<InstagramLoginResult> observable = Observable.create(observableEmitter -> {

            instagram.setup();
            observableEmitter.onNext(instagram.login());

        });

        return observable;

    }       
```

The String resource ```R.string.login_success``` is ```"ok"```.

### Get user info

```Java
InstagramSearchUsernameResult result = instagram.sendRequest(new InstagramSearchUsernameRequest("example_username"));
InstagramUser user = result.getUser();
```

Send the request using your ```Instagram4Android``` instance, and then call ```getUser()``` on the result.  Now poke around and see what info you can get from that ```user``` object.

### Follow user

```Java
instagram.sendRequest(new InstagramFollowRequest(user.getPk()));
```

Very simple.

## Being updated currently...
