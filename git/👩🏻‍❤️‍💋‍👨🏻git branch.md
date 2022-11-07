# 👩🏻‍❤️‍💋‍👨🏻git branch

### 브랜치 기본 명령어들

브렌치 확인 명령어

```jsx
git branch
```

브렌치 생성 명령어

```jsx
git branch <branchname>
```

브렌치 생성과 동시에 이동 명령어

```jsx
git switch -c <branchname>
```

브렌치 이동 명령어

```jsx
git switch <branchname>
```

브렌치 삭제 명령어

```jsx
git branch -d <branchname>
```

### 로컬에서 생성한 브랜치 원격 저장소에 반영하기

- 해당 브랜치에 커밋한 내역도 같이 반영된다.

```jsx
git branch -u origin <branchname>
```

원격의 브랜치 삭제

```jsx
git push origin --delete <branchname>
```

### 원격의 변경사항 확인

첫 번째 방법

```jsx
git fetch
```

두 번째 방법

```jsx
git branch -a
```

원격에서 생성된 브랜치 받아온다음 연결하기

```jsx
git switch -t origin/from-remote
```

### 깃허브 브랜치를 합처주는 Merge

브랜치를 합친다는 것은 한쪽의 브랜치로 브랜치를 합친 다는 것이다. 주로 main/master 브랜치로 merge를 하게 된다. 그러면 git switch master로 브랜치를 이동한 다음에 합치길 원하는 브랜치를 아래의 명령어로 실행한다.

```jsx
git merge <branchname>
```

### 병합된 브랜치 삭제

병합을 완료한 뒤 브랜치를 제거한다. 브랜치를 제거하지 않고 계속해서 새로운 브랜치가 생성되면 브랜치를 식별할 때 방해가 될 수 있다.

```jsx
git branch -d <branchname>
```

### 원격의 브랜치 삭제

로컬에서 브랜치를 지워도 원격에는 반영되지 않는다. 추가적으로 아래의 명령어로 원격 저장소에도 브랜치 삭제를 반영한다.

```jsx
git push origin --delete <branchname>
```
