# Priklady

## read and filter words from file

```python
import re

file_name = 'thermopylae.txt'

with open(file_name, 'r') as f:
    content = f.read()
    print(content)

    words = re.split(r'\W+', content)
    words.pop()
    print(words)

    words_2_3 = list(filter(lambda e: len(e) == 2 or len(e) == 3, words))
    print(words_2_3)
```



## filter function

```python
vals = [1, 2, 3, -4, 0, -8, -9, -1, 9]

positives = list(filter(lambda e: e > 0, vals))
print(positives)

negatives = list(filter(lambda e: e < 0, vals))
print(negatives)
```

```python
words = ['sky', 'war', 'warm', 'water', 'cup', 'cloud', 'forest']

words_w = list(filter(lambda e: e.startswith('w'), words))
print(words_w)

words_w_c = list(filter(lambda e: e.startswith(('w', 'c')), words))
print(words_w_c)

words_3c = list(filter(lambda e: len(e) == 3, words))
print(words_3c)
```



## map function

```python
def twice(e):
    return e * 2

def cube(e):
    return e * e * e

vals = [1, 2, 3, 4, 5]

vals2 = list(map(twice, vals))
print(vals2)

vals_cubed = list(map(cube, vals))
print(vals_cubed)
```

---

```python
def string_upper(e):
    return e.upper()


words = ['sky', 'blue', 'war', 'forest']
# words_uppered = list(map(string_upper, words))
words_uppered = list(map(lambda e: e.upper(), words))

print(words_uppered)
print(words)
```


## map with lambda

```python
vals = [1, 2, 3, 4, 5]

vals2 = list(map(lambda e: e * 2, vals))
print(vals2)

vals_cubed = list(map(lambda e: e * e * e, vals))
print(vals_cubed)
```



## compute sum of CSV data in file

```python
mysum = 0
file_name = 'data.csv'

with open(file_name, 'r') as f:
    lines = f.readlines()

    for line in lines:
        fields = line.rstrip().split(",")
        for field in fields:
            mysum += int(field)


print(mysum)
```


## compute sum from string of CSV vals

```python
data = """
1,2,3,4,5
6,7,8,9,10
11,12,13,14,15
"""

mysum = 0

lines = data.splitlines()[1:]
# lines.pop(0)
print(lines)

for line in lines:
    fields = line.split(',')

    for field in fields:
        mysum += int(field)

print(mysum)
```


## implicitne argumenty

```python
def power(x, y):
    r = 1

    for i in range(y):
        r = r * x

    return r


print(power(3, 2))
print(power(3, 3), end=' ')
print(power(5, 5), end=' ')
```


## Celsius to fahrenheit

```python
def cel_to_fahr(c):
    return c * 9/5 + 32

f1 = cel_to_fahr(100)
f2 = cel_to_fahr(0)
f3 = cel_to_fahr(30)

print(f1, f2, f3)
```


## keeplines funkcia

```python
data = """
sky
dark
warm
water
cup
ten
rock
"""

lines = data.splitlines()
lines.pop(0)
print(lines)
```


## Filter empty words

```python
data = """
sky
dark
warm

water
cup

ten

rock
"""

fields = data.split('\n')
print(fields)

words = []

for field in fields:
    if field != '':
        words.append(field)

print(words)
```



## input from user

```python
x = int(input('enter first integer value: '))
y = int(input('enter second integer value: '))

print(f'addition of {x} and {y} is {x + y}')
print(f'multiplication of {x} and {y} is {x * y}')
```


## nacitanie slov do zoznamu

```python
words = []

file_name = 'words.txt'

with open(file_name, 'r') as f:
    lines = f.readlines()

    for line in lines:
        cleaned_word = line.strip()
        words.append(cleaned_word)


words.sort()
print(words)
```


## Indexing

```python
mix = (1, 2, 3, (4, 5, 6, (7, 8, 9, (10, 11, 12))))
```


## tuple/list builtins

```python
first = [1, 2, 3]

first_n = tuple(first)
print(first_n, type(first_n))

second = (5, 6, 7)
second_l = list(second)
print(second_l, type(second_l))
```


## Filtering by type

```python
words = []
data = ('sky', 1, 2, True, 'forest', 3.4, 6.7, False, 'water')

for e in data:
    if type(e) == str:
        words.append(e)


print(words)
```


## Floats

```python
# 100 m is 0.1 km

distance = 0.1

# 9.87 s is 9.87/60*60 h

time = 9.87 / 3600

speed = distance / time

print(f"The average speed of a sprinter is {round(speed, 2)} km/h")
print(f"The average speed of a sprinter is {speed} km/h")
```


## Vykonanie operacie x krat

```python
msg = 'hello there!'

i = 1

while i <= 10:
    print(msg)
    i += 1

print('-----------------------')

for i in range(10):
    print(msg)
```


## podmienene vypisovanie slov

`words.txt`:

```
war
sky
cup
cloud
nice
water
warm
```

```python
file_name = 'words.txt'

with open(file_name, 'r') as f:

    lines = f.readlines()
    print(lines)

    for line in lines:

        if line.startswith(('w', 'c')):
            print(line.rstrip())
```


## Opakovanie

```python
# vypis hlasku pomocou fstringu
name = 'John Doe'
age = 34
occupation = 'gardener'

msg = f'{name} is {age} years old and he is a {occupation}'
print(msg)


# vypis sumu
# vypis prvy, posledny prvok
vals = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
print(vals[0])
print(vals[-1])

print(sum(vals))

mysum = 0

for val in vals:
    mysum = mysum + val

print(mysum)


# vypocitaj sumu
data = "1,2,3,4,5,6,7,8,9,10"

mysum2 = 0
fields = data.split(',')

for field in fields:
    mysum2 += int(field)

print(mysum2)
```
