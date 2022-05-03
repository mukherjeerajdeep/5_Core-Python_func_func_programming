# Lambdas and Functions

Arguments which are mentioned in the function call is known as the `actual argument.` 
For example:
```text
func1(12, 23);
```

Arguments which are mentioned in the `definition of the function is called formal 
arguments`. Formal arguments are very similar to local variables inside the function. 
Just like local variables, formal arguments are destroyed when the function ends.
```text
int factorial(int n){
    // write logic here
}
```

* **POSITIONAL Argument** :Are matched with formal arguments by position, in order.
* **KEYWORD Argument** : Are matched with formal arguments by name.

## `Functions` are `Objects` and can be passed around list like other objects 

In this resolve function we can see that by calling resolve it returns a function object.
```python
>>> def resolve(host):                                                          
...     return socket.gethostbyname(host)                                       
...   
                                                                          
>>> resolve                                                                     
<function resolve at 0x10f5d1ca0>                                               
>>> resolve('sixty-north.com')                                                  
'93.93.131.30'                                                                  
```

_**Function objects are callable objects.**_

## `__call__()` allows instances of classes to be a callable object

__call()__ is invoked on objects when they are called like functions. 
When we can make a function as callable :
```python
    def __call__(self, host):
        if host not in self._cache:
            self._cache[host] = socket.gethostbyname(host)
        return self._cache[host]
```
```python
from resolver import Resolver
resolve = Resolver()
resolve
Out[11]: <resolver.Resolver at 0x28b55d39b10>
resolve('sixty-north.com')
Out[12]: '93.93.131.30'
resolve.__call__('sixty-north.com')
Out[13]: '93.93.131.30'
resolve._cache
Out[14]: {'sixty-north.com': '93.93.131.30'}
resolve('pluralsight.com')
Out[15]: '54.203.101.68'
resolve._cache
Out[16]: {'sixty-north.com': '93.93.131.30', 'pluralsight.com': '54.203.101.68'}
```

## Similar like functions Classes are also Objects, but they are not instance objects

1. Similar like `def` keyword binds a function to a named-reference, the `class` keyword also
do the same. Like below the `resolver` is identified as `class object`.
```python
from resolver import Resolver
>>> Resolver                                                                    
<class 'resolver.Resolver'>     
```
2. Similar like callable functions the class are also by default callable

Example when a function is called and the returned instance is a class object. 
```python
# Example1:
>>> def sequence_class(immutable):
...     if immutable:                                                           
...         cls = tuple                                                         
...     else:                                                                   
...         cls = list                                                          
...     return cls                                                              
...

# Example2:
>>> seq = sequence_class(immutable=True)                                        
>>> t = seq("Timbuktu")                                                         
>>> t                                                                           
('T', 'i', 'm', 'b', 'u', 'k', 't', 'u')                                        
>>> type(t)                                                                     
<class 'tuple'>
```

Same implementation done by conditional expression, It is evaluated as :
<return expression1> if <condition is true> else <return expression2>
```python
>>> def sequence_class(immutable):
...     return tuple if immutable else list
...                                                                             
>>> seq = sequence_class(immutable=False)                                       
>>> s = seq("Nairobi")                                                          
>>> s                                                                           
['N', 'a', 'i', 'r', 'o', 'b', 'i']                                             
>>> type(s)                                                                     
<class 'list'>   
```
Here is the example where we can see that the lambda creates an unnamed method. Here
we used the lambda inside the sorted method which takes a iterable and key, which we
passed as `lambda`. 

In this case our lambda accepts only one argument called "`name`" and the body of the 
lambda `name.split()[-1]`, returns the last index of the splited name. 

```python
>>> scientists = ['Marie Curie', 'Albert Einstein', 'Rosalind Franklin',
...               'Niels Bohr', 'Dian Fossey', 'Isaac Newton',                  
...               'Grace Hopper', 'Charles Darwin', 'Lise Meitner']             
>>>                                                                             
>>> sorted(scientists, key=lambda name: name.split()[-1])                       
['Niels Bohr', 'Marie Curie', 'Charles Darwin', 'Albert Einstein', 'Dian Fossey'
, 'Rosalind Franklin', 'Grace Hopper', 'Lise Meitner', 'Isaac Newton']
```

Another example of lambda creating function-reference which is pointing towards an object
```python
>>> last_name = lambda name: name.split()[-1]                                   
>>> last_name                                                                   
<function <lambda> at 0x10d764ca0>                                              
>>> last_name("Nikola Tesla")                                                   
'Tesla'       
```
The above is exactly equivalent to defining a function like this :
```python
>>> def first_name(name):                                                       
...     return name.split()[0]                                                  
...    
```