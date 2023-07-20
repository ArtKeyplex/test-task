# 1. Что будет распечатано в следующих случаях
```python
l = list(range(10)) #  0, 1, 2, 3, 4, 5, 6, 7, 8, 9
print(l[2:-4])  # 2, 3, 4, 5
print(l[3:-4:-1])  # 3, 2, 1
print(l[8:-4:-1]) # 8, 7, 6, 5

words = ['world', 'anna', 'bob', 'castle', 'madam', 'refer']
for i in words:
    if i == i[::-1]:
        print(i)
        # anna, bob, madam, refer     

d = {1:'a', 2:'b', 3:'c'}
print({v:k for k, v in d.items()})
# {'a': 1, 'b': 2, 'c': 3}  
```

# 2. Имеется класс Fruit. В runtime экземпляры этого класса создаются и удаляются.
Необходимо сделить за утечками памяти, для чего система мониторинга вызывает метод класса instance_count
Необходимо реализовать этот метод

```python
class Fruit:
    counts = {}

    def __init__(self, name: str):
        self.name = name
        cls = self.__class__
        if cls not in cls.counts:
            cls.counts[cls] = 0
        cls.counts[cls] += 1

    @classmethod
    def instance_count(cls):
        return cls.counts.get(cls, 0)

    def __del__(self):
        cls = self.__class__
        if cls in cls.counts:
            cls.counts[cls] -= 1


class Banana(Fruit):
    pass


f1 = Fruit('Apple')
f2 = Fruit('Orange')
b1 = Banana('Sweet banana')

print(f'Fruit instance count = {Fruit.instance_count()}')    # Fruit instance count = 2
print(f'Banana instance count = {Banana.instance_count()}')  # Banana instance count = 1
del f1
print(f'Fruit instance count = {Fruit.instance_count()}')    # Fruit instance count = 1

```


# 3. Необходимо доработать класс Fruit таким образом, чтобы его можно было итерировать. В качестве коллекции используем имя класса
```python
class Fruit:
    def __init__(self, name: str):
        self.name = name
        self.index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.index >= len(self.name):
            raise StopIteration
        letter = self.name[self.index]
        self.index += 1
        return letter

f1 = Fruit('Apple')
for letter in f1:
    print(letter)  # Выведет в столбик A p p l e


```

# 4. Необходимо доработать класс Fruit таким образом, чтобы можно было использовать его в менеджере контекста
#    Выброшенное исключение нужно перехватить и распечатать его значение
```python
class Fruit:
    def __init__(self, name: str):
        self.name = name

    def __enter__(self):
        return self

    def __exit__(self, type, value, traceback):
        if type is not None:
            print(f'Exception: {value}')
            print('The end')


# client code
with Fruit('Apple') as f1:
    print(f'This is {f1.name}')
    raise Exception('this is exception')

print('The end')


# Prints next 3 lines:
# This is Apple
# Exception: this is exception
# The end
```


# 5. Работы с асинхронностью
## Как запустить корутину monitoring параллельно handle_requests, а потом остановить ее после выполнения handle_requests
```python
import asyncio


async def monitoring():
    while True:
        # do monitoring calls
        await asyncio.sleep(1)


async def handle_requests():
    print('meow')


async def main():
    monitoring_task = asyncio.create_task(monitoring())
    await handle_requests()
    monitoring_task.cancel()

asyncio.run(main())
```
