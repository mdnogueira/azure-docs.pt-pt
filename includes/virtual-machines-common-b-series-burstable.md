
> [!NOTE] 
> Pré-visualizações ficam disponíveis para si condition que aceita os termos de utilização. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Esta pré-visualização está limitada às seguintes regiões:
> - -NOS - oeste 2
> - -NOS - leste
> - Europa - Oeste
> - Ásia-Pacífico - Southeast


A família VM de série B permite-lhe escolher o tamanho da VM que fornece-lhe o desempenho de nível de base necessário para a carga de trabalho, a capacidade de impulsar desempenho da CPU de até 100% de um v4 Intel® Broadwell E5-2673 2.3 GHz ou um processador de v3 Intel® Haswell 2.4 GHz E5-2673 vCPU.

As VMs de série B são ideais para cargas de trabalho que não tem o desempenho total da CPU continuamente, como servidores web, desenvolvimento de bases de dados pequenas e ambientes de teste. Estas cargas de trabalho normalmente têm requisitos de desempenho burstable. A série B fornece-lhe a possibilidade de comprar um tamanho VM com o desempenho da linha de base e a instância VM cria créditos quando está a utilizar menor do que a linha de base. Quando a VM terem sido acumulados crédito, a VM pode impulsar acima a linha de base com até 100% do vCPU, quando a aplicação requer um desempenho superior da CPU.

A série B é apresentada nos tamanhos VM de seis seguintes:

| Tamanho          | do vCPU | Memória: GiB | SSD Local: GiB | Base de CPU de desempenho da VM | Desempenho de CPU máx. de VM | Créditos Banked / hora | Os créditos de Banked máx. |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10%                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20%                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a-about-this-preview"></a>As perguntas e respostas sobre esta pré-visualização

### <a name="q-how-can-i-participate-in-this-preview"></a>P: como podem participar nesta pré-visualização?
**A**: pedido de quota para a série de B das regiões suportadas.  Após a sua quota tiver sido aprovada, em seguida, pode utilizar o portal ou as APIs para efetuar a implementação como normalmente seriam. Para obter mais informações, consulte [aumentar a quota de núcleos de Gestor de recursos pedidos](../articles/azure-supportability/resource-manager-core-quotas-request.md).

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: como é obter o desempenho da linha de base de 135% de uma VM?
**A**: 135% é partilhado entre a 8 vCPU que compõem o tamanho da VM. Por exemplo, se 4 os 8 núcleos de trabalhar no processamento em lote tira partido da sua aplicação e cada um desses 4 vCPU são executadas em 30% de utilização a quantidade total de desempenho de VM CPU seria igual a % de 120.  O que significa que a VM iria criar tempo de crédito com base no delta de 15% do desempenho da sua linha de base.  Mas também significa que se tiver disponíveis que mesma VM pode utilizar a 100% de vCPU 8 todos os créditos da fornecer essa VM de desempenho de CPU máx. de 800%.

### <a name="q-is-there-a-discount-on-price-during-the-preview"></a>P: existe um desconto no preço durante a pré-visualização?
**A**: Sim, os preços da pré-visualização podem ser visualizados no nosso [página de preços](http://aka.ms/vmsizes).

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: como posso monitorizar as minhas saldo do crédito e consumo
**A**: podemos irá introduzir 2 nova métrica nas próximas semanas, o **crédito** métrica irá permitir-lhe ver quantas créditos a VM tem banked e o **ConsumedCredit** métrica mostra quantos Créditos de CPU VM consumiu do banco.    Poderá ver estas métricas no painel de métricas no portal ou de forma programática através das APIs do Monitor do Azure.

Para obter mais informações sobre como aceder aos dados de métricas para o Azure, consulte [descrição geral das métricas no Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>P: como são acumulados créditos?
**A**: taxas de VM a acumulação e o consumo de estão definidas de forma a que uma VM em execução no exatamente o nível de desempenho de base terá nem uma acumulação de rede ou o consumo de segurança créditos.  Uma VM tem um aumento net em créditos sempre que se encontra em execução abaixo o nível de desempenho de base e terá um decréscimo net em créditos sempre que a VM está a utilizar a CPU mais do que o nível de desempenho de base.

**Exemplo**: implementar uma VM com o tamanho de B1ms para a minha aplicação de base de dados do tempo de pequena e assistência. Este tamanho permite que a minha aplicação utilizar até 20% de um vCPU como o meu da linha de base, que é.2 créditos por minuto, que pode utilizar ou bank. 

A minha aplicação está ocupada no início e fim do dia de trabalho meu funcionários, entre 7:00-09:00:00 e 4:00-18:00:00. Durante as outras 20 horas do dia, a minha aplicação está normalmente em inatividade, apenas a utilizar 10% do vCPU. Para as horas de pico posso mais significativos beneficiar 0.2 créditos por minuto, mas apenas consumir 0.l créditos por minuto, pelo que a minha VM será bank.1 x 60 = 6 créditos por hora.  Para as horas de 20 que estou ponta, posso será bank 120 créditos.  

Durante o horário de pico a minha aplicação averages 60% de utilização de vCPU, posso ainda mais significativos beneficiar de 0.2 créditos por minuto mas consigo consumir 0,6 créditos por minuto, um custo net de.4 créditos um minuto ou.4 x 60 = 24 créditos por hora. Tenho de 4 horas por dia de utilização das horas de ponta, para que os custos de 24 x 4 = 96 créditos para os meus utilização máxima.

Se efetuar os 120 créditos que posso resultantes ponta e subtrair os 96 créditos que posso utilizado para a minha ponta, posso bank um créditos 24 adicionais por dia que podem ser utilizados para outros bursts da atividade.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: B-série suporta discos de dados de Premium Storage?
**A**: Sim, todos os tamanhos de série B suportam discos de dados de armazenamento Premium.   
    

### <a name="q-which-regions-can-i-access-the-preview-from"></a>P: quais regiões posso aceder à pré-visualização do?
**A**: A pré-visualização de série B estarão disponível as seguintes regiões:
- -NOS - oeste 2
- -NOS - leste
- Europa - Oeste
- Ásia-Pacífico - Southeast

Uma vez concluída a pré-visualização Lançamos a série de B a todas as regiões restantes.
    

    
