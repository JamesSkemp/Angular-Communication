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



