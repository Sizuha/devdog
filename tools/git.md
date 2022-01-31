# Git

## 간단 사용법
### 서버에 원격 저장소(bare repository) 생성
원격 저장소 home 디렉토리에서,
```
$ git init --bare <project name>.git
```

### 저장소로부터 가져오기

```
git clone {git-repo-address}
```

### 저장소에 올리기

```
git add . 혹은 git add --all
git commit
git push
```

#### 원격 저장소에 올릴때

```
git push origin branch_name
```

### Branch (가지)

가지 확인
```
git branch
```

가지 이동
```
git checkout {branch_name}
```

가지 만들기
```
git checkout -b {branch_name}
```

가지 지우기
```
git branch -d {branch_name}
```

origin에 있는 가지 제거
```
git push origin :branch_name
```

다른 가지에 있는 소스를 가져와서 머지
```
git merge {branch_name}
```
머지한 다음에 push까지 해야 저장소에 반영됨.

### Tag
#### 붙이기

```
$ git tag TAG_NAME 
$ git push origin TAG_NAME
``` 

로컬에 있는 태그를 전부 Push
```
$ git push origin --tags
```

#### 삭제

```
$ git tag -d TAG_NAME 
$ git push origin :TAG_NAME
```

## Tips
### Git 콘솔에서 컬러 표시

```
git config color.ui true
```

### .gitignore 바로 적용하기

```
$ git rm -r --cached .
$ git add .
$ git commit -m"fixed untracked files”
```

### Branch 운영

* master / main: 최근에 릴리즈된 상태 (안정버전)
* dev:  현재 개발 진행중인 상태
* pre: 릴리즈 직전 상태

### 프롬프트에 브런치 표시

#### zsh

\~/.zshrc 파일에 추가

```zsh
# VCSの情報を取得するzsh関数
autoload -Uz vcs_info
autoload -Uz colors # black red green yellow blue magenta cyan white
colors

# PROMPT変数内で変数参照
setopt prompt_subst

zstyle ':vcs_info:git:*' check-for-changes true #formats 設定項目で %c,%u が使用可
zstyle ':vcs_info:git:*' stagedstr "%F{green}!" #commit されていないファイルがある
zstyle ':vcs_info:git:*' unstagedstr "%F{magenta}+" #add されていないファイルがある
zstyle ':vcs_info:*' formats "%F{cyan}%c%u(%b)%f" #通常
zstyle ':vcs_info:*' actionformats '[%b|%a]' #rebase 途中,merge コンフリクト等 formats 外の表示

# %b ブランチ情報
# %a アクション名(mergeなど)
# %c changes
# %u uncommit

# プロンプト表示直前に vcs_info 呼び出し
precmd () { vcs_info }

# プロンプト（左）
PROMPT='%{$fg[red]%}[%n]%{$reset_color%}'
PROMPT=$PROMPT'${vcs_info_msg_0_} %{${fg[red]}%}%}%#%{${reset_color}%} '

# プロンプト（右）
RPROMPT='%{${fg[red]}%}[%~]%{${reset_color}%}'
```

표시예:
```
[user]+(master) %                                                                                                 [~/Documents/workspace/android/memo]
```
