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
```
from fastapi import FastAPI
from pydantic import BaseModel

# Crea la aplicación FastAPI
app = FastAPI()

# Define un modelo de datos para el POST
class Item(BaseModel):
    name: str
    description: str

# Endpoint GET
@app.get("/")
def read_root():
    return {"message": "Hola Mundo"}

# Endpoint POST
@app.post("/items/")
def create_item(item: Item):
    return {"name": item.name, "description": item.description}

```

Para correr el servicio de pyhton usando uvicorn
```
uvicorn main:app --reload
```
