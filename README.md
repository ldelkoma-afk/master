# 사용 방법 (Usage Instructions)

## 시작하기 (Getting Started)

이 저장소를 사용하는 방법을 안내합니다.

### 1. 저장소 클론

```bash
git clone <repository-url>
cd master
```

### 2. 브랜치 확인

```bash
git branch -a
```

### 3. 작업 브랜치 생성

새 기능이나 버그 수정 작업 시 별도의 브랜치를 만들어 사용하세요.

```bash
git checkout -b feature/your-feature-name
```

### 4. 변경사항 커밋

```bash
git add .
git commit -m "설명: 변경 내용 요약"
```

### 5. 원격 저장소에 푸시

```bash
git push -u origin feature/your-feature-name
```

## 기여 방법 (Contributing)

1. 이 저장소를 포크(Fork)합니다.
2. 새 브랜치를 생성합니다: `git checkout -b feature/my-feature`
3. 변경사항을 커밋합니다: `git commit -m "Add: my feature"`
4. 브랜치에 푸시합니다: `git push origin feature/my-feature`
5. Pull Request를 생성합니다.

## 문의 (Contact)

문제가 있거나 질문이 있으면 Issue를 등록해 주세요.
