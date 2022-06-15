# django_rest_framework
***
## 개념 정리
### 1. args, kwargs
   - `args`
     - argument의 줄임말이며, `*args`로 사용
     - args에는 `어떠한 단어가 들어가도 무방`하지만, 들어가는 위치는 준수해야 함
     - 복수의 인자를 함수로 받고자 할 때 사용
     - args의 타입은 `tuple`
      ```python
      def show_your_name(*names):
        for name in names:
          print(f'{names.index(name)} 번째 이름 : {name}')
        print(type(names))
        return True

      show_your_name('steve', 'malone', 'jake')
      
      
      >>> 0 번째 이름 : steve
          1 번째 이름 : malone
          2 번째 이름 : jake
          <class 'tuple'>
      ```
   - `kwargs`
      - keyword arguments의 줄임말이며, `**kwargs`로 사용
      - `keyword = 특정값`의 형태로 함수 안에 작성
      - kwargs는 `dictionary`의 형태로 전달됨
                
     ```python
     def soccer_player(**name):
        for key, value in name.items():
          print(f'His {key} is {value}!!')
        print(type(name))
        return True

     soccer_player(name='messi')
     soccer_player(name='ronaldo')
     
     >>> His name is messi!!
        <class 'dict'>
        His name is ronaldo!!
        <class 'dict'>
     ```
   - args와 kwargs 사용
     ```python
     def show_your_name(*names, **age):
        for name in names:
           for key, value in age.items():
              if len(names) > 1:
                print(f'저의 이름은 {name}이며, 제 나이는 {value}입니다.')
              else:
                print()
                print(f'사실 저 {name}인데,,,, 저 {value}살입니다.')
        return True

     show_your_name('steve', 'malone', 'jake', age=20)
     show_your_name('malone', age=21)
     
     >>> 저의 이름은 steve이며, 제 나이는 20입니다.
         저의 이름은 malone이며, 제 나이는 20입니다.
         저의 이름은 jake이며, 제 나이는 20입니다.

         사실 저 malone인데,,,, 저 21살입니다.
     ```
### 2. mutable과 immutable의 특성
   - 변수가 함수의 매개변수로 전달될 때 `원래 입력 변수값이 변경되는지 안되는지 결정`하는 중요한 속성
   - `mutable`
      - 문자의 뜻 그대로 `값이 변하는` 속성. 주소값을 할당
        - 리스트(List)
        - 딕셔너리(Dictionary)
   - `immutable`
      - 문자의 뜻 그대로 `값이 변하지 않는` 속성. 값을 할당
      - 만약 값이 변하게 하고 싶다면 `deepcopy()`나 리스트의 경우 `list[:]` 사용
        - 숫자형(Number)
        - 문자열(String)
        - 튜플(Tuple)
### 3. DB Field에서 사용되는 Key 종류와 특징
   -  
### 4.  QuerySet과 object의 차이
   - `QuerySet`
      - 데이터베이스의 `row`에 해당하며, 데이터베이스에서 전달받은 `객체들의 list`
      - `Dictionary`의 형태로 전달되므로 각각의 경우에 따라 접근하는 방법이 상이
      1) <클래스이름>.objects.values()
        - 아래와 같이 `django shell`에서 `.values`를 사용해 딕셔너리의 키와 밸류 구조를 확인할 수 있음
          - QuerySet은 `리스트 안에 딕셔너리`가 있는 형태 
          - `<변수명>[리스트인덱스값]['딕셔너리키값']`으로 value 값을 확인할 수 있음
        ```python
        >>> python manage.py shell
        >>> from user.models import UserModel
        >>> user_info = UserModel.objects.values()
        >>> user_info
         <QuerySet [{'id': 1, 'password': 'pbkdf2_sha256$320000$o8h39peh2nBH0g2F0rQGHE$NHKHRHonACwdurmFrIfyYNi0wNW7hWelrJ17RADB0gc=', 
            'last_login': datetime.datetime(2022, 6, 13, 12, 26, 27, 319703, tzinfo=datetime.timezone.utc), 
            'is_superuser': False, 'username': 'aaaaaaaa', 'first_name': '', 'last_name': '', 'email': '', 'is_staff': False, 'is_active': True, 
            'date_joined': datetime.datetime(2022, 6, 13, 12, 26, 20, 151691, tzinfo=datetime.timezone.utc)}, ... ]>
        >>> user_info[0]['username']
         'aaaaaaaa'
        ```
        
      2) <클래스이름>.objects.get(id=id값)
        - `get`의 경우, 딕셔너리의 요소를 `1개` 반환
        - 요소가 존재하지 않을때는 `DoesNotExist`, 여러개 존재할때는 `MultipleObjectsReturned` 에러가 발생
        - 단 하나만의 객체가 반환되기 때문에 `<변수명>.딕셔너리키값`으로 value를 확인할 수 있음
        ```python
        >>> user_info_2 = UserModel.objects.get(id=2)
        >>> user_info_2
         <UserModel: bbbbbbbb>
        >>> user_info_2.username
         'bbbbbbbb'
        ```
      3) <클래스이름>.objects.filter(id=id값)
        - `filter`는 get과 다르게 주어진 파라미터의 값에 맞는 값들을 `list`의 형태로 `여러 개` 들고 올 수 있음
        - list의 형태이기 때문에 `<변수명>[리스트인덱스값]`으로 QuerySet의 값을 불러올 수 있음
        ```python
        >>> user_info_3 = UserModel.objects.filter(username__contains='j')
        >>> user_info_3
         <QuerySet [<UserModel: wjdeorms>, <UserModel: jjjjjjjj>]>
        >>> user_info_3[0]
         <UserModel: wjdeorms>
        ```
      4) <클래스이름>.objects.all()
        - `all`은 QuerySet 안의 모든 객체를 `list`의 형태로 반환
        ```python
        >>> user_info_all = UserModel.objects.all()
        >>> user_info_all
         <QuerySet [<UserModel: aaaaaaaa>, <UserModel: bbbbbbbb>, <UserModel: wjdeorms>, <UserModel: eeeeeeee>, <UserModel: tttttttt>, 
          <UserModel: gggggggg>, <UserModel: hhhhhhhh>, <UserModel: mmmmmmmm>, <UserModel: nnnnnnnn>, <UserModel: zzzzzzzz>, <UserModel: jjjjjjjj>,
          <UserModel: glglglgl>, <UserModel: pppppppp>]>
        ```
   - `object`
      - 속성과 행동을 모아놓은 것
        - 속성 : 객체 속성(properties)
        - 행동 : 메서드(method)  
      - 우리가 models.py에 만드는 class를 생각하면 됨!
        - 우리가 `Movie` 모델을 생성할 때 해당 모델이 가져야 할 속성을 다음과 같이 작성 
        - 영화에 대한 제목, 이미지, 평점, 줄거리, 장르 등의 속성을 해당 모델에 생성
      ```python
      class Movie(models.Model):
         class Meta:
            db_table = "movie"

         movieId = models.CharField(max_length=50)
         title = models.CharField(max_length=256, default='')
         image = models.URLField(max_length=256)
         score = models.DecimalField(max_digits=2, decimal_places=1)
         desc = models.TextField()
         tag = models.ManyToManyField(Tag, related_name='movies')

         def __str__(self):
            return self.title
      ```
      - 여기서 만든 객체가 바로 위의 `QuerySet`의 쿼리 결과로 불러와짐
      - 해당 객체를 가지고 할 수 있는 행위들을 method로 
