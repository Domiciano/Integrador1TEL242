Crear un entorno virtual de Python
```
python -m venv nombre_del_entorno
```

Activa el entorno virtual usando en Windows
```
nombre_del_entorno\Scripts\activate
```

O en Linux y MacOS
```
source nombre_del_entorno/bin/activate
```

Puedes extraer todas las depenciencias y sus versiones del proyecto usando
```
pip freeze > requirements.txt
```

# FAST API

A continuación está el hola mundo de esto
```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# Modelo de datos
class Item(BaseModel):
    name: str
    description: str

@app.get("/")
def read_root():
    return {"message": "Hola Mundo"}

@app.post("/items/")
def create_item(item: Item):
    return {"name": item.name, "description": item.description}
```

Para path variables puede usar
```python
@app.get("/items/{item_id}")
def read_item(item_id: int):
    return {"item_id": item_id}
```

Para query params, use
```python
@app.get("/items/")
def read_item(skip: int = 0, limit: int = 10):
    return {"skip": skip, "limit": limit}
```


Para correr el servicio de pyhton usando uvicorn
```
uvicorn main:app --reload
```
