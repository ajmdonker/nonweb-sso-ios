SURFnet iOS SSO Library
===================================================

Now you can easily integrate the [SURFnet](https://www.surf.nl) SSO process flow in your iOS application by using this library.

If you want to see it in action, check out the sample app in this repository.

For background information and investigation into the best practices that were implemented in this SDK, see [this page](https://github.com/SURFnet/nonweb-demo/wiki/iOS).


HOW TO USE THE LIBRARY
-----

Before you can use the library you need to have your `consumerId` registered by SURFnet and have received your `consumer secret`.

### 1. Include library

If you are using CocoaPods, add the following line to your Podfile:

    pod 'SSOService-iOS'

alternatively, add the code from the SSOService folder into your project directly.

### 2. Register URL scheme

In the Info.plist of your app, add an entry in CFBundleURLTypes with the value for CFBundleURLSchemes set to the one registered with Surfnet.
This should look something similar like this:

    <key>CFBundleURLTypes</key>
    <array>
		<dict>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>sfoauth</string>
			</array>
			<key>CFBundleURLName</key>
			<string>sf<YourRedirectUriHere></string>
		</dict>
    </array>

### 3. Showing authorization screen

When you want to let the user login, create an authorization view controller and display it.
NOTE: Don't forget to replace the `@"your consumer id"` with the acquired Consumer ID from SURFnet.

    - (IBAction)authorize:(id)sender {
        UIViewController *authorizationViewController = [SSOService authorizationViewControllerForEndpoint:@"https://nonweb.demo.surfconext.nl/php-oauth-as/authorize.php" consumerId:@"your consumer id" state:@"demo" delegate:self];
        [self presentViewController:authorizationViewController animated:YES completion:nil];
    }

You should also implement a few delegate callback methods.

add the `SSOAuthorizationViewControllerDelegate` to your class, f.e.:

    @interface ViewController () <SSOAuthorizationViewControllerDelegate>
    
Implement the delegate callbacks:

    #pragma mark - SSOAuthorizationViewControllerDelegate

    - (void)dismissAuthorizationViewController:(UIViewController *)viewController animated:(BOOL)animated completion:(void (^)(void))completion {
        [self dismissViewControllerAnimated:animated completion:completion];
    }

    - (void)authorizationViewController:(UIViewController *)viewController didSucceedWithToken:(NSString *)token {
        UIAlertController *successAlert = [UIAlertController alertControllerWithTitle:NSLocalizedString(@"Authorization succeeded", @"") message:[NSString stringWithFormat:NSLocalizedString(@"Access token: %@", @""), token] preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:NSLocalizedString(@"OK", @"") style:UIAlertActionStyleDefault handler:nil];
        [successAlert addAction:okAction];
        [self presentViewController:successAlert animated:YES completion:nil];
    }

    - (void)authorizationViewController:(UIViewController *)viewController didFailWithError:(NSError *)error {
        UIAlertController *errorAlert = [UIAlertController alertControllerWithTitle:NSLocalizedString(@"Authorization error", @"") message:error.localizedDescription preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:NSLocalizedString(@"OK", @"") style:UIAlertActionStyleDefault handler:nil];
        [errorAlert addAction:okAction];
        [self presentViewController:errorAlert animated:YES completion:nil];
    }

### 4. Receiving access token

The access token will be delivered to your app via a callback URL. In your application delegate, implement the method to handle the URL.

    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options {
        BOOL urlHandled = [SSOService handleURL:url callbackScheme:@"sfoauth"];
        if (!urlHandled) {
            // Handle any other URLs
        }
        return urlHandled;
    }



[CHANGELOG](https://github.com/SURFnet/nonweb-sso-ios/blob/master/CHANGELOG.md)
-----

Current version: 0.0.1


DEVELOPED BY
------------

* SURFnet - [https://www.surf.nl](https://www.surf.nl)
* Egeniq - [https://www.egeniq.com/](https://www.egeniq.com/)


LICENSE
-----

    Copyright 2015 SURFnet BV, The Netherlands

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.