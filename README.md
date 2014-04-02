# Ruby on Rails Juniors

## Введение в профессиональную разработку на Ruby on Rails

#### Начало работы

Последовательность команд для установки Ruby on Rails на Ubuntu:

```bash
sudo apt-get update -y
sudo apt-get install build-essential bison openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-0 libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev nodejs -y
curl -L get.rvm.io | bash -s stable
source ~/.rvm/scripts/rvm
rvm install 2.1.1
rvm use 2.1.1 --default
gem install rails --no-rdoc --no-ri
```

После этого становится доступной команда создания нового rails-приложения `rails new {applicaion name}`. Затем необходимо перейти в каталог созданного приложения и запустить rails-сервер командой `rails s`. После этого приложение становится доступным в браузере на [localhost:3000](http://localhost:3000).

Пример создания приложения `first_app` и запуска его сервера:

```bash
rails new first_app
cd first_app
rails s
```

Чтобы запустить сервер на другом порте, необходимо указать номер порта с ключом `-p`:
```
rails s -p 5555
```
запустит сервер на порте 5555, и в браузере приложение нужно будет искать на [localhost:5555](http://localhost:5555).
