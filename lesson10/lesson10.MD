UI Frameworks
============

Довольно часто для реализации задумок дизайнеров, необходимо использовать популярные решения по типу селектов, 
date picker, слайдеров, бургеров и так далее. Так как на большинстве сайтов используются схожие решения, была необходимость 
в разработке набора готовых компонентов. Одним из таких является Angular Material:

## Angular Material

Прежде всего необходимо установить пакет материала: 

> ng add @angular/material

Команда `ng add` добавит `Angular Material` в ваш проект. Но нужно будет ответить на несколько вопросов в консоли.

* Выбрать подготовленную тему или `custom` [https://material.angular.io/guide/theming#using-a-pre-built-theme][Pre-built themes];
* Выбрать подготовленные типографические стили [https://material.angular.io/guide/typography][Typography];
* Импортировать `BrowserAnimationsModule` в ваше приложение для того, чтобы включить поддержку анимации для матириал компонентов.

### Подключение компонентов

Для того чтобы подключить компонент, нужно прежде всего импортировать его модуль:

> import { MatSliderModule } from '@angular/material/slider';

> @NgModule ({
imports: [
MatSliderModule,
]
})
class AppModule {}

Далее достаточно просто добавить тег желаемого компонента (`<mat-slider>`) в наш `HTML` компонента:

> <mat-slider min="1" max="100" step="1" value="50"></mat-slider>

И запустить локальный сервер `ng serve`.

Со списком всех компонентов можно ознакомиться по ссылке [https://material.angular.io/components/categories][Components];

### NGX-Bootstrap

Схема подключения такая же. 

* Подключаем в приложение `ng add ngx-bootstrap`;
* Импортируем модуль в корень:
> import { TooltipModule } from 'ngx-bootstrap/tooltip';

> @NgModule({
…
imports: [ TooltipModule.forRoot(), … ]
…
})

* Добавить компонент во вьюху:
> \<button type="button" class="btn btn-primary"
  tooltip="Vivamus sagittis lacus vel augue laoreet rutrum faucibus.">
  Simple demo\</button>

* Но, еще понадобится подключить `css` `bootstrap'a`:
> \<!--- index.html -->
> \<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.0/dist/css/bootstrap.min.css" rel="stylesheet" crossorigin="anonymous" integrity="sha384-KyZXEAg3QhqLMpG8r+8fhAXLRk2vvoC2f3B09zVXn8CA5QIVfZOJ3BCsw2P0p/We">

* И указать версию бутстрапа:
>  import { setTheme } from 'ngx-bootstrap/utils';

>@Component({…})
export class AppComponent {
constructor() {
setTheme('bs3'); // or 'bs4'
…
}
}

* И установить библиотеку и собрать либу:

> git clone https://github.com/valor-software/ngx-bootstrap.git

> cd ngx-bootstrap

> npm ci

> npm run build

> npm start

Список всех компонентов - [https://valor-software.com/ngx-bootstrap/#/components][Components];

### Prime NG

Так же одна из популярных библиотек. Устанавливается через `npm`:
> npm install primeng --save

> npm install primeicons --save

* Так же нужно импортировать модули:

> import {AccordionModule} from 'primeng/accordion';     //accordion and accordion tab

> import {MenuItem} from 'primeng/api';                  //api

Так же есть возможность подключить как зависимость ангуляр проекта:

> "dependencies": {
//...
"primeng": "^14.0.0-rc.1",
"primeicons": "^5.0.0"
},

И подключить стили:

> "styles": [

> "node_modules/primeng/resources/themes/lara-light-blue/theme.css",

> "node_modules/primeng/resources/primeng.min.css",

> "node_modules/primeicons/primeicons.css",
//...

> ],

Ознакомиться со списком компонентов(блоков) можно - [https://www.primefaces.org/primeblocks-ng/#/][Blocks]


## HomeWork

1) Выбрать фреймворк который нравится
2) Перевести (создать) компоненты на основе компонентов с фреймворка.