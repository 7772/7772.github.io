---
layout: post
title: Github Actions In React Native
comments: true
categories: Blog
---

<br/>

### [Github Actions](https://github.com/features/actions)

> 프로젝트의 Workflow를 쉽게 구성할 수 있도록 하는 Github의 기능.

<br/>

### 목표

- 테스트 자동화
- 빌드 자동화
- 원격 저장소(s3)에 Artifacts 파일 업로드
- Slack Notification

<br/>

### Github Secrets

> 원격 레포지토리에 업로드 할 수 없는 소스 코드를 Github Actions에서 사용하기 위함.

1. 안드로이드 빌드 패스워드 (KEY_PASSWORD, UPLOAD_PASSWORD)
2. 안드로이드 릴리즈 키 
3. AWS 정보
4. Slack Webhook Endpoint

<br/>

### 테스트 자동화

```
jobs:
  install-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install npm dependencies
        run: |
          yarn install && npx jetify          
      - name: Build typescript & run all tests
        run: |
          yarn build && yarn test --forceExit --detectOpenHandles
```

- `yarn`, `npm`, `npx` 등의 패키지 관리 라이브러리는 기본적으로 설치되어 있음
- Actions 환경에서 테스트 실행 후 종료되지 않는 이슈가 발생한다면 `--forceExit --detectOpenHandles` 옵션을 추가하여 실행할 것

<br/>

### 빌드 자동화

1. 의존성 설치

```
build-android:
  needs: [install-and-test]
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v2
    - name: Install npm dependencies
      run: |
        yarn install && npx jetify
```

- `needs` 에 본 작업 실행 전 필요한 작업을 작성함 (동기적으로 실행)

<br/>

2. Release Key 발급

```
- name: Generate Release Key
  run: |
    echo "${{ secrets.RELEASE_KEY }}" | base64 -d > android/app/app-key.keystore
```

- `xx.keystore` 파일은 깃허브에 업로드 하지 않기 때문에 파일을 base64로 전환한 후 Github Secrets로 등록하여 사용 (`${{ secrets.RELEASE_KEY }}`)

<br/>

3. Build

```
- name: Build Android Release
  run: |
    cd android && ./gradlew assembleRelease -PAPP_STORE_PASSWORD='${{ secrets.APP_UPLOAD_KEY_PASSWORD }}' -PAPP_KEY_PASSWORD='${{ secrets.APP_UPLOAD_STORE_PASSWORD }}' && cd ..
```

- 안드로이드 빌드 패스워드 2가지를 Github Secrets로 등록하여 사용

<br/>

4. Branch 이름 추출

```
- name: Extract branch name
  shell: bash
  run: echo "##[set-output name=branch;]$(echo ${GITHUB_REF#refs/heads/})"
  id: extract_branch
```

- S3 업로드 시 파일 이름에 작업 브랜치 이름을 사용하기 위하여 `yml`에 `key`로 등록한 후 사용함

<br/>

5. S3 업로드

```
- name: Upload artifact to S3
  uses: shallwefootball/upload-s3-action@v1.1.2
  with:
    aws_key_id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws_secret_access_key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws_bucket: ${{ secrets.S3_BUCKET }}
    source_dir: 'android/app/build/outputs/apk/release/'
    destination_dir: release-apk:${{ steps.extract_branch.outputs.branch }}  
```

- AWS 계정 정보(`key_id`, `secret_access_key`)와 bucket 정보를 입력해야 함

<br/>

6. Slack Notification

```
- name: Slack notification
  if: success()
  uses: rtCamp/action-slack-notify@master
  env:
    SLACK_CHANNEL: '@username'
    SLACK_COLOR: '#3278BD'
    SLACK_MESSAGE: https://s3.ap-northeast-2.amazonaws.com/{BUCKET_NAME}/{FILE_PATH}/{FILE_NAME}
    SLACK_TITLE: 'App Build 성공'
    SLACK_USERNAME: 'build-bot'
    SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
```

- [Slack Incomming webhook](https://olulo.slack.com/apps/A0F7XDUAZ-incoming-webhooks)을 이용하여 슬랙에 메세지를 전송
- Webhook Endpoint는 Github Secrets에 등록하여 사용함
- `SLACK_MESSAGE`에는 업로드된 apk 파일을 다운로드 할 수 있는 public endpoint를 기입함

<br/>
<br/>
