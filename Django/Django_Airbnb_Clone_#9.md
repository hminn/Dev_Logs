# Airbnb-Clone #9

- #9.1 seed_amenities command
- #9.2 seed_everything and seed_users
- #9.3 seed_rooms part One
- #9.4 seed_rooms part Two
- #9.5 seed_rooms part Three
- #9.6 seed_reviews
- #9,7 seed_lists
- #9.8 seed_reservations

---

## > seed_amenities command

- Amenity Data를 command를 이용하여 추가해보자!

  ```python
  from django.core.management.base import BaseCommand
  from rooms import models as room_models
  
  
  class Command(BaseCommand):
      help = "This command tells me that he loves me"
  
      def handle(self, *args, **options):
          amenities = [
              "Air conditioning",
              "Alarm Clock",
              # ...(생략)
          ]
          for a in amenities:
              room_models.Amenity.objects.create(name=a)
          self.stdout.write(self.style.SUCCESS("Amenities created!"))
  ```

- Amenity의 objects에 접근하여 create 메소드를 사용하여 생성 작업!

- 필요한 속성에 대한 값들을 인자로 넘겨주면 정상적으로 생성이 된다.

---

## > seed_everything and seed_users

- [Django_seed](https://github.com/mstdokumaci/django-seed#using-with-code)와 Custom Command를 이용하여 User 생성

  ```python
  from django.core.management.base import BaseCommand
  from django_seed import Seed
  from users.models import User
  
  
  class Command(BaseCommand):
  
      help = "This command creates many users"
  
      def add_arguments(self, parser):
          parser.add_argument(
              "--number",
              default=1,
              type=int,
              help="How many users do you want me to create?",
          )
  
      def handle(self, *args, **options):
          number = options.get("number")
          seeder = Seed.seeder()
          seeder.add_entity(User, number, {"is_staff": False, "is_superuser": False})
          seeder.execute()
          self.stdout.write(self.style.SUCCESS(f"{number} Users created!"))
  
  ```

- **add_arguments**  
  : 지정 argument 설정, default 값 설정, type 설정, help Message 설정

- **handle**  

  - seeder.add_entity  
    : 생성할 모델, number, 추가 제약 조건(?) 설정(?)

---

## > seed_rooms part

- **seeder.add_entity**
  : 테스트 데이터를 생성할 모델을 설정하고 각 필드에 대한 생성 조건을 추가할 수 있다.  
  : lambda 함수를 이용하여 값을 함수화하여 생성할 수도 있다.  
  : [seeder의 faker 메소드](https://faker.readthedocs.io/en/master/)를 이용하여 원하고자 하는 속성의 테스트 데이터를 생성할 수 있다. 매우 다양한 속성이 존재하므로 웬만한 것들은  faker를 이용하면 좋다.

  ```python
  seeder.add_entity(
              room_models.Room,
              number,
              {
                  "name": lambda x: seeder.faker.address(),
                  "host": lambda x: random.choice(all_users),
                  "room_type": lambda x: random.choice(room_types),
                  "price": lambda x: random.randint(1, 300),
                  "guests": lambda x: random.randint(1, 10),
                  "beds": lambda x: random.randint(1, 5),
                  "bedrooms": lambda x: random.randint(1, 5),
                  "baths": lambda x: random.randint(1, 5),
              },
          )
  ```

- **ManyToManyField Seed**

  - `seeder.execute()` returns the list of inserted PKs, indexed by class
    : seeder를 통해 생성된 인스턴스들에 삽입된 PK의 list를 execute 메소드를 통해 얻을 수 있다.

  ```python
  created_photos = seeder.execute()
  ```

  - `created_clean = flatten(list(created_photos.values()))`
    : PK List를 1차원 array의 형태로 차원을 낮춘다 (flatten)

  - 생성된 인스턴스의 PK를 이용하여 다대다 필드에 대한 데이터를 다음과 같이 임의로 생성한다. 


    ```python
            amenities = room_models.Amenity.objects.all()
            facilities = room_models.Facility.objects.all()
            rules = room_models.HouseRule.objects.all()
            for pk in created_clean:
                get_room = room_models.Room.objects.get(pk=pk)
                print(get_room, dir(get_room))
                for i in range(3, random.randint(10, 30)):
                    room_models.Photo.objects.create(
                        caption=seeder.faker.sentence(),
                        room=get_room,
    					file=f"room_photos/{random.randint(1,31)}.webp",
                    )
                for a in amenities:
                    magic_number = random.randint(0, 15)
                    if magic_number % 2 == 0:
                        get_room.amenities.add(a)
                for f in facilities:
                    magic_number = random.randint(0, 15)
                    if magic_number % 2 == 0:
                        get_room.facilities.add(f)
                for r in rules:
                    magic_number = random.randint(0, 15)
                    if magic_number % 2 == 0:
                        get_room.house_rules.add(r)
            self.stdout.write(self.style.SUCCESS(f"{number} Users created!"))
    ```

    - `room_models.Room.objects.get(pk=pk)`
      : pk 인자를 통하여 생성된 Room Model Instance를 하나씩 불러온다.

---

## > seed_lists

- Command의 전체 코드 중 일부분


  ```python
  		rooms = room_models.Room.objects.all()
  
      				""" 생략 """    
      
  		created = seeder.execute()
          cleaned = flatten(list(created.values()))
          for pk in cleaned:
              list_model = list_models.List.objects.get(pk=pk)
              to_add = rooms[random.randint(0, 5) : random.randint(6, 30)]
              list_model.rooms.add(*to_add)
  ```

  - *to_add는 도대체 무엇이람?
    : to_add 자체는 QuerySet array이고, 우리가 필요한 것은 array 안에 있는 요소이기에 * 연산자를 붙여주어 요소만 빼오는 것이다.
    *==이에 대해서는 자세히 찾아볼 필요가 있을 듯!==*

