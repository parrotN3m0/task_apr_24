# Задача. Опрос и голосование
Разработайте программу для проведения простых опросов. Пользователи могут создавать вопросы с вариантами ответов, регистрировать участников, собирать голоса и смотреть статистику.
---
### 1. Класс `Question` (вопрос)

Атрибуты:
- `text` (str) — текст вопроса (например, «Какой язык программирования лучший?»)
- `options` (list) — список строк с вариантами ответов (например, `["Python", "Java", "C++"]`)
- `votes` (dict) — словарь, где ключ — вариант ответа (строка), значение — количество голосов за этот вариант (изначально все 0).

Методы:
- `__init__(self, text, options)` — создаёт вопрос, инициализирует `votes` нулями для каждого варианта.
- `vote(self, option_index)` — увеличивает счётчик голосов для варианта с индексом `option_index` (если индекс корректен). Возвращает `True` при успехе, `False` если индекс вне диапазона.
- `get_results(self)` — возвращает словарь `votes` (копию или сам словарь — на ваше усмотрение).
- `total_votes(self)` — возвращает общее количество проголосовавших.
- `winner(self)` — возвращает строку — вариант ответа, набравший максимум голосов. Если несколько лидеров — возвращает `None`.

---

### 2. Класс `Participant` (участник)

Атрибуты:
- `name` (str) — имя участника
- `email` (str) — контактный email
- `voted_questions` (list) — список ID вопросов (или объектов вопросов), в которых участник уже проголосовал (чтобы нельзя было голосовать дважды за один вопрос).

Методы:
- `__init__(self, name, email)`
- `has_voted_in(self, question_id)` — проверяет, есть ли `question_id` в списке `voted_questions`.
- `add_voted_question(self, question_id)` — добавляет ID вопроса в список.

---

### 3. Класс `Poll` (опросник)

Атрибуты:
- `questions` (list) — список объектов `Question`
- `participants` (list) — список объектов `Participant`
- `next_question_id` (int) — просто счётчик для автоматической нумерации вопросов (или можно использовать индекс в списке).

Методы:
- `__init__(self)`
- `add_question(self, question)` — добавляет вопрос в список вопросов.
- `add_participant(self, participant)` — добавляет участника в список участников.
- `find_participant_by_email(self, email)` — возвращает объект `Participant` или `None`.
- `find_question_by_index(self, index)` — возвращает объект `Question` или `None`.
- `cast_vote(self, participant_email, question_index, option_index)` — выполняет голосование:
  1. Находит участника по email (если нет — вернуть `"Участник не найден"`).
  2. Находит вопрос по индексу (если нет — вернуть `"Вопрос не найден"`).
  3. Проверяет, не голосовал ли участник уже в этом вопросе (если да — вернуть `"Участник уже голосовал в этом вопросе"`).
  4. Вызывает у вопроса метод `vote(option_index)`. Если индекс некорректен — вернуть `"Неверный вариант ответа"`.
  5. Добавляет ID вопроса (или сам вопрос) в список проголосованных вопросов участника.
  6. Возвращает `"Голос принят"`.
- `get_question_statistics(self, question_index)` — возвращает удобную строку со статистикой:
  ```
  Вопрос: Какой язык лучший?
  Python: 3 голоса
  Java: 1 голос
  C++: 0 голосов
  Всего голосов: 4
  Победитель: Python
  ```

---

## Пример использования

```python
# Создаём опросник
poll = Poll()

# Добавляем вопрос
q1 = Question("Какой цвет вам нравится?", ["Красный", "Синий", "Зелёный"])
poll.add_question(q1)

# Добавляем участников
alice = Participant("Алиса", "alice@mail.ru")
bob = Participant("Боб", "bob@mail.ru")
poll.add_participant(alice)
poll.add_participant(bob)

# Голосование
print(poll.cast_vote("alice@mail.ru", 0, 0))   # Алиса голосует за Красный
print(poll.cast_vote("bob@mail.ru", 0, 1))     # Боб за Синий
print(poll.cast_vote("alice@mail.ru", 0, 0))   # повторная попытка — ошибка

# Статистика
print(poll.get_question_statistics(0))
```

---

## Примеры тестов (для самопроверки)

```python
# Тест 1: создание вопроса и подсчёт голосов
q = Question("Тест?", ["A", "B"])
q.vote(0)
q.vote(0)
q.vote(1)
assert q.total_votes() == 3
assert q.winner() == "A"
assert q.get_results() == {"A": 2, "B": 1}
print("Тест 1 пройден")

# Тест 2: некорректное голосование
q2 = Question("Опрос", ["Да", "Нет"])
assert q2.vote(2) == False   # неверный индекс
assert q2.total_votes() == 0
print("Тест 2 пройден")

# Тест 3: участник не может голосовать дважды
poll = Poll()
q3 = Question("Вопрос", ["1", "2"])
poll.add_question(q3)
user = Participant("Иван", "ivan@ya.ru")
poll.add_participant(user)
res1 = poll.cast_vote("ivan@ya.ru", 0, 0)
res2 = poll.cast_vote("ivan@ya.ru", 0, 1)
assert res1 == "Голос принят"
assert "уже голосовал" in res2.lower()
print("Тест 3 пройден")

# Тест 4: поиск победителя при ничьей
q4 = Question("Ничья", ["X", "Y"])
q4.vote(0)
q4.vote(1)
assert q4.winner() is None
print("Тест 4 пройден")
```
