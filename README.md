# Template Driven Forms

### Creating the form and registering the controls

1. Import FormModule\(`ctrl+p` app.module.ts\)

   ```typescript
    Import { FormsModule } from '@angular/forms';

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

   ```markup
    <!-- ngModel 告訴 Angular 這是需要被設定成 FormControl -->
    <!-- name="username" Name of FormControl -->
    <input type="text" id="username" class="form-control"
           ngModel
           name="username" />
   ```

3. Angular 表單提交且取得

   `app.component.html`

   ```markup
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

