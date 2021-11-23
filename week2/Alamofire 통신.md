## [ 1 ] 강의 영상
- API 통신을 위한 UI작업 : https://youtu.be/pBG66IYtKlk
- Alamofire 이용하기 : https://youtu.be/WR4_KenX0G8

<br/>

## [ 2 ] 스터디 내용
### (1) Alamofire란?

서버 통신을 도와주는 Swift 기반 HTTP 네트워킹 라이브러리이다. 

<br/>

### (2) Alamofire 설치 방법

아래의 명령어를 통해 Podfile을 vi 편집기로 편집한다.
```
sudo vi Podfile
```

Podfile에 Alamofire를 설치하기 위해 다음과 같은 문장을 추가한다. vi 편집기에서 편집을 진행하기 위해서는 <code>i</code> 를 눌러 'INSERT'모드로 변경하여 입력해야 한다. 편집이 완료된 후에는 <code>esc</code> 버튼을 통해 명령모드로 전환하여 <code>:wq!</code> 로 변경 내용을 저장하고 편집기를 종료한다.
```
pod 'Alamofire', '~> 5.4'
```
![image](https://user-images.githubusercontent.com/61380136/141781517-9fe3c679-756b-4636-928b-8ab4cf7cca6b.png)

이후 터미널에서 <code>pod install</code> 를 수행하여 Podfile에 있는 내용을 기반으로 Alamofire 라이브러리를 설치한다.

![image](https://user-images.githubusercontent.com/61380136/141783160-c29bfcbe-891b-47ef-a441-9620b778a25e.png)

정상적으로 라이브러리가 설치될 경우 위와 같이 'Installing Alamofire (5.4.4)'가 뜨는 것을 확인할 수 있다. 현재 프로젝트가 열려있다면 닫아주고 해당 프로젝트가 있는 위치에서 <code>open 해당 프로젝트 이름.xcworkspace</code>를 통해 프로젝트를 다시 열어준다.

<br/>

### (3) Unsplash에서 제공하는 api 사용하기

1. [Unsplash](https://unsplash.com/developer) 에 회원 가입하고 'Your Apps'에서 'New Application'을 선택한다. 
2. application 정보를 입력한다.

![image](https://user-images.githubusercontent.com/61380136/141786621-9aa0eaeb-c867-4408-8ce8-8a1d7c723b79.png)

3. api의 Access Key를 확인해 놓는다. (client_id로 사용)
4. Unsplash에서 제공하는 https://api.unsplash.com/search/photos 를 기반으로 'Endpoint'에 따라 서버에서 자원에 접근 가능하다.



* API(Application Programming Interface, 응용 프로그래밍 인터페이스)란?

```
API는 응용 프로그램에서 사용할 수 있도록, 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻한다. 
주로 파일 제어, 창 제어, 화상 처리, 문자 제어 등을 위한 인터페이스를 제공한다.
```

* Endpoint란?

```
Endpoint는 클라이언트 응용 프로그램에서 서비스에 액세스할 수 있는 URL이다.
```

<br/>

### (4) AlamoFire 사용 방법

#### 1. 주요 Http 요청 메소드
1) GET : 특정 리소스의 표시를 요청한다. 서버의 데이터를 수정하지는 않는다.
2) POST : 서버에 데이터를 보낸다.
3) PUT : 특정 위치에 데이터를 보내기 위해 사용한다. 
4) DELETE : 특정 위치의 데이터를 삭제하기 위해 사용한다.

#### 2. Alamofire의 주요 함수
1) AF.upload: 스트림, 파일 메소드 등을 통해 파일을 업로드한다.
2) AF.download: 파일을 다운로드 하거나, 이미 진행중인 다운로드를 재개한다.
3) AF.request: 파일 전송과 관련없는 기타 HTTP 요청을 한다.

#### 3. 사용 방법
1) 통신할 URL에 request를 보낸다.
2) response를 받는다.
3) json data로 받아 필요에 따라 사용한다.

#### 4. 코드로의 사용

```
let url = API.BASE_URL + "search/photos"
guard let userInput = self.searchBar.text else { return }
let queryParam = ["query" : userInput, "client_id" : API.CLIENT_ID]
        
AF.request(url, method: .get, parameters: queryParam).responseJSON(completionHandler: { response in
            debugPrint(response)
})
```

<br/>

## [ 3] Trouble Shooting 💥
- pod install 오류가 생기는 문제 (mac M1일 경우)
![image](https://user-images.githubusercontent.com/61380136/141780617-97802fde-4d49-4762-8a15-217f6f80d8fd.png)
```
sudo arch -x86_64 gem install ffi
arch -x86_64 pod install
```
=> pod install 대신에 <code>arch -x86_64 pod install</code>을 수행하면 된다.

<br/>

## [ 4] 참고 자료
- https://github.com/Alamofire/Alamofire
- https://github.com/CocoaPods/CocoaPods/issues/10518#issuecomment-798912624
- https://ko.wikipedia.org/wiki/API
