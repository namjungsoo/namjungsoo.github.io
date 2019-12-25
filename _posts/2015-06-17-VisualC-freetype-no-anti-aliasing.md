---
layout: post
title: Visual C++ Freetype anti aliasing 적용 안하기
---
```cpp
bool glyphBit(const FT_GlyphSlot &glyph, const int x, const int y)
{
    int pitch = abs(glyph->bitmap.pitch);
    unsigned char *row = &glyph->bitmap.buffer[pitch * y];
    char cValue = row[x >> 3];
    return (cValue & (128 >> (x & 7))) != 0;
}

for (int idx = 0; idx < wcslen(wstr); idx++) {
    //FT_Load_Char(face, wstr[idx], FT_LOAD_RENDER | FT_LOAD_NO_BITMAP);// 이렇게 하면 anti-aliasing 적용됨
    FT_Load_Char(face, wstr[idx], FT_LOAD_RENDER | FT_LOAD_MONOCHROME | FT_LOAD_TARGET_MONO);
    int width = face->glyph->bitmap.width;
    int height = face->glyph->bitmap.rows;

    for (int i = 0; i < height; i++) {//y
        for (int j = 0; j < width; j++) {//x
 
        // 비트별로 8비트 팩되어 있으므로 그것을 풀어냄
        bool value = glyphBit(face->glyph, j, i);
 
        // true일 경우 글자 칠해진 픽셀
        if (value) {
            // 이제 까만 영역이므로 색칠을 하자
            int xx = face->glyph->bitmap_left + j;
            int yy = FREETYPE_SIZE - face->glyph->bitmap_top + i;
            int idx = tgt->wid*yy + xx;
            pimg[idx] = 1;
        }
    }
}
```