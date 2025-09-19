#  Развертывание сайта Hugo через GitHub Actions

## Пошаговая инструкция

### 1. Создать репозиторий
Создать новый репозиторий на GitHub с именем:  
`username.github.io` (где `username` - ваш логин GitHub)

### 2. Клонировать репозиторий
```bash
git clone https://github.com/username/username.github.io.git
cd username.github.io
```

### 3. Добавить удаленный репозиторий
```bash
git remote add origin git@github.com:username/username.github.io.git
```

### 4. Настроить workflow
Создать файл `.github/workflows/hugo.yaml`:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### 5. Настроить config.toml
```toml
baseURL = 'https://Antero2113.github.io/'  
languageCode = 'ru-ru'
title = ' '
theme = "blowfish"

[markup]
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true

[params]
  defaultAppearance = "light"
  autoSwitchAppearance = true

ignoreLogs = ['warning-goldmark-raw-html']
```

### 6. Добавить .gitignore
Создать файл `.gitignore`:
```
/public/
/resources/_gen/
/assets/jsconfig.json
.hugo_build.lock
```

### 7. Запушить код
```bash
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

### 8. Активировать GitHub Pages
1. Перейти в **Settings** → **Pages**
2. Выбрать **Source**: **GitHub Actions**
3. Сохранить настройки




