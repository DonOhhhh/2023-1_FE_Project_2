# [2기 / 프로젝트 2] 노션 클로닝

## 프로젝트 소개 😎

VanillaJS를 활용한 Notion 클로닝 프로젝트 입니다.

## 노션 클로닝 프로젝트

---

노션 클로닝 프로젝트 강의 내의 API와 사용해야하는 API가 상이합니다.
아래 API들을 꼭 확인하시고 실습 진행해주세요~ 감사합니다:)
<br/>
<br/>

### 기본 요구사항

---

바닐라 JS만을 이용해 노션을 클로닝합니다.

기본적인 레이아웃은 노션과 같으며, 스타일링, 컬러값 등은 원하는대로 커스텀합니다.

-   글 단위를 Document라고 합니다. Document는 Document 여러개를 포함할 수 있습니다.
-   화면 좌측에 Root Documents를 불러오는 API를 통해 루트 Documents를 렌더링합니다.
    -   Root Document를 클릭하면 오른쪽 편집기 영역에 해당 Document의 Content를 렌더링합니다.
    -   해당 Root Document에 하위 Document가 있는 경우, 해당 Document 아래에 트리 형태로 렌더링 합니다.
    -   Document Tree에서 각 Document 우측에는 + 버튼이 있습니다. 해당 버튼을 클릭하면, 클릭한 Document의 하위 Document로 새 Document를 생성하고 편집화면으로 넘깁니다.
-   편집기에는 기본적으로 저장 버튼이 없습니다. Document Save API를 이용해 지속적으로 서버에 저장되도록 합니다.
-   History API를 이용해 SPA 형태로 만듭니다.
    -   루트 URL 접속 시엔 별다른 편집기 선택이 안 된 상태입니다.
    -   /documents/{documentId} 로 접속시, 해당 Document 의 content를 불러와 편집기에 로딩합니다.

### 기본 형태

![css](./demo.png)

### 보너스 요구사항

---

- [x]  기본적으로 편집기는 textarea 기반으로 단순한 텍스트 편집기로 시작하되, 여력이 되면 div와 contentEditable을 조합해서 좀 더 Rich한 에디터를 만들어봅니다.
- [x]  편집기 최하단에는 현재 편집 중인 Document의 하위 Document 링크를 렌더링하도록 추가합니다.
- [ ]  편집기 내에서 다른 Document name을 적은 경우, 자동으로 해당 Document의 편집 페이지로 이동하는 링크를 거는 기능을 추가합니다.
- [ ]  그 외 개선하거나 구현했으면 좋겠다는 부분이 있으면 적극적으로 구현해봅니다!

### API 사용법

---

1. 기본사항

> 모든 API에는 headers에 아래의 값을 넣어야 합니다. header에 해당 값이 누락이 되면 API 호출에 실패합니다.

`'x-username': 'DonOhhhh'`

1. Root Documents 가져오기

-   API URL : https://cnu1.notion.edu-api.programmers.co.kr/documents
-   method : GET

> 전체 Document의 구조를 트리 형태로 가져옵니다. Response의 형태는 아래와 같습니다.

```js
[
    {
        id: 1, // Document id
        title: "노션을 만들자", // Document title
        documents: [
            {
                id: 2,
                title: "블라블라",
                documents: [
                    {
                        id: 3,
                        title: "함냐함냐",
                        documents: [],
                    },
                ],
            },
        ],
    },
    {
        id: 4,
        title: "hello!",
        documents: [],
    },
];
```

3.  특정 Document의 content 조회하기

-   API URL : https://cnu1.notion.edu-api.programmers.co.kr/documents/{documentId}
-   method : GET

> Response의 형태는 아래와 같습니다.

```js
{
    "id": 1,
    "title": "노션을 만들자",
    "content": "즐거운 자바스크립트의 세계!",
    "documents": [
        {
            "id": 2,
            "title": "",
            "createdAt": "",
            "updatedAt": ""
        }
    ],
    "createdAt": "",
    "updatedAt": ""
}
```

4.  Document 생성하기

-   API URL : https://cnu1.notion.edu-api.programmers.co.kr/documents
-   method : POST

> request body에 JSON 형태로 아래처럼 값을 넣어야 합니다.

```js
{
    "title": "문서 제목",
    // parent가 null이면 루트 Document가 됩니다.
    // 특정 Document에 속하게 하려면 parent에
    // 해당 Document id를 넣으세요.
    "parent": null
}
```

> 생성에 성공하면 response에 아래처럼 생성된 결과를 내려줍니다.

```js
{
    "id": 6,
    "title": "문서 제목",
    "createdAt": "생성된 날짜",
    "updatedAt": "수정된 날짜"
}
```

5. 특정 Document 수정하기

-   API URL : https://cnu1.notion.edu-api.programmers.co.kr/documents/{documentId}
-   method : PUT

> request body에 수정할 내용을 JSON 형태로 넣으면 됩니다.

```js
{
    "title": "제목 수정",
    "content": "내용 수정"
}
```

> 수정에 성공한 경우 response에 다음 결과를 반환합니다.

```js
{
  "id": 148,
  "title": "제목 수정",
  "content": "내용 수정",
  "parent": {
    "id": "부모노드 ID",
    "title": "부모노드 title",
    "content": "부모노드 content",
    "parent": "조부모노드 id", // integer
    "username": "username",
    "created_at": "부모 생성일자",
    "updated_at": "부모 수정일자"
  }, // 만약 root 노드라면 {}
  "username": "username",
  "created_at": "생성일자",
  "updated_at": "수정일자"
}
```

6. 특정 Document 삭제하기

-   API URL : https://cnu1.notion.edu-api.programmers.co.kr/documents/{documentId}
-   method : DELETE

> documentId에 해당하는 Document를 삭제합니다.<br/>
> 삭제에 성공한 경우 response에 다음 결과를 반환합니다.

```js
{
    "id": 1,
    "title": "노션을 만들자",
    "content": "즐거운 자바스크립트의 세계!",
    "parent": null,
    "username": "roto",
    "created_at": "생성한 날짜",
    "updated_at": "수정한 날짜"
}
```

> 만약 하위 documents가 있는 document를 삭제한 경우, 하위 documents 등은 상위 document가 없어지므로 root document로 인식됩니다.
