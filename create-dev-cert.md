# mkcert 설치 (Windows)
## Chocolatey 가 없다면 먼저 설치
관리자 권한 PowerShell 에서:
```ps
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

## mkcert 설치
```ps
choco install mkcert -y
```

# 로컬 CA 신뢰하기
PowerShell 에서:
```ps
mkcert -install
```

# 개발용 도메인 인증서 만들기
개발용 도메인 (ex: arena-dev.com)
```ps
mkdir .\letsencrypt\live\arena-dev.com

mkcert `
  -key-file ".\letsencrypt\live\arena-dev.com\privkey.pem" `
  -cert-file ".\letsencrypt\live\arena-dev.com\fullchain.pem" `
  "*.arena-dev.com" "arena-dev.com"
```
위 명령으로

- privkey.pem → 비밀키
- fullchain.pem → 루트 CA까지 묶은 인증서

가 infra\letsencrypt\live\arena-dev.com 에 만들어집니다.
