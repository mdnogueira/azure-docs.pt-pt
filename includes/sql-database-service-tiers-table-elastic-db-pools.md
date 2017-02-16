<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->

### <a name="basic-elastic-pool-limits"></a>Limites do conjunto elástico básico

| Tamanho do conjunto (eDTUs)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento máximo por conjunto* | 5 GB| 10 GB| 20 GB| 29 GB| 39 GB| 78 GB| 117 GB| 156 GB|
| Número de DBs máximo por conjunto | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhadores simultâneos por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de inícios de sessão simultâneos por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por base de dados | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} |
| Máximo de eDTUs por base de dados | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} |
||||||||

### <a name="standard-elastic-pool-limits"></a>Limites do conjunto elástico padrão

| Tamanho do conjunto (eDTUs)  | **50** | **100** | **200** | **300** | **400** | **800** | 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Armazenamento máximo por conjunto* | 50 GB| 100 GB| 200 GB | 300 GB| 400 GB | 800 GB | 
| Número de DBs máximo por conjunto | 100 | 200 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhadores simultâneos por conjunto | 100 | 200 | 400 | 600 |  800 | 1600 |
| Máximo de inícios de sessão simultâneos por conjunto | 100 | 200 | 400 | 600 |  800 | 1600 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por base de dados | {0,10,20,<br>50} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| Máximo de eDTUs por base de dados | {10,20,<br>50} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limites do conjunto elástico standard (continuação) 

| Tamanho do conjunto (eDTUs)  |  **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento máximo por conjunto* | 1.2 TB | 1.6 TB | 2 TB | 2.4 TB | 2,9 TB | 
| Número de DBs máximo por conjunto | 500 | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhadores simultâneos por conjunto |  2400 | 3200 | 4000 | 5000 | 6000 |
| Máximo de inícios de sessão simultâneos por conjunto |  2400 | 3200 | 4000 | 5000 | 6000 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por base de dados | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| Máximo de eDTUs por base de dados | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limites do conjunto elástico premium

| Tamanho do conjunto (eDTUs)  | **125** | **250** | **500** | **1000** | **1500** | 
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento máximo por conjunto* | 250 GB| 500 GB| 750 GB| 750 GB| 750 GB| 
| Número de DBs máximo por conjunto | 50 | 100 | 100 | 100 | 100 |  
| Máximo de trabalhadores simultâneos por conjunto | 200 | 400 | 800 | 1600 |  2400 | 
| Máximo de inícios de sessão simultâneos por conjunto | 200 | 400 | 800 | 1600 |  2400 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por base de dados | {0,25,50,75,<br>125} | {0,25,50,75,<br>125,250} | {0,25,50,75,<br>125,250,500} | {0,25,50,75,<br>125,250,500,<br>1000} | {0,25,50,75,<br>125,250,500,<br>1000,1500} | 
| Máximo de eDTUs por base de dados | {25,50,75,<br>125} | {25,50,75,<br>125,250} | {25,50,75,<br>125,250,500} | {25,50,75,<br>125,250,500,<br>1000} | {25,50,75,<br>125,250,500,<br>1000,1500} |  
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limites do conjunto elástico premium (continuação) 

| Tamanho do conjunto (eDTUs)  |  **2000** | **2500** | **3000** | **3500** | **4000** |
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento máximo por conjunto* | 750 GB | 750 GB | 750 GB | 750 GB | 750 GB |
| Número de DBs máximo por conjunto | 100 | 100 | 100 | 100 | 100 | 
| Máximo de trabalhadores simultâneos por conjunto |  3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de inícios de sessão simultâneos por conjunto |  3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por base de dados | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} |  {0,25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
| Máximo de eDTUs por base de dados | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
||||||||

\* A base de dados elástica partilha o armazenamento do conjunto, pelo que o armazenamento da base de dados é limitado ao armazenamento mais pequeno do conjunto restante ou ao armazenamento máximo por base de dados



<!--HONumber=Dec16_HO3-->


