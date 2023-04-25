---
layout: post
title: "SP:중간고사"
categories: [lecture]
---

## 단답형

1. kill과 자주 사용되는 명령어
2. cd로 디렉토리를 이동하려고 할 때 필요한 권한은?
3. 디렉토리 파일에 파일이름 말고 저장되어 있는 정보는?
4. 한 줄로 myln이라는 이름으로 a.c 파일을 컴파일할 수 있는 코드는?
5. 디바이스 정보가 들어있는 경로는?
6. 시스템설정과 관련된 정보가 들어있는 경로는?
7. 리눅스는 파이프를 사용해 명령어를 여러 개 연결할 수 있다. 이때 사용되는 기호는?

## 서술형 코드 보고 결과 예상, 설명하는 문제

1. 
mkdir tmp
cd tmp
cat > a
ABC^D
cp a ~
rm a
cat ../a

2. 
test.c 파일은 다음과 같다.
```cpp
int main() {
    link("data.txt", "output.txt");

    int fd = open("data.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    close(fd);
    return 0;
}
```
다음 실행 결과와 이유를 설명하시오
cat > data.txt
Hello World
^D
ls
data.txt  test.c
gcc test.c
./a.out
cat output.txt

3. 
test.c 파일은 다음고 같다.
```cpp
int main() {
    struct stat *finfo;
    stat("data.txt", finfo);

    ...
}
```
다음 실행 결과와 이유를 설명하시오.

4. 
int main() {
    char msg[] = "This is an error message...";

    write(STDOUT_FILENO, "Hello ", strlen("Hello "));
    write(STDERR_FILENO, msg, strlen(msg));
    write(STDOUT_FILENO, "world!!\n", strlen("world!!\n"));
}

5. 
```cpp
int main() {
    int fd1 = open("data.txt", O_RDONLY);
    int fd2 = open("data.txt", O_RDONLY);

    read(fd1, buf, 1024);
    read(fd2, buf, 1024);
}
```
두 번째 read에서 파일의 처음부터 읽는지, 첫 번째 read 이후의 위치부터 읽는지 구조적으로 설명