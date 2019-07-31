# UIImageView

---

>단일 이미지 또는 연속되는 이미지를  인터페이스에 디스플레이 해주는 객체

<br/>

### OverView

- 특정한 `UIImage`  객체를 사용하는 어떠한 이미지를 능률적으로 그릴수 있게 해준다.

- example

- ```
  UIImageView 클래스를 다양한 표준 이미지파일들(PNG,JPEG)의 컨텐트를 그리기위해서 사용할 수있다.
  ```

- 코드 또는 인터페이스빌더로 이미지를 설정할 수 있고, 런타임에서 이미지를 변화시킬 수도 있다.

- animation을 start, stop 하는 메서드를 사용할 수 있다.

  <br/>

  ---

  <br/>

  <br/>

### UnderStanding How Images Are Scaled

- `ImageView`는 `contentMode` 라는 프로퍼티를 사용하여, 어떻게 이미지가 디스플레이 되어 질 지를  스스로 결정합니다.
- 지정된 이미지의 크기와 `ImageView`의 크기가 정확하게 일치하는 것이 가장 좋지만, 그렇지 않다면 `imageView`는 이미지의 비율을 조절한다.
- `ImageView`의 크기가 변하기 된다면 **필요에 따라  이미지의 크기도 같이 변해야할 수 있다.**
- `Cap inset` 이 없는 이미지는` ImageView`의 `contentMode`  홀로 이미지의 표현을 결정한다.



##### What is ContentMode ?🧐

> 뷰의 사이즈가 변할 때, 그들의 컨텐트를 어떻게 적응시킬 지 지정하는 옵션
>
> - `scaleToFil`
>
>   <img width="200" alt="scaleFill" src="https://user-images.githubusercontent.com/39197978/62196543-3588f300-b3b9-11e9-9166-6bf5c9b26576.png" align = left> 
>
>   
>
>   ​	
>
>   
>
>   ​	필요에 따라 이미지의 
>
>   ​	aspect ratio(종횡비)를 바꾸면서
>
>   ​	컨텐트의 사이즈를 맞추는 옵션
>
>   ​	**이미지의 가로세로 비율이 변경되어 보여질 수있다**
>
>   
>
>   
>
>   
>
>   
>
> - `scaleAspectFit`
>
>   <img width="200" alt="aspectFit" src="https://user-images.githubusercontent.com/39197978/62196542-3588f300-b3b9-11e9-9e7f-6669bc2b13b6.png" align = left> 
>
>    	
>
>   
>
>   
>
>   ​	aspect ratio를 유지하면서, 
>
>   ​	컨텐트의 사이즈를 알맞게 사이즈를 맞추는 옵션
>
>   ​	**남는 부분은 투명하게 처리한다**.
>
>   
>
>   
>
>   
>
>   
>
>   
>
> - `scaleAspectFill`
>
>   <img width="200" alt="aspectFill" src="https://user-images.githubusercontent.com/39197978/62196540-3588f300-b3b9-11e9-8bc7-0aa9300d1a1a.png" align = left>	
>
>   
>
>   
>
>   ​	
>
>   ​	Aspect ratio 를 유지하면서, 
>
>   ​	컨텐트를 뷰에 가득 채운다.
>
>   ​	**뷰를 가득 채우기 위해, 컨텐트의 일부가 잘릴 수 있다.**
>
>   
>
>   
>
>   
>
>   
>
>   
>
>   
>
>   ---



### Determining the Final Transparency of the Image

- 이미지는 ImageView의 background 와 합성된 이후, window의 나머지와 합성된다.
- 