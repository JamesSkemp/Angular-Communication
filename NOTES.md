# Module 3
## Binding and structural directives
### Interpolation
```typescript
// Component
pageTitle: string = 'Product List';
```
```html
<!-- Template -->
<div>{{pageTitle}}</div>
```

### Property binding
```typescript
// Component
imageWidth: number = 50;
```
```html
<!-- Template -->
<img [style.width.px]="imageWidth" />
```

### Event bidning
```typescript
// Component
toggleImage(): void {
	this.showImage = !this.showImage;
}
```
```html
<!-- Template -->
<button (click)="toggleImage()">Toggle Image</button>
```

### Two-way binding
```typescript
// Component
listFilter: string;
```
```html
<!-- Template -->
<input type="text" [(ngModel)]="listFilter" />
```

### *ngIf
```typescript
// Component
showImage: boolean = false;
```
```html
<!-- Template -->
<img *ngIf="showImage" [src]="product.imageUrl" />
```

### *ngFor
```typescript
// Component
products: IProduct[];
```
```html
<!-- Template -->
<tr *ngFor="let product of products">
```

### Two-way binding, long way
`[(ngModel)]="listFilter"` is shorthand for `[ngModel]="listFilter" (ngModelChange)="listFilter=$event"`.

So you could do `[ngModel]="listFilter" (ngModelChange)="onFilterChange($event)"`, where `onFilterChange` updates `listFilter`.

### Getters and setters
```typescript
// Component
listFilter: string;
```

```typescript
// Component
private _listFilter: string;
get listFilter(): string {
	return this._listFilter;
}
set listFilter(value: string) {
	this._listFilter = value;
}
```

# Module 4
In my opinion, using these has more downsides than benefits.

## `ViewChild`

```
// Angular Directive
@ViewChild(NgModel) filterInput: NgModel; // <input type="text" [(ngModel)]="listFilter" />
// Custom Directive or Child Component
@ViewChild(StarComponent) star: StarComponent;
// Template Reference Variable
@ViewChild('divElementVar') divElementRef: ElementRef; // <div #divElementVar>{{pageTitle}}</div>
// Above is available during/after ngAfterViewInit(), which is after constructor() and ngOnInit().
// However, if within a *ngIf you may run into an issue.
// ElementRef has a nativeElement which allows for access to any HTML element properties or methods.
@ViewChild(NgForm) editForm: NgForm; // if using template-driven forms.
```

With `NgModel` we can for example:
```
@ViewChild(NgModel) filterInput: NgModel;
this.filterInput.valueChanges.subscribe(() => this.performFilter(this.listFilter));
```

Otherwise, `NgModel` and `NgForm` are read-only.

## `ViewChildren`
```
@ViewChildren(NgModel) inputs: QueryList<NgModel>;
// Above would support checking for status.
@ViewChildren(StarComponent) stars: QueryList<StarComponent>;
@ViewChildren('divElementVar' divElementRefs: QueryList<ElementRef>;
@ViewChildren('filterElement, nameElement' divElementRefs: QueryList<ElementRef>;
// Tracks changes in the DOM.
this.divElementRefs.changes.subscribe(() => { /* act */ });
```

# Module 5
## Parent to child component communication

- Child: `@Input`, getters/setters, `OnChanges`
- Parent: Template reference variable, `@ViewChild`
- Use a service

## `@Input()`
Child:
```typescript
@Input() propertyName: string;
```

Parent:
```html
<app-child propertyName="binding source"></app-child>
```

Or:
```typescript
parentProperty: string;
```
```html
<app-child [propertyName]="parentProperty"></app-child>
```

## Getter and setter
Child component:
```typescript
private _propertyName: string;
get propertyName(): string {
	rerturn this._propertyName;
}
@Input() set propertyName(value: string) {
	this._propertyName = value;
}
```

## `OnChanges`
Child component:
```typescript
@Input() propertyName: string;

ngOnChanges(changes: SimpleChanges): void {
	// Note: values start at undefined.
	if (changes['propertyName']) {
		changes['propertyName'].currentValue
	}
}
```

## Template reference value
```html
<app-child #childReference [propertyName]="parentProperty"></app-child>

{{ childReference.propertyName }}
{{ childReference.methodName() }}
```

## `@ViewChild`
```html
<app-child #childReference [propertyName]="parentProperty"></app-child>
```
```typescript
@ViewChild('childReference') childComponent: ChildComponent;
```
or
```html
<app-child [propertyName]="parentProperty"></app-child>
```
```typescript
@ViewChild(ChildComponent) childComponent: ChildComponent;
parentPropertyName: string;

ngAfterViewInit(): void {
	this.parentVariable = this.childComponent.propertyName;
}
```
## Summary
1. Use a child component when:
	- for a specific task
	- complex
	- reusable
2. `@Input`, getter/setter, and `OnChanges` are easier for parent to child
	- Favor getter/setter if you only need to react to changes to specific properties
	- Favor `OnChanges` if you want to react to any input property changes, or if you need current/previous values
3. Template reference variable if you want to use it in the parent's template
4. `ViewChild` if you want to use it in the class
	- but it won't receive notification of changes




