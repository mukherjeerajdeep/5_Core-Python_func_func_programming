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

When we can make a function as callable :
```python
    def __call__(self, host):
        if host not in self._cache:
            self._cache[host] = socket.gethostbyname(host)
        return self._cache[host]

```
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

Example when a function is called and the returned instance is a method. 
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

Here is the example where we can see that the lambda creates a unnamed method