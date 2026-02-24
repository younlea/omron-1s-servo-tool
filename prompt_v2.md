```
Role
당신은 파이썬(Python) 기반의 PC 자동화 제어 및 실시간 통신(EtherCAT, CAN) 시스템을 개발하는 수석 소프트웨어 엔지니어입니다.
Objective
모터 성능 평가를 위한 '모터 다이나모미터(Dynamometer) 통합 제어 및 검증 소프트웨어'를 처음부터 구조적으로 개발하려고 합니다.
UI는 PyQt6를 사용하며, 비교군(부하) 모터는 EtherCAT으로 제어하고, 테스트할 모터(DUT)는 CAN 통신으로 제어하는 것이 핵심입니다.
Hardware Architecture
1. 부하 모터 (Load Motor): OMRON 1S Series (R88D-1SN02H-ECT). pysoem 라이브러리를 통해 PC를 EtherCAT Master로 구성하여 제어합니다. (CiA402 Drive Profile 적용)
2. 테스트 모터 (DUT): CAN 통신 구동 드라이버 사용. python-can 라이브러리를 사용합니다.
3. 기구 구성: 두 모터는 하나의 동력축으로 직결(Coupling)되어 있습니다.
Core Requirements & Software Architecture
1. 통신 및 스레드 분리 (Strict Threading)
• UI 스레드: PyQt6 기반 메인 스레드. 조작 지연이나 프리징이 없어야 합니다.
• Control/Worker 스레드 (Real-time Loop): EtherCAT(PySOEM) 주기적 통신과 CAN(python-can) 통신을 담당하는 별도의 백그라운드 스레드로 구현합니다. 데이터 동기화(Sync) 오차를 최소화하는 구조를 제안해 주세요.
2. 모터 제어 모드 및 PDO 매핑 (OMRON)
• 제어 모드: Cyclic Synchronous Velocity (CSV) 및 Cyclic Synchronous Torque (CST) 모드 지원.
• 주요 PDO Mapping (OMRON):
• Rx: Controlword(0x6040), Modes of Operation(0x6060), Target Torque(0x6071), Target Velocity(0x60FF)
• Tx: Statusword(0x6041), Velocity Actual Value(0x606C), Torque Actual Value(0x6077), Error Code(0x603F)
3. CAN 통신 통합 (DUT) - [중요: 스펙 추후 제공]
• DUT 모터 제어를 위한 CAN 통신 프로토콜(CAN ID, 데이터 프레임 포맷 등) 세부 스펙은 추후에 제가 따로 제공할 예정입니다.
• 따라서 이번 코드 작성 시에는 CanHandler 등의 별도 클래스로 모듈화해 두고, 임의의 목표 속도(RPM) 전송 및 전류/엔코더 값 수신을 시뮬레이션할 수 있는 더미(Dummy) 로직이나 플레이스홀더(Placeholder) 형태로 뼈대만 완벽하게 구성해 주세요.
4. 다이나모 제어 시퀀스 (Dynamo Auto-Sequence)
수동 제어 외에, T-N 커브 측정을 위한 자동화 시퀀스 로직을 구현해야 합니다.
• 동작 원리: DUT는 **속도 제어(Speed Control)**로 구동하고, OMRON 부하 모터는 역방향 **토크 제어(CST Mode)**로 구동하여 부하를 줍니다.
• 시퀀스 스텝:
1. CAN을 통해 DUT에 목표 RPM 지령 인가 및 도달 대기.
2. EtherCAT을 통해 OMRON 모터에 목표 부하(Torque) 인가 (예: 0.1Nm 단위로 스텝 증가).
3. 안정화 대기 (Settling Time): 부하 인가 후 과도응답이 지나가고 시스템이 안정화될 때까지 설정된 시간(예: 1~3초) 대기.
4. 데이터 캡처: 안정화된 시점의 양쪽 데이터를 동기화하여 수집 (DUT: RPM, 소모 전류 / OMRON: Actual Velocity, Actual Torque).
5. 종료 조건(Max Torque 또는 Max Current 도달)까지 2~4 과정 반복 및 종료 시 모터 정지.
5. GUI (PyQt6 + PyQtGraph) 구성
• Control Viewer:
• 통신 연결 설정 (EtherCAT 인터페이스, CAN 버스).
• 수동 조작 패널: 모터 상태 확인, Servo ON/OFF, Fault Reset.
• 자동화 시퀀스 패널: 목표 RPM, 부하 증가 Step(Nm), 안정화 대기 시간(sec) 입력 폼 및 Start Auto Test 버튼.
• Real-time Monitor:
• pyqtgraph를 사용하여 실시간 RPM, Torque, Current 트렌드를 빠르고 부드럽게 렌더링.
• Result Viewer (데이터 로깅):
• 테스트 완료 후 수집된 데이터를 바탕으로 T-N Curve (X축: RPM, Y축: Torque) 플롯 생성.
• 수집된 데이터(시간, DUT 데이터, OMRON 데이터 통합)를 pandas를 활용하여 .csv로 저장(Export)하는 기능.
Requested Output
위 요구사항을 모두 만족하는 완전한 형태의 Python 스켈레톤 코드를 작성해 주세요.
프로젝트 구조를 main.py, ethercat_handler.py, can_handler.py, dynamo_sequence.py, ui_mainwindow.py와 같이 논리적으로 분할하여 전체 코드를 제공해 주고, 각 모듈의 핵심 역할과 추후 CAN 스펙을 채워 넣어야 할 위치를 주석으로 명확히 표시해 주세요.
```
이제 이 프롬프트를 복사해서 입력하시면, 파일별로 깔끔하게 구조화된 전체 소프트웨어 프레임워크 코드를 짜줄 것입니다. 코드가 출력되면, 이후에 CAN 스펙 메뉴얼을 확인하시고 can_handler.py 부분만 채워 넣으시면 됩니다. 진행하시면서 코드 구조나 테스트 환경 세팅에 대해 궁금한 점이 생기면 언제든 질문해 주세요! 어떤 결과물이 나올지 저도 기대되네요.
