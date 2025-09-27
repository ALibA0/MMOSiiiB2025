# Лабораторная работа №2
## Шифры перестановки и подстановки

**Королев Адам Маратович**  
**Группа: НФИмд-01-25**

## План доклада

- Столбцовая перестановка с ключевым словом - маршрут по столбцам
- Шифрование решеткой Флейснера
- Шифр Виженера по русскому алфавиту без буквы "ё"

## Демонстрационные функции

Ниже удобные функции для показа работы алгоритмов. Они используют реализованные ранее классы:
- ColumnarTransposition
- FleissnerGrille
- VigenereRU


```python
def demo_columnar(text: str, key: str, filler: str = "х"):
    print("== Столбцовая перестановка ==")
    c = ColumnarTransposition(key=key, filler=filler)
    p_norm = normalize_text(text)
    print("Ключ:", c.key)
    print("Исходный:", p_norm)
    enc = c.encrypt(text)
    dec = c.decrypt(enc)
    n_cols = len(c.key)
    print("Таблица по строкам (для наглядности):")
    rows = chunk(pad(p_norm, n_cols, filler), n_cols)
    for r in rows:
        print(" ".join(list(r)))
    print("Шифртекст:", enc)
    print("Обратное преобразование:", dec)

def demo_fleissner(text: str, k: int, filler: str = "х", col_key: str = None):
    print("== Решетка Флейснера ==")
    g = FleissnerGrille(k=k, fill_char=filler)
    p_norm = normalize_text(text)
    print("Размер:", 2*k, "x", 2*k)
    print("Исходный:", p_norm)
    if col_key:
        print("Ключ столбцов:", normalize_text(col_key))
    enc = g.encrypt(text, col_key=col_key)
    dec = g.decrypt(enc, col_key=col_key)
    print("Шифртекст:", enc)
    print("Обратное преобразование:", dec)

def demo_vigenere(text: str, key: str):
    print("== Виженер ==")
    v = VigenereRU(key=key)
    p_norm = normalize_text(text)
    print("Ключ:", v.key)
    print("Исходный:", p_norm)
    enc = v.encrypt(text)
    dec = v.decrypt(enc)
    print("Шифртекст:", enc)
    print("Обратное преобразование:", dec)
```

## Демонстрация - запуск


```python
print()
demo_columnar("Пример для столбцовой перестановки", key="ключ")
print()
demo_fleissner("Пример для решетки", k=3, col_key=None)
print()
demo_vigenere("простейшая проверка", key="пример")
```

## Ожидаемые результаты
На случай если демонстрация не запускается, ниже зафиксированы эталонные выводы.

```
== Столбцовая перестановка ==
Ключ: ключ
Исходный: примердлястолбцовойперестановки
Таблица по строкам (для наглядности):
п р и м
е р д л
я с т о
л б ц о
в о й п
е р е с
т а н о
в к и х
Шифртекст: пеялветвррсборакмлоопсохидтцйени
Обратное преобразование: примердлястолбцовойперестановких

== Решетка Флейснера ==
Размер: 6 x 6
Исходный: примердлярешетки
Шифртекст: прииермерхтедляхкшхххххххххххххххххх
Обратное преобразование: примердлярешеткихххххххххххххххххххх

== Виженер ==
Ключ: пример
Исходный: простейшаяпроверка
Шифртекст: юацэчхшиилфаэтньпр
Обратное преобразование: простейшаяпроверка
```

## Как использовать

Столбцовая перестановка:
```python
c = ColumnarTransposition(key="пароль", filler="я")
cipher = c.encrypt("ваш текст")
back = c.decrypt(cipher)
```

Решетка Флейснера:
```python
g = FleissnerGrille(k=3, fill_char="я")
cipher = g.encrypt("ваш текст", col_key=None)
back = g.decrypt(cipher, col_key=None)
```

Виженер:
```python
v = VigenereRU(key="математика")
cipher = v.encrypt("ваш текст")
back = v.decrypt(cipher)
```

Функции принимают и возвращают только русские буквы. Пробелы и знаки препинания отбрасываются.
