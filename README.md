Authy Hit iOS
=========

## Project Structure

The project is structured as follows:

    ./
	 |-Examples
   	 |---AuthyHitExample
   	 |-Library

1. **AuthyHitExample**: Provides a sample authy-hit. If you want to get started with the AuthyHit the best way is by taking a look at the sample project.
2. **Library**: Contains the actual authy-hit library code.


## Installation Instructions
1. AuthyHit requires access to the camera, so you have to make sure you allow it through your cellphone Settings, otherwise you will not be able to scan QR codes.
2. To add the library to your project, copy the folder 'include' and the file 'libAuthyHitLibrary.a' into your project folder directly from XCode. This files can be found in the 'Library' folder.
3. Required frameworks and libraries. Add the following frameworks and libraries in 'Linked Frameworks and Libraries' under the 'General' tab of your project
    - libAuthyHitLibrary.a (this library should have already been included automatically from the previous step)
    - AVFoundation.framework
    - libstdc++.dylib
    - SystemConfiguration.framework
    - libz.dylib
    - MobileCoreServices.framework

## Getting Started
This is a short tutorial that will help you getting started with `AuthyHit`. There is one sample project provided that you can checkout to get you started with AuthyHit. Follow the next steps in order to sign `AHRequest`s.

1. Configure the Scanner.
2. Configure the `AuthyHit` instance.
3. Fetch a list of requests.
4. Approve/deny a request.

## Configure the Scanner
AuthyHit requires that you can QR codes.

**Remember** that in the class definition your controller should import `AuthyHitScanner.h` and adopt the protocol `AuthyHitScannerDelegate`

1. To create QR code scanner simply present the AuthyHitScanner controller as follows:

  ```objectiveC
  - (void)showScannerController {
    AuthyHitScanner *scanner = [[AuthyHitScanner alloc] initWithDelegate:self];
    [self presentViewController:scanner animated:NO completion:nil];
  }
  ```

2. When the QR code has been scanned, the following method is called. This is a sample implementation


  ```objectiveC
  // Authy Hit Scanner Delegate
  -(void)scannerController:(AuthyHitScanner *)authyHitScanner didScanAccount: (AHAccount *)account {
    if(!account) {
      // The scanner don't recognize as authy hit account
    } else {
      // You have successfully scanned a QR Code
      // The QR code has been scanned and is ready to be used using the
      // AHAccount account param in the loadWithAccount method to
      // register the account.
      // TODO: Load the AuthyHit instance with AHAccount
    }

    [authyHitScanner dismissViewControllerAnimated:YES completion:nil];
  }

  -(void)scannerControllerDidCancel:(AuthyHitScanner *)authyHitScanner {
    // The scanner has been stopped by user
    [authyHitScanner dismissViewControllerAnimated:NO completion:nil];
  }
  ```

## Configure the AuthyHit instance
Now, you will need an instance of `AuthyHit`, this is the main class you will be using. The `AuthyHit` class provides methods for fetching the list of pending `AHRequest`s as well as methods to aprove/deny `Request`s.

**Remember** that in the class definition your controller should import `AuthyHit.h`

1. Create an instance of `AuthyHit`

  ```objectiveC
  AuthyHit *authyHitDefault = [AuthyHit authyHitDefault];
  ```

  The authyHitDefault provides a thread-safe singleton instance of AuthyHit, so several calls to `[AuthyHit authyHitDefault]` will return the same instance.

2. Load the `AuthyHit` instance. Before you can actually use `AuthyHit` you will have to *load* the instance. Loading is as simple as:

  ```objectiveC
  [authyHitDefault loadWithAccount:authyHitAccount handler:^(NSError *error){
    if(!error) {
      // The account has been successfully loaded into the AuthyHit
      // object.
      // After this step, you can now use the other methods in the
      // AuthyHit class
    }else {
      // An error has occurred, prompt the user to scan the token
      // again.
    }
  }];
  ```

**Warning** bear in mind that before calling methods from an `AuthyHit` instance such as the ones below you will first need to load the instance. XCode will not warn you nor an exception will be thrown if the `AuthyHit` instance has not been loaded.

## Fetch a list of requests
To obtain the list of all requests (pending, denied and accepted) simply call the `getAllRequests` method as follows:

```objectiveC
[authyHitDefault getAllRequests:^(NSArray *array, NSError *error){
  if(!error){
    //Requests have been successfully fetched.
  } else {
    //An error has occurred, prompt the error.
  }
}];
```

Remember the array of requests contains `AHRequest` objects. You can see its properties directly from the `AHRequest.h` file included inside the 'include' folder added earlier.

## Approve/deny a request
To approve or deny a request simply call the `approveRequest` or `denyRequest` methods as follows

```objectiveC
[authyHitDefault approveRequest:pendingRequest handler:^(NSError
*error){
  if(error){
    //An error has occurred, prompt the error.
  } else {
    //The request has been approved.
  }
}];
```
