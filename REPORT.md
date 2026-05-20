## Laboratory work IX

Данная лабораторная работа посвещена изучению процесса создания артефактов на примере **Github Release**

```sh
$ open https://help.github.com/articles/creating-releases/
```

## Tutorial

```sh
$ export GITHUB_TOKEN=<полученный_токен>
$ export GITHUB_USERNAME=<имя_пользователя>
$ export PACKAGE_MANAGER=<пакетный менеджер>
$ export GPG_PACKAGE_NAME=<gpg2|gpg>
```

```sh
# for *-nix system
$ $PACKAGE_MANAGER install xclip
$ alias gsed=sed
$ alias pbcopy='xclip -selection clipboard'
$ alias pbpaste='xclip -selection clipboard -o'
```

```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
$ go get github.com/aktau/github-release
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab08 projects/lab09
$ cd projects/lab09
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab09
```

```sh
$ gsed -i 's/lab08/lab09/g' README.md
```

```sh
$ $PACKAGE_MANAGER install ${GPG_PACKAGE_NAME}
$ gpg --list-secret-keys --keyid-format LONG
$ gpg --full-generate-key
$ gpg --list-secret-keys --keyid-format LONG
$ gpg -K ${GITHUB_USERNAME}
$ GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
$ GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
$ gpg --armor --export ${GPG_KEY_ID} | pbcopy
$ pbpaste
$ open https://github.com/settings/keys
$ git config user.signingkey ${GPG_SEC_KEY_ID}
$ git config gpg.program gpg
```

```sh
$ test -r ~/.bash_profile && echo 'export GPG_TTY=$(tty)' >> ~/.bash_profile
$ echo 'export GPG_TTY=$(tty)' >> ~/.profile
```

```sh
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
PACK_GENERATOR="TGZ"
-- Configuring done (0.1s)
-- Generating done (0.0s)
-- Build files have been written to: /home/yulia/yuliyavroma-spec/workspace/projects/lab09/_build

$ cmake --build _build --target package
[ 25%] Building CXX object CMakeFiles/print.dir/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
[ 75%] Building CXX object CMakeFiles/demo.dir/demo.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /home/yulia/yuliyavroma-spec/workspace/projects/lab09/_build/print-0.1.0-Linux-x86_64.tar.gz generated.

```

```sh
$ mkdir -p .github/workflows
$ cat > .github/workflows/release.yml << 'EOF'
name: Create Release

on:
  push:
    tags:
      - 'v*'  

permissions:
  contents: write  

jobs:
  release:
    name: Create GitHub Release
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  
      
      - name: Build project
        run: |
          
          mkdir -p build
          cd build
          cmake ..
          make
          make package
          ls -la *.tar.gz
      
      - name: Create Release and Upload Assets
        uses: softprops/action-gh-release@v2
        with:
          name: "Release ${{ github.ref_name }}"
          body: |
            
          files: |
            build/*.tar.gz
          draft: false
          prerelease: false
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
EOF
$ git add .github/workflows/release.yml
$ git commit -m "Add GitHub Actions workflow for automated releases"
$ git push origin master
```

```sh
$ git tag -s v0.1.0.0
$ git tag -v v0.1.0.0
$ git show v0.1.0.0
$ git push -u  origin master --tags
Перечисление объектов: 9, готово.
Подсчет объектов: 100% (9/9), готово.
При сжатии изменений используется до 2 потоков
Сжатие объектов: 100% (4/4), готово.
Запись объектов: 100% (5/5), 776 байтов | 258.00 КиБ/с, готово.
Total 5 (delta 2), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To github.com:yuliyavroma-spec/lab09.git
   a3cecb8..f36eef1  master -> master

```

```sh
$ sudo apt install gh
[sudo] пароль для yulia: 
Следующие пакеты устанавливались автоматически и больше не требуются:
  libwoff1  linux-image-6.12.63+deb13-amd64
Для их удаления используйте «sudo apt autoremove».

Установка:
  gh

Сводка:
  Обновление: 0, Установка: 1, Удаление: 0, Пропуск обновления: 54
  Объём загрузки: 7 616 kB
  Требуемое пространство: 35,5 MB / 8 424 MB доступно

Пол:1 http://deb.debian.org/debian trixie/main amd64 gh amd64 2.46.0-3 [7 616 kB]
Получено 7 616 kB за 41с (186 kB/s)                            
Выбор ранее не выбранного пакета gh.
(Чтение базы данных … на данный момент установлен 178891 файл и каталог.)
Подготовка к распаковке …/archives/gh_2.46.0-3_amd64.deb …
Распаковывается gh (2.46.0-3) …
Настраивается пакет gh (2.46.0-3) …
Обрабатываются триггеры для man-db (2.13.1-1) …
$ gh --version
gh version 2.46.0 (2025-01-13 Debian 2.46.0-3)
https://github.com/cli/cli/releases/tag/v2.46.0
$ gh auth login
$ gh release create v0.1.0.0 \
  --title "libprint" \
  --notes "my first release"
https://github.com/yuliyavroma-spec/lab09/releases/tag/v0.1.0.0
$ gh release upload v0.1.0.0 build/*.tar.gz --clobber
Successfully uploaded 1 asset to v0.1.0.0

```

