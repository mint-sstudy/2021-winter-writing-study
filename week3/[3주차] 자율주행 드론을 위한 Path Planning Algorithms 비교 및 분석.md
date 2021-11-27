이번 글에서는 졸프에서 사용할, 자율주행 드론을 위한 Path Planning 알고리즘을 선별하기 위해 여러 알고리즘을 비교하고 분석하는 글을 써보려 합니다.

---

저희 팀이 개발하고자 하는 프로젝트에 대해 한 마디로 표현하자면 다음과 같습니다.

## 산불을 탐지하고 화재 위치를 전송하는 자율주행 드론

드론의 자율주행을 위해서는 **Path Planning, 경로 계획**이 반드시 수립되어야 합니다.

이때 저희 팀의 자율주행 드론의 경로 계획에서 고려되어야 할 점은 다음과 같습니다.

- 실시간성
- 경로의 최적성 판단
- 예상치 못한 상황(산불이 발생한 경우) 경로를 변경
- 장애물(나무)이 나타났을 때 기존에 그래프화된 지도 정보를 업데이트 용이

산에서 발생한 불을 인식하고 이후 경로를 변경해야 하기 때문에 이에 대응할 수 있는 Path Planning 알고리즘이 필요합니다. 
또한, 산의 특성 상 나무가 많아 나무의 위치를 미리 파악하기 어렵기에 나무와 같은 장애물이 발생하였을 때 해당 정보를 지도 정보에 업데이트할 수 있는 Path Planning 알고리즘이 필요합니다.

본격적으로 자율주행 드론에 사용되는 Path Planning Algorithms을 분석하고, 위에서 정리한 저희 팀 자율주행 드론의 Path Planning을 할 때 고려할 점을 토대로 저희 팀에 적합한 Path Planning 알고리즘을 선별하도록 하겠습니다.

---

## 1. Dijkstra Algorithm

Dijkstra Algorithm은 시작점과 도착점을 선정하여 두 점 사이의 최단거리를 구하는 알고리즘입니다.

![dijkstra](https://user-images.githubusercontent.com/70934572/143684479-a127e8cd-05b1-4814-87d5-a000ed10ac85.gif)

현재 위치해 있는 노드에서 인접한 노드들을 탐색하며 가중치가 작은 노드를 찾고 가중치가 작은 노드까지의 총 가중치에 노드와 이어진 다른 노드까지의 가중치를 합하여 저장되어 있는 총 가중치가 더 작은 노드로 이동하는 원리입니다.

![d2](https://user-images.githubusercontent.com/70934572/143684484-4d930fdb-1f3e-4cff-8edd-9f9f2beb701a.png)

위의 그림은 Dijkstra Algorithm을 MATLAB을 사용하여 시뮬레이션을 한 결과로,
왼쪽 상단에서부터 오른쪽 하단까지의 최단경로가 단순 직선경로인 빨간색처럼 보이지만
가중치를 부여하였을 때 최적의 경로는 초록색입니다.

이는 경로 사이에 있는 건물을 미리 확인하고 이에 가중치를 부여한 것으로, 경로(주행 지역) 내에 있는 여러 장애물을 파악하여 비행경로를 설정할 때 항상 경로의 정보와 가중치를 생각하며 Path Planning을 할 수 있습니다.

[Dijkstra Algorithm을 활용한 Drone Path Planning 영상](https://youtu.be/JNNeqXBoDOA?t=130)
영상의 2:10 부분에서 test하는 장면을 볼 수 있습니다. (영상 속 드론 소리가 꽤 큽니다.)

하지만 Dijkstra Algorithm의 경우에는 네트워크의 규모가 커질수록 즉, 그래프의 노드와 에지 수가 증가할수록 경로를 도출하는데 오랜 시간이 소요됩니다. 이는 실시간으로 경로 계획을 하는 것에 있어 좋은 방법은 아닙니다. 😥
뿐만 아니라 시작점과 목표점 사이의 모든 경로의 가중치를 계산하기 때문에 연산의 양과 낭비가 매우 많습니다.

✅ Dijkstra Algorithm은

🥕 실시간으로 Path Planning을 하는 데에 적합하지 않고  
🥕 연산량이 매우 많기 때문에  

저희 팀의 자율주행 드론의 Path Planning에는 적합하지 않습니다.



## 2. A* Algorithm (A-star Algorithm)

A* Algorithm은 출발 꼭짓점에서 목표 꼭짓점까지 최적 경로를 탐색하는 그래프 탐색 알고리즘으로 Searched-based Algorithm 중 가장 보편적으로 사용되고 있습니다.
![astar](https://user-images.githubusercontent.com/70934572/143688246-959f367c-19d0-46b5-956d-bc94ff1410d1.gif)

A* Algorithm에서 사용되는 비용 함수는 시작 정점-현재 정점까지의 경로 가중치와 현재 정점-목적 정점까지의 추정 경로 가중치의 합으로 계산합니다. 이러한 비용 비교를 통하여 최적의 다음 경로를 찾아내고, 이 과정을 반복하여 최종 목적지까지의 경로를 계산합니다.

A* Algorithm의 비용 함수 f(n)를 식으로 작성하면 아래와 같습니다.

> f(n) = g(n) + h(n)

g(n) : 출발 꼭짓점으로부터 꼭짓점 n까지의 경로 가중치  
h(n) : 꼭짓점 n으로부터 목표 꼭짓점까지의 추정 경로 가중치  
(여기서 비용함수란, 최적 경로를 결정하기 위해서 각 꼭짓점에서 정의하는 것을 말합니다.)

[A* Algorithm Path Finding](https://youtu.be/-L-WgKMFuhE)

위의 영상은 A* Algorithm이 동작하는 모습을 그림으로 설명한 영상입니다.

[A* Algorithm을 활용한 Drone Path Planning simulation](https://youtu.be/TR6J0TWS7p04)

(FCND Simulator) A* Algorithm을 통해 Path Planning을 Simulation하는 영상입니다.

추정 경로 가중치를 설계하는 방식에 따라 탐색 속도를 가속화 시킬 수 있고 해의 최적성을 향상시킬 수 있기에 A* Algorithm을 변형한 알고리즘도 있습니다.



## 3. LPA* Algorithm (Lifelong Planning A-star Algorithm)

LPA* Algorithm은 다음 경로점 후보들이 가질 수 있는 목표 꼭짓점까지의 경로 가중치를 모두 조사하여, 그중 가장 최솟값을 추정 경로 가중치로 사용합니다.

이를 통해 알고리즘이 최적의 해로 수렴하지 못하고, 극소점이 될 가능성을 줄이고자 한 알고리즘입니다.

하지만 A* Algorithm과 LPA* Algorithm의 가장 큰 문제점은 장애물에 대한 정확한 정보가 사전에 제공되어야 한다는 것입니다. 만약 탐색을 하다가 돌발 장애물이 나타날 경우에는 현재 위치를 새로운 시작점으로 간주하고 목적지까지 다시 경로를 계획해야 합니다. 또한, 수정된 경로의 최적성은 보장할 수 없습니다.

결국 환경에 변동이 있을 시 유연하게 반응하기 어렵다는 문제가 있습니다. 😥

✅ A* Algorithm, LPA* Algorithm은

🥕 장애물에 대한 정보가 사전에 필요하고   
🥕 예상치 못한 돌발 장애물(나무 등)이 발생하였을 때 유연하게 대응하기 어렵기에

저희 팀의 드론이 비행할 산에서는 나무나 동물 등 여러 장애물에 대한 정확한 정보를 사전에 파악하기 어려우므로 저희 팀의 자율주행 드론의 Path Planning에는 적합하지 않습니다.



## 4. D* Algorithm (D-star Algorithm)

D* Algorithm은 예상치 못한 돌발 장애물이 나타났을 때 그래프화된 기존의 지도 정보를 업데이트하여 새로운 최적 경로를 효율적으로 재계산하기 위해 제안된 알고리즘입니다. (Dynamic A* Algorithm으로 알려져 있기도 합니다.)
![dstar](https://user-images.githubusercontent.com/70934572/143688273-6a4373b3-cf00-42f5-b476-2a4a630b5bfb.PNG)

A* Algorithm과 D* Algorithm 둘 다 탐색 기반 알고리즘이지만 두 알고리즘의 가장 큰 차이점은 계산 방향이 반대인 것입니다.
D* Algorithm은 부분적으로만 알고 있는 환경에서 주행할 경우를 고려하기 때문에 동적환경에서 유리합니다.
또한, 주행 도중 환경에 변화가 있을 경우 탐색 시작점이 현재 드론의 위치로 고정되지 않아 돌발 장애물이 나타날 경우 그래프화된 지도 정보를 업데이트하기 용이하며 기존에 계산해 놓은 Back pointer 정보들을 최대한 이용하기 위해 계산량을 줄일 수 있습니다.

[D* Algorithm Path Finding](https://youtu.be/X5a149nSE9s)

위의 영상을 보면 부분적으로 알고 있는 환경에서 돌발 장애물이 발생할 경우 경로를 변경하는 것을 알 수 있습니다.

하지만 일반적으로 탐색 기반 알고리즘은 고차원 공간에서 비효율적으로 여겨지곤 합니다. 또한, 짧은 시간 안에 가능한 해 집합들을 모두 탐색하면서 해를 구하는 방법이기에 해 집합의 크기에 따라 탐색 시간이 길어질 수도 있습니다. 😥

✅ D* Algorithm은

🥕 탐색 기반 알고리즘이므로 고차원 공간에서 비효율적이며  
🥕 탐색 시간이 매우 길어질 수 있기에  

배터리의 용량에 많이 영향을 받는 저희 팀의 자율주행 드론에는 탐색 시간이 길수록 전체 경로를 탐색하는데 어려움이 있을 수 있어 저희 팀의 자율주행 드론의 Path Planning에는 적합하지 않습니다.

## 5. RRT Algorithm (Rapidly exploring Random Tree Algorithm)

RRT Algorithm은 Path Planning을 위해 시작점에서부터 목표점까지 점진적으로 Tree를 구축하는 방식을 사용합니다.
여러 샘플링 기반 알고리즘 중 가장 보편적으로 사용됩니다.

> 여기서! 샘플링 기반 알고리즘이란,
복잡한 고차원 공간에서도 **빠른 시간 안에 적절한 경로를 효율적으로 찾아내기 위해 제안된 방법**입니다.
형상 공간(configuration space)을 random하게 sample point를 여러 개 생성하여 point-wise로 공간을 탐색하여 경로를 찾아냅니다.


![rrt](https://user-images.githubusercontent.com/70934572/143688291-2681db50-1b01-456d-83d6-f8c28bc31f50.gif)

RRT Algorithm은 무인 이동체가 현재 위치에서 갈 수 있는 길 중 하나를 랜덤으로 선택해 이동해 보고 장애물과 부딪히는지 판단합니다.

만약 부딪힌다면 다른 길을 선택하고 부딪히지 않으면 선택한 지점까지 그래프를 연장하는 알고리즘을 통해 목적지에 도달할 때 까지 그래프의 연장을 반복하면서 최종 경로를 찾습니다.

[RRT* Algorithm Path Planning](https://youtu.be/p24VheOABV0?t=27)

RRT Algorithm은 영상에서 볼 수 있듯이 여러 가지 모양의 장애물이 있는 복잡한 환경 속에서도 효율적으로 경로를 생성시켜줍니다. 또한, 비선형 동적 프로그래밍보다 연산량이 적습니다.

하지만 샘플링 기반 알고리즘이기에 샘플링 개수가 충분하지 않다면 존재하는 경로를 찾지 못할 수 있습니다. 뿐만 아니라 RRT Algorithm은 탐색된 경로의 최적성을 보장하지 못합니다. 😥

✅ RRT Algorithm은

🥕 어떤 비용 함수도 사용하지 않기 때문에 경로의 최적성을 보장할 수 없어  

경로의 최적성을 고려해야 하는 저희 팀의 자율주행 드론의 Path Planning에는 적합하지 않습니다.



## 6. RRT* Algorithm

RRT* Algorithm은 RRT Algorithm의 단점을 보완하기 위해 나온 알고리즘으로, Rewriting 연산을 추가 수행하여 현재까지 구축된 트리의 구조를 수정함으로써 점근적 최적성(Asymptotic optimality)를 보장할 수 있습니다.
![rrtstar](https://user-images.githubusercontent.com/70934572/143688306-0647b529-236f-43bc-9c89-909c2c2a7117.gif)

Rewriting 연산은 트리 내의 노드를 대체하여 cost를 줄일 수 있는 경우 기존 노드를 대체하여 새로운 노드와 연결해 트리를 구성하는 것입니다.
점근적 최적성(Asymptotic optimality)은 알고리즘의 반복 횟수가 무한대에 가까워짐에 따라 최적 해를 포함할 확률이 1에 가까워진다는 것입니다.

이러한 연산을 반복해가며 계획된 경로가 점근적으로 최적 해에 수렴하게 되어 RRT Algorithm은 하지 못하였던 경로의 최적성을 보장할 수 있습니다.

[RRT* Algorithm Path Planning Simulation](https://youtu.be/g-5eRvpQW-w?t=80)

영상의 1:20 부분에서 RRT* Algorithm을 통해 Path Planning을 Simulation을 볼 수 있습니다.

✅ RRT* Algorithm은

🥕 실시간으로 경로 수정이 가능하고  
🥕 경로의 최적성을 보장할 수 있으며  
🥕 고차원 공간에서 경로 탐색이 빠르게 가능하기에  

위에서 정리한 저희 팀의 자율주행 드론의 경로 계획에서 고려되어야 할 점에 적절하기에 저희 팀의 자율주행 드론의 Path Planning에 적합합니다!

RRT* Algorithm이 저희 팀의 가장 적합한 Path Planning Algorithm이니 이와 관련한 Github 코드도 같이 찾아보았습니다.

[Quadcopter path planning using RRT* and minimum jerk trajectory generation](https://github.com/ayushgaud/path_planning)

위의 Github에 들어가면 RRT* Algorithm을 활용하여 Path Planning을 test할 수 있어 여러 오픈소스와 함께 저희 팀의 자율주행 드론의 Path Planning Algorithm을 만들어가고자 합니다.

추후에는 저희 팀의 드론 시뮬레이션을 맡은 분과 협업하여 해당 알고리즘이 Simulator에서 잘 작동하는지 확인할 예정입니다.

---

### 참고 문헌

[1] JiSu Han, SeungBeom Hong and WonHyuck Choi, "Route Optimization of Drones in Designated Areas Using Algorithm"

[2] Kevin Danancier, Delphine Ruvio, Inkyung Sung and Peter Nielsen, "Comparison of Path Planning Algorithms for an Unmanned Aerial Vehicle Deployment Under Threats"

[3] 홍유경, 김유경, 김수성, 이희수, 차지훈, "Research Trends on Environmental Perception and Motion Planning for Unmanned Aerial Vehicles"

[4] S. Koenig and L. Maxim, “Fast Replanning for Navigation in Unknown Terrain”

[5] S. Grzonka, G. Grisetti, and W. Burgard, “A Fully Autonomous Indoor Quadrotor”

[6] Gicheol Wang, Soojeon Lee, Byoung-Sun Lee, "Path Planning for Drones in the Airsim Environment"

[7] Zhangjie Fu, Jingnan Yu, Guowu Xie, Yiming Chen, and Yuanhang Mao, "A Heuristic Evolutionary Algorithm of UAV Path Planning"

[8] D.C. Schedl, I. Kurmi, and O. Bimber, "An Autonomous Drone for Search and Rescue in Forests using Airborne Optical Sectioning"

[9] Hao Zhou, Hai-Ling Xiong, Yun Liu, Nong-Die Tan and Lei Chen, "Trajectory Planning Algorithm of UAV Based on System Positioning Accuracy Constraints"

[10] [https://theclassytim.medium.com/robotic-path-planning-rrt-and-rrt-212319121378](https://theclassytim.medium.com/robotic-path-planning-rrt-and-rrt-212319121378)
