---
layout: post
title: iOS 게임 렌더링 루프 만들기
---
iOS에서 주기적으로 호출되는 렌더링 루프 함수를 만들어 보자.
```objc
// 선언
@interface RenderView : UIView 
{
    CADisplayLink *displayLink;
}

// 초기화 
displayLink = [CADisplayLink displayLinkWithTarget:self selector:@selector(runLoop)]; 
[displayLink addToRunLoop:[NSRunLoop currentRunLoop] forMode:NSDefaultRunLoopMode];

// 루프함수
-(void)runLoop {
    NSLog(@"runLoop");
    [self setNeedsDisplay];
}
```