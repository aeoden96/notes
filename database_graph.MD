
```sql
MATCH (p:Proizvod) 
       RETURN p
```

```sql
MATCH (p:Proizvod) 
       RETURN p.proizvod_naziv
```

```sql
MATCH (n:Person) 
       WHERE n.name IS NOT NULL 
       RETURN n.name
```
```sql
MATCH (n:City)
        WHERE n.name IS NOT NULL 
        RETURN DISTINCT n.name
```

vrati sve osobe i gradove koji su bilokako povezani

```sql
MATCH (p:Person)-[]-(c:City)
       RETURN p,c                       
```

vrati sve osobe i gradove koji su povezani s LIVES_IN
```sql
MATCH (p:Person)-[:LIVES_IN]->(c:City)
       RETURN p,c
```

dohvat dva brida i mogucih bridova medu njima

```sql
MATCH (a {name:'Nikola Tesla'} ),(b {name: 'Thomas Edison' })
          RETURN a,b;   
```

```sql
MATCH (a:Person {name:'Nikola'}) return a;
MATCH (a:Person)   WHERE a.name='Nikola' return a;

MATCH (a) WHERE a.name CONTAINS 'Tesla' return a;
MATCH (a) WHERE a.age>70 return a;
```

## AGREGATNE FJE
```sql
MATCH (a:Person) RETURN COUNT(a);
```

numerical:
```sql
MATCH (a:Person) RETURN MIN(a.age);
```

lists:
```sql
MATCH (a:Person) RETURN COLLECT (a.name);
```

grouping persons with same first name:
```sql
MATCH (a:Person) RETURN a.firstName,COUNT(*);
```

order:
ORDER BY 2,1 DESC
SKIP 10 LIMIT 20


Update brid:
```sql
MATCH ( {name: ...} )-[w:WORKED_WITH]-( {name: .... } )
         SET w.from=1882,w.until=1885;
```

Ukloni svojstva
```sql
MATCH (p:Person) SET p.age=NULL;
MATCH (p:Person) REMOVE p.age;
```

Nove labele

```sql
MATCH ...
SET p:Inventor
RETURN p.name,labels(p);
```
vraca listu labela

Remove labele

```sql
MATCH ...
REMOVE p:Inventor
```

Pazi da ne maknes jedinu labelu
```sql
MATCH (a) WHERE labels(a)=[]
RETURN a, id(a);
```

Brisanje podataka
```sql
MATCH (n)
DELETE n;
```
```sql
MATCH (n)
DETACH DELETE n;
```

Brisanje svih bridova izmedu 2 vrha

```sql
MATCH (p:Person)-[r]-()
WHERE p.name IN ['Nikola','Thomas']
DELETE r;
```

-----------------------------------------------------------

Napisati upite koji će vrhovima tipa zaposlenik dodati novo svojstvo, ime_prezime, a koje će
predstavljati ime i prezime zaposlenika te ukloniti dosadašnja svojstva ime i prezime s tih vrhova
```sql
MATCH (z:Zaposlenik) 
             SET z.ime_prezime = z.ime + " "+z.prezime,
                    z.ime=NULL,
                    z.prezime=NULL;
```

Napisati upit koji će napraviti top listu zaposlenika po broju obrađenih narudžbi (na početku liste su
oni koji su obradili najviše narudžbi)

```sql
MATCH (a:Zaposlenik)-[:OBRADIO]->(b:Narudzba) 
              RETURN a.ime_prezime , 
                             COUNT(b) as cnt 
              ORDER BY cnt DESC;
```




```
╒══════════════════╤═════╕
│"a.ime_prezime"   │"cnt"│
╞══════════════════╪═════╡
│"Margaret Peacock"│156  │
├──────────────────┼─────┤
│"Janet Leverling" │127  │
├──────────────────┼─────┤
│"Nancy Davolio"   │123  │
├──────────────────┼─────┤
│"Laura Callahan"  │104  │
├──────────────────┼─────┤
│"Andrew Fuller"   │96   │
├──────────────────┼─────┤
│"Robert King"     │72   │
├──────────────────┼─────┤
│"Michael Suyama"  │67   │
├──────────────────┼─────┤
│"Anne Dodsworth"  │43   │
├──────────────────┼─────┤
│"Steven Buchanan" │42   │
└──────────────────┴─────┘
```

Napisati upit koji će napraviti top listu zaposlenika po prihodu ostvarenom prodajom mliječnih
proizvoda (prihod je suma umnoška jediničnih cijena i količine naručenih proizvoda u narudžbi,
umanjen za popust).
Napomena: atributi jedinicna_cijena i popust su uvezeni kao String, pa nad njima u izračunu treba
iskoristiti funkciju toFloat.
```sql
MATCH (z:Zaposlenik)-[:OBRADIO]-(n:Narudzba)-[s:SADRZI]-(p:Proizvod)-[:PRIPADA]-(k:Kategorija {kategorija_naziv:"Mliječni pr."}) 
RETURN z.ime_prezime,
              SUM(
                   toFloat(s.jedinicna_cijena) *
                   toFloat(s.kolicina) *
                    (1-toFloat(s.popust))
             ) as sum 
ORDER BY sum DESC;
```
```
╒══════════════════╤══════════════════╕
│"z.ime_prezime"   │"sum"             │
╞══════════════════╪══════════════════╡
│"Nancy Davolio"   │36022.979999999996│
├──────────────────┼──────────────────┤
│"Margaret Peacock"│33549.8           │
├──────────────────┼──────────────────┤
│"Janet Leverling" │32320.835         │
├──────────────────┼──────────────────┤
│"Robert King"     │27621.859999999997│
├──────────────────┼──────────────────┤
│"Andrew Fuller"   │23812.550000000003│
├──────────────────┼──────────────────┤
│"Steven Buchanan" │21937.625         │
├──────────────────┼──────────────────┤
│"Laura Callahan"  │21101.47          │
├──────────────────┼──────────────────┤
│"Anne Dodsworth"  │21101.125         │
├──────────────────┼──────────────────┤
│"Michael Suyama"  │17039.039999999997│
└──────────────────┴──────────────────┘
```

```sql
MATCH
(k1:Kategorija)-[:PRIPADA]-(p1:Proizvod)-[:SADRZI]-(:Narudzba)-[:NARUCIO]-(kup:Kupac), (kup:Kupac)-[:NARUCIO]-(:Narudzba)-[:SADRZI]-(:Proizvod)-[:PRIPADA]-(k2:Kategorija {kategorija_naziv:"Začini"}) 
RETURN k1.kategorija_naziv,COUNT(p1);
```
```
╒═════════════════════╤═══════════╕
│"k1.kategorija_naziv"│"COUNT(p1)"│
╞═════════════════════╪═══════════╡
│"Pića"               │1468       │
├─────────────────────┼───────────┤
│"Začini"             │726        │
├─────────────────────┼───────────┤
│"Slatkiši"           │1146       │
├─────────────────────┼───────────┤
│"Mliječni pr."       │1353       │
├─────────────────────┼───────────┤
│"Žitarice"           │681        │
├─────────────────────┼───────────┤
│"Mesni proizvodi"    │636        │
├─────────────────────┼───────────┤
│"Voće i povrće"      │473        │
├─────────────────────┼───────────┤
│"Morska hrana"       │1131       │
└─────────────────────┴───────────┘
```
5.
```sql
MATCH (z:Zaposlenik)-[:OBRADIO]-(n:Narudzba)-[s:SADRZI]-(p:Proizvod)-[:PRIPADA]-(k:Kategorija {kategorija_naziv:"Pića"}) ,
(rk:Zaposlenik {ime_prezime:"Robert King"})-[:OBRADIO]-(n2:Narudzba)-[s2:SADRZI]-(p2:Proizvod)-[:PRIPADA]-(k2:Kategorija {kategorija_naziv:"Pića"})

RETURN z.ime_prezime,
              ROUND(SUM(
                   toFloat(s.jedinicna_cijena) *
                   toFloat(s.kolicina) *
                    (1-toFloat(s.popust))
             ) -
              SUM(
                   toFloat(s2.jedinicna_cijena) *
                   toFloat(s2.kolicina) *
                    (1-toFloat(s2.popust))
             ) ) as diff 
ORDER BY diff DESC;
```
```
╒══════════════════╤═════════╕
│"z.ime_prezime"   │"diff"   │
╞══════════════════╪═════════╡
│"Andrew Fuller"   │203776.0 │
├──────────────────┼─────────┤
│"Anne Dodsworth"  │59982.0  │
├──────────────────┼─────────┤
│"Robert King"     │0.0      │
├──────────────────┼─────────┤
│"Nancy Davolio"   │-8761.0  │
├──────────────────┼─────────┤
│"Janet Leverling" │-112747.0│
├──────────────────┼─────────┤
│"Steven Buchanan" │-183632.0│
├──────────────────┼─────────┤
│"Margaret Peacock"│-236167.0│
├──────────────────┼─────────┤
│"Michael Suyama"  │-471525.0│
├──────────────────┼─────────┤
│"Laura Callahan"  │-788803.0│
└──────────────────┴─────────┘
```