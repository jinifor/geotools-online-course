---
name: course-builder
description: 온라인 강의 제작 전체 워크플로우를 조율하는 스킬. 커리큘럼, 교안, 퀴즈, 실습과제, 전체 교육과정 패키지를 새로 만들거나 수정할 때 사용한다. Use when Codex needs a full course-building pipeline from a topic, learner profile, or existing training materials.
---

# Course Builder

이 스킬은 이 프로젝트의 전체 워크플로우를 조율한다.

## 입력에서 먼저 확인할 것

- 과정 주제와 목표 역량
- 대상 학습자 수준
- 총 학습 시간 또는 과정 규모
- 실습 언어, 도구, 플랫폼
- 기존 커리큘럼/교안/평가 자료 존재 여부

## 모드 선택

- 풀 파이프라인: 커리큘럼, 교안, 퀴즈, 실습, 리뷰를 모두 만든다.
- 커리큘럼 모드: `01_curriculum.md` 중심으로 진행한다.
- 교안 모드: 기존 커리큘럼을 기반으로 `02_lesson_plans.md`를 만든다.
- 퀴즈 모드: 기존 커리큘럼과 교안을 기반으로 `03_quizzes.md`를 만든다.
- 실습 모드: 기존 커리큘럼과 교안을 기반으로 `04_labs.md`를 만든다.

## 표준 실행 순서

1. 입력을 `_workspace/00_input.md`에 정리한다.
2. 커리큘럼이 없으면 `curriculum-designer`로 `01_curriculum.md`를 만든다.
3. 교안, 퀴즈, 실습은 각각 `content-writer`, `quiz-maker`, `lab-designer`에 맡긴다.
4. 마지막에 `course-reviewer`로 전체 결과를 교차 검증한다.
5. 최종 응답에서는 생성한 파일, 핵심 가정, 남은 리스크를 요약한다.

## 산출물 기준

- `_workspace/01_curriculum.md`
- `_workspace/02_lesson_plans.md`
- `_workspace/03_quizzes.md`
- `_workspace/04_labs.md`
- `_workspace/05_review_report.md`

기존 산출물이 있으면 재사용하고, 새로 작성한 부분과 보완한 부분을 구분한다.

## 품질 게이트

- 모든 퀴즈와 실습은 학습목표 ID에 매핑되어야 한다.
- 교안은 커리큘럼의 레슨 순서와 목표를 존중해야 한다.
- 실습은 난이도가 점진적으로 높아져야 한다.
- 리뷰에서 필수 수정이 남아 있으면 완료 처리하지 않는다.

## 범위 밖 작업

아래는 기본 범위가 아니다.

- LMS 구축
- 영상 제작 및 편집
- 수강생 운영
- 플랫폼 배포와 결제
