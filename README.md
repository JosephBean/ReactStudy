# React 19

## 선행 학습
> 1. Javascript (ES6+)
> 2. Static Web Service (HTML, CSS)
> 3. Asynchronous (AJAX, Fetch API, AXIOS)

## Install

+ CRA (`npx create-react-app my-app`)

+ VITE (`npm create vite@latest my-app`)
  + `18 > 19` update
  ```cmd
  npm install react@19 react-dom@19
  ```

+ NEXT (`npx create-next-app@latest`)
  + 추가 설치
  ```cmd
  npm install next@latest react@latest react-dom@latest
  ```

## Bootstrap Install
```cmd
npm install bootstrap bootstrap-icons
```

+ `jsx` 추가 하기
```js
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap/dist/js/bootstrap.bundle.min.js';
```

## URI 만들기
```cmd
npm install react-router-dom
```

## 비동기 통기 `AXIOS`
```cmd
npm install axios
```

> POST 요청
```js
axios.post('/')
  .then((response) => console.log(response))
  .catch((error) => console.log(error));
```
