Routing
============

В Single Page Application (SPA) есть необходимость переключаться между страницами, без перезагрузки. Задачу маршрутизации на себя берёт 
встроенный роутер. Который выводит необходимые компоненты при переходе по необходимому URL.

## Создание роутинг модуля

Для того чтобы настроить маршрутизацию, необходимо сгенерировать роутинг модуль. Через встроенные команды Angular CLI это делается командой:

> ng new "имя приложения" --routing --defaults

Далее необходимо подключить наш роутинг модуль в главный модуль приложения (обычно AppModule):

>  imports: [

> BrowserModule,

> AppRoutingModule // CLI adds AppRoutingModule to the AppModule's imports array

>],

## Подключение компонентов

Чтобы добавить пути, необходимо импортировать RouterModule и Routes:

> @NgModule({

> imports: [RouterModule.forRoot(routes)],

> exports: [RouterModule]

> })

Далее объявляем массив путей: 

> const routes: Routes = [

> { path: 'first-component', component: FirstComponent },

> { path: 'second-component', component: SecondComponent },

> ];

Обратите внимание, что данный массив был передан как аргумент в `RouterModule.forRoot(routes)`.

Следующим шагом нужно объявить ссылки, которые будут открывать наши компоненты. Для этого в HTML вашего главного компонента пропишем:

> <li><a routerLink="/first-component" routerLinkActive="active">First Component</a></li>

> <li><a routerLink="/second-component" routerLinkActive="active">Second Component</a></li>

И добавьте ниже тег `<router-outlet>` для отображения выбранных компонентов. Именно внутри него будут отображаться наши компоненты при выборе ссылки. 

> <router-outlet></router-outlet>

Внимание, порядок объявления путей важен, так как Ангуляр ищет первое совпадение путей. Соответвенно, если у вас будут одинаковые ссылки, будет работать только первая объявленная.

## Данные из пути

Довольно часто необходимо получить какую-то информацию из пути, такую как выбранный елемент, id объекта и т.д. Для того, чтобы получить информацию 
из URL достаточно импортировать `ActivatedRoute` и `ParamMap` и указать их в конструкторе (заинджектить):

> import { Router, ActivatedRoute, ParamMap } from '@angular/router';

> constructor(private route: ActivatedRoute) {}

Чтобы подписаться на изменение параметров пути можно вызвать `subscribe` у поля `queryParams` в хуке `OnInit`:

> ngOnInit() {

> this.route.queryParams.subscribe(params => {

> this.name = params['name'];

> });

> }

Как аргумент колбека переданного в `subscribe` приходят параметры. Откуда мы можем по ключу вытянуть необходимые нам данные.

## Пути по умолчанию

Если нам нужно переходить на определённую страницу в любом случае, можно использовать путь `**`: 

> { path: '**', component:  }

Типичным применение является отображение 404 страницы, если ни один из путей не подошел:

> const routes: Routes = [

> { path: 'first-component', component: FirstComponent },

> { path: 'second-component', component: SecondComponent },

> { path: '**', component: PageNotFoundComponent },  // Wildcard route for a 404 page

>];

В данном случае, ангуляр пройдется по всем путям и дойдет до нашего "пути по умолчанию" и отобразится компонент `PageNotFoundComponent`.

## Переадресация

Для того чтобы по определенному пути переадресовывать на другой путь, достаточно указать в пути поле `redirectTo`:

> const routes: Routes = [

> { path: 'first-component', component: FirstComponent },

> { path: 'second-component', component: SecondComponent },

> { path: '',   redirectTo: '/first-component', pathMatch: 'full' }, // redirect to `first-component`

> { path: '**', component: PageNotFoundComponent },  // Wildcard route for a 404 page

> ];

В данном примере, если мы только зашли на сайт, то наш URL после адреса нашего сайта - ''. Соответственно, первые два пути не подходят.  
Совпадает третий путь и он уже нас переадресует на `/first-component`.

Внимание, пути пишутся относительно, и поэтому при переадресации необходимо писать слеш перед путём.

## Вложенные маршруты

Если наше приложение достаточно большое, то велик шанс того, что в нашем приложении внутри нашего компонента необходим еще один роутер аутлет для отображения разных компонентов.
Для этого есть дочерние пути. Они указываются в поле `children` пути внутри которого будет отображаться.

> const routes: Routes = [
{
path: 'first-component',
component: FirstComponent, // this is the component with the <router-outlet> in the template
children: [
{
path: 'child-a', // child route path
component: ChildAComponent, // child route component that the router renders
},
{
path: 'child-b',
component: ChildBComponent, // another child route component that the router renders
},
],
},
];

При этом компонент `FirstComponent` должен содержать такой код в HTML: 

> <h2>First Component</h2>

> '<'nav>
  '<'ul>
    <'li><'a routerLink="child-a">Child A<'/a><'/li>
    <'li><'a routerLink="child-b">Child B<'/a><'/li>
  <'/ul>
<'/nav>

> <router-outlet></router-outlet>

## Относительные пути

Для того чтобы перемещаться из дочерних путей в пути выше уровнем, пути являются относительными. Если нам нужно из нашего дочернего пути
`FirstComponent` переместиться в `SecondComponent` достаточно выйти на уровень выше через `..`, аналогично путям в файловой системе:

> <'li><'a routerLink="../second-component">Relative Route to second component<'/a>

## Guard

Если нам нужно проверить, можно ли перейти по данному пути, можно воспользоваться Guard'ами. В Angular существуют такие guard:
* CanActivate
* CanActivateChild
* CanDeactivate
* Resolve
* CanLoad

Для того чтобы воспользоваться guard'ами, сначала необходимо сгенерировать сервис-guard. 

> ng generate guard your-guard

Должен быть сгенерирован сервис подобного вида:
> export class YourGuard implements CanActivate {
canActivate(
next: ActivatedRouteSnapshot,
state: RouterStateSnapshot): boolean {
// your  logic goes here
}
}

Чтобы привязать наш guard-сервис к конкретному пути, необходимо в пути его указать: 

> {

> path: '/your-path',

> component: YourComponent,

> canActivate: [YourGuard],

> }

## Параметры в пути 

Есть несколько способов задать параметры для пути.
1) Указав путь и параметр как массив из двух элементов:
> <a [routerLink]="['/hero', hero.id]">
<span class="badge">{{ hero.id }}</span>{{ hero.name }}
</a>

2) Указать опциональные параметры как объект:
> <a [routerLink]="['/crisis-center', { foo: 'foo' }]">Crisis Center</a>

## HomeWork

1) Создать роутер модуль и прописать роуты к компонентам
2) Создать guard-сервис и проверять что мы можем перейти на страницу (корзина или favorite), только если у нас есть объекты в избранном/корзине.
