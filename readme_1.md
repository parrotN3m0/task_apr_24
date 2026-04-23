
# Задача. Корзина покупок

Нужно реализовать три класса, которые умеют создавать товары, добавлять их в корзину, оформлять заказ с подсчётом итоговой суммы.


---


### 1. Класс `Product` (товар)

Атрибуты:
- `name` (str) — название товара
- `price` (float) — цена
- `product_id` (int) — уникальный номер товара

Методы:
- `__init__(self, name, price, product_id)`
- `get_info(self)` — возвращает строку `"Товар: X, цена: Y руб."`

---

### 2. Класс `Cart` (корзина)

Атрибуты:
- `items` (list) — список товаров (объектов `Product`), которые добавили в корзину.

Методы:
- `__init__(self)`
- `add_product(self, product)` — добавляет товар в конец списка `items`.
- `remove_product(self, product_id)` — удаляет из списка **первый** товар с указанным `product_id`. Если такого нет — ничего не делает.
- `total_price(self)` — возвращает сумму цен всех товаров в корзине.
- `clear(self)` — очищает корзину (делает `items` пустым списком).

---

### 3. Класс `Order` (заказ)

Атрибуты:
- `order_id` (int) — номер заказа
- `cart` (объект `Cart`) — корзина, из которой формируется заказ
- `customer_name` (str) — имя покупателя
- `is_paid` (bool) — оплачен заказ или нет (по умолчанию `False`)

Методы:
- `__init__(self, order_id, cart, customer_name)`
- `get_total(self)` — возвращает общую сумму заказа (из корзины) — просто вызывает `cart.total_price()`.
- `pay(self)` — меняет `is_paid` на `True` и возвращает строку `"Заказ №X оплачен на сумму Y руб."`.
- `receipt(self)` — возвращает строку-чек с перечнем товаров, общей суммой и статусом оплаты.  
  Пример:
  ```
  Заказ №123
  Покупатель: Анна
  ---
  1. Яблоки - 50 руб.
  2. Молоко - 80 руб.
  ---
  Итого: 130 руб.
  Оплачен: Да
  ```

---

## 📦 Пример использования

```python
# Создаём товары
apple = Product("Яблоки", 50, 1)
milk = Product("Молоко", 80, 2)

# Создаём корзину
cart = Cart()
cart.add_product(apple)
cart.add_product(milk)
cart.add_product(apple)   # можно добавить ещё раз

# Создаём заказ из этой корзины
order = Order(1, cart, "Анна")
print(order.get_total())  # 180

# Оплачиваем
print(order.pay())        # "Заказ №1 оплачен на сумму 180 руб."

# Получаем чек
print(order.receipt())
```

---

##  Примеры тестов (для самопроверки)


```python
# Тест 1: товар и корзина
p1 = Product("Ручка", 30, 1)
p2 = Product("Тетрадь", 50, 2)
cart = Cart()
cart.add_product(p1)
cart.add_product(p2)
assert cart.total_price() == 80
cart.add_product(p1)
assert cart.total_price() == 110
print("Тест 1 пройден")

# Тест 2: удаление товара из корзины
cart.remove_product(1)  # удаляет одну ручку
assert cart.total_price() == 80
cart.remove_product(99)  # нет такого — ничего не меняется
assert cart.total_price() == 80
print("Тест 2 пройден")

# Тест 3: очистка корзины
cart.clear()
assert cart.total_price() == 0
assert len(cart.items) == 0
print("Тест 3 пройден")

# Тест 4: заказ и оплата
cart = Cart()
cart.add_product(Product("Хлеб", 40, 3))
order = Order(10, cart, "Петр")
assert order.get_total() == 40
assert order.is_paid == False
order.pay()
assert order.is_paid == True
assert "оплачен" in order.receipt().lower()
print("Тест 4 пройден")
```
