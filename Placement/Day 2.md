## Dictionary Function
- dictionary have keys and values
```python
a = {
    "name": "Ashwin",
    "city": "ptm",
    "gender": "fluid",
}

print(a.get("gender"))
```

### Keys() function
- will display all the keys on the dictionary
```python
a = {
    "name": "Ashwin",
    "city": "ptm",
    "gender": "fluid",
}

print(a.keys())
```

### pop()
- to pop last element
```python
a.pop(key,default_value)
```

### Setdefault()
```python
a = {
    "name": "Ashwin",
    "city": "ptm",
    "gender": "fluid",
    "age": "12"
}

a.setdefault("age", 50)
print(a)
```