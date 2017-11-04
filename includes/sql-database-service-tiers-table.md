<!--
Used in:
sql-database-performance-guidance.md 
sql-database-single-database-resources.md 
-->

### <a name="basic-service-tier"></a>Camada de serviços básicos
| **Nível de desempenho** | **Básica** |
| :--- | --: |
| Máximo de DTUs | 5 |
| Incluído armazenamento (GB) | 2 |
| Opções de armazenamento máximo (GB) | 2 |
| Armazenamento em OLTP na memória máx. (GB) |N/D |
| Funcionários em simultâneo de máx. (pedidos) | 30 |
| Inícios de sessão simultâneos máx. | 30 |
| Sessões simultâneas máx. | 300 |
|||

### <a name="standard-service-tier"></a>Camada de serviços padrão
| **Nível de desempenho** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Máx. DTUs * * | 10 | 20 | 50 | 100 |
| Incluído armazenamento (GB) | 250 | 250 | 250 | 250 |
| Opções de armazenamento máximo (GB) * | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/D | N/D | N/D | N/D |
| Funcionários em simultâneo de máx. (pedidos)| 60 | 90 | 120 | 200 |
| Inícios de sessão simultâneos máx. | 60 | 90 | 120 | 200 |
| Sessões simultâneas máx. |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Camada de serviço Standard (continuação)
| **Nível de desempenho** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Máx. DTUs * * | 200 | 400 | 800 | 1600 | 3000 |
| Incluído armazenamento (GB) | 250 | 250 | 250 | 250 | 250 |
| Opções de armazenamento máximo (GB) * | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/D | N/D | N/D | N/D |N/D |
| Funcionários em simultâneo de máx. (pedidos)| 400 | 800 | 1600 | 3200 |6000 |
| Inícios de sessão simultâneos máx. | 400 | 800 | 1600 | 3200 |6000 |
| Sessões simultâneas máx. |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Camada de serviços premium 
| **Nível de desempenho** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Máximo de DTUs | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Incluído armazenamento (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Opções de armazenamento máximo (GB) * | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Armazenamento em OLTP na memória máx. (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Funcionários em simultâneo de máx. (pedidos)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Inícios de sessão simultâneos máx. | 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Sessões simultâneas máx. | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

### <a name="premium-rs-service-tier"></a>Camada de serviço RS Premium 
| **Nível de desempenho** | **PRS1** | **PRS2** | **PRS4** | **PRS6** |
| :--- |---:|---:|---:|---:|---:|---:|
| Máximo de DTUs | 125 | 250 | 500 | 1000 |
| Incluído armazenamento (GB) | 500 | 500 | 500 | 500 |
| Opções de armazenamento máximo (GB) * | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | 1 | 2 | 4 | 8 |
| Funcionários em simultâneo de máx. (pedidos)| 200 | 400 | 800 | 1600 |
| Inícios de sessão simultâneos máx. | 200 | 400 | 800 | 1600 |
| Sessões simultâneas máx. | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> \*Tamanhos de armazenamento maiores do que a quantidade de armazenamento incluído estão em pré-visualização e são aplicados custos adicionais. Para detalhes, ver os preços da [Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
>
>\* No escalão Premium, mais de 1 TB de armazenamento estão atualmente disponível nas seguintes regiões: E.U.A. Leste 2, E.U.A. Oeste, Gov (US) - Virginia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. Ver [Limitações Atuais P11-P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
>\*\*Número máximo de DTUs por base de dados a partir de 200 DTUs e superior padrão estão na pré-visualização.
>
