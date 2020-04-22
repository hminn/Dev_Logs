# Airbnb-Clone #20-(1)

- #20.0 Sizes in Tailwind
- #20.1 Header part One
- #20.2 Header part Two
- #20.3 Done with Header
- #20.4 Extending Tailwind
- ~~#20.5 Room Cards part One~~
- ~~#20.6 Room Cards part Two~~

---

## [RTFM](https://tailwindcss.com/docs/) : 

---

## > Sizes in Tailwind

### 1. Width

- width에는 두 가지의 사이즈가 존재한다.

  1. %

  2. rem : rem은 root font-size와 크기가 같다. (em은 가장 가까운 font-size와 같다!)
     무슨 말인지는 다음의 코드를 보면 한 번에 이해가 가능하다.

     ```css
     .html{
         font-size : 10px
     }
     
     .box{
         font-size : 20px;
         width : 1rem #여기서 1rem은 10px과 같다.
         .child{
             width : 0.5em; #여기서 0.5em은 10px과 같다. (1em = 20px)
         }
     }
     ```

     - em, rem 은 많은 반응형 디자인 웹사이트들이 사용하는 측정 단위!
     - 우리가 사용하는 단위가 Font-size에 비례하게 되기 때문에 편리하다!

### 2. [Customizin Spacing](https://tailwindcss.com/docs/customizing-spacing/#app) : Sizing Option 추가하기

- Tailwind는 이미 정해져있는 클래스 옵션에 한해서만 설정이 가능하다.

- 이러한 한계를 극복하고자 옵션을 확장시킬 수 있는 기능이 있다.

  ```js
  #[tailwind.config.js]
  
  module.exports = {
  	theme: {
          extend: {
              spacing: {
                  "25vh": "25vh",
                  "50vh": "50vh",
                  "75vh": "75vh",
              }
          }
  	},
  	variants: {},
  	plugins: [],
  };
  ```

  - 이런식으로 Size option 뿐만이 아니라, 색상이나 글꼴  등등 확장이 가능하다.
  - 교체도 가능하지만 확장을 추천!

