[[_TOC_]]

# Overview

1. 본 문서는 Gitlab을 편하게 업그레이드 하기 위해 개발한 IaC코드 입니다.
2. 개인적으로 편하게 쓰려고 제작했기 때문에 전체 모듈구성으로 사용하긴 어렵습니다.
3. 만들다보니 EC2의 Volume Mount 자동화와 Docker 설치 자동화 등 몇개를 추가하게 되었습니다.
4. 언젠가는 코드가 더 추가될 수 있으며 Gitlab Runner에 Volume Mount나 기타 작업을 붙여서 배포해 볼 예정입니다.(언제가 될지는..)
6. Ansible의 설치와 AWX는 다루지 않습니다. (관리포인트가 생깁니다)

# 사용법

Ansible을 사용하며 Mac, Linux 또는 Windows WSL에서 사용 할 수 있습니다. 간단한 `ansible-playbook` 명령어를 통해 배포를 진행합니다.

```bash
# 사용법
ansible-playbook -i hosts --private-key 프라이빗키 -u 접속유저 -t 배포태그 main.yml

# 예시
ansible-playbook -i hosts --private-key ~/.ssh/keypair-mzc-gitlab.pem -u ec2-user -t volume main.yml
```

# 구조

배포태그에 맞춰서 설치가 진행됩니다. Tag를 작성하지 않을 경우 모든 Role 동작합니다.

**`주의`** 

`remove` 롤은 Gitlab 테스트를 위해 테스트 인스턴스에 설치후 삭제하기 위해 만들어 놓은 롤이니 사용에 주의를 요합니다.

```bash
├── README.md
├── hosts
├── main.yml
├── remove.yml
├── roles
│   ├── backup
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── templates
│   ├── docker
│   │   └── tasks
│   │       └── main.yml
│   ├── gitlab-install
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── templates
│   │       └── docker-compose.yml.j2
│   ├── remove
│   │   └── tasks
│   │       └── main.yml
│   ├── upgrade
│   │   └── tasks
│   │       └── main.yml
│   └── volume
│       └── tasks
│           └── main.yml
├── test.yml
└── variables.yml

15 directories, 13 files
```

## hosts

이 파일에는 접속할 인스턴스의 HOSTNAME을 입력합니다. HOSTNAME의 IP정보를 확인할 수 있어야 접속 할 수 있습니다.

```bash
# 사용법
[gitlab]
호스트네임 ansible_python_interpreter=/usr/bin/python3

# 예시
[gitlab]
gitlab.mzcloud.xyz ansible_python_interpreter=/usr/bin/python3
```

## variables.yml

Ansible로 배포시 필요한 모든 동적 변수를 작성해 두었습니다. Docker-compose를 이용하여 Gitlab 설치를 진행하고 설치에 필요한 변수들을 정의합니다. 필요에 따라 내용을 변경하여 작성하고 배포를 진행합니다.

## main.yml

Volume의 Filesystem과 Mount 구성, Docker 설치, Gitlab 설치, Gitlab 백업, Gitlab Upgrade가 작성되어 있습니다. Gitlab Upgrade를 진행하기 전 문제가 될 부분이 있는지 Gitlab에서 제공하는 릴리즈 노트를 확인하고 업그레이드를 진행하도록 합니다.


# 업데이트중

