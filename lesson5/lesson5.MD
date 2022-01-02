Directives
============

Директивы в Angular можно поделить на:
 * Компоненты - директивы с собственным темплейтом (view)
 * Встроенные директивы (`NgClass`, `NgIf`)
 * Пользовательские директивы (написанные непосредственно программистом, который разрабатывает приложение).

В свою очередь, встроенные директивы бывают:
* Атрибутные (`NgClass`, `NgStyle` и `NgModel`) - которые изменяют поведение, отображение или навешивают функционал
* Структурные (`NgIf`, `NgFor`, `NgSwitch`) - которые изменяют структуру DOM

Рассмотрим детальнее встроенные директивы.

# Встроенные Директивы (Built-in directives)

## Атрибутные директивы (Attribute directives)

### NgClass

Директива NgClass используется для навешивания и удаления дополнительных классов на элементы, в том числе, при определенных условиях.
Директива объявляется в квадратных скобках и ей присваивается строка или запись (`Record<string, boolean>`)

> '<div [ngClass]="isSpecial ? 'special' : ''">This div is special</div>'

Где `isSpecial` - булевая переменная, которая добавит элементу `div` класс `special` в случае, если `isSpecial` будет равна `true`.

> '<div [ngClass]="currentClasses">This div is initially saveable, unchanged, and special.</div>'

>   currentClasses: Record<string, boolean> = {};
    /* . . . */
    this.currentClasses =  {
        saveable: this.canSave,
        modified: !this.isUnchanged,
        special:  this.isSpecial
>    };

В данном случае, мы имеем переменную с набором ключей-классов и значениями булевыми, который говорит о наличии данного класса у элемента. 
Благодаря этому, мы можем управлять различными классами (удалять и добавлять их) прямо во время выполнения приложения.

### NgStyle

Директива `NgStyle` позволяет во время выполнения программы задавать определённые CSS стили и изменять их.
Директива принимает `Record<string, string>`, где ключ - это css свойство, а значение - значение этого свойства

> '<div [ngStyle]="currentStyles">This div is initially italic, normal weight, and extra large (24px).</div>'

>   currentStyles: Record<string, string> = {};
    /* . . . */
    this.currentStyles = {
        'font-style':  this.canSave      ? 'italic' : 'normal',
        'font-weight': !this.isUnchanged ? 'bold'   : 'normal',
        'font-size':   this.isSpecial    ? '24px'   : '12px'
>   };

### ngModel

Директива `ngModel` позволяет отображать данные, которые изменяются во время работы приложения.

Прежде всего, для использования директивы необходимо импортировать модуль `FormsModule` и добавить в список импортов.
> import { FormsModule } from '@angular/forms'; // <--- JavaScript import from Angular

> @NgModule({

> imports: [BrowserModule, FormsModule // <--- import into the NgModule]

> })

Далее необходимо задать директиву элементу формы. Ей присвоить переменную, объявленную внутри компонента. Дополнительно, можно задать функцию, которая будет выполнена при изменении модели.

> '<input [ngModel]="currentItem.name" (ngModelChange)="setUppercaseName($event)" id="example-uppercase">'

## Встроенные структурные директивы (Built-in structural directives)

### NgIf

Директива `NgIf` позволяет скрыть или показать элемент на странице. Для этого, директиве передается переменная с булевым значением, 
в зависимости от её значения элемент и будет отображаться (`true`) или будет скрыт (`false`).

> <app-item-detail *ngIf="isActive" [item]="item"></app-item-detail>

Стоит отметить, что в отличие от атрибутных директив, структурные не нужно оборачивать квадратными скобками, но вместо этого
ставится звёздочка (`*`) перед их именем.
Данную директиву можно использовать для скрытия элементов, переменные для которых не заданы.

> '<div *ngIf="currentCustomer">Hello, {{currentCustomer.name}}</'div>'

### NgFor

Директива `NgFor` используется для выведения массива данных. Для использования этой директивы необходимо:
1) Навесить её на элемент, который задаёт, как будет отображён каждый элемент
2) Задать итерируемую переменную и присвоить имя элементу массива, в который будет подставляться значение на каждом шаге итерации. ()

> '<div *ngFor="let item of items">{{item.name}}</'div>

Аналогичным образом директива объявляется для дублирования компонентов:

> <app-item-detail *ngFor="let item of items" [item]="item"></app-item-detail>

Так же, чтобы получить индекс элемента, можно использовать конструкцию `let i=index`:

> '<div *ngFor="let item of items; let i=index">{{i + 1}} - {{item.name}}<'/div>

Так как ангуляру нужно понимать, какие элементы нужно перерендерить а какие нет, ему можно помочь и создать функцию, которая будет возвращать 
одно из полей, что будет изменено в случае изменения объекта и следить только за этим полем и перерендеривать при его изменении.
Данную функцию необходимо передать в `trackBy`:

> trackByItems(index: number, item: Item): number { return item.id; }

> '<div *ngFor="let item of items; trackBy: trackByItems">({{item.id}}) {{item.name}}<'/div> 

В данном случае, ангуляр будет перерисовывать элементы только при изменении id объекта. 

### NgSwitch

Директива `NgSwitch` аналогична конструкции `switch` в JavaScript. Она используется для выборочного отображение элементов.
Использование данной директивы подразумевает объявление нескольких дополнительных директив:
* `NgSwitch` - атрибутная директива, которое меняет поведение для работы других директив 
* `NgSwitchCase` - структурная директива, которая добавляет элемент в `DOM` в зависимости от значения переменной в `NgSwitch` и переданного значения в текущую директиву.
* `NgSwitchDefault` - структурная директива, которая добавляет элемент в `DOM` при отсутствии значения в `NgSwitch`

> '<div [ngSwitch]="currentItem.feature">

> <app-stout-item    *ngSwitchCase="'stout'"    [item]="currentItem"></app-stout-item>

> <app-device-item   *ngSwitchCase="'slim'"     [item]="currentItem"></app-device-item>

> <app-lost-item     *ngSwitchCase="'vintage'"  [item]="currentItem"></app-lost-item>

> <app-best-item     *ngSwitchCase="'bright'"   [item]="currentItem"></app-best-item>

> <'!-- . . . --'>

> <app-unknown-item  *ngSwitchDefault           [item]="currentItem"></app-unknown-item>

> <'/div>

## Кастомные Атрибутные директивы (Custom Attribute directives)  

Для создания кастомной (пользовательской) директивы, можно воспользоваться встроенными возможностью Angular CLI. 
Для генерации новой директивы можно ввести команду `ng generate directive *Имя директивы*`.
Должен получится сгенерированный файл, с такой структурой: 

> import { Directive } from '@angular/core';

> @Directive({

> selector: '[appHighlight]'

> })

> export class HighlightDirective {

> constructor() { }

>}

Для доступа к `DOM` элементов необходимо импортировать `ElementRef` из `@angular/core`.
> @Directive({
    selector: '[appHighlight]'
    })
    export class HighlightDirective {
    constructor(el: ElementRef) {
        el.nativeElement.style.backgroundColor = 'yellow';
    }
}

Чтобы применить директиву к элементу или компоненту достаточно прописать её как атрибут:

> <'p appHighlight>Highlight me!<'/p>

Так же есть возможность следить за браузерными событиями. Для этого необходимо: 

* Импортировать `HostListener` из `@angular/core`
* Добавить декораторы `HostListener` к функциям: 

> @HostListener('mouseenter') onMouseEnter() {
this.highlight('yellow');
}

> @HostListener('mouseleave') onMouseLeave() {
this.highlight('');
}

> private highlight(color: string) {
this.el.nativeElement.style.backgroundColor = color;
}

Пробросить значение в директиву можно точно так же как и в компонент:

> @Input() appHighlight = '';

> <'p [appHighlight]="color">Highlight me!<'/p>

## Кастомные структурные директивы (Structural directives)

Для создания кастомных структурных директив необходимо:

* Сгенерировать директиву с помощью команды Angular CLI `ng generate directive *имя директивы*`
* Импортировать `Input`, `TemplateRef`, и `ViewContainerRef`: `import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';`
* Заинджектить `TemplateRef` и `ViewContainerRef` в конструктор: `constructor(private templateRef: TemplateRef<any>, private viewContainer: ViewContainerRef) { }`
* Создать входящий параметр-сеттер, чтобы прятать или открывать отображение:

> @Input() set appUnless(condition: boolean) {

>if (!condition && !this.hasView) {
this.viewContainer.createEmbeddedView(this.templateRef);
this.hasView = true;

> } else if (condition && this.hasView) {
this.viewContainer.clear();
this.hasView = false;
}
}

Где `TemplateRef` позволяет получить доступ к контенту `<ng-template>`, а `ViewContainerRef` даёт доступ к контейнеру отображения.

Как мы можем вспомнить из встроенных структурных директив, они все начинаются со *. Так же это можно использовать для самописных (кастомных) структурных директив. 
Звездочка подразумевает что вокруг элемента или компонента создается `<ng-template>` и в него передается директива как атрибутная.

![Сокращения](./assets/shorthand.png)


## HomeWork

1) Создать директиву, которая будет подсвечивать текст/иконку/символ при наведении.
2) Создать директиву, которая будет выбранную единицу открывать на весь экран (по аналогии с модулем из JS'а)
