# Swift

iOS 개발환경을 기준으로 설명함.

## Version 確認

```
$ /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift -v
```

## 문법

* [Swift 문법](swift_syntax.md) 참조.
* [Swift 문법 심화](swift_syntax_adv.md) 참조.

## Regular Expression
참고
- https://developer.apple.com/documentation/foundation/nsregularexpression
- https://regex101.com/

NSRegularExpression 클래스를 활용.
```swift
/// 英数字の組み合わせで、大文字は必ず一つ入っていることを確認
/// - Parameter password: パスワード
public func checkValidation(password: String) -> Bool {
    let regx = try? NSRegularExpression(pattern: "^(?=.*[A-Z])(?=.*[0-9])[a-zA-Z0-9]{8,32}$")
    let range = NSRange(location: 0, length: password.count)
    return regx?.numberOfMatches(in: password, options: [], range: range) == 1
}
```

## 날짜/시간
날짜 시간 표현 방식이 기본적으로 시스템의 설정을 따라가기 때문에, 표준적인 방식으로 처리하기 위해서는 TimeZone, Locale 등을 직접 지정해 줄 필요가 있다.
```swift
let utcTimeZone = TimeZone(abbreviation: "UTC")!
let standardLocal = Locale(identifier: "en_US_POSIX")

let fmt = DateFormatter()
fmt.locale = standardLocal
fmt.dateFormat = "yyyy-MM-dd HH:mm:ss"
fmt.timeZone = utcTimeZone
```

## URL

경로만 얻기
```swift
let url = URL(fileURLWithPath: "/foo/bar/file.text")
let dirUrl = url.deletingLastPathComponent()
```
