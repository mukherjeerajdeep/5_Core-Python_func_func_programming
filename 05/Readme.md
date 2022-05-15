# Function decorator 

##### DECORATOR:
* A Function that accepts a function and returns another function.
* Modify or enhance an existing function in a non-intrusive and maintainable way
* Implemented as a callable that accepts a callable and returns a callable. Similar as fist bullet

Syntax looks like this.
```python
@my_decorator
def my_function():
    pass
```

## Creating decorators 

Here is the example of creating the decorators as not to affect the original program and only use that in a non-intrusive way. This is our decorator.  It takes a callable object as `f` and returns a callable object `wrap` 

```python
>>> def escape_unicode(f):                                                      
...     def wrap(*args, **kwargs):                                              
...         x = f(*args, **kwargs)                                              
...         return ascii(x)                                                     
...     return wrap                                                             
...
```

Now the function northern_city() is behaving not the way we expected. It just returned the unicode characters but not with ascii. 

```python
>>> def northern_city():                                                        
...     return 'Tromsø'                                                         
...                                                                             
>>> print(northern_city())                                                      
Tromsø 
```

Now we applied our decorator to the northern_city() function and get the ascii character. Remember that decorator doesn't create anything but bind the same calling function at the time of returning it.  

```python
>>> @escape_unicode                                                             
... def northern_city():                                                        
...     return 'Tromsø'                                                         
...                                                                             
>>> print(northern_city())                                                      
'Troms\xf8'    
```

## Other callables as decorators 

### Class objects as decorators.

Remember that class are also callables, hence we can also exploit them as decorator. Let's see the example and understand. 

**Class CallCount:**
It has a __call__() method to return the callable function that is passed in the constructor and then the __init__() when the CallCount is used as a decorator.

```python
>>> class CallCount:                                                            
...     def __init__(self, f):                                                  
...         self.f = f                                                          
...         self.count = 0                                                      
...     def __call__(self, *args, **kwargs):                                    
...         self.count += 1                                                     
...         return self.f(*args, **kwargs)        
```
Applying decorator, which is a class here.

```python
>>> @CallCount                                                                  
... def hello(name):                                                            
...     print('Hello, {}!'.format(name))                                       
```

Calls to successive method quite a few times and then check the count. 
```text
>>> hello('Fred')                                                               
Hello, Fred!                                                                    
>>> hello('Wilma')                                                              
Hello, Wilma!                                                                   
>>> hello('Betty')                                                              
Hello, Betty!                                                                   
>>> hello('Barney')                                                             
Hello, Barney!                                                                  
>>> hello.count   <<<-- See that we can invoke count on hello and get the number of execution
4       
```

So how does it work here ? 
1. `hello()` is passed as a instance object to class CallCount as `f`. The with the __call__() method implementation the reference hello() was returned. as there are no positional or keyword argument passed the return value is the function itself.
2. Each time the function hello() completes the round trip the count was incremented by +1. 

###  Instances as decorators. 

In this example we used the definition as this :

```python
>>>class Trace:
...     def __init__(self):
...         self.enabled = True  
...     def __call__(self, f):                                                  
...         def wrap(*args, **kwargs):                                          
...             if self.enabled:                                                
...                 print('Calling {}'.format(f))                               
...             return f(*args, **kwargs)                                       
...         return wrap   
```

Now we create an instance as class object is itself can't call the __call__() method. We applied the decorator after we created the instance from that class.  
```python
>>> tracer = Trace()                                                            
>>> @tracer                                                                     
... def rotate_list(l):                                                         
...     return l[1:] + [l[0]]                                                   
...                                    
```

By applying this decorator we can control the printing of the trace externally. 

```text
>>> l = [1, 2, 3]                                                               
>>> l = rotate_list(l)                                                          
Calling <function rotate_list at 0x10f7f5ca0>                                   
>>> l                                                                           
[2, 3, 1]                                                                       
>>> l = rotate_list(l)                                                          
Calling <function rotate_list at 0x10f7f5ca0>                                   
>>> l                                                                           
[3, 1, 2]                                                                       
>>> l = rotate_list(l)                                                          
Calling <function rotate_list at 0x10f7f5ca0>                                   
>>> l                                                                           
[1, 2, 3]                                                                       
>>> tracer.enabled = False   <<< -- Switch it off here.
>>> l = rotate_list(l)                                                          
>>> l                                                                           
[2, 3, 1]                                                                       
>>> l = rotate_list(l)                                                          
>>> l                                                                           
[3, 1, 2]                                                                       
>>> l = rotate_list(l)                                                          
>>> l                                                                           
[1, 2, 3]   
```






