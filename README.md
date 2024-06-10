# fortran-OO
How to convert module to "class"

## This is test.
    module bigmod
       a, b, c, . . .
       x, y, z, . . .
    contains
        func1() . . . “reads” some of a, b, c, . . .; “modifies” some of x, y, z, . ..
        func2() . . . Ditto.

Step 1
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


