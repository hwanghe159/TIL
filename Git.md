# Git

### git init : 비어있는 저장소 생성

- .git 디렉토리를 생성한다.

- .git이 로컬 디렉토리.

### git의 객체는?

- blob
- commit
- tree
- tag

- branch는 커밋에 대한 참조일뿐 객체가 아님.

### git add : 스테이지에 파일을 추가하는 명령

- git add 하는 시점에 로컬 저장소(.git)에 추가된다.

### git commit

- 커밋 : 작업 디렉토리 스냅샷, 세이브 포인트

### Git Submodule commit 방법

1. parent의 디렉토리(repo)에서 submodule에 있는 파일을 수정한다.
2. submodule의 디렉토리(repo)에 가서 add, commit, **push** 한다.
3. parent의 repo에서 `git submodule update --remote --merge [submodule의 경로]` 을 친다.
4. parent의 repo에서 `git add .` `git commit` 한다
5. parent의 수정 내역을 add, commit 한다.

그러니까 서브모듈과 부모 레포지토리는 아예 다른 걸로 인식해야한다.

서브모듈에 푸시를 한 후 부모 레포로 가서 update후, commit, push한다.

