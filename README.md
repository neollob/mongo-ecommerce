#### Ejercicio entregable
---

__Entregable__

Sigue los siguientes pasos:
1. clona el repositorio a tu directorio de trabajo local
```script
git clone https://github.com/bcncodes-training/mongo-ecommerce.git
```
2. haz el _checkout_ de la rama asignada:
```script
git checkout "pair"+numero
```
3. genera los documentos demandados:
  - mongodump de la bd (documentos+datos+índices)
  - queries.js con la información de las queries y los índices creados

4. sube los cambios:
```script
  git add .
  git commit -m 'msg'
  git push nombre_rama
 ```
__Ejercicio__

- Crear una bd para un ecommerce que contenga las siguientes colecciones:
  - USER
  - PRODUCT
  - CATEGORY
  - CART

- Generar consultas sobre las colecciones anteriores para llevar a cabo las siguientes operaciones:
  
  -  Generar algunas consultas sobre productos, que puedan ser requeridas por los usuarios (Buscar por tipo de producto, talla, tamaño, precios, recomendados, ofertas, últimas adquisiciones, etc…)
  
  crear indices nombre producto, modelo, precio, categoria, tipo (recomendado,oferta,promocion), fecha (novedades)
  
  aggregates con lookouts
  
  
  -  Realizar un mantenimiento de la jerarquía de categorías añadiendo una nueva categoría y modificando una categoría. Realizar también las consultas para obtener el hilo de Ariadna de un producto determinado
  
  insertar categoria con padre o sin. 
  
  Crear indices nombre y padre
  
  hilo de ariadna -> buscar el padre del padre hasta encontrar huerfano
  

  
  -  Gestionar el carrito: Añadir un producto al carrito, eliminar un producto del carrito, abandono del carrito sin comprar, pagar.

