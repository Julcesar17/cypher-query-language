# cypher-query-language

Cypher es un lenguaje de consulta diseñado para gráficos.

La DB se representa como un grafo, Siendo las entidades circulos y conectadas entre si mediante flechas.

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
