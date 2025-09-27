## Королев Адам Маратович  
## НФИмд-01-25  
## Лабораторная работа № 1

Реализация шифра Цезаря с произвольным ключом 'k'.  
Реализация шифра Атбаш.  
Готовые функции, тесты и наглядные таблицы соответствий.


Алфавиты, с которыми будем работать

- Русский: включает букву ё.  
- Латинский: стандартные английские буквы.  
- Регистр сохраняется (заглавные остаются заглавными).



```python
# Определим используемые алфавиты (строчные и прописные):
RU_LOWER = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя"
RU_UPPER = RU_LOWER.upper()

EN_LOWER = "abcdefghijklmnopqrstuvwxyz"
EN_UPPER = EN_LOWER.upper()

def get_alphabet(name: str):
    """Вернет кортеж (lower_alphabet, upper_alphabet) по имени 'ru' или 'en'."""
    name = name.lower().strip()
    if name in ("ru", "rus", "russian", "рус", "русский"):
        return RU_LOWER, RU_UPPER
    elif name in ("en", "eng", "english", "лат", "латиница"):
        return EN_LOWER, EN_UPPER
    else:
        raise ValueError("Неизвестный алфавит. Используйте 'ru' или 'en'.")

```

1. Шифр Цезаря

Идея: сдвиг каждой буквы на 'k' позиций по кругу в выбранном алфавите.  
Формально для индекса символа 'a' в алфавите из 'm' букв:  
[T^j(a) = (a + j) mod m]  
где 'j = k' - ключ сдвига.

Ниже - функции шифрования и расшифрования.



```python
from typing import Tuple

def _shift_char(c: str, k: int, alpha_lower: str, alpha_upper: str) -> str:
    if c in alpha_lower:
        m = len(alpha_lower)
        return alpha_lower[(alpha_lower.index(c) + k) % m]
    if c in alpha_upper:
        m = len(alpha_upper)
        return alpha_upper[(alpha_upper.index(c) + k) % m]
    return c

def caesar_encrypt(text: str, k: int, alphabet: str = "ru") -> str:
    low, up = get_alphabet(alphabet)
    return ''.join(_shift_char(c, k, low, up) for c in text)

def caesar_decrypt(text: str, k: int, alphabet: str = "ru") -> str:
    return caesar_encrypt(text, -k, alphabet)

def caesar_mapping(k: int, alphabet: str = "ru") -> Tuple[str, str]:
    low, _ = get_alphabet(alphabet)
    mapped = ''.join(low[(i + k) % len(low)] for i in range(len(low)))
    return low, mapped

def print_caesar_table(k: int, alphabet: str = "ru"):
    src, dst = caesar_mapping(k, alphabet)
    print(f"Алфавит: {alphabet} | k = {k}")
    print("Исх.:", ' '.join(src))
    print("Шиф.:", ' '.join(dst))

```

Примеры использования шифра Цезаря


```python
plain_ru = "Съешь еще этих мягких французских булок, да выпей чаю."
k_ru = 7
cipher_ru = caesar_encrypt(plain_ru, k_ru, "ru")
back_ru = caesar_decrypt(cipher_ru, k_ru, "ru")

print_caesar_table(k_ru, "ru")
print()
print("Открытый текст (ru):", plain_ru)
print("Шифртекст (ru):     ", cipher_ru)
print("Дешифрование:       ", back_ru)

plain_en = "VENI, VIDI, VICI"
k_en = 3
cipher_en = caesar_encrypt(plain_en, k_en, "en")
back_en = caesar_decrypt(cipher_en, k_en, "en")

print()
print_caesar_table(k_en, "en")
print()
print("Open text (en):", plain_en)
print("Cipher    (en):", cipher_en)
print("Decrypted (en):", back_en)

```

    Алфавит: ru | k = 7
    Исх.: а б в г д е ё ж з и й к л м н о п р с т у ф х ц ч ш щ ъ ы ь э ю я
    Шиф.: ж з и й к л м н о п р с т у ф х ц ч ш щ ъ ы ь э ю я а б в г д е ё
    
    Открытый текст (ru): Съешь еще этих мягких французских булок, да выпей чаю.
    Шифртекст (ru):      Шбляг лал дщпь уёйспь ычжфэъошспь зътхс, кж ивцлр юже.
    Дешифрование:        Съешь еще этих мягких французских булок, да выпей чаю.
    
    Алфавит: en | k = 3
    Исх.: a b c d e f g h i j k l m n o p q r s t u v w x y z
    Шиф.: d e f g h i j k l m n o p q r s t u v w x y z a b c
    
    Open text (en): VENI, VIDI, VICI
    Cipher    (en): YHQL, YLGL, YLFL
    Decrypted (en): VENI, VIDI, VICI
    

2. Шифр Атбаш

Идея: алфавит отображается зеркально. Первая буква идет в последнюю, вторая в предпоследнюю и так далее.

Ниже - классический Атбаш и вариант для русского алфавита, где в набор символов включен пробел.



```python
def _atbash_maps(alpha_lower: str, alpha_upper: str):
    rev_lower = alpha_lower[::-1]
    rev_upper = alpha_upper[::-1]
    return {a:b for a,b in zip(alpha_lower, rev_lower)} | {a:b for a,b in zip(alpha_upper, rev_upper)}

def atbash(text: str, alphabet: str = "ru") -> str:
    low, up = get_alphabet(alphabet)
    m = _atbash_maps(low, up)
    return ''.join(m.get(c, c) for c in text)

def atbash_with_space(text: str) -> str:
    low = RU_LOWER + " "
    up = RU_UPPER + " "
    rev_low = low[::-1]
    rev_up = up[::-1]
    m = {a:b for a,b in zip(low, rev_low)} | {a:b for a,b in zip(up, rev_up)}
    return ''.join(m.get(c, c) for c in text)

def print_atbash_table(alphabet: str = "ru", include_space: bool = False):
    if include_space:
        low = RU_LOWER + " "
        mapping = (RU_LOWER + " ")[::-1]
        title = "Атбаш ru + пробел"
    else:
        low, _ = get_alphabet(alphabet)
        mapping = low[::-1]
        title = f"Атбаш {alphabet}"
    print(title)
    print("Исх.:", ' '.join(low))
    print("Шиф.:", ' '.join(mapping))

```

Примеры использования шифра Атбаш


```python
plain_ru2 = "Секретное сообщение"
cipher_atb_ru = atbash(plain_ru2, "ru")
back_atb_ru = atbash(cipher_atb_ru, "ru")

print_atbash_table("ru", include_space=False)
print()
print("Открытый:", plain_ru2)
print("Шифр:    ", cipher_atb_ru)
print("Назад:   ", back_atb_ru)

print()
plain_ru3 = "пример с пробелом"
cipher_atb_space = atbash_with_space(plain_ru3)
back_atb_space = atbash_with_space(cipher_atb_space)

print_atbash_table("ru", include_space=True)
print()
print("Открытый:", plain_ru3)
print("Шифр:    ", cipher_atb_space)
print("Назад:   ", back_atb_space)

print()
plain_en2 = "Attack at dawn"
cipher_atb_en = atbash(plain_en2, "en")
back_atb_en = atbash(cipher_atb_en, "en")

print_atbash_table("en", include_space=False)
print()
print("Open:   ", plain_en2)
print("Cipher: ", cipher_atb_en)
print("Back:   ", back_atb_en)

```

    Атбаш ru
    Исх.: а б в г д е ё ж з и й к л м н о п р с т у ф х ц ч ш щ ъ ы ь э ю я
    Шиф.: я ю э ь ы ъ щ ш ч ц х ф у т с р п о н м л к й и з ж ё е д г в б а
    
    Открытый: Секретное сообщение
    Шифр:     Нъфоъмсръ нррюёъсцъ
    Назад:    Секретное сообщение
    
    Атбаш ru + пробел
    Исх.: а б в г д е ё ж з и й к л м н о п р с т у ф х ц ч ш щ ъ ы ь э ю я  
    Шиф.:   я ю э ь ы ъ щ ш ч ц х ф у т с р п о н м л к й и з ж ё е д г в б а
    
    Открытый: пример с пробелом
    Шифр:     рпчуыпАоАрпсяыфсу
    Назад:    пример с пробелом
    
    Атбаш en
    Исх.: a b c d e f g h i j k l m n o p q r s t u v w x y z
    Шиф.: z y x w v u t s r q p o n m l k j i h g f e d c b a
    
    Open:    Attack at dawn
    Cipher:  Zggzxp zg wzdm
    Back:    Attack at dawn
    

Удобные функции для проверки


```python
def demo_caesar(text: str, k: int, alphabet: str = "ru"):
    print_caesar_table(k, alphabet)
    res = caesar_encrypt(text, k, alphabet)
    print("\nТекст:", text)
    print("Шифр :", res)
    print("Назад:", caesar_decrypt(res, k, alphabet))

def demo_atbash(text: str, alphabet: str = "ru", include_space: bool = False):
    print_atbash_table(alphabet, include_space)
    if include_space and alphabet == "ru":
        res = atbash_with_space(text)
        back = atbash_with_space(res)
    else:
        res = atbash(text, alphabet)
        back = atbash(res, alphabet)
    print("\nТекст:", text)
    print("Шифр :", res)
    print("Назад:", back)

# Пример запуска:
demo_caesar("Пример текста", 12, "ru")
print()
demo_atbash("Пример текста", "ru", include_space=False)

```

    Алфавит: ru | k = 12
    Исх.: а б в г д е ё ж з и й к л м н о п р с т у ф х ц ч ш щ ъ ы ь э ю я
    Шиф.: л м н о п р с т у ф х ц ч ш щ ъ ы ь э ю я а б в г д е ё ж з и й к
    
    Текст: Пример текста
    Шифр : Ыьфшрь юрцэюл
    Назад: Пример текста
    
    Атбаш ru
    Исх.: а б в г д е ё ж з и й к л м н о п р с т у ф х ц ч ш щ ъ ы ь э ю я
    Шиф.: я ю э ь ы ъ щ ш ч ц х ф у т с р п о н м л к й и з ж ё е д г в б а
    
    Текст: Пример текста
    Шифр : Поцтъо мъфнмя
    Назад: Пример текста
    


```python

```
