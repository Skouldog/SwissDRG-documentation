# Optimierung und Analyse Backend


# Zeit
## Methoden

Es wurden mehrere Optimierungen durchgeführt. 

Der Naive Ansatz: Ruby liest die CSV/Egins Datei Zeile für Zeile und sucht nach Ergebnissen. 
DuckDB: Der Gem DuckDB liest die CSV. Duck ist dafür optimiert
DuckDB + Parquet: Parquet sind spezielle Datei Fromate für Duck wodurch es wie eine DB funktioniert


## Analyse

Als erstes wurden die Methoden Download/Read CSV umgeschrieben und verglichen

### Read CSV 
> Hier geht es um die End to End Zeit, von der Anfrage bis die Zeilen zurückgegen werden. Wichtig die größte Zeit beansprucht der DLS. 
> Zeiten zum Rubyoverhead sind ersichtlicher in der Pagination Analyse

| Methodik           | Zeit Max | Zeit Min | Zeit Test Run | Zeit Avg 10x Runs |
|:-------------------|:---------|:---------|:--------------|:------------------|
| **Naiv**           | 7000ms   | 6500ms   | 6888ms        | 6750ms            |
| **Duck**           | 4000ms   | 3500ms   | 3888ms        | 3999ms            |
| **Duck + Parquet** | 3000ms   | 2500ms   | 2888ms        | 2900ms            |



---



### Download CSV
> Hier geht es um die FUnktion die CSV als Download bereit zustellen. Dies beinhaltet auch die Read Funktion. 

| Methodik           | Zeit Max | Zeit Min | Zeit Test Run | Zeit Avg 10x Runs |
|:-------------------|:---------|:---------|:--------------|:------------------|
| **Naiv**           | 7000ms   | 6500ms   | 6888ms        | 6750ms            |
| **Duck**           | 4000ms   | 3500ms   | 3888ms        | 3999ms            |
| **Duck + Parquet** | 3000ms   | 2500ms   | 2888ms        | 2900ms            |



---

### Pagination 
> Da die Pagination lediglich eine normale Anfrage auf den Cache ist, ist es einfach ein End to End test ohne DLS Anfrage


| Methodik           | Zeit Max | Zeit Min | Zeit Test Run | Zeit Avg 10x Runs |
|:-------------------|:---------|:---------|:--------------|:------------------|
| **Naiv**           | 7000ms   | 6500ms   | 6888ms        | 6750ms            |
| **Duck**           | 4000ms   | 3500ms   | 3888ms        | 3999ms            |
| **Duck + Parquet** | 3000ms   | 2500ms   | 2888ms        | 2900ms            |

Hier waren wir noch ein wenig unzufrieden, da zwar bei normalen Filtern hundert ms keinen grossen Unterscheid machen, aber bei der Pagination schon
Deswegen wurden weiter Optimierungen durchgeführt. Namentlich wurden die DB hits durch Eager-Loading und interenes returnen von Cache-Objekten.

| Methodik                              | Zeit Max | Zeit Min | Zeit Test Run | Zeit Avg 10x Runs |
|:--------------------------------------|:---------|:---------|:--------------|:------------------|
| **Duck + Parquet**                    | 7000ms   | 6500ms   | 6888ms        | 6750ms            |
| **Duck + Parquet + Code Optimierung** | 3000ms   | 2500ms   | 2888ms        | 2900ms            |


---


# Memory

Nachdem wir grobe Memory Test durchgeführt haben, habe wir uns entschlossen, diese nicht tiefer zu verfolgen. 
Hier sind die Daten, aber der RAM Verbrauch ist so gering, das wir nicht tiefer analysieren müssen 

RAM Allocated 2 MB
RAM Leftover 0.5MB


# CPU Auslastung