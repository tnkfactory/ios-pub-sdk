# Migration Guide (for iOS)

구 SDK를 사용 중이던 매체사에서는 해당 마이그레이션 가이드를 참고하면 쉽게 신규 SDK를 적용할 수 있습니다.

## 목차

1. [필수 사항](#1-필수-사항)
   * [사이트 Migration](#사이트-migration)
   * [프로젝트에서 구 SDK 제거](#프로젝트에서-구-sdk-제거)
   * [Tnk Publisher SDK 가이드](#tnk-publisher-sdk-가이드)
2. [공통 변경 사항](#2-공통-변경-사항)
3. [전면 광고 (Interstitial Ad) 마이그레이션](#3-전면-광고-interstitial-ad-마이그레이션)
   * [구 SDK 사용 방법](#구-sdk-사용-방법)
   * [신규 SDK 사용 방법](#신규-sdk-사용-방법)
   * [차이점](#차이점)
4. [배너 광고 (Banner Ad) 마이그레이션](#4-배너-광고-banner-ad-마이그레이션)

5. [네이티브 광고 (Native Ad) 마이그레이션](#5-네이티브-광고-native-ad-마이그레이션)
   * [구 SDK 사용 방법](#구-sdk-사용-방법-2)
   * [신규 SDK 사용 방법](#신규-sdk-사용-방법-2)
   * [차이점](#차이점-2)

## 1. 필수 사항

### 사이트 Migration

신규 Publisher SDK를 위하여 새로운 Publisher Site가 오픈되었습니다. 그러므로 우선 Publisher Site에 계정 생성 및 인벤토리 생성 작업이 필요합니다.
다만 개발자의 편의를 위하여 저희 Tnk의 영업담당자를 통하여 Migration 을 요청하시면 기존 App 및 로직 ID 설정을 새로운 사이트에 맞추어 이관해드리고 있습니다. 그러므로 우선 Site 이관 요청을 해주시고 아래의 SDK Migration 작업을 수행해주시기 바랍니다.

### 프로젝트에서 구 SDK 제거

신규 Publisher SDK를 사용하기 위해서는 기존에 사용하던 구 SDK의 제거 후 사용이 가능합니다.

**추가되어 있는 구 SDK 및 관련 Framework 를 삭제해주세요.(표 참고)**
| 라이브러리                    | 용도                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| libtnksdk.a                   | 구 TnkAd SDK 라이브러리             |
| libz.tbd           | 압축라이브러리 (구 TnkAd SDK가 서버통신시 사용함)    |
| MediaPlayer.framework | 동영상 광고 표시를 위하여 사용됨              |
| AdSupport.framework | IdfA 값 획득을 위하여 사용됨 (반드시 Optional 모드로 추가해야 한다.) |
| SystemConfiguration.framework | Wifi 연결상태를 확인하기 위하여 사용됨 |
| AppTrackingTransparency.framework | 앱 추적 동의 팝업              |
| StoreKit.framework | AppStore 이동을 위한 기능 제공                 |

#### 신규 Pub SDK 등록

신규 Pub SDK 적용가이드를 참고하시어 신규 SDK 를 프로젝트에 추가해주세요.

참고)
구 SDK 는 Objective-C 로 작성된 Library 형태로 제공되어 SDK 라이브러리 및 필요한 프레임워크를 일일히 추가해야만 했으나
신규 Pub SDK 는 xcframework 로 개발되어 Pub SDK 만 추가하시면 필요한 라이브러리와 프레임워크는 별도로 추가하실 필요가 없습니다.
또한 기존 SDK 는 swift 에서 사용할 경우 Bridge 헤더파일을 생성해야했지만 신규 Pub SDK 는 그러한 번거로움 없이 Objective-C 와 swift 모두 자유롭게 사용하실 수 있습니다.

#### info.plist
기존에 info.plist 파일에 tnk_app_id 가 등록되어 있다면 삭제하여 주시고 신규로 발급 받은 Publisher ID(Inventory ID) 를 info.plist 파일에 tnk_pub_id 값으로 추가하여 주시기 바랍니다.

### Tnk Publisher SDK 가이드

더 상세한 Tnk Publisher SDK 사용방법은 [Tnk Publisher SDK (for iOS)](./iOS_Guide.md)를 참조하시면 됩니다. 

## 2. 공통 변경 사항

1) info.plist 파일의 **tnk_app_id**의 명칭을 **tnk_pub_id**로 변경하였습니다.

2) **광고 로직 ID**의 명칭을 **Placement ID**로 변경하였습니다.

* 광고를 노출하기 위해서는 광고별 Placement ID 생성 및 광고 설정은 필수입니다. (Site 이관을 요청하셨다면 기존에 사용하는 App ID, 광고로직 ID를 그대로 이용하실 수 있습니다.)
* 구 SDK에서 광고 로직 ID 설정없이 TnkSession.CPC를 넣어 사용하던 광고의 경우 Placement ID를 넣는 란에 아래를 참고하시어 해당 명칭을 넣어주시면 됩니다.

  + 전면광고 : DEFAULT_INTERSTITIAL
  + 배너광고 : DEFAULT_BANNER
  + 네이티브광고 : DEFAULT_NATIVE
  + 동영상광고 : DEFAULT_VIDEO

3) 구 SDK에서는 각 광고 타입별 리스너가 구분되어 존재했으나 신규 SDK에서 모든 광고 리스너는 **TnkAdListener** 하나로 통합되어 사용됩니다.

4) AdListener의 onFailure에서 제공되는 **AdError 클래스**의 description()를 사용하여 에러의 원인 파악이 쉬워졌습니다.

## 3. 전면 광고 (Interstitial Ad) 마이그레이션

구 SDK에서 신규 SDK로 전면 광고를 마이그레이션 하기 위해 사용 방법을 비교해보면 아래와 같습니다.

### 구 SDK 사용 방법

```objective-c
#import "tnksdk.h"

@interface ViewController : UIViewController <TnkInterstitialDelegate> {
    // ...
}
@end

@implementation ViewController {
    TnkInterstitialAd *tnkInterstitial;
}

- (void)viewDidLoad {
    [super viewDidLoad];

    // 1) 전면광고 용 객체 생성
    tnkInterstitial = [[TnkInterstitialAd alloc] init];

    // 2) 전면광고 요청 (Delegate 지정하여 호출함)
    [tnkInterstitial prepare:@"cross_first" delegate:self];
}

#pragma mark - TnkInterstitialDelegate

- (void) didInterstitialLoad:(TnkInterstitialAd *)ad {
    // 전면광고를 화면에 띄운다.
    [ad show];
}
```

### 신규 SDK 사용 방법 

```Objective-C
// ViewController.h
#import <UIKit/UIKit.h>
#import <TnkPubSdk/TnkPubSdk.h>

@interface ViewController : UIViewController <TnkAdListener>
@end

// ViewController.m
#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    TnkInterstitialAdItem* adItem = [[TnkInterstitialAdItem alloc]
                                        initWithViewController:self
                                        placementId:@"YOUR-PLACEMENT-ID"];
    [adItem setListener:self];
    [adItem load];
}

- (void)onLoad:(id<TnkAdItem>)adItem {
    [adItem show];
}

@end
```

### 차이점

1) AdListener가 TnkInterstitialDelegate -> TnkAdListener 로 변경되었습니다.
2) Interstitial 광고 객체의 클래스가 기존 TnkInterstitialAd -> TnkInterstitialAdItem 으로 변경되었습니다.
3) 광고 로딩 메소드 명이 prepare -> load 로 변경되었습니다.
4) Placement ID 를 지정하는 시점에 광고 로딩(prepare 호출)시점에서 객체 생성시점으로 변경되었습니다.
5) 전면 광고 클릭 감지의 경우 구 SDK에서는 AdListener의 onClose() 매개변수 type을 통해 감지할 수 있었으나 신규 SDK는 **onClick**을 분리하는 것으로 변경되었습니다.

## 4. 배너 광고 (Banner Ad) 마이그레이션

구 SDK 는 배너광고 기능을 제공하지 않았으나 신규 Pub SDK 에서는 배너 광고 기능을 제공하고 있습니다. 
자세한 내용은 [Tnk Publisher SDK (for iOS)](./iOS_Guide.md)를 참조해주세요.

## 5. 네이티브 광고 (Native Ad) 마이그레이션

### 구 SDK 사용 방법

```objective-c
#import "tnksdk.h"

@interface ViewController : UIViewController <TnkNativeAdDelegate> {
    // ...
}
@end

@implementation ViewController {
    TnkNativeAd *tnkNative;
}

- (void)viewDidLoad {
    [super viewDidLoad];

    // 1) Native Ad 광고용 객체 생성
    tnkNative = [[TnkNativeAd alloc] init];
    tnkNative.adStyle = AD_STYLE_LANDSCAPE;  // 가로 이미지 가져오도록 설정한다.

    // 2) 네이티브 광고 한건 가져온다.
    [tnkNative prepare:@"cross_first" delegate:self];
}

#pragma mark - TnkNativeAdDelegate

- (void) didNativeAdLoad:(TnkNativeAd *)ad {
    // Rendering native Ad

}
```

### 신규 SDK 사용 방법

```Objective-C
// ViewController.h
#import <UIKit/UIKit.h>
#import <TnkPubSdk/TnkPubSdk.h>

@interface ViewController : UIViewController <TnkAdListener>

@end

// ViewController.m
#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    TnkNativeAdItem* nativeItem = [[TnkNativeAdItem alloc] initWithPlacementId:@"event_a_native" adListener:self];
    [nativeItem load];
}

- (void)onLoad:(id<TnkAdItem>)adItem {
    TnkNativeAdItem* nativeItem = (TnkNativeAdItem*)adItem;
    
    // Rendering native Ad
}

@end
```

### 차이점

1) AdListener가 TnkNativeAdDelegate -> TnkAdListener 로 변경되었습니다.
2) Native 광고 객체의 클래스가 기존 TnkNativeAd -> TnkNativeAdItem 으로 변경되었습니다.
3) 광고 로딩 메소드 명이 prepare -> load 로 변경되었습니다.
4) Placement ID 를 지정하는 시점에 광고 로딩(prepare 호출)시점에서 객체 생성시점으로 변경되었습니다.
5) TnkNativeAd 객체 생성 후 adStyle 을 지정하는 로직이 삭제되었습니다. (Native 광고의 Size 는 Publisher Site 에서 지정합니다.)
6) Native Ad 의 rendering 방법은 유사하지만 메소드 이름에서 약간의 변경이 있습니다. 자세한 내용은 [Tnk Publisher SDK (for iOS)](./iOS_Guide.md)를 참조해주세요.


