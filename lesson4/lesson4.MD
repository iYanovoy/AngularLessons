Angular Basics
============
## Template syntax

# Interpolation

Интерполяция представляет собой подстановку переменных в разметку. Синтаксис: `{{ *Имя переменной* }}`
> currentCustomer = 'Maria';

> <span>Current customer: {{ currentCustomer }}</span>

Так же можно интерполировать аттрибуты в HTML:

> <p>{{title}}</p>
> '<'div>'<'img src="{{itemImageUrl}}">'<'/div>

В качестве данных для интерполяции можно использовать выражения:

> <-- "The sum of 1 + 1 is 2" -->
> <p>The sum of 1 + 1 is {{1 + 1}}.</p>

Переменные можно использовать для обозначения элементов в HTML:

> '<'label>Type something:

> '<'input #customerInput>{{customerInput.value}}

> '<'/label>

# Template statements

Для привязки хендлеров на браузерные события используется следующий синтаксис: `(event)="statement"`:

> <button (click)="deleteHero()">Delete hero'<'/button>

Так же мы можем передавать любые данные в вызываемую функцию:

> <button (click)="onSave($event)">Save</button>

> <button *ngFor="let hero of heroes" (click)="deleteHero(hero)">{{hero.name}}'<'/button>

> '<'form #heroForm (ngSubmit)="onSubmit(heroForm)"> ... </form>

# Binding syntax

Биндинг позволяет выводить в HTML актуальное значение переменных:

> <-- Bind button disabled state to `isUnchanged` property -->

> <button [disabled]="isUnchanged">Save</button>

Так же мы можем задавать значения для атрибутов:

> '<'input type="text" value="Sarah">

Где фреймворк создаст узел с полем value и запишет туда значение `Sarah`.

Мы можем задавать дополнительные атрибуты:

> '<'button disabled>Test Button'<'/button>

При такой записи, для поля `disabled` устанавливается значение `true`. Так же можно записать его как атрибут:

> '<'input [disabled]="condition ? true : false">

Или

> '<'input [attr.disabled]="condition ? 'disabled' : null">

В данном случае записи выше примерно равнозначны. За исключением того, что в первом случае атрибут смотрит на булевое значение, 
а во втором случае `null` приводится к `false` и аттрибут неактивен.

Есть 3 типа биндинга:

1) От источника к отображению (`{{expression}}` И `[target]="expression"`);
2) От отображения к источнику (`(target)="statement"`);
3) Двухсторонний (`[(target)]="expression"`).

# Property binding

Чтобы прибиндить переменные к свойствам DOM элементов, можно использовать квадратные скобки ('[]'):

> '<img [src]="itemImageUrl">'

Чтобы передать какие-то статические свойства, достаточно объявить "классическим образом":

> '<app-item-detail childItem="parentItem"></app-item-detail>'


# Attribute, class, and style bindings

Биндинг аттрибутов схож с биндингом свойств. Аттрибуты начинаются с префикса `attr`:

> '<p [attr.attribute-you-are-targeting]="expression"></p>

Классы биндятся аналогичным образом. Но, для этого есть несколько способов.

1) Через точку с префиксом `class` (`[class.sale]="onSale"`) который принимает булевое значение
2) Через биндинг свойств (`[class]="classExpression"`) который принимает
* `string` (`"my-class-1 my-class-2 my-class-3"`),
* `Record<string, boolean | undefined | null>` (`{foo: true, bar: false}`),
* `Array<string>` (`['foo', 'bar']`)

Стили биндятся аналогичным образом:

1) Через точку с префиксом `style` (`[style.background-color]="expression"` или `[style.backgroundColor]="expression"`)
который принимает булевое значение
2) Через биндинг свойств (`[style]='navStyle'`) который принимает
* `string` (`"width: 100px; height: 100px"`),
* `Record<string, string | undefined | null>` (`{width: '100px', height: '100px'}`)

Так же аттрибуты можно инжектить с помощью декторатора (`@Attribute(*attributeName*)`):

> @Component({
> selector: 'app-my-input-with-attribute-decorator',
> template: '<p>The type of the input is: {{ type }}</p>'
> })
> export class MyInputWithAttributeDecoratorComponent {
> constructor(@Attribute('type') public type: string) { }
> }

# Event binding

Чтобы прибиндить ивенты, можно использовать уже знакомый синтаксис биндинга, где в круглых скобках имя события,
а ему присвоена функция, которая будет вызвана по срабатыванию события (`(click)="onSave()"`).

Так же можно создать собственные события с помощью `EventEmitter`. Для этого нужно:
* Создать переменную типа `EventEmitter` у директивы или компонента.
* Для распространения ивента, достаточно вызвать `EventEmitter.emit(data)`. 
* Родительские директивы слушают событие и принимают данные как `$event` объект.

# Two-way binding

Двухсторонний биндинг используется для передачи информации между родительским и дочерним компонентами. 
Как мы рассматривали ранее, нужно использовать квадратные и круглые скобки для объявления в HTML (`[(size)]="fontSizePx"`).
Со стороны компонента, нужно создать входящий и исходящий параметр. Где, к примеру `input` это имя входящего параметра,
а `inputChange` - исходящего:

> @Input()  size!: number | string;

> @Output() sizeChange = new EventEmitter<number>();

В остальном все работает так же как и при обычных `Input` и `Output`.

# Template reference variables

Для того чтобы передавать какие-то элементы внутри шаблона, можно их занести в переменные.
Для создания переменной достаточно дать ей имя начинающееся с `#`:

> <input #phone placeholder="phone number" />


Благодаря этому мы можем далее использовать переменную для обращения к её значению, или аттрибутам:

> <button (click)="callPhone(phone.value)">Call</button>

По аналогии с переменными в JS, переменные в шаблоне видны внутри шаблона, `*ngIf`, `*ngFor` или `<ng-template>`.

# Pipes

Pipe - используются для изменения/подготовки данных для отображения. 
Они указываются в переменной для интерполяции через `|` справа от переменной (`<p>The hero's birthday is {{ birthday | date }}</p>`).
Ангуляр предоставляет несколько встроенных пайпов для трансформации данных.

Встроенные пайпы:

* DatePipe - используется для трансформации даты для отображения
* UpperCasePipe - трансформирует строку в верхний регистр
* LowerCasePipe - трансформирует строку в нижний регистр
* CurrencyPipe - трансформирует строку в курс валюты, в соответствии с местными нормами
* DecimalPipe - трансформирует данные в число с точкой, в соответствии с местными нормами
* PercentPipe - трансформирует данные в проценты, в соответствии с местными нормами

# Параметры пайпов

Параметры для пайпов задаются через двоеточие после названия пайпа и задаются строкой (`{{ amount | currency:'EUR' }}`).
Несколько параметров могут следовать друг за другом: `{{ amount | currency:'EUR':'Euros '}}`.

# Создание кастомных пайпов

Чтобы указать, что данный класс является пайпом, достаточно использовать `@Pipe` декоратор.
Так же, имплементируйте PipeTransform интерфейс. Вам необходимо будет реализовать `transform`, где и будет происходить трансформация данных.
Значение будет первым аргументом, и дополнительные аргументы будут следующими. 

> @Pipe({name: 'exponentialStrength'})

> export class ExponentialStrengthPipe implements PipeTransform {

> transform(value: number, exponent = 1): number {

>   return Math.pow(value, exponent);

>}}

## HomeWork

1) Создать дочерние компоненты и прокинуть в них значение для нескольких переменных.
2) Создать `Output` и вызывать функцию из родительского компонента при клике по кнопке. Передавать в эту функцию какие-то данные.
