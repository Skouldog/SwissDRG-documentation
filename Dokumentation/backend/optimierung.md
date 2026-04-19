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


| Methodik       | Zeit Max | Zeit Min | Zeit (1 Run) | Zeit Avg (6 Runs) | Read Avg |
|:---------------|:---------|:---------|:-------------|:------------------|:---------|
| **Normal**     | 14.834s  | 7.369s   | 11.961s      | 11.200s           | 4.862s   |
| **Duck**       | 28.868s  | 19.750s  | 28.868s      | 22.946s           | 16.753s  |
| **Parquet**    | 7.015s   | 5.275s   | 5.662s       | 6.151s            | 0.217s   |

Hier sind die Read Avg am interesanntesten, da die Gesamt Zeit stark vom DLS abhängt. 
Die DuckDB + Parquet Files, sind beim lesen der CSV und finden der Patiente somit x25 schneller als der Naive CSV Ruby Ansatz


---



### Download CSV
> Hier geht es um die FUnktion die CSV als Download bereit zustellen. Dies beinhaltet auch die Read Funktion. 


| Methodik       | Zeit Max | Zeit Min | Zeit (1 Run) | Zeit Avg (3 Runs) |
|:---------------|:---------|:---------|:-------------|:------------------|
| **Normal**     | 181.134s | 159.552s | 159.552s     | 169.176s          |
| **Duck**       | 20.797s  | 9.931s   | 20.797s      | 16.072s           |
| **Parquet**    | 5.139s   | 2.464s   | 5.139s       | 4.049s            |

Hier zeigt DuckDB + Parquet eine Leistungssteigerung von ~x40 zum normalen Download. 



---


# Memory

Nachdem wir grobe Memory Test durchgeführt haben, habe wir uns entschlossen, diese nicht tiefer zu verfolgen. 
Hier sind die Daten, aber der RAM Verbrauch ist so gering, das wir nicht tiefer analysieren müssen.  
Anmerkung: Der Naive Ansatz( CSV mit Ruby Zeile für Zeile durchzugehen), beansprucht so viel RAM, das man mit MemProfiler keine Messung mehr durchführen kann. 
Was nicht nötig ist, da dies beweist das der Naive Ansatz unzuverlässig ist für langfristigen Gebrauch

|               | Parquet (10) | Parquet (50) | Parquet (200) | Duck (10) | Duck (50) | Duck (200) |
|:--------------|:-------------|:-------------|:--------------|:----------|:----------|:-----------|
| **allocated** | 2.79MB       | 2.83MB       | 2.95MB        | 2.77MB    | 2.81MB    | 2.95MB     |
| **retained**  | 0.51MB       | 0.51MB       | 0.51MB        | 0.50MB    | 0.50MB    | 0.50MB     |

Erkenntnis: DuckDB Methoden scheint weniger Speicher zu benötigen, aber der minimale Speicher Gewinn rechtfertigt nicht den x4 performance Verlust

# CPU Auslastung

