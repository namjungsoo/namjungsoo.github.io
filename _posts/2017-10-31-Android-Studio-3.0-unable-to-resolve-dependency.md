---
layout: post
title: Android Studio 3.0 새 프로젝트 빌드 에러 unable to resolve depencency 
---
안드로이드 스튜디오 3.0이 출시되었다.
기존의 프로젝트들은 잘 동작하는데 새로운 프로젝트를 생성하였다면 새로운 프로젝트는 컴파일이 안되는 현상이 발생한다.

```
Unable to resolve dependency for ':app@debug/compileClasspath': Could not resolve com.android.support:appcompat-v7:26.0.0.
Unable to resolve dependency for ':app@debugAndroidTest/compileClasspath': Could not resolve com.android.support.test:runner:1.0.1.
...
```

이는 gradle plugin 버전이 3.0으로 높아졌고, gradle wrapper 버전이 4.1로 높아진 결과이다. 
이 버전업으로 인한 변경점은 다음에 다루겠다.

이 문제를 수정하려면 일단 모듈 app에 대한 build.gradle을 열고 다음과 같이 되어 있는 항목을
```groovy
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:26.0.0'
    implementation 'com.android.support.constraint:constraint-layout:1.0.2'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.1'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.1'
}
```

다음과 같이 변경하면 된다. 변경한 부분은 최신버전이 아닌 부분을 자동으로 최신버전으로 적용하라고 정확한 버전 대신에 '+'를 붙여준 것이다.
```groovy
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:26.+'
    implementation 'com.android.support.constraint:constraint-layout:1.0.2'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:+'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:+'
}
```
