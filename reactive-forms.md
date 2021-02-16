# ReactiveForm

### Creating the form and registering the controls

1. Import ReactiveFormModule\(`ctrl+p` app.module.ts\)

   `app.module.ts`

   ```typescript
    import { ReactiveFormsModule } from '@angular/forms';

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
    import { Component } from '@angular/core';
    import { FormGroup } from '@angular/forms';

    @Component({
        selector: 'app-root',
        templateUrl: './app.component.html',
        styleUrls: ['./app.component.scss']
    })
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

    @Component({
        selector: 'app-root',
        templateUrl: './app.component.html',
        styleUrls: ['./app.component.scss']
    })
    export class AppComponent implements OnInit {
        signUpForm: FormGroup;

        // 需要再 DOM 元素建立完成前 new FormGroup and FormControl
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
        signUpForm: FormGroup;

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
       signUpForm.get('email').value = {{ signUpForm.get('email').value }}
       signUpForm.controls.email.value = {{ signUpForm.controls.email.value }}

       <!-- 欄位是否合法 -->
       signUpForm.get('email).valid = {{ signUpForm.get('email').valid }}
       signUpForm.get('email).invalid = {{ signUpForm.get('email').invalid }}

       signUpForm.controls.email.valid = {{ signUpForm.controls.email.valid }}
       signUpForm.controls.email.invalid = {{ signUpForm.controls.email.invalid }}

       <!-- 欄位是否異動 -->
       signUpForm.get('email).pristine = {{ signUpForm.get('email').pristine }}
       signUpForm.get('email).dirty = {{ signUpForm.get('email').dirty }}

       signUpForm.controls.email.pristine = {{ signUpForm.controls.email.pristine }}
       signUpForm.controls.email.dirty = {{ signUpForm.controls.email.dirty }}

       <!-- 欄位是否觸碰過 -->
       signUpForm.get('email).touched = {{ signUpForm.get('email').touched }}
       signUpForm.get('email).untouched = {{ signUpForm.get('email').untouched }}

        signUpForm.controls.email.touched = {{ signUpForm.controls.email.touched }}
        signUpForm.controls.email.untouched = {{ signUpForm.controls.email.untouched }}

       <!-- 欄位是否啟用 -->
       signUpForm.get('email).enabled = {{ signUpForm.get('email').enabled }}
       signUpForm.get('email).disabled = {{ signUpForm.get('email').disabled }}

       <!-- 欄位 errors 物件 -->
       signUpForm.get('email).errors = {{ signUpForm.get('email').errors | json }}
   </pre>
   ```

5. Reactive: Creating Custom Validators

    `app.component.ts`

    ```typescript
    import { Component, OnInit } from '@angular/core';
    import { FormGroup, FormControl, Validators } from '@angular/forms';

    export AppComponent implments OnInit {
        genders = ['male', 'female'];
        signUpForm: FormGroup;
        forbiddenUsernames = ['Chris', 'Anna'];

        ngOnInit() {
            this.signUpForm = new FromGroup({
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
    import { FormBuilder, FormGroup, FormControl, Validators } from '@angular/forms';

    @Component({
        selector: 'app-root',
        templateUrl: './app.component.html',
        stylesUrl: './app.component.scss'
    })
    export class AppComponent implements OnInit {
        genders = ['male', 'female'];
        signUpForm: FormGroup;

        constructor(formBuilder: FormBuilder) {}

        ngOnInit() {
            // Method1
            this.signUpForm = new FormGroup({
                'userData': new FormGroup({
                    'username': new FormControl(null, [Validators.required, this.forbiddenNames.bind(this)]),
                    'email': new FormControl(null, [Validators.required, Validators.email])
                }),
                'gender': new FormControl('male')
            });

            // Method2 注入 FormBuilder 減少我們在創建 formGroup 時有太多重覆的定義
            this.signUpForm = this.formBuilder.group({
                'userData': this.formBuilder.group({
                    'username': ['', Validators.required],
                    'email': ['', [Validators.required, Validators.email]]
                }),
                'gender': ['male']
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

7. Creating a Custom Async Validator

    `app.component.ts`

    ```typescript
    import { Component } from '@angular/core';
    import { FormBuilder, FormGroup, FormControl, Validators } from '@angular/forms';

    @Component({
        'selector': 'app-root',
        'templateUrl': './app.component.html',
        'styleUrls': './app.component.css'
    })
    export class AppComponent implement OnInit {
        genders = ['male', 'female'];
        signUpForm: FormGroup;

        constructor(formBuilder: FormBuilder) {}

        ngOnInit() {
            // Method1
            this.signUpForm = new FormGroup({
                'userData': new FormGroup({
                    'username': new FormControl(null, [Validators.required, this.forbiddenNames.bind(this)]),
                    'email': new FormControl(null, [Validators.required, Validators.email])
                }),
                'gender': new FormControl('male', Validators.required)
            });

            // Method2 注入 FormBuilder 減少我們在創建 formGroup 時有太多重覆的定義
            this.signUpForm = this.formBuilder.group({
                'userData': this.formBuilder.group({
                    'username': ['', [Validators.required, this.forbiddenNames.bind(this)]],
                    'email': ['', [Validators.required, Validators.email], this.forbiddenEmail]
                }),
                'gender': ['', Validators.required]
            })
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

        forbiddenEmail(control: FormControl): Promise<any> | Observable<any> {
            const promise = new Promise((resolve, reject) => {
                setTimeout(() => {
                    if('test@test.com' === control.value) {
                        resolve({'emailIsForbidden': true});
                    } else {
                        resolve(null);
                    }
                }, 1500);
            });

            return promise;
        }
    }
    ```

    `app.component.html`

    ```html
    <div class="form-group">
        <label for="email">email</label>
        <input type="text" id="email" class="form-control" formControlName="email">
        <span
            class="help-block"
            *ngIf="signUpForm.get('userData.email').errors?.emailIsForbidden">Please enter a valid email</span>
    </div>
    ```

8. Reactive: Arrays of FormControls(FormArray)

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
        <!-- 使用 formArrayName directive 對應 signUpForm 自訂的 FormArray -->
        <div formArrayName="hobbies">
            <h4>Your Hobbies</h4>
            <button class="btn btn-default" type="button" (click)="onAddHobby()">Add Hobby</button>
            <!-- 新版 需要使用 getControls() 方法取得 FormArray 資料, 否則 compiler 會產生 error -->
            <div class="form-group" *ngFor="let hobbyControl of getControls(); let i = index">
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
            return (<FormArray>this.signUpform.get('hobbies')).push(control);
        }

        getControls() {
            return (<FormArray>this.signUpForm.get('hobbies')).control;
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

