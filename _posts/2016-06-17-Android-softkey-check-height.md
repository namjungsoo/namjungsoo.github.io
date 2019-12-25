---
layout: post
title: Android 소프트키 사용가능 여부 체크 및 높이 구하기
---
안드로이드 하단바 소프트키 네비게이션바 사용가능 여부 및 높이를 구할때는 다음과 같이 구한다. 단위는 pixel이다. 
```java
private boolean hasSoftMenu() {
    //메뉴버튼 유무
    boolean hasMenuKey = ViewConfiguration.get(getApplicationContext()).hasPermanentMenuKey();

    //뒤로가기 버튼 유무
    boolean hasBackKey = KeyCharacterMap.deviceHasKey(KeyEvent.KEYCODE_BACK);

    if (!hasMenuKey && !hasBackKey) { // lg폰 소프트키일 경우
        return true;
    } else { // 삼성폰 등.. 메뉴 버튼, 뒤로가기 버튼 존재
        return false;
    }
}

private int getSoftMenuHeight() {
    Resources resources = this.getResources();
    int resourceId = resources.getIdentifier("navigation_bar_height", "dimen", "android");
    int deviceHeight = 0;

    if (resourceId > 0) {
        deviceHeight = resources.getDimensionPixelSize(resourceId);
    }
    return deviceHeight;
}
```    