## Django : Airbnb Clone #5

> #### #5.0 Review Model

- **Django Relationship을 이용하여 String Method 다루기**

  - **Admin Panel list name **<br>
    : Review Admin Panel List에 각각의 리뷰를 어떻게 표현하는게 좋을까?

    ```python
    # 단순히 self.(FieldName)으로 사용해도 괜찮다.
    # 만약 return 값을 self.review로 줬다면 review의 내용이 List에 보이게 될 것이다.
    
    # 이 때, Django의 Relationshop을 이용해서 다양하게 표현할 수 있다.
    # 그 중에서도 가장 많이 쓰이는 것이 Foreign key를 이용한 표현이다.
    # 예를 들자면, Review class 내에서 Foreign key로 정의한 user field를 통해 다음과 같은 표현이 가능하다는 것이다.
    
    def __str__(self):
    	return self.user.username
    
    # 이 뿐만 아니라, room이라는 Foreign key를 타고 들어가서 또다시 그 내부에 있는 host라는 Foreign key를 타고 들어가 host의 username을 가져올 수도 있다는 것이다!
    
    def __str__(self):
        return self.room.host.username
    
    # + 추가
    # 여러 개의 field를 조합해서 쓰고 싶다면 다음과 같이 쓰면 된다.
    
    def __str__(self):
        return f'{self.user.username} - {self.room}'
    ```

> #### #6.0 Room Admin Panel

- **[Model Admin Option](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/)** : Model이 Admin 상에서 보여지는 형태를 설정
  - [list_display](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display) : 어떤 field를 외부적으로 보여지게 할 것인지 설정 
  - [list_filter](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_filter) : filtering 시에 사용할 field들을 지정
  - [search_fields](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.search_fields) : 검색의 기준이 되는 field를 지정 (내부 옵션들을 이용해 검색 방식을 달리할 수 있다. Default는 icontains[Search word와 겹치는 모든 객체를 반환] )
  - [filter_horizontal](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.filter_horizontal) : Field에서 여러 값을 입력하고 싶을 때 ManyToManyField를 사용하면 다중 선택 박스가 나타나 여러 개를 선택할 수 있지만, 값이 수 백개라면 다루기 힘들것이다. 그럴 때 사용하는 option
  - [fieldsets](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.fieldsets) : field를 지정한 그룹 별로 묶는 옵션
  - 등등.. 매우 많은 것들이 있으니 Documentation을 참고하여 사용하도록!

