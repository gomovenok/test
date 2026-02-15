# KoGaMa: адаптация вызова панели под новый интерфейс

Старый чит открывал панель через `contextmenu` по двум жёстким селекторам старого UI:

- `#profile-extended-toggle>a>i`
- `#react-ingame-mini-profile > div > div:nth-child(2) > div.sc-fMiknA.hkeFtp > img.sc-fAjcbJ.dLmONi`

В новом интерфейсе эти узлы отсутствуют, поэтому `event.target == document.querySelector(...)` больше не срабатывает.

## Что лучше использовать сейчас

В присланной разметке более устойчивые точки входа:

- ссылка профиля в хедере: `a[href^="/profile/"]`
- аватар в хедере: `.UA3TP ._3tYRU` (класс может меняться, использовать как fallback)
- элемент шапки: `header.MuiAppBar-root`

## Практичная стратегия

1. Не сравнивать `target` по строгому равенству с одним узлом.
2. Использовать делегирование и `closest()`:
   - `e.target.closest('a[href^="/profile/"]')`
   - fallback: `e.target.closest('header.MuiAppBar-root')`
3. Добавить hotkey (например `F8`) как резервный способ открыть/закрыть панель.

## Минимальный паттерн логики

- На `document.addEventListener('contextmenu', ...)`:
  - если найден `closest('a[href^="/profile/"]')`, то `console.style.display = 'block'` и `preventDefault()`.
- На `document.addEventListener('keydown', ...)`:
  - `F8` переключает `display` между `none/block`.

Это переживает изменения вложенности и динамических className (MUI/css-hash), чем старые «длинные» селекторы.
