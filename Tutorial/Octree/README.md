# Octree

구현물 : [python](https://github.com/jcummings2/pyoctree), [python#2](https://github.com/mhogg/pyoctree), [cpp](https://github.com/brandonpelfrey/SimpleOctree/), [시각화[js]](https://github.com/vanruesc/octree-helper)

[PCL Module octree overview](http://docs.pointclouds.org/1.8.1/group__octree.html)

## 개요 

- 각 노드 당 최대 8개의 자식들을 가질 수 있는 하나의 트리
- 입방체(정육면체)로 감싸인 3차원 공간을 표현하는데 이상적인 구조
- 옥트리는 삼차원 공간에서 오브젝트를 표현하기 위한 자료구조
- 계층화하여 그룹화하고, 공간 내의 빈 부분에 대한 표현을 피한다.
- 옥트리는 입방체(정육면체) 로 감싸인 3차원 공간을 표현하는데 이상적인 구조로 간주되며 공간을 분할하기 위해 쓰이는 경우 각 노드의 자식들은 부모입방체를 8동분 하는 동일한 입방체들이 됩니다 [[참고]](https://blog.naver.com/lee9742/80188584931)


구성 

- 첫 번째 옥트리 노드는 루트 셀인데,여덟 개의 연속되는 요소들의 배열이다. 


- 이들 각 요소들은 여덟 개의 연속된 요소들 중 다른 하나를 가리킬 수 있다. 
    - 여기에서 각각은 여덟 개의 연속되는 요소들 중 다른 하나를 가리키는 식인데, 이는 특정한 최대 숫자의 레벨에 도달할 때까지 계속된다. 


- 마지막 레벨은 단말 레벨이며, 여기에서 단말 요소나 복셀(voxel)이 저장된다.


옥트리 데이터

- 경계 입방체 : 옥트리가 차지 하는 공간을 의미하는 직육면체

- 입체목록 : 각 노드의 경계 입방체 안에 들어가는 다각형들의 목록

- 자식들 : 각 노드는 자신의 자식들을 가리키는 포인터들을 저장

- 이웃들 : 각 노드는 최대 6개의 이웃들을 가진다.

## 복셀 검색 원리 

![](https://i.imgur.com/2tmKHic.png)
`[트리는 여덟 개의 복셀과 다섯 개의 레벨을 가지고 있다. (각 레벨은 서로 다른 색으로 표현된다. )]`



위의 그림은 자료 구조가 메모리(왼쪽)와 그것의 삼차원 공간 표현(오른쪽) 사이에서 어떻게 대응되는지를 보여준다. 
 

옥트리의 마지막 레벨에 저장되는 여덟 개의 레벨은 분홍색으로 표현된다. 

위의 그림의 표는 마지막 셀에 있는 다섯번 째 복셀(index 4)의 좌표를 보여준다. 

그것의 좌표는 : 31, 30, 30이며, 후에 오른쪽에서 이진 형식으로 보여진다. 

마지막으로 각 좌표 비트와 연관된 각 열도 옥트리의 레벨과 관련이 있다. 

각 열의 비트를 합치면, 관련 레벨을 위한 셀의 각 요소에 대한 (테이블의 마지막 열에 나온) 인덱스를 획득하게 된다. 

그리고 이것은 트리를 순회하고 검색된 복셀을 찾는 데 사용된다. 이것이 옥트리에서 주어진 복셀을 검색하는 원리이다. 

> 각 열을 합칠 때, x를 0비트, y를 1비트, z를 2비트로 취급하면, 1 + 2 + 4 = 7, 0 + 0 + 4 = 4가 된다. 위의 표에서 열이 나타내는 숫자 0, 1, 2, 3, 4는 복셀의 인덱스를 의미하고 있다. 그런데 표가 이상하다. 비트의 합이 0 ~ 7까지 순서대로 나오는게 맞을텐데 다 7이고 4번째 열만 4다 

이러한 구조의 주요 이점은 복셀 좌표가 저장되지 않고, 순회되는 요소들의 위치에 의해 암묵적으로 내포된다는 것이다. 

이것은 좌표가 저장하기에는 크고 성가신 고해상도 복셀 공간에서 매우 편리하다. 

3D 그리드도 복셀 위치를 내포하는 좌표를 가지고 있지만, 대부분의 볼륨이 비어있는 경우에도 볼륨 내의 모든 복셀을 표현해야 한다는 심각한 단점을 가지고 있다. 




> [옥트리](https://blog.naver.com/lifeisforu/80022423480), [옥트리 분할 기법](https://blog.naver.com/lifeisforu/80022423600)



---

# [PCL Series 3 - Use of Octree](https://blog.csdn.net/qq_22170875/article/details/84844385 
)

1.1 Baidu Encyclopedia:
The definition of an octree is that if it is not an empty tree, the child nodes of any node in the tree will only have eight or zero, that is, the child nodes will not have numbers other than 0 and 8. So what is this used for? Imagine a cube, how many small cubes can we cut into at least the same? The answer is eight. Imagine that we have a room. There is a gold coin hidden in a corner of the room. We want to find out the gold coins very quickly. What do you do smartly? We can treat the room as a cube, cut it into eight small cubes, then eliminate the small cubes that don't put anything, and then cut the small cubes that might hold the gold coins into eight equal parts.... Go on, average on Log8 ( Gold coins can be found within the time of all items in the room. Therefore, octree is the scene management used in 3D space, you can quickly know the position of the object in the 3D scene, or detect whether there is collision with other objects and whether it is within the visible range.

1.2 The principle of implementing octree:
(1). Set the maximum recursion depth. 
(2). Find the maximum size of the scene and build the first cube with this size. 
(3). Throw the unit element elements into cubes that can be included and have no children. 
(4). If the maximum recursion depth is not reached, subdivide the octaves and then carry all the unit elements of the cube to the eight sub-cubes. 
(5). If the number of unit elements assigned to the subcube is not zero and is the same as the parent cube, then the subcube stops subdividing because of the allocation of the subdivided space according to the spatial partition theory. There must be fewer, if it is the same number, then how to cut the number is still the same, it will cause infinite cutting. 
(6). Repeat 3 until the maximum recursion depth is reached.



---

## Paper

- [8진트리 모델을 사용한 3D 물체 모델링과 특징점](): 이영재 
- [O-CNN: Octree-based Convolutional Neural Networks for 3D Shape Analysis](https://wang-ps.github.io/O-CNN_files/CNN3D.pdf): 2017
- ~~[OctNet: Learning Deep 3D Representations at High Resolutions](https://arxiv.org/pdf/1611.05009.pdf)~~: CVPR2017
- [Octree Generating Networks:Efficient Convolutional Architectures for High-resolution 3D Outputs](https://arxiv.org/pdf/1703.09438.pdf)

## Article 

- [Octree (옥트리) ](http://blog.naver.com/hermet/57456541): 네이버블로그 


---

# Octree Viewer 

![](http://pointclouds.org/documentation/tutorials/_images/octree_bunny2.png)

> http://pointclouds.org/documentation/tutorials/walkthrough.php#visualization

octree_viewer: allows the visualization of octrees

Syntax is: octree_viewer <file_name.pcd> <octree resolution>

Usage example:

Example: ./octree_viewer ../../test/bunny.pcd 0.02