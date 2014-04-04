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

#### Getting started: приступая к работе
В этом практическом задании рассмотрено большое количество очень разных вопросов, объединенных, тем не менее, плотным смысловым единством _(состоящим, пожалуй, в том, без всех этих вещей ни с одним rails-приложением нельзя сделать почти ничего)_:
- **Bundler** - пакетный менеджер Rails;
- язык описания представлений (views) **Slim**;
- **Twitter Bootstrap** для **Asset pipeline**;
- контроллер статических страниц;
- введение в роутинг;
- первое знакомство с хелперами.

В качестве бонуса освоим работу с хелпером `content_for`, который вовсе не так общезначим, но может иногда пригодиться.

Устанавливаем slim — в `Gemfile` добавляем
```ruby
gem 'slim-rails'
```
В каталоге приложения запускаем команду
```bash
bundle install
```
Впрочем, install — это наиболее востребованная операция Bundler'a, которая делается им по умолчанию, поэтому `bundle install`, как правило сокращается просто до
```
bundle
```
Это нужно делать всегда после обновления `Gemfile`, больше не будем на этом останавливаться.

Теперь мы можем создавать вьюхи на slim'e (Постоянно держа под рукой его [мануал](http://rdoc.info/gems/slim/frames)). Кроме того, теперь, когда мы выбрали язык описания представлений, можно установить [**Twitter Bootstrap**](https://github.com/seyhunak/twitter-bootstrap-rails).

Читаем документацию в readme по ссылке _(привыкаем к этому, это теперь на всю профессиональную жизнь в Rails)_, осмысливаем и делаем только то, что нам нужно:

Добавляем в `Gemfile`:
```ruby
gem 'therubyracer'
gem 'less-rails'
gem 'twitter-bootstrap-rails'
```
Затем запустим в каталоге приложения генераторы необходимых стилевых таблиц
```
rails generate bootstrap:install less
```
Теперь можно бегло глянуть `application.css` и `application.js`, чтобы примерно понять, как работает **Asset pipeline**, а позже обязательно внимательно прочитать [документацию](http://guides.rubyonrails.org/asset_pipeline.html).

Тем не менее, не отвлекаемся сильно и создаем главный layout приложения (здесь и далее: команда `rails` всегда выполняется в каталоге приложения):
```
rails g bootstrap:layout application fluid
```
Удаляем старый ужасный `application.html.erb` из `/app/views/layouts/`, и, чтобы увидеть значительные изменения, создаем контроллер статических страниц, воспользовавшись диалогом RubyMine или выполнив команду
```
rails g controller Page home index about info test
```
Выполнить команду, пожалуй, даже интереснее — ознакомление со списком созданных файлов в этом случае происходит более наглядно.

Теперь пора познакомиться с системой автоматической сборки кода **Rake** и запросить у нее список обслуживаемых приложением путей (здесь и далее: команда `rake` точно так же всегда запускается в каталоге приложения):
```
rake routes
```
Теперь на [localhost:3000](http://localhost:3000) нам открыты все пути, перечисленные в `rake routes`! _Если не открыты, запустите сервер и не забывайте его включать и выключать по необходимости — это тоже очень важный навык._

Однако в самом корне [localhost:3000](http://localhost:3000) пока еще стартовая страница по умолчанию.

В `routes.rb` (который находится в папке `/config` приложения) пишем
```ruby
root 'page#home'
```
или, для большего соответствия с полным синтаксисом `routes.rb`
```ruby
root to: 'page#home'
```
Работать будет и так, и так — делайте выбор из ваших синтаксических предпочтений. С помощью команды `root` можно направить корневой адрес приложения любому действию (action) любого контроллера, указав их в _(желательно одинарных)_ кавычках в формате `:controller#:action`.

Полностью команда настройки статического пути выглядит так:
```ruby
get '{:path}', to: ':controller#:action', as: {:alias}
```
Рассмотрим на примере:
```ruby
get 'page', to: 'page#index', as: :page
```
Все примерно понятно и знакомо, но зачем же alias? Снова запустим `rake routes` и найдем все элиасы в первой колонке таблицы, озаглавленном **Prefix**.
Теперь в `application.html.slim` (который, мы помним, в `/app/views/layouts`) найдем ссылки в шапке сайта и поправим их в соответствии со структурой нашего приложения:
```ruby
li = link_to 'Pages index', :page
```
Первый и, пожалуй, самый важный встреченный нами хелпер _**(ruby-функция rails, генерирующая html-код на основании переданных параметров)**_ `link_to` применяется, очевидно, для описания ссылок и первым параметром принимает текст или html-код отображения ссылки, вторым — адрес, на который будет указывать ссылка. Далее можно будет указать дополнительные параметры, как то `style:` или `class:`, но об этом позже. Адрес ссылки можно задать, помимо уже известного, еще двумя способами:
```ruby
li = link_to 'Pages index', page_path
```
и
```ruby
li = link_to 'Pages index', page_url
```
Встроенные методы rails `{:prefix}_path` и `{:prefix}_url` создадут соответственно относительный от корня сайта адрес страницы и абсолютный url, включающий адрес сайта. На локалхосте разницу, конечно, не заметить, но знать это надо. Вот мы и разобрались с префиксами роутов.

Продолжаем. В `routes.rb` пишем что-то вроде
```ruby
get 'any/other/page/anywhere/at/your/site', to: 'page#info', as: :info
```
в `application.html.slim`
```ruby
li = link_to 'Cool page', :info
```
Обновляем страничку, давим ссылку, читаем url в браузере и наслаждаемся полнотой своего понимания статического роутинга.

Теперь самое время заменить ужасную html-ную ссылку на главную страницу, которая, ко всем прочим недостаткам, из коробки `twitter bootstrap gem` еще и не работает (видимо, по каким-то серьезным идейным соображениям). Меняем
```
a.brand href="#"{:app_name}
```
на
```ruby
= link_to 'Practice', :root, class: %i[brand]
```
И запоминаем на всю жизнь, что тегов `a` во вьюхах рельсов **не бывает**. Для этого мы только что с таким трудом изучали соответствующий хелпер.

Еще об одном хелпере — `content_for`. В `application.html.slim` есть многообещающее
```ruby
title = content_for?(:title) ? yield(:title) : "Practice"
```
Как же задать этот title? _(использованный здесь тернарный оператор, конечно же, всем знаком)_ В любой взятой наугад вьюхе, например, `app/views/page/index.html.slim`, лучше всего в самой первой строчке, пишем:
```ruby
- content_for :title
  | Проникновенный и точный заголовок индексной страницы по умолчанию
```
И это, собственно, все. Сравниваем с заголовками вьюх, где `content_for :title` не определен и озаряемся пониманием.

Для закрепления поменяем последнюю ссылку в сайдбаре на
```ruby
li = content_for?(:sidebar) ? yield(:sidebar) : link_to("Link 3", "/path3")
```
Соответственно, во вьюхе, в которой мы хотим заменять эту ссылку, пишем, например:
```ruby
- content_for :sidebar
  - link_to 'Home Sweet Home', :root, style: 'color:green'
```
Вот, собственно, и весь `content_for` — второй хелпер, рассмотренный в этом курсе. Их еще много, и пора переходить к следующему занятию, где мы поближе познакомимся со странными двоеточими в начале некоторых слов, имеющими прямое отношение к важным различиям между символами и строками в ruby, и, возможно, даже с процентными литералами.

Можно пока остальные ссылки в лэйауте приложения допилить и все `content_for` снести. Ну, или, наоборот, добавить, если эффект понравился.

#### Getting continued: немного Ruby и FizzBuzz-тест (beginner & NIGHTMARE)

В ходе этой небольшой, но важной работы мы
- сформулируем самое общее представление о Ruby как о языке программирования;
- узнаем самые важные вещи об объектной модели Ruby;
- выделим самый важный класс объектов не только Ruby, а, возможно, всей Computer science;
- разберемся наконец с разницей между символами и строками в Ruby;
- сделаем простейшую реализацию принципа duck typing;
- слегка прикоснемся к магии Ruby — доброй и не очень;
- немного затронем регулярные выражения и процентные литералы;
- **а также проверим свои навыки постановки и решения прикладных задач**.

Запустим рельсовую консоль (здесь и далее: в каталоге приложения `rails c`) и запишем — вот прямо подряд:
```ruby
class String
def palindrome?
self == self.reverse
end
end
```
Проверяем:
```ruby
'deified'.palindrome?
```
```ruby
'olol'.palindrome?
```
```ruby
'ololo'.palindrome?
```
Смотрим и делаем ряд важных выводов:
- Ruby — один из самых объектно ориентированных языков. Объекты здесь везде;
- Ruby way — это всегда вызов метода объекта на объекте через точку;
- в Ruby можно дополнять описания встроенных системных классов;
- в Ruby принято дополнять имена проверяющих функций вопросительным знаком (привет `content_for?`!);
- _к слову: методы с восклицательным знаком, как правило, изменяют объект, на котором они вызваны;_
- метод возвращает последнее упомянутое в коде значение, `return` нужно использовать только тогда, когда код сложный и есть вероятность возникновения путаницы.

А также несколько не очень важных:
- рельсовая консоль красиво расставляет отступы;
- а в случае использования гема `pry-rails` еще и делает красивую подсветку синтаксиса.

Кто еще не поставил `pry-rails`, ставит:
```ruby
gem 'pry-rails'
```
в `Gemfile`, конечно. Здесь и далее.

Осваиваем конструкцию диапазона `(a..b)`, итератор `each`, однострочный блок `{ |variable| code(variable) }` и команду вывода `puts`:
```ruby
def count_to(limit)
  (1..limit).each { |i| puts i }
end
```
Вызов, само собой:
```ruby
count_to(10)
```
или даже
```ruby
count_to 10
```
Это есть такой в Ruby синтаксический сахар — передача аргументов методу без скобок. Удобно иногда, хотя лучше этим не увлекаться — от такого сахара по сути правильный код иногда вдруг внезапно перестает работать (обратите внимание, что в примере с `content_for :sidebar` аргументы `link_to` пришлось взять в скобки, и неспроста).

Еще немного:
```ruby
def count_to(limit)
  (1..limit).each do |i|
    print i
  end
end
```
Это, соответственно, многострочный блок и команда `print`.

Чтобы чуть облагородить вывод команды `print`, освоим интерполируемые строки (это которые в двойных кавычках, в отличие от неинтерполируемых в одинарных — знакомая во многих языках практика). К слову о культуре кода, следите постоянно за тем, чтобы в двойных кавычках обязательно происходила какая-нибудь интерполяция. Если ее нет, одинарные кавычки обязательны. Код читать будет намного удобнее.

Итак:
```ruby
def count_to(limit)
  (1..limit).each { |i| print "#{i}\n" }
end
```

Сформируем массив значений:
```ruby
def count_to(limit)
  (1..limit).map { |i| i }
end
```

Вот еще красивая практика для сокращения записи одного подвида тернарных операторов:
```ruby
['Fizz'][0]
```
```ruby
['Fizz'][1]
```
```ruby
['Fizz'][100500]
```
Все это значит, что в Ruby первый индекс массива — 0, а обращение к элементу массива с несуществующим индексом не выдает ошибки, а только `nil` — отсутствующее значение. Разовьем мысль:
```ruby
"#{['Fizz'][0]}"
```
```ruby
"#{['Fizz'][100500]}"
```
К слову. Метод `blank?` проверяет, является ли строка пустой. Еще к слову: если вы хотите вызвать какой-либо метод на объекте, генерируемом каким-либо оператором, возьмите этот оператор в скобки.

Вот и все. Теперь вы готовы к тому, чтобы почти мгновенно написать красивое однострочное решение для FizzBuzz-теста на Ruby:

> Определить функцию от двух аргументов, возвращающую массив целых чисел от одного аргумента до другого, обработанных так:
> - если число делится без остатка на 3, вернуть «Fizz»;
> - если число делится без остатка на 5, вернуть «Buzz»;
> - если и на 3 и на 5, конечно же, «FizzBuzz»;
> - если число не делится ни на 3, ни на 5, вернуть само число.

_**Подсказка 1:**_ Первый аргумент не обязательно меньше второго.

_**Подсказка 2:**_ Остаток от деления i на 3 — это `i%3`. Можно пользоваться справочниками по [операторам](http://www.tutorialspoint.com/ruby/ruby_operators.htm), [циклам](http://www.tutorialspoint.com/ruby/ruby_loops.htm) и [ветвлениям](http://www.tutorialspoint.com/ruby/ruby_if_else.htm) в Ruby.

_**Подсказка 3:**_ Ни сейчас, ни когда-либо еще не используйте `for {:variable} in {:array}`. Это совсем не Ruby way, не создает локальной области видимости переменных, и вообще некрасиво.

Когда тест готов, синтаксически совершенен и абсолютно работоспособен, наступает время опустить скучные модификации вроде **FizzBuzzEazzCozzDezz** и сделать все вовсе по-взрослому. На **NIGHTMARE** мы будем делать этот тест:
- принимая массив делителей как аргумент функции;
- и представляя числа как произведения своих делителей и остатка деления.

То есть, все просто. `fizzBuzzNigthmare(1,100,3,5)` должна нам выдавать `'3x2'` на 6, `'5x2'` на 10, `'3x5'` на 15 и так далее. Отложим пока фильтрацию ввода и сделаем пока чистую реализацию алгоритма, предполагая, что в массиве делителей не будет неприятных неожиданностей. Однако обязательно подумаем об этом позже.

А сейчас необходимо освоить несколько технических моментов как Ruby, так и Rails.

Прием массива как аргумента функции с помощью `*` (заодно раскроем тему двоеточих в Ruby):
```ruby
def checkArrayArg(a, b, *c)
  puts a; puts b; puts c.inspect
end
```
Проверяем:
```ruby
checkArrayArg(1,100,-1,0,1,2,3,5,7,4,10)
```
Это Ruby, здесь просто. А вот в рельсах есть MVC с роутингом, здесь все сложнее — чтобы получить такой же REPL в rails-приложении, нужно подергать и роутинг, и контроллеры, и вьюхи.

Вернемся к чуть более простому случаю — приему обычных параметров из url.

В `routes.rb` добавляем
```ruby
get 'test/:a/:b', to: 'page#test'
```
В `page_controller.rb`:
```ruby
def test
  @a, @b = params[:a], params[:b]
end
```
В `page/test.html.slim`:
```ruby
p = @a
p = @b
```
Вот и все, [наслаждаемся](http://localhost:3000/test/first_param=1/second_param=100500)

С массивом в качестве аргумента все до обидного аналогично и тривиально:
В `routes.rb`:
```ruby
get 'test/:a/:b/*c', to: 'page#test'
```
В `page_controller.rb`:
```ruby
def test
  @a, @b, @c = params[:a], params[:b], params[:c].split('/')
end
```
В `page/test.html.slim`:
```ruby
p = @a
p = @b
p = @c.inspect
```
Можно принять несколько массивов из одного запроса, используя разделитель:

В `routes.rb`:
```ruby
get 'test/:a/:b/*c/div/*d', to: 'page#test'
```
В `page_controller.rb`:
```ruby
def test
  @a, @b, @c, @d = params[:a], params[:b], params[:c].split('/'), params[:d].split('/')
end
```
В `page/test.html.slim`:
```ruby
p = @a
p = @b
p = @c.inspect
p = @d.inspect
```
Распилив пришедший параметр по разделителю методом `split`, мы более явно, чем в случае с одиночным параметром, убедились в том, что в параметрах запроса все значения приходят строками. Чтобы получить из них целые числа, следует воспользоваться методом `to_i` (to integer). Кстати, есть еще `to_s` для приведения к строкам, `to_f` к числам с точкой. Есть много других типов и методов для приведения к ним, и их можно будет освоить самостоятельно.

Подытожим: таким образом, корректное получение числовых значений из параметров запроса будет выглядеть так:
```ruby
def test
  @a, @b, @c = params[:a].to_i, params[:b].to_i, params[:c].split('/').map(&:to_i)
end
```
Тем, кто хорошо сделал домашнее задание, уже понятно все произошедшее. Для остальных поясню, что в метод `map` мы передали так называемую лямбду — блок, который вызывает на каждом элементе обрабатываемого им блока указанный метод и возвращает результат этого метода. А `map`, в свою очередь, записывает все полученные таким образом данные в массив.

Вот, кстати, дополнительная задача для будущих суровых well-grounded рубистов:
> Добавить в системный класс Fixnum описание метода `fizzBuzz`, который будет возвращать результат FizzBuzz-преобразования и вот так вот красиво работать через лямбду:
> ```ruby
> (1..100).map(&:fizzBuzz)
> ```

У кого получилось, может считать себя прикоснувшимся к доброй магии Ruby.

Однако нам потребуется еще несколько инструментов. Осваиваем:

Выбрать элементы массива, на которых условие выполняется:
```ruby
[2,3,5,7,11].select { |i| 55%i == 0 }
```
Посчитать размер массива:
```ruby
[2,3,5,7,11].select { |i| 55%i == 0 }.size
```
Удалить элементы массива, если условие выполняется:
```ruby
[-1,0,1,2,3,5,7,11,4,6,10].delete_if { |i| i < 2 }
```
Красиво вывести массив через разделитель:
```ruby
[2,3,5,7,11,].join('x')
```
*И вообще, `Array` — один из важнейших объектов не только Ruby, но и всей Computer Science вообще. Насколько это возможно близкое знакомство с [документацией класса Array](http://www.ruby-doc.org/core-2.1.1/Array.html). То, что в списке классов `Array` стоит первым, поверьте, не случайность.*

Однако, еще немного инструкций по мелочи.

Найти наибольший обший делитель:
```ruby
55.gcd(99)
```
Еще вот прямо совсем подсказка:
```ruby
a = 1024
5.times { a /= 2 }
puts a
```

Вот и все. Теперь вас не остановит даже FizzBuzz тест на NIGHTMARE. А внимательные читатели уже готовы сделать и фильтрацию ввода тоже.

##### О символах и duck typing

Пора, однако, раз и навсегда разобраться с уже порядком намозолившими глаза символами. Символы — это неизменяемые строки Ruby, имена собственные. Названия полей, методов, всех объектов вообще, ключи в хэшах — всегда символы. Символы очень похожи на строки, и их довольно легко получить друг из друга методами `to_s` и `to_sym` соответственно.

Существенная разница между состоит в том, что выделение памяти для хранения символов более экономно, оно не предполагает, что с символом будет происходить то, что обычно часто происходит со строками — изменение, разбиение, вычленение, добавление, проверка регулярным выражением. И самое главное — что каждый символ присутствует в памяти в единственном экземпляре.

Берегите память. Если не собираетесь изменять какую-либо строку — так и скажите, что она символ. Когда ваш проект окажется под высокой нагрузкой, будете себе за это очень благодарны.

К практике. Убедимся, что этот пример выдает ошибку:
```ruby
:some_symbol.to_i
```
*(как хорошо, что у нас все на одной странице, и то, что про ошибку я сказал до приведения кода примера. Заставить этот фрагмент работать, конечно, можно, добавив в определение системного класса `Symbol` описание метода `to_i`, но здесь как раз не нужно пользоваться этой восхитительной гибкостью Ruby)*

Это означает, к примеру, что такого рода фильтрация с приведением к целым числам
```ruby
def with_filtering(a,b,*c)
  c.map!(&:to_i)
end
```
Справится и с числами с точкой, и со строками, а вот одним-разъединственным символом подавится. Что же делать? Так
```ruby
:some_symbol.to_s.to_i
```
конечно же, сработает. Но что же нам теперь, все через строки конвертировать? Нет, конечно. Есть способ понять, определен ли какой-либо метод для этого объекта или нет.
```ruby
class Object
  def integerize!
    self.respond_to?(:to_i) ? self.to_i : self.to_s.to_i
  end
end
```
Теперь приобщим к этому величию нашу функцию фильтрации:
```ruby
def with_filtering(a,b,*c)
  c.map!(&:integerize!)
end
```
Бинго! Теперь наша фильтрация не давится символами, а мы получили опыт использования принципа duck typing. Подробнее о том, что это такое, зачем это нужно и почему это важно, хотя бы [здесь](http://ru.wikipedia.org/wiki/Утиная_типизация) и, например, [здесь](http://rubylearning.com/satishtalim/duck_typing.html).

##### Не вся магия одинаково полезна
Определим функцию, которая должна возвращать единицу, так:
```ruby
def dirty_magic
  def dirty_magic
    def dirty_magic
      def dirty_magic
        def dirty_magic
          def dirty_magic
            def dirty_magic
            1
            end
          2
          end
        3
        end
      4
      end
    5
    end
  6
  end
7
end
```
И будем вызывать ее до тех пор, пока она не станет устойчиво возвращать единицу.

Сверхгибкий язык программирования Ruby позволяет создавать эффективнейшие средства троллинга отдела тестирования. Если таким образом определить какой-либо экшн какого-либо контроллера, эффект сохранится. Каждый перезапуск сервера будет запускать такой генератор блуждающих неисправностей заново. Очень удобно: спринт стремительно заканчивается, по проекту гуляют блуждающие ошибки, тестировщики седеют, подавая пачки тщательно оформленных багрепортов, а вы спокойно занимаетесь своими делами, зная, что можете решить 90% поставленных вам задач одним движением. Ценнейший для командной работы навык.

> Хорошо бы придумать какое-то практическое применение для этого эффекта, которое не связано с вредительством. У меня пока не получилось.

##### Регулярные выражения и процентные литералы
Предположим, мы уже решили задачу и хотим красиво, по-бутстраповски вывести результаты. Пишем во вьюхе:
```ruby
- @result.each do |result|
  span class=%i[btn btn-large] class="btn-#{/\A\d+\Z/.match(result) ? 'danger' : 'success'}" style='margin:4px' = result
```
Тем, кто знаком с регулярными выражениями, уже почти все понятно. Стоит разве что напомнить, что регулярным выражением можно проверять многострочные последовательности, и `\A` и `\Z` означают соответственно начало и конец **всей проверяемой последовательности**. Привычные `$` и `^` работают, конечно, но означают начало и конец строки — возможно, одной из многих. Будьте внимательны.

Процентные литералы позволяют экономить символы на запятых, кавычках, двоеточих и прочей пунктуации, и тем значительно улучшают читаемость кода.

Массив символов:
```ruby
%i[one two three four five six seven eight nine ten]
```
Массив строк:
```ruby
%w[one two three four five six seven eight nine ten]
```
Знайте и используйте. Подробнее о процентных (и не только процентных) литералах обязательно смотрите [здесь](http://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Literals).

Однако наилучшим образом процентные литералы смотрятся в хелперах. Здесь `slim` позволяет и вовсе так:
```ruby
- @result.each do |result|
  span.btn.btn-large class="btn-#{/\A\d+\Z/.match(result) ? 'danger' : 'success'}" style='margin:4px' = result
```
