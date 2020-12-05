# Template Driven Forms

### Creating the form and registering the controls



1. Import FormModule\(`ctrl+p` app.module.ts\)

   ```typescript
    import { FormsModule } from '@angular/forms';

    @NgModule({
        declarations: [
            AppComponent
        ],
        imports: [
            BrowserModule,
            FormsModule
        ],
        providers: [],
        bootstrap: [AppComponent]
    })
    export class AppModule {  }
   ```

2. 設定表單 FormControl

   ```html
    <!-- ngModel 告訴 Angular 這是需要被設定成 FormControl -->
    <!-- name="username" Name of FormControl -->
    <input type="text" id="username" class="form-control"
           ngModel
           name="username" />
   ```

3. Angular 表單提交且取得

   `app.component.html`

   ```html
    <!-- Angular 使用 ngSubmit 事件模擬 formSubmit, 因此我們不會在 form 標籤加入 action 屬性 -->
    <!-- Angular 使用 ngForm 自動將表單轉換成 JavaScript Object -->
    <form (ngSubmit)="onSubmit(form)" #form="ngForm">
   ```

   `app.component.ts`

   ```typescript
    onSubmit(form: NgForm) {
        console.log(form);
    }
   ```

4. Adding Validation to Check User Input

   `app.component.html`

   ```html
    <!-- 新增 required 屬性, Angular 會透過 ngModel 自動驗證填入值是否合法, 動態加入 ng-invalid class -->
    <input type="text" id="username" class="form-control"
        ngModel
        name="username"
        required>
    <input type="email" class="form-control"
        #tEmail="ngModel"
        name="email"
        required email>

    <!-- button 可以透過 propertyp binding 是否可以點擊 -->
    <button class="btn btn-primary" type="submit" [disabled]="!form.valid">Submit</button>
   ```

   `app.component.css`

   ```css
    /* 針對 ngModel 動態產生的 class 改變 border 顏色 */
    input.ng-invalid { border: 1px solid red };

    /* 但如上會進入畫面即產生紅框不合法, 所以可以加上 ng-touched 讓 FormControl 被點擊過才驗證 */
    input.ng-invalid.ng-touched { border: 1px solid red };
   ```

   **檢視所有錯誤訊息**

   ```html
   <pre>
       tEmail.value = {{ tEmail.value }}
       tEmail.valid = {{ tEmail.valid }}
       tEmail.invalid = {{ tEmail.invalid }}
       tEmail.pristine = {{ tEmail.pristine }}
       tEmail.dirty = {{ tEmail.dirty }}
       tEmail.touched = {{ tEmail.touched }}
       tEmail.untouched = {{ tEmail.untouched }}
       tEmail.enabled = {{ tEmail.enabled }}
       tEmail.disabled = {{ tEmail.disabled }}
       tEmail.errors = {{ tEmail.errors | json }}
   </pre>
   ```

5. Outputting Validation Error Messages

   `app.component.html`

   ```html
    <!-- 由於 ngModel 會產生 ng-valid/ng-touched 等等常用屬性, 可以儲存成變數動態顯示錯誤訊息 -->
    <input type="text" id="username" class="form-control"
        ngModel
        name="username"
        #username="ngModel">

    <span class="help-block" *ngIf="!username.valid && username.touched">Please enter a valid username</span>
   ```

6. Set Default Values with ngModel Property Binding

   `app.component.ts`

   ```typescript
    export class AppComponent {
        @ViewChild('form') signUpForm: NgForm;
        defaultQuestion = 'pet';
    }
   ```

   `app.component.html`

   ```html
    <!-- 下拉選單如果需要預設值 ngModel 可以使用 one-way-binding -->
    <select id="secret" class="form-control"
        [ngModel]="defaultQuestion"
        name="secret">
   ```

7. Using ngModel with two-way-binding

   `app.component.html`

   ```html
    <!-- 需要立即檢核或是動態顯示輸入內容可以使用 two-way-binding -->
    <!-- 建立 textarea 透過 two-way-binding 方式立即顯示輸入的文字內容 -->
    <div class="form-group">
        <textarea class="form-control"
            [(ngModel)]="answer"
            name="answer"></textarea>
    </div>
    <p>Your reply: {{ answer }}</p>
   ```

   `app.component.ts`

   ```typescript
    export class AppComponent {
        @ViewChild('form') signUpForm: NgForm;
        defaultQuestion = 'pet';
        answer = '';
    }
   ```

### Form setValue vs patchValue

1. 表單如何修改預設值

   `app.component.html`

   ```html
    <!-- 表單結構承上 -->
    <button type="button" class="btn btn-primary" (click)="suggestUserName()">Suggest an Username</button>
   ```

   `app.component.ts`

   ```typescript
    export class AppComponent {
        @ViewChild('form') signUpForm: NgForm;

        suggestUserName() {
            // setValue 需要全部覆寫 FormControl
            this.signUpForm.setValue({
                username: 'foo',
                email: 'foo@gmail.com',
                secret: 'pet',
                answer: 'fooboo',
                gender: 'female'
            });

            // patchValue 可以覆寫特定內容, 需要透過 NgForm.form 物件來操作
            this.signUpForm.form.patchValue({
                username: 'foo',
                email: 'foo@gmail.com'
            })
        }
    }
   ```

