* [Мебель](#мебель)
* [Ткани](#ткани)
* [Инструментарий](#инструментарий)

## Мебель

Товары для мебельного каталога описываются файлами в папке [_items](_items/)

На основе этих файлов (и других, дополняющих их, — см. ниже) формируются CSV- и XML-файлы для различных площадок (Bitrix, Wix, 
Яндекс, Юла и т.д.)

Файлы для некоторых площадок нужно скачивать со [страницы витрины](https://pufeldom.github.io/pdgrab/out/) и загружать 
вручную, другие периодически самостоятельно скачиваются площадкой.

После внесения любого изменения в репозиторий запускается пересборка витрины и файлов CSV/XML, на это требуется 
некоторое время. Если что-то пошло не так, пересборка не произойдёт, и доступной останется предыдущая собранная версия.
Список сборок и их статус можно увидеть здесь: https://github.com/Pufeldom/pdgrab/deployments/

### Формат файлов

Файлы товаров и материалов хранятся в виде файлов специального формата.

Каждый файл начинается с **заголовочной части** ([Front Matter](https://jekyllrb.com/docs/front-matter/)), отбитой 
с обеих сторон тремя дефисами (`---`). Внутри которой описываются свойства товара/материала в строгом формате.

Строка начинается с имени свойства, затем следует двоеточие, затем значение свойства.
* значение может быть пустым, может быть многострочным, может представлять собой список 
* в случае обычного строкового значения могут действовать ограничения по символам (первому или любому), в этом случае 
  нужно обернуть значение в кавычки (одинарные или двойные)
* многострочное значение или _значение-список_ записываются с обязательным отступом в два или более пробелов; 
  для списков каждый элемент должен начинаться с дефиса и следующего после него пробела, затем идёт значение элемента
* также существуют специальные булевы значения: `true` и `false`

После завершения заголовочной части (после закрывающих `---`) следует опциональная текстовая часть, используемая как 
**описание** в свободной текстовой форме.

### Управление файлами товаров

Товары описываются файлами в папке [_items](_items/) по принципу «один файл — один товар».

Список товаров также визуализирован на [странице витрины](https://pufeldom.github.io/pdgrab/out/)

**Имя файла** (за вычетом расширения `.html`) соответствует уникальному внешнему коду / идентификатору товара.

Поля **заголовочной части** (см. выше о формате файлов):
* `hru`: HRU товара («символьный код» в терминах Bitrix); при оставлении пустым будет использовано имя файла за вычетом 
  расширения
* `title`: название товара
* `price`: цена товара в рублях (число)
* `section`: название раздела
* `color`: цвет (см. также ниже про управление свойствами Wix)
* `width`: ширина в сантиметрах (число)
* `depth`: глубина в сантиметрах (число)
* `height`: высота в сантиметрах (число)
* `material`: тип материала обивки; при оставлении пустым попробует найти значение среди материалов (см. ниже)
* `legs`: материал ножек
* `legs_color`: цвет ножек
* `foam`: толщина поролона в сантиметрах (число)
* `martindale`: значение теста Мартиндейла; при оставлении пустым попробует найти значение среди материалов (см. ниже)
* `density`: плотность ткани; при оставлении пустым попробует найти значение среди материалов (см. ниже)
* `weight`: вес в килограммах (число)
* `pmurl`: сокращённая ссылка PostImage (поле Link); нужна для получения маленькой картинки при использовании PostImage
* `image`: ссылка на основную картинку (при использовании PostImage - поле Direct link)
* `gallery`: список ссылок на остальные картинки товара (см. выше про формат значений-списков)
* `active`: булево поле (true/false): отображать ли товар в каталоге, включать ли в выдачу на площадки
* `youla`: булево поле (true/false): включать ли товар в выдачу Юлы, действует помимо поля `active`
  (@todo нужно ли на данный момент это отдельное поле для Юлы?)

### Управление материалами

Материалы обивки описываются файлами в папке [_materials](_materials/) по принципу «один файл — один материал».

**Заголовочная часть** (см. выше о формате файлов) содержит поля:
* `material`: тип материала обивки
* `martindale`: значение теста Мартиндейла (число)
* `density`: плотность ткани (число)

**Имя файла** (за вычетом расширения `.html`) соответствует имени материала — строке поиска в названии товаров (мебели 
и тканей). В случае если соответствующие параметры не указаны в товаре, они будут подставлены из файла материала, имя 
которого содержится в названии товара.

**Важное замечание:** в отличии от других файлов, относящихся только к мебели, данные файлы материалов также используются для автозаполнения свойств тканей.

### Управление дополнительными свойствами для Wix

Дополнительные свойства для Wix и источники их значений настраиваются в файле [wix.yml](_data/wix.yml)

В ключе **collections** для каждой из коллекций настраиваются:
* параметры для фильтров (_params_, до 6 штук)
* поля ввода для покупателя (_inputs_, до 2 штук)

Если имя коллекции не найдено в списке, данные берутся из ключа `_` (нижнее подчёркивание) — т.е. это можно использовать
как данные по умолчанию, например для мебели.
 
Ниже идёт **params** — конфигурация собственно «параметров для фильтров».

Для каждого из параметров указывается _источник получения_, среди которых есть несколько специальных (@todo может их 
написать с подчёркивания, чтобы как-то выделялись, не знаю?):
* `color`: хитрым образом обработанное поле `color` из товара (см. ниже)
* `material`: поле `material` из товара, а при пустом значении — из файла материала (папка _materials); принудительно 
  с заглавной буквы
* `martindale`: поле `martindale` из товара, а при пустом значении — из файла материала (см. выше)
* `density`: поле `density` из товара, а при пустом значении — из файла материала (см. выше)
* `legs`: поле `legs` из товара, но с принудительно заглавной буквы
* _любое другое значение_ — просто одноимённое поле из товара без изменения (например `width`)

И наконец **colors** — конфигурация цветов.
 
Там перечислены те названия цветов и цветовые коды, которые могут быть использованы в фильтрах
* _значения с `#`_ — это собственно «главные» цвета и их цветовые коды
* _значения с отсылкой_ к другому названию — значит название и цветовой код будут взяты из другого цвета (по идее должно 
  работать «по цепочке», главное чтобы не было циклической отсылки типа _Белый_ → _Светло-серый_ → _Серый_ → _Белый_, 
  а то ничего не соберётся)
* _последний элемент_ в списке (сейчас это «Другое») имеет также специальное значение — если цвет, указанный в товаре 
  (или в отсылке из другого цвета), не найден в списке, будет использовано именно это последнее в списке название 
  и его цветовой код

Порядок остальных цветов неважен, я просто их сгруппировал и упорядочил как мне показалось удобнее, можно переставлять
и дополнять любым удобным образом

### Файл экспорта для Aliexpress

В файле [aliexpress.xml](https://github.com/Pufeldom/pdgrab/edit/master/out/aliexpress.xml) задаются параметры, экспортируемые на площадку Aliexpress.

Описание для Aliexpress снабжено дополнительной текстовой припиской, её текст задаётся внутри блока `capture description`: https://github.com/Pufeldom/pdgrab/blob/master/out/aliexpress.xml#L40

Цена для Aliexpress увеличена на фиксированное значение, это значение задаётся через `plus:` внутри поля price: https://github.com/Pufeldom/pdgrab/blob/master/out/aliexpress.xml#L50

### Управление структурой CSV-файлов

В некоторой степени можно влиять на структуру CSV-файлов, автоматически генерируемых гитхабом для разных площадок; 
это делается в файле [csv.yml](_data/csv.yml)

_Ключ первого уровня_ соответствует целевой платформе (wix, bitrix и т.д.)

Далее внутри каждого ключа доступны ещё два:
* `delimiter`: разделитель колонок в CSV-файле
* `cols`: перечень колонок файла и их _идентификаторы_ (при необходимости)

**Идентификаторы полей** (латиница без пробелов) нужны для прокидывания значений в момент генерации файлов. (Если название
колонки и так представляет собой латиницу без пробелов, либо значение его полей должно быть пустым — можно обойтись и без
идентификатора.)

Сами значения в этом файле никак не настраиваются, это происходит внутри файлов генерации (которые лучше не трогать).

Однако при помощи данного файла можно легко:
* переставить колонки местами
* добавить новую пустую колонку
* удалить/переименовать имеющуюся колонку (при переименовании важно сохранить тот же идентификатор поля, если он был —
  иначе значение не попадёт в соответствующую колонку).

### Обновление файлов по CSV, скачанному из Bitrix

Доступен функционал обновления файлов в папке [_items](_items/) на основе CSV-файла, экспортированного и скачанного
из Битрикса. Обновляется только часть свойств, но хотя бы то, что возможно.

Процесс примерно такой:
1. экспортировать и скачать актуальный CSV из Битрикса
2. положить его в папку репозитория (pdgrab)
3. открыть консоль git
4. перейти в ней в папку репозитория:
```
cd pdgrab
```
5. скачать актуальный репозиторий:
```
git pull -r
```
6. запустить скрипт обновления:
```
python csv2items.py ИМЯ_ФАЙЛА.csv
```
(чтобы не писать имя файла целиком, поможет нажатие Tab после набора первых букв)

7. убедиться, что изменения корректны:
```
git diff
```
(промотка вверх/вниз — _клавиши-стрелочки_, выход — `q`)

8. закоммитить изменения:
```
git commit -a -m "update items"
```
(вместо update items можно написать что угодно, это просто комментарий к изменению)

9. отправить изменения в гитхаб:
```
git push
```
(он запросит логин и пароль от гитхаба; пароль при вводе будет не отображаться, это нормально)

Если неохота возиться с гитом в консоли, то пункты 3-5 и 7-9 можно сильно упростить, если установить редактор,
имеющий интеграцию с git (см. внизу раздел про инструментарий).


## Ткани

### Конфигурация

Скрипт скачивания тканей конфигурируется через файл [pdgrab.ini](pdgrab/pdgrab.ini)

#### Общие замечания

_Квадратные скобки_ обозначают _заголовок секции_ в ini-файле. Эти названия напрямую влияют на работоспособность кода, 
так что не стоит произвольно их добавлять, переименовывать или удалять.

В каждой секции есть набор _параметров_. Всё, что в строке идёт до первого _двоеточия_, считается именем параметра, всё 
после него считается значением (в т.ч. многострочным, пока не будет объявлен новый параметр).

В некоторых случаях имена параметров жёстко фиксированы, в некоторых — допускаются произвольные. Значение параметра
технически может быть в пустым (после двоеточия нет ничего), однако это скорее касается нестрогих параметров вроде
настройки полей CSV.

Наш скрипт специальным образом обрабатывает значения для некоторых параметров как списки. Значения в них идут со сдвигом
от начала строки, по одному значению на строку. (Это не является встроенным функционалом ini-файлов, докручено поверх.)

Символ `;` (точка с запятой) в ini-файлах имеет специальное значение — комментирование. Всё, что идёт после него 
до конца строки, не считывается как содержимое. Таким образом можно снабдить строку комментарием, либо временно 
выключить строку, при это не удаляя её (просто поставить точку с запятой в начале строки).

#### Конфигурация источников

Перечень активных источников (сайтов поставщиков) определяет секция `[grab]`. Параметр `sources` — список имён таких
источников. Каждый из них должен иметь собственный файл в коде, а также секцию `[grab_<ИМЯ_ИСТОЧНИКА>]` в конфиге.

В секциях вида `[grab_<ИМЯ_ИСТОЧНИКА>]` настраиваются следующие параметры:
* `base_url`: начальная часть адреса сайта поставщика, чаще всего заканчивается именем домена
* `section_prefix`: дальнейшая часть адреса, общая для всех разделов каталога; начинается и заканчивается со слеша (`/`)
* `sections`: список разделов, подлежащих обработки; каждый элемент списка представляет собой финальную часть адреса; 
  адрес каждой «входной точки» на сайте поставщика складывается так: `base_url` + `section_prefix` + элемент `sections`
  (по такому адресу должна находиться страница, содержимое которой умеет обрабатывать скрипт; если страница недоступна — 
  выдастся ошибка 404 и скрипт поедет дальше; если же содержимое не получится обработать, скрипт остановится).
* `skipped_section_titles` (опционально): список стоп-слов (фраз), при обнаружении любой из которых в названии 
  (под)раздела (в том виде, как его считывает скрипт), обработка такого (под)раздела будет пропущена

#### Автоприменение цветов

Для скачиваемых тканей доступно автоматическое заполнение поля «цвет».

В секции `[color]` задаются доступные варианты цвета и их цветовые коды (для wix)

Секция `[color_marks]` содержит перечень строк, которые ищутся в названии ткани, а также имя цвета из `[color]`, который
нужно применить в случае найденного совпадения.
* Если в названии ткани не найдено совпадения ни с одной из строк, выдастся сообщение _«WARNING! cannot detect color»_
  и применится цвет «Другой» и код «FFFFFF» (@todo вынести в конфиг)
* Если совпадение найдено, но указанное для него имя цвета не найдено в секции `[color]`, выдастся сообщение 
  _«WARNING! unknown color»_ и применится цвет «Другой» и код «FFFFFF» (@todo вынести в конфиг)
  
Важные моменты относительно поиска:
* поиск происходит последовательно, сверху вниз: не найдена первая строка — ищется вторая и т.д.
* при первом совпадении поиск останавливается, дальнейшие строки для того же товара не проверяются
* искомая строка должна полностью присутствовать в названии товара, в т.ч. с учётом регистра букв

#### Автоприменение свойств ткани

Дополнительные свойства ткани (плотность, тест Мартиндейла) определяются при помощи не конфига, а дополнительных файлов
в папке [_materials](_materials/) (тех же самых, что используются для мебели в каталоге)

В названии тканей последовательно ищутся названия файлов в этой папке (с учётом регистра букв, но без расширения 
`.html`). При нахождении совпадения к ткани применяются значения density и martindale, указанные в файле (и дальнейший
поиск прекращается).

#### Конфигурация целевых CSV-файлов

После того как ткани с сайтов поставщиков скачаны, на основании их параметров формируются CSV-файлы.

Перечень целевых файлов определяет секция `[csv]`. Параметр `targets` — список имён таких файлов. Каждый из них должен 
иметь собственный файл в коде, а также секцию `[csv_<ИМЯ_ЦЕЛИ>]` в конфиге.

В секциях вида `[csv_<ИМЯ_ЦЕЛИ>]` перечисляется список колонок, которые попадут в целевой CSV-файл и значения для них.
Значение в виде строки, числа или пустое подставляются в CSV как есть. Кроме того, есть несколько специальных строк 
с `@`, которые заменяются специальным значением (если это поддерживает код генерации целевого CSV-файла):
* `@dest_id`: уникальный внешний код / идентификатор товара
* `@title`: название товара
* `@slug`: HRU товара, «символьный код» в терминах Bitrix
* `@is_available`: доступность товара (в зависимости от целевого файла, получает значение «да/нет», «TRUE/FALSE» и т.д.)
* `@section`: раздел товара (обычно тип материала)
* `@subsection`: подраздел товара (обычно имя групповое имя коллекции на сайте поставщика)
* `@small_pic`: маленькая картинка товара
* `@large_pic`: крупная картинка товара
* `@color`: цвет, определённый по названию ткани (см. выше)
* `@color_code`: цветовой код, определённый по названию ткани (см. выше)
* `@density`: плотность ткани, определённая по её названию (см. выше)
* `@martindale`: значение теста Мартиндейла, определённое по названию ткани (см. выше)

### Запуск скрипта

Перед скачиванием тканей необходимо:
1. перейти в консоли в папку репозитория:
```bash
cd pdgrab
```
2. обновить версию репозитория:
```bash
git pull -r
```

Собственно сам скрипт скачивания запускается командой:
```bash
python buildcsv.py
```

После прохождения скрипта формируются CSV-файлы, по одному для каждого цели из конфига. Файлы именуются по принципу:
```
items-<ДАТА_И_ВРЕМЯ_ЗАПУСКА>-<ИМЯ_ЦЕЛИ>.csv
```
Например:
```
items-202103210236-bitrix.csv
items-202103210236-wix.csv
```


## Инструментарий

Часть **простых операций** с файлами в github можно производить прямо онлайн:
* напрямую в веб-редакторе github: https://github.com/Pufeldom/pdgrab/
* при помощи «витрины» (после изменений файлов некоторое время пересобирается): https://pufeldom.github.io/pdgrab/out/

Для работы с репозиторием **локально** понадобятся следующие инструменты:
1. **Python** — язык, на котором написаны наши скрипты (понадобится версия 3.6+)
    * все версии для windows: https://www.python.org/downloads/windows/
    * прямая ссылка на версию 3.7.9 для 64-разрядной windows: https://www.python.org/ftp/python/3.7.9/python-3.7.9-amd64.exe
2. **Git** — система контроля версий, которую использует github
    * страница скачивания для windows: https://git-scm.com/download/win
    * прямая ссылка на версию 2.29.2 для 64-разрядной windows: https://github.com/git-for-windows/git/releases/download/v2.29.2.windows.3/Git-2.29.2.3-64-bit.exe
3. **редактор**, интегрированный с Git, — для более удобной работы с файлами и репозиторием; например один из этих:
    * _VSCode_: https://code.visualstudio.com/download
    * _Atom_: https://atom.io/
    
### Команды Git

Распространённые команды для работы с репозиторием. Выполняются в консоли (командной строке git).

Жирным отмечены те, которые предположительно будут нужны чаще других: (`pull`, `diff`, `commit`, `push`).

Первичное скачивание репозитория:
```bash
git clone https://github.com/Pufeldom/pdgrab
```

**Скачать изменения** из github на локальный компьютер:
```bash
git pull -r
```

Просмотреть список файлов, изменённых локально (а также количество неотправленных коммитов и т.д.):
```bash
git status
```

**Просмотреть содержимое локальных изменений** (клавиши-стрелки проматывают вверх/вниз, `q` выходит из просмотра):
```bash
git diff
```

Отменить локальные изменения в конкретном файле (например в `_items/g0042.html`):
```bash
git checkout _items/g0042.html
```

Отменить локальные изменения во всех файлах:
```bash
git checkout .
```

Отменить локальные изменения в конкретном файле (например в `_items/g0042.html`):
```bash
git checkout _items/g0042.html
```

Отменить локальные изменения во всех файлах:
```bash
git checkout .
```

Сохранить локальные изменения в конкретном файле (например в `_items/g0042.html`):
```bash
git add _items/g0042.html
```

Сохранить локальные изменения во всех файлах:
```bash
git add .
```

**Сохранить в коммит все локальные изменения** (кроме новых созданных файлов):
```bash
git commit -a -m "Описание сути изменения"
```

**Оправить локальные изменения (коммиты) в github**:
```bash
git push
```

**Подробнее об этих и других командах Git**: https://git-scm.com/book/ru/v2


### Массовое изменение цены

В проекте расположен скрипт `price.py`, позволяющий массово изменять цену товаров.

**Первый параметр** определяет изменение цены для товаров (положительное либо отрицательное)

**Второй параметр** (опциональный) определяет, для каких товаров применить изменение цены. Нюансы:
* параметр выбирает целевые товары, производя поиск по заголовку
* если значение параметра начинается с символа `^`, то он работает как исключение (`^банкетка` выберет всё, кроме банкеток)
* если поисковая строка содержит пробелы, то её нужно заключить в кавычки (`"пуф сканд"`)
* в остальном поиск работает по тому же принципу, что и фильтр на странице товаров: ищется вся строка целиком,
  регистр не учитывается, начальные и концевые пробелы игнорируются
* если параметр не передан, изменение применится для всех товаров

Примеры использование:
```bash
python price.py 42
python price.py -42
python price.py 42 пуф
python price.py 42 ^пуф
python price.py 42 "^пуф сканд"
```

Перед/после использования понадобится применение редактора и/или [консольных команд git](#команды-git) для просмотра
и публикации внесённых изменений.
