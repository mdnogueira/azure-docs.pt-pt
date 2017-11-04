<!--
Used in:
sql-database-elastic-pool.md 
-->

 
### <a name="basic-elastic-pool-limits"></a>Limites do conjunto elástico básico

| eDTUs por conjunto | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento incluído por conjunto (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Opções de armazenamento máximo por conjunto (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Armazenamento máximo de memória OLTP por conjunto (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Número de DBs máximo por conjunto | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de inícios de sessão simultâneos por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Opções de eDTUs Mín por base de dados | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Opções de eDTUs máx. por base de dados | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Armazenamento máximo por base de dados (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Limites do conjunto elástico padrão

| eDTUs por conjunto | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Armazenamento incluído por conjunto (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Opções de armazenamento máximo por conjunto (GB) * | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Armazenamento máximo de memória OLTP por conjunto (GB) | N/D | N/D | N/D | N/D | N/D | N/D | 
| Número de DBs máximo por conjunto | 100 | 200 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 |
| Máximo de inícios de sessão simultâneos por conjunto | 100 | 200 | 400 | 600 | 800 | 1600 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opções de eDTUs Mín por base de dados * * | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Opções de eDTUs máx. por base de dados * * | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Armazenamento máximo por base de dados (GB)* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limites do conjunto elástico standard (continuação) 

| eDTUs por conjunto | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por conjunto (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Opções de armazenamento máximo por conjunto (GB) * | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Armazenamento máximo de memória OLTP por conjunto (GB) | N/D | N/D | N/D | N/D | N/D | 
| Número de DBs máximo por conjunto | 500 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 2400 | 3200 | 4000 | 5000 | 6000 |
| Máximo de inícios de sessão simultâneos por conjunto | 2400 | 3200 | 4000 | 5000 | 6000 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Opções de eDTUs Mín por base de dados * * | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opções de eDTUs máx. por base de dados * * | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Opções de armazenamento máximo por base de dados (GB) * | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limites do conjunto elástico premium

| eDTUs por conjunto | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento incluído por conjunto (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Opções de armazenamento máximo por conjunto (GB) * | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Armazenamento máximo de memória OLTP por conjunto (GB) | 1 | 2 | 4 | 10 | 12 | 
| Número de DBs máximo por conjunto | 50 | 100 | 100 | 100 | 100 | 
| Máximo de trabalhadores simultâneos por conjunto (pedidos) | 200 | 400 | 800 | 1600 | 2400 | 
| Máximo de inícios de sessão simultâneos por conjunto | 200 | 400 | 800 | 1600 | 2400 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por base de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Máximo de eDTUs por base de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Armazenamento máximo por base de dados (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limites do conjunto elástico premium (continuação) 

| eDTUs por conjunto | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento incluído por conjunto (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Opções de armazenamento máximo por conjunto (GB) * | 2048 | 2560 | 3072 | 3548 | 4096|
| Armazenamento máximo de memória OLTP por conjunto (GB) | 16 | 20 | 24 | 28 | 32 |
| Número de DBs máximo por conjunto | 100 | 100 | 100 | 100 | 100 | 
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de inícios de sessão simultâneos por conjunto | 3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Opções de eDTUs Mín por base de dados | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Opções de eDTUs máx. por base de dados | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Armazenamento máximo por base de dados (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-rs-elastic-pool-limits"></a>Limites do conjunto elástico Premium RS

| eDTUs por conjunto | **125** | **250** | **500** | **1000** |
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento incluído por conjunto (GB) | 250 | 500 | 750 | 750 |
| Opções de armazenamento máximo por conjunto (GB) * | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 
| Armazenamento máximo de memória OLTP por conjunto (GB) | 1 | 2 | 4 | 10 |
| Número de DBs máximo por conjunto | 50 | 100 | 100 | 100 |
| Máximo de trabalhadores simultâneos (pedidos) por conjunto | 200 | 400 | 800 | 1600 |
| Máximo de inícios de sessão simultâneos por conjunto | 200 | 400 | 800 | 1600 |
| Máximo de sessões simultâneas por conjunto | 30000 | 30000 | 30000 | 30000 |
| Opções de eDTUs Mín por base de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 |
| Opções de eDTUs máx. por base de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 
| Armazenamento máximo por base de dados (GB)* | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> \*Tamanhos de armazenamento maiores do que a quantidade de armazenamento incluído estão em pré-visualização e são aplicados custos adicionais. Para obter mais informações, consulte o [base de dados SQL página de preços](https://azure.microsoft.com/pricing/details/sql-database/). Tamanhos de armazenamento maiores do que a quantidade de armazenamento incluído estão em pré-visualização e aplicam custos adicionais. Para obter mais informações, consulte o [base de dados SQL página de preços](https://azure.microsoft.com/pricing/details/sql-database/).
>
> \* No escalão Premium, mais de 1 TB de armazenamento estão atualmente disponível nas seguintes regiões: E.U.A. Leste 2, E.U.A. Oeste, Gov (US) - Virginia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. 
>
>\*\*Número mínimo/máximo de eDTUs por base de dados a partir de 200 eDTUs e superior **padrão** agrupamentos estão em pré-visualização.
>
