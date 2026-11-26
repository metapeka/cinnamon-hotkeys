# Cinnamon · Навигация на клавиатуре

Справочник хоткеев Debian/Cinnamon, снятый с живой машины, плюс переиспользуемый стиль «Тёплая бумага».

Открывать офлайн двойным кликом:

- `hotkeys.html` — энциклопедия (11 разделов).
- `design/gallery.html` — превью токенов и компонентов.

## Что внутри

| Файл | Назначение |
|---|---|
| `hotkeys.html` | Справочник по навигации и хоткеям |
| `design/tokens.css` | Дизайн-токены: цвет, типографика, отступы, радиусы. Светлая и тёмная тема |
| `design/base.css` | Компоненты на токенах: карточки, таблицы, `kbd`, выноски, поток |
| `design/gallery.html` | Витрина токенов — смотреть, что можно переиспользовать |

## Стиль «Тёплая бумага»

Производная от оформления `memory/npotehin` (`styles/01-cards`): тёплая бумага, спокойный текст, читаемые сочетания клавиш.

### Подключение в новом проекте

1. Скопировать папку `design/` в проект.
2. В `<head>` — два `<link>`, порядок важен:

```html
<link rel="stylesheet" href="design/tokens.css">
<link rel="stylesheet" href="design/base.css">
```

3. На `<html>` поставить `data-theme="light"` или `data-theme="dark"`. Если атрибута нет — тема следует системной (`prefers-color-scheme`).
4. Разметка: `.wrap` → `.hero` → `.sec` с `h2[data-n]`.
5. Свои цвета не хардкодить. Добавлять токен в `tokens.css` для обеих тем.

### Токены

```css
:root {
  --paper:#f4f2ee;  --surface:#ffffff;  --soft:#faf8f5;
  --ink:#1d1d1f;    --muted:#6b665f;    --line:#e2ded7;
  --key:#7a4e0f;    --warn:#d9a41a;     --ok:#2b7a4b;

  --fs-xs:11px; --fs-sm:13px; --fs-base:16px; --fs-lg:19px; --fs-xl:28px;
  --s1:4px; --s2:8px; --s3:12px; --s4:16px; --s5:20px; --s6:26px; --s7:34px;
  --r-sm:6px; --r-md:10px; --r-lg:16px; --r-pill:999px;
  --wrap:860px;
}
[data-theme="dark"] { --paper:#0f0f10; --surface:#1c1c1e; --soft:#242426;
  --ink:#f2f2f7; --muted:#98989f; --line:#333; --key:#e0b446; --warn:#e0b446; }
```

### Переключатель темы

```js
const root = document.documentElement;
document.querySelectorAll('[data-t]').forEach(b => b.onclick = () => {
  root.dataset.theme = b.dataset.t;
  document.querySelectorAll('[data-t]').forEach(x =>
    x.setAttribute('aria-pressed', x.dataset.t === b.dataset.t));
});
```

## Привязка к машине

Данные в справочнике — не общий список, а снимок конкретной системы.

**Система:** Debian 13 «trixie», Cinnamon 6.4.10, X11, сессия `lightdm-xsession`, терминал `gnome-terminal`, раскладка `grp:alt_shift_toggle`.

**Где хранятся настройки**

| Что | Где | Как читать |
|---|---|---|
| Настройки Cinnamon и WM | `~/.config/dconf/user` (двоичная) | `dconf dump /org/cinnamon/` |
| Хоткеи WM | схемы gsettings | `dconf dump /org/gnome/desktop/wm/keybindings/` |
| Темы, расширения, апплеты | `~/.local/share/cinnamon/`, `~/.themes/` | `ls ~/.local/share/cinnamon` |
| Логи сессии | `~/.xsession-errors` | `tail ~/.xsession-errors` |

**Проверить и переназначить**

```bash
cat /etc/os-release                                  # дистрибутив
cinnamon --version                                   # версия стола
echo $XDG_SESSION_TYPE                               # x11 / wayland
setxkbmap -query                                     # раскладка

gsettings list-recursively org.cinnamon.desktop.keybindings.wm
gsettings list-recursively org.gnome.desktop.wm.keybindings
gsettings list-recursively org.cinnamon.desktop.keybindings.media-keys

gsettings get org.cinnamon.desktop.keybindings.wm close
gsettings set org.cinnamon.desktop.keybindings.wm close "['<Super>q']"

xev                                                  # имя/код нажатой клавиши
dconf watch /                                        # что пишется при смене в настройках

dconf dump /org/cinnamon/ > cinnamon-backup.ini      # резервная копия
dconf load /org/cinnamon/ < cinnamon-backup.ini      # восстановление, перезаписывает раздел
```

**Осторожно:** `dconf load` перезаписывает раздел целиком. Сначала `dconf dump` — это и есть бэкап.

## Материалы

- Debian — <https://www.debian.org/>
- Cinnamon, исходники — <https://github.com/linuxmint/Cinnamon>
- Cinnamon, проект — <https://linuxmint.com/>
- Локально: `man gsettings`, `man dconf`, `man xev`, `man setxkbmap`
- Все схемы: `gsettings list-schemas`
- Поиск по настройкам: `gsettings list-recursively | grep -i workspace`
