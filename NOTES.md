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

Can also use functions (`{{ getPageTitle() }}`) but may run into performance issues due to how Angular checks for changes.

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
		- The key here is that it's `@Input()` property changes.
3. Template reference variable if you want to use it in the parent's template
4. `ViewChild` if you want to use it in the class
	- but it won't receive notification of changes

# Module 6
## Child to parent component communication
- Event notification: `@Output`
- Provide information: Template reference variable, `@ViewChild`
- Service

## `@Output`
Child:
```typescript
@Output() valueChange = new EventEmitter<string>(); // in @angular/core

this.valueChange.emit(value);
```
Parent:
```html
<app-child (valueChange)="onValueChange($event)"></app-child>
```

```typescript
onValueChange(value: string): void {
	// ...
}
```

# Module 7 - Services
## Managing state options
From simple to complex:
1. Property bag
2. Basic state management
3. State management with notifications
4. ngrx (inspired by Redux)

## Property bag
Service that just contains properties.

Service:
```typescript
@Injectable()
export class ThingService {
	propertyName1: string;
	propertyName2: boolean;
}
```
Component:
```typescript
get propertyName1(): string {
	return this.thingService.propertyName1;
}
set propertyName1(value: string) {
	this.thingService.propertyName1 = value;
}

constructor(private thingService: ThingService) {
}
```

Great for 'stashing away properties for itself or other components.'

## Service scope
Register the service based upon what you want to be able to use it (scope), and how long it is retained for (lifetime).

- Register in the component - `@Component({ providers: [ ThingService ]})` - for that component and children (template or via router).
	- Good if you need multiple instances of the service for different component instances.
- Register in a module - `@NgModule({ providers: [ ThingService ] })` - no matter which module it's registered in (unless lazy-loaded) it will be available to all components.
	- Lazy-loaded module services are only available to components declared in that module, but is then available for the entire application lifetime.

`ngOnDestroy(): void { }` if you want to see the lifetime of a service.

## Guidelines
Property bag for:
- Retaining view state
- Retaining user selections
- Sharing data or other state
- Communicating state changes
- Okay if any component can read or change the values
- Components are only notified of state changes if they use template binding

# Module 8
to watch

# Module 9
to watch

# Module 10
to watch

# Module 11
to watch

