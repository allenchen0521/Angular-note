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
    import { FormGroup, FormControl, Validators } from '@angular/forms';

    export class AppComponent implements OnInit {
        genders = ['male', 'female'];
        signupForm: FormGroup;

        ngOnInit() {
            this.signupForm = new FromGroup({
                'username': new FormControl(null, Validators.required),
                'email': new FormControl(null, [Validators.required, Validators.email]),
                'gender': new FormControl('male')
            });
        }

    }
   ```

   `app.component.html`

   ```html
    <input type="text" id="username" class="form-control"
        formControlName="username">

    <span class="help-block" *ngIf="!signUpForm.get('username').valid && signUpForm.get('username').touched">Please enter a valid username</span>
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

5. Reactive: Creating Custom Validators

    `app.component.ts`

    ```typescript
    import { Component, OnInit } from '@angular/core';
    import { FormGroup, FormControl, Validators } from '@angular/forms';

    export AppComponent implments OnInit {
        genders = ['male', 'female'];
        signupForm: FormGroup;
        forbiddenUsernames = ['Chris', 'Anna'];

        ngOnInit() {
            this.signupForm = new FromGroup({
                // bind(this) 將 this 指向 AppComponent, 否則 this 會指向觸發事件的物件
                'username': new FormControl(null, [Validators.required, this.forbiddenNames.bind(this)]),
                'email': new FormControl(null, [Validators.required, Validators.email]),
                'gender': new FormControl('male')
            });
        }

        onSubmit() {
            console.log(this.signUpForm);
        }

        forbiddenNames(control: FormControl): {[s: string]: boolean} {
            if(this.forbiddenUsernames.indexOf(control.value) !== -1) {
                // FormControl invalid 回傳物件
                return {'nameIsforbidden': true};
            }

            // FormControl valid 回傳 null
            return null;
        }
    }
    ```

    `app.component.html`

    ```html
    <input type="text" id="username" class="form-control"
        formControlName="username">

    <span *ngIf="!signUpForm.get('username').valid && signUpForm.get('username').touched">
        <span class="help-block" *ngIf="!signUpForm.get('username').errors['nameIsForbidden']">Please enter a valid username</span>

        <span class="help-block" *ngIf="!signUpForm.get('username').errors['required']">The field is required!</span>
    </span>
    ```

6. Reative: Grouping Controls

   `app.component.ts`

   ```typescript
    import { Component, OnInit } from '@angular/core';
    import { FormGroup, FormControl, Validators } from '@angular/forms';

    export class AppComponent implements OnInit {
        genders = ['male', 'female'];
        signUpForm: FormGroup;

        ngOnInit() {
            this.signUpForm = new FormGroup({
                'userData': new FormGroup({
                    'username': new FormControl(null, Validators.required),
                    'email': new FormControl(null, [Validators.required, Validators.email])
                }),
                'gender': new FormControl('male')
            });
        }

        onSubmit() {
            console.log(this.signUpForm);
        }
    }
   ```

   `app.component.html`

   ```html
    <!-- form 標籤使用 formGroup directive 對應自訂的 signUpForm FormGroup -->
    <form [formGroup]="signUpForm">
        <!-- 使用 formGroupName directive 對應 signUpForm 自訂的 FormGroup -->
        <div formGroupname="userData">
            <input type="text" class="form-control"
                formControlName="username">
            <span *ngIf="!signUpForm.get('userData.username').valid && signUpForm.get('userData.username').touched">Please enter a valid username!</span>
            <input type="text" class="form-control"
                formControlName="email">
            <span *ngIf="!signUpForm.get('userData.email').valid && signUpForm.get('userData.email').touched"></span>
            <span></span>
        </div>
        <div class="radio" *ngFor="let gender of genders">
            <input type="radio"
                [value]="gender"
                formControlName="gender">{{ gender }}
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
   ```

7. Reactive: Arrays of FormControls(FormArray)

   `app.component.html`

   ```html
    <!-- form 標籤使用 formGroup directive 對應自訂的 signUpForm FormGroup -->
    <form [formGroup]="signUpForm">
        <!-- 使用 formGroupName directive 對應 signUpForm 自訂的 FormGroup -->
        <div formGroupname="userData">
            <input type="text" class="form-control"
                formControlName="username">
            <span *ngIf="!signUpForm.get('userData.username').valid && signUpForm.get('userData.username').touched">Please enter a valid username!</span>
            <input type="text" class="form-control"
                formControlName="email">
            <span *ngIf="!signUpForm.get('userData.email').valid && signUpForm.get('userData.email').touched"></span>
        </div>
        <div class="radio" *ngFor="let gender of genders">
            <input type="radio"
                [value]="gender"
                formControlName="gender">{{ gender }}
        </div>
        <div formArrayName="hobbies">
            <h4>Your Hobbies</h4>
            <button class="btn btn-default" type="button" (click)="onAddHobby()">Add Hobby</button>

            <div class="form-group" *ngFor="let hobbyControl of signUpForm.get('hobbies').controls; let i = index">
                <input type="text" class="form-control"
                    [formControlName]="i">
            </div>
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
   ```

   `app.component.ts`

   ```typescript
    import { Component, OnInit } from '@angular/core';
    import { FormGroup, FormControl, FormArray, Validators } from '@angular/forms';

    export class AppComponent implements OnInit {
        genders = ['male', 'female'];
        signUpForm: FormGroup;

        ngOnInit() {
            this.signUpForm = new FormGroup({
                'userData': new FormGroup({
                    'username': new FormControl(null, Validators.required),
                    'email': new FormControl(null, [Validators.required, Validators.email])
                }),
                'gender': new FormControl('male'),
                'hobbies': new FormArray([])
            });
        }

        onSubmit() {
            console.log(this.signUpForm);
        }

        onAddHobby() {
            const control = new FormControl(null, Validators.required);

            // 必須強制轉型成 FormArray TSLint 才不會產生錯誤
            (<FormArray>this.signUpform.get('hobbies')).push(control);
        }
    }
   ```

### Form setValue vs patchValue

1. 表單如何賦值

   `app.component.html`

   ```html
    
   ```

   `app.component.ts`

   ```typescript
    import { Component, OnInit } from '@angular/core';
    import { FormGroup, FormControl, FormArray, Validators } from '@angular/forms';

    export class AppComponent implements OnInit {
        genders = ['male', 'female'];
        signupForm: FormGroup;

        ngOnInit() {
            this.signUpForm = new FormGroup({
                'userData': new FormGroup({
                    'username': new FormControl(null, Validators.required),
                    'email': new FormControl(null, [Validators.requried, Validators.email])
                }),
                'gender': new FormControl('male'),
                'hobbies': new FormArray([])
            });

            // setValue 需要全部覆寫 FormControl
            this.signUpForm.setValue({
                'userData': {
                    'username': 'Max',
                    'email': 'max@test.com'
                },
                'gender': 'male',
                'hobbies': []
            });

            // patchValue 可以覆寫特定內容, 需要透過 NgForm.form 物件來操作
            this.signUpForm.patchValue({
                'userData': {
                    'username': 'Anna'
                }
            });
        }
    }
   ```

