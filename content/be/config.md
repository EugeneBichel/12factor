## III. Канфігурацыя
### Захоўвайце канфігурацыю ў асяроддзі

*Канфігурацыя* дадатку - гэта ўсё што хутчэй за ўсё будзе адрознівацца паміж [разгортваннямі](./codebase) (на прамежкавае (staging), вытворчасці (production), распрацоўшчыка асяроддзі, і так далей). Гэта ўключае ў сябе:

* Дэскрыптары рэсурсаў для базы дадзеных, Memcached і іншых [бэкэнд сэрвісаў (backing services)](./backing-services)
* Уліковыя дадзеныя для знешніх сэрвісаў, такіх як Amazon S3 або Twitter
* Значэння для кожнага разгортвання, такія як кананічнае імя хаста (hostname) для разгортвання

Дадаткі часам захоўваюць канфігурацыю ў выглядзе канстант ў кодзе. Гэта парушэнне метадалогіі дванаццаці-фактараў, якое патрабуе **строгага аддзялення конфігурацыі ад кода**. Канфігурацыя істотна адрозніваецца ў розных разгортваннях, а код - не.

Лакмусавы (litmus) тест каб вызначыць ці правільна раздзялілі канфігурацыю ад кода  заключаецца ў тым, каб адказаць на пытанне ці можна зрабіць кодавую базу адкрытым кодам у любы момант, без кампрэметаціі уліковых дадзеных (credentials).

Звярніце ўвагу, што гэта вызначэнне "канфігурацыя (config)" **не** ўклучае ўнутраную канфігурацыю дадатка, такую як `config/routes.rb` у Rails, або як [code modules are connected](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/beans.html) у [Spring](http://spring.io/). Гэты тып канфігурацыі не змяняецца паміж разгортваннямі, і таму лепш за ўсё рабіць гэта ў кодзе.

Iншым падыходам да канфігурацыі з'яўляецца выкарыстанне канфігурацыйных файлаў, якія не рэгіструюцца (checked into) ў сістэме кіравання версіямі, такія як `config/database.yml` у Rails. Гэта велізарнае паляпшэнне ў параўнанні з выкарыстаннем канстант, якія правяраюцца ў рэпазіторыі кода, але ўсё ж мае недахопы: лёгка памылкова праверыць канфігурацыйны файл у рэпазіторыі; таксама канфігурацыйныя файлы часта раскіданы ў розных месцах і розных фарматах, што робіць более цяжкім прагляд і кіраванне ўсімі канфігурацыямі ў адным месцы. Таксама, гэтыя фарматы, як правіла, з'яўляюцца спецыфічнымі для мовы распрацоўкі або фрэймворка.

**Дадатак, які падтрымлівае дванаццаць фактараў, захоўвае канфігурацыю ў *зменных асяроддзя (environment variables)*** (часта скарочаны да *env vars* або *env*).  Env vars легка змяняць паміж разгортваннямі (deploys) без змены якога - небудзь кода; у адрозненне ад канфігурацыйных файлаў, існуе мала шанцаў на тое, што яны будуць выпадкова запісаны (checked into) ў рэпазіторый кода; і ў адрозненне ад канфігурацыйных файлаў картыстальнікаў або іншых канфігурацыйных механізмаў такіх як Java System Properties, яны з'яўляюцца мова-агностычным і OS-агностычным стандартам.

Яшчэ адным аспектам кіравання канфігурацыямі з'яўляецца групоўка (grouping). Часам дадаткі групіруюць канфігураціі ў найменныя групы (іх часта клічуць "асяроддзямі (environments)") названыя ў залежнасці ад канкрэтных разгортванняў, такія як асяроддзі `распрацоўкі (development)`, `тэставання (test)`, і `вытворчасці (production)` у Rails. Гэты метад не маштабуецца добра: бо чым болей разгортванняў дадатку ствараецца, там болей новых імен для асяроддяў патрабуецца, такія як `прамежкавае (staging)` або `qa`. Па меры далейшага развіцця праекта распрацоўшчыкі могуць дадаваць свае ўласныя спецыяльныя асяроддзя, такія як `joes-staging`, што робіць кіраванне разгортваннямі дадатка вельмі далікатным.

Y дванаццаць-фактарнвым дадатку, env vars гранулярна кантралююцца, кожны цалкам незалежны ад іншых env vars. Яны ніколі не групуюцца разам як "асяроддзя (environments)", а замест гэтага незалежна кіруюцца для кожнага разгортвання. Выкарыстоўванне env vars - гэта такая мадэль, якая плаўна масштабуецца ў адпаведнасць з павялічэннем колькасці разгортванняў дадатка на працягу жыцця.