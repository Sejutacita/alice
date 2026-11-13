<p align="center">
<img src="https://raw.githubusercontent.com/jhomlala/alice/master/media/logo.png" width="250px">
</p>

# Alice

[![pub package](https://img.shields.io/pub/v/alice.svg)](https://pub.dartlang.org/packages/alice)
[![pub package](https://img.shields.io/github/license/jhomlala/alice.svg?style=flat)](https://github.com/jhomlala/alice)
[![pub package](https://img.shields.io/badge/platform-flutter-blue.svg)](https://github.com/jhomlala/alice)

Alice is an HTTP Inspector tool for Flutter which helps debugging http requests. It catches and stores http requests and responses, which can be viewed via simple UI. It is inspired from [Chuck](https://github.com/jgilfelt/chuck) and [Chucker](https://github.com/ChuckerTeam/chucker).

<table>
  <tr>
    <td>
		<img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/1.png">
    </td>
    <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/2.png">
    </td>
    <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/3.png">
    </td>
    <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/4.png">
    </td>
     <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/5.png">
    </td>
    <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/6.png">
    </td>
  </tr>
  <tr>
    <td>
	<img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/7.png">
    </td>
    <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/8.png">
    </td>
    <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/9.png">
    </td>
    <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/10.png">
    </td>
    <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/11.png">
    </td>
     <td>
       <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/12.png">
    </td>
  </tr>

</table>

**Supported Dart http client plugins:**

- Dio
- HttpClient from dart:io package
- Http from http/http package
- Chopper
- Generic HTTP client

**Features:**  
✔️ Detailed logs for each HTTP calls (HTTP Request, HTTP Response)  
✔️ Inspector UI for viewing HTTP calls  
✔️ Save HTTP calls to file  
✔️ Statistics  
✔️ Notification on HTTP call  
✔️ Support for top used HTTP clients in Dart  
✔️ Error handling  
✔️ Shake to open inspector  
✔️ HTTP calls search

## Install

1. Add this to your **pubspec.yaml** file:

```yaml
dependencies:
  alice: ^0.2.1
```

2. Install it

```bash
$ flutter packages get
```

3. Import it

```dart
import 'package:alice/alice.dart';
```

## Usage
### Alice configuration
1. Create Alice instance:

```dart
Alice alice = Alice();
```

2. Add navigator key to your application:

```dart
MaterialApp( navigatorKey: alice.getNavigatorKey(), home: ...)
```

You need to add this navigator key in order to show inspector UI.
You can use also your navigator key in Alice:

```dart
Alice alice = Alice(showNotification: true, navigatorKey: yourNavigatorKeyHere);
```

If you need to pass navigatorKey lazily, you can use:
```dart
alice.setNavigatorKey(yourNavigatorKeyHere);
```
This is minimal configuration required to run Alice. Can set optional settings in Alice constructor, which are presented below. If you don't want to change anything, you can move to Http clients configuration.

### Additional settings

You can set `showNotification` in Alice constructor to show notification. Clicking on this notification will open inspector.
```dart
Alice alice = Alice(..., showNotification: true);
```

You can set `showInspectorOnShake` in Alice constructor to open inspector by shaking your device (default disabled):

```dart
Alice alice = Alice(..., showInspectorOnShake: true);
```

If you want to use dark mode just add `darkTheme` flag:

```dart
Alice alice = Alice(..., darkTheme: true);
```

If you want to pass another notification icon, you can use `notificationIcon` parameter. Default value is @mipmap/ic_launcher.
```dart
Alice alice = Alice(..., notificationIcon: "myNotificationIconResourceName");
```

If you want to limit max numbers of HTTP calls saved in memory, you may use `maxCallsCount` parameter.

```dart
Alice alice = Alice(..., maxCallsCount: 1000));
```


If you want to change the Directionality of Alice, you can use the `directionality` parameter. If the parameter is set to null, the Directionality of the app will be used.
```dart
Alice alice = Alice(..., directionality: TextDirection.ltr);
```
### HTTP Client configuration
If you're using Dio, you just need to add interceptor.

```dart
Dio dio = Dio();
dio.interceptors.add(alice.getDioInterceptor());
```


If you're using HttpClient from dart:io package:

```dart
httpClient
	.getUrl(Uri.parse("https://jsonplaceholder.typicode.com/posts"))
	.then((request) async {
		alice.onHttpClientRequest(request);
		var httpResponse = await request.close();
		var responseBody = await httpResponse.transform(utf8.decoder).join();
		alice.onHttpClientResponse(httpResponse, request, body: responseBody);
 });
```

If you're using http from http/http package:

```dart
http.get('https://jsonplaceholder.typicode.com/posts').then((response) {
    alice.onHttpResponse(response);
});
```

If you're using Chopper. you need to add interceptor:

```dart
chopper = ChopperClient(
    interceptors: alice.getChopperInterceptor(),
);
```

If you have other HTTP client you can use generic http call interface:
```dart
AliceHttpCall aliceHttpCall = AliceHttpCall(id);
alice.addHttpCall(aliceHttpCall);
```

## Show inspector manually

You may need that if you won't use shake or notification:

```dart
alice.showInspector();
```

## Saving calls

Alice supports saving logs to your mobile device storage. In order to make save feature works, you need to add in your Android application manifest:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

## Extensions
You can use extensions to shorten your http and http client code. This is optional, but may improve your codebase.
Example:
1. Import:
```dart
import 'package:alice/core/alice_http_client_extensions.dart';
import 'package:alice/core/alice_http_extensions.dart';
```

2. Use extensions:
```dart
http
    .post('https://jsonplaceholder.typicode.com/posts', body: body)
    .interceptWithAlice(alice, body: body);
```

```dart
httpClient
    .postUrl(Uri.parse("https://jsonplaceholder.typicode.com/posts"))
    .interceptWithAlice(alice, body: body, headers: Map());
```


## Example
See complete example here: https://github.com/jhomlala/alice/blob/master/example/lib/main.dart
To run project, you need to call this command in your terminal:
```bash
flutter pub run build_runner build --delete-conflicting-outputs
```
You need to run this command to build Chopper generated classes. You should run this command only once,
you don't need to run this command each time before running project (unless you modify something in Chopper endpoints).
<p align="center">
 <img width="250px" src="https://raw.githubusercontent.com/jhomlala/alice/master/media/13.png">
<p align="center">

<!-- deploy-notice:start v1 (from andhika-sudarman/infra gates/deploy-notice/section.md; change it there, not here) -->
## Before you deploy or release: Claude (F) deploys this repo too

People and the owner's Claude workers (F) both deploy and release `Sejutacita/alice`. Before any staging or production
step (a release branch cut, a cherry-pick onto a release branch, a tag or GitHub release, an image tag change, a
production deploy, a migration, an Unleash flag switch), check what is already in flight, so neither side overwrites
the other:

1. **This repo:** the open PRs into `dev`, the staging build branches (`dev-*`, `dev_*`) and any release or hotfix branch, the newest merges and pushes there, and the newest tag or GitHub
   release.
2. **What runs now:** the newest Firebase App Distribution and TestFlight builds, and the store release in progress (Google Play Console, App Store Connect). Never upload a build number at or below one already uploaded.
3. **F's deploys:** the Board log (tab `Board log` of the CDA sheet, https://docs.google.com/spreadsheets/d/1rBHMmdFGxjw-hKN5PtktbhCMmO_OL8-rQ5tqHAQ5Pzw): the lines that start with
   `Deploy: Sejutacita/alice` or `Release train: alice` in the last 24 hours. No access to the sheet: ask in your PR,
   and a Claude session can read it for you.

Then:

- The commit you deploy must contain every change already live or queued for that environment: the commit live now,
  the commit of any deploy in flight and the head of the release branch it goes out from.
  `git merge-base --is-ancestor <that sha> <your sha>` must succeed for each; a newer build that lacks one of them
  drops that change. If it fails, merge that sha into yours (not a cherry-pick: a cherry-pick makes a new sha, so the
  check would still fail). A change that came in by cherry-pick or squash already passes when
  `git cherry <your sha> <that sha>` prints no `+` line; a squash of several commits, which `git cherry` cannot match:
  name in the PR each `+` commit and the commit of yours that holds its change. Two exceptions, each said in the PR:
  a planned rollback, and a hotfix. A hotfix contains the commit production runs, not a release in flight (it
  branches from that commit and carries only its fix); on staging it may go over a release line in flight, whose
  owner is told in the hotfix PR and deploys that line again after.
- Never skip or reorder a migration. If a release in flight adds one, yours goes after it, with it applied.
- Never switch an Unleash flag that another release in flight depends on.
- Another deploy of this repo in flight (started, not yet live and checked): start yours after it is live, or agree
  the order in its PR.
- Say in your PR who deploys it, with one line: `Deploys: <name, or F (Claude)> · <staging or production> · <tag or
  sha>`.

F writes one Board log line for each deploy it makes:
`Deploy: Sejutacita/alice <env> <tag> sha <commit link> at <yyyy-mm-dd hh:mm> WIB`.
<!-- deploy-notice:end -->
