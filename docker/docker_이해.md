## Docker

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh

# 일반 유저 도커 실행 권한 부여
sudo usermod -aG docker [USER]
logout

# docker-compose 설치
sudo apt install docker-compose -y
```

- 스크립트로 설치하는 방법이 가장 깔끔하다고 한다.

### 도커를 사용하는 이유

가장 큰 장점은 서버 **인프라 일관성 유지**

- 도커 프로그램 + 도커 이미지만 있으면 어느 서버 사양에서도 동일하게 동작하도록 보장

컨테이너가 하나의 프로세스이므로 다른 프로세스에 영향을 끼치지 않는다.

- 하나의 샌드박스의 역할을 수행

### docker hub

- 도커 이미지를 모아두는 docker registry
- repository처럼 public, private가 존재
- 자체적으로 구축할 수도 있고 다른 클라우드 서비스에도 존재
  - ex) aws ECR
- 이미지의 태그 확인 가능
  - `alpine`: 최소한의 설정만을 담아놓은 환경

### docker compose 작성하기

- `dockerfile` 혹은 `docker cli`로 실행하는 방법도 존재

```yaml
version: "3.5"

services:
  nginx:
    container_name: nginx
    image: nginx:1.23-alpine
    volumes:
      - ./:/home/keeper
    ports:
      - 80:80
      - 443:443
    command: sh -c "nginx && tail -f /dev/null"
```
