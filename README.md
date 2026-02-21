# ROS2Study
ROS2 공부 자료

 ROS2 응용편

### ROS 2 기본 환경 구성 및 워크스페이스 생성

- **워크스페이스(workspace)** : ROS 2에서 코드를 연습하고 빌드하는 작업 공간
- 홈 디렉토리에서 `ros2_study` 폴더를 만들고 그 안에 `src` 폴더 생성
- `colcon build` 명령을 워크스페이스 최상위 디렉토리에서 실행하여 빌드 수행
- 빌드 후 워크스페이스 내에 `build`, `install`, `log` 폴더가 생성됨
- **alias (얼라이언스)** 로 `source /opt/ros/humble/setup.bash` 및 워크스페이스 설치 스크립트(`install/local_setup.bash`)를 등록하여 편리하게 환경 설정 가능

---

### 2. ROS 2 패키지 생성 및 기본 노드 실행

- `ros2 pkg create` 명령어로 패키지 생성 가능 (예: `my_first_package`)
- 빌드 타입으로 Python (`ament_python`) 또는 CMake (`ament_cmake`) 선택 가능
- 예시 명령어:
- 패키지 기본 구조 생성 후 `colcon build` 및 환경 설정 수행
- `ros2 run my_first_package my_first_node`로 노드 실행 가능
- `ros2 pkg list` 및 `ros2 pkg list | grep <키워드>`로 패키지 리스트 확인

---

### 3. 노드 코드 작성 및 실행 (Python 기준)

- Python 노드는 `rclpy` 라이브러리 사용
- 기본 노드 구조:
- `setup.py`에 엔트리 포인트(Entry Point)로 노드 스크립트 등록 필수 (리스트에 콤마 주의)

---

### 4. ROS 2 메시지(Message) 정의 및 활용

- 메시지 정의는 `.msg` 파일에 선언하며, ROS 2 토픽에서 사용하는 데이터 타입을 정의
- 메시지 패키지는 `_msgs` 접미사 붙이는 관례 있음 (예: `my_first_package_msgs`)
- 메시지 예시(`cmdn_pose.msg`):
- `CMakeLists.txt` 및 `package.xml`에 메시지 빌드 및 의존성 선언 필요
- 빌드 후 `ros2 interface show <패키지명>/msg/<메시지명>` 명령으로 메시지 내용 확인 가능
- 노드에서 메시지 타입 임포트하여 구독(Subscription) 또는 발행(Publisher) 구현

---

### 5. ROS 2 토픽(Topic) 사용 실습

- **퍼블리셔(Publisher)** : 주기적으로 메시지를 발행
- **서브스크라이버(Subscriber)** : 토픽을 구독하며 콜백 함수 실행
- 예시: `cmdn_pose` 메시지를 사용하는 퍼블리셔와 서브스크라이버 구현
- 타이머(timer)를 사용해 일정 간격으로 메시지 발행
- 발행되는 토픽과 구독 토픽을 `rqt_graph`로 시각화 가능

---

### 6. ROS 2 서비스(Service) 정의 및 사용

- 서비스는 요청(Request)과 응답(Response)으로 구성된 통신 방식
- 서비스 정의는 `.srv` 파일에 리퀘스트와 리스폰스 데이터 타입 선언
- 서비스 예시(`MultiSpawn.srv`):
- 서비스 서버는 `create_service()`로 생성, 콜백 함수에서 요청 처리 후 응답 반환
- 서비스 클라이언트는 `create_client()`로 생성, `call()` 또는 `call_async()` 수행
- `ros2 service list` 명령으로 서비스 리스트 확인 가능
- `ros2 service call` 명령으로 서비스 호출 테스트

---

### 7. ROS 2 액션(Action) 정의 및 사용

- 액션은 목표(goal)을 보내고 피드백(feedback)을 받고 결과(result)를 받는 비동기 통신 방식
- 액션 정의는 `.action` 파일에 Request, Result, Feedback 세 영역으로 구성
- 액션 예시(`DistTurtle.action`):
- 액션 서버는 `ActionServer` 클래스로 생성, 목표 처리 및 피드백 발행 구현
- 액션 클라이언트는 `ActionClient` 클래스로 목표 전송, 결과 및 피드백 수신
- 다중 콜백 처리를 위해 **멀티스레드 익스큐터(Multi-threaded executor)** 사용 권장
- 액션 서버에서 현재 위치와 이전 위치를 비교해 이동 거리 계산(거리 오차 허용)
- 피드백은 목표 달성 전 계속 발행, 목표 도달 시 성공 상태 반환

---

### 8. ROS 2 파라미터(Parameter) 사용

- 파라미터는 노드가 가지는 설정값, 런타임에 동적으로 변경 가능
- 노드 내에서 `declare_parameter()`로 파라미터 선언 및 디폴트 값 지정
- `get_parameter()` 또는 `get_parameter_value()`로 파라미터 값 조회
- 파라미터 변경 감지를 위한 콜백 함수(`add_on_set_parameters_callback`) 등록 가능
- 파라미터 타입별 `ParameterDescriptor`를 사용해 범위 및 설명 설정 가능
- `ros2 param list`, `ros2 param get`, `ros2 param set` 명령어로 파라미터 관리
- `rqt_reconfigure` 플러그인으로 GUI 기반 파라미터 실시간 조절 가능 (다이나믹 리컨피규어)

---

### 9. ROS 2 로그(Logging) 사용

- `get_logger()`를 통해 로거 객체 획득
- 로그 레벨 종류: DEBUG, INFO, WARN, ERROR, FATAL
- 로그 출력 예시:
- 로그는 타임스탬프와 함께 rqt_console 플러그인에서 실시간 확인 가능
- 프린트문 대신 로그를 활용해 디버깅 및 상태 메시지 출력 권장

---

### 10. ROS 2 rosbag (ROS 백) 활용

- rosbag은 ROS 토픽들의 데이터를 파일로 녹화 및 재생하는 시스템
- 실제 로봇 동작 중 발생하는 토픽 데이터들을 저장하여 재실험 및 디버깅에 활용
- 녹화 명령 예:
- 재생 명령 예:
- rqt 플러그인에서 rosbag 재생 중 토픽 및 메시지 모니터링 가능

---

### 11. ROS 2 런치 파일(Launch File) 작성 및 실행

- 여러 노드를 한 번에 실행하고 환경 설정을 한꺼번에 할 수 있는 스크립트
- Python 런치 파일 예시:
- XML 런치 파일 예시:
- 런치 파일에서 파라미터 설정도 가능
- `ros2 launch <패키지명> <런치파일명>` 명령으로 실행

---

### 12. 멀티스레드 익스큐터(Multi-threaded Executor) 사용

- 하나의 노드에서 액션 콜백과 토픽 콜백 등 여러 콜백이 동시에 호출되어야 할 때 필요
- 기본 `SingleThreadedExecutor`는 콜백이 끝날 때까지 다음 콜백이 대기
- 멀티스레드 익스큐터는 병렬 처리 가능
- 복잡한 콜백 처리 시 시스템 응답성 향상

---

### 13. 알고리즘 예시: 원형 배열을 위한 좌표 계산

- 등 간격으로 N개의 거북이를 원형으로 배치하는 좌표 계산법

| 변수명 | 설명 |
| --- | --- |
|  | 반지름 |
|  | 각 간격 = |
|  | 번째 거북이 각도 = |
|  | 번째 거북이 x 좌표 = |
|  | 번째 거북이 y 좌표 = |
- Python 코드 활용 예 (주피터 노트북에서 테스트 가능)

---

### 14. 기본 실습 흐름 및 주의사항

| 단계 | 주요 내용 | 주의 사항 |
| --- | --- | --- |
| 워크스페이스 생성 | 폴더 및 소스코드 구조 만들기 | 빌드는 워크스페이스 최상위에서 수행 |
| 패키지 생성 | `ros2 pkg create` 명령으로 패키지 틀 생성 | 빌드 타입에 맞게 작성 (Python/C++) |
| 노드 작성 | `rclpy` 또는 `rclcpp` 사용 노드 작성 | `setup.py` 또는 `CMakeLists.txt` 엔트리포인트 등록 필수 |
| 메시지/서비스/액션 정의 | `.msg`, `.srv`, `.action` 파일 작성 | `CMakeLists.txt` 및 `package.xml`에 등록 필요 |
| 빌드 및 환경설정 | `colcon build` 후 `source install/local_setup.bash` | 환경설정 누락 시 노드 실행 안 됨 |
| 실행 및 테스트 | `ros2 run`, `ros2 service call`, `ros2 action send_goal` 등 | 실행 전 환경 설정 필수 |
| 로그 및 디버깅 | `get_logger()` 활용, `rqt_console`로 로그 관찰 | 로그 레벨 적절히 사용, `print` 대신 로그 권장 |
| 파라미터 관리 | 선언, 조회, 변경 및 콜백 등록 | 다이나믹 리컨피규어(rqt_reconfigure) 활용 가능 |
| 런치 파일 작성 | Python 또는 XML 형식으로 여러 노드 실행 자동화 | 파라미터 및 네임스페이스 설정 가능 |
| 멀티스레드 익스큐터 | 복잡한 콜백 처리 시 병렬 실행 | 단일 스레드 환경에서 발생하는 콜백 지연 문제 해결 |

---

### 15. ROS 2 명령어 정리

| 명령어 | 설명 |
| --- | --- |
| `ros2 run <패키지> <노드>` | 특정 패키지 내 노드 실행 |
| `ros2 pkg create` | 새 패키지 생성 |
| `colcon build` | 워크스페이스 빌드 |
| `source install/local_setup.bash` | 빌드 환경 설정 |
| `ros2 topic list` | 토픽 목록 조회 |
| `ros2 topic echo <토픽명>` | 토픽 메시지 출력 |
| `ros2 service list` | 서비스 목록 조회 |
| `ros2 service call <서비스명> <메시지>` | 서비스 호출 |
| `ros2 action send_goal <액션명> <메시지>` | 액션 목표 전송 |
| `ros2 param list` | 파라미터 목록 조회 |
| `ros2 param get/set` | 파라미터 조회 및 설정 |
| `ros2 bag record/play` | rosbag 녹화 및 재생 |
| `ros2 launch <패키지> <런치파일>` | 런치 파일 실행 |

---

### 16. 유용한 rqt 플러그인

- **rqt_graph** : 토픽 및 노드 간 통신 시각화
- **rqt_console** : 로그 메시지 실시간 모니터링
- **rqt_reconfigure** : 파라미터 실시간 조정(다이나믹 리컨피규어)
- **rqt_topic** : 토픽 모니터링 및 퍼블리시
- **rqt_service_caller** : 서비스 호출
- **rqt_bag** : rosbag GUI 재생 및 녹화

---

### 용어 정리

| 용어 | 설명 |
| --- | --- |
| ROS 2 (Robot Operating System 2) | 로봇 개발을 위한 오픈소스 미들웨어 프레임워크 |
| 패키지 (Package) | ROS 2에서 노드, 메시지, 서비스 등을 모아둔 단위 |
| 노드 (Node) | ROS 2에서 실행 프로세스, 기능 단위 |
| 토픽 (Topic) | 노드 간 메시지 교환 통신 채널 |
| 메시지 (Message) | 토픽에서 주고받는 데이터 타입 |
| 서비스 (Service) | 요청-응답 방식 통신 프로토콜 |
| 액션 (Action) | 목표 지향적 비동기 통신, 피드백 포함 |
| 파라미터 (Parameter) | 노드 내 설정 가능한 값, 런타임에 변경 가능 |
| rosbag | ROS 토픽을 기록, 재생하는 데이터 저장 시스템 |
| 런치 파일 (Launch File) | 여러 노드를 한번에 실행 및 설정하는 스크립트 |
| 멀티스레드 익스큐터 (Multi-threaded Executor) | 여러 콜백 동시 처리 지원 실행기 |

---

### 핵심 포인트

- ROS 2 개발은 **워크스페이스** 생성부터 시작하여, **패키지 생성**, **노드 작성**, **빌드 및 실행** 순으로 진행
- **메시지, 서비스, 액션 정의**를 통해 필요한 데이터 타입과 통신 방법을 구현
- **파라미터**는 노드 설정값으로, 동적 변경 및 모니터링이 가능
- **로그 기능**은 디버깅과 상태 모니터링에 필수적이며, `rqt_console`로 실시간 확인 가능
- **rosbag**은 실제 로봇 데이터 녹화 및 재생에 매우 유용
- **런치 파일**로 여러 노드 및 파라미터를 한번에 실행 및 관리 가능
- **멀티스레드 익스큐터** 사용으로 복잡한 노드 내 콜백 처리 병렬화 가능
- 모든 명령어와 코드 작성 시 **환경 설정(소스 명령어)** 을 반드시 수행하여야 함

---

### 참고 메타포 및 이야기

- **원형 배열 알고리즘**을 통해 거북이 여러 마리를 등 간격으로 배치하는 실습을 통해 좌표 변환과 삼각함수 활용법 이해
- **멀티스레드 실행**은 현실에서 여러 작업을 동시에 처리하는 것과 같으며, 액션 콜백과 토픽 콜백이 충돌하지 않도록 도움
- *rosbag(로스백)**은 실제 로봇 동작을 녹화해 반복 재생함으로써, 실제 환경 재현 및 문제 분석에 큰 도움을 줌
- **다이나믹 리컨피규어**는 파라미터를 실시간으로 조절하여 로봇 동작 테스트에 유용함
- 로그 레벨별 메시지 출력으로 로봇 상태를 효과적으로 모니터링하며, rqt 플러그인 활용으로 시각화 가능
