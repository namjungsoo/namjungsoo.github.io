---
layout: post
title: iOS CustomView에 메모리 비트맵 그리기
---
```objc
#import <UIKit/UIKit.h>

@interface RenderView : UIView
{
    CGImageRef imageRef;
    char *rawData;
int rb, w, h;
    CGColorSpaceRef colorSpace;
}

@end

#import "RenderView.h"

@implementation RenderView

- (id)initWithCoder:(NSCoder *)aDecoder {
    self = [super initWithCoder:aDecoder];
    NSLog(@"initWithCoder");
    
    colorSpace = CGColorSpaceCreateDeviceRGB();
    
    w = self.bounds.size.width;
    h = self.bounds.size.height;
    rb = 4*w;
    
    NSLog(@"w=%i h=%i", w, h);
    rawData = (char*)malloc(h * rb);
    for(int i=0; i<h; i++) {
        for(int j=0; j<w; j++) {
            rawData[rb*i + 4*j+0] = 255;//A
            rawData[rb*i + 4*j+1] = 0;
            rawData[rb*i + 4*j+2] = 0;
            rawData[rb*i + 4*j+3] = 255;//R
        }
    }
    return self;
}

// Only override drawRect: if you perform custom drawing.
// An empty implementation adversely affects performance during animation.
- (void)drawRect:(CGRect)rect
{
    [super drawRect:rect];
    // Drawing code
    NSLog(@"RenderView.drawRect");
    
    // 빨간 사각형을 그려보자
    CGContextRef context = UIGraphicsGetCurrentContext();
  

    // 픽셀을 입력하자
    CGContextRef ctx = CGBitmapContextCreate(rawData,w,h, 8,
                                             rb,
                                             colorSpace,
                                             kCGImageAlphaPremultipliedLast
                                             );
    imageRef = CGBitmapContextCreateImage(ctx);
    CGContextRelease(ctx);
    
    // 그리자 이제
    CGContextDrawImage(context, CGRectMake(0,0,w,h), imageRef);
}

-(void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    
}
-(void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event {
    
}
-(void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event {
    
}

@end
```