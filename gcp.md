# Google Cloud Platform

## SFTP 설정
### 준비

1. GCP VM인스턴스에서, 파일 업로드용 계정을 작성

### 인증키 만들기(로컬)

```
ssh-keygen -t rsa -f ~/.ssh/[KEY_FILENAME] -C [USERNAME]
```
여기서 USERNAME은 VM인스턴스의 계정이름과 일치시킨다.

~/.ssh/ 경로에 ''KEY_FILENAME''(비밀키), ''KEY_FILENAME.pub''(공개키)가 생성된다.
```
cat ~/.ssh/KEY_FILENAME.pub
```

이때 나오는 인증키 문자열을 복사해 둔다.

### 메타데이터 등록(GCP)
GCP 관리사이트에 접속해서, 내비게이션 메뉴 > Compute Engine > メタデータ 선택.

**SSH認証鍵** 선택후, 편집 버튼을 클릭.
 추가 버튼을 클릭해서, 앞서 복사해 두었던 인증키 문자열을 붙여넣고 저장.

### FTP 클라이언트 설정

* 전송방식: SFTP
* 인증방법: 鍵ファイル
* 鍵ファイル: /Users/xxxx/.ssh/KEY_FILENAME

FileZila의 경우는 鍵ファイル을 \*.ppk 형식으로 다시 저장하도록 안내해 준다. 안내에 따르면 됨.
