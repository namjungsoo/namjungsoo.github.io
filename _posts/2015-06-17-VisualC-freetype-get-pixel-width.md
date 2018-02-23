---
layout: post
title: Visual C++ Freetype 문자열 픽셀 길이 구하기
---
```cpp
int penx = 0;
for (int i = 0; i < wcslen(wstr); i++) {
    if (len > 0 && i >= len)
        break;
    FT_Load_Char(face, wstr[i], FT_LOAD_NO_BITMAP);// 문자열 width 테스트용
    penx += face->glyph->advance.x >> 6;
}
return penx;
```