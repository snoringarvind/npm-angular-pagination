# App Pagination

- An Angular directive for pagination.

![gif](https://github.com/snoringarvind/appPagination/blob/main/src/assets/pagination-record.gif)

### Angular Version

- This Angular directive is created using Angular 12+ .

### How to install

```
npm i @snoringarvind/pagination
```

### Set up

- #### module.ts

  Import pagination in your module.ts file and add it to the imports[ ].

  ```javascript
  import { PaginationModule } from "@snoringarvind/pagination";

  @NgModule({
    declarations: [AppComponent],
    imports: [BrowserModule, FormsModule, PaginationModule],
    providers: [],
    bootstrap: [AppComponent],
  })
  export class AppModule {}
  ```

### How to use

1. #### Add pagination directive to your container div element

   - #### component.html
     ```html
     <div class="pagination" pagination #pagination="pagination"></div>
     ```

1. #### Pass your [data] on which you want to implement your pagination. The data should be in the form of an array.

   - #### component.html

     The _data_ is your data variable.

     ```html
     <div
       class="pagination"
       pagination
       #pagination="pagination"
       [valuesArr]="data"
     ></div>
     ```

1. #### Adding buttons to navigate forward and backward.

   - #### component.html
     ```html
     <div
       class="pagination"
       pagination
       #pagination="pagination"
       [valuesArr]="data"
     >
       <button class="page-first-btn" (click)="pagination.first()"><<</button>
       <button class="page-prev-btn" (click)="pagination.prev()"><</button>
       <button class="page-next-btn" (click)="pagination.next()">></button>
       <button class="page-last-btn" (click)="pagination.last()">>></button>
     </div>
     ```

1. #### Adding clickable page number buttons.

   - #### component.html

     Five clickable buttons are displayed with the current page button in the middle.
     With [ngClass] we highlight the current page number.
     The variables _currentPage_ and _btnNosArr_ we get from the call-back, which we will cover later.

     ```html
     <div
       class="pagination"
       pagination
       #pagination="pagination"
       [valuesArr]="data"
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

1. #### Adding an input element to jump to the desired page number.

   - #### component.html
     ```html
     <div
       class="pagination"
       pagination
       #pagination="pagination"
       [valuesArr]="data"
     >
       <div class="pageno-input">
         <input
           name="input"
           [ngModel]="currentPage"
           (keyup.enter)="pagination.change($event)"
         />
       </div>
     </div>
     ```

1. #### Adding a dropdown to select the number of data-values on each page.

   - #### component.html

     The default selection is [5, 10, 25, 50 ,100, 250, 500] but you can also pass your own selection range.
     The variable _selectArr_ we get from the call-back, which we will cover later.

     ```html
     <div
       class="pagination"
       pagination
       #pagination="pagination"
       [valuesArr]="data"
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

1. #### Adding a call-back function.

   - #### component.ts

     The callback function return the new sliced data and other values.

     ```javascript
         newRulesCB(val: any) {
           this.currentPage = val.currentPage;
           this.btnNosArr = val.btnNosArr;
           this.rulesPerPage = val.rulesPerPage;
           this.selectArr = val.newPageRangeArr;
           this.slicedValues = val.valuesArr;
         }
     ```

   - #### component.html

     The variable _slicedValues_ contains our new sliced data for the current page.

     ```html
     <div
       class="pagination"
       pagination
       #pagination="pagination"
       [valuesArr]="data"
       [pageRangeArr]="[5, 20, 30, 40, 50]"
       (newRules)="newRulesCB($event)"
     ></div>
     ```

1. #### Adding a search box to filter the data. (Optional)

   - #### component.ts

     Here we create Observables for searchTerms and searchObservable to pass the seach-term and the latest search filtered data inside the directive.
     You can look more about creating search observables on [Angular Documentation](https://angular.io/guide/practical-observable-usage)

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

   - #### component.html

     ```html
     <div class="search-box">
       <input
         #searchBox
         (input)="search(searchBox.value)"
         placeholder="Search..."
       />
     </div>
     ```

1. #### The entire component.html file code is here.

   You just need to apply the directive to a div container and create a template refernce variable (#pagination) which points to the directive's instance.

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
         pagination
         #pagination="pagination"
         (newRules)="newRulesCB($event)"
         [valuesArr]="data"
         [searchTerms]="searchTerms"
         [searchObservable]="searchObservable"
         [pageRangeArr]="[5, 20, 30, 40, 50]"
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

1. #### The entire component.ts file code is here.

   In your component.ts file implement a callback function to receive the new sliced values and other details.

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
     searchTerms = new Subject<string>();

     constructor(private cdRef: ChangeDetectorRef) {}

     search(term: string) {
       this.searchTerms.next(term);
     }

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

     ngAfterViewInit() {
       this.cdRef.detectChanges();
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

### Live demo

- [Click to see the live demo.](https://stackblitz.com/edit/angular-npm-pagination?file=src/app/app.module.ts)
