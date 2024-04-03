---
title: Virtual Fit Viewer 적용하기
author: TesterJ
date: 2024-03-27
category: Set3D
layout: post
mermaid: true
---


### 주의사항

1. 3D 뷰어는 다음과 같은 환경에서 사용 가능합니다.
 - Cafe24를 통해 만들어진 웹 페이지인 경우
 - 직접 개발한 웹페이지인 경우
 
   (이외에 생성된 웹 환경에서 사용할 경우 발생하는 오류 사항에 대해 책임지지 않습니다.)
 

2. 3D 뷰어를 통해 자신의 도메인에 3D 상품을 표현하고자 할 시 다음과 같은 작업이 필요합니다.
 - 도메인 인증
 - 상품 정보(URL) 확인
 - 3D 뷰어 등록 

3. 적용 순서는 다음과 같습니다.
    <img style="width:800px;height:600px;" src='/VirtualFit/assets/img/flow.jpg'>


### 상품 정보 확인

도메인 인증이 완료되면 해당 도메인에 적용할 상품 정보를 확인합니다.
상품 정보는 [VirtualFit 관리자](https://service.allthatfit.com/Admin) 페이지에 로그인 시 상품 리스트 및 상품 URL을 확인할 수 있습니다. 이 때 원하시는 상품의 URL 난수 값을 복사합니다.

<img style="width:800px;height:100px;" src='/VirtualFit/assets/img/viewer_product_list.jpg'>


### 도메인 인증

먼저 3D 뷰어를 적용할 도메인에 대하여 해당 도메인이 사용하시는 관리자의 도메인이 맞는지 확인하는 절차가 필요합니다.
그 절차는 다음과 같습니다.

1. [VirtualFit 관리자](https://service.allthatfit.com/Admin) 페이지에 로그인
    - 관리자 계정이 없으신 경우 회원가입을 먼저 하시기 바랍니다.
   <br>
2. '내정보 - 도메인 관리'에서 도메인 정보 등록
   <img style="width:800px;height:280px;" src='/VirtualFit/assets/img/domain02.jpg'>
    - 이메일 인증 후 해당 서비스를 사용하실 수 있습니다.
    - 사용 중인 서비스에 따라 등록할 수 있는 도메인의 갯수에 차이가 있습니다.
        * 무료 : 도메인 1회 등록 가능
        * 이코노미 : 도메인 3회 등록 가능
        * 프레스티지 : 도메인 무제한 등록 가능
    <br>
3. 도메인 정보를 등록하시면 인증파일이 생성됩니다. 이 인증파일을 도메인의 루트 디렉토리에 적용합니다. <br>
   <img style="width:800px;height:330px;" src='/VirtualFit/assets/img/domain03.jpg'>
    - 사이트가 xxx.com 인 경우
        * xxx.com/abcdef12345ghiht67890.json

이후 3D 뷰어를 적용하였을 때 해당 json 파일이 확인되면 도메인 인증이 완료됩니다.

### 스크립트 및 3D 뷰어 추가
서비스를 운영할 페이지에 다음과 같이 자바스크립트와 3D 뷰어를 추가 합니다.

```markdown
<!-- 3D Viewer -->
<div id="mfafa-area" style=" height: 500px; margin-bottom: 30px;" 
	data-set="[상품 URL 난수]" 
	data-bgcolor="#F5F5F5" 
	data-bgimg="[Image URL]"
	data-light="0.22" 
	data-bglight="0.29">
</div>

<script type="text/javascript" src="https://service.allthatfit.com/js/lib/LAB.js?v=1.0"></script>
<script type="text/javascript" src="https://service.allthatfit.com/js/Front/StudioSet.js?v=1.0"></script>

<!-- JQuery 추가 (Cafe24 사이트인 경우 생략) -->
<script type="text/javascript" src="[JQuery URL]"></script>
```

 * 해당 서비스를 사용하기 위해선 JQuery가 필요합니다. (Cafe24를 통한 사이트인 경우 JQuery가 사이트에 적용되어 있어 생략하셔도 됩니다.)
 * data-set : 상품의 URL 난수 값입니다. (관리 페이지 상품 리스트에서 확인하실 수 있습니다.)
 * data-bgcolor : 상품이 표현될 시 배경색입니다. (생략하였을 시 기본 배경이 적용됩니다.)
 * data-bgimg :  상품이 표현될 시 배경 이미지입니다. (생략하였을 시 기본 배경이 적용됩니다.)
 * data-light :  상품의 정면 조명 값입니다. 0~1까지 설정하실 수 있으며 값이 클 수록 강한 조명으로 비추게 됩니다. (생략하였을 시 기본 수치 값이 적용됩니다.)
 * data-bglight : 상품의 전체 조명 값입니다. 0~1까지 설정하실 수 있으며 값이 클 수록 전체적으로 밝게 보입니다. (생략하였을 시 기본 수치 값이 적용됩니다.)
 
 