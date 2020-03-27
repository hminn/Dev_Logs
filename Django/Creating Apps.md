## Airbnb-clone #2

> **Creating Apps**

- Abstract Model
  : Django model을 작성하다 보면 여러 테이블에 같은 형식의 필드가 있는 경우가 있다. 이럴 땐, Abstarct Model(추상 모델)을 만들어 상속 받으면 된다.

  ```python
  # example) 
  # 1) Room 정보가 만들어질 때나 업데이트될 때를 알 수 있으면 좋을 것 같아, 이에 해당하는 필드를 추가하려고 한다.
  # 2) 생각해보니 Rooms 말고도 reservations, reviews, conversations 등에서도 똑같은 필드를 사용하고 싶다.
  # 3) 그렇다면 하나의 추상 모델을 만들어, 해당 필드가 필요한 클래스에 상속해주면 된다.
  
  class Meta:
      abstract = True
  
  #※ Meta class에 abstract = True를 추가하여 해당 모델이 추상 모델임을 알려준다.
  ```

- String method
  : 장고에 있는 모든 class들이 가지고 있는 하나의 Method.
  : 파이썬이 class를 발견하면 class를 마치 string 처럼 보여주는데 이 method를 파이썬에서 \_\_str__이라고  표시한다.

  ```python
  # Room admin list에서 등록된 room의 명칭이 'Room Object(1)' 이런 식으로 되어있다. (string method를 따로 구현해주지 않았다면)
  
  # 만약 list에 room에 설정한 room_name을 표시하고 싶다면 다음과 같이 string method를 구현해주면 된다.
  
  def __str__(self):
      return self.name
  ```

  

> **Relationship Field**

- Foreign key
  : 다대일 관계의 두 모델을 연결해주는 key이다.

  ```
  예를 들어, 에어비앤비 시스템의 room은 한 명의 user가 여러 개를 갖고 있을 수 있다. 이 때, room의 정보에는 한 명의 user 정보만을 가질 수 있는 반면에 한 명의 user는 등록한 다수의 room 정보를 가질 수 있다. 이러한 관계를 데이터베이스 상에서 이어주는 것이 Foreign key이다.
  ```

- ManyToMany relationships
  : 서로 여러개의 관계를 가지는 형태