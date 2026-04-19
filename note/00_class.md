# 00. 클래스 메서드 패턴

# 🔹 1. self (인스턴스 메서드) 패턴

👉 “각 객체가 자기 상태를 다룰 때”

```python
class User:
    def __init__(self, name):
        self.name = name

    def change_name(self, new_name):
        self.name = new_name
```

## ✔️ 언제 쓰냐

* 객체마다 값이 다를 때
* 상태 변경이 필요할 때
* 대부분의 OOP 로직

## 💡 핵심

👉 “이건 **내 객체 하나 기준 작업**”

---

# 🔹 2. @classmethod 패턴

👉 “클래스 전체 기준 로직 or 생성 대체”

```python
class User:
    def __init__(self, name):
        self.name = name

    @classmethod
    def from_email(cls, email):
        name = email.split("@")[0]
        return cls(name)
```

## ✔️ 언제 쓰냐

### 1) 다른 방식 생성 (팩토리 메서드)

```python
User.from_email("kim@gmail.com")
```

### 2) 클래스 변수 다룰 때

```python
class A:
    count = 0

    @classmethod
    def inc(cls):
        cls.count += 1
```

## 💡 핵심

👉 “이건 **객체가 아니라 클래스 기준 작업**”

---

# 🔹 3. @staticmethod 패턴

👉 “논리적으로 클래스 안에 넣은 그냥 함수”

```python
class Math:
    @staticmethod
    def add(a, b):
        return a + b
```

## ✔️ 언제 쓰냐

### 1) 클래스와 관련은 있는데 상태 안 씀

```python
Math.add(3, 5)
```

### 2) 유틸 함수 묶을 때

```python
class StringUtil:
    @staticmethod
    def is_korean(text):
        return any("가" <= c <= "힣" for c in text)
```

## 💡 핵심

👉 “이건 **그냥 함수인데 분류만 클래스 안에 한 것**”
