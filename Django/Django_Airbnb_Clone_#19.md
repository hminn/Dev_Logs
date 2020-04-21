# Airbnb-Clone #19

- #19.0 Intro to TailwindCSS
- #19.1 Setting Up TailwindCSS
- #19.2 Setup Explanation Part One
- #19.3 Setup Explanation Part Two
- #19.4 Static Files on Django

---

## > TailwindCSS

### 1. What is [TailwindCSS](https://tailwindcss.com/)

- Utility를 우선시하는 Framework.
- CSS Property들을 Class로써 가지고 있다.
- Html, CSS에 Class Name을 따로 만들어서 쓰지 않아도 된다. (작명 고민 해결!)
- Class 자체가 이미 디자인을 위한 요소가 되기 때문!
- `<div class = "md:flex bg-white rounded-lg p-6">`  
  : 위와 같은 방식으로 class를 통해 객체의 디자인을 변경할 수 있다.
- [여기](https://builtwithtailwind.com/)를 보면 알 수 있다시피, TailwindCSS로 작성된 웹들은 서로 비슷한 느낌이 크게 없다. (bootstrap으로 작성된 웹사이트들을 보면 뭔가 다 비슷해보이는 느낌이 든다.)

### 2. TailwindCSS Setup

- TailwindCSS를 Setup 하기 위해서는 [Node.js](https://velopert.com/133) 와 [Gulp](https://valuefactory.tistory.com/314)가 설치되어있어야 함.  
  (But, Node.js 나 Gulp를 이해하고 있을 필요는 크게 없다.)

- 다음의 과정을 순서대로 진행한다.

  1. `npm init` 후 ENTER 연타

  2. `npm install gulp gulp-postcss gulp-sass gulp-csso node-sass autoprefixer -D`

  3. `npm install tailwindcss -D`

  4. 이 때, node_modules 폴더 안에 수 많은 파일들이 생성되었을 것임.  
     엄한 파일 git에 올리지말고 .gitignore에 `node_modules/` 를 추가해주자!

  5. `npx tailwind init`

  6. 요로케까지 하면 tailwind.config.js가 생길 것임.

  7. 프로젝트 루트 디렉토리에 assets 폴더를 생성하고 그 안에 scss/styles.scss를 생성

  8. gulpfile.js 파일을 하나 만들어 주고 다음과 같이 입력해준다.

     ```js
     const gulp = require("gulp");
     
     const css = () => {
       const postCSS = require("gulp-postcss");
       const sass = require("gulp-sass");
       const minify = require("gulp-csso");
       sass.compiler = require("node-sass");
       return gulp
         .src("assets/scss/styles.scss")
         .pipe(sass().on("error", sass.logError))
         .pipe(postCSS([require("tailwindcss"), require("autoprefixer")]))
         .pipe(minify())
         .pipe(gulp.dest("static/css"));
     };
     
     exports.default = css;
     ```

  9. styles.scss 에 다음과 같이 입력한다.

     ```css
     @tailwind base;
     @tailwind components;
     @tailwind utilities;
     ```

  10. package.json에 `"scripts": {"css" : "gulp"}` 를 입력한다.

  11. `npm run css` 를 실행

  12. static 폴더에 css 폴더, styled.css 파일이 생성됨을 확인할 수 있다.

### 3. 간단한 설명

- **Gulp**  
  : 빌드 자동화 도구(JavaScript Task Runner)

- **PostCSS**
  : *PostCSS는 자바스크립트 기반의 플러그인을 사용하여 CSS 기능을 자동화하는* **소프트웨어 개발 도구**
- **gulpfile.js의 코드에 대한 설명 : 어떤 흐름인지 대략적인 이해를 위해**
  1. `.src("assets/scss/styles.scss")`
     : scss 파일이 어디있는지 알려주는 코드
  2. `.pipe(postCSS([require("tailwindcss"), require("autoprefixer")]))`
     : Scss 파일을 css로 변환해주는 작업에서 어떠한 Rule을 적용할 것인지 알려주는 코드
  3. `.pipe(minify())`  
     : 매우 긴 코드를 조금 더 짧게 만들어주는 작업
  4. `.pipe(gulp.dest("static/css"));`  
     : 변환된 css 파일을 어디에 저장할 것인지 알려주는 작업

- **왜 SCSS를 사용하는가?**  
  : 가장 큰 장점으로는 CSS작업 생산성을 높여준다 그래서 스타일시트를 보다 빠르게 작성할 수 있고, 편리하고 깔끔하게 가독성이 게 정리할수있다.
- 실제 개발 작업은 assets 폴더에서, 브라우저에 실제로 반영할 css 파일은 static 폴더에
- 정리하자면, TailwindCSS로 작업한 내용을 PostCSS를 통해 Real CSS 파일로 만들어주고, 그 파일을 minify를 통해 사이즈를 작게 만들어주는 일련의 과정을 거친 최종 CSS파일을 static/css 폴더로 보내지는 것.
- `npm run css`  
  : 생산성을 위해 TailwindCSS와 같은 Framework를 사용하지만, 브라우저에 반영하기 위해서는 작업 내용을 Real CSS 파일로 변환하는 작업을 해주어야 한다. 

### 4. Static 파일을 Enable 하는 방법

- [In config/settings.py]

  ```python
  STATIC_URL = "/static/"
  
  STATICFILES_DIRS = [os.path.join(BASE_DIR, "static")]
  ```

- 위의 과정을 거치면 "로컬주소/static/css/styles.css" url을 요청하면 styles.css로 접근이 가능함을 확인할 수 있따.

- 작성한 styles.css 파일을 프론트에 적용하고자 한다면?  
  해당 템플릿 파일(.html)에 다음과 같이 링크를 걸어주면 된다.

  ```html
  {% load static %}
  
   <link rel="stylesheet" href="{% static 'css/styles.css' %}">
  ```

  - url을 태그를 이용하여 달아주려면 위 처럼 html 파일 제일 상단에 load를 해주어야 한다.

- 위에서 CSS를 반영하고, body 태그 내에 존재하는 특정 태그를 TailwindCSS의 클래스를 이용하여 디자인을 할 경우에는 `npm run css`를 실행할 필요는 없다.

- `npm run css`를 실행하는 경우는 오직 scss 파일을 수정했을 때!