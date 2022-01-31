# bash Shell Script

## .sh 파일 만들기
새 텍스트 파일을 만든고, 첫 라인에 다음과 같이 적어둔다.
```bash
#!/bin/bash
```

실행 속성 부여
```
$ chmod +x ____.sh
```

## Arguments

| 기호 | 설명 |
| --- | --- |
| $# | 인자 갯수 |
| $0 ~ $n |  n번째 인자값 |

## for문
### 初期値・ループ条件・ループ時の処理で指定する

```bash
for ((初期値; ループ条件; ループ時の処理)); do
    # ...
done

#--- example ---
max=10
for ((i=0; i < $max; i++)); do
    echo $i
done
```

## inを利用する

```bash
for 変数 in 値のリスト; do
    # ...
done
```

#### 値を直接指定する

```bash
for i in 0 1 3 5 8 9; do
    echo $i
done
```

#### 値を範囲で指定する

```bash
for i in {0..9}; do
    echo $i
done
```

#### 値を配列変数で指定する

```bash
array=(0 1 2 3 4 5 6 7 8 9)
for i in ${array[@]}; do
    echo $i
done
```

#### 値をファイルで指定する

```bash
for i in $(cat number.txt); do
    echo $i 
done
```

#### 値にコマンドライン引数を利用する

```bash
for i in "$@"; do
    echo $i
done
```

## 참고

- http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html

