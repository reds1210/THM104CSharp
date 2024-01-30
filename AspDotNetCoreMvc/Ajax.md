
### XMLHttpRequest

- **GET 請求**

    ```javascript
    var xhr = new XMLHttpRequest();
    xhr.open('GET', 'https://yourapi.com/api/values', true);
    xhr.onload = function () {
        if (xhr.status === 200) {
            console.log(xhr.responseText);
        }
    };
    xhr.send();
    ```

- **POST 請求**

    ```javascript
    var xhr = new XMLHttpRequest();
    xhr.open('POST', 'https://yourapi.com/api/values', true);
    xhr.setRequestHeader('Content-Type', 'application/json');
    xhr.onload = function () {
        if (xhr.status === 201) {
            console.log(xhr.responseText);
        }
    };
    xhr.send(JSON.stringify({ name: 'New Item' }));
    ```

- **PUT 請求**

    ```javascript
    var xhr = new XMLHttpRequest();
    xhr.open('PUT', 'https://yourapi.com/api/values/1', true);
    xhr.setRequestHeader('Content-Type', 'application/json');
    xhr.onload = function () {
        if (xhr.status === 200) {
            console.log(xhr.responseText);
        }
    };
    xhr.send(JSON.stringify({ name: 'Updated Item' }));
    ```

- **DELETE 請求**

    ```javascript
    var xhr = new XMLHttpRequest();
    xhr.open('DELETE', 'https://yourapi.com/api/values/1', true);
    xhr.onload = function () {
        if (xhr.status === 200) {
            console.log('Item deleted');
        }
    };
    xhr.send();
    ```

### Axios

- **GET 請求**

    ```javascript
    axios.get('https://yourapi.com/api/values')
        .then(response => {
            console.log(response.data);
        })
        .catch(error => console.error(error));
    ```

- **POST 請求**

    ```javascript
    axios.post('https://yourapi.com/api/values', { name: 'New Item' })
        .then(response => {
            console.log(response.data);
        })
        .catch(error => console.error(error));
    ```

- **PUT 請求**

    ```javascript
    axios.put('https://yourapi.com/api/values/1', { name: 'Updated Item' })
        .then(response => {
            console.log(response.data);
        })
        .catch(error => console.error(error));
    ```

- **DELETE 請求**

    ```javascript
    axios.delete('https://yourapi.com/api/values/1')
        .then(response => {
            console.log('Item deleted');
        })
        .catch(error => console.error(error));
    ```

### jQuery AJAX

- **GET 請求**

    ```javascript
    $.ajax({
        url: 'https://yourapi.com/api/values',
        type: 'GET',
        success: function (data) {
            console.log(data);
        },
        error: function (error) {
            console.error(error);
        }
    });
    ```

- **POST 請求**

    ```javascript
    $.ajax({
        url: 'https://yourapi.com/api/values',
        type: 'POST',
        contentType: 'application/json',
        data: JSON.stringify({ name: 'New Item' }),
        success: function (data) {
            console.log(data);
        },
        error: function (error) {
            console.error(error);
        }
    });
    ```

- **PUT 請求**

    ```javascript
    $.ajax({
        url: 'https://yourapi.com/api/values/1',
        type: 'PUT',
        contentType: 'application/json',
        data: JSON.stringify({ name: 'Updated Item' }),
        success: function (data) {
            console.log(data);
        },
        error: function (error) {
            console.error(error);
        }
    });
    ```

- **DELETE 請求**

    ```javascript
    $.ajax({
        url: 'https://yourapi.com/api/values/1',
        type: 'DELETE',
        success: function (data) {
            console.log('Item deleted');
        },
        error: function (error) {
            console.error(error);
        }
    });
    ```

### Fetch API

- **GET 請求**

    ```javascript
    fetch('https://yourapi.com/api/values')
        .then(response => response.json())
        .then(data => console.log(data))
        .catch(error => console.error(error));
    ```

- **POST 請求**

    ```javascript
    fetch('https://yourapi.com/api/values', {
        method

: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name: 'New Item' })
    })
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error(error));
    ```

- **PUT 請求**

    ```javascript
    fetch('https://yourapi.com/api/values/1', {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name: 'Updated Item' })
    })
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error(error));
    ```

- **DELETE 請求**

    ```javascript
    fetch('https://yourapi.com/api/values/1', {
        method: 'DELETE'
    })
    .then(response => {
        if (response.ok) {
            console.log('Item deleted');
        }
    })
    .catch(error => console.error(error));
    ```

以上是各種方法對應於不同 HTTP 請求的基本用法。請根據您的實際 API 和業務需求進行調整。例如，處理身份驗證、路由參數、錯誤處理等。
