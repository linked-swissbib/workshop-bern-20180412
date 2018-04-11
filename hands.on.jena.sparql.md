1 lokale Installation der Spielumgebung 
Kommandos für Linux /oder Mac (auf Windows leicht anders - hier nicht beschrieben)


- Download der Umgebung:
http://www.swissbib.org/doc/wsbern20180412/jena.sparql.bern.20180412.tar.gz

- Entpacke das File:
tar xfz jena.sparql.bern.20180412.tar.gz

- Wechsle in das neue Verzeichnis
cd jena.sparql.bern.20180412

- Starte den server
./fuseki start

- Aufruf des servers im Browser
http://localhost:3030

- wähle Menuepunkt "manage datasets" aus

- es sind noch keine data sets vorhanden, wähle "add new one" aus

- wähle "persistent"

- vergebe einen dataset Namen (in unserem Beispiel swissbib)

- wähle upload data

- wähle "select files"

- in dem erstellten Verzeichnis (ausgepackte Daten) gibt es ein Unterverzeichnis testdata

- wähle dort die Datei "swissbib_2018-04-11_16-04-18.nq" aus un im Menue den Befehl "upload now"

- dadurch werden rund 20.000 Triple aus dem swissbib Datenset in den Sparql server geladen

- jetzt kann der Tab "queries" für Abfragen gewählt werden

--------------------------------------------------------------------------

2 Sparql Abfragen zum Ausprobieren

- Suche nach triples deren Objekt den Wert orange enthält 
SELECT ?subject ?predicate ?object
   WHERE {
     ?subject ?predicate ?object.
     FILTER regex(?object, "orange", "i")
   }


-----------------------------------------------------------------------------
- Gib mir alle Triple (Fakten) des Typs bibliographicResource mit der ID 19321489X 

PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
Prefix linkedbib: <http://data.swissbib.ch/bibliographicResource/>

SELECT ?subject ?predicate ?object
WHERE {
linkedbib:19321489X ?predicate ?object
 
}
LIMIT 1000

alternative Schreibweise (ohne Prefix)

PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?subject ?predicate ?object
WHERE {
<http://data.swissbib.ch/bibliographicResource/19321489X> ?predicate ?object
 
}
LIMIT 1000


--------------------------------------------------------------------

- Zeige mir alle bibliographischen Aufnahmen die eine Verbindug zu mehr als 5 Autoren haben

SELECT ?s 

where {
  ?s <http://purl.org/dc/terms/contributor> ?contr  .
}
group by ?s 
HAVING (count(?contr) > 5)
limit 100


------------------------------------------------------------------------
- sog. union Abfragen

SELECT ?subject ?predicate ?object
WHERE {
  {
    ?subject ?predicate ?object.
  FILTER regex(?object, "orange", "i")
  }
union 
{
     ?subject ?predicate ?object.
    filter regex ($object,"zürich", "i")    
    filter regex ($object,"Trifolium", "i")
    
}
}


-------------------------------------------------------------------------

- Zeige mir alle im Testdatenset vorkommenden Autoren

prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
prefix foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?contr ?firstName ?lastName

where {
  ?s <http://purl.org/dc/terms/contributor> ?contr  .
  ?contr foaf:firstName  ?firstName .
  ?contr foaf:lastName  ?lastName .
} ORDER BY asc(?lastName)

