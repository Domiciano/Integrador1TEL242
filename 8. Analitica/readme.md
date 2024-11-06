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
Para correr el servicio de pyhton usando uvicorn
```
uvicorn main:app --reload
```
