# HTTP

Para hacer llamados de tipo GET

```
const response = await fetch(url);
const json = await response.json();
```

Para hacer un POST, PUT o DELETE, sigue esto mismo
```
const response = await fetch(url, {
  method: 'POST', 
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(data)
});
const json = await response.json();
```
Donde data es un objeto
