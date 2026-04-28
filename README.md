# Claude Code 개발 환경 초기 세팅 가이드

> Windows에서 Claude Code(AI 코딩 도우미)를 처음 사용하시는 분들을 위한 단계별 안내입니다.
> 컴퓨터에 익숙하지 않으셔도 차근차근 따라하시면 약 30~60분 안에 완료할 수 있습니다.

---

## 이 가이드로 무엇을 만드나요?

- VS Code 안에서 Claude Code를 격리된 컨테이너 환경으로 사용 → 시스템에 영향 안 주고 안전하게 실험 가능
- 모든 대화 기록·메모리·설정이 자동으로 내 PC의 폴더(`C:\claude_workspace\.claude_storage`)에 저장 → **컨테이너를 다시 만들어도 데이터가 사라지지 않음**
- WSL2 안의 Docker Engine(오픈소스, 완전 무료)을 사용 → 별도 라이선스 부담 없음, 가벼움

## 준비물 및 사양

- Windows 10 (build 19041 이상) 또는 Windows 11
- 디스크 여유 공간 약 5~10 GB
- 인터넷 연결
- Anthropic 계정 (Claude Pro 구독 **또는** API 키 — 8단계에서 안내)

---

## 1단계: WSL2 + Ubuntu 설치

WSL2는 Windows 안에서 Linux를 돌릴 수 있게 해주는 기능입니다. 이 안에 Docker Engine을 설치할 거예요.

### 1-1. PowerShell을 **관리자 권한으로** 열기

1. 화면 좌측 하단의 **시작 메뉴(Windows 로고)** 클릭
2. → **powershell** 입력
3. 검색 결과의 **Windows PowerShell**에 마우스를 올리고 **오른쪽 버튼 클릭** → **"관리자 권한으로 실행"**
4. "이 앱이 디바이스를 변경할 수 있도록 허용..." 창이 뜨면 **예** 클릭

### 1-2. 설치 명령어 실행

PowerShell 창에 → **`wsl --install`** 입력 후 Enter.

> 명령어 복사 붙여넣기를 하려면 PowerShell 창에 마우스 오른쪽 버튼을 누르면 됩니다.

설치가 시작되며 몇 분 걸립니다.

### 1-3. 컴퓨터 재시작

부팅 후 자동으로 **Ubuntu 설정 창**이 뜹니다. 사용자 이름과 비밀번호를 정해주세요.

> **비밀번호는 입력해도 화면에 표시되지 않습니다 (정상)**. 잊지 않게 메모해두세요. 이 비밀번호는 나중에 `sudo` 명령어 사용할 때 입력해야 합니다.

설정 끝나면 Ubuntu 터미널이 열린 상태로 대기합니다 (`~$` 프롬프트가 보임).

**확인 방법:** PowerShell을 다시 열고 → **`wsl --status`** 입력 → "기본 버전: 2"가 보이면 성공.

---

## 2단계: WSL Ubuntu 안에 Docker Engine 설치

> 이 단계는 **WSL Ubuntu 터미널 안에서** 입력합니다. 시작 메뉴 → **Ubuntu** 검색 → 실행. 프롬프트가 `<유저이름>@<PC이름>:~$` 형태로 나타나면 됩니다.

### 2-1. Docker 자동 설치 스크립트 실행

Ubuntu 터미널에 아래 명령어를 한 줄씩 입력 (붙여넣을 때는 마우스 오른쪽 버튼 또는 `Ctrl+Shift+V`):

```
curl -fsSL https://get.docker.com | sh
```

설치는 1~3분 정도 걸립니다. `sudo` 비밀번호를 물어보면 1-3에서 정한 비밀번호 입력 (화면에 안 보임).

### 2-2. 내 사용자를 docker 그룹에 추가

```
sudo usermod -aG docker $USER
```

이걸 해야 매번 `sudo` 안 붙이고 docker 명령을 쓸 수 있습니다.

### 2-3. WSL에 systemd 활성화

Docker가 부팅 시 자동으로 켜지도록 설정합니다.

```
echo -e "[boot]\nsystemd=true" | sudo tee /etc/wsl.conf
```

### 2-4. WSL 재시작

PowerShell(Ubuntu 터미널 아님!)을 열고:

```
wsl --shutdown
```

그리고 다시 Ubuntu 터미널을 시작 메뉴에서 실행.

### 2-5. 동작 확인

다시 열린 Ubuntu 터미널에서:

```
docker version
```

`Client`와 `Server` 정보가 둘 다 출력되면 성공. 만약 "permission denied" 또는 "Cannot connect"가 나오면 PC 재부팅 후 다시 시도.

---

## 3단계: VS Code 설치

### 3-1. 다운로드

웹 브라우저에서 https://code.visualstudio.com/ 접속 → 큰 파란색 **"Download for Windows"** 버튼 클릭.

### 3-2. 설치

다운로드된 `VSCodeUserSetup-x64-...exe`를 더블클릭. 옵션 중 다음 두 개는 체크해두면 편합니다:

- **"PATH에 추가"** (기본 체크됨, 그대로)
- **"바탕 화면에 아이콘 만들기"**

나머지는 기본값. **다음** → **설치** 진행.

**확인 방법:** 바탕화면이나 시작 메뉴에 **Visual Studio Code**가 나타나면 성공.

---

## 4단계: VS Code 필수 확장 설치

### 4-1. VS Code 실행

방금 설치한 VS Code를 실행합니다.

### 4-2. 확장(Extensions) 패널 열기

화면 **왼쪽 사이드바**의 아이콘 중 **정사각형 4개가 묶인 모양** 아이콘을 클릭 (또는 단축키 `Ctrl+Shift+X`).

### 4-3. 다음 두 확장을 검색하여 설치

상단 검색 박스에 입력 → 결과 목록의 첫 번째 항목의 **Install** 버튼 클릭:

| 검색어 | 만든이 | 설명 |
|---|---|---|
| **WSL** | Microsoft | WSL과 VS Code 연동 |
| **Dev Containers** | Microsoft | 컨테이너 환경 자동 구성 |

**확인 방법:** VS Code 화면 **좌측 하단 모서리**에 파란색 `><` 모양 아이콘이 생기면 성공 (WSL/Dev Containers 활성화 표시).

---

## 5단계: 작업 폴더 만들기

> **경로는 자유롭게 정하세요.** 이 가이드에서는 예시로 `C:\claude_workspace`를 사용하지만, `D:\claude_workspace`나 `C:\Users\<내이름>\Documents\claude_workspace`처럼 본인이 원하는 곳에 만들어도 됩니다. 단, 다음 두 가지를 확인하세요:
>
> - **저장 용량**: 작업 파일과 npm 패키지가 들어가므로, 작업 폴더가 위치할 드라이브에 **최소 5~10 GB의 여유 공간**이 있어야 합니다. 확인 방법: 파일 탐색기 좌측에서 **내 PC** 클릭 → 각 드라이브 아래에 남은 용량이 표시됨.
> - **경로에 한글·공백 피하기**: 가능하면 폴더 이름과 경로에 한글이나 띄어쓰기가 없도록.
>
> 이후 단계에서 `C:\claude_workspace`로 표기된 부분은 본인이 정한 경로로 바꿔서 읽으시면 됩니다.

### 5-1. 폴더 생성

파일 탐색기를 열고 원하는 위치(예: `C:\` 드라이브)로 이동 → 빈 곳에 **마우스 오른쪽 버튼** → **새로 만들기 → 폴더** → 이름을 `claude_workspace`로 입력.

### 5-2. VS Code를 WSL 모드로 시작해서 폴더 열기

이게 핵심입니다. 평소처럼 VS Code로 폴더를 열면 안 되고, **WSL 모드로** 열어야 합니다.

1. VS Code 실행
2. `Ctrl+Shift+P` → 명령 팔레트 열기
3. → **WSL: Open Folder in WSL** 입력 후 Enter
4. 경로 입력란에 → **`/mnt/c/claude_workspace`** 입력 후 Enter (만약 본인이 `D:\claude_workspace`로 만들었다면 `/mnt/d/claude_workspace`)

> Windows의 `C:\` 드라이브는 WSL 안에서는 `/mnt/c/`로 보입니다. 같은 폴더를 WSL 시각으로 접근하는 것뿐입니다.

VS Code가 다시 열리며 **좌측 하단**에 `WSL: Ubuntu` 같은 표시가 나타나면 WSL 모드로 열린 것.

> 처음 열 때 "이 폴더의 작성자를 신뢰하시겠습니까?" 창이 뜨면 **예, 작성자를 신뢰합니다** 클릭.

### 5-3. 데이터 저장 폴더 미리 생성

VS Code 좌측 사이드바 **파일 모양 아이콘** 클릭 → 폴더 트리 위쪽 빈 공간에 **마우스 오른쪽 버튼** → **새 폴더** → 이름을 정확히 `.claude_storage`로 입력 (앞에 점 `.` 꼭 붙이기).

> 점으로 시작하는 폴더는 "숨김" 폴더로 취급되어 일부 환경에서는 잘 안 보일 수 있지만 정상입니다.

---

## 6단계: Dev Container 설정 파일 만들기

### 6-1. `.devcontainer` 폴더 생성

5-3과 동일한 방법으로 **새 폴더** → 이름 `.devcontainer` (점 포함).

### 6-2. `devcontainer.json` 파일 생성

방금 만든 `.devcontainer` 폴더를 클릭한 상태로, 폴더 트리 위 **새 파일** 아이콘 클릭 → 파일 이름을 `devcontainer.json`으로 입력.

### 6-3. 설정 내용 붙여넣기

새로 만든 빈 파일이 우측에 열립니다. 아래 회색 박스 안의 내용을 **그대로** 복사해서 붙여넣으세요 (박스의 테두리나 ``` 같은 마커는 복사되지 않습니다 — 박스 안의 텍스트만 복사하면 됩니다):

```json
{
  "name": "Claude Code Dev",
  "image": "mcr.microsoft.com/devcontainers/base:ubuntu",
  "features": {
    "ghcr.io/devcontainers/features/node:1": { "version": "20" }
  },
  "mounts": [
    "source=${localWorkspaceFolder}/.claude_storage,target=/home/vscode/.claude,type=bind"
  ],
  "postCreateCommand": "npm install -g @anthropic-ai/claude-code",
  "customizations": {
    "vscode": {
      "extensions": [
        "anthropic.claude-code"
      ]
    }
  },
  "remoteUser": "vscode"
}
```

### 6-4. 저장

`Ctrl+S`를 눌러 저장.

> **이 파일이 하는 일 (참고용 — 외워둘 필요 없음):**
> - Ubuntu 기반 컨테이너 생성, Node.js 20 설치
> - 컨테이너 안에서 Claude Code CLI 자동 설치
> - 핵심: 내 PC의 `.claude_storage` 폴더를 컨테이너 안의 `/home/vscode/.claude`로 직접 연결 → 컨테이너에서 만들어지는 모든 대화/메모리/설정 파일이 실제로는 내 PC에 저장됨
> - VS Code Claude Code 확장도 컨테이너 환경에 자동 설치

---

## 7단계: 컨테이너 빌드

### 7-1. Docker 실행 확인

Ubuntu 터미널에서 → **`docker ps`** 입력 후 Enter. 에러 없이 표 헤더가 출력되면 Docker가 잘 돌고 있음.

### 7-2. 컨테이너 빌드 명령

VS Code (WSL 모드 — 좌측 하단에 `WSL: Ubuntu` 표시되어 있어야 함)에서:

1. `Ctrl+Shift+P` → 명령 팔레트 열기
2. 입력란에 → **Dev Containers: Reopen in Container** 입력
3. 자동완성으로 나오면 Enter

### 7-3. 빌드 진행 (5~15분 소요)

VS Code가 닫혔다가 다시 열리며 컨테이너 이미지를 다운로드하고 설치합니다. 우측 하단에 진행 알림이 뜹니다. **화면이 한참 안 바뀌어도 백그라운드에서 작업 중**이니 인내심을 갖고 기다려주세요.

### 7-4. 완료 확인

VS Code **좌측 하단 모서리**의 영역이 `Dev Container: Claude Code Dev`처럼 표시되면 컨테이너 안으로 들어온 것입니다.

---

## 8단계: Claude Code 첫 실행 및 로그인

### 8-1. Claude Code 패널 열기

VS Code **왼쪽 사이드바**에 새로 생긴 Claude Code 아이콘을 클릭하면 우측에 채팅 패널이 열립니다.

### 8-2. 로그인

처음 실행이면 패널에 로그인 안내가 나옵니다:

- **Claude Pro/Max 구독자**: **Sign in with Claude** 버튼 클릭 → 자동으로 브라우저가 열리고 Anthropic 계정 로그인 → 로그인 후 "VS Code로 돌아가시겠습니까?" 메시지 클릭.
- **API 키 사용자**: `https://console.anthropic.com/`에서 API 키 발급 → 패널의 **Use API Key** 옵션 선택 → 입력.

> 어느 쪽이 좋을지 잘 모르겠으면, 일정 양 이상 쓸 거라면 Pro 구독($20/월), 가끔 쓸 거라면 API 키(쓴 만큼 과금)가 일반적입니다. 회사·연구실 정책이 있으면 담당자에게 문의하세요.

### 8-3. 동작 테스트

로그인이 끝나면 채팅창이 활성화됩니다. 시험 삼아 아래 문장을 그대로 입력해보세요.

> 안녕? 내 환경 잘 작동하는지 확인하고 싶어.

답변이 돌아오면 **모든 세팅 완료**입니다.

---

## 내 데이터는 어디에 있나요?

- 모든 대화 기록·메모리·설정·인증 토큰은 **본인이 만든 작업 폴더 안의 `.claude_storage`** 폴더에 저장됩니다 (예: `C:\claude_workspace\.claude_storage\`).
- 이 폴더는 Windows에 그대로 있기 때문에:
  - 컨테이너를 다시 만들거나 삭제해도 데이터가 사라지지 않음
  - 다른 PC로 옮기려면 이 폴더를 통째로 USB나 클라우드 드라이브로 복사
  - 백업하고 싶으면 OneDrive·Google Drive 등에 이 폴더를 복사해두세요
- **주의**: 폴더 안에는 인증 토큰(`.credentials.json`)도 들어있습니다. 다른 사람과 공유하지 마세요.

> `.claude_storage` 폴더는 숨김 폴더라 파일 탐색기에서 안 보일 수 있습니다. 파일 탐색기 상단 메뉴 **보기 → 표시 → 숨긴 항목** 체크.

---

## 안전 사용 수칙

Dev Container로 격리되어 있어 기본적으로는 안전하지만, 다음 사항을 지키면 더 안심하고 쓸 수 있습니다:

- Claude가 명령어 실행이나 파일 수정을 요청할 때 뜨는 **승인 프롬프트를 꼼꼼히 읽고** 의도와 맞는지 확인 후 승인하세요.
- 중요한 데이터(논문, 분석 결과)는 작업 폴더 밖에 두거나 별도 백업해두세요.
- Claude에게 시키지 않은 일을 하는 것 같으면 즉시 `Esc`로 중단.

---

## 자주 발생하는 문제

### Ubuntu 터미널에서 `docker version` 했더니 "Cannot connect to the Docker daemon"
WSL이 systemd로 재시작되지 않은 상태입니다. PowerShell에서 → **`wsl --shutdown`** 실행 후 Ubuntu를 다시 여세요. 그래도 안 되면 PC 재부팅.

### `docker version` 했더니 "permission denied"
2-2 단계의 사용자 그룹 추가가 적용되려면 WSL을 재시작해야 합니다. PowerShell에서 → **`wsl --shutdown`** → Ubuntu 다시 열기.

### "WSL 2 installation is incomplete"
PowerShell을 관리자 권한으로 열고 → **`wsl --update`** 실행 → 재부팅 후 다시 시도.

### VS Code 좌측 하단에 `WSL: Ubuntu`가 안 보임
WSL 모드로 안 열린 상태입니다. 5-2 단계로 돌아가서 `WSL: Open Folder in WSL` 명령으로 다시 여세요.

### 컨테이너 빌드 도중 네트워크 오류
인터넷 연결 확인. 회사·학교 네트워크라면 프록시 설정이 필요할 수 있으니 IT 담당자에게 문의.

### Claude Code 채팅 패널이 안 보여요
컨테이너 빌드가 아직 안 끝났거나 확장 설치가 실패한 것. `Ctrl+Shift+P` → **Dev Containers: Rebuild Container** 선택해서 다시 빌드.

### `.claude_storage` 폴더가 안 보여요
점(`.`)으로 시작하는 폴더는 숨김 폴더입니다. 파일 탐색기 상단 메뉴 **보기 → 표시 → 숨긴 항목**을 켜면 보입니다.

### 그 외 막막할 때
화면을 캡처해서 IT 담당자나 연구실 매니저에게 문의하세요. `Ctrl+Shift+P` → **Dev Containers: Show All Logs** 결과를 함께 첨부하면 진단이 쉬워집니다.

---

## 다음 단계

설치가 끝났으면 일반적인 Claude Code 사용법을 익히면 됩니다.

- 공식 문서: https://docs.claude.com/claude-code
- 채팅창에서 슬래시 명령어: `/help` 입력하면 사용 가능한 명령어 목록 출력
