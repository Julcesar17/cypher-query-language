# cypher-query-language

Cypher es un lenguaje de consulta diseñado para grafos.

![Imagen grafo](https://graphacademy.neo4j.com/courses/cypher-fundamentals/1-reading/1-intro-cypher/images/whiteboard.jpg)

La DB se representa como un grafo, Siendo las entidades circulos y conectadas entre si mediante flechas.

El dataset ocupado en los ejemplos de las consulas es `"Movie"`.

![Grafo de dataset Movie](https://graphacademy.neo4j.com/courses/cypher-fundamentals/1-reading/images/movie-schema.svg)

La nomenclatura de Cypher es la siguiente:

- Los nodos se representan por paréntesis `()`.
- Se usan dos puntos para indicar etiquetas, por `(:Persona)`.
- Las relaciones entre nodos se representan con dos guiones, por ejemplo `(:Persona)--(: Película)`.
- La dirección de una relación se indica mediante un símbolo mayor o menor que `<` o `>`, por ejemplo `(:Persona)-→(:Película)`.
- El tipo de relación se escribe utilizando corchetes entre los dos guiones: `[` y `]`, por ejemplo `[:ACTED_IN]`.
- Las propiedades *speech bubble* especifican en una sintaxis similar a JSON, por ejemplo, `{name: 'Tom Hanks'}`.

Un ejemplo de una consulta en cypher:
```
// example Cypher query
(m:Movie {title: 'Cloud Atlas'})<-[:ACTED_IN]-(p:Person)
```
Los dos nodos de esta cosunta son *Movie* y *Person*. Los nodos *Person* tienen una relación *ACTED_IN* en dirección a los nodos *Movie*. El nodo de *Movie* en esta consulta se filtra por la propiedad del *title* con el valor *Cloud Atlas*. Entonces, esta consulta representa a todas las personas en el grafo que actuaron en la película, *Cloud Atlas*.

### ¿Cómo funciona Cypher?

Cypher funciona haciendo coincidir patrones en los datos. Recuperando datos del grafo usando la palabra reservada `MATCH`. La sentencia `MATCH` es similar a `FROM` en una consulta SQL.

Por ejemplo, si queremos buscar un nodo *Person* en el grafo, usamos `MATCH` con la etiqueta de `:Person` - con un prefijo de dos puntos `:`.

```
// incomplete MATCH clause because we need to return something
MATCH (:Person)
```

Si quisieramos recuperar todos los nodos *Person* del grafo. Podemos asignar una variable colocandola antes de los dos puntos. Usemos la variable `p`. La variable `p` representa todos los nodos *Person* recuperados del grafo, podemos devolverlos usando la palabra reservada `RETURN`.

```
//Query para devuelve todos los nodos Person
MATCH (p:Person)
RETURN p
```

Esta consulta devuelve todos los nodos del grafo con la etiqueta *Person*. Se pueden ver el resultados devuelto utilizando la vista de grafo o la vista de tabla. Cuando se selecciona la vista de tabla, también se pueden ver las propiedades de los nodos devueltos.

Todos los nodos *Person* tienen una propiedad *name*. Si quisieramos encontrar el nodo *Person* cuyo nombre es *Tom Hanks*, se utilizarían las llaves `{..}` para especificar el llave/valor de *name* y *Tom Hanks* como filtro. Como *Tom Hanks* es un string, se coloca entre comillas simples o dobles.

```
/// Regresa nodo Person donde name es igual a Tom Hanks
MATCH (p:Person {name: 'Tom Hanks'})
RETURN p
```

En la consulta anterior, se regresa el nodo que representa a *Tom Hanks*. En la vista de grafo, el nodo se visualiza como un circulo. También se puede ver el resultado devuelto en la vista de tabla, donde se puede ver las propiedades del nodo.

En Cypher, tambien se puede acceder a las propiedades de un nodo usando *dot notation*. Por ejemplo, para devolver la propiedad *name* usamos `p.name`.

```
/// Regresa la propiedad born del nodo Person
MATCH (p:Person {name: 'Tom Hanks'})
RETURN  p.born
```

Otra forma de filtrar consultas es mediante la palabra reservada `WHERE`, en lugar de especificar el valor de la propiedad en línea con llaves.

La siguiente consulta devuelve los mismos datos que la consulta anterior.

```
MATCH (p:Person)
WHERE p.name = 'Tom Hanks'
RETURN p.name
```

Se puede usar `WHERE` para filtrar consultas con más lógica. Por ejemplo, filtrar por dos valores de *name*.

```
MATCH (p:Person)
WHERE p.name = 'Tom Hanks' OR p.name = 'Rita Wilson'
RETURN p.name, p.born
```

Esta consulta regresa dos nombres y sus años de nacimiento respectivamente.

### Buscando relaciones

Se puede modificar la sentencia `MATCH` para regresar las relaciones de tipo *ACTED_IN* a cualquier nodo. El dataset usado muestra que la relación *ACTED_IN* va desde el nodo *Person* al nodo *Movie*. Este comportamiento se le conoce como **traversal**.

```
//incomplete code
MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->()
```

El dataset indica que el nodo destino de la relación *ACTED_IN* es el nodo *Movie*, por lo que no es necesario especificar la etiqueta `:Movie` en el nodo; en su lugar, usaremos la variable m.

```
MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m)
RETURN m.title
```

La anterior consulta regresa los títulos de todos las peliculas en los que actuó *Tom Hanks*.

Si el grafo tuviera etiquetas diferentes, por ejemplo, nodos de *Television* y *Movie*, esta consulta regresaría todos los nodos de *Television* y *Movie* en los que actuó *Tom Hanks*. Es decir, si tuviéramos varios tipos de nodos al final de las relaciones *ACTED_IN* en nuestro grafo, podríamos asegurarnos de que solo devolvamos películas.

Por ejemplo:

```
MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m:Movie)
RETURN m.title
```

### Filtrando consultas

En las consultas anteriores, se ha filtrado la sentencia `WHERE` con una igualdad para las propiedades de un nodo, también se pueden usar expresiones lógicas para filtrar aún más lo que desea regresar.

En la siguiente consulta se regresan los nodos *Person* y *Movies* donde la persona actuó en una película que se lanzó en *2008* o *2009*:

```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released = 2008 OR m.released = 2009
RETURN p, m
```

### Filtrando por etiquetas de nodos

Si necesitaramos regresar los nombres de todas las personas que actuaron en la película *The Matrix*, la consulta sería la siguiente:

```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.title='The Matrix'
RETURN p.name
```

Una alternativa a esta consulta sería la siguiente, donde las etiquetas de los nodos se encuentras en la sentencia `WHERE`:

```
MATCH (p)-[:ACTED_IN]->(m)
WHERE p:Person AND m:Movie AND m.title='The Matrix'
RETURN p.name
```

### Filtrando usando rangos

Se puede especificar un rango para filtrar una consulta. A continuación, se regresan los nodos *Person* de personas que actuaron en películas estrenadas entre *2000* y *2003*:

```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE 2000 <= m.released <= 2003
RETURN p.name, m.title, m.released
```

### Filtrando por existencia de una propiedad

Por defecto, no existe ningún requisito de que un nodo o relación tenga una propiedad determinada. A continuación, se muestra un ejemplo de una consulta en la que solo queremos devolver los nodos *Movies* en los que *Jack Nicholson* actuó en la película y la película tiene la propiedad *tagline*.

```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name='Jack Nicholson' AND m.tagline IS NOT NULL
RETURN m.title, m.tagline
```

### Filtrando por cadenas parciales

Cypher tiene un conjunto de palabras claves relacionadas con *strings* que se pueden usar con las sentencias `WHERE`, para buscar patrones dentro de los *strings*. Especificamente se pueden usar `STARTS WITH`, `ENDS WITH`, y `CONTAINS`.

Por ejemplo, para encontrar todos los actores en el grafo cuyo primer nombre es Michael:

```
MATCH (p:Person)-[:ACTED_IN]->()
WHERE p.name STARTS WITH 'Michael'
RETURN p.name
```

Las busquedas en los *strings* distinguen entre mayúsculas y minúsculas, por lo que se podría utilizar las funciones `toLower()` o `toUpper()` para asegurar que regresen los resultados correctos. Por ejemplo:

```
MATCH (p:Person)-[:ACTED_IN]->()
WHERE toLower(p.name) STARTS WITH 'michael'
RETURN p.name
```

### Filtrando por patrones en el grafo

Se quieren encontrar a todas las personas que escribieron una película pero no dirigieron esa misma película. La consulta sería:

```
MATCH (p:Person)-[:WROTE]->(m:Movie)
WHERE NOT exists( (p)-[:DIRECTED]->(m) )
RETURN p.name, m.title
```

### Filtrando usando listas

En Cypher, una lista es un conjunto de valores separados por comas entre corchetes.

La lista se usa dentro de la sentencia `WHERE`. Durante una consulta, se compara cada propiedad con los valores `IN` de la lista. Puede colocar valores numéricos o cadenas en la lista, pero por lo general, los elementos de la lista son del mismo tipo de datos.

En el siguiente ejemplo, se busca regresar los nodos *Person* de personas nacidas en 1965, 1970 o 1975:

```
MATCH (p:Person)
WHERE p.born IN [1965, 1970, 1975]
RETURN p.name, p.born
```

También se puede comparar un valor con una lista existente en el grafo.

La relación `:ACTED_IN` tiene una propiedad `roles` que contiene la lista de roles que tuvo un actor en una película en particular en la que actuó. La siguiente consulta devuelve el nombre del actor que interpretó a *Neo* en la película *The Matrix*:

```
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
WHERE  'Neo' IN r.roles AND m.title='The Matrix'
RETURN p.name, r.roles
```

#### Creando nodos

Se usa la palabra reservada `MERGE` para crear un nodo en la dataset.

Después de la sentencia `MERGE`, se especifica el nodo que se quiere crear. Por lo general, será un solo nodo o una relación entre dos nodos.

Si quisiéramos crear un nodo para representar a *Michael Cain*. La consulta sería la siguiente:

```
MERGE (p:Person {name: 'Michael Cain'})
```

La consulta anterior crea un solo nodo en el grafo. Cuando se usa `MERGE` para crear un nodo, se debe especificar al menos una propiedad que será la *unique primary key* para el nodo.

Para verificar el nodo creado:

```
MATCH (p:Person {name: 'Michael Cain'})
RETURN p
```

### Ejecutando múltiples sentencias

Se puede ejecutar varias sentencias `MERGE` juntas dentro de una sola consulta.

```
MERGE (p:Person {name: 'Katie Holmes'})
MERGE (m:Movie {title: 'The Dark Knight'})
RETURN p, m
```

La consulta anterior crea dos nodos, cada uno con una propiedad *primary key*. Debido a que se especifico las variables p y m, se pueden usar para regresar los nodos creados.

### Usando `CREATE` en lugar de `MERGE` para crear nodos

Cypher tiene la palabra reservada `CREATE` que se puede usar para crear nodos. El beneficio de usar `CREATE` es que no busca el *primary key* antes de agregar el nodo. Se usa `CREATE` si se está seguro de que los datos son correctos y desea una mayor velocidad durante la importación. Se usa `MERGE` para eliminar la duplicación de nodos.

### Creando relaciones entre dos nodos

La sentencia `MERGE` se usa para crear nodos en el grafo, pero también se utiliza para crear relaciones entre dos nodos. Primero se debe declarar la referencia a los nodos para los que se creará la relación. Para crear una relación entre dos nodos, se debe tener:

- Type (tipo)
- Direction (Dirección)

Por ejemplo, si los nodos *Person* y *Movie* existen, podemos obtenerlos usando la sentencia `MATCH` antes de crear la relación entre ellos.

```
MATCH (p:Person {name: 'Michael Cain'})
MATCH (m:Movie {title: 'The Dark Knight'})
MERGE (p)-[:ACTED_IN]->(m)
```

En la consulta anterior, se buscan los dos nodos donde se quiere crear la relación. Después se usa la referencia a los nodos para crear la relación *ACTED_IN*.

Se puede verificar la relación con la siguiente consulta:

```
MATCH (p:Person {name: 'Michael Cain'})-[:ACTED_IN]-(m:Movie {title: 'The Dark Knight'})
RETURN p, m
```

No es necesario especificar la dirección de la relación, ya que la ejeciutar la consulta, se buscarán todos los nodos que estén conectados y su dirección.

Por ejemplo, si ejecutamos la siguiente consulta:

```
MATCH (p:Person {name: 'Michael Cain'})<-[:ACTED_IN]-(m:Movie {title: 'The Dark Knight'})
RETURN p, m
```

Esta consulta no devuelve nodos, ya que no hay nodos con la relación *ACTED_IN* con los nodos *Person* hacía los nodos *Movie* en el grafo.

### Creando nodos y relaciones usando múltiples sentencias `MERGE`

Podemos usar varias sentencias `MERGE` dentro de un mismo bloque de código.

```
MERGE (p:Person {name: 'Chadwick Boseman'})
MERGE (m:Movie {title: 'Black Panther'})
MERGE (p)-[:ACTED_IN]-(m)
```

Este código crea dos nodos y una relación entre ellos. Como usamos las variables p y m, podemos usarlas en el código para crear la relación entre los dos nodos. Por default, la relación la crea de izquierda a derecha.

Se puede verificar la relación con la siguiente consulta:

```
MATCH (p:Person {name: 'Chadwick Boseman'})-[:ACTED_IN]-(m:Movie {title: 'Black Panther'})
RETURN p, m
```

### Usando `MERGE` para crear nodos y una relación en una sola sentencia

`MERGE` crea el nodo o la relación si no existe en el grafo.

La siguiente consulta crea con éxito los nodos y la relación:

```
MERGE (p:Person {name: 'Emily Blunt'})-[:ACTED_IN]->(m:Movie {title: 'A Quiet Place'})
RETURN p, m
```

Si se ejecuta varías veces la misma consulta no crearía nodos ni relaciones nuevas.

### Actualizando propiedades

##### Agregar propiedades para un nodo o relación

Hay dos formas de asignar una propiedad a un nodo o una relación.

1. Inline como parte de la sentencia `MERGE`

Se puede asignar una propiedad a una relación o nodo inline de la siguiente manera:

```
MERGE (p:Person {name: 'Michael Cain'})
MERGE (m:Movie {title: 'Batman Begins'})
MERGE (p)-[:ACTED_IN {roles: ['Alfred Penny']}]->(m)
RETURN p,m
```

En esta consulta, el actor *Michael Cain* existe, pero la película *Batman Begins* no. Buscamos el nodo *Person* y creamos el nodo *Movie*. Luego, creamos la relación *ACTED_IN* entre el nodo de *Michael Cain* y el nodo *Batman Begins* recién creado. Y se asigna la propiedad *roles* para esta relación en una lista de valores, que contiene el valor *Alfred Penny*.

2. Usar la palabra reservada `SET` para hacer referencia a un nodo o una relación

Podemos usar la sentencia `SET` para asignar un valor de propiedad. En la sentencia `MERGE` o `MATCH` en particular donde se ha definido una variable para hacer referencia al nodo o relación, se puede establecer el valor de la propiedad.

```
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
WHERE p.name = 'Michael Cain' AND m.title = 'The Dark Knight'
SET r.roles = ['Alfred Penny']
RETURN p, r, m
```

#### Actualizando propiedades

Teniendo una referencia a un nodo o relación, también se puede usar `SET` para modificar la propiedad. Por ejemplo, si quisiéramos modificar el rol de *Michael Cain*, podríamos hacer lo siguiente:

```
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
WHERE p.name = 'Michael Cain' AND m.title = 'The Dark Knight'
SET r.roles = ['Mr. Alfred Penny']
RETURN p, r, m
```

#### Eliminando propiedades

Se puede eliminar una propiedad de un nodo o una relación utilizando la palabra reservada `REMOVE` o asignado la propiedad en *null*.

En la siguiente consulta eliminamos la propiedad *roles* de la relación:

```
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
WHERE p.name = 'Michael Cain' AND m.title = 'The Dark Knight'
REMOVE r.roles
RETURN p, r, m
```

En la siguiente consulta eliminamos la propiedad *born* de un actor:

```
MATCH (p:Person)
WHERE p.name = 'Gene Hackman'
SET p.born = null
RETURN p
```

**Nota:** Nunca se de be eliminar la propiedad que se usa como *primary key* de un nodo.

### Procesamiento de `MERGE`

Se puede usar `MERGE` para crear nodos y relaciones en el grafo. Las sentencias `MERGE` funcionan intentando primero encontrar un patrón en el grafo. Si se encuentra el patrón, los datos ya existen y no crea nada. Si no se encuentra el patrón, crea los datos.

### Modificando el comportamiento de `MERGE`

También se puede modificar el comportamiento en tiempo de ejecución, permitiendo asignar propiedades cuando se crea un nodo o cuando se obtiene un nodo. Podemos usar las sentencias condicionales `ON CREATE SET` o `ON MATCH SET`, o la palabra reservada `SET` para asignar propiedades adicionales.

En el siguiente ejemplo, si el nodo *Person* de *McKenna Grace* no existe, se crea y se asigna la propiedad *createdAt*. Si se encuentra el nodo, se asigna la propiedad *updatedAt*. En ambos casos, se asigna la propiedad *born*.

```
// Find or create a person with this name
MERGE (p:Person {name: 'McKenna Grace'})

// Only set the `createdAt` property if the node is created during this query
ON CREATE SET p.createdAt = datetime()

// Only set the `updatedAt` property if the node was created previously
ON MATCH SET p.updatedAt = datetime()

// Set the `born` property regardless
SET p.born = 2006

RETURN p
```

Si se quiere asignar varias propiedades en una sentencia `ON CREATE SET` o `ON MATCH SET`, los valores se separan con comas. Por ejemplo:

`ON CREATE SET m.released = 2020, m.tagline = '¡Un gran viaje!'`

### Fusión de relaciones

Si queremos crear un nodo *Movie* para la película *The Cider House Rules*. Podríamos escribir la siguiente consulta, la cual producirá un error.

```
MERGE (p:Person {name: 'Michael Cain'})-[:ACTED_IN]->(m:Movie {title: 'The Cider House Rules'})
RETURN p, m
```

Esto es lo que sucede en al procesar las consultas:

1. Intenta encontrar un nodo *Person* con el nombre *Michael Cain*.

2. Después intenta obterner las relaciones *ACTED_IN* en el grafo.

3. Para los nodos encontrados en la relación, buscará el nodo de *Michael Cain*.

En el dataset, existe el nodo *Person* **pero no existe** el nodo *Movie*. Entonces se intentará crear el patrón completo; primero creando un nuevo nodo *Person*, luego el nodo *Movie*, antes de crear la relación entre ellos.

Esto provocará que regrese un error que nos indique que este código violaría la restricción de integridad.

Pero se puede dividir la consulta en tres partes. Primero se crea el nodo *Person*, luego el nodo *Movie*, luego la relación *ACTED_IN* entre los dos.

```
// Find or create a person with this name
MERGE (p:Person {name: 'Michael Cain'})

// Find or create a movie with this title
MERGE (m:Movie {title: 'The Cider House Rules'})

// Find or create a relationship between the two nodes
MERGE (p)-[:ACTED_IN]->(m)
```

Esta consulta crea el nodo *Movie* y la relación.

### Eliminando datos

En un grafo se pueden eliminar:

- nodos
- relaciones
- propiedades

Para eliminar cualquier dato en un dataset, primero se debe obtener y luego se puede eliminar.

#### Eliminar un nodo

Se ha creado un nodo *Person* para *Jane Doe*.

```
MERGE (p:Person {name: 'Jane Doe'})
```

Para elimiar el nodo primero se obtiene el nodo. Luego, con una referencia al nodo, se puede eliminarlo.

```
MATCH (p:Person)
WHERE p.name = 'Jane Doe'
DELETE p
```

#### Eliminar una relación

Tenemos el nodo *Jane Doe* donde fue agregada como actriz en la película *The Matrix*. Ejecute la siguiente consulta para crear el nodo y la relación.

```
MATCH (m:Movie {title: 'The Matrix'})
MERGE (p:Person {name: 'Jane Doe'})
MERGE (p)-[:ACTED_IN]->(m)
RETURN p, m
```

Para dejar el nodo *Jane Doe* en el grafo, pero eliminar la relación, se obtiene primero la relación y despues se elimina.

```
MATCH (p:Person {name: 'Jane Doe'})-[r:ACTED_IN]->(m:Movie {title: 'The Matrix'})
DELETE r
RETURN p, m
```

Para volver a crear la relación, se ejecuta la siguiente consulta:

```
MATCH (p:Person {name: 'Jane Doe'})
MATCH (m:Movie {title: 'The Matrix'})
MERGE (p)-[:ACTED_IN]->(m)
RETURN p, m
```

Si se intenta eliminar el nodo *Jane Doe* regresará un error porque tiene relaciones en el grafo. Ya que no puede haber relaciones huérfanas.

```
MATCH (p:Person {name: 'Jane Doe'})
DELETE p
```

#### Eliminar un nodo y sus relaciones

Existe una sentencia que permite eliminar un nodo y sus relaciones entrantes o salientes. 

```
MATCH (p:Person {name: 'Jane Doe'})
DETACH DELETE p
```

Esta consulta elimina la relación y el nodo *Person*.

**Nota:** También podría eliminar todos los nodos y relaciones de un dataset con esta sentencia. No lo haga, recuerde que: `"Un gran poder conlleva una gran responsabilidad"`.

```
MATCH (n)
DETACH DELETE n
```

Solo debe hacer esto con datasets relativamente pequeñas, ya que ejecutar esta consulta en un dataset grande agotará la memoria.

#### Consultas adicionales:

**1. What people acted in a movie?**
```
MATCH (p:Person)-[:ACTED_IN]-(m:Movie)
WHERE m.title = 'Sleepless in Seattle'
RETURN p.name AS Actor
```

**2. What person directed a movie?**
```
MATCH (p:Person)-[:DIRECTED]-(m:Movie)
WHERE m.title = 'Hoffa'
RETURN  p.name AS Director
```

**3. What movies did a person act in?**
```
MATCH (p:Person)-[:ACTED_IN]-(m:Movie)
WHERE p.name = 'Tom Hanks'
RETURN m.title AS Movie
```

**4. How many users rated a movie?**
```
MATCH (u:User)-[:RATED]-(m:Movie)
WHERE m.title = 'Apollo 13'
RETURN count(*) AS `Number of reviewers`
```

**5. Who was the youngest person to act in a movie?**
```
MATCH (p:Person)-[:ACTED_IN]-(m:Movie)
WHERE m.title = 'Hoffa'
RETURN  p.name AS Actor, p.born as `Year Born` ORDER BY p.born DESC LIMIT 1
```

**6. What role did a person play in a movie?**
```
MATCH (p:Person)-[r:ACTED_IN]-(m:Movie)
WHERE m.title = 'Sleepless in Seattle' AND
p.name = 'Meg Ryan'
RETURN  r.role AS Role
```

**7. What is the highest rated movie in a particular year according to imDB?**
```
MATCH (m:Movie)
WHERE m.released STARTS WITH '1995'
RETURN  m.title as Movie, m.imdbRating as Rating ORDER BY m.imdbRating DESC LIMIT 1
```

La siguiente consulta agrega una pelicula con su director, cambiando la respuesta de la consulta anterior.

```
//Run the Cypher code to add another Movie node and its director to the graph:
MERGE (casino:Movie {title: 'Casino', tmdbId: 524, released: '1995-11-22', imdbRating: 8.2, genres: ['Drama','Crime']})
MERGE (martin:Person {name: 'Martin Scorsese', tmdbId: 1032})
MERGE (martin)-[:DIRECTED]->(casino)
```

**8. What drama movies did an actor act in?**
```
MATCH (p:Person)-[:ACTED_IN]-(m:Movie)
WHERE p.name = 'Tom Hanks' AND
'Drama' IN m.genres
RETURN m.title AS Movie
```

**9. What users gave a movie a rating of 5?**
```
MATCH (u:User)-[r:RATED]-(m:Movie)
WHERE m.title = 'Apollo 13' AND
r.rating = 5
RETURN u.name as Reviewer
```

**10. Retorna todo el grafo**
```
MATCH (n) RETURN n
```

---

**Fuente:**
https://graphacademy.neo4j.com/

**Recursos:**
https://github.com/neo4j-graph-examples/movies
