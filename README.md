# 🚀 Antigravity Installer for Arch Linux

[![Shell Script](https://img.shields.io/badge/Shell-Bash-4EAA25?logo=gnubash&logoColor=white)](https://www.gnu.org/software/bash/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?logo=archlinux&logoColor=white)](https://archlinux.org/)

> Unofficial installer for **[Google Antigravity](https://antigravity.withgoogle.com)** on Arch-based Linux distributions (Arch, Garuda, Manjaro, EndeavourOS, etc.)
>
> Based on [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch) with improvements.

**🇬🇧 [English](#english)** | **🇷🇺 [Русский](#русский)** | **🇨🇳 [中文](#中文)** | **🇪🇸 [Español](#español)** | **🇫🇷 [Français](#français)** | **🇩🇪 [Deutsch](#deutsch)** | **🇯🇵 [日本語](#日本語)** | **🇰🇷 [한국어](#한국어)** | **🇧🇷 [Português](#português)** | **🇮🇳 [हिन्दी](#हिन्दी)** | **🇸🇦 [العربية](#العربية)** | **🇹🇷 [Türkçe](#türkçe)** | **🇮🇹 [Italiano](#italiano)**

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

# 中文

## ✨ 功能特性

| 功能 | 说明 |
|------|------|
| 📦 **最新版本** | 直接从 Google 官方 APT 仓库获取最新的 Antigravity `.deb` 包 |
| 🔒 **SHA256 校验** | 安装前验证包的完整性 |
| 📁 **干净安装** | 将二进制文件安装到 `/opt/antigravity`，在 `/usr/local/bin` 创建符号链接 |
| 🖥️ **桌面集成** | 安装 `.desktop` 启动器和应用图标 |
| 🛡️ **沙箱修复** | 自动为 `chrome-sandbox` 应用 SUID 修复（与 Google Chrome 相同） |
| 🔄 **幂等性** | 重新运行脚本即可更新到最新版本 |
| 🗑️ **干净卸载** | `--uninstall` 参数移除所有已安装的文件 |

## 🔧 与原版的区别

此 fork 相对于 [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch) 包含以下改进：

| # | 变更 | 详情 |
|---|------|------|
| 1 | **依赖检查中添加了 `sudo`** | 原始脚本在尝试特权操作前未验证 `sudo` 是否可用。此 fork 提前检查 `sudo`，在安装中途失败前给出清晰的错误提示。 |
| 2 | **容错 EOF 的 APT 索引解析器** | 原始 `awk` 解析器仅在条目以空行分隔时捕获包元数据。如果 `Packages` 文件末尾没有空行（APT 规范允许），最后一个条目会被静默跳过。此 fork 正确处理了这种情况。 |

## 📋 依赖

| 工具 | 包名 (pacman) | 用途 |
|------|---------------|------|
| `curl` | `curl` | 下载包和索引 |
| `bsdtar` | `libarchive` | 解压 `.deb` 归档 |
| `sha256sum` | `coreutils` | 验证包完整性 |
| `awk` | `gawk` | 解析 APT 索引 |
| `sudo` | `sudo` | 以 root 权限安装 |

```bash
sudo pacman -S curl libarchive coreutils gawk sudo
```

## 📦 安装和使用

### 快速安装

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### 逐步安装

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### 更新

重新运行安装脚本即可。

### 卸载

```bash
./antigravity-installer.sh --uninstall
```

---

# Español

## ✨ Características

| Característica | Descripción |
|----------------|-------------|
| 📦 **Última versión** | Descarga el último `.deb` de Antigravity directamente del repositorio APT oficial de Google |
| 🔒 **Verificación SHA256** | Valida la integridad del paquete antes de la instalación |
| 📁 **Instalación limpia** | Instala binarios en `/opt/antigravity`, enlaces simbólicos en `/usr/local/bin` |
| 🖥️ **Integración de escritorio** | Instala lanzadores `.desktop` e icono de la aplicación |
| 🛡️ **Corrección de sandbox** | Aplica automáticamente la corrección SUID para `chrome-sandbox` |
| 🔄 **Idempotente** | Volver a ejecutar el script actualiza a la última versión |
| 🗑️ **Desinstalación limpia** | El flag `--uninstall` elimina todos los archivos instalados |

## 🔧 Cambios respecto al original

Este fork incluye las siguientes mejoras respecto a [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch):

| # | Cambio | Detalles |
|---|--------|----------|
| 1 | **`sudo` añadido a la verificación de dependencias** | El script original no verificaba la disponibilidad de `sudo` antes de las operaciones privilegiadas. Este fork lo comprueba de antemano. |
| 2 | **Parser APT tolerante a EOF** | El parser `awk` original solo capturaba metadatos cuando las entradas estaban separadas por líneas en blanco. Si el archivo `Packages` terminaba sin línea en blanco final, la última entrada se omitía. Este fork maneja correctamente esos casos. |

## 📋 Requisitos

| Utilidad | Paquete (pacman) | Propósito |
|----------|------------------|-----------|
| `curl` | `curl` | Descargar paquetes e índice |
| `bsdtar` | `libarchive` | Extraer archivos `.deb` |
| `sha256sum` | `coreutils` | Verificar integridad |
| `awk` | `gawk` | Parsear índice APT |
| `sudo` | `sudo` | Instalar con privilegios root |

```bash
sudo pacman -S curl libarchive coreutils gawk sudo
```

## 📦 Instalación y uso

### Instalación rápida

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### Paso a paso

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### Actualizar

Vuelva a ejecutar el script instalador.

### Desinstalar

```bash
./antigravity-installer.sh --uninstall
```

---

# Français

## ✨ Fonctionnalités

| Fonctionnalité | Description |
|----------------|-------------|
| 📦 **Dernière version** | Récupère le dernier `.deb` d'Antigravity directement depuis le dépôt APT officiel de Google |
| 🔒 **Vérification SHA256** | Valide l'intégrité du paquet avant l'installation |
| 📁 **Installation propre** | Installe les binaires dans `/opt/antigravity`, lien symbolique dans `/usr/local/bin` |
| 🖥️ **Intégration bureau** | Installe les lanceurs `.desktop` et l'icône de l'application |
| 🛡️ **Correction sandbox** | Applique automatiquement le correctif SUID pour `chrome-sandbox` |
| 🔄 **Idempotent** | Relancer le script met à jour vers la dernière version |
| 🗑️ **Désinstallation propre** | Le drapeau `--uninstall` supprime tous les fichiers installés |

## 🔧 Modifications par rapport à l'original

Ce fork inclut les améliorations suivantes par rapport à [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch) :

| # | Modification | Détails |
|---|-------------|----------|
| 1 | **`sudo` ajouté à la vérification des dépendances** | Le script original ne vérifiait pas la disponibilité de `sudo` avant les opérations privilégiées. Ce fork le vérifie en amont. |
| 2 | **Parseur APT tolérant à l'EOF** | Le parseur `awk` original ne capturait les métadonnées que lorsque les entrées étaient séparées par des lignes vides. Si le fichier `Packages` se terminait sans ligne vide finale, la dernière entrée était ignorée. Ce fork gère correctement ces cas. |

## 📋 Prérequis

| Utilitaire | Paquet (pacman) | Rôle |
|------------|-----------------|------|
| `curl` | `curl` | Téléchargement |
| `bsdtar` | `libarchive` | Extraction `.deb` |
| `sha256sum` | `coreutils` | Vérification d'intégrité |
| `awk` | `gawk` | Analyse de l'index APT |
| `sudo` | `sudo` | Installation avec privilèges root |

```bash
sudo pacman -S curl libarchive coreutils gawk sudo
```

## 📦 Installation et utilisation

### Installation rapide

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### Étape par étape

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### Mettre à jour

Relancez simplement le script d'installation.

### Désinstaller

```bash
./antigravity-installer.sh --uninstall
```

---

# Deutsch

## ✨ Funktionen

| Funktion | Beschreibung |
|----------|-------------|
| 📦 **Neueste Version** | Lädt das neueste Antigravity `.deb` direkt aus dem offiziellen Google APT-Repository |
| 🔒 **SHA256-Verifizierung** | Überprüft die Paketintegrität vor der Installation |
| 📁 **Saubere Installation** | Installiert Binärdateien nach `/opt/antigravity`, Symlink in `/usr/local/bin` |
| 🖥️ **Desktop-Integration** | Installiert `.desktop`-Launcher und Anwendungssymbol |
| 🛡️ **Sandbox-Fix** | Wendet automatisch den SUID-Fix für `chrome-sandbox` an |
| 🔄 **Idempotent** | Erneutes Ausführen aktualisiert auf die neueste Version |
| 🗑️ **Saubere Deinstallation** | `--uninstall` entfernt alle installierten Dateien |

## 🔧 Änderungen gegenüber dem Original

Dieser Fork enthält folgende Verbesserungen gegenüber [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch):

| # | Änderung | Details |
|---|---------|----------|
| 1 | **`sudo` zur Abhängigkeitsprüfung hinzugefügt** | Das Originalskript prüfte nicht, ob `sudo` verfügbar ist. Dieser Fork prüft dies vorab und gibt eine klare Fehlermeldung aus. |
| 2 | **EOF-toleranter APT-Index-Parser** | Der ursprüngliche `awk`-Parser erfasste Paketmetadaten nur bei leerzeilen-getrennten Einträgen. Wenn die `Packages`-Datei ohne abschließende Leerzeile endete, wurde der letzte Eintrag übersprungen. Dieser Fork behandelt diesen Fall korrekt. |

## 📋 Voraussetzungen

| Werkzeug | Paket (pacman) | Zweck |
|----------|----------------|-------|
| `curl` | `curl` | Downloads |
| `bsdtar` | `libarchive` | `.deb`-Extraktion |
| `sha256sum` | `coreutils` | Integritätsprüfung |
| `awk` | `gawk` | APT-Index-Analyse |
| `sudo` | `sudo` | Installation mit Root-Rechten |

```bash
sudo pacman -S curl libarchive coreutils gawk sudo
```

## 📦 Installation und Verwendung

### Schnellinstallation

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### Schritt für Schritt

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### Aktualisieren

Führen Sie das Installationsskript erneut aus.

### Deinstallieren

```bash
./antigravity-installer.sh --uninstall
```

---

# 日本語

## ✨ 機能

| 機能 | 説明 |
|------|------|
| 📦 **最新ビルド** | Google 公式 APT リポジトリから最新の Antigravity `.deb` を直接取得 |
| 🔒 **SHA256 検証** | インストール前にパッケージの整合性を検証 |
| 📁 **クリーンインストール** | バイナリを `/opt/antigravity` に、シンボリックリンクを `/usr/local/bin` に配置 |
| 🖥️ **デスクトップ統合** | `.desktop` ランチャーとアプリケーションアイコンをインストール |
| 🛡️ **サンドボックス修正** | `chrome-sandbox` の SUID 修正を自動適用 |
| 🔄 **べき等性** | スクリプトを再実行すると最新バージョンに更新 |
| 🗑️ **クリーンアンインストール** | `--uninstall` フラグですべてのファイルを削除 |

## 🔧 オリジナルからの変更点

このフォークは [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch) に以下の改善を加えています：

| # | 変更 | 詳細 |
|---|------|------|
| 1 | **依存関係チェックに `sudo` を追加** | オリジナルは特権操作の前に `sudo` の存在を確認していませんでした。このフォークは事前にチェックし、明確なエラーメッセージを表示します。 |
| 2 | **EOF 耐性のある APT インデックスパーサー** | オリジナルの `awk` パーサーは空行で区切られたエントリのみを処理していました。`Packages` ファイルが末尾の空行なしで終わった場合、最後のエントリがスキップされていました。このフォークはそのケースを正しく処理します。 |

## 📋 必要条件

| ツール | パッケージ (pacman) | 用途 |
|--------|-------------------|------|
| `curl` | `curl` | ダウンロード |
| `bsdtar` | `libarchive` | `.deb` 展開 |
| `sha256sum` | `coreutils` | 整合性検証 |
| `awk` | `gawk` | APT インデックス解析 |
| `sudo` | `sudo` | root 権限でのインストール |

```bash
sudo pacman -S curl libarchive coreutils gawk sudo
```

## 📦 インストールと使い方

### クイックインストール

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### ステップバイステップ

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### 更新

インストールスクリプトを再実行してください。

### アンインストール

```bash
./antigravity-installer.sh --uninstall
```

---

# 한국어

## ✨ 기능

| 기능 | 설명 |
|------|------|
| 📦 **최신 빌드** | Google 공식 APT 저장소에서 최신 Antigravity `.deb`를 직접 가져옴 |
| 🔒 **SHA256 검증** | 설치 전 패키지 무결성 검증 |
| 📁 **클린 설치** | 바이너리를 `/opt/antigravity`에, 심볼릭 링크를 `/usr/local/bin`에 설치 |
| 🖥️ **데스크톱 통합** | `.desktop` 런처와 앱 아이콘 설치 |
| 🛡️ **샌드박스 수정** | `chrome-sandbox` SUID 수정 자동 적용 |
| 🔄 **멱등성** | 스크립트를 다시 실행하면 최신 버전으로 업데이트 |
| 🗑️ **클린 제거** | `--uninstall` 플래그로 모든 파일 제거 |

## 🔧 원본과의 차이점

이 포크는 [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch) 대비 다음과 같은 개선 사항을 포함합니다:

| # | 변경 | 세부 사항 |
|---|------|----------|
| 1 | **의존성 검사에 `sudo` 추가** | 원본 스크립트는 권한 있는 작업 전에 `sudo`의 가용성을 확인하지 않았습니다. 이 포크는 사전에 확인하여 명확한 오류 메시지를 제공합니다. |
| 2 | **EOF 허용 APT 인덱스 파서** | 원본 `awk` 파서는 빈 줄로 구분된 항목만 처리했습니다. `Packages` 파일이 마지막 빈 줄 없이 끝나면 마지막 항목이 무시되었습니다. 이 포크는 해당 경우를 올바르게 처리합니다. |

## 📋 요구 사항

| 도구 | 패키지 (pacman) | 용도 |
|------|----------------|------|
| `curl` | `curl` | 다운로드 |
| `bsdtar` | `libarchive` | `.deb` 추출 |
| `sha256sum` | `coreutils` | 무결성 검증 |
| `awk` | `gawk` | APT 인덱스 파싱 |
| `sudo` | `sudo` | root 권한 설치 |

```bash
sudo pacman -S curl libarchive coreutils gawk sudo
```

## 📦 설치 및 사용법

### 빠른 설치

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### 단계별 설치

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### 업데이트

설치 스크립트를 다시 실행하세요.

### 제거

```bash
./antigravity-installer.sh --uninstall
```

---

# Português

## ✨ Recursos

| Recurso | Descrição |
|---------|----------|
| 📦 **Versão mais recente** | Baixa o último `.deb` do Antigravity diretamente do repositório APT oficial do Google |
| 🔒 **Verificação SHA256** | Valida a integridade do pacote antes da instalação |
| 📁 **Instalação limpa** | Instala binários em `/opt/antigravity`, links simbólicos em `/usr/local/bin` |
| 🖥️ **Integração com desktop** | Instala lançadores `.desktop` e ícone do aplicativo |
| 🛡️ **Correção de sandbox** | Aplica automaticamente a correção SUID para `chrome-sandbox` |
| 🔄 **Idempotente** | Reexecutar o script atualiza para a versão mais recente |
| 🗑️ **Desinstalação limpa** | O flag `--uninstall` remove todos os arquivos instalados |

## 🔧 Alterações em relação ao original

Este fork inclui as seguintes melhorias em relação ao [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch):

| # | Alteração | Detalhes |
|---|----------|----------|
| 1 | **`sudo` adicionado à verificação de dependências** | O script original não verificava a disponibilidade do `sudo` antes de operações privilegiadas. Este fork verifica antecipadamente. |
| 2 | **Parser APT tolerante a EOF** | O parser `awk` original só capturava metadados quando as entradas eram separadas por linhas em branco. Se o arquivo `Packages` terminasse sem linha em branco final, a última entrada era ignorada. Este fork trata corretamente esses casos. |

## 📋 Requisitos

| Utilitário | Pacote (pacman) | Finalidade |
|------------|-----------------|------------|
| `curl` | `curl` | Downloads |
| `bsdtar` | `libarchive` | Extração `.deb` |
| `sha256sum` | `coreutils` | Verificação de integridade |
| `awk` | `gawk` | Análise do índice APT |
| `sudo` | `sudo` | Instalação com privilégios root |

```bash
sudo pacman -S curl libarchive coreutils gawk sudo
```

## 📦 Instalação e uso

### Instalação rápida

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### Passo a passo

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### Atualizar

Execute novamente o script de instalação.

### Desinstalar

```bash
./antigravity-installer.sh --uninstall
```

---

# हिन्दी

## ✨ विशेषताएँ

| विशेषता | विवरण |
|----------|-------|
| 📦 **नवीनतम बिल्ड** | Google के आधिकारिक APT रिपॉजिटरी से सीधे नवीनतम Antigravity `.deb` डाउनलोड करता है |
| 🔒 **SHA256 सत्यापन** | इंस्टालेशन से पहले पैकेज की अखंडता की जाँच करता है |
| 📁 **क्लीन इंस्टॉल** | बाइनरी को `/opt/antigravity` में, सिमलिंक `/usr/local/bin` में इंस्टॉल करता है |
| 🖥️ **डेस्कटॉप इंटीग्रेशन** | `.desktop` लॉन्चर और ऐप आइकन इंस्टॉल करता है |
| 🛡️ **सैंडबॉक्स फिक्स** | `chrome-sandbox` के लिए SUID फिक्स स्वचालित रूप से लागू करता है |
| 🔄 **इडेम्पोटेंट** | स्क्रिप्ट को दोबारा चलाने पर नवीनतम संस्करण में अपडेट होता है |
| 🗑️ **क्लीन अनइंस्टॉल** | `--uninstall` फ्लैग सभी इंस्टॉल की गई फाइलें हटाता है |

## 🔧 मूल से अंतर

इस फोर्क में [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch) की तुलना में निम्नलिखित सुधार शामिल हैं:

| # | परिवर्तन | विवरण |
|---|----------|-------|
| 1 | **डिपेंडेंसी चेक में `sudo` जोड़ा** | मूल स्क्रिप्ट प्रिविलेज्ड ऑपरेशन से पहले `sudo` की उपलब्धता की जाँच नहीं करती थी। यह फोर्क पहले से जाँच करता है। |
| 2 | **EOF-सहिष्णु APT इंडेक्स पार्सर** | मूल `awk` पार्सर केवल खाली पंक्तियों से अलग किए गए प्रविष्टियों को प्रोसेस करता था। यह फोर्क EOF-टर्मिनेटेड केस को सही ढंग से हैंडल करता है। |

## 📦 इंस्टालेशन

### त्वरित इंस्टालेशन

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### चरण-दर-चरण

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### अनइंस्टॉल

```bash
./antigravity-installer.sh --uninstall
```

---

# العربية

## ✨ المميزات

| الميزة | الوصف |
|--------|-------|
| 📦 **أحدث إصدار** | يجلب أحدث `.deb` من Antigravity مباشرة من مستودع Google APT الرسمي |
| 🔒 **تحقق SHA256** | يتحقق من سلامة الحزمة قبل التثبيت |
| 📁 **تثبيت نظيف** | يثبت الملفات التنفيذية في `/opt/antigravity`، روابط رمزية في `/usr/local/bin` |
| 🖥️ **تكامل سطح المكتب** | يثبت مشغلات `.desktop` وأيقونة التطبيق |
| 🛡️ **إصلاح sandbox** | يطبق إصلاح SUID لـ `chrome-sandbox` تلقائياً |
| 🔄 **متساوي القوة** | إعادة تشغيل السكريبت يحدّث إلى أحدث إصدار |
| 🗑️ **إزالة نظيفة** | علامة `--uninstall` تزيل جميع الملفات المثبتة |

## 🔧 التغييرات عن الأصل

يتضمن هذا الفورك التحسينات التالية مقارنة بـ [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch):

| # | التغيير | التفاصيل |
|---|---------|----------|
| 1 | **إضافة `sudo` لفحص المتطلبات** | السكريبت الأصلي لم يتحقق من توفر `sudo` قبل العمليات المميزة. هذا الفورك يتحقق مسبقاً. |
| 2 | **محلل فهرس APT متسامح مع EOF** | محلل `awk` الأصلي كان يعالج فقط الإدخالات المفصولة بأسطر فارغة. هذا الفورك يعالج حالات نهاية الملف بشكل صحيح. |

## 📦 التثبيت

### تثبيت سريع

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### خطوة بخطوة

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### إلغاء التثبيت

```bash
./antigravity-installer.sh --uninstall
```

---

# Türkçe

## ✨ Özellikler

| Özellik | Açıklama |
|---------|----------|
| 📦 **En son sürüm** | Google'ın resmi APT deposundan en son Antigravity `.deb` paketini indirir |
| 🔒 **SHA256 doğrulama** | Kurulumdan önce paket bütünlüğünü doğrular |
| 📁 **Temiz kurulum** | İkili dosyaları `/opt/antigravity`'ye, sembolik bağlantıyı `/usr/local/bin`'e kurar |
| 🖥️ **Masaüstü entegrasyonu** | `.desktop` başlatıcıları ve uygulama simgesini kurar |
| 🛡️ **Sandbox düzeltmesi** | `chrome-sandbox` için SUID düzeltmesini otomatik uygular |
| 🔄 **İdempotent** | Betiği yeniden çalıştırmak en son sürüme günceller |
| 🗑️ **Temiz kaldırma** | `--uninstall` bayrağı tüm kurulu dosyaları kaldırır |

## 🔧 Orijinalden farklar

Bu fork, [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch)'a kıyasla şu iyileştirmeleri içerir:

| # | Değişiklik | Ayrıntılar |
|---|-----------|------------|
| 1 | **Bağımlılık kontrolüne `sudo` eklendi** | Orijinal betik, ayrıcalıklı işlemlerden önce `sudo`'nun mevcut olup olmadığını kontrol etmiyordu. Bu fork önceden kontrol eder. |
| 2 | **EOF'a toleranslı APT indeks ayrıştırıcı** | Orijinal `awk` ayrıştırıcı yalnızca boş satırlarla ayrılmış girdileri işliyordu. Bu fork dosya sonu durumlarını doğru şekilde ele alır. |

## 📦 Kurulum

### Hızlı kurulum

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### Adım adım

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### Kaldırma

```bash
./antigravity-installer.sh --uninstall
```

---

# Italiano

## ✨ Funzionalità

| Funzionalità | Descrizione |
|-------------|----------|
| 📦 **Ultima versione** | Scarica l'ultimo `.deb` di Antigravity direttamente dal repository APT ufficiale di Google |
| 🔒 **Verifica SHA256** | Verifica l'integrità del pacchetto prima dell'installazione |
| 📁 **Installazione pulita** | Installa i binari in `/opt/antigravity`, link simbolico in `/usr/local/bin` |
| 🖥️ **Integrazione desktop** | Installa i launcher `.desktop` e l'icona dell'applicazione |
| 🛡️ **Fix sandbox** | Applica automaticamente il fix SUID per `chrome-sandbox` |
| 🔄 **Idempotente** | Rieseguire lo script aggiorna all'ultima versione |
| 🗑️ **Disinstallazione pulita** | Il flag `--uninstall` rimuove tutti i file installati |

## 🔧 Modifiche rispetto all'originale

Questo fork include i seguenti miglioramenti rispetto a [apipa12/antigravity-arch](https://github.com/apipa12/antigravity-arch):

| # | Modifica | Dettagli |
|---|---------|----------|
| 1 | **`sudo` aggiunto al controllo dipendenze** | Lo script originale non verificava la disponibilità di `sudo` prima delle operazioni privilegiate. Questo fork lo verifica in anticipo. |
| 2 | **Parser indice APT tollerante all'EOF** | Il parser `awk` originale catturava i metadati solo quando le voci erano separate da righe vuote. Se il file `Packages` terminava senza riga vuota finale, l'ultima voce veniva ignorata. Questo fork gestisce correttamente questi casi. |

## 📦 Installazione

### Installazione rapida

```bash
curl -fsSL https://raw.githubusercontent.com/Yudaev/antigravity-arch/main/antigravity-installer.sh | bash
```

### Passo dopo passo

```bash
git clone https://github.com/Yudaev/antigravity-arch.git
cd antigravity-arch
chmod +x antigravity-installer.sh
./antigravity-installer.sh
```

### Disinstallare

```bash
./antigravity-installer.sh --uninstall
```

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
