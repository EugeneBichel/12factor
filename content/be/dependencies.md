## II. Залежнасці
### Яўна дэкларуйце і ізалюйце залежнасці

Большасць моў распрацавання дадаткаў прапануюць сістэму ўпакоўкі для распаўсюджвання бібліятэк падтрымкі, такія як [CPAN](http://www.cpan.org/) для Perl ці [Rubygems](http://rubygems.org/) для Ruby.  Бібліятэкі, усталяваныя праз сістэму ўпакоўкі, могуць быць устаноўлены ў маштабах усёй сістэмы (вядомыя як "пакеты сайтаў" ("site packages")),  або змешчаныя ў каталог, які змяшчае дадатак (вядомы як "vendoring" або "звязванне" ("bundling")).

**Дванаццаці-фактарны дадатак ніколі не разлічвае на няяўнае існаванне агульнасістэмных пакетаў.**  Такі дадатак аб'яўляе ўсе залежнасці цалкам і дакладна праз маніфест *аб'явы залежнасцяў (dependency declaration)*. Акрамя таго, ён выкарыстоўвае *інструмент ізаляцыі (dependency isolation)* залежнасцяў падчас выканання, каб гарантаваць, што ніякія няяўныя залежнасці не "выцякаюць (leak in)" з сістэмы (surrounding system). Поўная і яўная(explicit) спецыфікацыя залежнасцяў ўжываецца раўнамерна як да вытворчасці (production), так і да распрацоўкі (development).

Напрыклад, [Bundler](https://bundler.io/) для Ruby прапануе фармат маніфесту `Gemfile` для аб'явы залежнасцяў і `bundle exec` для ізаляцыі залежнасцяў. У Python ёсць два асобных інструмента для гэтых крокаў - [Pip](http://www.pip-installer.org/en/latest/) выкарыстоўваецца для аб'явы і [Virtualenv](http://www.virtualenv.org/en/latest/) для ізаляцыі. Нават C мае  [Autoconf](http://www.gnu.org/s/autoconf/) для аб'явы залежнасцяў, а статычнае звязванне (static linking) можа забяспечыць ізаляцыю залежнасцяў. Незалежна ад таго, які набор інструментаў выкарыстоўваеце, аб'ява залежнасцей і ізаляцыя заўсёды павінны выкарыстоўвацца разам - маючы толькі аб'яву або ізаляцыю недастаткова для задавальнення дванаццаці фактараў.

Адна з пераваг яўнай аб'явы залежнасцяў заключаецца ў тым, што яна спрашчае наладу(setup) для распрацоўшчыкаў, якія ўпершыню выкарыстоўваюць дадатак. Новы распрацоўшчык можа праверыць кодавую базу дадатка на сваёй машыне распрацоўкі, патрабуючы толькі моўнае асяроддзе выканання і мэнэджэр залежнасцяў, устаноўленыя ў якасці папярэдніх умоў. Яны змогуць наладзіць усё неабходнае для запуску кода прыкладання з дапамогай дэтэрмінаваных *каманды зборкі(build command)*. Напрыклад, каманда build для Ruby/Bundle - гэта `bundle install`, а для Clojure/[Leiningen](https://github.com/technomancy/leiningen#readme) - `lein deps`.

Дванаццаці-фактарныя дадаткі таксама не належаць на няяўнае(implicit) існаванне якіх-небудзь сістэмных інструментаў. Прыклады ўключаюць у сябе shelling out або ImageMagick або `curl`. Нават калі гэтыя інструменты могуць існаваць у шмат якіх сістэмах ці нават у большасці з іх, няма ніякай гарантыі, што яны будуць існаваць ва ўсіх сістэмах, дзе дадатак можа працаваць у будучыні, ці будзе версія, знойдзеная ў будучай сістэме, сумяшчальная з дадаткам. Калі дадатак мае залежнасць на сістэмны інструмент, гэты інструмент павінен быць уключаны (vendored) ў дадатак.