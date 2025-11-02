# ansible-role-kubya

Kubernetes 및 DevOps 도구를 활용한 개발 환경을 자동으로 구성하는 Ansible Role입니다.

## 📋 목차

- [개요](#-개요)
- [주요 기능](#-주요-기능)
- [지원 플랫폼](#-지원-플랫폼)
- [필수 요구사항](#-필수-요구사항)
- [설치](#-설치)
- [사용 방법](#-사용-방법)
- [변수 설정](#-변수-설정)
- [태그](#-태그)
- [예제](#-예제)
- [보안](#-보안)
- [라이선스](#-라이선스)

## 🎯 개요

**ansible-role-kubya**는 개발자 워크스테이션을 자동으로 설정하는 Ansible Role로, 다음과 같은 작업을 자동화합니다:

- 최신 CLI 도구 설치 및 구성
- SSH 키 및 Kubernetes 설정 배포
- Git 저장소 자동 복제 및 관리
- 셸 환경 커스터마이징 (zsh/bash)
- Dotfiles 관리 (Chezmoi 사용)
- 개발 도구 통합 설정

이 Role은 특히 Kubernetes 환경에서 작업하는 DevOps 엔지니어와 개발자를 위해 설계되었습니다.

## ✨ 주요 기능

### 1. 현대적인 CLI 도구 설치

다음과 같은 강력한 CLI 도구들을 자동으로 설치합니다:

- **bat**: 구문 강조 기능이 있는 cat 대체 도구
- **delta**: 차이점을 시각적으로 표시하는 Git diff 도구
- **eza**: 현대적인 ls 대체 도구 (색상 및 아이콘 지원)
- **fd-find**: find 명령어의 빠른 대체 도구
- **fzf**: 대화형 퍼지 파인더
- **ripgrep**: 매우 빠른 정규식 검색 도구
- **zoxide**: 스마트한 cd 명령어 (자주 사용하는 디렉토리 기억)

### 2. SSH 구성 관리

- SSH 디렉토리 생성 및 권한 설정
- SSH 키 자동 배포 (암호화된 변수 사용)
- SSH config 파일 생성

### 3. Kubernetes 환경 설정

- `~/.kube` 디렉토리 생성
- kubeconfig 파일 초기화
- K9s 설정 커스터마이징 (로그 tail 길이 등)

### 4. Git 저장소 관리

자동으로 다음 저장소들을 복제합니다:

- `kubya`: 메인 애플리케이션 저장소
- `kubya-playbooks`: Ansible playbook 저장소
- `jenkins-docker`: Jenkins Docker 설정 저장소

모든 저장소는 `dev` 브랜치로 체크아웃되며 GitHub 토큰 인증을 사용합니다.

### 5. Dotfiles 관리

Chezmoi를 사용하여 dotfiles를 관리하고 동기화합니다:

- ArchLinux: `chezmoi init --apply` 실행
- 기타 Linux: chezmoi 자동 설치 및 설정

### 6. 개발 워크스페이스 구성

- `~/.local/bin`: 사용자 로컬 실행 파일
- `~/.local/workspace`: 작업 디렉토리
- 셸별 설정 디렉토리 (zsh/bash)

## 🖥️ 지원 플랫폼

다음 운영체제를 지원합니다:

- RedHat Enterprise Linux (모든 버전)
- Debian (모든 버전)
- Ubuntu (모든 버전)
- Alpine Linux (모든 버전)
- ArchLinux (모든 버전)

## 📦 필수 요구사항

- Ansible 2.1 이상
- sudo 권한
- 인터넷 연결 (패키지 설치 및 Git 복제용)
- curl (ArchLinux 외 시스템에서 chezmoi 설치용)

## 🚀 설치

### Ansible Galaxy를 통한 설치 (권장)

```bash
ansible-galaxy install codesuiteapp.ansible-role-kubya
```

### Git을 통한 직접 설치

```bash
cd ~/.ansible/roles
git clone https://github.com/codesuiteapp/ansible-role-kubya.git
```

## 💻 사용 방법

### 기본 Playbook

```yaml
---
- hosts: localhost
  become: true
  roles:
    - role: ansible-role-kubya
```

### 변수와 함께 사용

```yaml
---
- hosts: localhost
  become: true
  vars:
    use_kube: true
    git_clone: true
    k9s_tail: 5000
    github_username: "your-github-username"
  roles:
    - role: ansible-role-kubya
```

### 특정 태그만 실행

```bash
# SSH 설정만 실행
ansible-playbook playbook.yml --tags ssh

# Kubernetes 설정만 실행
ansible-playbook playbook.yml --tags kube

# Git 복제만 실행
ansible-playbook playbook.yml --tags git_clone
```

## 🔧 변수 설정

### 경로 변수

| 변수명 | 기본값 | 설명 |
|--------|--------|------|
| `user_home` | `~` | 사용자 홈 디렉토리 |
| `kubeconfig` | `~/.kube/config` | Kubernetes 설정 파일 |
| `ssh_key` | `~/.ssh/id_rsa` | SSH 개인 키 경로 |
| `ssh_pub` | `~/.ssh/id_rsa.pub` | SSH 공개 키 경로 |
| `local_ws` | `~/.local/workspace` | 작업 공간 디렉토리 |

### 기능 토글

| 변수명 | 기본값 | 설명 |
|--------|--------|------|
| `use_kube` | `true` | Kubernetes 설정 활성화 |
| `git_clone` | `true` | Git 저장소 복제 활성화 |
| `k9s_tail` | `10000` | K9s 로그 tail 라인 수 |

### 설치 패키지

`kubya_packages` 변수로 설치할 패키지를 커스터마이징할 수 있습니다:

```yaml
kubya_packages:
  - bat
  - delta
  - eza
  - fd-find
  - fzf
  - ripgrep
  - zoxide
```

### 암호화된 변수 (필수)

다음 변수들은 Ansible Vault로 암호화하여 제공해야 합니다:

```yaml
enc_id_rsa_key: "{{ vault_enc_id_rsa_key }}"    # SSH 개인 키 내용
enc_id_rsa_pub: "{{ vault_enc_id_rsa_pub }}"    # SSH 공개 키 내용
enc_gh_user: "{{ vault_enc_gh_user }}"          # GitHub 사용자명
enc_gh_token: "{{ vault_enc_gh_token }}"        # GitHub 토큰
enc_aval_pw: "{{ vault_enc_aval_pw }}"          # Ansible Vault 비밀번호
```

## 🏷️ 태그

Role은 다음 태그를 지원합니다:

| 태그 | 설명 |
|------|------|
| `pkg` | 패키지 업데이트 및 설치 |
| `ssh` | SSH 구성 작업 |
| `kube` | Kubernetes 설정 |
| `git_clone` | Git 저장소 복제 |
| `sync` | 동기화 작업 |
| `k9s` | K9s 구성 |

## 📝 예제

### 예제 1: 최소 설정

```yaml
---
- hosts: localhost
  become: true
  vars_files:
    - vault.yml
  roles:
    - ansible-role-kubya
```

### 예제 2: Kubernetes 없이 실행

```yaml
---
- hosts: localhost
  become: true
  vars:
    use_kube: false
    git_clone: true
  vars_files:
    - vault.yml
  roles:
    - ansible-role-kubya
```

### 예제 3: 패키지 설치만 실행

```bash
ansible-playbook playbook.yml --tags pkg
```

### 예제 4: Vault 파일 생성

민감한 정보를 저장할 vault 파일을 생성합니다:

```bash
ansible-vault create vault.yml
```

vault.yml 내용:

```yaml
---
vault_enc_id_rsa_key: |
  -----BEGIN RSA PRIVATE KEY-----
  [Your SSH private key content]
  -----END RSA PRIVATE KEY-----

vault_enc_id_rsa_pub: "ssh-rsa AAAAB3... your-email@example.com"
vault_enc_gh_user: "your-github-username"
vault_enc_gh_token: "ghp_xxxxxxxxxxxxxxxxxxxx"
vault_enc_aval_pw: "your-ansible-vault-password"
```

### 예제 5: Vault와 함께 실행

```bash
ansible-playbook playbook.yml --ask-vault-pass
```

## 🔒 보안

이 Role은 민감한 정보를 다룹니다. 다음 보안 지침을 따르세요:

1. **Ansible Vault 사용**: 모든 민감한 변수는 Ansible Vault로 암호화하세요
2. **파일 권한**: SSH 키는 자동으로 600 권한으로 설정됩니다
3. **토큰 관리**: GitHub 토큰은 최소 권한 원칙을 따라 설정하세요
4. **버전 관리**: vault 파일을 Git에 커밋하지 마세요 (.gitignore에 추가)

### 권장 .gitignore 설정

```
vault.yml
*.vault
```

## 🛠️ 디버깅

문제가 발생한 경우 다음 명령어로 상세 로그를 확인할 수 있습니다:

```bash
# 상세 출력
ansible-playbook playbook.yml -v

# 더 상세한 출력
ansible-playbook playbook.yml -vvv

# 특정 태스크만 실행
ansible-playbook playbook.yml --start-at-task="Create SSH directory"
```

## 🤝 기여

버그 리포트, 기능 제안, Pull Request를 환영합니다!

## 📄 라이선스

이 프로젝트는 GPL-2.0-or-later 라이선스 하에 배포됩니다.

## 👤 저자

**CodeSuiteApp**

- GitHub: [@codesuiteapp](https://github.com/codesuiteapp)

## 📞 지원

문제가 발생하거나 질문이 있으신 경우:

1. [GitHub Issues](https://github.com/codesuiteapp/ansible-role-kubya/issues)에 문의
2. 상세한 에러 로그와 환경 정보를 포함해 주세요

---

⭐ 이 프로젝트가 유용하다면 Star를 눌러주세요!
