---
layout: default
title: OpenGL BMP 텍스처 로딩
---
OpenGL에서 bmp파일을 로딩하여 texture를 생성하는 코드이다. bmp는 24/32비트만을 지원한다. 15,16비트와 256칼라는 지원하지 않는다. glTexImage2D로 생성하기 때문에 다음과 같이 필터가 셋팅되어 있어야 한다.

```c
#define GL_BGRA 0x80E1 // Use this for 32bit bmp
#define GL_BGR_EXT 0x80E0

void LoadBmp()
{
 glBindTexture(GL_TEXTURE_2D, tex);
 FILE *fp = fopen("c:\\cap2.bmp", "rb");
 if (!fp)
  return;
 BITMAPFILEHEADER bfh;
 BITMAPINFOHEADER bih;
 fread(&bfh, 1, sizeof(BITMAPFILEHEADER), fp);
 fread(&bih, 1, sizeof(BITMAPINFOHEADER), fp);
 fseek(fp, bfh.bfOffBits, SEEK_SET);// 중간에 더미값이 있을수도 있으니 정확하게 이렇게해서 데이타 시작 바이트를 찾아가자
 int internalFormat = GL_BGR_EXT;
 if (bih.biBitCount == 32)
 {
  internalFormat = GL_BGRA;
 }
 else if (bih.biBitCount == 24)
 {
  internalFormat = GL_BGR_EXT;
 }
 else
 {
  // 지원안되는 포맷(24,32만 지원)
  fclose(fp);
  glBindTexture(GL_TEXTURE_2D, 0);
  return;
 }
 unsigned char *data2 = new unsigned char[bih.biSizeImage];
 fread(data2, 1, bih.biSizeImage, fp);
 glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, bih.biWidth, bih.biHeight, 0, GL_BGRA, GL_UNSIGNED_BYTE, data2);// GL_BGRA
 fclose(fp);
 delete[] data2;
 glBindTexture(GL_TEXTURE_2D, 0);
}
```
