Forms
============

Формы используются для сбора, валидации и отправки данных на сервер. Ангуляр предоставляет два пути обработки пользовательских форм: 
Реактивную и темплейтоориентированную (Template-driven). Оба подхода преобразовывают данные пользователя в модель,
для валидации, отслеживания ввода или сохранения данных.

## Выбор пути самурая

Оба подхода являются отличными, и для начала нужно определиться с тем, что нам нужно.

* Реактивные формы позволяют напрямую работать с потоком данных от пользователя. Подходит к общему реактивному стилю приложения.
* Template-driven формы более быстрый и простой способ работать с данными. Хуже масштабируются, но и проще добавляются.

Оба вида форм имеют такие директивы: 

* FormControl - отслеживает изменение конкретного поля
* FormGroup - отслеживает изменение коллекции форм контролов
* FormArray - отслеживает изменения массива контролов
* ControlValueAccessor - создает мост между ангуляровским FormControl и встроенным DOM элементом

## Реактивные формы

Для реактивных форм нужно непосредственно в компоненте прописать создание форм контрола.

> import { Component } from '@angular/core';
import { FormControl } from '@angular/forms';

>@Component({
selector: 'app-reactive-favorite-color',
template: `
Favorite Color: <input type="text" [formControl]="favoriteColorControl">
`
})
export class FavoriteColorComponent {
favoriteColorControl = new FormControl('');
}

Чтобы создать базовую реактивную форму, достаточно 3 шагов: 
* Зарегистрировать `ReactiveFormsModule`. Этот модуль декларирует директивы, которые нам будут необходимы.
* Сгенерировать новый `FormControl` инстанс и сохранить это в компоненте.
* Зарегистрировать `FormControl` в темплейте.

Чтобы зарегистрировать `ReactiveFormsModule`, достаточно его импортировать и указать в вашем модуле: 

> import { ReactiveFormsModule } from '@angular/forms';

>@NgModule({
imports: [
// other imports ...
ReactiveFormsModule
],
})
export class AppModule { }

Чтобы зарегестрировать новый инстанс форм контрола, достаточно вызвать одноименный конструктор со словом new:

> import { Component } from '@angular/core';
import { FormControl } from '@angular/forms';

>@Component({
selector: 'app-name-editor',
templateUrl: './name-editor.component.html',
styleUrls: ['./name-editor.component.css']
})

>export class NameEditorComponent {
name = new FormControl('');
}

Чтобы зарегистрировать форм контрол в темплейте, достаточно воспользоваться одноименной директивной:

><'label for="name">Name: </label>
<input id="name" type="text" [formControl]="name">

Для получения значений контрола, можно воспользоваться 2 способами: 

* Через `valueChanges` Observable слушая изменения (async pipe для отображения в темплейте)
* Через свойство `value`, которое отдаёт значение в момент вызова

Чтобы задать значение, достаточно воспользоваться встроенной функцией `setValue`:

> updateName() {
this.name.setValue('Nancy');
}

Для того, чтобы сгруппировать несколько контролов, можно воспользоваться 2 способами:
* FormGroup - фиксированное кол-во контролов, которые могут быть обработаны вместе
* FromArray - динамическая форма, где можно добавлять и удалять контролы "на лету"

Для создания форм группы достаточно вызвать конструктор и передать контролы в необходимом виде:
> import { Component } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

>@Component({
selector: 'app-profile-editor',
templateUrl: './profile-editor.component.html',
styleUrls: ['./profile-editor.component.css']
})

> export class ProfileEditorComponent {
profileForm = new FormGroup({
firstName: new FormControl(''),
lastName: new FormControl(''),
});
}

Зарегистрировать форм группу в темплейте можно аналогичным контролу образом:

> <'form [formGroup]="profileForm">

> <'label for="first-name">First Name: </label>
<'input id="first-name" type="text" formControlName="firstName">

> <'label for="last-name">Last Name: </label>
<'input id="last-name" type="text" formControlName="lastName">

> <'/form>

Чтобы сохранить данные из формы, можно добавить обработчик на отправку формы для директивы `ngSubmit`:

> <'form [formGroup]="profileForm" (ngSubmit)="onSubmit()">

Так же есть возможность создавать вложенные форм группы (форм группы внутри форм групп):  

> import { Component } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

> @Component({
selector: 'app-profile-editor',
templateUrl: './profile-editor.component.html',
styleUrls: ['./profile-editor.component.css']
})

> export class ProfileEditorComponent {
profileForm = new FormGroup({
firstName: new FormControl(''),
lastName: new FormControl(''),
address: new FormGroup({
street: new FormControl(''),
city: new FormControl(''),
state: new FormControl(''),
zip: new FormControl('')
})
});
}

Регистрация в темплейте аналогична обычной форм группе:

> <'div formGroupName="address">
  <'h2>Address</h2>

> <'label for="street">Street: </label>
<'input id="street" type="text" formControlName="street">

> <'label for="city">City: </label>
<'input id="city" type="text" formControlName="city">

> <'label for="state">State: </label>
> <'input id="state" type="text" formControlName="state">

> <'label for="zip">Zip Code: </label>
  <'input id="zip" type="text" formControlName="zip">
<'/div>

Чтобы обновить значение форм группы можно использовать:

* setValue() - задает новое значение для контролов
* patchValue() - заменяет значения объявленные в объекте

Основные отличия в том, что `setValue` отловит ошибку при задании некорректного значения, а patchValue не будет генерировать ошибку



## Template-driven формы

Чтобы создать Template-driven форму необходимо указать ngModel директиву для каждого инпута.

> import { Component } from '@angular/core';

> @Component({
selector: 'app-template-favorite-color',
template: `
Favorite Color: <input type="text" [(ngModel)]="favoriteColor">
`
})
export class FavoriteColorComponent {
favoriteColor = '';
}

В большинстве случаев этого достаточно, чтобы записать значение в переменную. 


## Валидция

Валидация нам необходима для того, чтобы подсказать пользователю, что он ввел некорректные данные или же 
отсеять данные, которые не стоит отправлять на сервер, к примеру.

Валидация зависит от выбранного пути. Для валидации темплейтоориентированных форм, необходимо писать кастомную директиву, 
которая будет следить за изменением состояния, и, имея доступ к DOM, будет навешивать необходимые аттрибуты или классы на наш елемент.
 Либо использовать встроенные в HTML инструменты валидации (minLength, required и т.д.).

Пример директивы для валидации темплейтоориентированных форм:

> @Directive({

> selector: '[appForbiddenName]',

>providers: [{provide: NG_VALIDATORS, useExisting: ForbiddenValidatorDirective, multi: true}]


>})
> export class ForbiddenValidatorDirective implements Validator {

> @Input('appForbiddenName') forbiddenName = '';

>validate(control: AbstractControl): ValidationErrors | null {
return this.forbiddenName ? forbiddenNameValidator(new RegExp(this.forbiddenName, 'i'))(control)
: null;
}
}

Если же мы используем реактивные формы, то валидация требует лишь написания функции-валидатора и передать ее или массив таких
функций как второй аргумент в конструктор `FormControl`.

>  this.heroForm = new FormGroup({

>   name: new FormControl(this.hero.name, [
Validators.required,
Validators.minLength(4),
forbiddenNameValidator(/bob/i) // <-- Here's how you pass in the custom validator.
]),

> alterEgo: new FormControl(this.hero.alterEgo),
power: new FormControl(this.hero.power, Validators.required)

>});

Пример функции валидатора: 

> export function forbiddenNameValidator(nameRe: RegExp): ValidatorFn {
return (control: AbstractControl): ValidationErrors | null => {
const forbidden = nameRe.test(control.value);
return forbidden ? {forbiddenName: {value: control.value}} : null;
};
}

В данном случае, мы возвращаем объект ошибки, которая будет указана в состоянии формы.
Так же, стоит отметить, что в реактивных формах мы можем использовать встроенные функции-валидаторы (`Validators.required`, `Validators.minLength(4)` и т.д).

Если же нам нужна асинхронная валидация (к примеру, если валидация происходит на беке), то мы передаем массив валидаторов как третий аргумент в конструкторе.
Для этого нам нужно написать свой класс который заимплементит интерфейс `AsyncValidator`:


> @Injectable({ providedIn: 'root' })

> export class UniqueAlterEgoValidator implements AsyncValidator {

> constructor(private heroesService: HeroesService) {}

>validate(
control: AbstractControl
): Observable<ValidationErrors | null> {
return this.heroesService.isAlterEgoTaken(control.value).pipe(
map(isTaken => (isTaken ? { uniqueAlterEgo: true } : null)),
catchError(() => of(null))
);
}
}

И при создании форм контрола достаточно указать его, и прибиндить контектс:

> const alterEgoControl = new FormControl('', {
asyncValidators: [this.alterEgoValidator.validate.bind(this.alterEgoValidator)],
updateOn: 'blur'
});

Стоит отметить, что ангуляр будет добавлять классы для опасания состояния инпута, такие как `ng-touched`, `ng-invalid` и т.д.
Благодаря чему мы можем отобразить пользователю, что он ввел неправильные данные и т.д. Так как можно основывать наши стили на данных классах.



## HomeWork

1) Создать страничку создания товаров для магазина.
2) Асинхронно валидировать наличие товара с таким именем/айди/штрихкодом и валидировать все поля на длину, обязательность к заполнению и т.д.