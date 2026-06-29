# GPU Manager
- mdev orphan(ghost) 탐지
- ghost mdev cleanup 명령 생성 (mdevctl stop)
- OpenStack VM ↔ libvirt VM 매핑
- VM별 GPU 사용 여부 확인
- nvidia-smi 기본/상세 조회
- MIG 프로파일 목록 조회 → 선택 → GI/CI 생성/삭제 인터랙티브 UI
- mdev 자원 해제


추가하면 좋을 것
GPU 온도 / 메모리 사용률 조회 명령어 




==============================
# gpu-manager

OpenStack GPU 호스트 운영을 위한 TUI 관리 도구입니다.

## 배경

OpenStack 환경에서 GPU를 VM에 할당하는 방식은 Passthrough와 MIG(mdev) 두 가지입니다.
MIG 방식에서는 VM이 삭제된 후에도 mdev 장치가 sysfs에 남는 orphan(ghost) 문제가 발생하며,
이를 수동으로 찾고 정리하는 과정이 번거로워 직접 툴을 만들었습니다.

OpenStack 기반 Private Cloud에서 mdev 방식 MIG 인스턴스 운영 중,
orphan mdev가 수동으로 해제되지 않아 GPU 자원이 낭비되는 문제 발생.
현황 파악과 해제 명령어 생성을 자동화하는 TUI 도구를 개발.


## 운영 플로우
MIG 프로파일 조회
→ GI/CI 생성
→ mdev로 VM에 할당
→ VM 삭제 후 orphan 발생
→ orphan 탐지
→ mdevctl stop으로 정리

## 주요 기능

- **GPU 할당 방식별 설정 가이드** — Passthrough / MIG (mdev / DeviceSpec) Control 노드, Compute 노드, Flavor metadata 설정
- **mdev orphan 탐지** — sysfs에서 ghost mdev UUID 스캔
- **ghost mdev cleanup** — `mdevctl stop` 명령 생성 및 실행
- **OpenStack VM ↔ libvirt VM 매핑** — mdev와 OpenStack 인스턴스 연결
- **VM별 GPU 사용 여부 확인** — 어떤 VM이 어떤 GPU를 쓰는지 한눈에
- **MIG 프로파일 목록 조회 → GI/CI 생성/삭제** — 인터랙티브 UI
- **nvidia-smi 기본/상세 조회**
- **GPU 온도 / 메모리 사용률 조회**

## 환경

- OS: Ubuntu 22.04
- GPU: NVIDIA (MIG / Passthrough)
- 인프라: OpenStack (Nova, libvirt)
- 언어: Go

## 테스트 완료 환경

- NVIDIA A100
- NVIDIA H100
- NVIDIA B300

## 사용법

```bash
./gpu-manager
```

OpenStack 연동 기능은 `~/contrabass-openrc` 또는
`GPU_MANAGER_OPENSTACK_OPENRC` 환경변수로 openrc 경로를 지정하세요.
