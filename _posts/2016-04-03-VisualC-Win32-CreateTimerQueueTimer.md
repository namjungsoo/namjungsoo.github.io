---
layout: default
title: Visual C++ Win32 타이머 CreateTimerQueueTimer 예제
---

# Visual C++ Win32 타이머 CreateTimerQueueTimer 예제
Win32 콘솔 프로젝트에서 사용할수 있는 Timer 예제이다.

```cpp
#include <stdio.h>
#include <windows.h>

VOID CALLBACK TimerCallback(PVOID lpParameter, BOOLEAN TimerOrWaitFired) {
  printf("TimerCallback\n");
}

int main() {
  // 타이머 큐를 만든다.
  HANDLE timerQueue = CreateTimerQueue();
  // 타이머를 만든다.
  HANDLE timer;
  // 처음 시작할때 0.5초 지연, 주기 0.5초마다 호출되게
  CreateTimerQueueTimer(&timer, timerQueue, TimerCallback, NULL, 500, 500, 0);
  while (1) {
    Sleep(100);
  }
  return 0;
}
```