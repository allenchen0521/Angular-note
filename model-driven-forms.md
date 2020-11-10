# Model Driven Forms

### Creating the form and registering the controls

1. Import ReactiveFormModule\(`ctrl+p` app.module.ts\)

   `app.module.ts`

   ```typescript
    Import { ReactiveFormsModule } from '@angular/forms';

    @NgModule({
        declarations: [
            AppComponent
        ],
        imports: [
            BrowserModule,
            ReactiveFormsModule
        ],
        providers: [],
        bootstrap: [AppComponent]
    })
    export class AppModule { }
   ```

   `app.component.ts`

   ```typescript
    import { FormGroup } from '@angular/forms';

    export class AppComponent {
        // Model Driven Forms 需要宣告行建立 FormGroup, 再後續建立 FormControl
        signUpForm: FormGroup;
    }
   ```

2. 設定表單 FormControl

   `app.component.ts`

   ```typescript
    import { Component, OnInit } from '@angular/core';
    import { FormGroup, FormControl } from '@angular/forms';

    export class AppComponent implements OnInit {
        signUpForm: FormGroup;

        // 需要再 DOM 元素建立完成後 new FormGroup and FormControl
        ngOnInit() {
            // 這邊 FormControl 可以接收一些參數在這邊先預設 null
            this.signUpForm = new FormGroup({
                'username': new FormControl(null),
                'email': new FormControl(null),
                'gender': new FormControl(null)
            });
        }
    }
   ```

   `app.component.html`

   ```html
    <!-- form 標籤使用 formGroup directive 對應自訂的 signUpForm FormGroup -->
    <form [formGroup]="signUpForm">
        <!-- 使用 formControlName directive 對應 signUpForm 自訂的 FormControl -->
        <input type="text" class="form-control"
            formControlName="username">
        <input type="text" class="form-control"
            formControlName="email">
        <div class="radio" *ngFor="let gender of genders">
            <input type="radio"
                [value]="gender"
                formControlName="gender">{{ gender }}
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
   ```

3. Angular 表單提交且取得

   `app.component.html`

   ```html
    <!-- Angular 使用 ngSubmit 事件模擬 formSubmit, 因此我們不會在 form 標籤加入 action 屬性 -->
    <form [formGroup]="signUpForm" (ngSubmit)="onSubmit()">
   ```

   `app.component.ts`

   ```typescript
    onSubmit() {
        // app.component.ts 已經宣告 signUpForm 為 FormGroup 可以直接取值
        console.log(this.signUpForm.value);
    }
   ```

4. Adding Validation to Check User Input

   `app.component.ts`

   ```typescript
    import { Component, OnInit } from '@angular/core';
    import { FormGroup, FormControl } from '@angular/forms';

    export class AppComponent implements OnInit {
        genders = ['male', 'female'];
        signupForm: FormGroup;

        ngOnInit() {
            this.signupForm = new FromGroup({
                'username': new FormControl(null, Validators.required),
                'email': new FormControl(null, [Validators.required, Validators.email]),
                'gender': new FormControl(null)
            });
        }

    }
   ```

   `app.component.html`

   ```html
    <span *ngIf="!signupForm.get('username').valid && signupForm.get('username').touched"
        class="help-block">Please enter a valid username!
    </span>
   ```

   **檢視所有錯誤訊息**

   ```html
   <pre>
       signupForm.get('email').value = {{ signupForm.get('email').value }}
       <!-- 欄位是否合法 -->
       signupFrom.get('email).valid = {{ signupFrom.get('email').valid }}
       signupFrom.get('email).invalid = {{ signupFrom.get('email').invalid }}

       <!-- 欄位是否異動 -->
       signupFrom.get('email).pristine = {{ signupFrom.get('email').pristine }}
       signupFrom.get('email).dirty = {{ signupFrom.get('email').dirty }}

       <!-- 欄位是否觸碰過 -->
       signupFrom.get('email).touched = {{ signupFrom.get('email').touched }}
       signupFrom.get('email).untouched = {{ signupFrom.get('email').untouched }}

       <!-- 欄位是否啟用 -->
       signupFrom.get('email).enabled = {{ signupFrom.get('email').enabled }}
       signupFrom.get('email).disabled = {{ signupFrom.get('email').disabled }}

       <!-- 欄位 errors 物件 -->
       signupFrom.get('email).errors = {{ signupFrom.get('email').errors | json }}
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

1. 表單如何賦值

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

