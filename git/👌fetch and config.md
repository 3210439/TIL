# 👌fetch and config

## Fetch와 pull의 차이

- fetch: 원격 저장소의 최신 커밋을 로컬로 가져오기만 한다.
- pull: 원격 저장소의 최신 커밋을 로컬로 가져와 merge 또는 rebase 한다.

### fetch한 내역 적용 전 살펴보기

1. 원격의 main 브랜치에 커밋 추가
- git checkout origin/main으로 확인해보기

1. 원격의 변경사항 fetch
- git checkout origin/main 으로 확인해보기
- pull로 적용

### 원격의 새 브랜치 확인

- git checkout origin/(브랜치명)
- git switch -t origin/(브랜치명)

## git help

---

```jsx
git (명령어) -h
```

- 해당 명령어의 설명과 옵션 보기

```jsx
git help (명령어)
```

## git config

---

config를 —global과 함께 지정하면 전역으로 설정된다. 역으로 생각해보면 —global을 제외하면 로컬 설정을 할 수 있다.

- 현재 모든 설정 확인하기

```jsx
git config (global) --list
```

- 에디터에서 보기

```jsx
git config (global) -e
```

- 기본 브랜치 설정

```jsx
git config --global init.defaultBranch main
```

- push시 로컬과 동일한 브랜치명으로

```jsx
git config --global push.default current
```

### 단축키 설정

```jsx
git config --global alias.(단축키) "명령어"
```

- 예시: git config —global [alias.cam](http://alias.cam) “commit -am”

## 커밋 Type

| 타입 | 설멸 |
| --- | --- |
| feat | 새로운 기능 추가 |
| fix | 버그 수정 |
| docs | 문서 수정 |
| style | 공백, 세미콜론 등 스타일 수정 |
| refactor | 코드 리팩토링 |
| pert | 성능 개선 |
| test  | 테스트 추가 |
| chore | 빌드 과정 또는 보조 기능(문서 생성기능 등) 수정 |

예시

```jsx
feat: 압축파일 미리보기 기능 추가

사용자의 편의를 위해 압축을 풀기 전에
다음과 같이 압축파일 미리보기를 할 수 있도록 함
 - 마우스 오른쪽 클릭
 - 윈도우 탐색기 또는 맥 파인더의 미리보기 창

Closes #125
```

### Gitmoji

깃 이모지 - 깃 커밋을 재밌게 만들 수 있다.