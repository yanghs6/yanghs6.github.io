---
date: 2023-01-21 11:55:00 +/-0900
title: "[Python] Dictionary"
categories: [Develop, python]
tags: [개발(develop), 파이썬(python), 딕셔너리(dictionary), 팁(tip)]

---
## 개요
안녕하세요.

이번 글에서는 Python의 Dictionary에 대해 간단히 살펴보겠습니다.

---
## 딕셔너리의 개념

### 파이썬의 딕셔너리란?

파이썬의 딕셔너리는 파이썬의 기본 자료형 중 하나입니다.

[해시 게시글]({% post_url 2023-01-13-1001_hash_basic %})에서 이야기한 해시테이블 자료구조가 바로 딕셔너리입니다.

### 딕셔너리가 필요한 이유

짝을 이뤄 표현 혹은 연산이 필요하다면 딕셔너리를 가장 먼저 떠올리게 됩니다.

예를 들어 시험 성적을 저장한다고 해봅시다.

```python
# 1. List
list_grade = [90, 100, 80, 70]      # English, Math, Art, Music

# 2. Dict
dict_grade = {"English":90,"Math":100,"Art":80,"Music":70}
```

위의 경우 어떠한 순서로 저장되었는지에 대한 정보가 필수적입니다. 

또한 혹시라도 순서가 바뀌게 된다면 시험의 점수가 모두 섞이게 됩니다.

아래의 경우 시험의 이름을 키, 시험의 점수를 값으로 하는 딕셔너리를 사용했습니다.

이제는 어떤 순서로 저장했는지 또는 순서가 바뀌었는지 등에 대해 자유로워졌습니다!

### 딕셔너리의 특징

#### 1. 키는 유일하다

1개의 키는 1개의 값을 가집니다.

따라서 중복된 키로 무언가를 저장한다면 가장 최신의 값만 유지됩니다.

#### 2. 키에는 변경이 불가능한 모든 객체가 올 수 있다

키로 설정할 수 있는 종류로는 숫자, 문자, 변수, 심지어는 클래스나 함수도 키로 설정할 수 있습니다.

하지만 list, set, dict 등은 키로 설정할 수 없습니다.

#### 3. 조회는 빠르지만, 추가나 삭제는 느리다

딕셔너리는 키만 있다면 해당 키의 값을 빠르게 조회합니다.

반면에 새로운 키와 값을 추가하거나 삭제에는 비교적 시간이 필요합니다.

---

## 간단한 사용법

### ① 생성하기

```python
# 1. {, } 기호 사용
dict_animal = {"name":"blue", "age":5, "species":"cat"}

# 2. dict() 함수 사용
dict_animal = dict(name="blue", age=5, species="cat")
```

### ② 접근하기

```python
# 1. dict 생성
dict_animal = {"name":"blue", "age":5, "species":"cat"}

# 2. 접근
print(dict_animal)                  # {'name': 'blue', 'age': 5, 'species': 'cat'}
print(dict_animal["name"])          # blue
print(dict_animal["age"])           # 5

# 3. 키만, 값만, 키와 값의 쌍만 접근
print(dict_animal.keys())           # dict_keys(['name', 'age', 'species'])
print(dict_animal.values())         # dict_values(['blue', 5, 'cat'])
print(dict_animal.items())          # dict_items([('name', 'blue'), ('age', 5), ('species', 'cat')])

# 4. 값 수정
print(dict_animal["age"])           # 5
dict_animal["age"] = 10
print(dict_animal["age"])           # 10
```

### ③ 기타 매소드 사용해보기

```python
dict_animal = {"name":"blue", "age":5, "species":"cat"}

# get(키)                   : 해당하는 값 가져오기
print(dict_animal.get("name"))
print(dict_animal)

# pop(키)                   : 해당하는 값 가져오고 해당 쌍 제거하기
print(dict_animal.pop("name"))
print(dict_animal)

# popitem(키)               : 해당하는 키와 값 가져오고 해당 쌍 제거하기
print(dict_animal.popitem())
print(dict_animal)

# clear()                   : dict 초기화
dict_animal = {"name":"blue", "age":5, "species":"cat"}
dict_animal.clear()
print(dict_animal)

# update()                  : 전달한 딕셔너리의 값으로 업데이트
dict_animal = {"name":"blue", "age":5, "species":"cat"}
dict_animal.update({"name":"cute", "color":"darkblue"})
print(dict_animal)

# copy()                    : 딕셔너리의 얕은 복사
dict_animal2 = dict_animal.copy()
print(dict_animal2)

# fromkeys(iterable, 값)    : 하나의 값을 가지는 딕셔너리 생성
dict_animal3 = dict.fromkeys(["name", "age", "species"], "Not Set")
print(dict_animal3)
```

### ④ 조건문, 반복문 사용해보기

```python
# 1. dict 생성
dict_animal = {"name":"blue", "age":5, "species":"cat"}

# 2. 조건문 사용
if "name" in dict_animal:
    print(dict_animal["name"])

if dict_animal["age"] > 5:
    print("Old")
else:
    print("Young")

# 3. 반복문 사용
for key in dict_animal.keys():
    print(key)

for key, value in dict_animal.items():
    print(key, value)
```

---
## 마무리하며

이 글에서는 파이썬의 딕셔너리가 무엇이고 어떻게 사용되는지 간단하게 살펴보았습니다.

조언과 댓글은 언제나 환영입니다.

감사합니다. 😀

---
## 참고 문헌
- Mapping Types — dict, [https://docs.python.org/3.11/library/stdtypes.html?highlight=dict#mapping-types-dict](https://docs.python.org/3.11/library/stdtypes.html?highlight=dict#mapping-types-dict)
