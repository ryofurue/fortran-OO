# fortran-OO
How to convert module to "class"

## Stage 0
```fortran
module bigmod
  # module vars that remain constant after initial assignment
  real, save:: a, b, c ! or PARAMETER
  # module vars that are changed by the subroutines below
  real, save:: x, y, z
contains
  ! Read-only-use a, b, c, . . .
  ! Update x, y, z, . . .
  subroutine func1()
    . . .
  subroutine func2()
    . . .
```
## Step 1
```fortran
module bigmod
       a, b, c, . . .
       x, y, z, . . .
       type global
          x, y, z, . . .
       end type global
    contains
        set_global(self::global)
           x = self.x
           y = self.y
           . . .
        get_global(self::global)
           self.x = x
           self.y = y
           . . .
        func1(self) . . . reads some of a, b, c, . . . ; doesn’t refer to the module x, y, z, . . .
           uses selfl.x, self.y, . . .
        func2(self) . . . Ditto
        func3() . . . unmodified from Step 0.

    main
       type(global):: obj
       call get_global(obj)
       call func1(obj)
       call func2(obj) ! just inherit obj
       call set_global(obj) ! need to copy back for the old-style function
       call func3() ! old-style function.
```
Step 2 (final, OO style)
    module bigmod
       a, b, c, . . .
       ! delete x, y, z, . . . as module-global
       type global
          x, y, z, . . .
    contains
        new_state(self) . . . creates a “global” object
        func1(self)
        func2(self)
        func3(self)


   main
      type(global):: obj  ! mutable state of the module
      call new_global(obj) ! creates new state
      call func1(obj)
      call func2(obj)
      call func3(obj)
      . . . 


