개발을 편하게 해주는 도구가 있다면, 물론 필수는 아니지만 생산성을 고려했을때 꼭 사용하시기를 권장드립니다.

#### 몇가지 유용한 개발 도구들

_eslint, prettier 모두 vscode marketplace에서 설치해줍니다._

**eslint는 자바스크립트 문법을 검사해줍니다.**

유효하지 않은 문법이나 꼭 error가 아니더라도 warning을 띄워주게 할 수 있습니다.

그 예로는 변수를 선언하고 사용하지 않을 경우 warning을 띄워주는 것이 있습니다.

무엇보다 eslint의 큰 장점은 바벨이 트랜스파일링을 하기전에, browser runtime 이전에 editor상에서 warning 혹은 error를 잡아준다는 것입니다.

**prettier는 코드를 이쁘게 정리해줍니다. **

vscode설정에서 \(Code =&gt; 기본설정 =&gt; 설정\) format =&gt; format on save에 체크를 해주면 저장할때마다 코드를 정리해줍니다.

\(기본으로 체크되어있는 경우도있습니다\)

또한 format대신 prettier를 입력하시면 vscode prettier에서 제공해주는 다양한 설정들을 적용하실 수 있습니다.

> 사실 eslint나 prettier는 vscode를 사용하지않고 npm install을 통해 설치하고 .eslintrc, .prettierrc등을 통해서도 적용할 수 있어요.
>
> 하지만 vscode가 이런 기능들을 손쉽게 적용할 수 있게 해주기 때문에 더욱 편리하게 사용할 수 있습니다.

**Profiler**

리액트 profiler는 performance탭의 record와 비슷한 기능을 제공합니다.

다만 profiler는 개발자도구 v4에 추가된지 얼마되지 않아서 버그관련 이슈가 많습니다.

profiler 개발자도 작년 10월에 bug issue를 받고 고치겠다고 했는데 현재 시점으로 아직 아무런 변화가 없습니다.

따라서 profiler를 사용할 일이 있다면 현재 시점에서는 performance를 사용하시는 것을 권장드립니다.

다만 profiler에서는 highlight updates라는 기능을 제공해주는데요, 이는 수업시간에 보여드렸듯이 서로 영향받는 컴포넌트들끼리 반짝이게 해줌으로써 나중에 프로젝트 규모가 커졌을때 컴포넌트간 관계를 파악할 때 유용하게 쓰일 수 있습니다.

참고로 highlight updates는 profiler가 나오기 전에는 독립적인 영역에 따로 존재했었는데, 개발자도구가 업데이트 되면서 개발자도구에서 잠시 사라졌었습니다.

그런데 원래 highlight updates를 사용하던 많은 개발자들이 github에 issue를 하자 몇달 전에 다시 생겨났습니다.

그만큼 유용하다는 기능이겠죠? 나중에 실제 프로젝트를 할 때 꼭 사용해보세요.

