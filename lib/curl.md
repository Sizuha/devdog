# curl

## Build: Mac OS X, iOS

### 빌드 스크립트

- iOS용 빌드 스크립트
  - https://github.com/miyabichan/cURL-SSL-for-iOS
- Mac OS X와 iOS용 빌드 스크립트
  - https://github.com/brunodecarvalho/curl-ios-build-scripts

### 프레임워크 추가
Xcode에서 다음 프레임워크를 추가해야 한다.

- libz.dylib
- Security.framework

## Easy Interface
libCURL은 아래와 같은 easy라는 이름을 가지는 인터페이스를 제공한다.

1. curl_global_init : curl 라이브러리를 초기화 한다.
1. curl_easy_init : context를 생성한다.
1. curl_easy_setopt : context 설정
1. curl_easy_perform : 요청을 초기화 하고 callback함수를 대기시킨다.
1. curl_easy_cleanup : context를 없앤다.
1. curl_global_cleanup : curl 라이브러리를 없앤다.

## HTTP GET
curl_easy_init 함수를 호출해서 context 객체를 생성한다.
```c
CURL *ctx = curl_easy_init();
```

curl_easy_setopt 를 이용해서 context객체를 설정한다. CURLOPT_URL은 목표 URL이다. 
```c
curl_easy_setopt(ctx, CURLOPT_URL, argv[1]);
```

curl_easy_setopt를 이용하면 이외에도 몇 가지 설정을 더 해줄 수 있다.
```c
curl_easy_setiopt(ctx, CURLOPT_WRITEHEADER, stderr);
curl_easy_setiopt(ctx, CURLOPT_WRITEDATA, stdout);
```

header 정보는 표준에러로, body정보는 표준출력으로 가져오도록 설정을 했다.

이제 curl_easy_perform함수를 이용해서 실제 페이지를 긁어오는 일을 하도록 하자.
```c
const CURLcode rc = curl_easy_perform(ctx);
if (CURLE_OK != rc) {
   std::cerr << "Error from cURL: " << curl_easy_strerror(rc) std::endl;
}
else {
    // 데이터 처리
}
```

이후 데이터는 아래와 같이 curl_easy_getinfo를 통해서 얻어와서 처리하면 된다.
```c
long statLong;
curl_easy_getinfo(ctx, CURLINFO_HTTP_CODE, &statLong);
std::cout << "HTTP response code: " << statLong << std::endl;
```
원하는 값을 가져오기 위해서는 데이터의 타입에 맞는 인자를 써야 한다. 200이나 404와 같은 HTTP 응답코드를 가져오길 원한다면 CURLINFO_HTTP_CODE를 전송받은 문서의 크기를 알아내길 원한다면 CURLINFO_SIZE_DOWNLOAD를 사용하는 식이다. 

모든 작업이 다 끝났다면, curl_easy_cleanup을 호출해서 curl_easy_setopt 객체를 소멸시켜야 한다. 그렇지 않을경우 메모리누수 현상을 겪게 될 것이다.
```c
/*
 sample for O'ReillyNet article on libcURL:
    {TITLE}
    {URL}
    AUTHOR: Ethan McCallum
 
 Scenario: use http/GET to fetch a webpage
 
 이 코드는 Ubuntu 리눅스 Kernel 2.6.15에서 
 libcURL 버젼 7.15.1로 테스트 되었다.
 2006년 8월 3일
 */
 
#include <iostream>
 
extern "C" {
    #include <curl/curl.h>
}
 
// - - - - - - - - - - - - - - - - - - - -
 
enum {
    ERROR_ARGS = 1 ,
    ERROR_CURL_INIT = 2
} ;
 
enum {
    OPTION_FALSE = 0 ,
    OPTION_TRUE = 1
} ;
 
enum {
    FLAG_DEFAULT = 0 
} ;
 
// - - - - - - - - - - - - - - - - - - - -
  
int main( const int argc , const char** argv ) {
    if( argc != 2 ){
        std::cerr << " Usage: ./" << argv[0] << " {url} [debug]" << std::endl ;
        return( ERROR_ARGS ) ;
    }
 
    const char* url = argv[1] ;
 
    // lubcURL 초기화 
    curl_global_init( CURL_GLOBAL_ALL ) ;
 
    // context객체의 생성
    CURL* ctx = curl_easy_init() ;
 
    if( NULL == ctx ){
        std::cerr << "Unable to initialize cURL interface" << std::endl ;
        return( ERROR_CURL_INIT ) ;
    }
 
    // context 객체를 설정한다.    
    // 긁어올 url을 명시하고, url이 URL정보임을 알려준다.
    curl_easy_setopt( ctx , CURLOPT_URL,  url ) ;
 
    // no progress bar:
    curl_easy_setopt( ctx , CURLOPT_NOPROGRESS , OPTION_TRUE ) ;
 
    /*
    By default, headers are stripped from the output.
    They can be:
 
    - passed through a separate FILE* (CURLOPT_WRITEHEADER)
 
    - included in the body's output (CURLOPT_HEADER -> nonzero value)
        (here, the headers will be passed to whatever function
         processes the body, along w/ the body)
 
    - handled with separate callbacks (CURLOPT_HEADERFUNCTION)
        (in this case, set CURLOPT_WRITEHEADER to a
         matching struct for the function)
 
    */
    
    // 헤더는 표준에러로 출력하도록 하다. 
    curl_easy_setopt( ctx , CURLOPT_WRITEHEADER , stderr ) ;
 
 
    // body 데이터는 표준출력 하도록 한다.
    curl_easy_setopt( ctx , CURLOPT_WRITEDATA , stdout ) ;
 
    // context 객체의 설정 종료 
 
 
    // 웹페이지를 긁어온다. 
 
    const CURLcode rc = curl_easy_perform( ctx ) ;
 
    if( CURLE_OK != rc ){
 
        std::cerr << "Error from cURL: " << curl_easy_strerror( rc ) << std::endl ;
 
    } else {
 
        // get some info about the xfer:
        double statDouble ;
        long statLong ;
        char* statString = NULL ;
 
        // HTTP 응답코드를 얻어온다. 
        if( CURLE_OK == curl_easy_getinfo( ctx , CURLINFO_HTTP_CODE , &statLong ) ){
            std::cout << "Response code:  " << statLong << std::endl ;
        }
 
        // Content-Type 를 얻어온다.
        if( CURLE_OK == curl_easy_getinfo( ctx , CURLINFO_CONTENT_TYPE , &statString ) ){
            std::cout << "Content type:   " << statString << std::endl ;
        }
 
        // 다운로드한 문서의 크기를 얻어온다.
        if( CURLE_OK == curl_easy_getinfo( ctx , CURLINFO_SIZE_DOWNLOAD , &statDouble ) ){
            std::cout << "Download size:  " << statDouble << "bytes" << std::endl ;
        }
 
        // 
        if( CURLE_OK == curl_easy_getinfo( ctx , CURLINFO_SPEED_DOWNLOAD , &statDouble ) ){
            std::cout << "Download speed: " << statDouble << "bytes/sec" << std::endl ;
        }
 
    }
 
    // cleanup
    curl_easy_cleanup( ctx ) ;
    curl_global_cleanup() ;
 
    return( 0 ) ;
}
```

## HTTP POST
POST로 보내는 데이터는 key=value형태로 되어 있으며, 각각의 key=value 는 &를 통해서 구분된다. 
```c
const char* postData="param1=value1¶m2=value2&...";
```
POST 데이터 전송을 위해서 CURLOPT_POSTFIELDS옵션을 설정하면 된다.
```c
curl_easy_setopt(ctx, CURLOPT_POSTFIELDS, postData);
```

이제 CURLOPT_HTTPHEADER를 이용해서 사용자정의 HTTP 헤더를 만들도록 한다.
```c
curl_sist * responseHeaders=NULL;
 
responseHeaders = curl_slist_append(
    responseHeaders,
    "Expect: 100-continue"
);

curl_easy_setopt(ctx, CURLOPT_HTTPHEADER, responseHeaders);
```
주의 할것은 libCURL은 hidden 필드나 JavaScript와 같은 클라이언트측의 기술들을 사용하지 못한다는 점이다. 예를들어 폼입력을 하고나서 submit버튼을 클릭하면 폼의 각 필드를 검사하는 등의 자바스크립트등은 처리할 수 없다.

```c
/*
 sample for O'ReillyNet article on libcURL:
    {TITLE}
    {URL}
    AUTHOR: Ethan McCallum
 
 HTTP POST (e.g. form processing or REST web services)
 
 이 코드는 Ubuntu 6.06 Dapper Drake,  
 libcURL
 This code was built/tested under Fedora Core 3,
 libcURL version 7.12.3 환경에서 테스트 되었다.
 */
 
#include <cstdio>
#include <iostream>
#include <string>
#include <sstream>
 
extern "C" {
    #include <curl/curl.h>
}
 
// - - - - - - - - - - - - - - - - - - - -
 
enum {
    ERROR_ARGS = 1 ,
    ERROR_CURL_INIT = 2
} ;
 
enum {
    OPTION_FALSE = 0 ,
    OPTION_TRUE = 1
} ;
 
enum {
    FLAG_DEFAULT = 0 
} ;
 
const char* targetUrl ;
 
// - - - - - - - - - - - - - - - - - - - -
 
int main( int argc , char** argv ) { 
    if( argc != 2 ){
        std::cerr << "test of libcURL: test an HTTP post" << std::endl ;
        std::cerr << "(post data is canned)" << std::endl ;
        std::cerr << " Usage: " << argv[0] << " {post url}" << std::endl ;
        std::exit( ERROR_ARGS ) ;
    }
 
    targetUrl = argv[1] ;
 
    curl_global_init( CURL_GLOBAL_ALL ) ;
 
 
    CURL* ctx = curl_easy_init() ;
 
    if( NULL == ctx ){
        std::cerr << "Unable to initialize cURL interface" << std::endl ;
        return( ERROR_CURL_INIT ) ;
    }
 
    /* BEGIN: configure the handle: */
 
    // Target URL: 
    curl_easy_setopt( ctx , CURLOPT_URL,  targetUrl ) ;
    // no progress bar:
    curl_easy_setopt( ctx , CURLOPT_NOPROGRESS , OPTION_TRUE ) ;
 
    // 응답데이터를 표준출력으로 보낸다. 
    curl_easy_setopt( ctx , CURLOPT_WRITEDATA , stdout ) ;
 
    // 사용자 정의 HTTP 헤더: create a linked list and assign
    curl_slist* responseHeaders = NULL ;
    responseHeaders = curl_slist_append( responseHeaders , "Expect: 100-continue" ) ;
    responseHeaders = curl_slist_append( responseHeaders , "User-Agent: Some Custom App" ) ;
    curl_easy_setopt( ctx , CURLOPT_HTTPHEADER , responseHeaders ) ;
 
    // POST Data 설정 
    // notice the URL-unfriendly characters (such as "%" and "&")
    // URL에서는 '%', '&', ' '와 같은 문자를 URL encoding 시켜줘야 한다.
    // curl_escape 함수를 이용해서 인코딩할 수 있다. 
    const char* postParams[] = {
        "One"      , "this has % and & symbols" ,
        "Dos"      , "value with spaces" ,
        "Trois"    , "plus+signs+will+be+escaped" ,
        "Chetirye" , "fourth param..." ,
        NULL
    } ; 
 
    // buffer for the POST params
    std::ostringstream postBuf ;
 
    const char** postParamsPtr = postParams ;
 
    while( NULL != *postParamsPtr )
    {
        // curl_escape( {string} , 0 ): replace special characters
        // (such as space, "&", "+", "%") with HTML entities.
        // ( 0 => "use strlen to find string length" )
        // remember to call curl_free() on the strings on the way out
        char* key = curl_escape( postParamsPtr[0] , FLAG_DEFAULT ) ;
        char* val = curl_escape( postParamsPtr[1] , FLAG_DEFAULT )  ;
 
        std::cout << "Setting POST param: "" << key << "" => "" << val << """ << std::endl ;
        postBuf << key << "=" << val << "&" ;
 
        postParamsPtr += 2 ;
 
        // the cURL lib allocated the escaped versions of the
        // param strings; we must free them here
        curl_free( key ) ;
        curl_free( val ) ;
 
    }
    postBuf << std::flush ;
 
    // We can't really call "postBuf.str().c_str()" here, because
    // the std::string created in the middle is a temporary.  In turn,
    // the char* buf from its c_str() operation isn't guaranteed to
    // be around after the function call.
    // The solution: explicitly create the string.
 
    // Larger (and/or better) code would use std::string::copy() to create
    // a const char* pointer to pass to cURL, then clean it up later.
    // e.g.:
    //    const char* postData = new char*[ 1 + postBuf.tellg() ] ;
    //     postBuf.str().copy( postData , std::string::npos ) ;
    //  postData[ postBuf.tellg() ] == '' ;
    const std::string postData = postBuf.str() ;
 
    std::cout << "post data: " << postData << std::endl ;
    curl_easy_setopt( ctx , CURLOPT_POSTFIELDS , postData.c_str() ) ;
 
    // do a standard HTTP POST op
    // in theory, this is automatically set for us by setting
    // CURLOPT_POSTFIELDS...
    curl_easy_setopt( ctx , CURLOPT_POST , OPTION_TRUE ) ;
 
    /* END: configure the handle */
    // action!
    std::cout << "- - - BEGIN: response - - -" << std::endl ;
    CURLcode rc = curl_easy_perform( ctx ) ;
    std::cout << "- - - END: response - - -" << std::endl ;
 
    // "curl_easy_strerror()" available in curl v7.12.x and later
    if( CURLE_OK != rc ){
        std::cerr << 't' << "Error from cURL: " << curl_easy_strerror( rc ) << std::endl ;
    }
 
    // cleanup
    curl_slist_free_all( responseHeaders ) ;
    curl_easy_cleanup( ctx ) ;
    curl_global_cleanup() ;
    std::exit( 0 ) ;
 }
```

## Time out

```c
// 타임아웃 설정: 단위는 초(sec)
void setTimeout(CURL* curl, int conn_timeout, int http_timeout)
{
  curl_easy_setopt(curl, CURLOPT_CONNECTTIMEOUT, conn_timeout); // 연결시 타임아웃
  curl_easy_setopt(curl, CURLOPT_TIMEOUT, http_timeout); // 데이터 전송 타임아웃
}
```

milliseconds 단위로 설정하려면 **CURLOPT_TIMEOUT_MS** 옵션으로 설정.
