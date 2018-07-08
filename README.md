# Extended calling conventions in CPython

## Summary

3rd party tools, like Cython, and hand written C extensions are forced to
choose either performance or flexibility. They cannot have both.
Either callables are builtin methods with a narrow range of argument types and no ability to
use reflection, or they implement the `tp_call` function pointer and suffer impaired performance.

We aim to add a new calling convention to CPython, so that 3rd party tools can generate performant *and* flexible code.


## Requirements

### 1. The new API should be fully backwards compatible and should not break the ABI.

The exact meaning of "ABI" is a bit vague. No one adheres strictly to [PEP 384](https://www.python.org/dev/peps/pep-0384/). We should avoid any gratuitous breakage and minimise other breakages.

### 2. The new API should be exposed to 3rd party code *and* used internally. 

The intent of this is that 3rd party extensions are not second class citizens in term of call performance. By treating all callees equally, Cython generated code and hand written extensions can be implemented efficiently and be called as fast builtin functions and methods.

### 3. The new API should not prevent 3rd party extensions having full introspection capabilities, supporting keyword arguments or other features supported by Python functions.

The motivating goal here is that Cython functions need to access class and module variables, so need reliable access to their `self` parameter.

### 4. The implementation should not be excessivley complex.

This is inherently subjective, but changes should be coherent and a reason size for review. The net change to the size of the code base should be small, ideally negative (not including additonal tests).

### 5. It should not slow down CPython

We will use the  for the standard benchmark suite as a metric for this, but we should not expect a slow down for any program.

### 6. It should be understandable.

It shouldn't use any esoteric ideas or language features. Any non obvious parts should be clearly documented.



## Notes

Stefan Behnel pointed out that  "We're essentially designing a Fast Duck Calling convention here".
Antoine Pitrou suggested we call it the "Quick Quack protocol" :)


### 1.  The new API should be fully backwards compatible and should not break the ABI.

Nick Coghlan pointed out that no one uses the formal ABI and the informal ABI that "everyone uses" gets broken all the time. 


### 4. The implementation should not be excessivley complex.

Mark Shannon suggested a fixed limit to the size of the patch. Nick Coghlan and Brett Cannon felt that this was too restrictive.


### 5. It should not slow down CPython

Mark Shannon believes it is possible and desirable to speed up CPython with this change. INADA Naoki believes it impossible to speed up CPython with a new calling convention.
Jeroen Demeyer requested we use the term "not slower".
Antoine Pitrou noted that the primary goal was to bring 3rd party code up to speed, not to speed up CPython.
Although, we choose "not slower" rather than "faster" as a requirement, no one will object to it being faster :)




