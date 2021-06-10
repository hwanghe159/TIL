- 발표영상 : https://youtu.be/cflK7FTGPlg
- 발표자료 (원페이지뷰) : https://github.com/benelog/entity-dev/blob/master/src/index.adoc
- 발표자료 (슬라이드) : https://benelog.github.io/entity-dev/

## ENTITY를 감추기

### DTO 이름 고민

- 역할별로 구분된 DTO 정의 예
  - 이슈 조회 json 응답 : IssueResponse, IssueDto, IssueDetailDto
  - 이슈 생성 json 요청 : IssueCreationRequest, IssueCreationCommand
  - 이슈 조회 조건 : IssueQuery, IssueCriteria
  - 이슈 DB 통계 조회 결과 : IssueStatRow
  - 이슈 + 코멘트 복합 조회 결과 : IssueCommentRow

## AGGREGATE로 ENTITY 간의 선긋기

