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
import React, { useRef, useEffect } from "react";
import { WebView } from "react-native-webview";

const CityWeatherWebView = ({ cityData }) => {
    const webViewRef = useRef(null);

    // WebView에 날씨 데이터 전송
    const sendWeatherData = (data) => {
        if (webViewRef.current) {
            webViewRef.current.postMessage(
                JSON.stringify({
                    type: "WEATHER_DATA",
                    payload: data,
                })
            );
        }
    };

    // 날씨 데이터가 변경되면 WebView로 전송
    useEffect(() => {
        if (cityData) {
            sendWeatherData(cityData);
        }
    }, [cityData]);

    // WebView에서 메시지를 받을 때 처리
    const handleMessage = (event) => {
        try {
            const message = JSON.parse(event.nativeEvent.data);

            if (message.type === "REQUEST_WEATHER_DATA") {
                // WebView에서 데이터를 요청하면 현재 데이터 전송
                sendWeatherData(cityData);
            }
        } catch (error) {
            console.error("WebView message parsing error:", error);
        }
    };

    return (
        <WebView
            ref={webViewRef}
            source={{ uri: "file:///path/to/web/pages/city.html" }}
            onMessage={handleMessage}
            javaScriptEnabled={true}
            domStorageEnabled={true}
            startInLoadingState={true}
        />
    );
};

// 사용 예시
const App = () => {
    const weatherData = {
        cityName: "강남구",
        cityNameEn: "Gangnam-gu",
        currentWeather: {
            main: {
                temp: 33.24,
                feels_like: 39.77,
                humidity: 59,
                pressure: 1011,
            },
            weather: [
                {
                    description: "맑음",
                    icon: "01d",
                },
            ],
            wind: {
                speed: 3.2,
            },
            visibility: 10000,
        },
        todayMinMax: {
            min: 26.38,
            max: 33.24,
        },
    };

    return <CityWeatherWebView cityData={weatherData} />;
};
```

### 데이터 구조

WebView로 전송하는 날씨 데이터는 다음 구조를 따릅니다:

```javascript
{
    type: 'WEATHER_DATA',
    payload: {
        cityName: '강남구',           // 도시 이름 (한글)
        cityNameEn: 'Gangnam-gu',     // 도시 이름 (영어)
        currentWeather: {
            main: {
                temp: 33.24,           // 현재 온도
                feels_like: 39.77,     // 체감온도
                humidity: 59,          // 습도 (%)
                pressure: 1011         // 기압 (hPa)
            },
            weather: [{
                description: '맑음',   // 날씨 설명
                icon: '01d'            // 날씨 아이콘 코드
            }],
            wind: {
                speed: 3.2             // 풍속 (m/s)
            },
            visibility: 10000          // 가시거리 (미터)
        },
        todayMinMax: {
            min: 26.38,               // 오늘 최저온도
            max: 33.24                // 오늘 최고온도
        }
    }
}
```

### 메시지 통신

WebView와 React Native 간의 양방향 통신:

#### React Native → WebView

```javascript
// 날씨 데이터 전송
webViewRef.current.postMessage(
    JSON.stringify({
        type: "WEATHER_DATA",
        payload: {
            cityName: "강남구",
            currentWeather: {
                /* ... */
            },
            todayMinMax: {
                /* ... */
            },
        },
    })
);

// 에러 전송
webViewRef.current.postMessage(
    JSON.stringify({
        type: "ERROR",
        payload: {
            message: "날씨 데이터를 불러올 수 없습니다",
        },
    })
);
```

#### WebView → React Native

```javascript
// WebView에서 데이터 요청
window.ReactNativeWebView.postMessage(
    JSON.stringify({
        type: "REQUEST_WEATHER_DATA",
    })
);
```

### 이전 버전과의 호환성

기존의 함수 호출 방식도 여전히 지원합니다:

```javascript
// 직접 함수 호출 (이전 방식)
webViewRef.current.injectJavaScript(`
    window.updateWeatherData({
        temp: 35,
        description: '맑음',
        humidity: 60,
        pressure: 1010,
        windSpeed: 3.2,
        visibility: 10.0,
        min: 26,
        max: 35,
        feelsLike: 40,
        icon: 'sunny'
    });
    true;
`);
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
