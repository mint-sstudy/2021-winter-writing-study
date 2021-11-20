### 케이디 님의 유니티 입문 강좌를 듣고 작성한다.
유니티는 C#을 사용하기 때문에 소스 코드는 모두 C#으로 작성되었다.

## part1 게임 오브젝트

### 기본 제어 방법
![](https://images.velog.io/images/kkily55/post/d52ada97-b285-424f-b1a6-30969226ef54/image.png)
- 마우스 휠을 누르고 움직이면 hand tool을 누르고 사용하는 것과 똑같은 효과
- 휠을 올렸다 내렸다하면 확대, 축소
- 마우스 우클릭하고 움직이면 시점이 회전 (키보드 방향키로도 이동 가능)
- Hierarchy에서 객체들을 클릭하면 화면의 정중앙에 옴
- Move tab(십자가 툴)은 객체를 움직일 수 있음
- Rotate tab(회전 툴)은 객체 회전 
- Scale tab은 크기 조절
- Rect tab은 카메라의 시야 조절 가능(네모모양이 나오는데 그걸로)/ 다른 것도 조절할 수 있는지는 봐야할듯
- 마지막 tab은 종합적으로 관리 가능

+) Inspector 설명
Transform component는 기본 컴포넌트로 삭제 불가

### Object 설명
- 컴포넌트에 따라 기능이 달라진다.
ex) 카메라 컴포넌트를 삭제하면 Main Camera는 카메라 기능을 하지 못함.
![](https://images.velog.io/images/kkily55/post/fa051c77-64f7-48bb-877a-53e5c5cd2297/image.png)

- 이름 옆에 있는 체크박스를 해제하면 비활성화(안보임), 체크하면 활성화(보임)
- 태그는 이름표와 같은 역할, ex) tag가 Cube인 애의 position을 옮겨라.
- Layer는 카메라와 관련하여 쓰임
ex. cube의 layer을 water로 하고 Main Camera의 object의 인스펙터 창에서 Culling mask에서 water를 해제하면 카메라에 cube가 안보임
- static
	-  lightmap은 주변의 빛을 반사해서 주변을 자신의 색으로 물들임
    - Occluder은 카메라에 보이지 않는 것들을 다 안나타나게 함
    - Batching은 CPU란 것을 램에 상주시킴
    - Navigation은 길찾기
    - Occludee는 Occluder과 유사함
    - Off Mesh Link는 Navigation의 성능 보안 버전
    - Reflection Probe는 빛을 반사 , 자신이 반사되어서 상대방이 보임
    
## part 2 트랜스폼

### Transform
![](https://images.velog.io/images/kkily55/post/19446b34-e86b-45b9-820f-5bc51301b7cf/image.png)
위에서 부터 각각 위치 값, 회전 값, 크기 값을 결정해줌
<img src="https://images.velog.io/images/kkily55/post/c464b87c-c518-4516-b6f5-3cfe3de5c1f9/image.png">
유니티 좌표계, z축이 파란색 (앞뒤), x축이 빨간색(좌우), y축이 초록색 (상하)

### Scripts
- 파일 이름 대문자로 시작, 띄어쓰기 없음
![](https://images.velog.io/images/kkily55/post/444b1b0e-f8dd-449a-89c9-5617156ce364/image.png)
MonoBehavior : Start(초기상태), Update(매 프레임마다 실행) 등 유니티에서 자체적으로 제공하는 함수들을 담은 클래스
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Test : MonoBehaviour
{
    Vector3 rotation;
    [SerializeField] // inspector상에 Go_Camera 필드가 생겨 거기에 Main_Camera를 넣어서 go_camera가 private함에도 불구하고 쓰게 해줌
    private GameObject go_camera;

    void Start(){
        rotation=this.transform.eulerAngles;
    }

    // Update is called once per frame
    void Update()
    {
        if(Input.GetKey(KeyCode.W)){ // W키를 누르면

            //1초에 1씩 Z축으로 움직임
            this.transform.position=this.transform.position+new Vector3(0,0,1);
            //Time.deltaTime은 한프레임 실행하는데 걸리는 시간, 약 60분의 1        
  	        //this.transform.Translate(new Vector3(0,0,1)*Time.deltaTime); 과 같음, 이게 더 편리

            //x축으로 1초에 한번씩 회전
            rotation=rotation+new Vector3(90,0,0)*Time.deltaTime;
            this.transform.eulerAngles=rotation;
            //this.transform.Rotate(new Vector3(90,0,0)*Time.deltaTime); 과 같음, 이게 더 편리
            /*
            rotation=rotation+new Vector3(90,0,0)*Time.deltaTime;
            this.transform.totation=Quaternion.Euler(rotation);
            과 같음
            */

            //1초에 2배씩 크기 커짐
            this.transform.localScale=this.transform.localScale+new Vector3(2,2,2)*Time.deltaTime;

            //카메라를 바라보게 함
            this.transform.LookAt(go_camera.transform.position);

            //큐브가 카메라 주위를 빙빙 돎
            transform.RotateAround(go_camera.transform.position,Vector3.up,100*Time.deltaTime);

        }
    }
}

```

moveSpeed*this.transform.right * Time.deltaTime
//움직이는 속도 * 정규화 벡터(방향만을 알려줌, right인 경우 newVector3(1,0,0) ,up인경우 newVector3(0,1,0),forward인 경우 newVector3(0,0,1))

## part3 리지드바디

Rigidbody: 물리효과를 구현해줌
***

Use Gravity: 중력효과
Mass: 질량, 큰 쪽이 작은 쪽을 밀어버림
Drag: 저항, 0일때는 빨리 떨어지고 10일 때는 천천히 떨어짐
Angular Drag: 회전값에 대한 저항, 빠르게 움직높으면 빠르게 회적이 멈춤
Is Kinematic: 물리효과를 없앰 , 체크하면 중력 등 적용 x
Interpolate: 이전 프레임의 움직임을 통해 다음 프레임을 만들면서 움직임을 자연스럽게 만듦
Extrapolate: 다음 프레임의 움직임을 예측해서 움직임을 자연스럽게 만듦
Collision Detection: 충돌 탐지, Continuous는 매우 빠른 애(예: 총알)에게 적용하면 좋음
Constraints: Position과 Rotation 고정

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Test : MonoBehaviour
{
   
    //[SerializeField] //inspector상의 myRigid를 채워넣을 수 있게 해줌
    private Rigidbody myRigid;
    private Vector3 rotation;

    void Start(){
        myRigid=GetComponent<Rigidbody>();
        rotation=this.transform.eulerAngles;
    }

    // Update is called once per frame
    void Update()
    {
        if(Input.GetKey(KeyCode.W)){ // W키를 누르면

            //z축 방향으로 속도가 1로 바뀌어서 앞으로 나가게 됨
            myRigid.velocity = new Vector3(0,0,1);

            //회전속도 조절
            myRigid.angularVelocity = -Vector3.right;

            //질량 조절
            myRigid.mass=2f;

            //저항 조절
            myRigid.drag=2f;

            //회전 저항 조절
            myRigid.angularDrag=2f;

            //회전을 최대속도로 변경시킴
            myRigid.maxAngularVelocity=100;
            myRigid.angularVelocity = Vector3.right*100;

            myRigid.isKinematic=true;

            myRigid.useGravity=true;

            //일정 방향으로 이동시킴,w키를 떼면 멈춤, 관성과 질량의 영향 받지x
            myRigid.MovePosition(transform.forward); 


            //1초에 90도씩 회전 ,w키를 떼면 멈춤, 관성과 질량의 영향 받지x
            rotation+=new Vector3(90,0,0)*Time.deltaTime;
            myRigid.MoveRotation(Quaternion.Euler(rotation));

            //z축 방향으로 1의 세기만큼 힘을 가해서 움직임, 관성과 질량의 영향을 받아서 w키를 떼도 바로 멈추지않고 약간 더 감
            myRigid.AddForce(Vector3.forward);

            //y축 방향으로 돎, 관성과 질량의 영향을 받아서 w키를 떼도 바로 멈추지않음
            myRigid.AddTorque(Vector3.up);

            //폭발 구현할 때 좋음
            myRigid.AddExplosionForce(10,this.transform.right,10); //폭발 세기, 위치, 반경    

            //add시리즈는 물리효과 적용, move 시리즈는 강제적으로 이동시켜 물리효과 미적용        

        }
    }
}

```

