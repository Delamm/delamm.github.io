---
title: "Mi primer proyecto en ciberseguridad: script en Python para detectar y crackear hashes."
published: true
---

La idea de este script sale de un momento puntual hace algunas semanas. Estaba haciendo una màquina tipo CTF y obtuve un hash el cual no sabìa que tipo de hash era, pero tampoco me querìa enfocar mucho en eso y necesitaba recopilar màs informaciòn sobre la màquina, entonces se me ocurriò automatizar un poco esto.

El objetivo del script no es ser una herramienta de crackeo como `jhon` o `hashcat`, sino que su principal función es la de detectar el tipo de hash rápidamente y, en caso de que sea un cifrado débil, poder crackearlo con un diccionario.

Está màs orientado a un uso simple y rápido.

## [](#header-2) wHash

El nombre del script es `wHash`, detecta hashes **MD5**, **SHA-1**, **SHA-256**, **SHA-512**, **bcrypt**, **Argon2**, **PBKDF2**, **SHA-256crypt** y **SHA-512crypt**.

En el caso de aportar un diccionario puede crackear los hashe **MD5**, **SHA-1**, **SHA-256** y **SHA-512**.

Uso:
```python
python3 wHash.py <hash> -d <diccionario(opcional)>
```

Ejemplo:
```shell
❯ python3 wHash.py 482c811da5d5b4bc6d497ffa98491e38 -d /usr/share/wordlists/rockyou.txt

🔍 Hash analizado: 482c811da5d5b4bc6d497ffa98491e38
🛠️ Algoritmos detectados:
 - MD5

🔍 Buscando en diccionario: /usr/share/wordlists/rockyou.txt
¡Coincidencia! Palabra: 'password123' (Algoritmo: MD5)
```


* * *

## [](#header-2) Funcionamiento

`wHash` tiene 3 funciones principales:

*   <span style="color:#85c1e9">detect_hash_type</span>

Esta función primero crea dos expresiones regulares para detectar si el hash está en Hexadecimal o Base64.
```python
    hex_regex = re.compile(r'^[a-f0-9]+$', re.IGNORECASE);
    base64_regex = re.compile(
        r'^([A-Za-z0-9+/]{4})*([A-Za-z0-9+/]{2}==|[A-Za-z0-9+/]{3}=)?$'
    );
```
Estos objetos van a servir para matchear más adelante, la función prosigue analizando los prefijos del hash para detectar scripts como **bcrypt** o **Argon2**.

Luego utilizando los objetos definidos anteriormente detecta si el hash está en Hexadecimal o Base64, y basandose en la longitud puede detectar el tipo de hash.

*   <span style="color:#85c1e9">generate_hash</span>

Utilizando la librería `hashlib`, genera el hash de una palabra utilizando el algoritmo que se le indique.
Esta función está creada para ser utilizada más adelante.

*   <span style="color:#85c1e9">check_dictionary</span>

Esta función recibe 3 argumentos, uno es el hash que introdujo el usuario, otro es la ruta del diccionario y el otro son los algoritmos detectados anteriormente.

Abre el diccionario y también revisa los algoritmos detectados, ya que solo puede decodificar alunos especificos ya mencionados.

En el caso de que el algoritmo sea valido genera el hash con la función <span style="color:#85c1e9">generate_hash</span> y realiza una comparación de hashes entre el introducido por el usuario y el generado por la función.

```python

with open(dict_path, "r", encoding="utf-8", errors="ignore") as file:
            for line in file:
                word = line.strip()
                for algo in algorithms:
                    if algo.lower() in ["md5", "sha-1", "sha-256", "sha-512"]:
                        generated_hash= generate_hash(word, algo);
                        if generated_hash.lower() == hash_input.lower():
                            print(f"\033[92m¡Coincidencia!\033[0m Palabra: '{word}' (Algoritmo: {algo})");
                            return True;

return False

```
* * *

El código completo está disponible en GitHub:

*   [Mi script para crackear hashes con Python](https://github.com/Delamm/wHash)

### [](#header-2) Mejoras

Primero que nada cualquier aporte o mejora que crean necesaria, por mas mínima que sea, es bien recibida y se va a tener en cuenta.

Me gustó mucho el desarrollo de este script, por lo que aprendí de criptografía y también porque es mi primer proyecto serio.

Algunas mejoras que tengo pensadas implementar van a ser:

<span style="color:#e74c4c">1.</span>   Añadir soporte para más hashes.

<span style="color:#e74c4c">2.</span>   Hacerlo más eficiente, implementando hilos por ejemplo.

<span style="color:#e74c4c">3.</span>   Utiliza alguna api de hashes, o agregar un diccionario junto con el script para que se utilice por defecto, como el de CrackStation.

Invito al que quiera a probar el script, lo pueden guardar con un alias entonces no tienen la necesidad de colocar la ruta completa, esto también ayudaría a cumplir el objetivo del script de ser simple y rápido.

Si llegaron hasta esta parte del post les agradezco mucho, voy a seguir actualizando con más herramienas o noticias que me parezcan importantes.

<span style="color:#f5b7b1"><3</span>


