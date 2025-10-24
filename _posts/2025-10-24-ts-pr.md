---
title: "[TS 실습] 프로젝트 모음(투두, 날씨, 블로그 FE, 쇼핑카트, 대시보드, CLI 도구) "
date: 2025-10-24 21:40:00 +0900
categories: [웹 개발, 타입스크립트]
tags: [웹개발, 타입스크립트, TS, 대시보드]
pin: true
math: true
mermaid: true
---

d:\typescript-프로젝트-추천 폴더에 있음

# 할 일 목록(To-Do List) 앱


## React를 사용하지 않은 경우 (Vanilla TypeScript + HTML)

이 방식은 웹의 가장 기본적인 동작 원리를 이해하는 데 도움이 됩니다. TypeScript 코드가 직접 HTML 요소를 찾아내고, 사용자의 입력에 따라 화면을 변경(DOM 조작)하는 '명령형' 프로그래밍 방식입니다.

### 핵심 아이디어:

**상태 관리:** todos라는 배열에 할 일 목록 데이터를 저장합니다.

**DOM 조작:** 사용자가 할 일을 추가하거나 삭제하면, todos 배열을 변경하고 이 배열의 내용에 맞게 HTML 목록을 처음부터 다시 그립니다.

### 1-1. index.html (HTML 구조)

먼저, 앱의 뼈대가 될 HTML 파일을 작성합니다.

```
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vanilla TS Todo List</title>
    <style>
        /* 간단한 스타일링 */
        body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif; background-color: #f4f4f4; padding: 20px; color: #333; }
        .container { max-width: 500px; margin: auto; background: white; padding: 20px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        form { display: flex; gap: 10px; margin-bottom: 20px; }
        input[type="text"] { flex-grow: 1; padding: 10px; border: 1px solid #ddd; border-radius: 4px; font-size: 16px; }
        button { padding: 10px 15px; border: none; background-color: #007bff; color: white; border-radius: 4px; cursor: pointer; }
        button:hover { background-color: #0056b3; }
        ul { list-style-type: none; padding: 0; }
        li { display: flex; align-items: center; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #eee; cursor: pointer; }
        li.completed { text-decoration: line-through; color: #888; }
        .delete-btn { background-color: #dc3545; color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer; }
        .delete-btn:hover { background-color: #c82333; }
    </style>
</head>
<body>
    <div class="container">
        <h1>할 일 목록</h1>
        <form id="todo-form">
            <input type="text" id="todo-input" placeholder="새로운 할 일을 입력하세요" required>
            <button type="submit">추가</button>
        </form>
        <ul id="todo-list"></ul>
    </div>
    <!-- TypeScript를 컴파일한 JavaScript 파일을 불러옵니다 -->
    <script src="main.js"></script>
</body>
</html>
```

### 1-2. main.ts (TypeScript 로직)

이제 HTML을 제어하는 로직을 작성합니다.

```typescript
// 할 일 항목의 타입을 정의합니다.
interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

// 할 일 목록을 저장할 배열 (애플리케이션의 '상태')
let todos: Todo[] = [];

// HTML 요소들을 가져옵니다. 타입 단언(as)을 사용해 TypeScript에게 요소의 타입을 알려줍니다.
const todoForm = document.getElementById('todo-form') as HTMLFormElement;
const todoInput = document.getElementById('todo-input') as HTMLInputElement;
const todoList = document.getElementById('todo-list') as HTMLUListElement;

// 할 일 목록을 화면에 렌더링하는 함수
function renderTodos() {
  // 기존 목록을 비웁니다.
  todoList.innerHTML = '';
  
  // todos 배열의 각 항목에 대해 <li> 요소를 생성합니다.
  todos.forEach(todo => {
    const li = document.createElement('li');
    li.textContent = todo.text;
    if (todo.completed) {
      li.classList.add('completed');
    }

    const deleteBtn = document.createElement('button');
    deleteBtn.textContent = '삭제';
    deleteBtn.classList.add('delete-btn');
    
    // 할 일 텍스트 클릭 시 완료/미완료 토글
    li.addEventListener('click', () => {
      toggleTodo(todo.id);
    });

    // 삭제 버튼 클릭 시 항목 삭제
    deleteBtn.addEventListener('click', (e) => {
      e.stopPropagation(); // 부모 요소(li)의 클릭 이벤트가 실행되지 않도록 함
      deleteTodo(todo.id);
    });

    li.appendChild(deleteBtn);
    todoList.appendChild(li);
  });
}

// 새로운 할 일을 추가하는 함수
function addTodo(text: string) {
  const newTodo: Todo = {
    id: Date.now(), // 간단하게 현재 시간을 ID로 사용
    text: text,
    completed: false,
  };
  todos.push(newTodo);
  renderTodos(); // 목록을 다시 렌더링
}

// 할 일의 완료 상태를 변경하는 함수
function toggleTodo(id: number) {
  todos = todos.map(todo =>
    todo.id === id ? { ...todo, completed: !todo.completed } : todo
  );
  renderTodos();
}

// 할 일을 삭제하는 함수
function deleteTodo(id: number) {
  todos = todos.filter(todo => todo.id !== id);
  renderTodos();
}

// 폼 제출 이벤트 리스너
todoForm.addEventListener('submit', (e) => {
  e.preventDefault(); // 폼의 기본 동작(페이지 새로고침)을 막습니다.
  const newTodoText = todoInput.value.trim();
  if (newTodoText) {
    addTodo(newTodoText);
    todoInput.value = ''; // 입력창 비우기
    todoInput.focus();
  }
});

// 초기 렌더링
renderTodos();
```

**실행 방법:** 이 .ts 파일을 브라우저에서 실행하려면 TypeScript 컴파일러(tsc)를 사용하여 JavaScript 파일로 변환해야 합니다. (tsc main.ts 명령어 실행)


## 2. React와 TypeScript를 사용한 경우

React는 '선언형' UI 라이브러리입니다. DOM을 직접 조작하는 대신, '상태(state)'가 변경되면 화면이 어떻게 보여야 하는지만 정의하면 React가 알아서 효율적으로 화면을 업데이트해 줍니다.

### 핵심 아이디어:

**상태 관리:** useState 훅(Hook)을 사용하여 todos 배열 상태를 관리합니다.

**선언형 UI:** JSX 문법을 사용하여 todos 배열을 화면 요소(<li>)로 변환(mapping)하는 방법을 선언합니다.

**단방향 데이터 흐름:** 사용자의 액션(클릭 등)이 발생하면 상태를 변경하는 함수(setTodos)를 호출하고, 상태가 변경되면 React가 UI를 다시 렌더링합니다.

### TodoApp.tsx (React 컴포넌트)

React 프로젝트 환경에서 아래와 같은 컴포넌트 파일을 작성합니다.

```tsx
import React, { useState, FormEvent, ChangeEvent } from 'react';

// 할 일 항목의 타입을 정의합니다.
interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

const TodoApp: React.FC = () => {
  // useState 훅으로 상태 관리
  const [todos, setTodos] = useState<Todo[]>([]);
  const [inputText, setInputText] = useState<string>('');

  const handleInputChange = (e: ChangeEvent<HTMLInputElement>) => {
    setInputText(e.target.value);
  };

  const handleFormSubmit = (e: FormEvent) => {
    e.preventDefault();
    if (inputText.trim() === '') return;

    const newTodo: Todo = {
      id: Date.now(),
      text: inputText,
      completed: false,
    };
    
    // 상태 업데이트 함수를 사용하여 새로운 todo 추가 (불변성 유지)
    setTodos(prevTodos => [...prevTodos, newTodo]);
    setInputText('');
  };

  const toggleTodo = (id: number) => {
    setTodos(
      todos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };
  
  const deleteTodo = (id: number) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };

  return (
    <div style={{ maxWidth: '500px', margin: 'auto', background: 'white', padding: '20px', borderRadius: '8px', boxShadow: '0 2px 4px rgba(0,0,0,0.1)', fontFamily: 'sans-serif' }}>
      <h1>할 일 목록 (React)</h1>
      <form onSubmit={handleFormSubmit} style={{ display: 'flex', gap: '10px', marginBottom: '20px' }}>
        <input
          type="text"
          value={inputText}
          onChange={handleInputChange}
          placeholder="새로운 할 일을 입력하세요"
          style={{ flexGrow: 1, padding: '10px', border: '1px solid #ddd', borderRadius: '4px', fontSize: '16px' }}
        />
        <button type="submit" style={{ padding: '10px 15px', border: 'none', backgroundColor: '#007bff', color: 'white', borderRadius: '4px', cursor: 'pointer' }}>추가</button>
      </form>
      <ul style={{ listStyleType: 'none', padding: 0 }}>
        {todos.map(todo => (
          <li
            key={todo.id}
            onClick={() => toggleTodo(todo.id)}
            style={{
              display: 'flex',
              alignItems: 'center',
              justifyContent: 'space-between',
              padding: '12px 0',
              borderBottom: '1px solid #eee',
              cursor: 'pointer',
              textDecoration: todo.completed ? 'line-through' : 'none',
              color: todo.completed ? '#888' : '#333'
            }}
          >
            {todo.text}
            <button
              onClick={(e) => {
                e.stopPropagation(); // li의 클릭 이벤트가 실행되지 않도록 함
                deleteTodo(todo.id);
              }}
              style={{ backgroundColor: '#dc3545', color: 'white', border: 'none', padding: '5px 10px', borderRadius: '4px', cursor: 'pointer' }}
            >
              삭제
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default TodoApp;
```

## 두 방식의 핵심 차이점

**DOM 제어권:** Vanilla 방식에서는 개발자가 직접 DOM을 조작해야 하지만, React에서는 React가 가상 DOM(Virtual DOM)을 통해 이 작업을 대신 해 줍니다. 개발자는 '상태'만 신경 쓰면 됩니다.

**코드 구조:** Vanilla 방식은 기능(추가, 삭제, 렌더링)별로 함수가 나뉘는 경향이 있고, React는 관련된 상태와 UI 로직이 하나의 '컴포넌트' 안에 모여있어 재사용과 관리가 더 쉽습니다.

**생산성:** 작은 앱에서는 Vanilla 방식도 충분하지만, 애플리케이션이 복잡해질수록 상태 관리가 어려워집니다. React는 복잡한 UI를 체계적으로 관리할 수 있게 도와주어 생산성을 크게 높여줍니다.

---

# 날씨 정보 앱

날씨 정보 앱'은 비동기 데이터 처리, API 연동, 그리고 받아온 데이터의 타입을 정의하는 방법을 익힐 수 있어 TypeScript 초급자에게 훌륭한 두 번째 프로젝트입니다.
 
이번에도 'React를 사용하지 않은 순수 TypeScript 버전'과 'React를 사용한 버전'으로 나누어, 외부 날씨 API를 호출하는 코드를 작성해 보겠습니다.

**⚠️ API 키 발급 안내**

아래 두 예제 모두 OpenWeatherMap에서 발급하는 무료 API 키가 필요합니다. 회원가입 후 API keys 탭에서 키를 발급받아 코드의 YOUR_API_KEY 부분을 교체해주세요.

## 1. React를 사용하지 않은 경우 (Vanilla TypeScript + HTML)

DOM을 직접 제어하여 API 요청 상태(로딩, 성공, 실패)에 따라 화면을 동적으로 변경하는 방법을 배울 수 있습니다.

### 핵심 아이디어:

**비동기 통신:** fetch와 async/await를 사용하여 외부 API로부터 날씨 데이터를 비동기적으로 가져옵니다.

**타입 정의:** API 응답 데이터의 구조에 맞춰 interface를 선언하여 타입 안정성을 확보합니다.

**상태에 따른 DOM 업데이트:** 로딩 중일 때, 데이터를 성공적으로 받았을 때, 에러가 발생했을 때 각각에 맞춰 HTML 요소를 직접 업데이트합니다.

## 1-1. index.html (HTML 구조)
```
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vanilla TS Weather App</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; background-color: #f0f4f8; display: flex; justify-content: center; align-items: center; min-height: 100vh; margin: 0; }
        .container { width: 90%; max-width: 400px; background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); text-align: center; }
        h1 { color: #333; }
        form { display: flex; gap: 10px; margin-bottom: 20px; }
        input { flex-grow: 1; padding: 12px; border: 1px solid #ddd; border-radius: 8px; font-size: 16px; }
        button { padding: 12px 18px; border: none; background-color: #4a90e2; color: white; border-radius: 8px; cursor: pointer; transition: background-color 0.3s; }
        button:hover { background-color: #357abd; }
        #loading { display: none; color: #555; }
        #error { display: none; color: #d9534f; }
        #weather-result { display: none; }
        #weather-result h2 { margin: 10px 0 5px; color: #4a4a4a; }
        #weather-result p { margin: 5px 0; color: #666; font-size: 1.2rem; }
    </style>
</head>
<body>
    <div class="container">
        <h1>날씨 정보 앱</h1>
        <form id="weather-form">
            <input type="text" id="city-input" placeholder="도시 이름을 입력하세요 (예: Seoul)" required>
            <button type="submit">검색</button>
        </form>
        <div id="loading">불러오는 중...</div>
        <div id="error"></div>
        <div id="weather-result">
            <h2 id="city-name"></h2>
            <p id="temperature"></p>
            <p id="description"></p>
        </div>
    </div>
    <script src="weather.js"></script>
</body>
</html>
```

## 1-2. weather.ts (TypeScript 로직)
```typescript
// OpenWeatherMap API 응답 데이터 구조에 대한 인터페이스
interface WeatherData {
  name: string;
  main: {
    temp: number;
  };
  weather: {
    description: string;
    icon: string;
  }[];
}

const API_KEY = 'YOUR_API_KEY'; // 여기에 발급받은 API 키를 입력하세요.
const form = document.getElementById('weather-form') as HTMLFormElement;
const cityInput = document.getElementById('city-input') as HTMLInputElement;
const loadingDiv = document.getElementById('loading') as HTMLDivElement;
const errorDiv = document.getElementById('error') as HTMLDivElement;
const weatherResultDiv = document.getElementById('weather-result') as HTMLDivElement;
const cityNameEl = document.getElementById('city-name') as HTMLHeadingElement;
const temperatureEl = document.getElementById('temperature') as HTMLParagraphElement;
const descriptionEl = document.getElementById('description') as HTMLParagraphElement;

form.addEventListener('submit', async (e: Event) => {
  e.preventDefault();
  const city = cityInput.value.trim();
  if (!city) return;

  // UI 상태 초기화
  weatherResultDiv.style.display = 'none';
  errorDiv.style.display = 'none';
  loadingDiv.style.display = 'block';

  try {
    const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${API_KEY}&units=metric&lang=kr`);

    if (!response.ok) {
      throw new Error('도시를 찾을 수 없습니다.');
    }

    const data: WeatherData = await response.json();
    displayWeather(data);

  } catch (err) {
    displayError(err instanceof Error ? err.message : '알 수 없는 오류가 발생했습니다.');
  } finally {
    loadingDiv.style.display = 'none';
  }
});

function displayWeather(data: WeatherData) {
  cityNameEl.textContent = data.name;
  temperatureEl.textContent = `온도: ${data.main.temp.toFixed(1)}°C`;
  descriptionEl.textContent = data.weather[0].description;
  weatherResultDiv.style.display = 'block';
}

function displayError(message: string) {
  errorDiv.textContent = message;
  errorDiv.style.display = 'block';
}
```

----

## 2. React와 TypeScript를 사용한 경우

useState 훅을 사용하여 API 요청과 관련된 상태(데이터, 로딩, 에러)를 관리합니다. 상태가 변경되면 React가 자동으로 UI를 다시 렌더링해주므로 DOM을 직접 제어할 필요가 없습니다.

### 핵심 아이디어:

**상태 관리:** weatherData, loading, error라는 세 가지 상태를 useState로 관리합니다.

**선언형 UI:** JSX 내에서 삼항 연산자나 논리 연산자를 사용하여 각 상태에 따라 어떤 UI를 보여줄지 '선언'합니다.

**이벤트 핸들링:** 폼 제출 시 실행될 비동기 함수 안에서 API를 호출하고, 결과에 따라 상태를 업데이트(setWeatherData, setLoading 등)합니다.

### WeatherApp.tsx (React 컴포넌트)
```tsx
import React, { useState, FormEvent } from 'react';

// API 응답 데이터의 타입을 정의합니다.
interface WeatherData {
  name: string;
  main: {
    temp: number;
  };
  weather: {
    description: string;
    icon: string;
  }[];
}

const WeatherApp: React.FC = () => {
  const API_KEY = 'YOUR_API_KEY'; // 여기에 발급받은 API 키를 입력하세요.
  
  const [city, setCity] = useState('');
  const [weatherData, setWeatherData] = useState<WeatherData | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const handleSearch = async (e: FormEvent) => {
    e.preventDefault();
    if (city.trim() === '') return;

    setLoading(true);
    setError(null);
    setWeatherData(null);

    try {
      const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${API_KEY}&units=metric&lang=kr`);
      
      if (!response.ok) {
        throw new Error('도시를 찾을 수 없습니다. 영문으로 입력해보세요.');
      }
      
      const data: WeatherData = await response.json();
      setWeatherData(data);

    } catch (err) {
      setError(err instanceof Error ? err.message : '알 수 없는 오류가 발생했습니다.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div style={{ width: '90%', maxWidth: '400px', margin: 'auto', background: 'white', padding: '25px', borderRadius: '12px', boxShadow: '0 4px 15px rgba(0,0,0,0.1)', textAlign: 'center', fontFamily: 'sans-serif', color: '#333' }}>
      <h1>날씨 정보 앱 (React)</h1>
      <form onSubmit={handleSearch} style={{ display: 'flex', gap: '10px', marginBottom: '20px' }}>
        <input
          type="text"
          value={city}
          onChange={(e) => setCity(e.target.value)}
          placeholder="도시 이름을 입력하세요"
          style={{ flexGrow: 1, padding: '12px', border: '1px solid #ddd', borderRadius: '8px', fontSize: '16px' }}
        />
        <button type="submit" style={{ padding: '12px 18px', border: 'none', backgroundColor: '#4a90e2', color: 'white', borderRadius: '8px', cursor: 'pointer' }}>
          검색
        </button>
      </form>
      
      {loading && <p>불러오는 중...</p>}
      
      {error && <p style={{ color: '#d9534f' }}>{error}</p>}
      
      {weatherData && (
        <div>
          <h2 style={{ margin: '10px 0 5px', color: '#4a4a4a' }}>{weatherData.name}</h2>
          <p style={{ margin: '5px 0', color: '#666', fontSize: '1.2rem' }}>
            온도: {weatherData.main.temp.toFixed(1)}°C
          </p>
          <p style={{ margin: '5px 0', color: '#666', fontSize: '1.2rem' }}>
            {weatherData.weather[0].description}
          </p>
        </div>
      )}
    </div>
  );
};

export default WeatherApp;
```

## 두 방식의 차이점 요약

**UI 업데이트 방식:** Vanilla TS는 document.getElementById로 요소를 찾아 .textContent나 .style.display를 직접 변경하는 명령형 방식입니다. 반면, React는 useState로 상태를 변경하면 UI가 자동으로 다시 그려지는 선언형 방식입니다.

**상태 관리의 복잡성:** 이 예제처럼 간단한 앱에서는 큰 차이가 없지만, 앱이 복잡해질수록 Vanilla 방식은 UI 상태를 일관성 있게 관리하기 어려워집니다. React는 상태가 UI를 결정하는 구조이므로 복잡한 상호작용도 체계적으로 관리할 수 있습니다.

두 가지 버전을 모두 실습해보시면 TypeScript의 타입 시스템이 어떻게 비동기 데이터의 안정성을 높여주고, React와 같은 프레임워크가 UI 개발을 얼마나 편리하게 만들어주는지 명확하게 이해하실 수 있을 겁니다.

---

# 블로그 플랫폼 프런트엔드 

이 프로젝트는 여러 컴포넌트 간의 상태 관리, 데이터 구조화, 그리고 사용자 인터랙션을 종합적으로 다루기 때문에 TypeScript와 React 실력을 한 단계 끌어올리는 데 매우 효과적입니다.

기존 프로젝트 추천 앱에 자연스럽게 통합하여, '블로그 플랫폼' 카드를 클릭하면 데모가 모달(Modal) 창으로 나타나도록 구현해 보겠습니다. 이를 통해 실제 애플리케이션에서 기능을 확장하는 방식도 함께 경험하실 수 있습니다.

다음과 같이 파일들을 추가하고 업데이트하겠습니다.
```
types.ts: Post 데이터 타입을 추가합니다.
constants.tsx: 블로그에 사용할 목업(mockup) 데이터를 추가합니다.
components/Modal.tsx: 재사용 가능한 모달 컴포넌트를 새로 만듭니다.
components/blog/: 블로그 데모를 위한 4개의 새 컴포넌트를 추가합니다.
BlogPlatform.tsx: 블로그 앱의 메인 컴포넌트
PostList.tsx: 포스트 목록
PostDetail.tsx: 포스트 상세 보기
PostForm.tsx: 포스트 작성 폼
App.tsx: 모달의 상태를 관리하고 데모를 실행하는 로직을 추가합니다.
components/ProjectCard.tsx: '데모 실행' 버튼을 추가하여 모달을 열 수 있도록 수정합니다.
```

---

# 실시간 쇼핑 카트

'실시간 쇼핑 카트'는 상태 관리 로직, 특히 배열 데이터를 다루는 기술과 컴포넌트 간의 데이터 흐름을 깊이 있게 연습할 수 있는 중급자에게 안성맞춤인 프로젝트입니다.

이전과 마찬가지로, 기존 프로젝트 추천 앱 내에서 '실시간 쇼핑 카트' 카드의 '데모 실행하기' 버튼을 누르면 모달 창으로 쇼핑 카트 데모가 나타나도록 기능을 확장해 보겠습니다.

다음과 같이 파일을 추가하고 업데이트하여 기능을 구현하겠습니다.

1. types.ts: Product와 CartItem 데이터 타입을 추가합니다.
2. constants.tsx: 쇼핑 카트 데모에서 사용할 상품 목업 데이터를 추가합니다.
3. components/cart/: 쇼핑 카트 데모를 위한 3개의 새 컴포넌트를 추가합니다.
  - ShoppingCart.tsx: 상품 목록과 카트 상태를 모두 관리하는 메인 컴포넌트입니다.
  - ProductList.tsx: 판매 중인 상품 목록을 보여주는 UI 컴포넌트입니다.
  - CartView.tsx: 장바구니에 담긴 상품들과 총액을 보여주는 UI 컴포넌트입니다.
4. App.tsx: 쇼핑 카트 데모 모달의 상태를 관리하고, 이를 열기 위한 로직을 추가합니다.
5. components/ProjectCard.tsx: '실시간 쇼핑 카트' 프로젝트에도 '데모 실행하기' 버튼이 보이도록 조건을 수정합니다.

아래 변경될 전체 코드입니다.
```
types.ts
check_circle
constants.tsx
check_circle
App.tsx
check_circle
components/ProjectCard.tsx
check_circle
components/cart/ShoppingCart.tsx
check_circle
components/cart/ProductList.tsx
check_circle
components/cart/CartView.tsx
```

---

# 데이터 시각화 대시보드

고급 프로젝트인 '데이터 시각화 대시보드'를 구현해 보겠습니다. 이 프로젝트는 TypeScript의 고급 타입을 활용하고, 외부 라이브러리 없이 직접 SVG(Scalable Vector Graphics)를 사용하여 동적인 차트를 렌더링하는 실력을 기를 수 있는 좋은 기회입니다.

기존 애플리케이션에 자연스럽게 통합하여, '데이터 시각화 대시보드' 카드의 '데모 실행하기' 버튼을 클릭하면 모달 창으로 대시보드 데모가 나타나도록 기능을 추가하겠습니다.

다음과 같이 파일을 추가하고 업데이트하여 기능을 구현합니다.

1. types.ts: 대시보드에서 사용할 MonthlySale과 CategoryData 타입을 추가합니다.
2. constants.tsx: 대시보드 데모에 사용할 월별 판매량 및 카테고리별 분포에 대한 목업 데이터를 추가합니다.
3. components/dashboard/: 대시보드 데모를 위한 4개의 새로운 컴포넌트를 추가합니다.
   1. DataDashboard.tsx: 여러 차트와 요약 카드를 포함하는 대시보드의 메인 컴포넌트입니다.
   2. SummaryCard.tsx: 총 매출과 같은 핵심 지표를 보여주는 재사용 가능한 카드 컴포넌트입니다.
   3. SalesLineChart.tsx: 순수 SVG로 월별 매출 추이를 보여주는 라인 차트 컴포넌트입니다.
   4. CategoryPieChart.tsx: 순수 SVG로 카테고리별 매출 분포를 보여주는 파이 차트 컴포넌트입니다.
4. App.tsx: 대시보드 데모 모달의 상태를 관리하고, 이를 열기 위한 로직을 추가합니다.
5. components/ProjectCard.tsx: '데이터 시각화 대시보드' 프로젝트에도 '데모 실행하기' 버튼이 보이도록 조건을 수정합니다.


아래 변경될 전체 코드입니다.
```
types.ts
check_circle
constants.tsx
check_circle
App.tsx
check_circle
components/ProjectCard.tsx
check_circle
components/dashboard/DataDashboard.tsx
check_circle
components/dashboard/SummaryCard.tsx
check_circle
components/dashboard/SalesLineChart.tsx
check_circle
components/dashboard/CategoryPieChart.tsx
check_circle
```

---

# 타입스크립트 기반 CLI 도구

실제 CLI(Command Line Interface) 도구는 Node.js 환경에서 동작하여 파일 시스템에 직접 접근하지만, 브라우저 환경에서는 보안상의 이유로 이것이 불가능합니다. 따라서 이번 데모에서는 브라우저 내에서 실제 터미널처럼 동작하는 시뮬레이터를 만들어, CLI 도구의 핵심 로직과 타입 구조를 경험할 수 있도록 구현하겠습니다.

이 데모는 가상 파일 시스템을 상태로 관리하고, 사용자 입력을 파싱하여 명령을 실행하는 복잡한 상호작용을 다루므로 고급 TypeScript 활용 능력을 기르는 데 큰 도움이 될 것입니다.

다음과 같이 파일을 추가하고 업데이트하여 기능을 구현합니다.

1. types.ts: 가상 파일 시스템의 구조를 정의하기 위한 FileSystemNode 타입을 추가합니다.
2. constants.tsx: CLI 데모에서 사용할 가상 파일 시스템 목업 데이터를 추가합니다.
3. components/cli/: CLI 데모를 위한 새로운 컴포넌트를 추가합니다.
   1. CliTool.tsx: 터미널 UI, 명령어 입력, 출력 히스토리, 가상 파일 시스템 상태를 모두 관리하는 메인 컴포넌트입니다.
4. App.tsx: CLI 도구 데모 모달의 상태를 관리하고, 이를 열기 위한 로직을 추가합니다.
5. components/ProjectCard.tsx: '타입스크립트 기반 CLI 도구' 프로젝트에도 '데모 실행하기' 버튼이 보이도록 조건을 수정합니다.
   
아래는 변경될 전체 코드입니다.
```
types.ts
check_circle
constants.tsx
check_circle
App.tsx
check_circle
components/ProjectCard.tsx
check_circle
components/cli/CliTool.tsx
check_circle
```

