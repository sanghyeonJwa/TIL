#  2024.06.05 TIL

# 1. Session과  Cookie


### 1-1. 왜 필요한가?

HTTP는 서버와 클라이언트 간의 요청과 응답으로 데이터를 주고 받는 형식으로 서버는 클라이언트 요청에 <br> 응답을 하고 나면 그 연결을 끊어버린다. 클라이언트는 다시 서버에 요청하려면 새로 연결하여 응답을 받아야 한다.
<br>

이렇게 되면 연결이 끊어지기 때문에 유지되어야 하는 정보들이 사라지는 문제가 발생한다.<br>
ex) 로그인된 후 로그인 정보, 장바구니에 넣은 데이터 등등..

이를 해결하기 위해서 Session과 Cookie를 사용하게 된다.

### 1-2. Session과 Cookie란?
---
`Session` : 
연결이 끊어진 이후에도 client에 대한 정보를 유지하기 위해 서버에서 데이터를 보관하는 기술 
<br>

`Cookie` : Session과 동일한 목적을 가지지만 클라이언트 쪽에서 데이터를 보관하는 기술
