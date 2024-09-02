# FunLan
A simple functional language as a university project
---
Funlan - язык, реализованный на F# для учебного задания по курсу "Функциональное программирование". Этот репозиторий содержит его документацию.  
Команда: Кравцов Алексей, Леонова Елизавета, Бадалян Тигран. Учебная группа: М8О-210Б-22.

## Идея
Наш язык основан на лямбда-исчислении, то есть содержит аппликацию `App` и абстракцию `Lam`.

```F#
type id = string 
type expr = 
| App of expr*expr 
| Lam of id*expr 
| Var of id 
| Int of int 
| PFunc of id 
| Cond of expr*expr*expr 
| Let of id*expr*expr 
| LetRec of id*expr*expr 
| Op of id*int*expr list*env 
| Closure of expr*env 
| RClosure of expr*env*id 
| Susp of expr*env 
| EList of expr list 
| ConstE of string 
| ConstL of expr list 
| True 
| False 
and 
  env = Map<id,expr> 
```
## Лексер и парсер
Функции splitcommands и splitwords разделяют код на отдельные строки и строки на токены соответственно.  
Парсер реализован методом рекурсивного спуска. Он принимает список токенов, возвращает дерево выражения и оставшиеся токены.

## Интерпретатор
Для интерпретатора была выбрана eval/apply логика. Данный интерпретатор содержит две основные одноименные функции: eval вычисляет значение выражения в заданном окружении, а apply редуцирует выражение на один шаг. Интерпретатор использует ленивые вычисления, для этого была добалена вспомогательная конструкция `Susp`.

## Компилятор
Функция build запускает сначала лексер, затем парсер. Результат подает в интерпретатор и компилирует получившееся дерево выражений. Чтобы скомпилировать код, необходимо написать в следующем формате: `build "<код>"`.  
`build "| < 3 2 | | - 9 2 |" `

## Синтаксис
За основу был взят синтаксис языка F# с небольшими изменениями.  
    - В начале и в конце строки ставится |  
    `| let x = 3 in ( + x 5 ) | # 8`
    - Используется префиксная форма записи выражений  
    `| < 3 2 | # False`  
    `| and true false | # False`  
    - Атом обозначается символом $   
    `| $A |`  
    `| $[ A , B , C ] |`  
    - Список обозначается скобками []  
    `| ttl [ 1 , 2 , 3 , 4 ] |`

## Возможности языка
В FunLan представлены следующие возможности:
    - Арифметические операции (+,-,*,/)
    `"| - 9 22 |" `  
    - Операции сравнения (==,<,>,<=)  
    `| == 1 1 |`
    - Булева алгебра ()
    `| and 1 0 |`  
    `| and true false |`
