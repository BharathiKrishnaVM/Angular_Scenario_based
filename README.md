# Angular_Scenario_based
# Question1:
***I have a reactive forms of three fields accept the user to enter any of the two fields in angular***

To create a reactive form in Angular that requires the user to fill in any two out of three fields, you can use custom validation. This custom validator will check if at least two out of the three fields are filled in.

Step-by-Step Implementation
Set Up the Reactive Form: Create a reactive form with three fields.
Create a Custom Validator: Implement a custom validator to check if at least two fields are filled in.
Apply the Validator to the Form: Apply the custom validator to the form group.
Example Implementation
Step 1: Set Up the Reactive Form
First, set up the reactive form in your component.
```javascript
// app.module.ts
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [
    // Other imports...
    ReactiveFormsModule
  ],
  // Other configurations...
})
export class AppModule { }
```

```javascript
// app.component.ts
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  myForm: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.myForm = this.fb.group({
      field1: [''],
      field2: [''],
      field3: ['']
    }, { validators: this.atLeastTwoFieldsValidator });
  }

  atLeastTwoFieldsValidator(formGroup: FormGroup) {
    const field1 = formGroup.get('field1').value;
    const field2 = formGroup.get('field2').value;
    const field3 = formGroup.get('field3').value;

    const filledFields = [field1, field2, field3].filter(field => field !== '').length;

    return filledFields >= 2 ? null : { atLeastTwoFields: true };
  }

  onSubmit() {
    if (this.myForm.valid) {
      console.log('Form Submitted!', this.myForm.value);
    } else {
      console.log('Form is invalid');
    }
  }
}
```
Step 2: Create the Template
Create the template for the form in your component's HTML file.

```html
<!-- app.component.html -->
<form [formGroup]="myForm" (ngSubmit)="onSubmit()">
  <div>
    <label for="field1">Field 1</label>
    <input id="field1" formControlName="field1">
  </div>
  <div>
    <label for="field2">Field 2</label>
    <input id="field2" formControlName="field2">
  </div>
  <div>
    <label for="field3">Field 3</label>
    <input id="field3" formControlName="field3">
  </div>
  <div *ngIf="myForm.errors?.atLeastTwoFields">
    <p>Please fill in at least two fields.</p>
  </div>
  <button type="submit">Submit</button>
</form>
```

Explanation
Reactive Form Setup:

The FormBuilder service is used to create a reactive form with three fields: field1, field2, and field3.
The custom validator atLeastTwoFieldsValidator is applied to the form group.
Custom Validator:

The atLeastTwoFieldsValidator function checks if at least two out of the three fields are filled in.
It retrieves the values of the three fields and counts how many of them are not empty.
If at least two fields are filled in, the validator returns null (indicating no error). Otherwise, it returns an error object { atLeastTwoFields: true }.
Template:

The form template binds to the reactive form using [formGroup].
Each input field is bound to a form control using formControlName.
An error message is displayed if the custom validator returns an error.
The form is submitted using the (ngSubmit) event, which calls the onSubmit method.

# Question2
***now i have three fields after entering the value in email show address custom form and if the value is empty remove the newly added form***

To achieve this functionality, you can use Angular's reactive forms along with conditional rendering in the template. Specifically, you can listen for changes in the email field and dynamically add or remove the address form group based on whether the email field has a value.

Step-by-Step Implementation
Set Up the Reactive Form: Create a reactive form with three fields.
Listen for Changes in the Email Field: Use the valueChanges observable to listen for changes in the email field.
Dynamically Add or Remove the Address Form Group: Add or remove the address form group based on the value of the email field.
Example Implementation
Step 1: Set Up the Reactive Form
First, set up the reactive form in your component.
```javascript
// app.module.ts
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [
    // Other imports...
    ReactiveFormsModule
  ],
  // Other configurations...
})
export class AppModule { }
```
```javascript
// app.component.ts
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  myForm: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.myForm = this.fb.group({
      email: ['', Validators.email],
      field2: [''],
      field3: ['']
    });

    this.onEmailChanges();
  }

  onEmailChanges(): void {
    this.myForm.get('email').valueChanges.subscribe(email => {
      if (email) {
        this.addAddressFormGroup();
      } else {
        this.removeAddressFormGroup();
      }
    });
  }

  addAddressFormGroup(): void {
    if (!this.myForm.get('address')) {
      this.myForm.addControl('address', this.fb.group({
        street: [''],
        city: [''],
        zip: ['']
      }));
    }
  }

  removeAddressFormGroup(): void {
    if (this.myForm.get('address')) {
      this.myForm.removeControl('address');
    }
  }

  onSubmit() {
    if (this.myForm.valid) {
      console.log('Form Submitted!', this.myForm.value);
    } else {
      console.log('Form is invalid');
    }
  }
}
```
Step 2: Create the Template
Create the template for the form in your component's HTML file.
```html
<!-- app.component.html -->
<form [formGroup]="myForm" (ngSubmit)="onSubmit()">
  <div>
    <label for="email">Email</label>
    <input id="email" formControlName="email">
  </div>
  <div>
    <label for="field2">Field 2</label>
    <input id="field2" formControlName="field2">
  </div>
  <div>
    <label for="field3">Field 3</label>
    <input id="field3" formControlName="field3">
  </div>
  <div formGroupName="address" *ngIf="myForm.get('address')">
    <h3>Address</h3>
    <div>
      <label for="street">Street</label>
      <input id="street" formControlName="street">
    </div>
    <div>
      <label for="city">City</label>
      <input id="city" formControlName="city">
    </div>
    <div>
      <label for="zip">Zip</label>
      <input id="zip" formControlName="zip">
    </div>
  </div>
  <button type="submit">Submit</button>
</form>
```
Explanation
Reactive Form Setup:

The FormBuilder service is used to create a reactive form with three fields: email, field2, and field3.
The onEmailChanges method subscribes to changes in the email field.
Listen for Changes in the Email Field:

The valueChanges observable is used to listen for changes in the email field.
If the email field has a value, the addAddressFormGroup method is called to add the address form group.
If the email field is empty, the removeAddressFormGroup method is called to remove the address form group.
Dynamically Add or Remove the Address Form Group:

The addAddressFormGroup method checks if the address form group already exists. If not, it adds the address form group with street, city, and zip fields.
The removeAddressFormGroup method checks if the address form group exists. If so, it removes the address form group.
Template:
The form template binds to the reactive form using [formGroup].
Each input field is bound to a form control using formControlName.
The address form group is conditionally rendered using *ngIf based on whether the address form group exists in the form.

# Question3
***I have a rating component with the input of rating and a form in another component called rating form control which needs the value from rating component***

To achieve this, you can create a custom form control component for the rating input and integrate it with a reactive form in another component. This involves creating a custom form control component that implements the ControlValueAccessor interface, which allows it to work seamlessly with Angular's reactive forms.

Step-by-Step Implementation
Create the Rating Component: Implement the rating component that will act as a custom form control.
Implement ControlValueAccessor: Implement the ControlValueAccessor interface in the rating component.
Create the Form Component: Create a form component that uses the rating component as a form control.
Example Implementation
Step 1: Create the Rating Component
First, create the rating component that will act as a custom form control.
```javascript
ng generate component rating
```
rating.component.ts
```javascript
import { Component, forwardRef } from '@angular/core';
import { ControlValueAccessor, NG_VALUE_ACCESSOR } from '@angular/forms';

@Component({
  selector: 'app-rating',
  templateUrl: './rating.component.html',
  styleUrls: ['./rating.component.css'],
  providers: [
    {
      provide: NG_VALUE_ACCESSOR,
      useExisting: forwardRef(() => RatingComponent),
      multi: true
    }
  ]
})
export class RatingComponent implements ControlValueAccessor {
  rating: number = 0;
  onChange: any = () => {};
  onTouched: any = () => {};

  writeValue(value: any): void {
    this.rating = value;
  }

  registerOnChange(fn: any): void {
    this.onChange = fn;
  }

  registerOnTouched(fn: any): void {
    this.onTouched = fn;
  }

  setRating(value: number): void {
    this.rating = value;
    this.onChange(this.rating);
    this.onTouched();
  }
}
```
rating.component.html
```html
<div class="rating">
  <span *ngFor="let star of [1, 2, 3, 4, 5]" (click)="setRating(star)">
    {{ star <= rating ? '★' : '☆' }}
  </span>
</div>
```
Step 2: Create the Form Component
Create a form component that uses the rating component as a form control.
```javascript 
ng generate component rating-form
```
rating-form.component.ts
```javascript
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup } from '@angular/forms';

@Component({
  selector: 'app-rating-form',
  templateUrl: './rating-form.component.html',
  styleUrls: ['./rating-form.component.css']
})
export class RatingFormComponent implements OnInit {
  ratingForm: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.ratingForm = this.fb.group({
      rating: [0]
    });
  }

  onSubmit() {
    if (this.ratingForm.valid) {
      console.log('Form Submitted!', this.ratingForm.value);
    } else {
      console.log('Form is invalid');
    }
  }
}
```
rating-form.component.html
```html
<form [formGroup]="ratingForm" (ngSubmit)="onSubmit()">
  <div>
    <label for="rating">Rating</label>
    <app-rating formControlName="rating"></app-rating>
  </div>
  <button type="submit">Submit</button>
</form>
```
Explanation
Rating Component:

The RatingComponent implements the ControlValueAccessor interface, which allows it to be used as a custom form control.
The writeValue method is called by Angular to update the component with the value from the form model.
The registerOnChange and registerOnTouched methods are used to register callbacks that Angular will call when the value or touch state changes.
The setRating method updates the rating value and calls the registered onChange and onTouched callbacks.
Form Component:

The RatingFormComponent creates a reactive form with a single control for the rating.
The app-rating component is used as a form control with formControlName="rating".
The form is submitted using the (ngSubmit) event, which calls the onSubmit method.

# Question4
***Interfaces in Typescript***

***I have a interface with name, mail, age I want to use that interface along with two mandatory properties which are empId, empDesgination***

To achieve this, you can define an interface for the common properties (name, mail, age) and then extend this interface to include the mandatory properties (empId, empDesignation). This approach allows you to create a more modular and reusable type definition.

Step-by-Step Implementation
Define the Base Interface: Create an interface for the common properties.
Extend the Base Interface: Create another interface that extends the base interface and includes the mandatory properties.
Use the Extended Interface: Use the extended interface in your components or services.
Example Implementation
Step 1: Define the Base Interface
First, define the base interface for the common properties.
```typescript
// employee-base.interface.ts
export interface EmployeeBase {
  name: string;
  mail: string;
  age: number;
}
```

Step 2: Extend the Base Interface
Next, create another interface that extends the base interface and includes the mandatory properties.
```typescript
// employee.interface.ts
import { EmployeeBase } from './employee-base.interface';

export interface Employee extends EmployeeBase {
  empId: string;
  empDesignation: string;
}
```

***To Omit the mail in EmployeeBase interface then use Omit***
```typescript
// employee.interface.ts
import { EmployeeBase } from './employee-base.interface';

export interface Employee extends Omit<EmployeeBase, 'mail'> {
  empId: string;
  empDesignation: string;
}
```
Step 3: Use the Extended Interface
Now, you can use the extended interface in your components or services.
```typescript
// app.component.ts
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { Employee } from './employee.interface';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  employeeForm: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.employeeForm = this.fb.group({
      name: ['', Validators.required],
      mail: ['', [Validators.required, Validators.email]],
      age: ['', [Validators.required, Validators.min(18)]],
      empId: ['', Validators.required],
      empDesignation: ['', Validators.required]
    });
  }

  onSubmit() {
    if (this.employeeForm.valid) {
      const employee: Employee = this.employeeForm.value;
      console.log('Form Submitted!', employee);
    } else {
      console.log('Form is invalid');
    }
  }
}
```
1 vulnerability
Step 4: Create the Template
Create the template for the form in your component's HTML file.
<!-- app.component.html -->
<form [formGroup]="employeeForm" (ngSubmit)="onSubmit()">
  <div>
    <label for="name">Name</label>
    <input id="name" formControlName="name">
  </div>
  <div>
    <label for="mail">Mail</label>
    <input id="mail" formControlName="mail">
  </div>
  <div>
    <label for="age">Age</label>
    <input id="age" formControlName="age" type="number">
  </div>
  <div>
    <label for="empId">Employee ID</label>
    <input id="empId" formControlName="empId">
  </div>
  <div>
    <label for="empDesignation">Employee Designation</label>
    <input id="empDesignation" formControlName="empDesignation">
  </div>
  <button type="submit">Submit</button>
</form>

***To Pick few options from an interface then use PICK***
Step 1: Define the Base Interface
First, define the base interface for the common properties.
```typescript
// employee-base.interface.ts
export interface EmployeeBase {
  name: string;
  mail: string;
  age: number;
  place: string;
  state: string;
}
```
Step 2: Extend the Base Interface and Pick Specific Properties
Next, create another interface that extends the base interface and includes only the specific properties you want.
```typescript
// employee.interface.ts
import { EmployeeBase } from './employee-base.interface';

export interface Employee extends Pick<EmployeeBase, 'name' | 'age' | 'place' | 'state'> {
  empId: string;
  empDesignation: string;
}
```
# Question 5:
***How to handle memory Leaks***

Memory Leaks in Angular
Memory leaks in Angular applications can occur due to various reasons, such as improper handling of subscriptions, event listeners, and DOM manipulations. These leaks can lead to increased memory usage over time, causing the application to slow down or crash. Understanding how to detect and prevent memory leaks is crucial for maintaining the performance and stability of your Angular applications.

Common Causes of Memory Leaks in Angular
Unsubscribed Observables:

```typescript
import { Component, OnInit } from '@angular/core';
import { interval } from 'rxjs';

@Component({
  selector: 'app-leaky-component',
  template: '<p>Leaky Component</p>'
})
export class LeakyComponent implements OnInit {
  ngOnInit() {
    interval(1000).subscribe(val => console.log(val));
  }
}
```

Failing to unsubscribe from observables can cause memory leaks, as the subscriptions remain active even after the component is destroyed.
Example:
Event Listeners:

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-leaky-component',
  template: '<button id="leaky-button">Click me</button>'
})
export class LeakyComponent implements OnInit {
  ngOnInit() {
    const button = document.getElementById('leaky-button');
    button.addEventListener('click', () => console.log('Button clicked'));
  }
}
```

Adding event listeners without removing them can cause memory leaks, as the listeners retain references to the DOM elements.
Example:
Detached DOM Nodes:
```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-leaky-component',
  template: '<div id="leaky-div">Leaky Div</div>'
})
export class LeakyComponent implements OnInit {
  ngOnInit() {
    const div = document.getElementById('leaky-div');
    document.body.removeChild(div);
    // div is still referenced in JavaScript
  }
}
```

Detached DOM nodes are elements that have been removed from the DOM tree but are still referenced in JavaScript.
Example:
Timers and Intervals:

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-leaky-component',
  template: '<p>Leaky Component</p>'
})
export class LeakyComponent implements OnInit {
  ngOnInit() {
    setInterval(() => console.log('Interval running'), 1000);
  }
}
```

Timers and intervals can cause memory leaks if they are not cleared when no longer needed.
Example:
Detecting Memory Leaks in Angular
Browser Developer Tools:

  1. Modern browsers provide developer tools to help detect and analyze memory leaks.
  2. In Chrome, you can use the Memory tab to take heap snapshots and analyze memory usage.
     
Heap Snapshots:

   1. Heap snapshots capture the state of memory at a specific point in time.
   2. By comparing heap snapshots taken at different times, you can identify objects that are not being garbage collected.
   
Performance Monitoring:

  1. Monitoring the performance of your application can help identify memory leaks.
  2. Look for increasing memory usage over time, especially in long-running applications.

Preventing Memory Leaks in Angular
Unsubscribe from Observables:

  1. Always unsubscribe from observables to prevent memory leaks.
  2. Use the takeUntil operator with an ngOnDestroy hook to manage subscriptions.
Example:
```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { interval, Subject } from 'rxjs';
import { takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-safe-component',
  template: '<p>Safe Component</p>'
})
export class SafeComponent implements OnInit, OnDestroy {
  private destroy$ = new Subject<void>();

  ngOnInit() {
    interval(1000).pipe(takeUntil(this.destroy$)).subscribe(val => console.log(val));
  }

  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

Remove Event Listeners:

  1. Always remove event listeners when they are no longer needed.
Example:
```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-safe-component',
  template: '<button id="safe-button">Click me</button>'
})
export class SafeComponent implements OnInit, OnDestroy {
  private button: HTMLElement;

  ngOnInit() {
    this.button = document.getElementById('safe-button');
    this.button.addEventListener('click', this.handleClick);
  }

  ngOnDestroy() {
    this.button.removeEventListener('click', this.handleClick);
  }

  handleClick() {
    console.log('Button clicked');
  }
}
```
Manage DOM Nodes:

  1. Ensure that references to detached DOM nodes are cleared.
Example:

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-safe-component',
  template: '<div id="safe-div">Safe Div</div>'
})
export class SafeComponent implements OnInit, OnDestroy {
  private div: HTMLElement;

  ngOnInit() {
    this.div = document.getElementById('safe-div');
    document.body.removeChild(this.div);
  }

  ngOnDestroy() {
    this.div = null; // Clear the reference
  }
}
```
Clear Timers and Intervals:

  1. Always clear timers and intervals when they are no longer needed.
Example:
```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-safe-component',
  template: '<p>Safe Component</p>'
})
export class SafeComponent implements OnInit, OnDestroy {
  private intervalId: number;

  ngOnInit() {
    this.intervalId = setInterval(() => console.log('Interval running'), 1000);
  }

  ngOnDestroy() {
    clearInterval(this.intervalId);
  }
}
```
Summary
Memory leaks in Angular applications can occur due to various reasons, such as unsubscribed observables, event listeners, detached DOM nodes, and timers/intervals. Detecting memory leaks can be done using browser developer tools, heap snapshots, and performance monitoring. Preventing memory leaks involves using best practices such as unsubscribing from observables, removing event listeners, managing DOM nodes, and clearing timers and intervals. Understanding and addressing memory leaks is essential for maintaining the performance and stability of your Angular applications.



