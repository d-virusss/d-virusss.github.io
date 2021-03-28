---
layout: post
title: Bootstrap4 datetimepicker 적용, moment를 사용한 시간정보 전달, Time vs Datetime
subtitle: 여러분은 삽질하지 마세요!
# cover-img: /assets/img/2020.03/dev_cover.png$opacity_0.5
thumbnail-img: /assets/img/2020.03/datetimepicker.png
tags: [rails datetimepicker moment]
comments: true
---


### 개요
프로젝트에서 구현하는 폼 중 datetime으로 표현해야하는 column이 있었습니다. 사용자한테 시간정보를 입력받아야 했는데, HTML5에서 지원하는 기본 datetimepicker는 너무 구린 거 같아 Bootstrap을 dependency로 한 쓸만한 datetimepicker를 찾아봤습니다.

1. jQuery date_timepicker  
UI 구려서 OUT

2. Bootstrap 3 Date/Time Picker  
deprecated 되어 PASS

3. Tempus Dominus  
git star도 6.8K에 UI도 이쁜데 뭘 잘못했는지 적용이 잘 안됐습니다. 그래서 다른 걸 찾아보기로..

4. Bootstrap 4를 지원하는 무명의 Date/Time picker  
UI도 3번과 크게 다르지 않고, option들도 기존 Bootstrap3 위젯과 동일하게 구현되었다고 해서 이걸로 선택  
[데모는 여기서](https://www.jqueryscript.net/demo/Date-Time-Picker-Bootstrap-4/)

### 적용 과정
1) 소스파일 다운  
이 라이브러리는 cdn이 없어 직접 js, scss를 받아서 폴더에 넣어줘야 합니다.  
[docs 및 다운로드](https://www.jqueryscript.net/time-clock/Date-Time-Picker-Bootstrap-4.html) <br>

<img width="800" alt="js" src="https://user-images.githubusercontent.com/55905801/112276846-82bf8400-8cc4-11eb-856d-f044ca03e7c8.png">  

<hr>
<img width="800" alt="scss two pieces" src="https://user-images.githubusercontent.com/55905801/112277773-6e2fbb80-8cc5-11eb-8be1-89d8bb0ad6e3.png">
<hr>
bootstrap-datetimepicker.js JS 파일 한개와 
_bootstrap-datetimepicker.scss / bootstrap-datetimepicker-build.scss 파일 2개를 각각 옮겨주면 됩니다.  

<img width="400" alt="ss 63" src="https://user-images.githubusercontent.com/55905801/112278120-d9798d80-8cc5-11eb-9ba6-feaae5133ffa.png">

2) application.html.erb에 script / stylesheet 추가  
layout 템플릿 파일로서의 역할을 하는 application.html.erb 에 script와 스타일시트를 순서에 맞게 삽입하는 게 가장 중요합니다. 여러 의존성이 얽혀있기 때문에 순서에 맞게 넣어주지 않으면 오류가 생깁니다.  
해당 라이브러리의 docs를 살펴보면(docs라기엔 좀 무성의하지만) 의존성이 설명되어 있는데,  
(1) 부트스트랩  
(2) jQuery  
(3) popper.js  
(4) font-awesome  
(5) moment  
입니다.  

보통은  
```
<%= javascript_include_tag 'web' %>
```
혹은
```
<%= javascript_include_tag 'application' %>
```
을 통해서 필요한 web.js / application.js 를 로드하는데 이 파일에서 gem으로 설치된 모듈들이
```
//=require (module_name)  
```  

이런 방식으로 불려 사용되고 있습니다.
그 외 cdn으로 가져온 라이브러리들은
```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.14.0/css/all.min.css" integrity="sha512-1PKOgIY59xJ8Co8+NE6FZ+LOAZKjy+KY8iq0G4B3CyeY6wYHN3yt9PW0XpSriVlkMXe40PTKnXrLnZ9+fkDaog==" crossorigin="anonymous" />
```  

이런 방식으로 script들이 삽입되어 있을겁니다.
jQuery를 중복해서 삽입하거나 하지 않게 순서를 잘 맞춰주세요.

저 같은 경우엔 순서가  
(1) fontawesome by CDN  
(2) <%= javascript_include_tag 'web' %> 을 통해서 script 삽입  

```
//= require jquery  
//= require cocoon  
//= require popper  
//= require bootstrap  
//= require moment  
//= require moment/ko  
```
이런 식으로 되어 있습니다.  

js 파일을 삽입한 후에 stylesheet만 삽입해주면 사용할 준비는 끝납니다.  
   ```
    <%= stylesheet_link_tag 'bootstrap-datetimepicker-build' %>
   ```  

_bootstrap-datetimepicker.scss 파일은 build로 끝나는 scss 에서 @import로 불러와진 후 precompile 되기 때문에 application.html.erb에서 불러주지 않아도 괜찮습니다.

여기서 주의할 점이 프로젝트에 기본으로 jquery 혹은 bootstrap datetimepicker 모듈이 포함되어 있을 수 있습니다. 이런 경우 우리가 이제 적용할 Date / Time Picker 컴포넌트의 스타일이 깨지거나 이상하게 동작할 수 있으므로 다 지워줍니다.

<img width="748" alt="ss 64" src="https://user-images.githubusercontent.com/55905801/112280856-c74d1e80-8cc8-11eb-8f03-aa4011122aec.png">

이런식으로 그냥 date picker든 date_time_picker든 전부 지워줍니다. 어차피 옵션을 통해 time_picker로만, date_picker로만 사용할 수 있습니다.

이렇게 js와 stylesheet를 의존성 순서에 맞게 로드해주면 됩니다.

3) 컴포넌트 적용하기  
   
적용은 간단합니다.  

   ```
   <input type="text" class="form-control" id="example">

   <script>
   $(function () {
     $('#example').datetimepicker();
   });
   </script>
   ```  

![ss 65](https://user-images.githubusercontent.com/55905801/112281402-5b1eea80-8cc9-11eb-96ee-f882eff43d58.png)  


이렇게 하면 적용됩니다.

제가 진행하는 프로젝트는 한국인 대상이라 날짜 포맷을 한국어로 맞춰줬습니다. 우리가 사용하는 Date / Time Picker 컴포넌트의 format 형식은 moment.js의 포맷형식을 따르는데 moment의 locale의 기본값이 'en'이기 때문에 (스마트하게 지역마다 알아서 바꿔주는 줄 알고 삽질..) 혹시 한국어로 시간을 표시하고자 하면 locale을 'ko'로 변경해줘야 합니다.  


locale을 'ko'로 변경하는 방법은
   ```
   //= require moment
   //= require moment/ko
   ```
web.js 혹은 application.js 에서 위와 같이 불러주시면 됩니다.

moment.js 관련된 포맷은 여기서 확인하시면 됩니다.  
[moment.js 공식문서](https://momentjs.com/)  

처음 docs 들어가면 영어포맷만 보이지만 페이지 밑으로 조금 내리셔서 Korean 태그 클릭해주시면 한국말 format 어떻게 변경하는지 확인할 수 있습니다.

제 코드는 다음과 같은데  

```
$(function (){
 $(".delivery-datetime").datetimepicker({
   format: "YYYY-MM-DD dddd a hh:mm",
   locale: moment.locale(),
   icons: {
     time: 'fas fa-clock',
   }
 });
});
```  
font-awesome이 순서에 맞게 들어가 있음에도 시간 아이콘이 표시되지 않아 이런 방식으로 커스텀 했습니다.
   
4) Date / Time picker 컴포넌트로 선택한 시간 정보로 ActiveRecord 객체의 datetime 필드 채우기  
   <br>
    이제 사용자가 입력한 이 시간값을 컨트롤러에서 적절히 받아서 datetime 타입의 column에 잘 저장하기만 하면 됩니다. 저는 프론트 단에서 data를 적절히 조작한다음에 백 단에서는 간단한 로직으로 객체를 생성하는 방법을 선호하기 때문에 사용자가 선택한 시간을 unixtime으로 바꾼 다음 컨트롤러로 보내주는 방법을 생각해봤습니다.  

그리고 HTML 5의 기본 date / time picker를 사용하지 않기로 한 이상 f.datefield를 사용하거나 input태그의 type="datetime-local"을 사용하면 안됩니다.
HTML 5의 Date / Time picker와 우리가 힘들게 적용한 두 컴포넌트가 동시에 사용되어 버립니다.  

그래서 저는 다음과 같은 방법을 사용했습니다.  

### Date / Time picker를 표시할 input태그와 실제로 서버로 params를 보낼 hidden_field를 분리한 다음에 input태그에 이벤트를 걸어두고 값이 변하면 hidden_field에 unixtime으로 바꾼 값을 넣어준다!  
<br>

(1) 프론트 부분 코드 및 설명
   ```
   <input type="text" class="form-control delivery-datetime" data-target="truck_start_at">
   ```
이렇게,

실제 요청을 통해 값이 전달되는 부분은
   ```
   <%= f.hidden_field :start_at %>
   ```

이렇게 하였습니다.

Event Delegation 방식을 통해 이벤트를 효과적으로 달아줬고, delivery-datetime selector에서 포커스가 사라지게 되면 그 값을 unix_time으로 바꿔 hidden_field의 value로 넣어주는 코드입니다.

```  
$(document).on('blur', '.delivery-datetime', (e) => {
let target_selector = e.target.dataset.target;
let locale_date = e.target.value;
let unix_time = (moment(locale_date, "YYYY-MM-DD dddd a hh:mm").valueOf() / 1000);
$(`#${target_selector}`).val(unix_time);
});
```  

change 이벤트는 어떤 이유에선지 먹통이였습니다.(분명 개발자 도구로 찍어보면 val은 변하는데 이벤트가 실행이 안됨..) propertychange change keyup paste input blur 이벤트를 시도해봤고, 결국 blur 이벤트를 통해 구현했습니다.

input 태그에 data attribute로 연결시킬 hidden_field의 id값을 넣어뒀습니다. 이 attribute를 사용하여 발생된 이벤트에서 unix time을 전부 계산한다음 값을 갱신해줬습니다.  

moment("_사용자가 입력한 값_", "_해석할 포맷_").valueOf() 메소드를 사용하면 화면에 표시된 값을 unix time 으로 변경할 수가 있습니다. moment에서는 unixtime을 ms로 표시하는데 우리가 필요한건 초 단위까지이므로 1000으로 나눠줬습니다.  

(2) 컨트롤러 부분  

컨트롤러 부분에서는 Time.at(unixtime)을 통해 생성한 값을 column에 넣어주면 됩니다.  
   ```
   @truck.start_at = Time.at(params[:truck][:start_at].to_i)
   @truck.end_at = Time.at(params[:truck][:end_at].to_i)
   @truck.save
   ```  

요런 식으로 넣어주면 되는데, Time.at의 매개변수는 반드시 정수여야 하니 .to_i 메소드로 정수로 변환해주세요.

추가로, 기능을 구현하다가 Date와 Time, DateTime의 차이점이 뭔지 궁금해서 검색해봤습니다.
[이 문서](https://classmethod.dev/ko/questions/3928275)를 참조하면 되는데 요약하자면,  

Ruby에서 사용하는 Time은 POSIX 표준 time_t 값을 감싸는 래퍼이고  
Datetime은 SQL 표준 datetime필드를 감싸는 래퍼이다.  

:timestamp 및 :datetime 필드는 기본적으로 DateTime으로 저장되지만  
:timestamp는 4바이트(1970~2038년), :datetime은 8바이트(1000~9999년 표현가능)를 사용한다.  

> DB의 column 타입을 결정할 때

db 크기를 걱정해야 한다면 :timestamp  
날짜만 저장해도 된다면 :date  
시간만 저장해도 된다면 :time  

> Ruby에서 시간 객체를 다룰 때  

Datetime -> 윤초 고려안함(os 따라 다름), 섬머타임 고려 안함, 시간대를 UTC + offset 으로 간단히 표현  
Time -> 윤초 고려함, 섬머타임 고려함  

저처럼 unix time을 사용하거나 현재와 멀리 떨어지지 않은 시간을 다룰 거면 Time 객체를, 그 외에는 Datetime을 쓰는 게 좋을 거 같습니다.


### 마치며

date_time picker가 별 거 아닌 거 같은데 적용할 때마다 사소한 부분들 때문에 삽질하게 되서 저도 참고하고 여러분도 보시라고 글로 남겨둡니다. 혹시 프로젝트에 date / time picker 쓰시게 되는 분들은 참고하시면 좋을 거 같습니다.