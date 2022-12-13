# Zoom API를 사용하며 회고

회사에서 제공하는 플랫폼을 만들기 위해서 이번에 Zoom API를 사용하게 되었다. Zoom API를 사용하면서 느꼈던 점은 Zoom API를 사용하기 위해서 전달해야되는 변수가 굉장히 많다는 것이다! 새어보진 않았지만 적어도 30개 이상은 되는 것 같다. 이를 전달하는 것도 꽤 큰 일이라 생각된다. 그래서 쉽게 DTO를 만들 수 없을까 생각을 해보았고 방법을 찾아냈다. JSON 으로 작성된 데이터를 JAVA 클래스로 생성해주는 서비스를 제공하는 서비스를 찾아냈다.

[https://www.site24x7.com/tools/json-to-java.html](https://www.site24x7.com/tools/json-to-java.html)

위 링크를 들어가면 JSON을 JAVA 파일로 변경할 수 있다. 해당 사이트를 알아두기만해도 유용하게 사용할 수 있을 것으로 보인다. 이번에 Zoom 에서 사용하려고 했던 기능은 미팅 생성 기능이다. 단순하게 미팅 생성하는 것을 생각했지만 설정할 수 있는 부분이 너무 많아 이해하는데 어려움을 겪었다. 

미팅을 생성하면서 아래 에러를 만났다. 

`"code":300,"message":"The value that you entered for the schedule_for field is invalid. Enter a valid value and try again.”`

에어를 보면 알 수 있지만 schedule_for 변수에 들어가있던 계정이 미팅을 생성할 수 있는 권한이 없어서 발생한 에러다.

그 다음에도 에러를 만났었는데 alternative_host 변수에 호스트 권한을 줄 수 없는 계정을 등록하여 발생한 에러였다. 위 변수를 사용할 때 권한 여부를 잘 체크해야 될 것 같다.