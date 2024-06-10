# fortran-OO
How to convert module to "class"

## Stage 0
```f90
module bigmod
  ! module vars that remain constant after initial assignment
  real, save:: a, b, c ! or PARAMETER
  ! module vars that are changed by the subroutines below
  real, save:: x, y, z
contains
  ! Read-only-use a, b, c, . . .
  ! Update x, y, z, . . .
  subroutine func1()
    . . .
  subroutine func2()
    . . .
```
## Stage 1
```f90
module bigmod
  real, save:: a, b, c
  real, save:: x, y, z
  type instancevars
    x, y, z
    . . .
contains
  ! determine a, b, c, . . . x, y, z, . . .
  subroutine init_bigmod()
    . . .
  subroutine set_instancevars(self::instancevars)
    x = self.x
    y = self.y
    . . .
  subroutine get_instancevars(self::instancevars)
    self.x = x
    self.y = y
  . . .
  ! Read-only-use a, b, c, . . .
  ! Don't refer to the module-global x, y, z, . . .
  subroutine func1(self)
    !. . . use self.x, self.y, . . .
  subroutine func2(self)
    . . .
  subroutine func3() ! Not yet updated. Modifies x, y, z, . . .
  . . .

program main
  use bigmod
  type(instancevars):: obj
  call init_bigmod()
  call get_instancvars(obj)
  call func1(obj)
  call func2(obj) ! just inherit obj
  call set_instancevars(obj) ! need to copy back for the next func
  call func3() ! old-style function.
  . . .
```
## Stage 2 (final, OO style)
```f90
module bigmod  real, save:: a, b, c
  ! abolish module-global x, y, z
  type instancevars
    x, y, z
    . . .
contains
  ! determine a, b, c, . . .
  subroutine init_bigmod()
    . . .
  subroutine new_instancevars(self::instancevars)
    self.x = . . .
    self.y = . . .
    . . .
  subroutine func1(self)
    !. . . use self.x, self.y, . . .
  subroutine func2(self)
    . . .
  subroutine func3(self)
    . . .
  . . .
program main
  use bigmod
  type(instancevars):: obj
  call init_bigmod()
  call new_instancvars(obj)
  call func1(obj)
  call func2(obj) ! inherits obj
  call func3(obj) ! inherits obj
  . . .
```
