## 애니메이션
![](https://images.velog.io/images/kkily55/post/5fbcd4c2-7615-4041-8deb-214ae8d834e7/image.png)

녹화버튼 누르면 움직임 녹화
한 간격당 1분
samples는 1초에 몇프레임을 실행하냐

![](https://images.velog.io/images/kkily55/post/5dbedf3a-f559-4977-a6a4-0d2e749a8f92/image.png)

- element: 애니메이션을 하는 애
- play automatically: play 시작하자마자 큐브 1에있는 애니메이션을 재생
- Amimate Physics: 애니메이션에도 물리를 넣겠다
- Culling Type: 카메라의 시야를 벗어나면 안보임 

![](https://images.velog.io/images/kkily55/post/512ee273-0189-49b7-b766-0d340faee80e/image.png)

- Loop은 왔다갔다 계속 진행
- Pingpong은 마지막 프레임이 되면 마지막 프레임이 첫 프레임이 됨
- clamp forever은 마지막 프레임 멈춘상태로 가만히 있음

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Text_chap8 : MonoBehaviour
{
    private Animation anim;
    private AnimationClip clip;

    // Start is called before the first frame update
    void Start()
    {
        anim=GetComponent<Animation>();
    }

    // Update is called once per frame
    void Update()
    {
        if(Input.GetKeyDown(KeyCode.W)){ //w키 누르면 
            anim.Play("Cube_2"); //cube2라는 애니메이션 실행
            anim.PlayQueued("Cube_2");// 끝나면 cube2 실행
            anim.Blend("Cube_2"); // 애니메이션 cube1과 2가 섞임
            anim.crossFade("Cube_2"); //실행하던 것이 자연스럽게 사라지고 2가 실행
            if(!anim.IsPlaying("Cube_2"))
                anim.Play("Cube_2");
            anim.Stop(); //정지
            anim.wrapMode=WrapMode.Loop; //랩모드 변경
            anim.clip=clip;
            anim.animatePhysics=false;


        }
        
    }
}

```



## 애니메이터
![](https://images.velog.io/images/kkily55/post/d70fae79-19df-4f90-bb2f-0100adb3a1bd/image.png)

sphere를 이용해 cube의 움직이는 방향을 보여줌 (사진은 큐브가 점프하는는 상태)

Animation들을 다 만든 후에 Animator 탭을 열어줌

![](https://images.velog.io/images/kkily55/post/80bbc2e8-4e92-4851-933d-dc18944ae00b/image.png)

디폴트 상태로 원하는 것을 우클릭해서 default로 만듦

![](https://images.velog.io/images/kkily55/post/aacb2e2d-924f-4715-8c71-65070f473f34/image.png)

- Exit time은 애니메이션이 얼만큼 끝나고 난 뒤에 상태 전이 할지(0=Has Exit Time 체크해제)
- Transition Duration은 두개의 효과간에 얼마나 자연스럽게 연출하는지(상태 전이하는에 얼마나 걸리게 할지)
- Transition Offset은 늘리면 그만큼 뒤에서 애니메이션이 실행됨
- Interruption source는 언제 중단 시킬 것인지

![](https://images.velog.io/images/kkily55/post/9e0120e9-0f82-402c-b8dd-5c6b43c93c08/image.png)

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Test_chap9 : MonoBehaviour
{

    private Animator anim;
    [SerializeField] private float moveSpeed;

    // Start is called before the first frame update
    void Start()
    {
        anim=GetComponent<Animator>();

    }

    // Update is called once per frame
    void Update()
    {
        float _dirX=Input.GetAxisRaw("Horizintal"); 
        //A,D키 혹은 방향키(오,왼)을 뜻함 오른쪽 키가 눌리면 1이 리턴되고 왼쪽키가 눌리면 -1이 리턴 안눌리면 0리턴
        float _dirZ=Input.GetAxisRaw("Vertical"); 
        //w,s키 혹은 방향키(위,아래)을 뜻함 위키가 눌리면 1 아래키 눌리면 -1 안눌리면 0리턴

        Vector3 direction = new Vector3(_dirX,0,_dirZ);
        anim.SetBool("Right",false);
        anim.SetBool("Left",false);
        anim.SetBool("Up",false);
        anim.SetBool("Down",false);
   
        if(direction!=Vector3.zero){
            this.transform.Translate(direction.normalized *moveSpeed * Time.deltaTime);
            //대각선 움직임도 수평 수직 움직임과 똑같이 만들기 위해 normalized를 함. 하지 않으면 대각선 속도만 유독 빨라짐.

            if(direction.x>0){
                anim.SetBool("Right",true);
            }
            else if(direction.x<0){
                anim.SetBool("Left",true);
            }
            else if(direction.z>0){
                anim.SetBool("Up",true);
            }
            else if(direction.z<0){
                anim.SetBool("Down",true);
            }


        }
    }
}

```

위는 복잡함 -> 간단하게 구현

![](https://images.velog.io/images/kkily55/post/34ed4820-282e-4171-ac14-b12491c9c5dd/image.png)

Any State는 어떤 상태에서든 스페이스바를 누르면 점프하게 만들기 위해 연결한 것

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Test_chap9 : MonoBehaviour
{

    private Animator anim;
    [SerializeField] private float moveSpeed;
    private bool isMove;

    // Start is called before the first frame update
    void Start()
    {
        anim=GetComponent<Animator>();

    }

    // Update is called once per frame
    void Update()
    {
        float _dirX=Input.GetAxisRaw("Horizintal"); 
        //A,D키 혹은 방향키(오,왼)을 뜻함 오른쪽 키가 눌리면 1이 리턴되고 왼쪽키가 눌리면 -1이 리턴 안눌리면 0리턴
        float _dirZ=Input.GetAxisRaw("Vertical"); 
        //w,s키 혹은 방향키(위,아래)을 뜻함 위키가 눌리면 1 아래키 눌리면 -1 안눌리면 0리턴

        Vector3 direction = new Vector3(_dirX,0,_dirZ);
        isMove=false;
        
        if(direction!=Vector3.zero){

             isMove=true;
            this.transform.Translate(direction.normalized *moveSpeed * Time.deltaTime);
            //대각선 움직임도 수평 수직 움직임과 똑같이 만들기 위해 normalized를 함. 하지 않으면 대각선 속도만 유독 빨라짐.
        }
        anim.SetBool("Move",isMove);
        anim.SetFloat("DirX",direction.x);
        anim.SetFloat("DirZ",direction.z);
    }
}

```

Parameter 만들 때 Trigger는 실행시키는 것
substatemachine(여기선 Jump)은 state를 넣을 수 있는 폴더같은 것, 보기에 깔끔해보이려고 씀
![](https://images.velog.io/images/kkily55/post/24a02f31-3c8e-4dbd-8790-808cd2b510a7/image.png)

점프까지 한 완성 코드
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Test_chap9 : MonoBehaviour
{

    private Animator anim;
    private Rigidbody rigid;
    private BoxCollider col;

    [SerializeField] private float moveSpeed;
    [SerializeField] private float jumpForce; //점프 스피드
    [SerializeField] private LayerMask layerMask;

    private bool isMove;
    private bool isJump;
    private bool isFall;

    // Start is called before the first frame update
    void Start()
    {
        anim=GetComponent<Animator>();
        rigid=GetComponent<Rigidbody>();
        col=GetComponent<BoxCollider>();

    }

    // Update is called once per frame
    void Update()
    {
        TryWalk();
        TryJump();
    }

    private void TryJump(){

        if(isJump){
            if(rigid.velocity.y<=-0.1f&&!isFall){
                isFall=true;
                anim.SetTrigger("Fall");
            }

            RaycastHit hitInfo;
            if(Physics.Raycast(transform.position,-transform.up,out hitInfo,col.bounds.extents.y + 0.1f,layerMask)) 
            //자기 자신의 방향에서 아래 방향으로 레이저를 (박스콜라이더 y값*1/2)+0.1f 만큼 특정 레이어만 반응하게 레이저를 쏨
            {
                anim.SetTrigger("Land");
                isJump=false;
                isFall=false;
            }
        }

        if(Input.GetKeyDown(KeyCode.Space)&&!isJump){
            isJump=true; //한번만 실행되게
            rigid.AddForce(Vector3.up * jumpForce);
            anim.SetTrigger("Jump");
        }
    }

    private void TryWalk(){
        float _dirX=Input.GetAxisRaw("Horizontal"); 
        //A,D키 혹은 방향키(오,왼)을 뜻함 오른쪽 키가 눌리면 1이 리턴되고 왼쪽키가 눌리면 -1이 리턴 안눌리면 0리턴
        float _dirZ=Input.GetAxisRaw("Vertical"); 
        //w,s키 혹은 방향키(위,아래)을 뜻함 위키가 눌리면 1 아래키 눌리면 -1 안눌리면 0리턴

        Vector3 direction = new Vector3(_dirX,0,_dirZ);
        isMove=false;
        
        if(direction!=Vector3.zero){

             isMove=true;
            this.transform.Translate(direction.normalized *moveSpeed * Time.deltaTime);
            //대각선 움직임도 수평 수직 움직임과 똑같이 만들기 위해 normalized를 함. 하지 않으면 대각선 속도만 유독 빨라짐.
        }
        anim.SetBool("Move",isMove);
        anim.SetFloat("DirX",direction.x);
        anim.SetFloat("DirZ",direction.z);
    }
}

```

Blend와 Substate Machine를 이용하면 깔끔하게 만들 수 있음! 

![](https://images.velog.io/images/kkily55/post/cdc0f340-d02b-44f5-b197-9562602f4a4e/image.png)

Jump 더블클릭시
![](https://images.velog.io/images/kkily55/post/2b418339-f34e-4a27-aff7-6340e6d4a647/image.png)
