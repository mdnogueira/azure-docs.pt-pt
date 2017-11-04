
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
Para reduzir os custos, pode colocar em pausa e retomar a computação recursos a pedido. Por exemplo, se não utilizar a base de dados durante a noite e no fim de semana, pode colocar em pausa-lo durante essas horas e retomá-lo durante o dia. Não lhe será cobrado dwus enquanto a base de dados está em pausa.

Quando colocar em pausa uma base de dados:

* Recursos de computação e memória são devolvidos para o agrupamento de recursos disponíveis no Centro de dados
* Os custos DWU são zero para a duração de pausa.
* Armazenamento de dados não é afetado e os dados permanecem intactos. 
* O SQL Data Warehouse cancela todas as operações em execução ou em fila.

