# TnkFactory Pub SDK (iOS)

Tnk Pub SDK는 Tnk의 광고 네트워크 상에서 광고앱 이나 매체앱을 개발하기 위하여 제공되는 통합된 SDK이며 아래의 기능들을 사용하실 수 있습니다.

* 전면광고(Interstitial Ad)
* 배너광고(Banner Ad)
* 네이티브 광고(Native Ad)
* 비디오 광고(Video Ad)

## iOS

### iOS Guide

[iOS 가이드 문서](./iOS_Guide.md)

### Migration Guide

[Migration 가이드 문서](./Migration_Guide.md)

### Update Notice

* 2023.09.14 v.1.12
  * TnkBannderAdView 내부 버그 수정
* 2023.03.09 v.1.11
  * nativeAdItem 에 onShow() listener 호출로직의 버그 수정
  * Reward Video 광고 기능 추가
* 2023.03.07 v.1.10
  * nativeAdItem 에 onShow() listener 호출되도록 수정
  * bannerAdItem 에 visibility check 로직 추가
* 2023.02.27 v.1.09
  * memory leak 수정
  * dynamic library -> static library (framework 의 Embed 설정을 Do Not Embed 로 설정)
  * nativeAdItem에 CTA, 광고제공자 로고이미지, 광고 정책 page url 가져오는 API 추가
  * nativeAdItem 에 여러개의 clickView 를 지정할 수 있는 기능 추가
  * bannerAdItem 에 onShow() listener 호출되도록 수정
  * video play 종료시점 crash 되는 버그 수정
* 2022.10.28
  * v.1.07 업데이트
    * tnk_pub_id 값을 info.plist 가 아니라 코딩으로 설정할 수 있는 기능 제공 
* 2022.07.18
  * v.1.06 업데이트
    * 노출 처리 관련 내부 버그 수정
* 2022.06.28
  * v.1.05 업데이트
    * 노출 처리 관련 내부 버그 수정
* 2022.05.20 
  * v1.03 업데이트
    *  SDK 내부 Base URL 변경
* 2022.04.21 
  * v1.02 업데이트
    *  impression 이 누락되는 버그 수정
* 2022.02.09 
  * v1.01 업데이트
    *  신규 출시

