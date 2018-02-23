---
layout: post
title: Visual C++ iconv 사용하여 wchar_t 변환하기
---
Visual C++에서는 wchar_t로 유니코드를 사용한다. 그런데 유닉스나 모바일 호환성을 위해서 Win32 API가 아닌 iconv로 인코딩을 변환하다보면 에러가 발생한다.  
그럴때는 다음과 같이 tocode에 "UTF-8" 대신에 "WCHAR_T"를 입력하면 된다.  

```cpp
char str[] = "변환할문자열";
TCHAR str2[4096];
iconv_convert("WCHAR_T", "EUC-KR", str, (char*)str2, 4096 * 2);

// 현재 사용하고있는 iconv 래퍼 함수이다.
int iconv_convert(const char *tocode, const char *fromcode, const char *instr, char *outbuf, size_t outlen)
{
    iconv_t cd = iconv_open(tocode, fromcode);
    if (cd == (iconv_t)(-1)) {
        //LOG("iconv_open error");
        return -1;
    }

    // 이거 왜이렇게 했지? 내부적으로 버퍼를 사용함
    char inbuf[BUF_SIZE];
    sprintf(inbuf, "%s", instr);

    // 포인터만 기록해 놓음
    const char *in = inbuf;
    size_t inlen = strlen(in);

    // 메모리 초기화
    memset(outbuf, 0, outlen);
    char *out = outbuf;

    if (iconv(cd, &in, &inlen, &out, &outlen) < 0) {
        //LOG("iconv error");
        return -1;
    }

    iconv_close(cd);
    return 0;
}
```