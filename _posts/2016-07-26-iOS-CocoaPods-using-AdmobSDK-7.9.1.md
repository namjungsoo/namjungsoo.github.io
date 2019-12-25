---
layout: post
title: iOS CocoaPods에서 Admob SDK 7.9.1 버전 사용하기
---
Admob을 Google SDK페이지에서 시킨대로 사용하다 보면 최신버전은 7.9.1인데 7.8.1이 연동되어 다음과 같은 에러메세지가 나는 경우가 있다.

```
You are currently using version 7.8.1 of the SDK. Please consider updating your SDK to the most recent SDK version to get the latest features and bug fixes. The latest SDK can be downloaded from http://goo.gl/iGzfsP. A full list of release notes is available at https://developers.google.com/admob/ios/rel-notes.
```

이렇게 되는 원인은 다음과 같다. Podfile에서 다음과 같이 되어 있는 경우이다.

```ruby
source 'https://github.com/CocoaPods/Specs.git'

platform :ios, '7.0'

target 'project' do
  use_frameworks!
  pod 'Firebase'
  pod 'Firebase/Core'
  pod 'Firebase/AdMob'
  pod 'Fabric'
  pod 'Crashlytics'
  pod 'Google/Analytics' 
end
```

이것을 다음과 같이 바꾸자. Firebase/AdMob을 제거하고 수동으로 Google-Mobile-Ads-SDK를 집어 넣자.

```ruby
source 'https://github.com/CocoaPods/Specs.git'

platform :ios, '7.0'

target 'nextdoor' do
  use_frameworks!
  pod 'Firebase'
  pod 'Firebase/Core'
#  pod 'Firebase/AdMob'
  pod 'Fabric'
  pod 'Crashlytics'
  pod 'Google/Analytics' 
  pod 'Google-Mobile-Ads-SDK'
end
```

그러면 최신버전 7.9.1의 Admob SDK가 연동된다.  
Firebase/AdMob이 구형 7.8.1을 참조해서 문제가 생긴 것이다.
