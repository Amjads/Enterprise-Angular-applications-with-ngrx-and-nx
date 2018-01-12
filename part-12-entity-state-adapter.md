# Part 12 - Entity state adapter

```
ng g guard guards/auth-admin/auth-admin -a=auth
```

```ts
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot, Router } from '@angular/router';
import { Observable } from 'rxjs/Observable';
import { AuthService } from './../../services/auth.service';
import { AuthState, getUser } from '@demo-app/auth';
import { Store } from '@ngrx/store';
import { map } from 'rxjs/operators';
import { User } from '@demo-app/data-models';

@Injectable()
export class AuthAdminGuard implements CanActivate {

  constructor(
    private router: Router,
    private store: Store<AuthState>) {}

  canActivate(
    next: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean> {
      return this.store.select(getUser).pipe(
        map((user: User) => {          
          if(user && user.role === 'auth') {
            return true;
          } else {
            this.router.navigate([`/auth/login`]);
            return false;
          }
        })
      )
  }
}
```

Make a users lib in an admin directory

```
ng g lib users --directory=admin-portal
```

```
ng g lib users --routing --lazy --parent-module=apps/admin-portal/src/app/app.module.ts
```

```
ng g c containers/user-list -a=admin-portal/users
```



```

```



