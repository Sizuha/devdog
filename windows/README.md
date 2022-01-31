# Windows

## Windows 10/11 최적화
코타나 제거

중지시킬 서비스
* Connected User Experiences and Telemetry

## 문제 및 해결
### 보안관련

* 보안경고 이력 지우기: https://www.thewindowsclub.com/clear-windows-defender-protection-history

### Windows 10 계정에 연결된, Microsoft Account 지우기

https://appuals.com/remove-microsoft-account-windows-10/

최후의 수단으로 레지스트리 편집.

```
HKEY_USERS\.DEFAULT\Software\Microsoft\IdentityCRL\StoredIdentities
```
이 아래에 있는 Email 계정 이름으로 되어있는 노드를 삭제.
