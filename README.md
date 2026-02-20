# 🚀 Antigravity Installer for Arch Linux

[![Shell Script](https://img.shields.io/badge/Shell-Bash-4EAA25?logo=gnubash&logoColor=white)](https://www.gnu.org/software/bash/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?logo=archlinux&logoColor=white)](https://archlinux.org/)

> Unofficial installer for **[Google Antigravity](https://antigravity.withgoogle.com)** on Arch-based Linux distributions (Arch, Garuda, Manjaro, EndeavourOS, etc.)
>
> Based on [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch) with improvements.

**🇬🇧 [English](#english)** | **🇷🇺 [Русский](#русский)**

---

# English

## ✨ Features

| Feature | Description |
|---------|-------------|
| 📦 **Latest build** | Fetches the latest Antigravity `.deb` directly from Google's official APT repository |
| 🔒 **SHA256 verification** | Validates package integrity before installation |
| 📁 **Clean install** | Installs binaries to `/opt/antigravity`, symlinks to `/usr/local/bin` |
| 🖥️ **Desktop integration** | Installs `.desktop` launchers and application icon |
| 🛡️ **Sandbox fix** | Applies Chrome-style `chrome-sandbox` SUID fix automatically |
| 🔄 **Idempotent** | Re-running the script updates Antigravity to the latest version |
| 🗑️ **Clean uninstall** | `--uninstall` flag removes all installed files |

## 🔧 Changes from Original

This fork includes the following improvements over [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch):

| # | Change | Details |
|---|--------|---------|
| 1 | **`sudo` added to dependency check** | The original script did not verify that `sudo` is available before attempting privileged operations. This fork checks for `sudo` upfront, providing a clear error message instead of failing mid-install. |
| 2 | **EOF-tolerant APT index parser** | The original `awk` parser only captured package metadata when stanzas were separated by blank lines. If the `Packages` file ended without a trailing blank line (valid per APT spec), the last entry was silently skipped. This fork handles EOF-terminated stanzas correctly, ensuring the latest version is always detected. |

## 📋 Requirements

The following utilities must be present on your system:

| Utility | Package (pacman) | Purpose |
|---------|------------------|---------|
| `curl` | `curl` | Download packages and index |
| `bsdtar` | `libarchive` | Extract `.deb` archives |
| `sha256sum` | `coreutils` | Verify package integrity |
| `awk` | `gawk` | Parse APT Packages index |
| `sudo` | `sudo` | Install with root privileges |

Install all dependencies at once:

```bash
sudo pacman -S curl libarchive coreutils gawk sudo
```

> [!NOTE]
> Most Arch-based distributions already have these packages pre-installed.

## 📦 Installation & Usage

### Quick start (one-liner)

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### Step-by-step

#### 1. Clone the repository

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
```

#### 2. Make the script executable

```bash
chmod +x antigravity-installer.sh
```

#### 3. Run the installer

```bash
./antigravity-installer.sh
```

The script will automatically:
- Fetch the latest Antigravity `.deb` package from Google APT
- Verify the SHA256 checksum
- Install files into `/opt/antigravity`
- Create a symlink at `/usr/local/bin/antigravity`
- Install `.desktop` entries and the application icon
- Apply sandbox fixes if needed (SUID bit on `chrome-sandbox`)

#### 4. Launch Antigravity

```bash
antigravity
```

Or find **Antigravity** in your application launcher.

#### 5. Update

Simply re-run the installer — it will fetch and install the latest version:

```bash
./antigravity-installer.sh
```

#### 6. Uninstall

```bash
./antigravity-installer.sh --uninstall
```

This removes:
- `/opt/antigravity` (binaries)
- `/usr/local/bin/antigravity` (symlink)
- `.desktop` files from `/usr/share/applications`
- Application icon from `/usr/share/pixmaps`

## 🏗️ How It Works

```
┌─────────────────────────────────────────────────────────┐
│                  antigravity-installer.sh                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. Fetch APT Packages index from Google repository     │
│                        ↓                                │
│  2. Parse latest antigravity package metadata (awk)     │
│                        ↓                                │
│  3. Download .deb package                               │
│                        ↓                                │
│  4. Verify SHA256 checksum                              │
│                        ↓                                │
│  5. Extract .deb → data.tar.xz → files (bsdtar)        │
│                        ↓                                │
│  6. Install to /opt/antigravity                         │
│                        ↓                                │
│  7. Create symlink, .desktop files, icon                │
│                        ↓                                │
│  8. Fix chrome-sandbox permissions (SUID)               │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

## 📁 Installed File Locations

| Path | Description |
|------|-------------|
| `/opt/antigravity/` | Application binaries |
| `/opt/antigravity/antigravity` | Main executable |
| `/opt/antigravity/chrome-sandbox` | Chromium sandbox (SUID root) |
| `/usr/local/bin/antigravity` | Symlink to the main executable |
| `/usr/share/applications/antigravity.desktop` | Desktop launcher |
| `/usr/share/applications/antigravity-url-handler.desktop` | URL handler |
| `/usr/share/pixmaps/antigravity.png` | Application icon |

## 🔐 Security Notes

> [!IMPORTANT]
> Always review shell scripts before running them with `sudo` or elevated privileges.

- The script sets `set -euo pipefail` for safe execution
- Package integrity is verified via **SHA256** before installation
- The `chrome-sandbox` binary gets **SUID root** (`chmod 4755`) — this is the same approach Google Chrome uses
- All temporary files are cleaned up automatically via `trap`
- No external dependencies beyond standard Arch Linux packages

## 🛠️ Troubleshooting

<details>
<summary><b>bsdtar: command not found</b></summary>

Install `libarchive`:
```bash
sudo pacman -S libarchive
```
</details>

<details>
<summary><b>Antigravity won't start / sandbox error</b></summary>

Make sure `chrome-sandbox` has the correct permissions:
```bash
sudo chown root:root /opt/antigravity/chrome-sandbox
sudo chmod 4755 /opt/antigravity/chrome-sandbox
```

Alternatively, you can run with `--no-sandbox` flag:
```bash
antigravity --no-sandbox
```
</details>

<details>
<summary><b>SHA256 checksum mismatch</b></summary>

This may indicate a network issue or a corrupted download. Try running the script again:
```bash
./antigravity-installer.sh
```
</details>

## ⚠️ Disclaimer

- This installer is **unofficial** and is **not affiliated with or endorsed by Google**
- Use at your own risk
- The script aims to be safe and minimal, but you are responsible for your own system

---

# Русский

## ✨ Возможности

| Возможность | Описание |
|-------------|----------|
| 📦 **Последняя сборка** | Загружает последний `.deb`-пакет Antigravity напрямую из официального APT-репозитория Google |
| 🔒 **Проверка SHA256** | Проверяет целостность пакета перед установкой |
| 📁 **Чистая установка** | Устанавливает файлы в `/opt/antigravity`, создаёт символическую ссылку в `/usr/local/bin` |
| 🖥️ **Интеграция с рабочим столом** | Устанавливает `.desktop`-файлы и иконку приложения |
| 🛡️ **Исправление sandbox** | Автоматически применяет SUID-бит для `chrome-sandbox` (как в Google Chrome) |
| 🔄 **Идемпотентность** | Повторный запуск скрипта обновляет Antigravity до последней версии |
| 🗑️ **Чистое удаление** | Флаг `--uninstall` удаляет все установленные файлы |

## 🔧 Отличия от оригинала

Этот форк содержит следующие улучшения по сравнению с [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch):

| # | Изменение | Подробности |
|---|-----------|-------------|
| 1 | **`sudo` добавлен в проверку зависимостей** | Оригинальный скрипт не проверял наличие `sudo` перед выполнением привилегированных операций. В этом форке `sudo` проверяется заранее, выдавая понятное сообщение об ошибке вместо падения в середине установки. |
| 2 | **Устойчивый к EOF парсер APT-индекса** | Оригинальный `awk`-парсер получал метаданные пакета только при разделении записей пустыми строками. Если файл `Packages` заканчивался без завершающей пустой строки (что допустимо по спецификации APT), последняя запись молча пропускалась. Этот форк корректно обрабатывает такие случаи, гарантируя обнаружение последней версии. |

## 📋 Зависимости

На вашей системе должны быть установлены следующие утилиты:

| Утилита | Пакет (pacman) | Назначение |
|---------|----------------|------------|
| `curl` | `curl` | Загрузка пакетов и индекса |
| `bsdtar` | `libarchive` | Распаковка `.deb`-архивов |
| `sha256sum` | `coreutils` | Проверка целостности пакета |
| `awk` | `gawk` | Парсинг APT-индекса |
| `sudo` | `sudo` | Установка с правами root |

Установить все зависимости одной командой:

```bash
sudo pacman -S curl libarchive coreutils gawk sudo
```

> [!NOTE]
> В большинстве дистрибутивов на базе Arch эти пакеты уже предустановлены.

## 📦 Установка и использование

### Быстрая установка (одной командой)

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### Пошаговая установка

#### 1. Клонируйте репозиторий

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
```

#### 2. Сделайте скрипт исполняемым

```bash
chmod +x antigravity-installer.sh
```

#### 3. Запустите установку

```bash
./antigravity-installer.sh
```

Скрипт автоматически:
- Найдёт последнюю доступную версию Antigravity в репозиториях Google APT
- Проверит целостность файла по **SHA256**
- Установит программу в `/opt/antigravity`
- Создаст символическую ссылку `/usr/local/bin/antigravity`
- Добавит ярлыки приложения в меню и иконку
- Настроит sandbox (SUID-бит на `chrome-sandbox`)

#### 4. Запуск

```bash
antigravity
```

Или найдите **Antigravity** в меню приложений.

#### 5. Обновление

Просто запустите скрипт повторно — он загрузит и установит последнюю версию:

```bash
./antigravity-installer.sh
```

#### 6. Удаление

```bash
./antigravity-installer.sh --uninstall
```

Будут удалены:
- `/opt/antigravity` (файлы программы)
- `/usr/local/bin/antigravity` (символическая ссылка)
- `.desktop`-файлы из `/usr/share/applications`
- Иконка из `/usr/share/pixmaps`

## 🏗️ Принцип работы

```
┌──────────────────────────────────────────────────────────┐
│                  antigravity-installer.sh                  │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  1. Загрузка APT-индекса из репозитория Google            │
│                        ↓                                 │
│  2. Парсинг метаданных последней версии (awk)             │
│                        ↓                                 │
│  3. Скачивание .deb-пакета                               │
│                        ↓                                 │
│  4. Проверка контрольной суммы SHA256                     │
│                        ↓                                 │
│  5. Распаковка .deb → data.tar.xz → файлы (bsdtar)       │
│                        ↓                                 │
│  6. Установка в /opt/antigravity                          │
│                        ↓                                 │
│  7. Создание ссылки, .desktop-файлов, иконки              │
│                        ↓                                 │
│  8. Настройка прав chrome-sandbox (SUID)                  │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

## 📁 Расположение установленных файлов

| Путь | Описание |
|------|----------|
| `/opt/antigravity/` | Файлы приложения |
| `/opt/antigravity/antigravity` | Основной исполняемый файл |
| `/opt/antigravity/chrome-sandbox` | Sandbox Chromium (SUID root) |
| `/usr/local/bin/antigravity` | Символическая ссылка на исполняемый файл |
| `/usr/share/applications/antigravity.desktop` | Ярлык приложения |
| `/usr/share/applications/antigravity-url-handler.desktop` | Обработчик URL |
| `/usr/share/pixmaps/antigravity.png` | Иконка приложения |

## 🔐 Безопасность

> [!IMPORTANT]
> Всегда проверяйте содержимое скриптов перед запуском с `sudo` или повышенными привилегиями.

- Скрипт использует `set -euo pipefail` для безопасного выполнения
- Целостность пакета проверяется по **SHA256** перед установкой
- Бинарник `chrome-sandbox` получает **SUID root** (`chmod 4755`) — аналогично Google Chrome
- Все временные файлы автоматически удаляются через `trap`
- Нет внешних зависимостей помимо стандартных пакетов Arch Linux

## 🛠️ Решение проблем

<details>
<summary><b>bsdtar: command not found</b></summary>

Установите `libarchive`:
```bash
sudo pacman -S libarchive
```
</details>

<details>
<summary><b>Antigravity не запускается / ошибка sandbox</b></summary>

Убедитесь, что `chrome-sandbox` имеет правильные разрешения:
```bash
sudo chown root:root /opt/antigravity/chrome-sandbox
sudo chmod 4755 /opt/antigravity/chrome-sandbox
```

Альтернативно можно запустить с флагом `--no-sandbox`:
```bash
antigravity --no-sandbox
```
</details>

<details>
<summary><b>Ошибка контрольной суммы SHA256</b></summary>

Это может указывать на проблему с сетью или повреждённую загрузку. Попробуйте запустить скрипт заново:
```bash
./antigravity-installer.sh
```
</details>

## ⚠️ Отказ от ответственности

- Этот установщик является **неофициальным** и **не связан с Google и не одобрен Google**
- Используйте на свой страх и риск
- Скрипт стремится быть безопасным и минимальным, но ответственность за вашу систему лежит на вас

---

## 🤝 Contributing / Участие в разработке

Contributions, pull requests, and feedback are welcome! / Приветствуются любые доработки, pull request'ы и обратная связь!

1. Fork the repository / Сделайте форк
2. Create your feature branch / Создайте ветку (`git checkout -b feature/awesome`)
3. Commit your changes / Сделайте коммит (`git commit -m 'Add awesome feature'`)
4. Push to the branch / Отправьте ветку (`git push origin feature/awesome`)
5. Open a Pull Request / Откройте Pull Request

## 📝 License / Лицензия

This project is open source. See the [LICENSE](LICENSE) file for details.

Проект с открытым исходным кодом. Подробности в файле [LICENSE](LICENSE).

## 🙏 Credits / Благодарности

- Original script / Оригинальный скрипт: [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch)
- [Google Antigravity](https://antigravity.withgoogle.com)
