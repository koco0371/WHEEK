# WHEEK

## Handmade

> - [신재협](https://github.com/shin0343)
> - [박성우](https://github.com/koco0371)
> - [손영현](https://github.com/sonyyhh)

***
### Proejct Summary

> 홍익대학교 졸업 프로젝트로 진행한 프로젝트입니다. YOLO darknet을 손바닥과 주먹 데이터로 트레이닝시켜 활용했습니다. YOLO를 통해 얻은 bounding box를 마우스 포인터에 매핑하여 위치를 변경했습니다. X window 시스템의 X11 라이브러리 함수를 활용해서 마우스 포인터의 위치를 변경하고, 마우스 왼쪽 클릭을 주먹을 인식했을 때 동작하도록 만들었습니다.
***
### Environment

<img src="https://upload.wikimedia.org/wikipedia/commons/1/18/ISO_C%2B%2B_Logo.svg" width=300 height=150>
<img src="https://miro.medium.com/max/3200/1*S8Il5ethl3YFh0M9XKVz-A.png" width=300 height=150>
<img src="https://t1.daumcdn.net/cfile/tistory/9923B0495D66434618" width=300>
<img src="https://i2.wp.com/wp.laravel-news.com/wp-content/uploads/2016/12/laravel-valet-ubuntu.png?resize=2200%2C1125" width=300>
<img src="https://media.vlpt.us/images/swhybein/post/2e77281f-8562-4990-abba-e942bc416308/python.png" width=300>
<img src="https://pjreddie.com/media/image/yologo_2.png" width=300>


### Installation

> darknet-master 내 README.md 참조
***
### Directory

```
.
└── darknet-master
    └── src
        └── demo.c
    
```
***
### Source description

### demo.c

> detect 결과를 받아 마우스 포인터에 매핑

**1. demo.c**

```
- control_display
 sorted_dets를 통해 들어온 bounding box를 체크합니다.
 들어온 bounding box에 정확도가 기준 이상이고, 물체가 존재하는 bounding box의 위치를 
 저장합니다. 해당 물체의 class도 저장합니다.
 그리고 현재 상태를 관측된 물체의 클래스로 지정합니다. (손, 주먹, nothing)
 만약 현재 상태가 nothing인 경우, 중간에 detect 실패가 있을 수 있으므로 도입된
 변수인 frame_count의 값이 0인지 여부를 확인합니다. 
 (연속적으로 frame이 10개가 nothing일 경우 frame_count가 0이 됩니다.)
 frame_count가 0일 경우, 현재 클릭 상태를 해제하는 click_release 함수를 실행하고
 frame_count 값을 초기화한 후 이전 상태를 나타내는 prev_state를 nothing으로 설정합니다.
 cur_state와 prev_state가 다를 경우,
 prev_state가 nothing이고 cur_state가 palm이면 손의 위치를 다시 매핑하는 함수인
 detect_hand 함수를 실행합니다. 그리고 frame_count를 다시 초기화합니다.
 이외의 경우에는 frame_count를 초기화하고, 왼쪽 마우스 버튼을 클릭하는 함수인 
 left_click을 실행합니다.
 그리고 cur_state와 prev_state가 같을 경우에는 frame_count를 초기화하고
 마우스 이동 함수인 drag를 실행합니다.

- left_click
 prev_state가 palm이고 cur_state가 fist이면 손바닥의 크기와 주먹의 크기가 다르기
 때문에 마우스의 위치가 바뀌는 문제를 해결하기 위해 get_y_distance 함수를 실행합니다. 그리고 그 차이를 기억하고 현재 좌표를 해당 차이를 더한 만큼으로 저장합니다.
 그리고 왼쪽 마우스 키를 클릭하는 이벤트인 left_down 함수를 실행합니다.
 prev_state가 FIST이고 cur_state가 PALM일 경우에는
 drag 민감도를 줄이기 위한 변수인 drag_count를 초기화하고,
 왼쪽 마우스 키 클릭을 해제하는 함수인 left_up을 실행합니다.
 해당 작업을 종료하면 prev_state의 값을 변경하고, 이전의 x와 y 좌표인 prev_x, prev_y도
 변경합니다.

- get_y_distance
 realy 변수에 현재의 y 좌표와 이전의 y 좌표 차이를 저장합니다.
 cur_y와 prev_y의 차이가 너무 작을 경우, 의도치 않은 손의 흔들림이나 bounding box의
 크기 변화일 수 있으므로, realy 값을 0으로 변경합니다. (해당 사항을 반영하지 않음)
 차이 값이 의미 있을 경우, realy에 palm_fist_dif를 더하여 현재위치를 저장합니다.

- left_down
 XQueryPointer함수를 활용해, 현재 마우스 포인터를 매핑하고,
 XTestFakeButtonEvent함수를 활용해 마우스 왼쪽 버튼 클릭 이벤트를 발생시킵니다.

- left_up
 XQueryPointer 함수를 사용해 마우스 포인터를 매핑하고,
 XTestFakeButtonEvent 함수를 통해 마우스 왼쪽 버튼 해제 이벤트를 발생시킵니다.

- drag
 prev_state가 FIST고 cur_state가 FIST일 경우,
 drag 민감도를 담당하는 drag_count 값이 0일 경우 move_pointer 함수를 실행시킵니다.
 prev_state가 PALM이고, cur_state가 PALM일 경우,
 x의 이동 좌표, y의 이동 좌표 정도가 0.1보다 작을 경우 move_pointer 함수를
 실행합니다.
 그리고 prev_state를 cur_state로 변경하고, prev_x와 prev_y에 현재의 x,y 좌표를
 저장합니다.

- move_pointer
 XQueryPointer를 활용해 마우스 포인터를 매핑하고,
 XTestFakeMotionEvent를 통해 마우스 포인터를 이동합니다.    

- click_release
 prev_state가 FIST면 left_up 함수를 실행합니다.
```

***
### License

> GPL
