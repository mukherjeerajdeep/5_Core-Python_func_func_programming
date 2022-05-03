# args and kwargs 

Remember everything is callables, i.e. `method/classes/instances` all. 


* `args` = example arg1, arg2 are normal positioanl argument
* `* args` = arbitrary number of positional arguments
* `kwargs` = kwargs1=value1, kwargs2=value2 are normal keyword argument
* `**kwargs` = arbitrary number of keyword arguments


## Example pf *args. 

Here we can see that the passed argument as formal argument of the method definition is captured 
as the tuple. 

```python
>>> def hypervolume(*args):
...     print(args)                                                             
...     print(type(args))                                                       
...                                                                             
>>> hypervolume(3, 4)                                                           
(3, 4)                                                                          
<class 'tuple'>                                                                 
>>> hypervolume(3, 4, 5)                                                        
(3, 4, 5)                                                                       
<class 'tuple'>
```
Another example of the `hyper-volume` program where we passed the several number of arguments at each scenario
and the number are multiplied as it enters into the method.

```python
>>> def hypervolume(*lengths):                                                  
...     i = iter(lengths)                                                       
...     v = next(i)                                                             
...     for length in i:                                                        
...         v *= length                                                         
...     return v                    
```
```text
>>> hypervolume(2, 4)                                                           
8                                                                               
>>> hypervolume(2, 4, 6)                                                        
48                                                                              
>>> hypervolume(2, 4, 6, 8)                                                     
384                                                                             
>>> hypervolume(1)                                                              
1
```
However, just calling the function without the argument exposed the whole function, which we don't 
want to do.
```text
>>> hypervolume()                                                               
Traceback (most recent call last):                                              
  File "<stdin>", line 1, in <module>                                           
  File "<stdin>", line 3, in hypervolume                                        
StopIteration   
```
Several ways to fix it :
1. Catch the StopIteration exception thrown by next and wrap to more understandable exception.
2. Raise a TypeError dur to insufficient number of argument is passed to a function. For that we can add a positional argument as a first argument and then *args as the second argument.

```python
>>>def hypervolume(length, *length)
...     v = length
...     for item in lengths:                                                    
...         v *= item                                                           
...     return v                                                                
...                                                                             
>>> hypervolume(3, 5, 7, 9)                                                     
945                                                                             
>>> hypervolume(3, 5, 7)                                                        
105                                                                             
>>> hypervolume(3, 5)                                                           
15                                                                              
>>> hypervolume(3)                                                              
3                                                                               
>>> hypervolume()                                                               
Traceback (most recent call last):                                              
  File "<stdin>", line 1, in <module>                                           
TypeError: hypervolume() missing 1 required positional argument: 'length'      
```

### **RULES:** 
1. Must come `after` the normal `positional` arguments.
2. `*args` only collect positional arguments, for keyword argument we have separate thing. 

## Example of **kwargs

Here we can see how the kwrgs are passed. 
```python
>>> def tag(name, **kwargs):                                                    
...     print(name)                                                             
...     print(kwargs)                                                           
...     print(type(kwargs))                                                     
...                                                                             
>>> tag('img', src="Monet.jpg", alt="Sunrise by Claude Monet", border=1)        
img                                                                             
{'src': 'Monet.jpg', 'alt': 'Sunrise by Claude Monet', 'border': 1}             
<class 'dict'>
```

Here with some meaningful names, check that the `.format()` also can take the keyword arguments in the same way. 

```python
>>> def tag(name, **attributes):                                                
...     result = '<' + name                                                     
...     for key, value in attributes.items():                                   
...         result += ' {k}="{v}"'.format(k=key, v=str(value))                  
...     result += '>'                                                           
...     return result                                                           
...                                                                             
>>> tag('img', src="Monet.jpg", alt="Sunrise by Claude Monet", border=1)        
'<img src="Monet.jpg" alt="Sunrise by Claude Monet" border="1">' 
```                                                             
It is not permitted. `**kwargs `should always come after the `*args`. 

```python
>>> def print_args(**kwargs, *args):                                            
  File "<stdin>", line 1                                                        
    def print_args(**kwargs, *args):                                            
                             ^                                                  
SyntaxError: invalid syntax     
```

if someone wants to pass arguments after the * args, then that should be keyword arguments. 
valid sequences :
```python
>>> def print_args(arg1, arg2, *args):                                          
...     print(arg1)                                                             
...     print(arg2)                                                             
...     print(args)                                                             
...                                                                             
>>> print_args(1, 2, 3, 4, 5)                                                   
1                                                                               
2                                                                               
(3, 4, 5)                                                                       
>>> def print_args(arg1, arg2, *args, kwarg1, kwarg2):                          
...     print(arg1)                                                             
...     print(arg2)                                                             
...     print(args)                                                             
...     print(kwarg1)                                                           
...     print(kwarg2)                                                           
...                                                                             
>>> print_args(1, 2, 3, 4, 5, kwarg1=6, kwarg2=7)                               
1                                                                               
2                                                                               
(3, 4, 5)                                                                       
6                                                                               
7                                                  
```

Invalid sequence :
```python
>>> def print_args(arg1, arg2, *args, kwarg1, kwarg2):                          
...     print(arg1)                                                             
...     print(arg2)                                                             
...     print(args)                                                             
...     print(kwarg1)                                                           
...     print(kwarg2)                                                           
...   
>>> print_args(1, 2, 3, 4, 5, 6, 7)                                             
Traceback (most recent call last):                                              
  File "<stdin>", line 1, in <module>                                           
TypeError: print_args() missing 2 required keyword-only arguments: 'kwarg1' and 
'kwarg2'      
```

Python will allow to pass keyword argument without the mandatory *args postional argument, if we only leave the position as `*`

```python
>>> def name_tag(first_name, last_name, *, title=''):                           
...     print(title, first_name, last_name)                                     
...                                                                             
>>> name_tag('Judy', 'Spudmeyer', title='Galactic Commander')                   
Galactic Commander Judy Spudmeyer                                 
```

However passing an argument for the arbitrary number of positional arguments(*args) will raise an error.
```python
>>> def name_tag(first_name, last_name, *, title=''):                           
...     print(title, first_name, last_name)  

>>> name_tag('James', 'Tiberius', 'Kirk', title='Capt.')                        
Traceback (most recent call last):                                              
  File "<stdin>", line 1, in <module>                                           
TypeError: name_tag() takes 2 positional arguments but 3 positional arguments (a
nd 1 keyword-only argument) were given        
```
Valid passing of kwargs :
```python
>>> def print_args(arg1, arg2, *args, kwarg1, kwarg2, **kwargs):                
...     print(arg1)                                                             
...     print(arg2)                                                             
...     print(args)                                                             
...     print(kwarg1)                                                           
...     print(kwarg2)                                                           
...     print(kwargs)                                                           
...                                                                             
>>> print_args(1, 2, 3, 4, 5, kwarg1=6, kwarg2=7, kwarg3=8, kwarg4=9)           
1                                                                               
2                                                                               
(3, 4, 5)                                                                       
6                                                                               
7                                                                               
{'kwarg3': 8, 'kwarg4': 9}
```

Invalid passing of keyword argument after the **kwargs, this is not permitted. The **kwargs should be at the last. 
```python
>>> def print_args(arg1, arg2, *args, kwarg1, kwarg2, **kwargs, kwargs99):      
  File "<stdin>", line 1                                                        
    def print_args(arg1, arg2, *args, kwarg1, kwarg2, **kwargs, kwargs99):      
                                                                ^               
SyntaxError: invalid syntax     
```

## Positional only arguments 

To specify the postional only argument we need to add a forward-slash `/` after the psotional arguments. If we try to pass a keyword argument it will fail. All the argument before `/`are positional only. 
```python
>>>def len_of_number(x, /)
...     return len(str(x))
...                                                                             
>>> number_length(2112)                                                         
4                             

>>> number_length(x=31557600) <<<--- Fails                                                      
Traceback (most recent call last):                                              
  File "<stdin>", line 1, in <module>                                           
TypeError: number_length() got some positional-only arguments passed as keyword 
arguments: 'x'     
```

## Extended call syntax 

### In this case we can pass a tuple as actual argument with * as we can see below and ask the function to unpack and distribute the content of it. 

The *in the actual argument has no relation with the *args in the formal arguments. 

```python
>>> def print_args(arg1, arg2, *args):                                          
...     print(arg1)                                                             
...     print(arg2)                                                             
...     print(args)                                                             
...                                                                             
>>> t = (11, 12, 13, 14)                                                        
>>> print_args(*t)   <<< --- The * indicate python to unpack it.                                                           
11         << arg1                                                                      
12         << arg2                                                                     
(13, 14)                                                                        
>>>        
```
### Same happens when we pass a dictionary. 
The **k has no relation with **kwargs, the **k only says Python to unpack it and distribute. 

```python
>>> def color(red, green, blue, **kwargs):                                      
...     print("r =", red)                                                       
...     print("g =", green)                                                     
...     print("b =", blue)                                                      
...     print(kwargs)                                                           
...                                                                             
>>> k = {'red':21, 'green':68, 'blue':120, 'alpha':52 }                         
>>> color(**k)                                                                  
r = 21                                                                          
g = 68                                                                          
b = 120                                                                         
{'alpha': 52}                                                                   
>>> k = dict(red=21, green=68, blue=120, alpha=52)           
```
**Note:** The `dict()` uses the same `**kwargs` in its initializer. 

### Argument forwarding

The *args and **kwargs can be used together to be used in another function. For example `trace()` takes both `*args` and `**kwargs` to be used inside it with the `f`, which can be a `method-reference`. 

This nice little function calls the `int()` method/function with `function-reference` named `int` and passed a p`ositional arbitrary argument ff` and `keyword argument base=16` and get `255` in return.  

```python
>>> def trace(f, *args, **kwargs):                                              
...     print("args =", args)                                                   
...     print("kwargs =", kwargs)                                               
...     result = f(*args, **kwargs)   <<<--- Here f() used *args and **kwargs                                     
...     print("result =", result)                                               
...     return result                                                           
...    

>>> trace(int, "ff", base=16)                                                   
args = ('ff',)                                                                  
kwargs = {'base': 16}                                                           
result = 255                                                                    
255     
```