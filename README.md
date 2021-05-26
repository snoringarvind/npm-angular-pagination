<h1>App Pagination</h1>
<p>An Angular directive for pagination.</p>

<h2>Angular Version</h2>
<p>This Angular directive is created using Angular 12+ .</p>

<h2>How to install</h2>

```
npm i snoring-pagination
```

<h2>Set up</h2>

<ol>

<li>
<p>Import pagination in your module.ts file and add it to the imports[ ].</p>

```javascript
import { SnoringPaginationDirective } from "./snoring-pagination.directive";

@NgModule({
  declarations: [AppComponent, SnoringPaginationDirective],
  imports: [BrowserModule, FormsModule, AppRoutingModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

</li>
</ol>

<h2>How to use</h2>

<ol>

<li>
<h4>Add pagination directive to your div element</h4>

```html
<div class="pagination" snoringPagination #pagination="snoringPagination"></div>
```

</li>

<li>
<h4>Pass your [data] on which you want to implement your pagination. The data should be in the form of an array.</h4>

<p>The <em>values</em> is your data variable.</p>

```html
<div
  class="pagination"
  snoringPagination
  #pagination="snoringPagination"
  [valuesArr]="values"
></div>
```

</li>

<li>
<h4>Adding buttons to navigate forward and backward</h4>

```html
<div
  class="pagination"
  snoringPagination
  #pagination="snoringPagination"
  [valuesArr]="values"
>
  <button class="page-first-btn" (click)="pagination.first()"><<</button>
  <button class="page-prev-btn" (click)="pagination.prev()"><</button>
  <button class="page-next-btn" (click)="pagination.next()">></button>
  <button class="page-last-btn" (click)="pagination.last()">>></button>
</div>
```

</li>

<li>
<h4>Adding clickable page number buttons and also highlighting the current page number.</h4>
<p>Five clickable buttons are displayed with the curren page button in the middle.</p>
<p>The variables <em>currentPage</em> and <em>btnNosArr</em> we get from the call-back, which we will cover later.</p>

```html
<div
  class="pagination"
  snoringPagination
  #pagination="snoringPagination"
  [valuesArr]="values"
>
  <div *ngFor="let number of btnNosArr">
    <button
      (click)="pagination.OnClick(number, $event)"
      [ngClass]="{
          'active-page': currentPage === number,
          'page-btn': true
        }"
    >
      {{ number }}
    </button>
  </div>
</div>
```

</li>

<li>
<h4>Adding an input element to jump to the desired page number.</h4>

```html
<div
  class="pagination"
  snoringPagination
  #pagination="snoringPagination"
  [valuesArr]="values"
>
  <div>
    <input
      name="input"
      [ngModel]="currentPage"
      (keyup.enter)="pagination.change($event)"
    />
  </div>
</div>
```

</li>

<li>
<h4>Adding a  dropdown to select the number of data-values on each page. The default selection is [5, 10, 25, 50 ,100, 250, 500] but you can also pass your own selection range.</h4>

<p>The variable <em>selectArr</em> we get from the call-back, which we will cover later.</p>

```html
<div
  class="pagination"
  snoringPagination
  #pagination="snoringPagination"
  [valuesArr]="values"
  [pageRangeArr]="[5, 20, 30, 40, 50]"
>
  <select (click)="pagination.onRulePerPageChange($event)">
    <option
      *ngFor="let i of selectArr"
      [value]="[i]"
      [ngClass]="{
      selected: rulesPerPage === i,
      option: 'true'
      }"
    >
      {{ i }}
    </option>
  </select>
</div>
```

</li>

<li>
<h4>Adding a call-back function for new sliced values inside our respective component.ts file.</h4>

<ul> <!--sublist -->
<li> <!-- sublist 1>
<h5>component.ts </h5>
  
```javascript
    newRulesCB(val: any) {
      this.currentPage = val.currentPage;
      this.btnNosArr = val.btnNosArr;
      this.rulesPerPage = val.rulesPerPage;
      this.selectArr = val.newPageRangeArr;
      this.slicedValues = val.valuesArr;
    }
```
</li>

<li> <!-- sublist 2-->
<h5>component.html</h5>

<p>The variable <em>slicedValues</em> contains our new sliced data for the current page.</p>

```html
<div
  class="pagination"
  snoringPagination
  #pagination="snoringPagination"
  [valuesArr]="values"
  [pageRangeArr]="[5, 20, 30, 40, 50]"
  (newRules)="newRulesCB($event)"
></div>
```

</li>
</ul> <!-- sublist end-->

</li>

<li>
<h4>Adding a search box to filter the data. (Optional)</h4>

<ul> <!--sublist-->
<li> <!--sublist 1-->
<h5>component.ts</h5>
<p>Here we create Observables for searchTerms and searchObservable to pass the seach-term and the latest search filtered data inside the directive.</p>
<p>You can look more about creating search observables on <a href='https://angular.io/guide/practical-observable-usage'>Angular Documentation</a></p>

```javascript
searchTerms = new Subject<string>();

search(term: string) {
this.searchTerms.next(term);
}

ngOnInit(): void {
this.searchObservable = this.searchTerms.pipe(
debounceTime(300),

      distinctUntilChanged(),
      switchMap((term: string) => {
        let fr: any = [];
        this.names.filter((v: any, i: any) => {
          if (v.toLocaleLowerCase().includes(term.toLocaleLowerCase())) {
            fr.push(v);
          }
        });
        return of(fr);
      })
    );

}
```

</li>

<li> <!--sublist 2-->

<h5>component.html</h5>

```html
<div class="search-box">
  <input #searchBox (input)="search(searchBox.value)" placeholder="Search..." />
</div>
```

</li>

<li>
<h4>The entire component.html file code is here.</h4>
<h5>You just need to apply the directive to a div container and create a template refernce variable (#pagination) which points to the directive's instance.</h5>

```html
<div class="main-container">
  <div class="search-box">
    <input
      #searchBox
      (input)="search(searchBox.value)"
      placeholder="Search..."
    />
  </div>

  <div *ngIf="slicedNames.length == 0">No data</div>

  <ng-container *ngIf="slicedNames.length > 0">
    <div *ngFor="let name of slicedNames" class="table">
      <div class="sr-no">{{ data.indexOf(name) + 1 }}</div>
      <div class="name">{{ name }}</div>
    </div>
  </ng-container>

  <!-- ---pagination-starts -->
  <div class="pagination-container">
    <div class="total-pages">Total: <span> {{ data.length }} </span></div>

    <div
      class="pagination"
      snoringPagination
      #pagination="snoringPagination"
      (newRules)="newRulesCB($event)"
      [valuesArr]="data"
      [searchTerms]="searchTerms"
      [searchObservable]="searchObservable"
      [pageRangeArr]="[10, 40, 30, 15, 17, 1000]"
    >
      <button (click)="pagination.first()" class="page-first-btn"><<</button>
      <button (click)="pagination.prev()" class="page-prev-btn"><</button>

      <div *ngFor="let number of btnNosArr">
        <button
          (click)="pagination.OnClick(number, $event)"
          [ngClass]="{
            'active-page': currentPage === number,
            'page-btn': true
          }"
        >
          {{ number }}
        </button>
      </div>

      <div class="pageno-input">
        <input
          name="input"
          [ngModel]="currentPage"
          (keyup.enter)="pagination.change($event)"
        />
      </div>

      <button (click)="pagination.next()" class="page-next-btn">></button>
      <button (click)="pagination.last()" class="page-last-btn">>></button>

      <select (click)="pagination.onRulePerPageChange($event)">
        <option
          *ngFor="let i of selectArr"
          [value]="[i]"
          [ngClass]="{
            selected: rulesPerPage === i,
            option: 'true'
          }"
        >
          {{ i }}
        </option>
      </select>
    </div>
  </div>
</div>
```

</li>
</ul> <!--sublist end>

</li>

<li>                                                                                              
<h4>The entire component.ts file code is here</h4>
<h5>In your component.ts file implement a callback function to receive the new sliced values and other details.</h5>

```javascript
import { Component, OnChanges, OnInit } from '@angular/core';
import { AfterViewInit, ChangeDetectorRef } from '@angular/core';
import { Observable, of, Subject } from 'rxjs';
import { debounceTime, distinctUntilChanged, switchMap } from 'rxjs/operators';
import { DATA } from './mock-data';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
})
export class AppComponent implements OnInit, OnChanges, AfterViewInit {
  title = 'pagination';
  data!: any[];
  slicedNames: any[] = [];
  showSelect: boolean = false;

  selectArr: number[] = [];
  btnNosArr: number[] = [];
  rulesPerPage!: number;
  currentPage: number = 1;

  searchObservable!: Observable<any>;

  constructor(private cdRef: ChangeDetectorRef) {}

  ngOnInit() {
    let x: any = DATA.split('\n');
    this.data = x;

    this.searchObservable = this.searchTerms.pipe(
      debounceTime(300),

      distinctUntilChanged(),
      switchMap((term: string) => {
        let fr: any = [];
        this.data.filter((v: any, i: any) => {
          if (v.toLocaleLowerCase().includes(term.toLocaleLowerCase())) {
            fr.push(v);
          }
        });
        return of(fr);
      })
    );
  }

  ngOnChanges() {}

  onValuesChange(val: any[]) {
    this.slicedNames = val;
  }

  ngAfterViewInit() {
    this.cdRef.detectChanges();
  }

  searchTerms = new Subject<string>();

  search(term: string) {
    this.searchTerms.next(term);
  }

  newRulesCB(val: any) {
    this.currentPage = val.currentPage;
    this.btnNosArr = val.btnNosArr;

    this.rulesPerPage = val.rulesPerPage;
    this.selectArr = val.newPageRangeArr;
    this.slicedNames = val.valuesArr;
  }
}
```

</li>
</ol>
