# 서울 날씨 앱 - 웹 페이지

이 폴더는 React Native 앱의 WebView에서 사용할 웹 페이지들을 포함합니다.

## 파일 구조

```
web/
├── index.html          # 메인 인덱스 페이지
├── css/
│   └── common.css      # 공통 스타일시트
└── pages/
    ├── empty.html      # 빈 페이지
    └── city.html       # 도시 날씨 페이지
```

## 페이지 설명

### 1. 빈 페이지 (`pages/empty.html`)

-   간단한 준비중 메시지를 표시하는 페이지
-   WebView 테스트나 준비중인 기능에 사용

### 2. 도시 날씨 페이지 (`pages/city.html`)

-   서울 각 구의 상세 날씨 정보를 표시
-   URL 파라미터로 도시 지정: `?city=강남구`
-   샘플 데이터 포함 (강남구, 종로구, 중구, 용산구, 성동구)

## 사용 방법

### React Native WebView에서 사용

```javascript
import { WebView } from 'react-native-webview';

// 빈 페이지
<WebView source={{ uri: 'file:///path/to/web/pages/empty.html' }} />

// 도시 페이지
<WebView source={{ uri: 'file:///path/to/web/pages/city.html?city=강남구' }} />
```

### JavaScript 인터페이스

도시 페이지는 React Native에서 호출할 수 있는 함수들을 제공합니다:

```javascript
// 날씨 데이터 업데이트
webViewRef.current.postMessage(
    JSON.stringify({
        action: "updateWeather",
        data: {
            temp: 35,
            description: "맑음",
            humidity: 60,
            // ... 기타 데이터
        },
    })
);

// 도시 정보 업데이트
webViewRef.current.postMessage(
    JSON.stringify({
        action: "updateCity",
        cityName: "강남구",
        cityNameEn: "Gangnam-gu",
    })
);
```

## GitHub Pages 배포

1. 이 `web` 폴더를 GitHub 저장소의 루트에 배치
2. GitHub Pages 설정에서 소스를 `/web` 폴더로 지정
3. 또는 별도의 `gh-pages` 브랜치 생성하여 배포

## 로컬 테스트

```bash
# 간단한 HTTP 서버로 테스트
cd web
python -m http.server 8000
# 또는
npx serve .
```

브라우저에서 `http://localhost:8000`으로 접속하여 테스트할 수 있습니다.

## 커스터마이징

-   `css/common.css`: 스타일 수정
-   `pages/city.html`의 `cityData` 객체: 샘플 데이터 수정
-   새로운 페이지 추가 시 `pages/` 폴더에 HTML 파일 생성
