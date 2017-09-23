
>[!NOTE]
>O Log Analytics era anteriormente conhecido como Operational Insights.
>
>

Os seguintes limites aplicam-se aos recursos do Log Analytics por subscrição:

| Recurso | Limite Predefinido | Comentários
| --- | --- | --- |
| Número de áreas de trabalho gratuitas por subscrição | 10 | Não pode aumentar este limite. |
| Número de áreas de trabalho pagas por subscrição | N/D | Está limitado pelo número de recursos dentro de um grupo de recursos e pelo número de grupos de recursos por subscrição | 


Os limites seguintes aplicam-se a cada área de trabalho do Log Analytics:

|  | Gratuito | Standard | Premium | Autónomo | OMS |
| --- | --- | --- | --- | --- | --- |
| Volume de dados recolhido por dia |500 MB<sup>1</sup> |Nenhuma |Nenhuma | Nenhuma | Nenhuma
| Período de retenção de dados |7 dias |1 mês |12 meses | 1 mês<sup>2</sup> | 1 mês <sup>2</sup>|

<sup>1</sup> Quando os clientes atingem o limite diário de transferência de dados de 500 MB, a análise de dados é parada e retomada no início do dia seguinte. Os dias são baseados no fuso horário UTC.

<sup>2</sup> O período de retenção de dados dos planos de preços Autónomo e OMS pode ser aumentado para 730 dias.

| Categoria | Limites | Comentários
| --- | --- | --- |
| API do Recoletor de Dados | O tamanho máximo para um post individual é 30 MB<br>O tamanho máximo para os valores de campos é 32 KB | Dividir volumes maiores em vários posts<br>Os campos com mais de 32 KB são truncados. |
| API de Pesquisa | 5000 registos devolvidos para dados não agregados<br>500&000; registos para dados agregados | Os dados agregados são uma pesquisa que inclui o comando `measure`
 
