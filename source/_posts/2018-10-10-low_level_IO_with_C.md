---
title:Low level I/O with C
date: 2018-10-10 22:50:00 
author: Ho95
githublink: https://github.com/Ho95
tags: [C,Device_Driver]  
---

# 저수준의 파일 입출력 with C

리눅스는 모든 것을 File로 관리 하는데, 장치들도 File로 관리합니다.  그리고 리눅스는 대부분 C로 

구성되어 있습니다. 이번학기 Device Driver(이하 DD) 수업을 들으면서 C로 짜여진 소스를 보게 되는데, 

이때 저수준의 파일 입출력 함수가 쓰이고, 주로 Block DD가 아닌 Character DD의 소스를 봅니다.

이 글에는 문자 DD에 쓰이는 C언어의 저수준 파일 입출력 함수들을 정리 해보고자 합니다.





## C언어의 파일 입출력의 종류

C의 파일 입출력은 2가지 종류가 있습니다.

고수준과 저수준 2가지입니다. 차이점은 얼마나 더 사람에 가까운가 입니다.

![FILE_1](https://github.com/Ho95/algoittm.github.io/blob/source/source/images/2018-10-10-low_level_IO_with_C/FILE_1.JPG?raw=true)

​										출처: www.soen.kr

위의 표가 수준 차이에 따른 차이점들입니다. 그리고 중간에 버퍼가 있냐 없냐 입니다.

DD에서는 저수준 입출력만 사용 한다고 하였는데, 그 이유는 중간 버퍼의 버퍼링으로 인해 

지연이 있을 수 있기 때문입니다. 바로 바로 처리가 되야 문제가 안 생기겠죠?..



## 저수준 파일 입출력 함수의 종류

**open("파일명",옵션, 파일권한)**

:열고자 하는 파일과 모드를 지정하고 fd(file descriptor) 값을 얻어옵니다. 성공시 0이아닌 값 return

 옵션은 O_CREAT: 파일이 없으면 생성 / O_WRONLY: 쓰기전용 / O_RDONLY: 읽기 전용 / O_RDWR: RW 전용

 O_APPEND: 파일 끝 부분에 추가 / O_TRUNC: 기존 파일 존재 시, 내용 모두 삭제 입니다.  | 로 여러개 옵션 지정O

파일 권한 같은 경우는 리눅스에서 쓰이는 RWX 권한을 의미합니다.



**close(fd)**

:오픈한 파일을 그만 사용하고 싶을 때 사용합니다. fd반납.



**read(fd,buf,sizeof(buf))**

:fd에 해당 하는 파일로 가서 buf 사이즈 만큼 읽어 buf에 저장합니다. 



**write(fd,buf,sizeof(buf))**

:buf에서 buf 사이즈 만큼 읽어 fd에 해당하는 파일에 저장합니다.



**lseek(fd, 기준 위치에서 변위, 기준위치 설정)**

:fd에 해당 되는 파일 속에서 원하는 위치로 이동하고 싶을때 사용합니다.  (SQL에서의 커서 개념정도..?)

 예를 들어 lseek( fd, 0x378, SEEK_SET ); 이렇게 한 줄의 코드가 있다면,

 fd에 해당하는 파일에서 SEEK_SET인해 일단 파일의 맨 시작 위치(주소)로 갑니다. 거기서 0x378(HEX)만큼

 더 가는 것 입니다. 이제 그 주소에 있는 값에 대하여 read,write등을 할 수 있게 됩니다.

 SEEK_SET: 파일의 처음 주소 위치 SEEK_CUR: 현재 위치 SEEK_END: 파일의 맨 끝 위치.

 변위를 적을때 -를 적으면 기준위치보다 앞으로 +면 뒤로 이동합니다.



**ioctl(fd,command, arg)**

: 기존의 read()/write()로 처리 하지 못하는 경우, ioctl을 fd, command와 arg를 인자로 호출하면

 커널에 적재되 있는 DD모듈속 command에 해당하는 처리루틴을 실행후 값을 arg로 받거나 전달 할수 있습니다.

 DD 포팅시 ioctl부분의 command 처리 부분을 다 구현 해주어야함.



**fsync()**

: Device 내부에 buf가 있을 경우 buf 내용을 강제로 내보냅니다. (잘 안쓰인다고 하네요.)



## 마치며

DD에서 쓰이는 C언어의 저수준 입출력을 짧게나마 찾아서 정리 해보았습니다.

DD를 구현(포팅) 할려면 lseek()를 정말 능숙하게 다뤄야 할 것 같습니다.

비트 단위로 연산되기 때문에 주소 지정이 정말 중요합니다.

학교 강의에서도 아직 초반이기 때문에 많이는 모르지만 배울수록 매력을 느끼는 분야인 것 같습니다.

끝!
