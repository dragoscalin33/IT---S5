# Bases de Datos No Relacionales  
## Consultas con MongoDB  

En este proyecto se trabajó con **MongoDB** y una base de datos orientada a una aplicación de entretenimiento cinematográfico.  
El objetivo fue **realizar consultas analíticas en un entorno NoSQL**, a partir de varias colecciones proporcionadas.  


## 1. Creación de la base de datos y colecciones  

Se creó la base de datos y se cargaron las siguientes colecciones:  

- **users** → información de usuarios (nombre, email, contraseña cifrada).  
- **theatres** → datos de cines (ID, dirección, coordenadas geográficas).  
- **sessions** → sesiones de usuario (ID de usuario, tokens JWT para autenticación).  
- **movies** → detalles de películas (trama, géneros, duración, elenco, comentarios, año de lanzamiento, directores, clasificación, premios).  
- **comments** → comentarios de usuarios sobre películas (autor, ID de película, texto, fecha).  

Ejemplo de inserción de documento en la colección *movies*:  

```json
{
  "title": "The Blues Brothers",
  "year": 1980,
  "genres": ["Comedy", "Musical"],
  "cast": ["John Belushi", "Dan Aykroyd"],
  "directors": ["John Landis"],
  "imdb": {
    "rating": 7.9,
    "votes": 234567
  },
  "awards": {
    "wins": 5,
    "nominations": 3
  }
}

```

2. Consultas realizadas

Nivel 1
	•	Mostrar los 2 primeros comentarios de la base de datos.
	•	Número total de usuarios registrados.
	•	Número de cines en California.
	•	Identificar al primer usuario registrado.
	•	Número de películas de comedia en la base de datos.

Ejemplo de consulta:
```
db.comments.find().limit(2);

db.users.countDocuments();
```

	•	Películas producidas en 1932 cuyo género sea drama o estén en francés.

```
db.movies.find({
  year: 1932,
  $or: [
    { genres: "Drama" },
    { languages: "French" }
  ]
});

```

	•	Películas estadounidenses con entre 5 y 9 premios producidas entre 2012 y 2014.

```
db.movies.find({
  countries: "USA",
  "awards.wins": { $gte: 5, $lte: 9 },
  year: { $gte: 2012, $lte: 2014 }
});

```

Nivel 2
	•	Número de comentarios escritos por usuarios con correo del dominio GAMEOFTHRON.ES.
```

db.comments.aggregate([
  { $match: { email: /@GAMEOFTHRON\.ES$/ } },
  { $group: { _id: "$email", total: { $sum: 1 } } }
]);
```
	•	Número de cines en cada código postal de Washington D.C.

```
db.theatres.aggregate([
  { $match: { "location.address.state": "DC" } },
  { $group: { _id: "$location.address.zipcode", total: { $sum: 1 } } }
]);
```

Nivel 3
	•	Películas dirigidas por John Landis con puntuación IMDb entre 7.5 y 8.

```
db.movies.find({
  directors: "John Landis",
  "imdb.rating": { $gte: 7.5, $lte: 8 }
});
```

	•	Mostrar en un mapa la ubicación de todos los teatros:

```
db.theatres.find(
  { "location.geo": { $exists: true } },
  { "location.geo": 1, _id: 0 }
);
```

(Se puede exportar a GeoJSON y visualizar en MongoDB Compass o en herramientas de mapas).


3. Buenas prácticas aplicadas
   
	•	Uso de filtros con operadores lógicos ($and, $or, $gte, $lte).

	•	Consultas agregadas con $group y $match para obtener métricas.

	•	Normalización parcial de datos en campos multivalor (géneros, idiomas, premios).

	•	Exportación de resultados a GeoJSON para visualización geoespacial.

	•	Documentación de consultas en formato Markdown para trazabilidad del proyecto.


4. Tecnologías utilizadas:

	•	MongoDB
	
	•	Mongo Shell
	
	•	MongoDB Compass

