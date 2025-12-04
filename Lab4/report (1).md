---
## Front matter
title: "Отчет оп лабораторной работе 4"
subtitle: "Кибербезопасность предприятия"
author: "Апареев Д.А., Игнатенкова В.Н., Демидович Н.М., Ендонова А.В., Машковцева К.С., Шубнякова Д.И."

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float}
  - \floatplacement{figure}{H}
  - \addtokomafont{author}{\scriptsize}
---

# Задача

Научиться обнаруживать и эксплуатировать уязвимости в изолированной сетевой среде для получения несанкционированного доступа к резервным копиям данных, хранящимся на backup-сервере.

Способы получения флага:
1. Поиск backup-сервера
2. Эксплуатация уязвимости ms17

# Теоретическое введение

1. Meterpreter — полезная нагрузка в Metasploit Framework, предоставляющая интерактивную оболочку для управления скомпрометированной системой.

2. MS17-010 (EternalBlue) — уязвимость в протоколе SMBv1, позволяющая выполнить удалённый код на уязвимых системах Windows.

3. ARP-сканирование — метод обнаружения активных хостов в локальной сети по MAC-адресам.

4. Проброс портов (port forwarding) — техника, позволяющая перенаправлять сетевой трафик через скомпрометированный узел для доступа к внутренним ресурсам.

5. SMB (Server Message Block) — сетевой протокол для совместного доступа к файлам, принтерам и другим ресурсам.



# Выполнение лабораторной работы

## Описание сценария

Для получения доступа во внутреннюю сеть была использована уязвимость в WordPress-плагине wp_wpdiscuz_unauthenticated_file_upload, позволяющая загрузить вредоносный файл и получить meterpreter-сессию на корпоративном сайте.
Альтернативно можно было использовать уязвимость exchange_proxyshell_rce на почтовом сервере  (рис. [-@fig:001]).

![Получение meterpreter-сессии](image/01.png){#fig:001 width=70%}

После успешной эксплуатации была получена активная meterpreter-сессия с узлом в DMZ  (рис. [-@fig:002]).

![Запуск](image/02.png){#fig:002 width=70%}

## Поиск backup-сервера

Отображаем конфигурацию сети  (рис. [-@fig:003]).

![Отображение сетевых интерфейсов](image/04.png){#fig:003 width=70%}

Прописываем в активной meterpreter-сессии маршрут до данной подсети (рис. [-@fig:004]).

![Проброс портов во внутреннюю сеть](image/05.png){#fig:004 width=70%}

Далее сворачиваем активную сессию и запускаем прокси-сервер с помощью socks (рис. [-@fig:005]).

![Запуск прокси-сервера](image/06.png){#fig:005 width=70%}

Продолжаем сбор информации о найденной подсети с помощью arp_scanner (рис. [-@fig:006]).

![arp_scanner](image/07.png){#fig:006 width=70%}

Далее проводим сканирование открытых портов на найденных хостах, используя модуль portscan/tcp (рис. [-@fig:007], [-@fig:008]).

![Сканирование открытых портов на хосте](image/08.png){#fig:007 width=70%}

![Сканирование portscan](image/09.png){#fig:008 width=70%}


## Эксплуатация уязвимости ms17

С помощью модуля scanner/smb проводим сканирование порта smb на наличие уязвимости ms17 (рис. [-@fig:009]).

![Сканирование порта на наличие уязвимости](image/10.png){#fig:009 width=70%}

Хост уязвим для ms17. Для эксплуатации уязвимости нужно воспользоваться модулем (рис. [-@fig:010]).

![Сканирование порта на наличие уязвимости](image/11.png){#fig:010 width=70%}

Необходимый код для решения задания (рис. [-@fig:011]).

![Сканирование порта на наличие уязвимости](image/12.png){#fig:011 width=70%}


# Выводы

В ходе лабораторной работы были успешно выполнены следующие этапы:
1. Получен начальный доступ к узлу в DMZ через уязвимость WordPress/Exchange.
2. Обнаружен backup-сервер во внутренней сети с помощью ARP- и портового сканирования.
3. Эксплуатирована уязвимость MS17-010 для получения полного контроля над backup-сервером.
4. Найден и извлечён файл с флагом.
