```
title: java memory struct + etc
date: 2018-11-18 11:30:30
author: Ho95
githublink: https://github.com/Ho95
tags: [Device_Driver]  
```



이 글은 안드로이드 프로그래밍 과제에 적었던 내용을 가져왔습니다.



###  자바의 메모리 구조

------

 

 자바의 메모리 구조는 플랫폼에 상관없이 WORA(Write Once Run Anywhere)을 

가능하게 해주는JVM(Java Virtual Machine)에 존재하고 있으며, 크게 5가지 영역으로 

나뉘어져 있습니다. 영역은 아래 표와 같습니다.

| Class(Static) | Stack     | Heap        | Native Method    | PC Register      |
| ------------- | --------- | ----------- | ---------------- | ---------------- |
| 전역 변수     | 지역 변수 | 객체 데이터 | 플랫폼 호환 코드 | 다음 명령어 주소 |

 

 1)Class(Static) Area

 클래스 영역은 간단하게 말하면 클래스안의 모든 멤버들이 접근할 수 있는 전역 변수들이 저장됩니다. static으로 선언된 필드나, 클래스안, 메소드 밖에 선언된  필드, 그리고 생성자와 클래스가 가지고 있는 메소드가 저장됩니다. 이 영역에는 JVM 시작시 할당되고, 프로그램 종료 혹은 null 선언까지 남아있습니다. 자바에서 메모리 할당을 풀어주는 GC(Garbage Collection)의 대상입니다.

 

 2)Stack Area

 스택 영역은 간단히 말하면 한 메소드의 내부 필드나 new를 이용해 생성한 객체들이 저장되고 메소드 종료시 삭제됩니다. 내부 필드는 값이 직접 저장되어 있으나, 객체는 heap영역에 실제 객체 정보가 저장 되어진 주소가 저장 되고, 한 메소드당 하나의 스택영역이 할당됩니다. 각 메소드의 구분은 스택 프레임이라는 형태를 사용하여 관리합니다. 스택 프레임의 구성은 메소드 호출시 사용된 매개변수, 복귀 주소, 이전 메소드의 스택 프레임 시작주소(ebp), 현재 스택 프레임에서실행되고 있는 명령어의 위치(esp)로 구성되어있습니다.

 

 3)Heap Area

 Heap영역은 new로 생성된 객체나 배열의 실제 내용들이 저장됩니다. 스택 영역에서 참조를 하고, 스택 영역에서 참조하는 필드나 객체가 없을 때 GC의 대상이 됩니다.

 

 4)Native Method Area

 JAVA가 아닌 플랫폼에 호환되는 언어로 작성된 코드를 위한 스택 영역입니다.

 

 5)PC Register

 JVM이 사용할 명령어의 주소를 저장합니다. CPU의 Program Counter와 같은 역할입니다.



### 익명의 내부 클래스 이용시 상수 선언이 필요한 이유

------

```
ㅡ생략ㅡ
for(i=0; i<numBtnIDs.length; i++){
    numButtons[i] = (Button) findViewById(numBtnIDs[i]);
}

for(i=0; i<numBtnIDs.length; i++){
    final int index;
    index = i;
	
	numButtons[index].setOnClickListener(new View.OnClickListener(){
       @override
       public void onClick(View v){
           if(edit1.isFocused() == true){
               num1 =edit1.getText().toString()+numButtons[index].getText().toString();
               edit1.setText(num1);
           }
           else if(edit2.isFocused() == true){
               num2 =edit2.getText().toString()+numButtons[index].getText().toString();
               edit2.setText(num2);
           }
           else {
               Toast.makeText(getApplicationContext(),"먼저 텍스트 입력", Toast.LENGTH_SHORT).show();
           }
       }
	});
}
ㅡ생략ㅡ
```



위의 코드를 보면 처음부분에 final 선언이 있습니다.

final을 선언하지 않으면 스택 영역에 index 필드가 저장 됩니다. 

이는 필드를 가진 메소드에서만 사용을 할 수 있어 익명 내부 클레스에서는 index값을 알 방법이 없어 

오류가 발생하게 됩니다. 

하지만 상수 선언을 하게되면 클래스 영역에 index값이 저장되어 익명의 내부 클래스에서도 index값을 이용할 수 

있게 되고, 이를 이용하여 원하는 버튼이 할당된 numButton[]에 접근할 수 있게됩니다. 

그래서 사각형 안의 코드가 꼭 필요합니다. 