# SECTION 4. Working With Databases

# 17. ****Environment Files and Database Connections****

가. 도커를 사용하고 있는 경우 영상과 달리 이미 example_app db가 생성되어 있음. 따라서, .env 파일에 아래와 같이 정보를 입력해주면 됨.

```jsx
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=example_app
DB_USERNAME=sail
DB_PASSWORD=password
```

<aside>
💡 단, 저의 경우 처음 프로젝트 명을 example_app 로 하지 않고 munglara 로 설정하였기에 위의 DB_DATABASE 부분을 아래와 같이 설정하였음. 아래 화면에서 데이터베이스명이 다른 점 참고바람.

</aside>

```jsx
DB_DATABASE=munglara
```

나. 데이터베이스 스키마 생성

```jsx
php artisan migrate
```

```jsx
./vendor/bin/sail mysql –uroot –p
```

- 위 명령 입력 후 아래와 같이 확인해 볼 수  있음.

![image01.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image01.png)

- 또는, 영상에 나오는 아래와 같은 데이터베이스 접속 및 관리 프로그램 등을 설치하여 확인해 볼 수 있음.

[https://tableplus.com/](https://tableplus.com/)

![image02.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image02.png)

![image03.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image03.png)

# 18. Migrations: The Absolute Basics

가. 데이터베이스 초기 생성 정보가 저장된 곳

- database-migrations 폴더에 정의되어 있음.

![image01.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image01%201.png)

나. 데이터베이스 이전으로 되돌리기(rollback)

- 그럼 최근 migrate 작업은 취소되고 그 직접으로 돌아감.

```jsx
php artisan migrate:rollback
```

![image01.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image01%202.png)

다. 데이터베이스 처음으로 초기화

```jsx
php artisan migrate:fresh
```

- 개발 과정에는 유용할 수 있으나, 모든 데이터가 손실되기 때문에 조심해야 할 명령임.
- .env 에서 ‘APP_ENV=local’ 이 아닌 ‘APP_ENV=production’ 일 경우 본 명령을 내리면 경고창을 띄워줌.

라. DB를 수정, 생성하고 싶을 때

  1) database-migrations 밑에 있는 파일 수정

![image01.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image01%203.png)

  2) 롤백 후 생성

```jsx
php artisan migrate:rollback
php artisan migrate 

또는 

php artisan migrate:fresh
```

# 19. Eloquent and the Active Record Pattern

가. Eloquent

![image01.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image01%204.png)

(참조) [https://pronist.dev/142](https://pronist.dev/142)

나. Active Record Pattern

<참고1>

```jsx
part = new Part()
part.name = "Sample part"
part.price = 123.45
part.save()
```

위의 명령은 아래와 동일함

```jsx
INSERT INTO parts (name, price) VALUES ('Sample part', 123.45);
```

<참고2>

```jsx
b = Part.find_first("name", "gearbox")
```

위의 명령은 아래와 동일함

```jsx
SELECT * FROM parts WHERE name = 'gearbox' LIMIT 1;
```

- Active Record Pattern은 데이터베이스 의 데이터에 액세스하는 접근 방식
- 데이터베이스의 테이블(or 뷰)는 클래스로 래핑
- 인스턴스는 테이블의 단일 행에 연결
- 객체 생성 후 저장 시 테이블에 새 행이 추가
- 로드된 모든 개체는 데이터베이스에서 해당 정보를 가져옴.
- 개체가 업데이트되면 테이블의 해당 행도 업데이트됨.
- 래퍼 클래스는 테이블 또는 뷰의 각 열에 대한 접근자 메서드 또는 속성을 구현.

다. 데이터 추가 방법

  1) 터미널에 아래 명령어 입력

```jsx
php artisan tinker
```

    ※ Laravel용 REPL이 ‘tinker’ (REPL: Read-Eval-Print-Loop 대화형 실행창)

  2) 이후 아래 그림처럼 예시 데이터 입력

![image01.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image01%205.png)

- 비밀번호는 bcrypt 문 사용하여 암호화 시켜줌.
- 마지막, $user->save(); 를 해주어야 DB에 저장됨.
- 그럼, 레코드 생성 날짜 등은 라라벨이 알아서 저장해줌.

  3) DB 수정 및 확인

![image01.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image01%206.png)

  4) DB 검색 및 조회

```jsx
>>> User::find(1);

>>> User::find(1234);    // null 반환
=> null

>>> User::findOrFail(1234);  // 예외처리
Illuminate\Database\Eloquent\ModelNotFoundException with message 'No query results for model [App\Models\User] 1234'

>>> User::all();     // 모든 사용자 조회
 
>>> $users = User::all();   // 전체 collection 연결
>>> $users->pluck('name');   // 이름 필드값만 불러옴.
     // 위의 2줄은 아랫줄과 동일한 의미 : 
>>> $users->map(function($user) { return $user->name;});

>>> $users->first();  // 첫번째 row 
>>> $users[0]         // 배열로도 표현 가능 : 
```

# 20. Make a Post Model and Migration

- 예전 app-Models-Post.php 를 완전 새롭게 구현함.
- 여기서는 make:migration, make:model 기능 활용

가. post 테이블 생성을 위한 migration 작업

```jsx
php artisan make:migration create_posts_table
```

- database-migrations ~생성날짜~~_create_posts_table.php 파일이 생성됨.
- create_******_table.php => ****** 부분이 파일 안에 테이블명으로 자동으로 생성되어 있음.

나. 이제 원하는 필드 생성

- string는 최대 255자, 그보다 많은 내용을 저장할 경우 text 사용

![image01.png](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/image01%207.png)

- 테이블 이름은 복수형, 그와 연관된 모델 이름은 단수형으로 붙이는 것이 일반적임.

다. model 생성

```jsx
php artisan make:model Post
```

- 그럼 app-Models 밑에 Post.php가 생성됨. (Eloquent model)

라. 생성 확인을 위한 확인 ⇒ tinker로 접속

```jsx
$ php artisan tinker
>>> App\Models\Post::all();  //posts 테이블에서 모든 자료를 가져옴. 현재는 비어있음.
>>> App\Models\Post::count();   //행 갯수
>>> $post = new App\Models\Post;
>>> $post->title = '첫번째 게시물';
>>> $post->excerpt = '그들의 피가 황금시대를 눈이';
>>> $post->body = "~~~~~~~~~";
>>> $post->save();

>>> use App\Models\Post;
>>> Post::count();
>>> Post::all();
>>> Post::first();
>>> Post::find(1);
>>> $post = Post::find(1);
>>> $post->title;
```

마. 이제 web.php 파일의 아랫 부분에서 $slug를 $id로 수정해줌.

![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled.png)

바. posts.blade.php 수정

![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%201.png)

# 21. ****Eloquent Updates and HTML Escaping****

## ✅ 데이터에 html태그, 양식을 적용하는 방법

## 🍇 데이터에서 html 태그, 양식 적용하기 (방법1)

1. 터미널에서 tinker 실행하기 
    
    ```jsx
    ./vendor/bin/sail php artisan tinker
    ```
    
2. Post 모델의 첫번째 자료 $post 변수에 담기
    
    ```jsx
    $post = App\Models\Post::first();
    ```
    
3. $post의 body 부분 html 태그 인식하도록 바꾸기
    
    ```jsx
    $post->body = '<p>' . $post->body . '</p>'
    ```
    
    <aside>
    💡 자바스크립트처럼 ``  백태그? 처리 안되고  . 도 없으면 인식이 안됨!                    '  ' (작따) 안에 html 태그를  넣고,    "    " (큰따) 안에 그대로 출력될 문자를 넣고,                        . (마침표)  으로  항목들을 구분함. (맞나요?)
    
    </aside>
    
    <aside>
    💡 (예시)
    
    $post->body = '<p>' .  " This happens with every controller. I've tried " . '<span>' . "yyyyyyyyeeeeee" . '</span>'. '</p>'
    
    (예시2)  큰따옴표 안에 다 넣기
    
    $post->body =  “<p>This happens with every controller. I've tried  <span>yyyyyyyyeeeeee</span></p>”
    
    </aside>
    
4. $post 변경한 내용 저장 및 확인
    
    ```jsx
    $post->save();
    ```
    
    ```jsx
    localhost/posts/1    새로고침 
    ```
    
5. Post 모델의 2번째 자료 가져와서 변경하기(여전히  tinker 내부)
    
    ```jsx
    $post = App\Models\Post::find(2);
    ```
    
    <aside>
    💡 find( )  내부의 숫자는 id를 의미
    
    </aside>
    
6. 3번과 4번 동일하게 실행

## 🍇 데이터에서 html 태그, 양식 적용하기 (방법2)

1. Post모델을 기본으로 설정 (여전히 tinker 환경)
    
    ```jsx
    use App\Models\Post;
    ```
    
2. 시도해보기
    
    ```jsx
    $post = Post::first();
    ```
    
    ```jsx
    $post->title = 'My <strong>First</strong> Post'; 
    ```
    
    ```jsx
    $post->save();
    ```
    
3. 확인하기(실패)      
    
    ```jsx
    localhost/posts/1    새로고침 
    ```
    
4. post.blade.php 파일에서 수정 (html 코드 다 인정)
    
    ```jsx
    <h1> {!! $post -> title !!} </h1>
    ```
    
    <aside>
    💡 기존 코드 <h1> {{ $post -> title }} </h1>
    
    </aside>
    
5. 확인하기(성공)      
    
    ```jsx
    localhost/posts/1    새로고침 
    ```
    

## ⚠ 데이터에서 html 태그, 양식 적용의 문제점

1. $post 변수 확인하기 (tinker 환경)
    
    ```jsx
    $post
    ```
    
2. $post 변수 title 변경 (자바스크립트 alert() 함수 포함)
    
    ```jsx
    $post->title = 'My Post <script>alert("hello")</script>';
    ```
    
3. $post 변수 저장
    
    ```jsx
    $post->save();
    ```
    
4. 확인하기  
    
    ```jsx
    localhost/posts/1    새로고침 
    ```
    

<aside>
⚠️ 내가 입력 권한을 갖고 있을 경우에만   { !!  !! } 사용하기

 blade.php 파일에서 html 태그로 묶어주는 게 나을 것 같다.

</aside>

## 🗨 참고자료

1.  tinker? 간단한 설명 
    
    > [https://mosei.tistory.com/268](https://mosei.tistory.com/268)
    > 
2. alert 함수?
    
    > [https://bono915.tistory.com/entry/JavaScript-alert-prompt-confirm-대화상자-내장함수](https://bono915.tistory.com/entry/JavaScript-alert-prompt-confirm-%EB%8C%80%ED%99%94%EC%83%81%EC%9E%90-%EB%82%B4%EC%9E%A5%ED%95%A8%EC%88%98)
    > 

# 22. ****3 Ways to Mitigate Mass Assignment Vulnerabilities****

## ✅ 여러 자료를 입력하는 방법에서 주의사항!

## ⚠ 가장 좋은 건 사전에 예방하기! (필요 없는 부분은 사용자가 입력하지 못하게 만들기)

## 🍇 세 번째 포스트 자료 추가하기

1. 터미널에서 tinker 실행하기 
    
    ```jsx
    ./vendor/bin/sail php artisan tinker
    ```
    
2. Post 모델 사용하기
    
    ```jsx
    use App\Models\Post;
    ```
    
3. 새로운 $post 만들기
    
    ```jsx
    $post = new Post;
    ```
    
    ```jsx
    $post->title = "My Third Post";
    ```
    
    ```jsx
    $post->excerpt = "excerpt of post";
    ```
    
    ```jsx
    $post->body = "when i heard that sound, i felt like freezed. Her shouting was so awkward and she slept only 30 minutes. so i didnt know what i have to do. what i know is my free time was over.";
    ```
    
    ```jsx
    $post->save();
    ```
    
    ```jsx
    Post::all();
    ```
    
4. posts.blade.php 뷰파일 수정
    
    ```jsx
    {!! $post->title !!}
    ```
    
    <aside>
    💡 기존코드  {{ $post->title }}
    
    </aside>
    
5.  [localhost](http://localhost) 메인화면 새로고침 후 확인

## 🍇 네 번째 포스트 자료 추가하기

1. 터미널 tinker 환경에서 네 번째 포스트 만들기
    
    ```jsx
    Post::create(['title' => 'My Fourth Post', 'excerpt' => 'excerpt of post', 'body' => 'when i heard that sound, i felt like freezed. Her shouting was so awkward and she slept only 30 minutes. so i didnt know what i have to do. what i know is my free time was over.']) 
    ```
    
2. MassAssignmentException  에러 발생!

## 🍇 MassAssignment Error 해결 (방법1 - fillable)

<aside>
✅ 지정된 필드가 아니면 값을 저장할 수 없음.

</aside>

1. app/Models/Post.php  들어가서 해당 부분 추가하기
    
    ```jsx
    class Post extends Model
    {
    	use HasFactory;
    
    	//==== 추가하기 =====//
    	protected $fillable = ['title', 'excerpt', 'body'];
    }
    ```
    
2. fillable 방식은 저장 가능한 필드를 설정함

## 🍇 MassAssignment Error 해결 (방법2 - guarded)

<aside>
✅ 지정된 필드는 자료 임의 저장이 불가, 나머지 필드는 원하는 대로 저장.

</aside>

1. app/Models/Post.php  들어가서 해당 부분 수정하기
    
    ```jsx
    class Post extends Model
    {
    	use HasFactory;
    
    	//==== 추가하기 =====//
    	protected $guarded = ['id'];
    
    	//==== 음영처리하기 =====//
    	// protected $fillable = ['title', 'excerpt', 'body'];
    }
    ```
    
2. guarded 방식은 저장하지 못하는(정할 수 없는) 필드를 설정함

## ⚠ 7분 30초 전후에… never ever ever… 뭐라는…

## 🍇 데이터 베이스에서 자료 update 하기

1. Post모델을 기본으로 설정 (여전히 tinker 환경)
    
    ```jsx
    use App\Models\Post;
    ```
    
2. $post에서 변경하고 싶은 항목만 update함수로 저장
    
    ```jsx
    $post = Post::first();
    ```
    
    ```jsx
    $post->update(['excerpt' => 'Changed']);
    ```
    
    <aside>
    ⚠️ $post→save() 하지 않아도 됨!
    
    </aside>
    
    <aside>
    ✅ 수정하던 (아직 save() 하지 않은) 자료를 원래 데이터 베이스 자료로 설정할 때
    
    $post→fresh() 
    
    </aside>
    
3. 확인하기   
    
    ```jsx
    localhost/    새로고침 
    ```
    

## 🗨 참고자료

### 1. Mass Assignment? (대량 할당?)

- 데이터를 쉽게 저장할 수 있도록 돕는 도구 (by. Eloquent ORM)?

> [https://dev.to/zubairmohsin33/understanding-mass-assignment-in-laravel-eloquent-orm-331g](https://dev.to/zubairmohsin33/understanding-mass-assignment-in-laravel-eloquent-orm-331g)
> 

### 2. Vulnerability (취약성?)

- 원하지 않는 정보까지 저장, 할당될 수 있음

# 23. ****Route Model Binding****

## ✅ Route에서 모델의 특정 필드로 접속하기

### ⇒ 영상에서는 방법1 선택!

## 🍇 (사전작업) Post 모델에 slug 추가

1. vsc 로 route/web.php 파일 열고 posts/{post} 부분 변경하기
    - {post} 라는 와일드카드 값과 Post의 $post가 일치하면? 아, Post모델에서 post를 가져오라는 말이군. id겠지?
    - $post 대신 다른 $foo 값을 입력하면 모델에서 내용을 불러오지 못함
    
    ```php
    Route::get('posts/{post}', function (Post $post){
    	return view('post', [
    		'post' => $post  // Post 모델의 개별 post를 활용! (default가 id)
    	])
    })
    ```
    
2. database/migrations/ …..  create_posts_table.php에서 slug필드 추가
    
    ```php
    Schema::create('posts', function (Blueprint $table) {
                $table->id();
                $table->string('slug')->unique();  // string으로 된 slug필드 추가, 중복불가능
                $table->string('title');
                $table->string('excerpt');
                $table->string('body');
                $table->timestamps();
                $table->timestamp('published_at')->nullable();
            });
    ```
    
3. 터미널)   테이블 새로고침하기!
    
    ```php
    ./vendor/bin/sail php artisan migrate:fresh
    ```
    
4. TablePlus) 
    - 1 선택 확인(아직 새로고침 하지 않아서 전에 입력한 자료 보이는 듯)
    - 2 전체 자료 선택 후 마우스 우클릭
    - 3 Copy as
    - 4 insert satement 클릭
    - 5 새로고침 (자료 사라짐)
    
    ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%202.png)
    
5. TablePlus) 
    - 1 sql 클릭
    - 2 자료 붙여넣고,   `slug`,  추가하기 (백틱 - 탭키 위 자판버튼)
    - 3 각 자료에 slug에 해당하는 거 추가(빈칸없이 -로 연결)   ‘my-first-post’,   (따옴표)
    - 콤마, 따옴표 제대로 입력후 4번 클릭
    - 5번 제대로 적용되었는지 확인
    
    ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%203.png)
    
6. TablePlus) posts table에서 새로고침 후 확인

## 🍇 Post 모델의 slug 주소로 접속하기(방법1)

1. web.php route 파일 아래처럼 수정
    
    ```php
    Route::get('posts/{post:slug}', function (Post $post){
    	return view('post', [
    		'post' => $post  
    	])
    })
    ```
    
2. resources/views/posts.blade.php 아래처럼 id를 slug로 수정
    
    ```php
    <h1>
      <a href="posts/{{ $post->**slug** }} ">
        {!! $post->title !!}
      </a>
    </h1>
    ```
    
3. localhost에서 개별 포스트 들어가서 주소창 확인해보기 (slug와 일치?)

## 🍇 Post 모델의 slug 주소로 접속하기(방법2)

1. web.php route 파일 아래처럼 수정
    
    ```php
    Route::get('posts/{post}', function (Post $post){
    	return view('post', [
    		'post' => $post  
    	])
    })
    ```
    
2. app/Models/Post.php  protected 아래에 세줄 추가 
    
    ```php
    protected $guarded = [];
    
    **public function getRouteKeyName()
        {
            return 'slug';
        }**
    ```
    
3. localhost에서 개별 포스트 들어가서 확인해보기

> [https://dev.to/zubairmohsin33/understanding-mass-assignment-in-laravel-eloquent-orm-331g](https://dev.to/zubairmohsin33/understanding-mass-assignment-in-laravel-eloquent-orm-331g)24. ****Your First Eloquent Relationship****
> 

# 24. ****Your First Eloquent Relationship****

## ✅ Category와 Post 모델 연결시키기

## 🍇 (사전작업) Category 모델, migration 파일(필드 생성) 추가

1. 터미널)  모델을 만들 때 사용할 수 있는 명령어 확인하기
    
    ```php
    ./vendor/bin/sail php artisan help make:model
    ```
    
2. 방법1. 따로 만들기(실행하지 않아요~~~)
    
    ```php
    ~~./vendor/bin/sail php artisan make:migration create_categories_table~~
    ```
    
    ```php
    ~~./vendor/bin/sail php artisan make:model Category~~
    ```
    
3. 방법2. 한 번에 만들기
    
    ```php
    ./vendor/bin/sail php artisan make:model Category -m
    ```
    
    <aside>
    ⚠️ create_categories_table  에서 타자 오류 가능성이 높으니.. 방법2가 편할 듯!
    
    </aside>
    

## 🍇 Migration 파일 수정(두 개 연결하기)

1. Category.php 에 name / slug 필드 추가하기
    
    ```php
    public function up()
    {
        Schema::create('categories', function (Blueprint $table) {
            $table->id();
            **$table->string('name');  // 추가하기
            $table->string('slug');  // 추가하기**
            $table->timestamps();
        });
    }
    ```
    
2. Post.php 에 추가.   category의 아이디를 인용해서 필드로 사용하겠다!
    
    ```php
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            **$table->foreignId('category_id');  // 추가하기**
            $table->string('slug')->unique();
            $table->string('title');
            $table->string('excerpt');
            $table->string('body');
            $table->timestamps();
            $table->timestamp('published_at')->nullable();
        });
    }
    ```
    
3. 터미널)  migrate 파일 새로고치기!
    
    ```php
    ./vendor/bin/sail php artisan migrate:fresh
    ```
    

## 🍇 Post와 Category 모델 Data 입력하기

1. categories table 에  1 Personal personal / 2 Work work / 3 Hobbies hobbies  자료 입력  
2. categories table 에 자료 추가 (방법1 - 영상처럼 tinker 활용)
    
    ```php
    ./vendor/bin/sail php artisan tinker
    ```
    
    ```php
    use App\Models\Category;
    ```
    
    ```php
    $c = new Category;
    ```
    
    ```php
    $c->name = 'Work';
    ```
    
    ```php
    $c->slug = 'work';
    ```
    
    ```php
    $c->save();
    ```
    
3. categories table 에 자료 추가 (방법2 - tableplus 활용)
    
    ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%204.png)
    
4. post table에 자료 추가하기 **(카테고리 id 1, 2, 3으로 각 1개씩 총 3개 만들기)**
    
    ```php
    ./vendor/bin/sail php artisan tinker
    ```
    
    ```php
    use App\Models\Post;
    ```
    
    ```php
    Post::create(['title'=>'my work post', 'excerpt'=>'excerpt for my post',
    'body' => 'lorem ipsum dolar sit amet.', 'slug'=>'my-work-post', 'category_id'=>2])
    ```
    
5. tableplus)  category_id가 1인 posts 들을 찾는 방법
    
    ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%205.png)
    
6. tinker에서 찾는 방법 
    
    ```php
    ./vendor/bin/sail php artisan tinker
    ```
    
    ```php
    Post::where('category_id', 1)->find(1)
    ```
    

## ⚠ 데이터에서 직접 입력만 하면, category_id가 1인 것에 대한 정보도 없고 모델간에 연결이 되지 않은 상태

## 🍇 Post와 Category 모델 연결하기

### ⚠ 상자 안에 물건을 넣는다고 생각하기
    - Category 안에 Post들을 넣을 수 있음! 
    - 상자가 되는 Category 는 hasOne 또는 hasMany 가능.
    - 물건이 되는 Post 는 belongsTo 또는 belongsToMany 가능.

1. Post.php 모델 파일 들어가서 내용 추가, 저장
    
    <aside>
    💡   가능한 값 ( hasOne   hasMany    belongsTo    belongsToMany )
    
    </aside>
    
    <aside>
    💡   생각하는 방법. 
      현재 열어둔 파일  Post 는  category에(을/를)  (  가능한 값  )    넣어보기.
    
    </aside>
    
    ```php
    class Post extends Model
    {
        use HasFactory;
    
        protected $guarded = [];
    
        **public function category()   // 세 줄 추가하기
        {
            return $this->belongsTo(Category::class);
        }**
    }
    ```
    
2. 터미널) 들어가서 Post의 첫번째 자료 선택
    
    ```php
    ./vendor/bin/sail php artisan tinker
    ```
    
    ```php
    $post = App\Models\Post::first();
    ```
    
3. category함수 실행시키고 연결된 클래스와 아이디 확인하기
    
    ```php
    $post->category();
    ```
    
    <aside>
    ⚠️ BadMethodCallException with message 'Call to undefined method App\Models\Post::category()’ 오류가 날 경우
      1.  Post.php 변경내용 저장했는지 확인
      2.  ctrl + c로 tinker를 종료했다가 다시  2번으로 돌아가 실행하기
    
    </aside>
    
    ```php
    $post->category
    ```
    
    ```php
    $post->category->name
    ```
    

## 🍇 Posts, Post 화면에 Category 보여주기

1. posts.blade.php
    
    ```php
    <h1>
        <a href="posts/{{ $post->slug }} ">
            {!! $post->title !!}
        </a>
    </h1>
    
    **<p>
        <a href="">{{ $post->category->name }}</a>
    </p>**
    
    <div>
        {{ $post->excerpt }}
    </div>
    ```
    
2. post.blade.php
    
    ```php
    <h1>
        {!! $post->title !!}
    </h1>
    **<p>
        <a href="#">{{ $post->category->name }}</a>
    </p>**
    <div>
        {!! $post->body !!}
    </div>
    ```
    

# 25.  ****Show All Posts Associated With a Category****

## ✅ Category를 누르면 관련 posts 보여주기

## 🍇 Category에서 posts 연결하기

1. web.php 상단, 하단에 추가하기!
    
    ```php
    use Illuminate\Support\Facades\Route;
    use App\Models\Post;
    **use App\Models\Category;   //추가
    
    // ... //
    
    Route::get('categories/{category:slug}', function (Category $category) {
        return view('posts', [
            'posts' => $category->posts // category 모델에서 posts 설정필요!
        ]);
    });**
    ```
    
2. Category.php 모델 파일에 posts 함수 설정
    
    ```php
    class Category extends Model
    {
        use HasFactory;
    
        **public function posts()
        {
            return $this->hasMany(Post::class);
        }**
    }
    ```
    
3. tinker에서 확인. Category 1(personal)에 해당하는 posts를 보여주기
    
    ```php
    ./vendor/bin/sail php artisan tinker;
    ```
    
    ```php
    App\Models\Category::first()->posts
    ```
    

## 🍇 화면에서 해당 주소로 연결시키기

1. posts.blade.php에 a링크 주소 넣기
    
    ```php
    <p>
        <a href="**/categories/{{ $post->category->slug }}**">{{ $post->category->name }}</a>
    </p>
    ```
    
2. post.blade.php 에 a링크 주소 넣기
    
    ```php
    <p>
        <a href="**/categories/{{ $post->category->slug }}**">{{ $post->category->name }}</a>
    </p>
    ```
    

# **26.  Clockwork, and the N+1 Problem**

## ✅ N+1 Query 문제 해결하기

## N+1 Query 문제 확인하기

N+1 Query 문제를 확인하기 위해 log 파일을 만드는 과정입니다. 주석(/* */) 안의 코드를 새로 추가해주시면 됩니다.

web.php 파일

```php
Route::get('/' function() {
	
	/*

	\Illuminate\Support\Facades\DB::listen(function ($query) {
	logger($query->sql, $query->bindings);

	*/

	return view('posts', [
		'posts' => Post::all()
	]);
});
```

## Clockwork 설치하기

```bash
$ composer require itsgoingd/clockwork
```

Clockwork를 설치하면 아래와 같이 query와 관련된 정보를 확인할 수 있습니다

![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%206.png)

## N+1 Query 문제 해결하기

N+1 Query 문제의 원인은 라라벨이 기본적으로 lazy loading 하기 때문입니다. 따라서, with()를 사용하여 eager loading의 형태로 바꾸어주면 문제가 해결됩니다.

참고) [https://advancedwebtuts.com/video-tutorial/what-is-difference-between-laravel-eager-vs-lazy-loading-use-of-with-method](https://advancedwebtuts.com/video-tutorial/what-is-difference-between-laravel-eager-vs-lazy-loading-use-of-with-method)

/routes/web.php

```php

Route::get('/' function() {

return view('posts', [
  // 아래 줄이 수정된 부분입니다.
	'posts' => Post::with('category')->get()
]);

```

 수정 후의 clockwork 화면

![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%207.png)

# **27.  Database Seeding Saves Time**

테이블 구조에 변경이 생겨서 아래와 같이 migration을 fresh하게 되면 기존에 테이블에 저장된 데이터가 날아가서 post, category 등의 데이터를 다시 만들어야 하는 문제가 생깁니다.

```bash
터미널
$ php artisan migrate:fresh
```

하지만 아래와 같은 seeding 기능을 사용하면 migration을 fresh한 뒤, 세팅된 seed 데이터를 자동으로 넣어주는 과정까지 하기 때문에 문제가 해결됩니다(아래 DatabaseSeeder.php 파일을 작성한 뒤에 이 명령어를 내리셔야 합니다.)

```bash
터미널
$ php artisan migrate:fresh --seed 
```

그렇다면 같이 seed 데이터를 세팅해보아요

/database/seeders/DatabaseSeeder.php

```php
namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\User;
use App\Models\Category;     //이것도 추가해야 오류가 나지 않네요.
use App\Models\Post;         //영상에서는 이것이 자동으로 생성되는 듯. 윈도우용에서는 
                             //어떤 확장을 설치해야 그렇게 될까요???

class DatabaseSeeder extends Seeder
{
	public function run()
	{
		// truncate()를 포함시키고 터미널에서 php artisan db:seed를 실행하게 되면
    // User, Category, Post 테이블에 저장된 데이터를 지우고 seeding하게 됩니다
    // truncate()를 제외하고 실행하면, 기존 테이블에 저장된 데이터에 추가적으로 seeding합니다
		User::truncate();
		Category::truncate();
		Post::truncate();

		$user = User::factory()->create();

		$personal = Category::create([
			'name' => 'Personal',
			'slug' => 'personal'
		]);

		$family = Category::create([
			'name' => 'Family',
			'slug' => 'family'
		]);

		$work = Category::create([
			'name' => 'Work',
			'slug' => 'work'
		]);

		Post::create([
			'user_id' => $user->id,
			'category_id' => $family->id,
			'title' => 'My Family Post',
			'slug' => 'my-first-post',
			'excerpt' => 'Lorem ipsum dolar sit amet.',
			'body' => 'Lorem ipsum dolor sit amet, consectetur adipicing elit. Morbit viverra ~'
		]);
	}
}

```

(php artisan db:seed는 단순히 db에 seed 데이터를 추가하는 것이고, php artisan migrate:fresh --seed는 migration 파일을 fresh한 뒤에, seed 데이터까지 추가하는 점이 다릅니다!)

# **28.  Turbo Boost With Factories**

터미널에 아래 명령어를 입력하면 기본 틀을 갖춘 UserFactory 파일을 생성해줍니다

```bash
$ php artisan make:factory UserFactory
```

자세한 내용을 함께 채워봅시다!

/database/factories/UserFactory.php

```php
namespace Database\Factories;
 
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;
 
class UserFactory extends Factory
{
		/**
		 * The name of the factory's corresponding model.
     *
		 * @var string
		 */
		protected $model = User::class;

    /**
     * Define the model's default state.
     *
     * @return array
     */
    public function definition()
    {
        return [
            'name' => $this->faker->name(), // faker를 사용하면 다양한 형식에 맞는 dummy 데이터를 쉽게 생성할 수 있어요!
            'email' => $this->faker->unique()->safeEmail(),
            'email_verified_at' => now(),
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
            'remember_token' => Str::random(10),
        ];
    }
}
```

모델 파일도 수정해주어야 합니다!

/app/Models/User.php

```php
namespace App\Models;

// 이 부분이 추가해야 하는 내용입니다.
// 아래 코드들을 추가하여야 User::factory()와 같이 사용할 수 있습니다.

use Illuminate\Contracts\Auth\MustVerifyEmail;
// use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
	// use HasFactory;

}

```

(Post도 User와 같은 방식으로 진행하시면 됩니다)

/database/factories/PostFactory.php

```php
namespace Database\Factories;
 
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;
use App\Models\User; // 아래에서 User::factory()를 실행하기 위해 꼭 import해줘야 합니다 
 
class PostFactory extends Factory
{
		/**
		 * The name of the factory's corresponding model.
     *
		 * @var string
		 */
		protected $model = Post::class;

    /**
     * Define the model's default state.
     *
     * @return array
     */
    public function definition()
    {
        return [
            'user_id' => User::factory(), // 이렇게만 입력해주면 UserFactory에 정의된 대로 User 데이터가 하나 생성되고, 자동으로 id 값을 잡아서 user_id에 저장해줍니다
						'title' => $this->faker->sentence,
						'exerpt' => $this->faker->sentence,
						'body' => $this->faker->paragraph
        ];
    }
}
```

/app/Models/Post.php

```php
namespace App\Models;

use Illuminate\Contracts\Auth\MustVerifyEmail;
// use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;

class Post extends Authenticatable
{
	// use HasFactory;

}

```

UserFactory에서 name을 $this→faker→name()으로 생성되도록 지정하였더라도, 아래와 같이 직접 정의하게 되면 내가 원하는 방식으로 데이터를 생성할 수 있습니다!

```php
User::factory()->create([
	'name' => 'John Doe'
]);
```

# **29.  View All Posts By An Author**

- Post 추가하기
    - php artisan tinker (sail tinker) 실행
    
    ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%208.png)
    
    - 혹시 아래 처럼 나오더라도 쫄지 말고 한번 더 하면 잘 됨 (랜덤으로 생성되지만 중복 생성되는 경우가 있음)
    
    ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%209.png)
    
- 정렬
    - 가장 최근에 작성한 글이 먼저 `Post::*latest*()->with('category')->get()`
    - 참고로 요건 반대 결과 `Post::*oldest*()->with('category')->get()`
- 릴레이션 매서드 이름 변경
    - user() 를 author()로 변경
        - 메서드 이름을 User::class 와 같게 user 로 하면 라라벨은 자동으로 user_id 값을 이용해서 릴레이션을 만든다
        - 하지만 매서드 이름을 모델명과 다르게 하면 자동으로 찾지 못하기 때문에 아래 처럼 ‘user_id’로 참조할 키 값을 명시해 줘야 한다.
    
    ```php
    public function author()
    {
        return $this->belongsTo(User::class, 'user_id');
    }
    ```
    
    - 매서드를 사용한 Blade 파일 수정 `{{ $post->author->name }}`
    - posts.blade.php 도 post.blade.php 처럼 작성자 이름이 나오도록 수정
        
        ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%2010.png)
        
- N + 1 쿼리 문제 해결
    - 엘로퀀트 연관관계들을 속성으로 접근할 때 연관관계 데이터는 "지연 로드" 됩니다. 이는 실제로 속성에 엑세스 하기 전까지 연관관계 데이터가 로드되지 않는다는 것을 의미합니다. 하지만 엘로퀀트는 상위 모델을 조회할 때 연관관계를 맺고 있는 하위 모델을 "eager 로드-즉시로드"할 수있도록 기능을 제공합니다. Eager 로딩은 N + 1 쿼리 문제를 해결 합니다. <출처: 라라벨코리아>
    - 제프리처럼 N +1 쿼리를 확인해 봅니다.
    
    ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%2011.png)
    
    - `author` 이거로딩 추가, 아래 두가지 모두 같은 결과를 보여줍니다.
        - `Post::*oldest*()->with('category', 'author')->get()`
        - `Post::*oldest*()->with(['category', 'author'])->get()`
        - N + 1 쿼리가 해결되었습니다.
        
        ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%2012.png)
        
- 특정 작가가 작성한 글 모아보기
    - 라우트 추가
        
        ```php
        Route::get('authors/{author}', function (User $author) {
            return view('posts', [
                'posts' => $author->posts
            ]);
        });
        ```
        
        - [http://localhost/authors/1](http://blog.test/authors/1) 로 접속하면 글 목록이 나옴
        - dd($author)로 찍어 보면 User 인스턴스가 확인됨.
- 모델바인딩 키 바꾸기
    - authors 뒤에 숫자가 나오는 것이 못 마땅한 제프리는 /authors/seongbum 이런식으로 이름으로 나오게 하고 싶은데 현재 DB에 있는 이름은 실제 이름이어서 서비스에서 사용할 사용자 이름 칼럼을 새로 추가 함.
    - 마이그레이션 파일에 username 칼럼 추가하고, UserFactory 파일에도 추가
        - User 테이블 코드 `$table->string('username')->unique();`
        - UserFactory 코드 `'username' => fake()->unique()->userName,`
    - 라우트 변경
        - `Route::get('authors/{author:username}', function (User $author) {`
    - 블레이드 수정
        - `<a href="/authors/{{ $post->author->username }}"> {{ $post->author->name }}</a>`
- TablePlus 에서 username 변경후 변경한 이름으로 접속해서 확인
    
    ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%2013.png)
    

# 30. ****Eager Load Relationships on an Existing Model****

- 글목록을 출력하는 다른 라우트에서 발생하는 N+1 쿼리 문제 해결
    - 다른 라우트도 이거로딩 적용
        
        ```php
        Route::get('categories/{category:slug}', function (Category $category) {
            return view('posts', [
                'posts' => $category->posts->load(['category', 'author'])
            ]);
        });
        
        Route::get('authors/{author:username}', function (User $author) {
            return view('posts', [
                'posts' => $author->posts->load(['category', 'author'])
            ]);
        });
        ```
        
    - `Post::*oldest*()->with('category', 'author')->get()`
        - 아직 get()으로 글들을 가져오기 전에는 with()로 이거로딩
    - `$category->posts->load(['category', 'author'])`
        - (결과물로 가져와진 자료인) 컬랙션은 load()로 이거로딩
    - [https://laravel.kr/docs/9.x/eloquent-collections#introduction](https://laravel.kr/docs/9.x/eloquent-collections#introduction)
        - 사용가능한 메소드들 중에  load 내용 확인
            
            ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%2014.png)
            
- ****모델을 로딩할 때 연관관계모델을 항상 Eager 로딩 하기****
    - web.php 라우트 파일에 반복해서 이거로딩하는 릴레이션을 Post 모델에 설정해서 반복되는 코드를 줄입니다.
    - Post 모델에 추가
        - `protected $with = ['category', 'author'];`
    - web.php 수정
        
        ```php
        Route::get('/', function () {
            return view('posts', [
                'posts' => Post::oldest()->get()
            ]);
        });
        
        Route::get('posts/{post:slug}', function (POST $post) {
            return view('post', [
                'post' => $post
            ]);
        });
        
        Route::get('categories/{category:slug}', function (Category $category) {
            return view('posts', [
                'posts' => $category->posts
            ]);
        });
        
        Route::get('authors/{author:username}', function (User $author) {
            return view('posts', [
                'posts' => $author->posts
            ]);
        });
        ```
        
    
- 릴레이션 속성 없이 모델의 원래 속성 값들만 사용
    - 모델에 `protected $with` 로 설정하면 편한 경우가 많긴 하지만, 활용되지 않을 불필요한 데이터까지 다루게 되어 시스템 자원을 낭비하는 경우도 있습니다.
    - 이럴 경우에는 모델에 이거로딩이 설정되어 있어도 사용할때 이거로딩을 해제할 수 있습니다.
    - tinker 에서 확인
        - 모델에 기본 적용 되었을 때 App\Models\Post::first() 하면 Post 모델만 호출했지만 아래처럼 category 와 author 도 함께 가져온다.
        
        ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%2015.png)
        
        - without() 으로 해제가 가능하다.
        
        ![Untitled](SECTION%204%20Working%20With%20Databases%204acb4c6bdd2847589cbff53c499ccfd5/Untitled%2016.png)