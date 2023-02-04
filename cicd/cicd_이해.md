## CI/CD

### 수동 배포

1. docker를 통한 빌드

   ```docker
   FROM gradle:7.6-jdk17

   ARG APP_TAG

   # 해당 경로가 없을 경우 자동으로 디렉토리 생성
   WORKDIR /작업경로

   # public 레포인 경우 가능
   RUN git clone 클론 주소
   # locales 변경을 위한 업데이트 및 설치
   # -y 옵션은 설치 시 나타나는 모든 물음에 yes
   RUN apt-get update && apt-get install -y locales
   # locales 정보 추가
   RUN localedef -f UTF-8 -i ko_KR ko_KR.UTF-8

   # 환경 변수 설정
   ENV LANG=ko_KR.UTF-8 \
       LANGUAGE=ko_KR.UTF-8 \
       LC_ALL=ko_KR.UTF-8

    # 빌드를 위한 entrypoint.sh
    # 컨테이너가 실행될 때 수행할 명령 지정
    COPY docker/data/entrypoint.${APP_TAG}.sh /entrypoint.sh
    COPY docker/data/application.yml /application.yml
   ```

   ```bash
   #!/bin/bash

   cp /application.yml /프로젝트경로/src/main/resources/application.yml

   cd /프로젝트경로

   git pull

   gradle build --daemon --build-cache --parallel

   java -Djarmode=layertools -jar /프로젝트경로/build/libs/프로젝트명-0.0.1-SNAPSHOT.jar extract
   ```

2. 이미지 생성 후 컨테이너 실행
   - 컨테이너 내부에 **layered jar** 파일이 생성
   - `scp` 명령어를 사용하여 원격 서버로 layered jar 파일 전송
3. 원격 서버에서 docker-compose 수행
   - 전송받은 layered jar 파일을 바탕으로 `nginx`, `db`, `app` 컨테이너가 동작
