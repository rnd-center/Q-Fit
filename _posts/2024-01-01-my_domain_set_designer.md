---
title: Allthatfit Designer 적용하기
author: TesterJ
date: 2024-01-23
category: Set3D
layout: post
mermaid: true
---

다음과 같이 운영 환경을 구성합니다. 
해당 서비스는 자바스크립트로 운영됩니다.


### 선행 작업
3D 뷰어를 적용함에 앞서 먼저 어느 곳에서 호출한 것인지, 그리고 어떠한 상품의 정보를 표현할 것인지 알아야 합니다.
1. 상품 정보를 불러오기 위해서 기업 정보를 통해 토큰을 발급하고
2. 해당 토큰을 통해 상품 정보를 호출하는데 각각의 절차는 다음과 같습니다.

```markdown
request>>
  curl -H "Domain: [도메인 주소]" -H "COMP_KEYS: [키 값]" -H "Accept: application/json" https://service.allthatfit.com/getToken/

response>>
  {"_result":true,"_message":"","_code":200,"_value":{"Token":"[토큰 값]","Domain":"[도메인 주소]","Timestamp":"[타임스탬프 값]"}}
```
위와 같이 '_result' 값이 true인 경우 정상적으로 토큰 값을 생성한 것이며 해당 토큰은 '_value'의 'Token'에 정의되어 있습니다.
여기서 타임스탬프 값은 토큰을 발급받은 시간이며 토큰 발급 후 타임스탬프 값을 기준으로 하루동안 해당 토큰이 유효합니다.
(하루가 지나면 토큰을 재발급 받으셔야 합니다.)
키 값은 해당 서비스를 사용하는 사용자에게 비공개가 되어야 합니다.
(회사 고유 키 값으로 공개되었을 시 문의 바랍니다.)

서버의 응답값 구조는 다음과 같습니다.
 - _result : '값'이 정상적인지 확인
 - _message : 오류가 있거나 요구사항이 있는 경우 해당 내용 반환
 - _code : 서버 응답 코드
 - _value : 서버 응답 값  (다른 요청에 대한 응답도 동일 구조이며 이후 _value안의 값만 확인합니다.)



```markdown
request>>
  curl -X POST -d "SAVE_INFO=[저장 데이터가 있는 경우]" -H "Domain: [도메인 주소]" -H "Timestamp: [상기 정의된 타임스탬프 값]" -H "Token: [상기 정의된 토큰 값]" -H "Accept: application/json" https://service.allthatfit.com/getProd/[Page URL]

response>>
  {
    "_result":true,
    "_message":"",
    "_code":200,
    "_value":{
      "_InFo":"상품 정보(Text)",
      "_Name":"상품 명칭",
      "FABRICS":[
        {
          "_InFo":"원단 정보(Text)",
          "_Mix":"", 
          "_Tiln":"Object에 표현할 타일 갯수", 
          "_Norm":"노멀 파일 URL",
          "_Name":"원단 명칭",
          "_Code":"원단 식별자(Int)",
          "_Mtln":"금속 재질 표현 (Metalness)",
          "_Thmb":"썸네일 URL",
          "_Type1":"타입 명",
          "_Alpha":"알파값",
          "_Type2":"세부 타입 값",
          "_Roug":""
        }
      ],
      "OBJECTS":[
        {
          "fileName":"파일 명칭",
          "fabric":"해당 오브젝트에 설정된 원단 정보",
          "fileUrl":"파일 다운로드 URL",
          "Extn":"파일 확장자"
        }    
      ],
      "Thumbnail":"상품 썸네일",
      "_Type1":"상품 타입1",
      "_Type2":"상품 타입2",
      "ARTWORKS":[]
    }
  }
```

request >>
 - Page URL : 상품 정보를 요청할 URL입니다. 관리페이지에 각 상품별 정의되어 있습니다.
 - Domain : 도메인 주소로 토큰 요청 시 결과값으로 들어있는 도메인 주소를 헤더에 설정하시면 됩니다.
 - Token : 상기 getToken을 통해 받아온 토큰 정보를 기입합니다.
 - Timestamp : 해당 토큰의 발급 시간으로 헤더에 설정해주시면 됩니다.

response >>
 응답값은 다음과 같이 구성되어 있습니다. 
<b>(이 응답값을 json._value로 지칭합니다. )</b>
 - _NAME : 상품의 명칭입니다.
 - _INFO : 서버에 설정된 상품의 설명입니다.
 - FABRICS : 상품에 설정된 원단 리스트 정보입니다.
 - OBJECTS : 상품의 3D 오브젝트에 대한 정보입니다.

### 3D 스크립트 추가
서비스를 운영할 페이지에 다음과 같이 자바스크립트를 Import 합니다.

```markdown
<script type="text/javascript" src="https://service.allthatfit.com/js/lib/LAB.js?v=1.0"></script>
<script type="text/javascript" src="https://service.allthatfit.com/js/Front/Studio3D.js?v=1.0"></script>
```

### 3D 모델 확인

위 스크립트 적용 후 3D Viewer가 나오는 부분을 정의합니다.
```yaml
 <!-- 3D Viewer 설정 -->
 <div id="mfafa-area" style=" height: 500px; margin-bottom: 30px;"></div>

<script>
  // 3D Viewer 초기화
  WebGL_Load();
 
 // json._value : 상품에 대한 정보
 _setupModel(json._value);
</script>
```

상품 정보의 응답값에 해당하는 <b>'json._value'</b> 부분을 모두 _setupModel에 적용하시면 해당 상품에 대한 3D 모델링이 위의 뷰어 화면에 보이게 됩니다.
'_setupModel()'은 위에 Import한 자바스크립트 파일에 정의되어 있습니다.

<img style="width:600px;height:600px;" src='/Qfit/assets/img/1.jpg'>

### 원단 리스트 확인 및 적용

원단 적용 시 다음과 같이 'FabricSetType' 함수를 호출하여 전체 적용할지 한부분만 적용할지 선택할 수 있습니다.

```markdown
<button onclick="FabricSetType(0)"></button>
```
- FabricSetType(0) : 의상 중 클릭한 부분만 적용
- FabricSetType(1) : 의상 전체에 적용

```markdown
<div style=" overflow-y:scroll;">
    <div id="fabricInfoArr" style="display: flex; flex-flow: wrap; "></div>
</div>
    
<script>
let ServerUrl = 'https://service.allthatfit.com';
let fabricInfoHTML = ``;
// 원단 리스트 목록 JSON.FABRICS
for (var i = 0; i < FABRICS.length; i++) {
    fabricInfoHTML += `<div class="item fabricSorting" >
                           <img class="fabricItem" style="width: 50px;height: 50px;" src="${ServerUrl + FABRICS[i]._Thmb}" data-set="${JSON.stringify(FABRICS[i]).replaceAll("\"", "'")}"title="${FABRICS[i]._Name}">
                       </div>`;
}
$("#fabricInfoArr").empty().append(fabricInfoHTML);
$(".fabricItem").on("click", () => {
    OnBtnClickFabric(event);
});
</script>
```
FabricSetType을 설정하신 후 위와 같이 원단 이미지를 설정하실 수 있습니다.
위 내용은 이미지를 클릭하였을 시 해당 이미지가 3D 모델에 적용되도록 한 것으로 img의 'data-set'에 원단에 해당하는 정보가 들어가며 클릭 시 'OnBtnClickFabric(event)'를 호출하도록 하였습니다.

- data-set을 설정 시 <b>"(쌍따옴표)를 '(따옴표)로 변경</b>하여 넣어주시기 바랍니다.
- 3D Object를 클릭 후 해당 원단을 클릭하여 OnBtnClickFabric를 호출하였을 시 FabricSetType 설정여부에 따라 적용 범위가 달라집니다.

<img style="width:600px;height:600px;" src='/Qfit/assets/img/2.png'>

- 3D Object를 클릭 할 시 클릭한 부분의 원단이 노란색으로 바뀝니다.
- 이후 원단 리스트에서 원하는 원단을 클릭하였을 시 노랗게 변경된 부분에 해당 원단이 적용됩니다. (FabricSetType(0)인 경우)
- FabricSetType(1)인 경우 3D Object 클릭여부와 관계 없이 전체적으로 해당 원단이 적용됩니다.
<br><br>

### 아트웍 리스트 확인 및 적용

아트웍 이미지도 원단과 같은 방법으로 적용할 수 있습니다.
의류에 아트웍 이미지를 적용하기 위해 다음과 같이 아트웍 리스트를 불러올 수 있습니다.

```markdown
<div style=" overflow-y:scroll;">
 <div id="artworkInfoArr" style="display: flex; flex-flow: wrap; "></div>
 </div>

<script>
let ArtworkInfoHtml = ``;

// 이미지 경로 
let ArtworkUrl = [
    "/assets/img/TestImg/1.jpg",
    "/assets/img/TestImg/2.jpeg",
    "/assets/img/TestImg/3.png",
];
for (var i = 0; i < ArtworkUrl.length; i++) {
    ArtworkInfoHtml += `<div class="item artworkSorting mr-2" >
                            <img class="artworkItem" style="width: 50px;height: 50px;" src="${ArtworkUrl[i]}" data-set="${ArtworkUrl[i].replaceAll("\"", "'")}" >
                       </div>`;
}
$("#artworkInfoArr").empty().append(ArtworkInfoHtml);
$(".artworkItem").on("click", () => {
    OnBtnClickArtwork(event);
});
</script>
```

- data-set을 설정 시 <b>"(쌍따옴표)를 '(따옴표)로 변경</b>하여 넣어주시기 바랍니다.

<img style="width:310px;height:400px;" src='/Qfit/assets/img/3.png'>
<img style="width:310px;height:400px;" src='/Qfit/assets/img/4.png'>

- 이후 아트웍 리스트에서 원하는 이미지를 클릭하였을 시 노랗게 변경된 부분에 해당 이미지가 적용됩니다.
- 마우스 컨트롤을 통해 이미지를 확대/축소, 회전 하실 수 있습니다.
<br>

### 아트웍 컨트롤

아트웍 이미지를 적용하시면 다음과 같은 방법으로 위치나 크기를 수정하실 수 있습니다.
- 마우스를 이용하시는 경우 드래그 앤 드롭으로 위치를 수정하거나 확대/축소, 회전 아이콘을 클릭하여 수정하실 수 있습니다.
- 별도의 버튼을 두어 편집하고자 하시는 경우 다음과 같이 함수를 호출하여 수정 하실 수 있습니다.

```markdown
<button  onclick="OnBtnMoveArtworks('left')"> </button>
<button  onclick="OnBtnMoveArtworks('scale_plus')"> </button>
```

OnBtnMoveArtworks 함수의 컨트롤 명령어는 다음과 같습니다.
- 'scale_plus' : 아트웍을 확대합니다.
- 'scale_min' : 아트웍을 축소합니다.
- 'rotate_plus' : 오른쪽으로 회전합니다.
- 'rotate_min' : 왼쪽으로 회전합니다.
- 'left' : 아트웍이 왼쪽으로 한칸 이동합니다.
- 'right' : 아트웍이 오른쪽으로 한칸 이동합니다.
- 'up' : 아트웍이 위쪽으로 한칸 이동합니다.
- 'down' : 아트웍이 아래쪽으로 한칸 이동합니다.
- 'del' : 아트웍을 삭제합니다.

