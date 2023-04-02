# Websocket

![https://www.koskila.net/wp-content/uploads/2021/04/gifmachine-21.gif](https://www.koskila.net/wp-content/uploads/2021/04/gifmachine-21.gif)

WebSocket - протокол связи поверх TCP-соединения, предназначенный для обмена сообщениями между браузером и веб-сервером, используя постоянное соединение.

### Разница между HTTP и Websocket

![https://assets.htmlacademy.ru/content/blog/1317/09@1x.png](https://assets.htmlacademy.ru/content/blog/1317/09@1x.png)

HTTP — это протокол клиент-серверного взаимодействия, который используется для передачи данных между клиентом и сервером. WebSocket — это более новый протокол, который позволяет установить постоянное соединение между клиентом и сервером, чтобы обеспечить более эффективную передачу данных.

WebSocket обеспечивает более низкую задержку и более быстрый обмен данными, чем HTTP. Он также позволяет серверу отправлять сообщения на клиентскую сторону без необходимости запроса от клиента. Несмотря на это, HTTP все еще является более распространенным протоколом и может использоваться для большинства задач, которые требуют передачи данных.

### Базовое использование на фронте

Для использования WebSocket на фронте, нужно создать объект WebSocket, указав адрес сервера WebSocket, к которому вы хотите подключиться. Затем вы можете добавить обработчики событий onopen, onmessage, onclose и onerror для управления соединением и обменом данными.

Пример использования:

```
const socket = new WebSocket('ws://localhost:8080');

socket.onopen = function() {
  console.log('Соединение установлено');
};

socket.onmessage = function(event) {
  console.log(`Получено сообщение: ${event.data}`);
};

socket.onclose = function(event) {
  console.log('Соединение закрыто');
};

socket.onerror = function(error) {
  console.log(`Ошибка: ${error.message}`);
};

```

### Базовое использование на сервере на node.js

Для использования WebSocket на сервере с помощью node.js нужно установить пакет ws и создать экземпляр WebSocket-сервера, указав порт, на котором он будет слушать входящие соединения. Затем вы можете добавить обработчики событий on('connection'), on('message'), on('close') и on('error') для управления соединением и обменом данными.

Пример использования:

```
const WebSocket = require('ws');

const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', function connection(ws) {
  console.log('Соединение установлено');

  ws.on('message', function incoming(message) {
    console.log(`Получено сообщение: ${message}`);
  });

  ws.on('close', function close() {
    console.log('Соединение закрыто');
  });
});

```

### Какие библиотеки на фронте используются вместо нативного Websocket?

На фронте часто используются библиотеки [Socket.IO](http://socket.io/) и SockJS вместо нативного WebSocket. Они предоставляют дополнительные возможности, такие как автоматическое переподключение, поддержку старых браузеров и механизмы безопасности, что делает их более удобными для использования в реальных проектах.

```jsx
const socket = io('<http://localhost:8080>');

socket.on('connect', function() {
  console.log('Соединение установлено');
});

socket.on('message', function(data) {
  console.log(`Получено сообщение: ${data}`);
});

socket.on('disconnect', function() {
  console.log('Соединение закрыто');
});

socket.on('error', function(error) {
  console.log(`Ошибка: ${error}`);
});

```

### Базовый пример использования [Socket.IO](http://socket.io/)  в react проектах

```jsx
import React, { useState, useEffect } from 'react';
import io from 'socket.io-client';

const socket = io('<http://localhost:8080>');

function App() {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    socket.on('message', (data) => {
      setMessages((prevMessages) => [...prevMessages, data]);
    });
  }, []);

  const handleSubmit = (event) => {
    event.preventDefault();
    const message = event.target.elements.message.value;
    socket.emit('message', message);
    event.target.elements.message.value = '';
  };

  return (
    <div>
      <ul>
        {messages.map((message, index) => (
          <li key={index}>{message}</li>
        ))}
      </ul>
      <form onSubmit={handleSubmit}>
        <input type="text" name="message" />
        <button>Отправить</button>
      </form>
    </div>
  );
}

export default App;

```

Этот пример использует библиотеку [Socket.IO](http://socket.io/) для установления соединения с сервером WebSocket и отправки и получения сообщений. Он использует хук `useState` для отслеживания списка сообщений и хук `useEffect` для подписки на событие `message` и добавления новых сообщений в список. Также он использует `socket.emit` для отправки сообщений на сервер и обработчик события `onSubmit` для обработки отправки сообщений из формы.

### Базовый пример использования [Socket.IO](http://socket.io/)  в react проектах с redux

Для использования `Socket.IO` с `redux` необходимо установить пакеты `socket.io-client` и `redux-thunk`. Затем необходимо создать экземпляр `Socket.IO` и передать его в функцию `thunk`, которая будет обрабатывать соединения и обмен данными.

Пример использования:

```
import io from 'socket.io-client';

const socket = io('<http://localhost:8080>');

export const CONNECT_SOCKET = 'CONNECT_SOCKET';
export const DISCONNECT_SOCKET = 'DISCONNECT_SOCKET';
export const RECEIVE_MESSAGE = 'RECEIVE_MESSAGE';

export const connectSocket = () => ({
  type: CONNECT_SOCKET,
});

export const disconnectSocket = () => ({
  type: DISCONNECT_SOCKET,
});

export const receiveMessage = (message) => ({
  type: RECEIVE_MESSAGE,
  payload: message,
});

export const sendMessage = (message) => (dispatch) => {
  socket.emit('message', message);
};

export const startListening = () => (dispatch) => {
  dispatch(connectSocket());

  socket.on('connect', () => {
    console.log('Соединение установлено');
  });

  socket.on('message', (data) => {
    dispatch(receiveMessage(data));
  });

  socket.on('disconnect', () => {
    dispatch(disconnectSocket());
    console.log('Соединение закрыто');
  });
};

```

Этот пример находится в файле `actions.js` и определяет четыре действия: `CONNECT_SOCKET`, `DISCONNECT_SOCKET`, `RECEIVE_MESSAGE` и `sendMessage`. Функция `startListening` является функцией `thunk`, которая обрабатывает соединения и обмен данными. Она подписывается на события `connect`, `message` и `disconnect` и диспетчит соответствующие действия в зависимости от события.

Для использования этого примера в `redux`, необходимо определить редьюсеры, которые будут обрабатывать каждое действие. Кроме того, необходимо использовать хуки `useSelector` и `useDispatch` в компонентах, чтобы связать состояние и действия с `redux`.

```
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { startListening, sendMessage } from './actions';

function App() {
  const dispatch = useDispatch();
  const messages = useSelector((state) => state.messages);

  useEffect(() => {
    dispatch(startListening());
  }, [dispatch]);

  const handleSubmit = (event) => {
    event.preventDefault();
    const message = event.target.elements.message.value;
    dispatch(sendMessage(message));
    event.target.elements.message.value = '';
  };

  return (
    <div>
      <ul>
        {messages.map((message, index) => (
          <li key={index}>{message}</li>
        ))}
      </ul>
      <form onSubmit={handleSubmit}>
        <input type="text" name="message" />
        <button>Отправить</button>
      </form>
    </div>
  );
}

export default App;

```

Этот пример использует хук `useSelector` для выбора списка сообщений из состояния `redux` и хук `useDispatch` для диспетчинга действий в `redux`. Он также использует обработчик события `onSubmit` для отправки сообщений на сервер.

### Базовый пример использования [Socket.IO](http://socket.io/)  в react проектах с redux и saga

Для использования `Socket.IO` с `redux-saga` необходимо установить пакеты `socket.io-client` и `redux-saga`. Затем необходимо создать экземпляр `Socket.IO` и передать его в функцию генератор, которая будет обрабатывать соединения и обмен данными.

Пример использования:

```
import io from 'socket.io-client';
import { put, takeEvery } from 'redux-saga/effects';

const socket = io('<http://localhost:8080>');

export const CONNECT_SOCKET = 'CONNECT_SOCKET';
export const DISCONNECT_SOCKET = 'DISCONNECT_SOCKET';
export const RECEIVE_MESSAGE = 'RECEIVE_MESSAGE';

export const connectSocket = () => ({
  type: CONNECT_SOCKET,
});

export const disconnectSocket = () => ({
  type: DISCONNECT_SOCKET,
});

export const receiveMessage = (message) => ({
  type: RECEIVE_MESSAGE,
  payload: message,
});

export function* sendMessage(action) {
  yield socket.emit('message', action.payload);
}

export function* startListening() {
  yield put(connectSocket());

  socket.on('connect', () => {
    console.log('Соединение установлено');
  });

  socket.on('message', (data) => {
    yield put(receiveMessage(data));
  });

  socket.on('disconnect', () => {
    yield put(disconnectSocket());
    console.log('Соединение закрыто');
  });
}

export function* watchSendMessage() {
  yield takeEvery('SEND_MESSAGE', sendMessage);
}

```

Этот пример находится в файле `actions.js` и определяет четыре действия: `CONNECT_SOCKET`, `DISCONNECT_SOCKET`, `RECEIVE_MESSAGE` и `sendMessage`. Функция `startListening` является функцией генератором, которая обрабатывает соединения и обмен данными. Она подписывается на события `connect`, `message` и `disconnect` и диспетчит соответствующие действия в зависимости от события. Функция `watchSendMessage` является функцией генератором, которая отслеживает отправку сообщений и вызывает функцию генератор `sendMessage`.

Для использования этого примера в `redux-saga`, необходимо определить редьюсеры, которые будут обрабатывать каждое действие. Кроме того, необходимо использовать хуки `useSelector` и `useDispatch` в компонентах, чтобы связать состояние и действия с `redux`.

```
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { startListening, sendMessage } from './actions';

function App() {
  const dispatch = useDispatch();
  const messages = useSelector((state) => state.messages);

  useEffect(() => {
    dispatch(startListening());
  }, [dispatch]);

  const handleSubmit = (event) => {
    event.preventDefault();
    const message = event.target.elements.message.value;
    dispatch(sendMessage(message));
    event.target.elements.message.value = '';
  };

  return (
    <div>
      <ul>
        {messages.map((message, index) => (
          <li key={index}>{message}</li>
        ))}
      </ul>
      <form onSubmit={handleSubmit}>
        <input type="text" name="message" />
        <button>Отправить</button>
      </form>
    </div>
  );
}

export default App;

```

Этот пример использует хук `useSelector` для выбора списка сообщений из состояния `redux` и хук `useDispatch` для диспетчинга действий в `redux`. Он также использует обработчик события `onSubmit` для отправки сообщений на сервер.

### Базовый пример использования [Socket.IO](http://socket.io/)  в react проектах с redux и **RTK Query**

```jsx
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';
import { io } from 'socket.io-client';

const socket = io('http://localhost:3001');

const messageApi = createApi({
  reducerPath: 'messageApi',
  baseQuery: fetchBaseQuery({ baseUrl: '/' }),
  endpoints: (builder) => ({
    getMessage: builder.query({
      query: () => 'getMessage',
    }),
    sendMessage: builder.mutation({
      // Определяем функцию, которая будет отправлять сообщение на сервер
      query: (message) => {
        // Отправляем сообщение через сокет
        socket.emit('sendMessage', { message });
      },
      // Необязательный колбэк-функция, которая вызывается после успешной отправки сообщения
      onQueryStarted: () => {
        console.log('Sending message...');
      },
    }),
  }),
});

socket.on('connect', () => {
  console.log('connected to server');
});

socket.on('newMessage', (data) => {
  messageApi.endpoints.getMessage.invalidate(); // Обновляем данные RTK Query при получении новых сообщений
});

export const { useGetMessageQuery, useSendMessageMutation } = messageApi;

```

### Собственные события

Кроме стандартных событий(open, message, error, close) вы можете определять собственные события. В протоколе WebSocket есть механизм называемый "пользовательские события" (custom events), которые позволяют отправлять данные определенного типа. 

Пример на клиенте:

```jsx
import { io } from 'socket.io-client';

const socket = io('http://localhost:3001');

socket.on('connect', () => {
  console.log('connected to server');
});

socket.on('listMessage', (data) => {
  console.log(`received listMessage: ${data}`);
});

// Отправка события listMessage
socket.emit('listMessage', 'Hello from the client!');

```

Пример на сервере:

```jsx
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = new Server(server);

io.on('connection', (socket) => {
  console.log(`Socket ${socket.id} connected`);

  socket.on('listMessage', (data) => {
    console.log(`Received message: ${data}`);
    socket.emit('listMessage', `Server says: ${data}`);
  });

  socket.on('disconnect', () => {
    console.log(`Socket ${socket.id} disconnected`);
  });
});

server.listen(3001, () => {
  console.log('Server is listening on port 3001');
});

```

В данном примере клиент отправляет событие **`listMessage`**
 на сервер, и сервер принимает его и отправляет всем клиентам в качестве ответа новое событие **`listMessage`**
 с префиксом **`Server:`**
. Клиент также подписывается на событие **`listMessage`**
 и выводит в консоль полученное сообщение.

### Какие ошибки чаще всего допускают react разработчики при работе с websocket?

При работе с websocket в React приложениях, разработчики могут допустить ряд ошибок, включая:

1. Не закрытие соединения: неправильная обработка события "onclose", когда соединение закрывается, может привести к утечке ресурсов и потере памяти.
2. Необработанные ошибки: при отправке сообщений через WebSocket может возникнуть ошибка, которая не будет обработана, что может привести к падению приложения.
3. Проблемы с конфигурацией соединения: неправильно настроенное соединение может привести к сбоям, утечке памяти или ненужным повторным подключениям.
4. Некорректное управление состоянием: необходимо корректно управлять состоянием компонента в зависимости от состояния соединения WebSocket, чтобы избежать ошибок и непредсказуемого поведения.
5. Проблемы с безопасностью: необходимо учитывать возможность атак на WebSocket, такие как межсайтовая подделка запроса (CSRF), атаки на маршрутизацию (routing attacks) и другие уязвимости безопасности.

В целом, при работе с WebSocket необходимо тщательно обрабатывать все возможные ошибки и учитывать потенциальные угрозы безопасности, чтобы обеспечить надежность и безопасность приложения.

### Плюсы и минусы Websocket?

Плюсы:

1. Двусторонняя связь: WebSocket обеспечивает полнодуплексную двустороннюю связь между клиентом и сервером, что позволяет как клиенту, так и серверу инициировать отправку сообщений в любой момент времени. Это делает WebSocket очень эффективным для реализации приложений, которым нужно быстро реагировать на изменения данных и взаимодействовать с пользователями в режиме реального времени.
2. Низкая задержка: WebSocket обеспечивает быструю и эффективную связь между клиентом и сервером. Он работает на основе протокола TCP, который обеспечивает низкую задержку и малое количество потерь данных при передаче.
3. Масштабируемость: WebSocket может обрабатывать большое количество одновременных подключений, что делает его идеальным для создания масштабируемых приложений, таких как онлайн-игры, чат-приложения и многие другие.
4. Безопасность: WebSocket поддерживает защищенное соединение SSL/TLS, которое обеспечивает безопасную передачу данных между клиентом и сервером. Кроме того, WebSocket обеспечивает защиту от атак типа XSS и CSRF.
5. Простота использования: WebSocket легко интегрируется с различными языками программирования и платформами, что делает его доступным для широкого круга разработчиков. Кроме того, многие фреймворки и библиотеки, такие как Socket.IO для Node.js и SignalR для .NET, предоставляют простой и удобный интерфейс для работы с WebSocket.

Минусы

- Не поддерживается всеми браузерами
- Требует дополнительной настройки на стороне сервера для поддержки постоянного соединения
- Более высокая нагрузка на сервер в сравнении с HTTP при передаче большого количества данных
