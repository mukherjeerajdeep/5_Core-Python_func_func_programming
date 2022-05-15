# Function inside functions and closures

The local function definition is same as defining another function inside a function.
Here the `last_letter` as key of the `sorted(iterable, key<func>)` is used locally within the scope and then executed. 

```python
def sort_by_last_letter(strings):                                           
    def last_letter(s):                                                     
       return s[-1]                                                         
    return sorted(strings, key=last_letter)                                 
                                                                            
>>> sort_by_last_letter(['hello', 'from', 'a', 'local', 'function'])            
['a', 'local', 'from', 'function', 'hello']     
```

See that each time the inner function last_letter is called, it is called with new function-reference. Below we see that each execution has new reference to it. 

```python
def sort_by_last_letter(strings):                                           
    def last_letter(s):                                                     
       return s[-1]                                                         
    return sorted(strings, key=last_letter)                                
                                                                            
>>> sort_by_last_letter(['ghi', 'def', 'abc'])                                  
<function sort_by_last_letter.<locals>.last_letter at 0x102f3cd30>  <<<-- New Ref              
['abc', 'def', 'ghi']    

>>> sort_by_last_letter(['ghi', 'def', 'abc'])
<function sort_by_last_letter.<locals>.last_letter at 0x1013250d0>  <<<-- New Ref
['abc', 'def', ghi'] 

>>> sort_by_last_letter(['ghi', 'def', 'abc'])                                  
<function sort_by_last_letter.<locals>.last_letter at 0x1013250c8>  <<<-- New Ref
['abc', 'def', ghi']       
```

## Description of LEGB

This function explains how the binding works with **[**LEGB**](https://realpython.com/python-scope-legb-rule/#using-the-legb-rule-for-python-scope)** rule. 
* L = Local
* E = Enclosing
* G = Global
* B = Built-In

```python
g = 'global'                                                                
def outer(p='param'):                                                       
   l = 'local'                                                              
   def inner():                                                             
       print(g, p, l)                                                       
   inner()                                                                  
                                                                            
>>> outer()         <<<-- call to outer() can see "global" , "param" and "local"                                                             
global param local 
```

**Remember** : The local functions _are not_ `members` of their enclosing functions.  
However outer() function/callable can't bind the inner in such a way. Doing this will throw and error. 

```python
>>> outer.inner()                                                               
Traceback (most recent call last):                                              
  File "<stdin>", line 1, in <module>                                           
AttributeError: 'function' object has no attribute 'inner'   
```

## How a local functions can live even if the outer scope is already destroyed

The function below returns a reference of the local function and this is possible because of a special technique in python called **`closure`**. 

Closure :
* Keeps the records of the objects from the enclosing scopes
* Keeps recorded objects alive for use after the enclosing scope is gone and not let it garbage collected.
* Can be implemented with the `__closure__` attribute

```python
def enclosing():                                                            
    def local_func():                                                       
        print('local func')                                                 
    return local_func                                                       
                                                                            
>>> lf = enclosing()                                                            
>>> lf()                                                                        
local func                                                                      
>>>  
```
As mentioned above that it keeps the local function as objects and not garbage collected. The object can be seen with special dunder method `__closure__()`. 

Check that the `lf.__closure__` returns the object for the inner functions we defined.  

```python
def enclosing():                                                            
    x = 'closed over'                                                       
    def local_func():                                                       
            print(x)                                                        
    return local_func                                                       
                                                                            
>>> lf = enclosing()                                                            
>>> lf()                                                                        
closed over

>>> lf.__closure__                                                              
(<cell at 0x10a661d60: str object at 0x10a76a5b0>,)                             
>>>    
```
### Closures can be used with function factories 

Functions Factories :
* Functions that return another function 
* The factory function the outer one takes one argument and use that argument in the inner function and then return the reference of the inner function. 

Each time the enclosing is called correctly uses the outermost functions argument and the use it accordingly. 

```python
def raise_to(exp):                                                          
    def raise_to_exp(x):                                                    
        return pow(x, exp)                                                  
    return raise_to_exp                                                     
                                                                            
>>> square = raise_to(2)     
```

Here we can see that the square is actually a closure. So the argument `exp` is bound with `x` inside the function and then returned. 

```python
>>> square.__closure__   <<<-- object is created from the pow(x, exp) and kept safely by closure  
(<cell at 0x10543de50: int object at 0x1051baab0>,)          
```

Call to the functions. It can be square or the cube.

```python
>>> square(5)                                                                   
25                                                                              
>>> square(9)                                                                   
81                                                                              
>>> square(1234)                                                                
1522756          

>>> cube = raise_to(3)                                                          
>>> cube(3)                                                                     
27                                                                              
>>> cube(10)                                                                    
1000                                                                            
>>> cube(23)                                                                    
12167    
```

## Non-local usage

When we want to modify the **_global object/data_** inside any local scope such as function we use `global` keyword. . However, if we want to modify the **_outer function object/data_** from the inner function we use the `non-local` keyword. 

```python
message = 'global'

def enclosing():
    message = 'enclosing'
    
    def local():
        global message
        message = 'local'
    
    print('enclosing message:', message)
    local()
    print('enclosing message:', message)

print('global message:', message)
enclosing()
print('global message:', message)
```

In the above case the call of the `local()` will change the global message however there is not way to call the message in the `enclosing()` scope and not the global scope. 

This problem is solved by the `non-local` keyword. :grinning:

```python
import time                                                                 
                                                                            
def make_timer():                                                           
    last_called = None # Never                                              
    def elapsed():            
        nonlocal last_called                                                
         now = time.time()                                                   
        if last_called is None:                                             
            last_called = now                                               
            return None                                                     
        result = now - last_called                                          
        last_called = now                                                   
        return result                                                       
   return elapsed   
```

Here is the execution of the `make_timer()` 

```text
>>> t = make_timer()                                                            
>>> t()                                                                         
>>> t()                                                                         
0.10840821266174316                                                             
>>> t()                                                                         
0.11033487319946289                                                             
>>> t()                                                                         
0.10557293891906738                                                             
>>> t1 = make_timer()                                                           
>>> t2 = make_timer()                                                           
>>> t1()                                                                        
>>> t1()                                                                        
0.10762977600097656                                                             
>>> t2()                                                                        
>>> t2()                                                                        
0.10677790641784668                                                             
>>> t2()                                                                        
0.10698699951171875                                                             
>>> t2()                                                                        
0.10787391662597656                                                             
>>> t2()                                                                        
0.10452818870544434                                                             
>>> t1()                                                                        
0.6384401321411133                                                              
>>> t1()                                                                        
0.10730099678039551    
```
