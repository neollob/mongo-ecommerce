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
    
      db.product.createIndex({"nombre":"text","precio":1})
  
    aggregates con lookouts
    
     Buscar productos por nombre
     
      db.product.find( { $text: { $search: "Lenovo" } } )
          
     Buscar productos por rango de precios
     
      db.product.find( { $and: [ { precio: { $gte: 20 } }, { precio: { $lte: 100 } } ] } )
     
     Buscar productos por rango de precios y categoria
      busqueda por product_id
      
      db.product.aggregate([
        {$match:{ $and:[ 
          {"precio":{ $gte: 20 , $lte: 1000 }},
          {"categoria":"5d9392ff1c9d440000261a17"}
        ]}}         
      ])
            
     busqueda por catgory_name y precio produto
      
      
      db.category.aggregate([
        {
          "$project": { "_id": {"$toString": "$_id"},"name":"$name"}
        },
        {
          "$lookup": {
            "from": "product",
            "localField": "_id",
            "foreignField": "categoria",
            "as": "role"
          }
        },
        {$match:{ $and:[ 
                {"role.precio":{ $gte: 20 ,$lte: 1000}},
                {"name":{$in:["Ordenadores"]}}
              ]}}
      ])
  
  -  Realizar un mantenimiento de la jerarquía de categorías añadiendo una nueva categoría y modificando una categoría. Realizar también las consultas para obtener el hilo de Ariadna de un producto determinado
  
  insertar categoria con padre o sin. 
  
      db.category.insert( { name: ** nombreCategoria **, padre: ** padreId ** } )
      db.category.insert( { name: ** nombreCategoria ** } )
  
  Crear indices nombre y padre
      
      db.category.createIndex({"name":"text","padre_id":1})     
  
  hilo de ariadna -> buscar el padre del padre hasta encontrar huerfano
  
  
  -  Gestionar el carrito: Añadir un producto al carrito, eliminar un producto del carrito, abandono del carrito sin comprar, pagar.

  añadir a carro
  
    ver si hay stock
      
      db.product.aggregate(
      {"$project": { "_id": {"$toString": "$_id"},cantidad:"$cantidad"}},
      {$match:{"_id":"5d95c2170d79970740c4ccc9"}}
      )
           
    añadir producto (si no hay) o sumar (si ya hay) unidad a carro quitar de stock
            
      db.cart.update(
        { "userid":"5d8f8e8f1c9d4400007120d3", state:"A","products.prodid":"5d95a8610d79970740c4ccbe"},
        { $inc: {"products.qty": 1 } },
        { upsert: true}
      )
      db.product.update(
        { "_id":ObjectId("5d95a57516a7610740c9667b") },
        { $inc: {"cantidad": -1 } },
        { upsert: true}
      )

     
  quitar de carro y añadir a stock
  
      db.cart.update(
        { "userid":"5d8f8e8f1c9d4400007120d3", state:"A","products.prodid":"5d95a8610d79970740c4ccbe"},
        { $inc: {"products.qty": -1 } },
        { upsert: true}
      )
      db.product.update(
        { "_id":ObjectId("5d95a57516a7610740c9667b") },
        { $inc: {"cantidad": 1 } },
        { upsert: true}
      )
 --------------------------------------------------------------------------------------------     
  abandonar carro
   - verificar fecha carros, si mayor 1 dia no asegura stock
   
    db.cart.find({}).forEach(function(doc) {
       var ts= doc._id.getTimestamp();
       var nts= new Date(ts.getTime() + 1000 * 3600 * 24);
       printjson(ts+" - "+nts)
    })   
    
  pagar
  
      si fecha carro inferior 1 dia pagar
      si mayor verificar si hay stock si no preguntar si mantener el producto en _deseados_ para cuando haya stock
      o eliminar del carro para siempre
      pagar.
db.product.findOne( { "_id": ObjectId("5d93894f1c9d4400002619fb") } )
