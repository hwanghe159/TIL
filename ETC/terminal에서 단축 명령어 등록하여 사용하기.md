## terminal에서 단축 명령어 등록하여 사용하기

1. `.zshrc` 열기

    `vi ~/.zshrc` 

2. 등록하고자 하는 단축 명령어 등록

   예 : `alias st='open /Applications/Sourcetree.app'`

3. `:wq!`로 변경사항 저장 후 종료

4. 변경사항 적용

   `source ~/.zshrc`

5. 터미널 앱에서 정상 작동하는지 확인

   예 : `st` 를 입력하면 소스트리가 실행되는지 확인

