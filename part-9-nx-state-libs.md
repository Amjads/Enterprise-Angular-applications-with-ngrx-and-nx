# Part 9 - nx state libs

```
npm i github:ngrx/schematics-builds --save-dev
```

```
https://github.com/ngrx/platform/issues/674
```

```
ng generate action Auth --collection @ngrx/schematics
```

```
ng g lib auth-state
```

```
ng g ngrx auth-state --module=libs/auth-state/src/auth-state.module.ts
```

```ts
import { Action } from '@ngrx/store';
import { User } from '@demo-app/data-models';

export enum AuthStateActionTypes {
  Login = '[AuthState] Login',
  LoginSuccess = '[AuthState] Login Success',
  LoginFail = '[AuthState] Login Fail'
}

export class LoginAction implements Action {
  readonly type = AuthStateActionTypes.Login;
  constructor(public payload: User) {}
}

export class LoginSuccessAction implements Action {
  readonly type = AuthStateActionTypes.LoginSuccess;
  constructor(public payload) {}
}

export class LoginFailAction implements Action {
  readonly type = AuthStateActionTypes.LoginFail;
  constructor(public payload) {}
}

export type AuthStateActions = 
  LoginAction
  | LoginFailAction
  | LoginSuccessAction;
```

```ts
import { Injectable } from '@angular/core';
import { Effect, Actions } from '@ngrx/effects';
import { of } from 'rxjs/observable/of';
import 'rxjs/add/operator/switchMap';
import { AuthState } from './auth.interfaces';
import * as authActions from './auth.actions';
import { map, catchError, tap, mergeMap } from 'rxjs/operators';
import { AuthService } from './../services/auth.service';

@Injectable()
export class AuthEffects {
  @Effect()
  login = this.actions$
    .ofType(authActions.AuthActionTypes.Login)
    .pipe(
      mergeMap((action: authActions.LoginAction) =>
        this.authService
          .login(action.payload.username, action.payload.password)
          .pipe(
            tap(console.log),
            map(user => new authActions.LoginSuccessAction(user)),
            catchError(error => of(new authActions.LoginFailAction(error)))
          )
      )
    );

  constructor(private actions$: Actions, private authService: AuthService) {}
}
```

```ts
import { Injectable } from '@angular/core';
import { Effect, Actions } from '@ngrx/effects';
import { of } from 'rxjs/observable/of';
import 'rxjs/add/operator/switchMap';
import { AuthState } from './auth.interfaces';
import * as authActions from './auth.actions';
import { map, catchError, tap, mergeMap } from 'rxjs/operators';
import { AuthService } from './../services/auth.service';
import { DataPersistence } from '@nrwl/nx';

@Injectable()
export class AuthEffects {
  @Effect()
  login$ = this.dataPersistence.fetch(authActions.AuthActionTypes.Login, {
    run: (action: authActions.LoginAction, state: AuthState) => {
      return this.authService
        .login(action.payload.username, action.payload.password)
        .pipe(map(user => new authActions.LoginSuccessAction(user)));
    },

    onError: (action: authActions.LoginAction, error) => {
      return of(new authActions.LoginFailAction(error));
    }
  });

  constructor(
    private actions: Actions,
    private dataPersistence: DataPersistence<AuthState>,
    private authService: AuthService
  ) {}
}
```



