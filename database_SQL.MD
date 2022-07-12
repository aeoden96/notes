1. S PONAVLJANJEM


```js
 db.narudzbe.find(  {  "detalji.proizvod.kategorija" :  "Pića" } , { "_id":0,  "kupac" :1  }  ).sort( { "kupac.drzava" :-1, "kupac.grad" :1 }).limit(10)
 ```

1. DISTINCT
```js
db.test.distinct({"key":true,"score":true,"note":true});
```  
NEMOZE S LIMIT

```js
db.narudzbe.distinct( "kupac"  ,   {  "detalji.proizvod.kategorija" :  "Pića" }  )   
```
NEMOZE S LIMIT


```js
db.narudzbe.aggregate([
    {$match: {  "detalji.proizvod.kategorija" :  "Pića" }   },
    { $group: {   _id:  "$kupac" } },
    { $sort: {"_id.drzava" :-1 ,"_id.grad" :1 } },
    { $limit: 10},
    { $project: { _id:0 , drzava: "$_id.drzava",grad: "$_id.grad"  , naziv: "$_id.naziv"  } }
])


db.narudzbe.distinct( "detalji.proizvod.id" )

db.totalTable.findOne( {  "total_popust" :  "Pića" } )

db.totalTable.aggregate({$match:{$expr:{$gt:["$total_popust", "$total_popust"]}}})
```


```js
db.collection.aggregate(
[  
    {$unwind : "$sections"},
    {
        "$match": {
            "_id": ObjectId("571c5c87faf473f40fd0317c")
        }
    },
    {
        "$group" : {
            "_id" : "$_id",
            "maxSectionId" : {"$max" : "$sections.Id"}
        }
    }
]);
```


```js
{ $or:  [
       {  "zaposlenik.zaposlenik" :  "Robert King" },
       {  "kupac.drzava" :  "Portugal" }
] }


 db.narudzbe.find(
 { $or:  [
       {  "zaposlenik.zaposlenik" :  "Robert King" },
       {  "kupac.drzava" :  "Portugal" }
] }
 , { "narudzba_id" : 1  }  ).count()
```


```js
 db.narudzbe.find(
   {  "kupac.drzava" :  "Portugal" }
 , { "zaposlenik.zaposlenik" : 1  }  
)

db.narudzbe.distinct( "zaposlenik.zaposlenik"  ,  {  "kupac.drzava" :  "Portugal" })
```
```js
 db.narudzbe.find(
   {  "zaposlenik.zaposlenik" :  "Janet Stevens" }
 , { "zaposlenik" : 1  }  
)

 db.narudzbe.updateMany(
   {  "zaposlenik.zaposlenik" :  "Janet Leverling" }
 ,{$set: {  "zaposlenik.zaposlenik" :  "Janet Stevens"  }}  
)



db.popusti.find({ $expr:{$qt:["$popust_total_AGGR", "$value.popust_total_MR"]} } , { "_id":0,  "popust_total_AGGR":1 ,  "value.popust_total_MR":1  })


db.popusti.find("this.popust_total_AGGR+ 0.001 > this.value.popust_total_MR").count();







db.popusti.aggregate( [           {   $match:{ $expr:{$eq:["$popust_total_AGGR", "$value.popust_total_MR"]}   } }  ,{ $group: { _id: null, count: { $sum: 1 } } } ] )

db.inventory.find( { "popust_total_AGGR" : { $eq: 20 } } )
```

```js
    db.narudzbe.aggregate([ 
    {$match: {  "datum_narudzbe" : { $gte: new Date(2016)  } }   }, 
    { $group: {   _id:  "$kupac" } },
    { $sort: {"_id.grad" :-1 ,"_id.drzava" :1 } },
    { $limit: 10},
     { $project: { _id:0 ,grad: "$_id.grad"  ,drzava: "$_id.drzava" , naziv: "$_id.naziv"  } }
])

  db.narudzbe.aggregate([ 
    { $match: {  "datum_narudzbe" : { $gte: "2016-01-01"  } }   },
    { $unwind : "$detalji"},
    { $group: {   _id:  "$zaposlenik" , total_popust: {  "$sum":{ $multiply: [ "$detalji.kolicina", "$detalji.popust" , "$detalji.jedinicna_cijena" ] }     } } }
   ]).pretty()


  db.narudzbe.aggregate([ 
    { $match: {  "datum_narudzbe" : { $gte: "2016-01-01"  } }   },
    { $unwind : "$detalji"}  ,
    { $group: 
             {   
                  _id:  "$zaposlenik"   ,  
                   total_popust: {    "$sum": {   $multiply: [ "$detalji.kolicina", "$detalji.popust" , "$detalji.jedinicna_cijena" ] } }
             }
    }
 ])

```

```js

var key= this.zaposlenik;


var value = {
                   count:1,
                   total:   this.   



}


  db.narudzbe.aggregate([ 
    { $match: {  "datum_narudzbe" : { $gte: "2016-01-01"  } }   },
    { $unwind : "$detalji"}  ,
    { $group: 
             {   
                  _id:  "$zaposlenik"   ,  
                   total_popust: {    "$sum": {   $multiply: [ "$detalji.kolicina", 
                                                               "$detalji.popust" , 
                                                               "$detalji.jedinicna_cijena" 
                                                             ] 
                                              } 
                                 }
             }
    },
  { $merge: {  into: "totalTable" , on:"_id" ,whenMatched: "merge", whenNotMatched: "insert" } }
 ])
 ```