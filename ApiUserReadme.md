# 📚 Руководство по использованию Digital Ruble API

## 🎯 Что такое Digital Ruble API?

Digital Ruble API — это интерфейс для взаимодействия с ботом Digital Ruble через веб-запросы. С его помощью вы можете:

- ✅ Проверять баланс пользователей
- ✅ Переводить средства между пользователями
- ✅ Просматривать и покупать NFT
- ✅ Передавать NFT между пользователями
- ✅ Получать информацию о пользователях

---

## 🚀 Быстрый старт

### 1. Получение токена доступа

Для работы с API вам нужен JWT токен. **Для получения токена обратитесь к @lobby на тестовом сервере Telegram.**

Администратор сгенерирует токен через эндпоинт:

```
POST /token/generate
```

**Параметры:**
- `user_id` - ваш ID пользователя
- `admin_key` - ключ администратора (предоставляется админом)

**Пример ответа:**
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user_id": 123456789,
  "permissions": ["read", "transfer"],
  "expires_in": "30 days"
}
```

### 2. Использование токена

Все запросы к API должны содержать заголовок авторизации:

```
Authorization: Bearer YOUR_TOKEN_HERE
```

---

## 💰 Работа с балансом

### Получение баланса пользователя

```
GET /balance/{user_id}
```

**Пример:**
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
     http://localhost:8000/balance/123456789
```

**Ответ:**
```json
{
  "user_id": 123456789,
  "balance": 1500.0,
  "success": true
}
```

---

## 👤 Информация о пользователях

### Получение информации о пользователе

```
GET /user/{user_id}
```

**Ответ:**
```json
{
  "user_id": 123456789,
  "username": "john_doe",
  "wallet": "DRabc123def456",
  "custom_names": ["John", "Doe"],
  "success": true
}
```

---

## 💸 Переводы средств

### Запрос перевода

```
POST /transfer/request
```

**Параметры:**
```json
{
  "from_user_id": 123456789,
  "to_user_id": 987654321,
  "amount": 500.0,
  "comment": "Оплата за услуги"
}
```

**Важно:** Перевод требует подтверждения в боте!

**Ответ:**
```json
{
  "success": true,
  "message": "Запрос на перевод отправлен в бота для подтверждения",
  "transaction_id": "api_1701234567_123456789"
}
```

### Проверка статуса перевода

```
GET /transfer/status/{transaction_id}
```

**Ответ:**
```json
{
  "status": "pending",
  "data": {
    "from_user_id": 123456789,
    "to_user_id": 987654321,
    "amount": 500.0,
    "comment": "Оплата за услуги"
  },
  "success": true
}
```

**Возможные статусы:**
- `pending` - ожидает подтверждения
- `completed` - перевод выполнен
- `not_found` - транзакция не найдена

---

## 🎨 Работа с NFT

### Просмотр доступных NFT

```
GET /nft/list
```

**Ответ:**
```json
{
  "success": true,
  "nfts": [
    {
      "nft_id": 1,
      "name": "NFT Мишка",
      "model": "Bear",
      "price": 8880.0,
      "total_supply": 100,
      "current_supply": 48,
      "available": true,
      "description": "Ограниченная коллекция NFT медведей",
      "gif_path": "Gif/mishka.gif"
    }
  ]
}
```

### Информация о конкретном NFT

```
GET /nft/{nft_id}
```

**Ответ:**
```json
{
  "nft_id": 1,
  "name": "NFT Мишка",
  "model": "Bear",
  "price": 8880.0,
  "total_supply": 100,
  "current_supply": 48,
  "description": "Ограниченная коллекция NFT медведей",
  "gif_path": "Gif/mishka.gif",
  "success": true
}
```

### NFT пользователя

```
GET /user/{user_id}/nfts
```

**Ответ:**
```json
{
  "user_id": 123456789,
  "nfts": [
    {
      "name": "NFT Мишка",
      "model": "Bear",
      "serial_number": 5,
      "nft_id": 1,
      "rarity": null,
      "link": "https://t.me/DigitalRubleBot?start=Bear_5"
    }
  ],
  "success": true
}
```

### Покупка NFT

```
POST /nft/buy
```

**Параметры:**
```json
{
  "user_id": 123456789,
  "nft_id": 1
}
```

**Важно:** Покупка требует подтверждения в боте!

**Ответ:**
```json
{
  "success": true,
  "message": "Запрос на покупку NFT отправлен в бота для подтверждения",
  "transaction_id": "nft_abc123def456"
}
```

### Проверка статуса покупки NFT

```
GET /nft/purchase/status/{transaction_id}
```

---

## 🔄 Передача NFT

### Запрос передачи NFT

```
POST /nft/transfer
```

**Параметры:**
```json
{
  "from_user_id": 123456789,
  "to_user_id": 987654321,
  "nft_id": 1,
  "serial_number": 5
}
```

**Важно:** 
- Только владелец может передать NFT
- Передача требует подтверждения в боте!

**Ответ:**
```json
{
  "success": true,
  "message": "Запрос на передачу NFT отправлен в бота для подтверждения",
  "transaction_id": "nft_transfer_abc123def456"
}
```

### Проверка статуса передачи NFT

```
GET /nft/transfer/status/{transaction_id}
```

---

## 🔧 Проверка здоровья API

### Статус API

```
GET /health
```

**Ответ:**
```json
{
  "status": "healthy",
  "timestamp": 1701234567.123,
  "database": "connected"
}
```

---

## 📋 Полный список эндпоинтов

```
GET  /                                    - Информация об API
GET  /health                              - Статус API
GET  /balance/{user_id}                   - Баланс пользователя
GET  /user/{user_id}                      - Информация о пользователе
GET  /user/{user_id}/nfts                 - NFT пользователя
POST /transfer/request                    - Запрос перевода
GET  /transfer/status/{transaction_id}    - Статус перевода
GET  /nft/list                            - Список NFT
GET  /nft/{nft_id}                        - Информация о NFT
POST /nft/buy                             - Покупка NFT
GET  /nft/purchase/status/{transaction_id} - Статус покупки NFT
POST /nft/transfer                        - Передача NFT
GET  /nft/transfer/status/{transaction_id} - Статус передачи NFT
POST /token/generate                      - Генерация токена (только админ)
```

---

## ⚠️ Важные моменты

### 🔐 Безопасность
- Все запросы требуют валидный JWT токен
- Токены имеют ограниченный срок действия (30 дней)
- Храните токены в безопасном месте

### 🔄 Подтверждения
- Переводы средств требуют подтверждения в боте
- Покупки NFT требуют подтверждения в боте
- Передачи NFT требуют подтверждения в боте

### 💰 Ограничения
- Минимальная сумма перевода: 200 Ц₽
- Нельзя переводить средства самому себе
- Нельзя передавать NFT самому себе

### 🎨 NFT особенности
- Каждый NFT имеет уникальный серийный номер
- CustomName NFT переносят кастомные имена
- Проверяйте доступность NFT перед покупкой

---

## 🛠️ Примеры использования

### Python с requests

```python
import requests

# Настройка
API_URL = "http://localhost:8000"
TOKEN = "your_jwt_token_here"
headers = {"Authorization": f"Bearer {TOKEN}"}

# Получение баланса
response = requests.get(f"{API_URL}/balance/123456789", headers=headers)
balance_data = response.json()
print(f"Баланс: {balance_data['balance']} Ц₽")

# Запрос перевода
transfer_data = {
    "from_user_id": 123456789,
    "to_user_id": 987654321,
    "amount": 500.0,
    "comment": "Тестовый перевод"
}
response = requests.post(f"{API_URL}/transfer/request", 
                        json=transfer_data, headers=headers)
result = response.json()
print(f"ID транзакции: {result['transaction_id']}")
```

### JavaScript с fetch

```javascript
const API_URL = "http://localhost:8000";
const TOKEN = "your_jwt_token_here";

// Получение баланса
async function getBalance(userId) {
    const response = await fetch(`${API_URL}/balance/${userId}`, {
        headers: {
            'Authorization': `Bearer ${TOKEN}`
        }
    });
    const data = await response.json();
    return data.balance;
}

// Запрос перевода
async function requestTransfer(fromId, toId, amount, comment) {
    const response = await fetch(`${API_URL}/transfer/request`, {
        method: 'POST',
        headers: {
            'Authorization': `Bearer ${TOKEN}`,
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            from_user_id: fromId,
            to_user_id: toId,
            amount: amount,
            comment: comment
        })
    });
    return await response.json();
}
```

---

## 🆘 Обработка ошибок

### Коды ошибок

- `401 Unauthorized` - Неверный или истекший токен
- `404 Not Found` - Пользователь/NFT не найден
- `405 Method Not Allowed` - Неверный HTTP метод
- `500 Internal Server Error` - Ошибка сервера

### Пример обработки ошибок

```python
import requests

try:
    response = requests.get(f"{API_URL}/balance/123456789", headers=headers)
    response.raise_for_status()  # Вызовет исключение при ошибке HTTP
    data = response.json()
    print(f"Баланс: {data['balance']} Ц₽")
except requests.exceptions.HTTPError as e:
    if e.response.status_code == 401:
        print("❌ Неверный токен или токен истек")
    elif e.response.status_code == 404:
        print("❌ Пользователь не найден")
    else:
        print(f"❌ Ошибка HTTP: {e}")
except Exception as e:
    print(f"❌ Ошибка: {e}")
```

---

## 📞 Поддержка

Если у вас возникли вопросы или проблемы:

1. Проверьте правильность токена
2. Убедитесь, что API сервер запущен
3. Проверьте формат запросов
4. Обратитесь к администратору системы

---

## 🔄 Обновления API

API может обновляться. Следите за изменениями в документации и обновляйте ваши интеграции при необходимости.

**Версия API:** 1.0.0
**Последнее обновление:** Декабрь 2024 