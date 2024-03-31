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

## Creating decorators as functions 

Here is the example of creating the decorators as not to affect the original program and only use that in a non-intrusive way. This is our decorator.  It takes a callable object as `f` and returns a callable object `wrap` 

```python
def escape_unicode(f):                                                      
    def wrap(*args, **kwargs):                                              
        x = f(*args, **kwargs)                                              
        return ascii(x)                                                     
    return wrap                                                             
```

Now the function northern_city() is behaving not the way we expected. It just returned the unicode characters but not with ascii. 

```python
def northern_city():                                                        
    return 'Tromsø'                                                         
                                                                            
>>> print(northern_city())                                                      
Tromsø 
```

Now we applied our decorator to the northern_city() function and get the ascii character. Remember that decorator doesn't create anything but bind the same calling function at the time of returning it.  

```python
@escape_unicode                                                             
def northern_city():                                                        
    return 'Tromsø'                                                         
                                                                           
>>> print(northern_city())                                                      
'Troms\xf8'    
```

## Other callables as decorators i.e. Class

### Class objects as decorators.

Remember that class are also callables, hence we can also exploit them as decorator. Let's see the example and understand. 

**Class CallCount:**

The instance of a class can be made as callable object only if we implement the __call__ method for that class. So to make instance as a callable the __call__ is defined which will return a callable.  

```python
class CallCount:                                                            
    def __init__(self, f):                                                  
        self.f = f                                                          
        self.count = 0                                                      
    def __call__(self, *args, **kwargs):                                    
        self.count += 1                                                     
        return self.f(*args, **kwargs)        
```
Applying decorator, which is a class here.
The function hello() will be passed in the constructor as `f` and then in the initializer __init__ as `f` as wll to create the instance object. Then that instance could only return the newly wrapped function if callable is implemented by `return self.f(*args, **kwargs)`. Each time 

```python
@CallCount                                                                  
def hello(name):                                                            
    print('Hello, {}!'.format(name))                                       
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
class Trace:
     def __init__(self):
         self.enabled = True  
     def __call__(self, f):                                                  
         def wrap(*args, **kwargs):                                          
             if self.enabled:                                                
                 print('Calling {}'.format(f))                               
             return f(*args, **kwargs)                                       
         return wrap   
```

Now we create an instance as class object is itself can't call the __call__() method. We applied the decorator after we created the instance from that class.  

```python
tracer = Trace()   <<<< Create a object-which is a instance of the class

@tracer                                                                     
def rotate_list(l):                                                         
    return l[1:] + [l[0]]                                                   
                                   
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

## Multiple decorators 

Here is the example for the decorators we created earlier. 

```python
def escape_unicode(f):
     def wrap(*args, **kwargs):
         x = f(*args, **kwargs)                                              
         return x.encode('unicode-escape').decode('ascii')                   
     return wrap                                                             
                                                                             
class Trace:                                                                
    def __init__(self):                                                     
        self.enabled = True                                                 
    def __call__(self, f):                                                  
        def wrap(*args, **kwargs):                                          
            if self.enabled:                                                
                print('Calling {}'.format(f))                               
            return f(*args, **kwargs)                                       
        return wrap   
```
Now we can apply them together on the top of the `norwegian_island_maker()` function and check how it works. In general the strategy is this function would be an input for the first decorator that is `escape_unicode` and then the output from the `escape_unicode` would be another **function/callable** and that will be the input of `@tracer` decorator. Finally, the `tracer` decorator will bind the final callable to `norwegian_island_maker` again, 

So the `norwegian_island_maker()` is wrapped by two other functions here. First the `escape_unicode` and then the `tracer`. 

```python
>>> tracer = Trace()

@tracer
@escape_unicode
def norwegian_island_maker(name):   
    return name + 'øy'    
```

Here is the output for the called function when used with REPL. We can see that the tracer works as it is expected and also the `escape_unicode` works as well. Even if we stopped the `tracer` by `tracer.enabled = False` then also the `escape_unicode` translation worked. 

```text
>>> norwegian_island_maker('Llama')                                             
Calling <function escape_unicode.<locals>.wrap at 0x103b22ee0>                  
'Llama\\xf8y'                                                                   
>>> norwegian_island_maker('Python')                                            
Calling <function escape_unicode.<locals>.wrap at 0x103b22ee0>                  
'Python\\xf8y'                                                                  
>>> norwegian_island_maker('Troll')                                             
Calling <function escape_unicode.<locals>.wrap at 0x103b22ee0>                  
'Troll\\xf8y'                                                                   
>>> tracer.enabled = False                                                      
>>> norwegian_island_maker('Llama')                                             
'Llama\\xf8y'                                                                   
>>> norwegian_island_maker('Python')                                            
'Python\\xf8y'                                                                  
>>> norwegian_island_maker('Troll')                                             
'Troll\\xf8y'       
```

## How not to mess with the doc-string for the original function when using the decorator

This is a function, and it has its own dunder name and doc, which are used during the other tools generates the documents from it. 

```python
def hello():                                                                
    "Print a well-known message."                                           
    print('Hello, world!')            
```

Here is the variables for that callable or function. 

```text
>>> hello.__name__                                                              
'hello' def noop_wrapper                                                        
>>> hello.__doc__                                                               
'Print a well-known message.'  
                                                 
>>> help(hello)                 <<< We can see the nice help here.                                                   
Help on function hello in module __main__:  

hello()                                                                         
    Print a well-known message.                                                 
(END)  
```

However, using decorator will ruin this. See below we used the decorator `noop` here.

```python
def noop(f):                                                                
    def noop_wrapper():                                                     
        return f()                                                          
    return noop_wrapper                                                     
                                                                            
@noop                                                                       
def hello():                                                                
    "Print a well-known message."                                           
    print('hello world!')                                                   
```
Now printing the `hello()` shows the implementation of the `noop` not the `hello`.

```text
>>> help(hello)                                                                 
Help on function noop_wrapper in module __main__:                               
                                                                                
noop_wrapper()                                                                  
(END)            

>>> hello.__name__                                                              
'noop_wrapper'                                                                  
>>> hello.__doc__                     
```
This can be circumvented by this way by assgning the dunder name and doc to the decorator function.

```python
def noop(f):                                                                
    def noop_wrapper():                                                     
        return f()                                                          
    noop_wrapper.__name__ = f.__name__                                      
    noop_wrapper.__doc__ = f.__doc__                                        
    return noop_wrapper                                                     
                                                                            
@noop                                                                       
def hello():                                                                
    "Print a well-known message."                                           
    print('hello world!')                                                   
                                                                            
>>> help(hello)                                                                 
Help on function hello in module __main__:                                      
                                                                                
hello()                                                                         
    Print a well-known message.                                                 
(END)                                
```
This is not the right thing, we can luckily have another decorator which solves the problem. That is **functools**. See the implementation below. 

```python
import functools                                                            
def noop(f):                                                                
    @functools.wraps(f)                                                     
    def noop_wrapper():                                                     
        return f()                                                          
    return noop_wrapper                                                     
                                                                            
@noop                                                                       
def hello():                                                                
    "Print a well-known message."                                           
    print('hello world!')                                                   
                                                                            
>>> help(hello)                                                                 
Help on function hello in module __main__:                                      
                                                                                
hello()                                                                         
    Print a well-known message.                                                 
(END)                                                                           
                                                                                
>>> hello.__name__                                                              
'hello'                                                                         
>>> hello.__doc__                                                               
'Print a well-known message.'     
```

## using multiple decorator 

Here is an example of the multiple decorator as well. Here the hierarchy went two level deeper in nature. The decorator is the check_non_negative which takes the index position however the input for the validator is the function itself.  

```python
def check_non_negative(index):                             
    def validator(f):
        def wrap(*args):                                                   
            if args[index] < 0:                                             
                raise ValueError('Argument {} must be non-negative.'.format(index))      
            return f(*args)                                                 
        return wrap                                                         
    return validator                                                        
                                                                            
@check_non_negative(1)                                                      
def create_list(value, size):                                               
    return [value] * size                                                   
                                                                            
>>> create_list('a', 3)                                                         
['a', 'a', 'a']       

>>> create_list(123, -6)                                                        
Traceback (most recent call last):                                              
  File "<stdin>", line 1, in <module>                                           
  File "<stdin>", line 5, in wrap                                               
ValueError: Argument 1 must be non-negative.                                    
>>>    
```