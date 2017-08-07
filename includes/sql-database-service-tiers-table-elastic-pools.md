<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->
 
### <a name="basic-elastic-pool-limits"></a>Limites do conjunto elástico básico

| Tamanho do conjunto (eDTUs)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento de dados máximo por conjunto* | 5 GB | 10 GB | 20 GB | 29 GB | 39 GB | 78 GB | 117 GB | 156 GB |
| Armazenamento OLTP máximo em memória por conjunto | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Número de DBs máximo por conjunto | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de inícios de sessão simultâneos por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por base de dados | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Máximo de eDTUs por base de dados | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Armazenamento de dados máximo por base de dados | 2GB | 2GB | 2GB | 2GB | 2GB | 2GB | 2GB | 2GB | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Limites do conjunto elástico padrão

| Tamanho do conjunto (eDTUs)  | **50** | **100** | **200**** | **300**** | **400**** | **800****| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Armazenamento de dados máximo por conjunto* | 50 GB| 100 GB| 200 GB | 300 GB| 400 GB | 800 GB | 
| Armazenamento OLTP máximo em memória por conjunto | N/D | N/D | N/D | N/D | N/D | N/D | 
| Número de DBs máximo por conjunto | 100 | 200 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 100 | 200 | 400 | 600 |  800 | 1600 |
| Máximo de inícios de sessão simultâneos por conjunto | 100 | 200 | 400 | 600 |  800 | 1600 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por base de dados** | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Máximo de eDTUs por base de dados** | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Armazenamento de dados máximo por base de dados | 50 GB | 100 GB | 200 GB | 250 GB | 250 GB | 250 GB |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limites do conjunto elástico standard (continuação) 

| Tamanho do conjunto (eDTUs)  |  **1200**** | **1600**** | **2000**** | **2500**** | **3000**** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento de dados máximo por conjunto* | 1.2 TB | 1.6 TB | 2 TB | 2.4 TB | 2,9 TB | 
| Armazenamento OLTP máximo em memória por conjunto | N/D | N/D | N/D | N/D | N/D | 
| Número de DBs máximo por conjunto | 500 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhadores simultâneos (pedidos) por conjunto |  2400 | 3200 | 4000 | 5000 | 6000 |
| Máximo de inícios de sessão simultâneos por conjunto |  2400 | 3200 | 4000 | 5000 | 6000 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por base de dados** | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Máximo de eDTUs por base de dados** | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Armazenamento de dados máximo por base de dados | 250 GB | 250 GB | 250 GB | 250 GB | 250 GB | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limites do conjunto elástico premium

| Tamanho do conjunto (eDTUs)  | **125** | **250** | **500** | **1000** | **1500*****| 
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento de dados máximo por conjunto* | 250 GB | 500 GB | 750 GB | 1 TB | 1,5 TB | 
| Armazenamento OLTP máximo em memória por conjunto | 1 GB| 2GB| 4GB| 10 GB| 12 GB| 
| Número de DBs máximo por conjunto | 50 | 100 | 100 | 100 | 100 |  
| Máximo de trabalhadores simultâneos por conjunto (pedidos) | 200 | 400 | 800 | 1600 |  2400 | 
| Máximo de inícios de sessão simultâneos por conjunto | 200 | 400 | 800 | 1600 |  2400 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por base de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000 | 
| Máximo de eDTUs por base de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000 |
| Armazenamento de dados máximo por base de dados | 250 GB | 500 GB | 500 GB | 500 GB | 500 GB | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limites do conjunto elástico premium (continuação) 

| Tamanho do conjunto (eDTUs) | **2000***** | **2500***** | **3000***** | **3500***** | **4000*****|
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento de dados máximo por conjunto* | 2 TB | 2,5 TB | 3 TB | 3,5 TB | 4 TB |
| Armazenamento OLTP máximo em memória por conjunto | 16 GB | 20 GB | 24 GB | 28 GB | 32 GB |
| Número de DBs máximo por conjunto | 100 | 100 | 100 | 100 | 100 | 
| Máximo de trabalhadores simultâneos (pedidos) por conjunto |  3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de inícios de sessão simultâneos por conjunto |  3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por base de dados | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 |  0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Máximo de eDTUs por base de dados | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Armazenamento de dados máximo por base de dados | 500 GB | 500 GB | 500 GB | 500 GB | 500 GB | 
||||||||

### <a name="premium-rs-elastic-pool-limits"></a>Limites do conjunto elástico Premium RS

| Tamanho do conjunto (eDTUs)  | **125** | **250** | **500** | **1000** |
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento de dados máximo por conjunto* | 250 GB| 500 GB | 750 GB | 750 GB |
| Armazenamento OLTP máximo em memória por conjunto | 1 GB | 2GB | 4GB | 10 GB |
| Número de DBs máximo por conjunto | 50 | 100 | 100 | 100 |
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 200 | 400 | 800 | 1600 |
| Máximo de inícios de sessão simultâneos por conjunto | 200 | 400 | 800 | 1600 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por base de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 |
| Máximo de eDTUs por base de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 
| Armazenamento de dados máximo por base de dados | 250 GB | 500 GB | 500 GB | 500 GB | 
||||||||

> [!IMPORTANT]
>\* As bases de dados agrupadas partilham o armazenamento agrupado, pelo o que o armazenamento de dados num conjunto elástico é limitado ao armazenamento agrupado mais pequeno restante ou armazenamento máximo por base de dados. 
>
>
>\*\* O valor mínimo/máximo de eDTUs por base de dados a partir de 200 eDTUs e superior está em pré-visualização pública.
>
>\*\*\* O armazenamento de dados máximo predefinido por conjunto para conjuntos Premium com 500 eDTUs ou mais é de 750 GB. Para obter o tamanho de armazenamento de dados máximo mais elevado por conjunto Premium para 1000 ou mais eDTUs, este tamanho tem de ser selecionado explicitamente no portal do Azure, no [PowerShell](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-powershell), na [CLI do Azure](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-the-azure-cli) ou na [API REST](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-the-rest-api). Os conjuntos Premium com mais de 1 TB de armazenamento estão atualmente em pré-visualização pública nas seguintes regiões: E.U.A. Leste 2, E.U.A. Oeste, Gov (US) - Virginia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. O armazenamento máximo por conjunto para todas as outras regiões está limitado a 750 GB.
>
