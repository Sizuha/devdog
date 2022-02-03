# iOS In-App Purchase

* 필요한 프레임워크: **StoreKit.framework**
* 출처: http://blog.naver.com/PostView.nhn?blogId=kimysaptec&logNo=130095894390

## 준비
### 확인사항
* 은행계좌가 사전에 등록 필수 --- 없으면 테스트 확인불가
* 아이폰 설정/store의 계정 로그아웃(반드시) ---- 테스트 계정 사용을 위함
* 상품 판매 지역 언어 일치 필수 --- 개발어플과 애플 iTunes Connect 사이트의 Manage Your Applications에서
* 애플 결제 얼럿창은 지연시간이 발생하여 느리게 표시 될 수 있음. (이유는 네트워크와 애플 서버의 영향인듯 함) 최대 15초까지 지연 발생 가능 확인 됨.
 
### In-app 상품 등록
**iTunes Connect** > **Manage Your Apps**에서 해당 앱을 선택.

_Manage In-App Purchases_ 버튼 클릭.

※ 상품 가격은 한번 정하면 바꾸기 어렵다. 기간 한정으로만 바꿀 수 있는 듯.

#### 새 상품 등록
_Create New_ 버튼 클릭.

판매 상품 유형은 아래 3가지 유형중 하나 선택

- 보충 가능(Consumable): 이 응용 프로그램 내 구입 기능은 매번 구입해야 하고 무료로 다시 다운로드할 수 없는 항목입니다. 
  - 추가 체력
  - 추가 경험치
- 보충 불가능(Non-Consumable): 이 응용 프로그램 내 구입 기능은 한 번만 구입해야 하고, 동일한 iTunes Store 계정으로 인증된 여러 장비에 전송할 수 있는 항목입니다. 
  - 보너스 게임 레벨
  - 도시 안내 지도
- 구독(Subscriptions): 구독 기간이 만료될 경우 다시 구입해야 하는 일회성 서비스입니다. 
  - 한 달 구독
  - 위치 서비스 구독

상품 유형을 선택하면, In-app 상품에 대한 상세 설정 페이지가 나온다.

- Reference Name: iTunes Connect 사이트 내에서 표시되는 이름. 관리자가 알아볼 수 있게 작성.
- Product ID: 상품 ID. 유니크한 이름이 되도록 작성.
- Cleared for Sale: 판매해야될 상품이므로  Yes로 설정해 놓자.
- In-App Purchase Details: ''Add Language'' 버튼을 클릭해서 해당 언어에 맞게 추가.
  - Language: 언어
  - Display Name: 사용자에게 표시되는 상품 이름.
  - Description: 상품 설명. 실제 사용자에게는 보이지 않는 듯.
  - Publication Name: 구독 상품인 경우, 매거진이나 뉴스 매체의 타이틀.
- Review Notes (Optional)
- Screenshot for Review : 검수 과정에서, 검수자가 참고할 수 있는 스크린 샷.{{ note | 참고 | In-app 결재시, 결재를 진행하기 전에 사용자에게 확인을 요구하는 과정을 한번 밟아주는게 좋다. <br />보통 yes/no 팝업을 하나 띄워주면 될 것이다. }}

### In-app 결재 테스트
아이폰에서 결제하는 방식이므로 로그인 이메일 계정으로 임의로 생성 함. 단지, 이미 iTunes 계정으로 등록 된 계정은 생성 하면 안됨. 실제 결제될 수 있음.

#### 테스트 사용자 등록
1. **iTunes Connect** > **Manage Users**
1. _Test User_ 클릭.
1. _Add New user_ 클릭.
1. 항목들을 잘 채우고 _Save_ 클릭.
   1. 이때 등록한 E-mail이 사용자 ID가 된다.

#### 테스트 진행
1. In-App 결재 테스트에 앞서,  App Store를 로그 아웃 상태로 만들어 놓는다.
1. App을 설치한다.
1. App에서 결재를 시도한다. 이때 샌드박스 테스트 아이디로 로그인 한다.

## 구현
* 참고: [In App Purchase Programming Guide](http://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/)에서 [Adding a Store to Your Application](http://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/AddingaStoretoYourApplication/AddingaStoretoYourApplication.html#//apple_ref/doc/uid/TP40008267-CH101-SW1) 내용
 
#### 헤더

```objc
#import <StoreKit/StoreKit.h>
```

### 델리게이브와 트랜젝션 옵져버

```objc
@interface XXXXX :  NSObject <SKPaymentTransactionObserver, KProductsRequestDelegate> {
}
- (void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray *)transactions;
- (void)productsRequest:(SKProductsRequest *)request didReceiveResponse:(SKProductsResponse *)response
```

* 실제 상품결제 진행에 따른 결과 델리게이트 발생 시킴. (프로그램 초기화나 로드시, 또는 결제버튼 클릭시 처리)
```objc
[[SKPaymentQueue defaultQueue] addTransactionObserver:self];
```

### 애플결제 이용가능 상태를 확인

```objc
if ([SKPaymentQueue canMakePayments])
{
   ... // Display a store to the user.
}
else
{
   ... // Warn the user that purchases are disabled.
}
```
 
### 상품정보 유무 확인

```objc
- (void)requestProductData
{
   SKProductsRequest *request= [[SKProductsRequest alloc] initWithProductIdentifiers: [NSSet setWithObject: @"com.testman.inappdemo.001"]];
   request.delegate = self;
   [request start];
}

- (void)productsRequest:(SKProductsRequest *)request didReceiveResponse:(SKProductsResponse *)response
{
    NSArray *myProduct = response.products;
    // populate UI
    [request autorelease];
}
```

### 상품결제 요청

```objc
SKPayment *payment = [SKPayment paymentWithProductIdentifier:@"com.testman.inappdemo.001"];
[[SKPaymentQueue defaultQueue] addPayment:payment];
```

### 결제 결과에 따른 델리게이트

```objc
- (void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray *)transactions
{
    for (SKPaymentTransaction *transaction in transactions)
    {
        switch (transaction.transactionState)
        {
            case SKPaymentTransactionStatePurchased:
                [self completeTransaction:transaction];
                break;
            case SKPaymentTransactionStateFailed:
                [self failedTransaction:transaction];
                break;
            case SKPaymentTransactionStateRestored:
                [self restoreTransaction:transaction];
            default:
                break;
        }
    }
}

- (void) completeTransaction: (SKPaymentTransaction *)transaction
{
// Your application should implement these two methods.
   //생략가능 [self recordTransaction: transaction];
   //생략가능 [self provideContent: transaction.payment.productIdentifier];
// Remove the transaction from the payment queue.
    [[SKPaymentQueue defaultQueue] finishTransaction: transaction];
}

- (void) restoreTransaction: (SKPaymentTransaction *)transaction
{
    //생략 가능[self recordTransaction: transaction];
    //생략가능 [self provideContent: transaction.originalTransaction.payment.productIdentifier];
    [[SKPaymentQueue defaultQueue] finishTransaction: transaction];
}

- (void) failedTransaction: (SKPaymentTransaction *)transaction
{
    if (transaction.error.code != SKErrorPaymentCancelled)
    {
        // Optionally, display an error here.
    }
    [[SKPaymentQueue defaultQueue] finishTransaction: transaction];
}
```

### Sample
다음은 Unity 연동을 위해 만든 구현 샘플 코드이다.
```objc
//
//  iOSPlugins.h
//
#ifndef Unity_iPhone_iOSPlugins_h
#define Unity_iPhone_iOSPlugins_h

#import <StoreKit/StoreKit.h>

@interface InappObserver : NSObject<SKPaymentTransactionObserver, SKPaymentTransactionObserver> {
}
#pragma mark Store Kit
-(void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray *)transactions;
-(void)completeTransaction: (SKPaymentTransaction *)transaction;
-(void)failedTransaction: (SKPaymentTransaction *)transaction;
@end

#endif
```

```objc
//
//  iOSPlugins.m
//
#import <Foundation/Foundation.h>
#include "iOSPlugins.h"


InappObserver* g_inapp;

void initPlugins()
{
    g_inapp = [[InappObserver alloc] init];
    
    if ([SKPaymentQueue canMakePayments]) {
        [[SKPaymentQueue defaultQueue] addTransactionObserver: g_inapp];
    }
}

void purchase_ios(int product_id, const char* market_pid)
{
    NSLog(@"purchase_ios: %d, %s", product_id, market_pid);
    
    NSString* pid = [NSString stringWithUTF8String: market_pid];
    
    //-- 1.상품요청
    NSSet *set = [NSSet setWithObjects:pid, nil];
    SKProductsRequest *productsRequest = [[SKProductsRequest alloc] initWithProductIdentifiers:set];
    productsRequest.delegate = g_inapp;
    [productsRequest start];
}

@implementation InappObserver

#pragma mark Store Kit

//-- 2.상품응답
- (void)productsRequest:(SKProductsRequest *)request didReceiveResponse:(SKProductsResponse *)response {
    
    if ([response.products count] > 0) {
        SKProduct *product = [response.products objectAtIndex:0];
        NSLog(@"Title:%@", product.localizedTitle);
        NSLog(@"Description:%@", product.localizedDescription);
        NSLog(@"Price:%@", product.price);
        
        //-- 3.구매요청
        SKPayment *payment = [SKPayment paymentWithProduct:product];
        [[SKPaymentQueue defaultQueue] addPayment: payment];
    }
}

- (void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray *)transactions
{
    //-- 4.구매응답
    for (SKPaymentTransaction *transaction in transactions) {
        switch (transaction.transactionState) {
            // Call the appropriate custom method for the transaction state.
                
            case SKPaymentTransactionStateFailed:
                NSLog(@"購入失敗: %@, %@", transaction.transactionIdentifier, transaction.error);
                [self failedTransaction:transaction];
                break;
                
            case SKPaymentTransactionStatePurchased:
                NSLog(@"購入成功");
                [self completeTransaction:transaction];
                
                const char* product_id = [[transaction.payment productIdentifier] UTF8String];
                UnitySendMessage("ShopList", "storeBuyCallBack", product_id);
                break;
                
            case SKPaymentTransactionStateRestored:
                // リストア処理
                // NSLog(@"以前に購入した機能を復元");
                //[queue finishTransaction:transaction];
                // TODO: アイテム購入した処理（アップグレード版の機能制限解除処理等）
                break;
                
            default:
                // For debugging
                NSLog(@"Unexpected transaction state %@", @(transaction.transactionState));
                break;                
        }        
    }
}

- (void) completeTransaction: (SKPaymentTransaction *)transaction
{
    NSString *receiptStr = [transaction.transactionReceipt base64Encoding];
    NSLog(@"completeTransaction");
    NSLog(@"Trasaction Identifier : %@", transaction.transactionIdentifier);
    NSLog(@"Trasaction Date : %@", [transaction.transactionDate description]);
    
    [[SKPaymentQueue defaultQueue] finishTransaction: transaction];
    
    //LOG(@"Trasaction transactionReceipt : %@", receiptStr);  
}

- (void) failedTransaction: (SKPaymentTransaction *)transaction
{
    NSLog(@"failedTransaction");
    [[SKPaymentQueue defaultQueue] finishTransaction: transaction];
    
    const char* msg =  [[transaction.error localizedDescription] UTF8String];
    UnitySendMessage("ShopList", "storeFailCallBack", msg);
 }
@end
```
