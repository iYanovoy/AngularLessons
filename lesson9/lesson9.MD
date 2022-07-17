HTTP
============

Для того чтобы работать с HTTP, прежде всего необходимо подключить `HttpClientModule`:

> import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';

> @NgModule({
imports: [
BrowserModule,
// import HttpClientModule after BrowserModule.
HttpClientModule,
],
declarations: [
AppComponent,
],
bootstrap: [ AppComponent ]
})
export class AppModule {}

Далее необходимо заинджектить его в классе, в котором мы планируем его использовать `HttpClient`:

> import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

> @Injectable()
export class ConfigService {
constructor(private http: HttpClient) { }
}

Стоит помнить, что `HttpClient` всегда возвращает `observable`, поэтому нам так же необходимо импортировать интерфейс `Observable` 
и пайпы для работы с ним:

> import { Observable, throwError } from 'rxjs';
import { catchError, retry } from 'rxjs/operators';


### Работа с сервером


## ПОлучение данных с сервера

Для отправки запроса на сервер у нас есть метод `HttpClient.get()` который запрашивает данные у сервера. Метод принимает 2 аргумента. 
Первый это URL запроса, а второй объект опций:

> options: {
headers?: HttpHeaders | {[header: string]: string | string[]},
observe?: 'body' | 'events' | 'response',
params?: HttpParams|{[param: string]: string | number | boolean | ReadonlyArray<string | number | boolean>},
reportProgress?: boolean,
responseType?: 'arraybuffer'|'blob'|'json'|'text',
withCredentials?: boolean,
}

В опциях есть 2 важных поля. Это `observe` и `responseType`. `observe` указывает на то, какую часть ответа следует вернуть. 
`responseType` на то, в каком виде будут возвращены данные. Сам метод подобен методу `fetch` в JS.

Пример просто запроса по URL:

> configUrl = 'assets/config.json';

> getConfig() {
return this.http.get<Config>(this.configUrl);
}

Пример использования запроса для получения конфига:

> showConfig() {

> this.configService.getConfig()
.subscribe((data: Config) => this.config = {
heroesUrl: data.heroesUrl,
textfile:  data.textfile,
date: data.date,
});

> }

Важная ремарка - HttpClient метод не отправит свой HTTP-запрос, пока вы не вызовете `subscribe()` у `observable` который вернет `get`.

Если вы хотите получить все тело запроса, используйте опцию `observable` - `response`:

> getConfigResponse(): Observable<HttpResponse<Config>> {
return this.http.get<Config>(
this.configUrl, { observe: 'response' });
}

> showConfigResponse() {

> this.configService.getConfigResponse()
// resp is of type `HttpResponse<Config>`
.subscribe(resp => {
// display its headers

> const keys = resp.headers.keys();

> this.headers = keys.map(key =>
`${key}: ${resp.headers.get(key)}`);

      // access the body directly, which is typed as `Config`.
      this.config = { ...resp.body! };
    });
> }

## Отправка данных на сервер

Для отправки данных на сервер достаточно использовать `post` метод запроса:

> /** POST: add a new hero to the database */
addHero(hero: Hero): Observable<Hero> {
return this.http.post<Hero>(this.heroesUrl, hero, httpOptions)
.pipe(
catchError(this.handleError('addHero', hero))
);
}

В данном случае добавляется тело запроса, и передается вторым аргументом. Соответственно `options` становятся третьим аргументом.  
Как Вы помните с курса по js, названия методов соответствуют методам HTTP.

## Добавление и удаление заголовков

Иногда, нам необходимо добавлять или удалять какие-то заголовки запросов. Если нам нужно создавть заголовки:

> import { HttpHeaders } from '@angular/common/http';

> const httpOptions = {
headers: new HttpHeaders({
'Content-Type':  'application/json',
Authorization: 'my-auth-token'
})
};

И если нам нужно проапдейить заголовки:

> httpOptions.headers =
httpOptions.headers.set('Authorization', 'my-new-auth-token');

## Конфигурация параметров

Иногда нам необходимо добавить в наш запрос URL параметры. Они будут автоматически добавлены к URL вашего запроса. 
Делается все аналогично заголовкам: 

> import {HttpParams} from "@angular/common/http";

> /* GET heroes whose name contains search term */
searchHeroes(term: string): Observable<Hero[]> {
term = term.trim();

> // Add safe, URL encoded search parameter if there is a search term
const options = term ?
{ params: new HttpParams().set('name', term) } : {};

> return this.http.get<Hero[]>(this.heroesUrl, options)
.pipe(
catchError(this.handleError<Hero[]>('searchHeroes', []))
);
}

## Interceptors (Перехватчики)

Иногда, существует необходимость "перехватывать" запросы. К примеру, если нам нужно отлавливать ошибки, добавлять какие-то специальные заголовки и так далее. 
Для этих задач в Angular существую `Interceptor`. Для имплементации интерцептора достаточно написать реализацию метода `intercept` интерфейса `HttpInterceptor`:

> import { Injectable } from '@angular/core';
import {
HttpEvent, HttpInterceptor, HttpHandler, HttpRequest
} from '@angular/common/http';

> import { Observable } from 'rxjs';

> /** Pass untouched request through to the next request handler. */
@Injectable()
export class NoopInterceptor implements HttpInterceptor {

>intercept(req: HttpRequest<any>, next: HttpHandler):
Observable<HttpEvent<any>> {
return next.handle(req);
}
}

Где метод `intercept` перехватывает запрос (тело запроса) и трансформирует его в `Observable`. В данном примере
`next` это `HttpHandler` который непосредственно обрабатывает запрос. И мы можем добавить некого функционала перед отправкой запроса на сервер.

> export abstract class HttpHandler {
abstract handle(req: HttpRequest<any>): Observable<HttpEvent<any>>;
}

Так как наш интерсептор это класс, мы должны его где-то подключить. Это "где-то" это модуль, в котором вы подключили ваш `HttpClient`. 
В связи с тем, что интерсептор влияет непосредственно на клиента. Для удобства можно организовать провайдер интерсепторов: 

> /* "Barrel" of Http Interceptors */
import { HTTP_INTERCEPTORS } from '@angular/common/http';

> import { NoopInterceptor } from './noop-interceptor';

> /** Http interceptor providers in outside-in order */
export const httpInterceptorProviders = [
{ provide: HTTP_INTERCEPTORS, useClass: NoopInterceptor, multi: true },
];

А после подключить в модуль: 

> providers: [
httpInterceptorProviders
],

Пример использования интерсепторов. К примеру, когда вам нужно подкинуть какой-то заголовок по-умолчанию:

> import { AuthService } from '../auth.service';

> @Injectable()
export class AuthInterceptor implements HttpInterceptor {

> constructor(private auth: AuthService) {}

> intercept(req: HttpRequest<any>, next: HttpHandler) {
// Get the auth token from the service.
const authToken = this.auth.getAuthorizationToken();

    // Clone the request and replace the original headers with
    // cloned headers, updated with the authorization.
    const authReq = req.clone({
      headers: req.headers.set('Authorization', authToken)
    });

    // send cloned request with header to the next handler.
    return next.handle(authReq);
>}
}


## HomeWork

1) Создать несколько интерсепторов.
2) Первый должен добавлять заголовок с уникальным ключом.
3) Следующий - подкидывать данные в тело.
4) И финальный должен логгировать запросы (консоль и т.д.).
