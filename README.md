# FunLan
A simple functional language as a university project
---
FunLan - язык, реализованный на F# для учебного задания по курсу "Функциональное программирование". Этот репозиторий содержит его документацию.  

| ФИО | Роль |
| ----------- | ----------- |
| Кравцов Алексей    | Написание кода   |
| Леонова Елизавета   | Написание документации   |
| Бадалян Тигран    | Поиск информации, подбор примеров кода   |

Учебная группа: М8О-210Б-22.

## Идея
Наш язык основан на лямбда-исчислении, содержит аппликацию `App` и абстракцию `Lam`.

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
Функции `splitcommands` и `splitwords` разделяют код на отдельные строки и строки на токены соответственно.  
Парсер реализован методом рекурсивного спуска. Он принимает список токенов, возвращает дерево выражения и оставшиеся токены.

## Интерпретатор
Для интерпретатора была выбрана eval/apply логика. Данный интерпретатор содержит две основные одноименные функции: `eval` вычисляет значение выражения в заданном окружении, а `apply` редуцирует выражение на один шаг. Интерпретатор использует ленивые вычисления, для этого была добалена вспомогательная конструкция `Susp`.

## Компилятор
Функция `build` запускает сначала лексер, затем парсер. Результат подает в интерпретатор и компилирует получившееся дерево выражений. Чтобы скомпилировать код, необходимо написать в следующем формате: `build "<код>"`.  
`build "| < 3 2 | | - 9 2 |" `

## Синтаксис
За основу был взят синтаксис языка F# с небольшими изменениями.  
    - В начале и в конце строки ставится |  
    `| let x = 3 in ( + x 5 ) | // 8`  
    - Используется префиксная форма записи выражений  
    `| < 3 2 | // False`   
    - Атом обозначается символом $   
    `| $A | // A`   
    `| $[ A , B , C ] | // $[ A B C ]`  
    - Список обозначается скобками []  
    `| ttl [ 1 , 2 , 3 , 4 ] | // [ 2 3 4 ]`

## Возможности языка
В FunLan представлены следующие возможности:  
    - Арифметические операции (+,-,*,/)  
    `| - 9 22 | // -13`  
    - Операции сравнения (==,<,>,<=)  
    `| == 1 1 | // True`  
    - Булева алгебра (and, or, not)  
    `| and 1 0 | // 0`  
    `| and true false | // False`  
    - Операции над списками  
    -> Взятие головы списка  
    `| thl [ 1 , 2 , 3 , 4 ] | // 1`  
    -> Взятие хвоста списка  
    `| ttl [ 1 , 2 , 3 , 4 ] | // [ 2 3 4 ]`  
    -> Создание списка из двух составляющих   
    `| cons 1 [ 2 , 3 ] | // [ 1 2 3 ]`  
    `| cons [ [ 1 , 2 ] ] [ 2 , 3 ] | // [ [ [ 1 2 ] ] 2 3 ]`  
    -> Вычисление длины списка
    `| len [ 2 , 3 ] | // 2`  
    -> Иное  
    `| map abs [ -1 , 2 , -3 ] // [ 1 2 3 ]|`  
    - Let  
    `| let x = 3 in ( + x 5 ) | // 8`  
    `| let x = 5 in ( let y = 1 in ( + x y ) | // 6`  
    - Условия  
    `| if ( < 3 5 ) then ( 5 ) else ( 3 ) | // 5`  
    `| if ( < 3 5 ) then ( * 5 5 ) else ( * 3 3 ) | // 25`    
    - Рекурсия  
    `| letrec fact = ( lambda x = if ( <= x 1 ) then ( 1 ) else ( * x ( fact ( - x 1 ) ) ) ) in ( fact 5 ) | // 120`  
    - Именованные переменные  
    `| var A = [ 1 , 2 , 3 ] |    | * 5 7 |    | A | // 35 [ 1 2 3 ]`  
    - Еще примеры  
    `| let fun = ( lambda x = ( * x 2 ) ) |  | * 3 5 |    | fun 10 | // 15 20`  
    `| letrec fact = ( lambda x = if ( <= x 1 ) then ( 1 ) else ( * x ( fact ( - x 1 ) ) ) ) |  | * 3 2 |    | fact 5 | // 6 120`  
    
## Заключение
Это был интересный и сложный опыт. Языку FunLan многого не хватает, например, обработки ошибок и оповещения о них. Тем не менее, вот наш MVP. Для того, чтобы быстро узнать, как работают конкретные моменты в синтаксисе F#, мы пользовались ИИ. Например, с его помощью мы решили такие микрозадачи как добавление условния в патерн матчинг и проверка, что строка с чего-то начинается.
