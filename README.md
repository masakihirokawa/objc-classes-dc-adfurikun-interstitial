# アドフリくんの動画インタースティシャル広告を表示する「DCAdfurikunInterstitial」クラス

iPhoneアプリに[アドフリくん](https://adfurikun.jp/ "アドフリくん")の動画インタースティシャル広告を表示する「DCAdfurikunInterstitial」クラスです。

[公式マニュアル](https://github.com/glossom-dev/AdfurikunSDK-iOS/wiki "公式マニュアル")の通りの実装になりますが、作業時間短縮のお役に立てば幸いです。ご使用の際は広告枠IDを指定してください。

## 導入準備

### 1. 広告枠IDの保持

```objective-c
NSString *const ADFURIKUN_INTERS_UNIT_ID = @"広告枠ID";
```

### 2. アプリ起動時に初期化

#### AppDelegate.h

```objective-c
#import <ADFMovieReward/ADFmyInterstitial.h>
```

#### AppDelegate.m

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // アドフリくんインタースティシャル広告初期化
    if ([ADFmyInterstitial isSupportedOSVersion] ) {
        [ADFmyInterstitial initializeWithAppID:ADFURIKUN_INTERS_UNIT_ID];
    }
    
    return YES;
}
```

### e.x. Info.plistの編集

[AdMob](https://admob.google.com/intl/ja/home/ "AdMob")を使用する場合は *Info.plist*に *GADApplicationIdentifier*の設定値を追加してください。

## クラスの使用方法

### 動画インタースティシャル広告の読み込み

```objective-c
[[DCAdfurikunInterstitial sharedManager] loadInterstitial:self];
```

## ソースコード

### DCAdfurikunInterstitial.h

```objective-c
#import <Foundation/Foundation.h>
#import <ADFMovieReward/ADFmyInterstitial.h>

@interface DCAdfurikunInterstitial : NSObject <ADFmyMovieRewardDelegate> {
    ADFmyInterstitial *adfurikunInterstitial;
}

#pragma mark - property
@property (nonatomic, strong) UIViewController *rootViewController;

#pragma mark - public method
+ (id)sharedManager;
- (void)loadInterstitial:(UIViewController *)viewController;

@end
```

### DCAdfurikunInterstitial.m

```objective-c
#import "DCAdfurikunInterstitial.h"

@implementation DCAdfurikunInterstitial

#pragma mark - Shared Manager

static id sharedInstance = nil;

+ (id)sharedManager
{
    @synchronized(self) {
        if (!sharedInstance) {
            sharedInstance = [[self alloc] init];
        }
    }
    
    return sharedInstance;
}

#pragma mark - Adfurikun Interstitial Ad

// Adfurikunインタースティシャル広告読み込み
- (void)loadInterstitial:(UIViewController *)viewController
{
    if (![viewController isEqual:self.rootViewController]) {
        self.rootViewController = viewController;
    }
    
    adfurikunInterstitial = [ADFmyInterstitial getInstance:ADFURIKUN_INTERS_UNIT_ID delegate:self];
    [adfurikunInterstitial load];
}

// 広告の読み込みが完了した時に呼ばれる
- (void)AdsFetchCompleted:(NSString *)appID isTestMode:(BOOL)isTestMode_inApp
{
    if (adfurikunInterstitial != nil && [adfurikunInterstitial isPrepared] ) {
        [adfurikunInterstitial playWithPresentingViewController:self.rootViewController];
    }
}

// 広告の読み込みに失敗した時に呼ばれる
- (void)AdsFetchFailed:(NSString *)appID error:(NSError *)error
{
}

// 広告の表示を開始した時に呼ばれる
- (void)AdsDidShow:(NSString *)appID adNetworkKey:(NSString *)adNetworkKey
{
}

// 広告の再生が完了した時に呼ばれる
- (void)AdsDidCompleteShow:(NSString *)appID
{
}

// 広告を閉じた時に呼ばれる
- (void)AdsDidHide:(NSString *)appID
{
}

// 広告の再生エラー時に呼ばれる
- (void)AdsPlayFailed:(NSString *)appID
{
}

@end
```