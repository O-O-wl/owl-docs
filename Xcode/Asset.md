# Asset

> 에셋 카탈로그(Asset Catalog)
>
> 앱 슬라이싱(App Slicing)
>
> 앱 시닝(App Thinning)



###에셋 카탈로그(Asset Catalog)

-   **Asstes.xcassets** 다양한 에셋 관리
- 셋과 다양한 디바이스의 속성에 대한 파일의 연결을(mapping) 통해서 애플리케이션 리소스에 쉽게 접근



**에셋 카탈로그 타입**

에셋 카탈로그의 대표적인 타입과 확장자에 대해 알아봅시다.

| 폴더 타입         | 확장자       | 설명                                         |
| ----------------- | ------------ | -------------------------------------------- |
| App Icon Type     | .appiconset  | 애플리케이션의 아이콘 입니다.                |
| Catalog Type      | .xcassets    | 에셋 카탈로그의 최상위 폴더입니다.           |
| Image Set Type    | .imageset    | 객체들이 사용하는 이미지입니다.              |
| Data Set Type     | .dataset     | 애플리케이션에서 사용되는 데이터 파일입니다. |
| Launch Image Type | .launchimage | 애플리케이션의 실행화면 이미지입니다.        |

- App Icon Type : 다양한 크기와 해상도의 애플리케이션 아이콘 원본 이미지입니다.
- Catalog Type : 에셋 카탈로그 폴더구조의 최상위 폴더입니다. 한 개의 에셋 카탈로그에 하나만 존재할 수 있습니다.
- Image Set Type : 이미지 에셋에서 UIImage와 NSImage의 인스턴스에 사용되는 이미지 파일입니다.
- Data Set Type : 장치 실행 가능 코드(device-executable code)를 제외한 Xcode에 의해 생성된 모든 종류의 데이터를 포함하는 파일들의 집합입니다.
- Launch Image Type : 애플리케이션 실행화면 이미지입니다. (iOS 7.0 이하 버전에만 필요합니다. iOS 8.0 버전 이상은 기본적으로 실행화면 스토리보드(launch screen storyboard)를 사용합니다.)
- 자세한 내용은 아래 링크2.[Type Reference](https://developer.apple.com/library/content/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/AssetTypes.html)를 참조하세요.



### 앱 시닝(App Thinning)

- 애플리케이션이 디바이스에 설치될 때 앱 스토어와 운영체제가 그 디바이스의 특성에 맞게 설치하도록 하는 설치 최적화 기술 ( ex ) 앱스토어에서 다운 받을 시 아이패드,아이폰 각각에 맞는 에셋이 설치



### 앱 슬라이싱(App Slicing)

- 개발자가 애플리케이션의 전체 버전을 iTunes Connect에 업로드하게 되면, 앱 스토어에는 각 디바이스 특성에 다양한 버전의 조각들이 생성
- 사용자의 디바이스의 가장 적합한 조각이 다운로드되어 설치

