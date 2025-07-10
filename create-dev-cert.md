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
# 도메인별 디렉터리 생성 (docker volume 구조와 동일하게)
mkdir .\letsencrypt\live\arena-dev.com

# mkcert 로 인증서·키 생성
mkcert `
  -key-file ".\letsencrypt\live\arena-dev.com\privkey.pem" `
  -cert-file ".\letsencrypt\live\arena-dev.com\fullchain.pem" `
  arena-dev.com
```
위 명령으로

- privkey.pem → 비밀키
- fullchain.pem → 루트 CA까지 묶은 인증서

가 infra\letsencrypt\live\arena-dev.com 에 만들어집니다.

만약 *.arena-dev.com과 같은 와일드카드 인증서를 생성하려면 다음과 같이 수행합니다.
```ps
mkdir .\letsencrypt\live\arena-dev.com

mkcert `
  -key-file ".\letsencrypt\live\arena-dev.com\privkey.pem" `
  -cert-file ".\letsencrypt\live\arena-dev.com\fullchain.pem" `
  "*.arena-dev.com" "arena-dev.com"
```

nginx의 구성 설정을 다음과 같이 구성 가능합니다.
```nginx
server {
    listen 443 ssl http2;
    server_name arena-dev.com *.arena-dev.com;

    ssl_certificate     /etc/letsencrypt/live/arena-dev.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/arena-dev.com/privkey.pem;
    …
}
```