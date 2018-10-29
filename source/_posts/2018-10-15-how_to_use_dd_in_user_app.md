```
title: How to use Device Driver in user app
date: 2018-10-15 11:35:30
author: Ho95
githublink: https://github.com/Ho95
tags: [Device_Driver]  
```



## 응용 프로그램이 Device Driver를 사용 하는 법

디바이스 드라이버는 core와 wrapper로 나눌수 있습니다.

core는 실제 H/W의 데이타시트를 보고 코드를 작성 해줘야 합니다. 이름 그대로 핵심 부분 이며

장치 제조사들이 제공 한다고 하네요. 이 글에서 적고자 하는 것은 일반 사용자들의 응용 프로그램이 

사용 하는 Wrapper입니다. 응용들은 이 Wrapper를 이용하여 디바이스 드라이버를 사용 합니다.

모든 글에 적어 놓지만 틀린 부분이 있을 수 있습니다.



## Wrapper

정의 : core의 함수들을 kernel이 알 수 있도록 등록 시키고,  응용들이 호출할 함수들과 core의 함수

​	   1:1 사상(맵핑) 시켜 놓은 것.

![userapp1](https://raw.githubusercontent.com/Ho95/algoittm.github.io/source/source/images/2018-10-15-how_to_use_dd_in_user_app/userapp1.JPG)

(출처: [IT EXPERT 리눅스 디바이스 드라이버])

위의 사진에서 file_operations 구조체가 Wrapper 부분입니다.

응용에서의 함수들과 DD 내부의 함수들을 1:1로 연결 시켜 놓은 것입니다.

예로 응용의 open 함수를 호출하면 kernel이 가지고 있는 문자 디바이스 드라이버 배열 구조체인 chrdevs[]의 

주번호 자리로 가서 그 자리에 있는 file_operations구조체를 참조하여 맵핑 되있는 core의 open 함수를 

호출 해주는 개념입니다.



## 코드로 디바이스 드라이버 작동법 보기

```
#include <linux/init.h>
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/fs.h>          
#include <linux/errno.h>       
#include <linux/types.h>       
#include <linux/fcntl.h>       

/*일부 생략*/
/*디바이스 드라이버 소스 코드*/

struct file_operations call_fops ={ //Wrapper 부분
   .open = DD_open,
   .read = DD_read,
   .write = DD_write,
   .lseek = DD_lseek,
   .close = DD_close,
};
int call_init(void){
    int result;
    printk( "디바이스 드라이버 시작 \n" );    
    result = register_chrdev( 주번호, 디바이스파일_이름, &Wrapper 구조체); //장치 등록부분
    if (result < 0) return result;
    return 0;
}
void call_exit(void){
    printk( "디바이스 드라이버 종료 \n" );    
    unregister_chrdev( 주번호, 디바이스파일_이름);
}
module_init(call_init);
module_exit(call_exit);
MODULE_LICENSE("Dual BSD/GPL");
```

```
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/ioctl.h>
#include <fcntl.h>
#include <unistd.h>

/*일부 생략*/
/*사용자 응용 프로그램 소스*/

int main(){
    int DD;   
    DD = open( 디바이스파일_이름, 옵션);

/*일부 생략*/

```

리눅스 환경에서 위와 같은 소스가 있다고 가정합니다.

root로 로그인후 mknod 명령어를 이용하여 장치 파일을 만듭니다.

```
mknod 장치파일경로 |b 주번호 부번호
(ex mknod /dev/testDD c 255 1)
```

mknod로 만들어진  testDD가 장치파일이 됩니다. 

그리고 이 이름은 위의 드라이버 소스에서 디바이스파일_이름에 해당하게 됩니다.

그 다음 디바이스 드라이버 소스 파일을 컴파일하여 .ko 파일을 만들고 insmod 명령어를 사용합니다.

```
insmod 만든파일.ko
```

insmod 를 실행하게 되면 디바이스 드라이버 소스에서 제일 밑에 있는 modules_init()이 실행 되고,

()안에 있는 call_init이 호출 되게 됩니다. 다시 call_init()의 코드를 쭉 보다보면 register_chrdev()함수가 있는데, 

이 부분이 사용자 응용 프로그램들이 디바이스 드라이버를 사용할 수있게 chrdevs[주번호]에 Wrapper를 등록

해주는 부분입니다. 



응용이 open()을 호출하게 되면 1)응용이 mknod로 만들었던 장치 파일에 접근

​						      2)커널에 올라와 있는 ko파일에서 장치파일에 할당 해놓은 주번호를 얻는다.

​						      3)커널의 chrdevs[주번호]로 가서 Wrapper를 참조한다.

​						      4)대응 되는 함수를 호출한다.

이런 식으로 사용자의 응용들이 디바이스 드라이버 파일을 사용하게 됩니다. 



```
rmmod 만든파일. ko
```

를 터미널 창에 입력하게되면 module_exit()가 호출되고 ()안에 있는 unregister_chrdev()가 호출 됩니다.

unregister_chrdev()로 chrdevs[주번호]자리에 등록한 Wrapper를 삭제(?)합니다.



## module_init(), module_exit()

insmod, rmmod 명령어 실행시 수행되는 함수 입니다.

**module_init()**의 경우 모듈 적재시 실행되며 ()안에 함수를 인자로 받아, 그 함수를 호출 해줍니다.

호출된 함수에서는 register_chrdev나 register_blkdev를 이용하여 디바이스 드라이버를 등록해주고

하드웨어를 사용하기 위해 초기화를 해줍니다. 필요하다면 kmalloc()함수를 이용하여 메모리를 할당 할 수있습니다.

**module_exit()**의 경우 적재된 모듈을 제거시 호출됩니다.  unregister_chrdev, unregister_blkdev를 사용합니다.

할당한 메모리가 있으면 해제 해줍니다.



## (un)register_chrdev()함수

register_chrdev()함수와 unregister_chrdev()함수는 <linux/fs.h>에 정의 되어있습니다.

### register_chrdev()

```
int register_chrdev(unsigned int major, const char *name, struct file_operations *fops)
```

인자로 디바이스 드라이버의 주번호, mknod로 만든 장치파일 이름,  Wrapper 구조체의 주소를 받습니다.

주번호를 가지고 chrdevs[] 배열구조체에서 주번호 자리에 해당 디바이스 드라이버의 Wrapper 구조체 주소를

저장합니다. 그리고 주번호 값을 리턴합니다. 최대 256개 실패시 음수 리턴.

자세한 함수 구조는 http://naito.tistory.com/14에 있습니다.



### unregister_chrdev()

```
int unregister_chrdev(unsigned int major, const char *name);
```

인자로 디바이스 드라이버의 주번호와, mknod로 만든 장치파일 이름을 받습니다.

정상적으로 등록시 0을 반환하고, 주번호 값이 255보다 크거나, 제거하고자 하는 주번호 값이 등록된 디바이스

드라이버가 아니면 음수값을 변환.

자세한 함수 구조는 https://elixir.bootlin.com/linux/v4.2/source/fs/char_dev.c#L569 의 309번줄에 있습니다.



### 끝!

