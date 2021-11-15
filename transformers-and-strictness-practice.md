# Transformers practice

## Задание 1

Продолжаем развлекаться с eDSL.

Мы теперь знаем как комбинировать монады,
поэтому готовы реализовать более-менее полноценный игрушечный интерпретатор
для нашего eDSL.

Необходимо реализовать функцию:
```haskell
evalStatement :: (???) => Statement a -> String
```
для eDSL, который является объединением 1-3 заданий из [прошлой практики](./monad-practice.md), т.е.
нужно поддерживать переменные, сообщения об ошибках и логирование.

## Задание 2

Реализовать стековый строковой процессор. Его грамматика следующая:
* `push "string" :: s -> String : s` положить строку на вершину стека.
* `read :: String {file} : s -> String : s` прочитать файл с путем, заданным на вершине стека и положить содержимое его на вершину стека.
* `slice <int> <int> :: String : s -> String : s`
* `concat :: String : String : s -> String : s`

Необходимо реализовать интерпретатор. Самостоятельно определите тип
для eDSL этого строкового процессора.

В конце работы интепретатора, вернуть строку с вершини стека, если стек размера 1,
и сообщить об ошибке, если размер стека не единичный.

В качестве среды необходимо использовать `ReaderT (IORef [Text]) IO`.


## Задание 3

Аналогично предыдущему, только в качестве среды исполнения необходимо использовать
`ReaderT (Map FilePath Text) (StateT [Text])`.

## Задание 4

`WriterT` на стероидах.

Реализовать трансформер, инстанс `MonadTrans` для него, а также mtl-style type-class,
которые будут предоставлять функционал для логирования.

Логирование должно поддерживать несколько уровней:
```haskell
data LogLevel
  = Error
  | Warn
  | Info
  | Debug
```

```haskell
newtype LoggingT m a = LoggingT { runLoggingT :: m (a, [String]) }

class Monad m => MonadLogger m where
  logMessage :: MonadLogger m => String -> LogLevel -> m ()
```

И реализовать несколько вспомогательный функций:
```haskell
logError :: MonadLogger m => String -> m ()
logWarn :: MonadLogger m => String -> m ()
logInfo :: MonadLogger m => String -> m ()
logDebug :: MonadLogger m => String -> m ()
```

В качестве примера, используйте новый эффект для расширения функционала логирования в предыдущих заданиях.

# Strictness practice

## Задание 1

Реализуйте функцию
```haskell
fastPow :: Integer -> Int -> Integer
```
используя расширение языка
```
{-# LANGUAGE BangPatterns #-}
```
(функция должна реализовывать бинарное возведение в степень).

Реализуйте также версию без расширения, сравните производительность
двух функций.

## Задание 2
Реализуйте вручную инстанс `NFData` для
```haskell
data Tree = Node { label :: String, children :: [Tree] }
```

Обратите внимание, что вам необходимо будет подключить пакет
`deepseq`, для того чтобы использовать `NFData`.

## Задание 3
Вам дан `newtype`
```haskell
newtype StrictNum a = StrictNum { unStrictNum :: a }
```

Реализуйте `instance (???) => Num (StrictNum a)` так чтобы результаты
арифметических операций были всегда в головной нормальной
форме.

Сравните время работы `sum` на больших размерах списка для `Int` и `StrictNum Int`.

# Streams practice

## Задание 1

Реализуйте функцию `return` для типа `Stream`, который обсуждался на лекции.

```haskell
data Step s a
  = Done
  | Skip s
  | Yield a s

data Stream a = forall s . Stream (s -> Step s a) s
```

Функция должна иметь следующий тип:
```haskell
return :: a -> Stream a
```

## Задание 2

Реализуйте функцию `concat` для `Stream`:
```haskell
concat :: Stream a -> Stream a -> Stream a
```

## Задание 3

Реализуйте функции `foldl` и `foldr` для `Stream`:
```haskell
foldl :: (b -> a -> b) -> b -> Stream a -> b

foldr :: (a -> b -> b) -> b -> Stream a -> b
```

# Mutable structures practice

## Задание 1

Реализуйте двоичную кучу и функции для взаимодействия с ней используя мутабельный структуры данных,
который обсуждались на [лекции](https://slides.com/fp-ctd/lecture-10#/35/0/2).

Это задание может быть рассказано более чем одним человеком при условии, что разными людьми
будут использованы разные типы для представления мутабельного массива.

## Задание 2

Императивно еализуйте in-place `O(nlogn)` сортировку используя `ST` монаду и мутабельные списки.
