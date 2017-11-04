# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento do Azure Premium: Conceção de elevado desempenho
## <a name="overview"></a>Descrição geral
Este artigo fornece diretrizes para a criação de aplicações de elevado desempenho com o Premium Storage do Azure. Pode utilizar as instruções fornecidas neste documento juntamente com as melhores práticas aplica-se a tecnologias utilizadas pela sua aplicação. Para ilustrar as diretrizes, ter utilizámos o SQL Server em execução no armazenamento Premium como um exemplo ao longo deste documento.

Enquanto que tratam cenários de desempenho da camada de armazenamento neste artigo, terá de otimizar a camada da aplicação. Por exemplo, se alojar um Farm do SharePoint no armazenamento do Azure Premium, pode utilizar os exemplos de SQL Server do artigo para otimizar o servidor de base de dados. Além disso, Otimize o servidor Web e servidor de aplicação para obter o desempenho de maioria do Farm do SharePoint.

Este artigo irá podem ajudar a responder a perguntas comuns sobre a otimização do desempenho de aplicações no armazenamento do Azure Premium, os seguintes

* Como medir o desempenho da sua aplicação?  
* Por que motivo não lhe existem esperado de elevado desempenho?  
* Os fatores que influenciam o desempenho de aplicações no armazenamento Premium?  
* Como é que estes fatores influenciam o desempenho da sua aplicação no armazenamento Premium?  
* Como pode otimizar de IOPS, largura de banda e latência?  

Fornecemos-estas diretrizes especificamente para o Premium Storage porque as cargas de trabalho em execução no armazenamento Premium altamente confidencial de desempenho. Fornecemos exemplos apropriado. Também pode aplicar algumas destas diretrizes para aplicações em execução em VMs de IaaS com discos de armazenamento Standard.

Antes de começar, se estiver familiarizado com o armazenamento Premium, ler primeiro o [Premium Storage: armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual de Azure](../articles/virtual-machines/windows/premium-storage.md) e [metas de desempenho e escalabilidade do Storage do Azure](../articles/storage/common/storage-scalability-targets.md) artigos.

## <a name="application-performance-indicators"></a>Indicadores de desempenho de aplicações
Iremos avaliar se uma aplicação está a funcionar corretamente ou não a utilizar como indicadores de desempenho, a rapidez uma aplicação está a processar um pedido de utilizador, a quantidade de dados está a processar uma aplicação por pedido, pedidos quantos estar a processar uma aplicação num período específico de tempo, como um utilizador tem de aguardar para obter uma resposta depois de submeter o pedido. Os termos de licenciamento técnicos para estes indicadores de desempenho são IOPS, débito ou largura de banda e latência.

Nesta secção, vamos abordar os indicadores de desempenho comuns no contexto de armazenamento Premium. A secção seguinte, os requisitos de aplicação de recolha, irá aprender a medir estes indicadores de desempenho para a sua aplicação. Mais tarde otimizar o desempenho da aplicação, irá aprender sobre os fatores que afetam estes indicadores de desempenho e recomendações para otimizá-los.

## <a name="iops"></a>IOPS
IOPS é o número de pedidos que a aplicação está a enviar para os discos de armazenamento num segundo. Uma operação de entrada/saída foi possível ler ou escrever, sequenciais ou aleatório. Aplicações de OLTP como um site de revenda online tem processado de imediato muitos pedidos de utilizador em simultâneo. Os pedidos de utilizador são insert e atualizar as transações de base de dados que consomem muita, que a aplicação tem de processar rapidamente. Por conseguinte, aplicações de OLTP necessitam de IOPS muito elevada. Essas aplicações processam milhões de pedidos de e/s pequenos e aleatórios. Se tiver uma aplicação deste tipo, deve conceber a infraestrutura de aplicações para otimizar o para o IOPS. Na secção posterior, *otimizar o desempenho da aplicação*, vamos discutir em detalhe os fatores que tem de considerar para obter o IOPS elevado.

Quando anexa um disco de armazenamento premium para a escala elevada de VM, do Azure Aprovisiona para que um número garantido de IOPS de acordo com a especificação de disco. Por exemplo, um disco de P50 Aprovisiona 7500 IOPS. Cada grande escala de tamanho VM também tem um limite IOPS específico que possa suportar. Por exemplo, uma VM GS5 padrão tem 80.000 IOPS limitar.

## <a name="throughput"></a>Débito
Débito ou largura de banda é a quantidade de dados que a aplicação está a enviar para os discos de armazenamento num intervalo especificado. Se a aplicação está a efetuar operações de entrada/saída com o tamanho de unidade de e/s grande, requer o débito elevado. Aplicações de armazém de dados tendem a emitir operações intensivas de análise que aceder a grandes partes dos dados de cada vez e normalmente efetuarem operações em massa. Por outras palavras, essas aplicações requerem um maior débito. Se tiver uma aplicação deste tipo, deve conceber a infraestrutura para otimizar o débito. Na secção seguinte, vamos discutir em detalhe os fatores tem otimizar para conseguir isto.

Quando anexa um disco de armazenamento premium para uma escala elevada de VM, do Azure Aprovisiona débito de acordo com a especificação desse disco. Por exemplo, um disco de P50 Aprovisiona 250 MB por segundo disco débito. Cada grande escala de tamanho VM também tem como limite de débito específico que possa suportar. Por exemplo, o padrão de GS5 de VM tem um débito máximo de 2.000 MB por segundo. 

Há uma relação entre o débito e IOPS, conforme mostrado na fórmula abaixo.

![](media/premium-storage-performance/image1.png)

Por conseguinte, é importante determinar os valores de débito e IOPS ideal que requer a sua aplicação. Como tentar otimizar um, o outro também obtém afetado. Numa secção posterior, *otimizar o desempenho da aplicação*, vamos abordar em obter mais detalhes sobre a otimização de IOPS e débito.

## <a name="latency"></a>Latência
A latência é o tempo que demora uma aplicação para receber um pedido único, enviá-lo para os discos de armazenamento e enviar a resposta ao cliente. Esta é uma medida crítico de desempenho de uma aplicação, além de IOPS e débito. A latência de um disco de armazenamento premium é o tempo que demora a obter as informações de um pedido e comunicá-la novamente para a sua aplicação. Armazenamento Premium fornece baixas latências consistentes. Se ativar anfitrião só de leitura a colocação em cache nos discos de armazenamento premium, pode obter muito menor latência de leitura. Vamos abordar colocação em cache do disco mais detalhadamente na secção posterior no *otimizar o desempenho da aplicação*.

Quando são otimizar a sua aplicação para obter mais elevado de IOPS e débito, afetará a latência da sua aplicação. Após a otimização do desempenho de aplicações, avalie sempre a latência da aplicação para evitar um comportamento inesperado latência elevada.

## <a name="gather-application-performance-requirements"></a>Recolher requisitos de desempenho de aplicações
Ao conceber aplicações de elevado desempenho em execução no armazenamento do Azure Premium, o primeiro passo é compreender os requisitos de desempenho da sua aplicação. Depois de recolher requisitos de desempenho, pode otimizar a sua aplicação para alcançar o desempenho ideal.

Na secção anterior, é explicado indicadores de desempenho comuns, IOPS, débito e latência. Tem de identificar que estes indicadores de desempenho são críticos para a sua aplicação para fornecer a experiência de utilizador pretendido. Por exemplo, IOPS elevado é mais importante para aplicações de OLTP processar milhões de transações num segundo. Enquanto, é essencial para aplicações de armazém de dados grandes quantidades de dados de processamento em segundo débito elevado. Latência extremamente baixa é fundamental para aplicações em tempo real, como as vídeo em direto Web sites de transmissão em fluxo.

Em seguida, medir os requisitos de desempenho máximo da sua aplicação ao longo da respetiva duração. Utilize a lista de verificação de exemplo abaixo como um início. Registe a requisitos de desempenho máximo durante normal, os períodos de carga de trabalho de pico e off-hours. Ao identificar os requisitos para todos os níveis de cargas de trabalho, será capaz de determinar os requisitos de desempenho global da sua aplicação. Por exemplo, a carga de trabalho normal de um site de comércio eletrónico será as transações serve durante a maior parte dos dias num ano. A carga de trabalho de pico do Web site será as transações serve durante a época ou eventos de venda especiais. A carga de trabalho de pico é normalmente experiente por um período limitado, mas pode exigir a sua aplicação a dimensionar dois ou mais vezes o funcionamento normal. Determinar o percentil 50, percentil 90 e requisitos de percentil 99. Isto ajuda a filtrar quaisquer valores atípicos nos requisitos de desempenho e pode concentrar-se os esforços na otimizar para os valores corretos.

**Lista de verificação de requisitos de desempenho de aplicações**

| **Requisitos de desempenho** | **Percentil 50** | **Percentil 90** | **Percentil 99** |
| --- | --- | --- | --- |
| Um máximo de Transações por segundo | | | |
| Operações de leitura % | | | |
| Operações de escrita % | | | |
| Operações aleatório % | | | |
| Operações sequenciais % | | | |
| Tamanho do pedido de e/s | | | |
| Débito médio | | | |
| Um máximo de Débito | | | |
| Min. Latência | | | |
| Latência média de | | | |
| Um máximo de CPU | | | |
| CPU média | | | |
| Um máximo de Memória | | | |
| Memória média | | | |
| Profundidade de fila | | | |

> [!NOTE]
> Deve considerar o dimensionamento estes números com base no esperado crescimento futuro da sua aplicação. É aconselhável planear crescimento antecedência, porque pode ser harder para alterar a infraestrutura para melhorar o desempenho mais tarde.
>
>

Se tiver uma aplicação existente e pretender mover para o Premium Storage, primeiro crie a lista de verificação acima para a aplicação existente. Em seguida, criar um protótipo da sua aplicação no armazenamento Premium e estruturar a aplicação com base em diretrizes descritas em *otimizar o desempenho da aplicação* numa secção posterior deste documento. A secção seguinte descreve as ferramentas que pode utilizar para reunir as medidas de desempenho.

Crie uma lista de verificação semelhante à sua aplicação existente para o protótipo. Utilizar ferramentas de Benchmarking pode simular as cargas de trabalho e medir o desempenho da aplicação de protótipo. Consulte a secção sobre [Benchmarking](#benchmarking) para obter mais informações. Por se o fizer, pode determinar se o armazenamento Premium podem corresponder ou surpass os seus requisitos de desempenho de aplicações. Em seguida, pode implementar as mesmas diretrizes para a sua aplicação de produção.

### <a name="counters-to-measure-application-performance-requirements"></a>Contadores para medir os requisitos de desempenho de aplicações
A melhor forma de medir os requisitos de desempenho da aplicação, é utilizar ferramentas de monitorização de desempenho fornecidas pelo sistema operativo do servidor. Pode utilizar PerfMon para Windows e iostat para Linux. Estas ferramentas capturam contadores correspondente a cada medida explicada na secção acima. Quando a aplicação está a executar o normal, cargas de trabalho das horas de ponta e off-hours, tem de capturar os valores de um destes contadores.

Os contadores de PerfMon estão disponíveis para processador, memória e, como cada disco lógico e disco físico do seu servidor. Quando utilizar discos de armazenamento premium com uma VM, os contadores de disco físico são para cada disco de armazenamento premium e contadores de disco lógico são para cada volume criado nos discos de armazenamento premium. Tem de capturar os valores para os discos que alojam a carga de trabalho da aplicação. Se existir um mapeamento de um-para-um entre os discos lógicos e físicos, pode consultar os contadores de disco físico caso contrário, consulte os contadores de disco lógico. No Linux, o comando iostat gera um relatório de utilização da CPU e de disco. O relatório de utilização do disco fornece estatísticas por partição ou o dispositivo físico. Se tiver um servidor de base de dados com a dados e registo nos discos separados, recolha estes dados para os dois discos. Tabela abaixo descreve os contadores de discos, processadores e memória:

| Contador | Descrição | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS ou de transações por segundo** |Número de pedidos de e/s emitido para o disco de armazenamento por segundo. |Leituras de disco/seg <br> Escritas de disco/seg |tps <br> r/s <br> w/s |
| **Leituras de disco e de escrita** |% de leituras exibidas as operações executadas no disco. |% Tempo de leitura do disco <br> % De tempo de escrita de disco |r/s <br> w/s |
| **Débito** |Quantidade de dados lidas ou escritas para o disco por segundo. |Bytes lidos/seg de disco <br> Bytes de escrita de disco/seg |kB_read/s <br> kB_wrtn/s |
| **Latência** |Total de tempo para concluir um pedido de e/s de disco. |Médio disco seg/leitura <br> Média de disco seg/escrita |await <br> svctm |
| **Tamanho de e/s** |O tamanho de e/s pedidos problemas para os discos de armazenamento. |Bytes de média de disco/leitura <br> Bytes de média de disco/escrita |avgrq sz |
| **Profundidade de fila** |Número de e/s pendentes pedidos a aguardar para ser formulário de leitura ou escrita para o disco de armazenamento. |Comprimento da fila de disco |avgqu sz |
| **Máx. Memória** |Quantidade de memória necessária para executar a aplicação facilmente |% Dos Bytes consolidados em utilização |Utilizar vmstat |
| **Máx. CPU** |Quantidade de CPU necessária para executar a aplicação facilmente |% Tempo do processador |% util |

Saiba mais sobre [iostat](http://linuxcommand.org/man_pages/iostat1.html) e [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).

## <a name="optimizing-application-performance"></a>Otimizar o desempenho de aplicações
Os principais fatores que influenciam o desempenho de uma aplicação em execução no armazenamento Premium são natureza de e/s pedidos, tamanho da VM, o tamanho do disco, número de discos, colocação em cache do disco, Multithreading e profundidade de fila. Pode controlar algumas destes fatores com botões fornecidos pelo sistema. A maioria das aplicações poderão não dão-lhe uma opção para alterar o tamanho de e/s e a profundidade de fila diretamente. Por exemplo, se estiver a utilizar o SQL Server, não é possível escolher a profundidade de fila e de tamanho de e/s. SQL Server escolhe os ideal e/s tamanho e a fila de profundidade valores para obter o desempenho mais. É importante compreender os efeitos de ambos os tipos de fatores no desempenho da aplicação, para que possa aprovisionar os recursos adequados para satisfazer as necessidades de desempenho.

Ao longo de nesta secção, consulte a lista de verificação de requisitos de aplicação que criou, para identificar quanto precisa otimizar o desempenho da sua aplicação. Com base no que, será capaz de determinar quais fatores desta secção, terá de ajustar. Para o testemunho os efeitos de cada fator no desempenho da sua aplicação, execute ferramentas direcionamento de caminhos na sua configuração de aplicação. Consulte o [Benchmarking](#Benchmarking) secção no final deste artigo para obter os passos executar as ferramentas de direcionamento de caminhos comuns no Windows e VMs com Linux.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Otimizar o IOPS, débito e latência num instante
A tabela abaixo resume todos os fatores de desempenho e os passos para otimizar o IOPS, débito e latência. As secções seguintes este resumo descrevem cada fator é profundidade muito mais.

| &nbsp; | **ESP** | **Débito** | **Latência** |
| --- | --- | --- | --- |
| **Cenário de exemplo** |Aplicação de OLTP de Enterprise que requerem muito elevadas transações por segundo taxa. |Aplicação processamento vastos volumes de dados de armazém de dados da empresa. |Junto de aplicações em tempo real que requerem respostas instantâneas a pedidos de utilizador, como jogos online. |
| Fatores de desempenho | &nbsp; | &nbsp; | &nbsp; |
| **Tamanho de e/s** |Tamanho mais pequeno de e/s gera IOPS superior. |Tamanho maior de e/s para gera um maior débito. | &nbsp;|
| **Tamanho da VM** |Utilize um tamanho VM que oferece maior do que o requisito da aplicação de IOPS. Consulte os tamanhos de VM e os respetivos limites IOPS aqui. |Utilize um tamanho VM com limite de débito maior do que o requisito da aplicação. Consulte os tamanhos de VM e os respetivos limites de débito aqui. |Utilize um tamanho VM que oferece Dimensionar limites maiores do que o requisito da aplicação. Consulte os respetivos limites aqui e tamanhos VM. |
| **Tamanho do disco** |Utilize um tamanho de disco que oferece maior do que o requisito da aplicação de IOPS. Consulte os tamanhos de disco e os respetivos limites IOPS aqui. |Utilize um tamanho de disco com limite de débito maior do que o requisito da aplicação. Consulte os tamanhos de disco e os respetivos limites de débito aqui. |Utilize um tamanho de disco que ofertas aumentar os limites de maiores do que o requisito da aplicação. Consulte os respetivos limites aqui e tamanhos de disco. |
| **VM e os limites de escala de disco** |Limite IOPS do tamanho da VM escolhido deve ser maior que ESP totais condicionadas pelos discos de armazenamento premium ligados ao mesmo. |Limite de débito do tamanho da VM escolhido deve ser maior que o débito total conduzido pelos discos de armazenamento premium ligados ao mesmo. |Os limites de escala do tamanho da VM escolhido tem de ser maiores do que os limites de escala total dos discos de armazenamento premium ligados. |
| **Colocação em cache do disco** |Ative a Cache de só de leitura nos discos de armazenamento premium com as operações de leitura pesadas para obter mais elevado de leitura de IOPS. | &nbsp; |Ative a Cache de só de leitura nos discos de armazenamento premium com operações pesadas prontos para obter as latências de leitura muito baixa. |
| **Disco Striping** |Utilizar vários discos e stripe-los em conjunto para obter um limite IOPS e de débito superior combinado. Tenha em atenção que o valor de limite combinado por VM deve ser superior aos limites combinados de discos premium ligados. | &nbsp; | &nbsp; |
| **Tamanho do stripe** |Tamanho mais pequeno do stripe para padrão de e/s pequena aleatória visto nas aplicações OLTP. Por exemplo, utilize o tamanho do stripe de 64KB para a aplicação do SQL Server OLTP. |Tamanho maior de stripe para padrão de e/s grande sequencial visto nas aplicações do armazém de dados. Por exemplo, utilize o tamanho do stripe de 256KB para a aplicação de armazém de dados do SQL Server. | &nbsp; |
| **Multithreading** |Utilize multithreading para emitir o maior número de pedidos para o Premium Storage que irá causar a maior IOPS e débito. Por exemplo, no SQL Server definir um valor MAXDOP elevado alocar mais CPUs, ao SQL Server. | &nbsp; | &nbsp; |
| **Profundidade de fila** |Maior profundidade de fila gera IOPS superior. |Maior profundidade de fila gera um maior débito. |Profundidade de fila menor gera latências inferiores. |

## <a name="nature-of-io-requests"></a>Natureza dos pedidos de e/s
Um pedido de e/s é uma unidade de operação de entrada/saída que irão realizar a aplicação. Identificar a natureza dos pedidos de e/s, aleatórios ou sequenciais, ler ou escrever, tamanho, ajudará a determinar os requisitos de desempenho da sua aplicação. É muito importante compreender a natureza dos pedidos de e/s, tomar as decisões corretas quando conceber a infraestrutura de aplicação.

O tamanho de e/s é um dos fatores mais importantes. O tamanho de e/s é o tamanho do pedido de operação de entrada/saída gerado pela sua aplicação. O tamanho de e/s tem um impacto significativo no desempenho, especialmente no IOPS e largura de banda que a aplicação é capaz de alcançar. A fórmula seguinte mostra a relação entre IOPS, tamanho de e/s e largura de banda/débito.  
    ![](media/premium-storage-performance/image1.png)

Algumas aplicações permitem-lhe alterar o tamanho de e/s, enquanto algumas aplicações não compatíveis. Por exemplo, o SQL Server determina o tamanho ideal de e/s próprio e não fornecer aos utilizadores quaisquer botões para alterá-la. Por outro lado, Oracle fornece um parâmetro denominado [DB\_bloquear\_tamanho](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) através do qual pode configurar o tamanho do pedido de e/s da base de dados.

Se estiver a utilizar uma aplicação, o que lhe permite alterar o tamanho de e/s, utilize as diretrizes neste artigo para otimizar o desempenho de KPI que é mais relevante para a aplicação. Por exemplo,

* Uma aplicação OLTP gera milhões de pedidos de e/s pequenos e aleatórios. Para resolver este tipo de pedidos de e/s, deve conceber a infraestrutura de aplicação para obter o IOPS superior.  
* Uma aplicação de armazém de dados gera pedidos de e/s grande e sequenciais. Para resolver este tipo de pedidos de e/s, deve conceber a infraestrutura de aplicação para obter maior largura de banda ou débito.

Se estiver a utilizar uma aplicação, o que permite-lhe alterar o tamanho de e/s, utilizar esta regra geral para o tamanho de e/s para além de outras diretrizes de desempenho

* E/s mais pequeno obter IOPS superior. Por exemplo, 8 KB para uma aplicação OLTP.  
* Tamanho maior de e/s para obter um maior largura de banda/débito. Por exemplo, 1024 KB para uma aplicação de armazém de dados.

Eis um exemplo sobre como pode calcular o débito/largura de banda e IOPS para a sua aplicação. Considere uma aplicação utilizando um disco de P30. O máximo IOPS e débito/largura de banda de um disco de P30 podem alcançar é 5000 IOPS e 200 MB por segundo, respetivamente. Agora, se a aplicação requer o IOPS máximo do disco P30 e utilizar um tamanho mais pequeno de e/s como 8 KB, a largura de banda resultante será capaz de obter é de 40 MB por segundo. No entanto, se a aplicação requer a débito/largura de banda máxima do disco P30 e utilizar um tamanho maior de e/s como 1024 KB, o IOPS resultante será menos 200 IOPS. Por conseguinte, ajustar o tamanho de e/s, de forma a que ele cumpra o requisito de IOPS e débito/largura de banda de ambos da sua aplicação. Tabela a seguir resume os diferentes tamanhos de e/s e os respetivos IOPS correspondente e o débito para um disco de P30.

| Requisito da aplicação | Tamanho de e/s | IOPS | Débito/largura de banda |
| --- | --- | --- | --- |
| IOPS máximo |8 KB |5,000 |40 MB por segundo |
| Débito máximo |1024 KB |200 |200 MB por segundo |
| Débito máximo + IOPS elevado |64 KB |3,200 |200 MB por segundo |
| IOPS máximo + débito elevado |32 KB |5,000 |160 MB por segundo |

Para obter IOPS e largura de banda superior ao valor máximo de um disco de armazenamento premium único, utilize vários discos premium repartidos em conjunto. Por exemplo, stripe dois P30 discos para obter um IOPS combinado de IOPS 10 000 ou um débito combinado de 400 MB por segundo. Conforme explicado na secção seguinte, tem de utilizar um tamanho VM que suporte o combinado IOPS e débito do disco.

> [!NOTE]
> Como aumentar o IOPS ou débito outros também aumenta, certifique-se de que não atingiu o débito ou limites IOPS da VM ou disco quando aumento deles.
>
>

Para testemunho os efeitos de tamanho de e/s no desempenho da aplicação, pode executar as ferramentas de direcionamento de caminhos na sua VM e os discos. Criar várias execuções do teste e utilizar o tamanho de e/s diferente para cada execução para ver o impacto. Consulte o [Benchmarking](#Benchmarking) secção no final deste artigo para obter mais detalhes.

## <a name="high-scale-vm-sizes"></a>Tamanhos de VM de grande escala
Quando inicia a conceber uma aplicação, um dos primeiros aspetos a fazer é, selecione uma VM para alojar a aplicação. Armazenamento Premium inclui os tamanhos de VM de escala elevada que podem ser executadas as aplicações que requerem maior capacidade de computação e um elevado desempenho de e/s de disco local. Estas VMs fornecem processadores mais rápidos, um rácio superior de memória-core e um Solid-State unidade (SSD) para o disco local. Exemplos de elevada de dimensionamento de VMs que suportam o Premium Storage são a série DS, série DSv2 e GS VMs.

Elevada de dimensionamento de VMs estão disponíveis em diferentes tamanhos com um número diferente de núcleos CPU, memória, SO e tamanho de disco temporário. Cada tamanho da VM também tem o número máximo de discos de dados que pode anexar à VM. Por conseguinte, o tamanho da VM escolhido irão afetar a quantidade de processamento, memória, e a capacidade de armazenamento está disponível para a sua aplicação. Também afeta a capacidade de cálculo e o custo de armazenamento. Por exemplo, seguem-se as especificações do maior tamanho da VM numa série DS, série DSv2 série e uma série GS:

| Tamanho da VM | Núcleos de CPU | Memória | Tamanhos de disco da VM | Um máximo de discos de dados | Tamanho da cache | IOPS | Limites de Cache e/s de largura de banda |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |SO = 1023 GB <br> Local SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB por segundo |4000 IOPS e 33 MB por segundo |
| Standard_GS5 |32 |448 GB |SO = 1023 GB <br> Local SSD = 896 GB |64 |4224 GB |80.000 IOPS <br> 2.000 MB por segundo |5000 IOPS e 50 MB por segundo |

Para ver uma lista completa de todos os tamanhos de VM do Azure disponíveis, consulte [tamanhos de Windows VM](../articles/virtual-machines/windows/sizes.md) ou [tamanhos de VM com Linux](../articles/virtual-machines/linux/sizes.md). Escolha um tamanho VM que pode satisfazer e dimensionar aos seus requisitos de desempenho da aplicação desejada. Além disto, tenha em conta considerações importantes sobre a seguir ao escolher tamanhos de VM.

*Limites de escala*  
Os limites IOPS máximos por VM e por disco são diferentes e independentes entre si. Certifique-se de que a aplicação está a ocasionar IOPS dentro dos limites da VM, bem como os discos premium ligados ao mesmo. Caso contrário, o desempenho da aplicação verificará a limitação.

Por exemplo, suponha que um requisito da aplicação é um máximo de 4000 IOPS. Para atingir esse objetivo, aprovisionar um disco de P30 numa DS1 VM. O disco P30 pode proporcionar até 5000 IOPS. No entanto, a VM DS1 está limitada ao 3,200 IOPS. Por conseguinte, o desempenho da aplicação irá ser restrita, o limite VM em 3,200 IOPS e será degradação do desempenho. Para evitar esta situação, escolha um tamanho da VM e o disco que será ambos cumpre os requisitos da aplicação.

*Custo de operação*  
Em muitos casos, é possível que o custo global de operação de utilizar o Premium Storage é inferior ao utilizar o armazenamento Standard.

Por exemplo, considere uma aplicação que requerem 16,000 IOPS. Para alcançar este desempenho, precisa de um padrão\_D14 Azure VM do IaaS, que pode dar um IOPS máximo de 16.000 utilizando 32 discos de 1 TB de armazenamento standard. Cada disco de armazenamento standard de 1TB pode alcançar um máximo de 500 IOPS. O custo estimado desta VM por mês será $1,570. O custo mensal 32 padrão dos discos de armazenamento será $1,638. O custo mensal total estimado será $3,208.

No entanto, se estiver alojado no armazenamento Premium, a mesma aplicação, terá um tamanho VM mais pequeno e menos discos de armazenamento premium, reduzindo, deste modo, o custo global. Um padrão\_DS13 VM pode cumpre o requisito de IOPS 16,000 utilizando quatro P30 discos. A VM DS13 tem um IOPS máximo de 25,600 e cada disco P30 tem um IOPS máximo de 5000. Em geral, esta configuração pode alcançar a 5000, 4 = 20.000 IOPS. O custo estimado desta VM por mês será $1,003. O custo mensal quatro P30 premium dos discos de armazenamento será $544.34. O custo mensal total estimado será $1,544.

Tabela a seguir resume a repartição de custo neste cenário para Standard e Premium Storage.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Custo de VM por mês** |$1,570.58 (padrão\_D14) |$1,003.66 (padrão\_DS13) |
| **Custo de discos por mês** |$1,638.40 (discos de 32 x 1 TB) |$544.34 (4 discos de x P30) |
| **Custo global por mês** |$3,208.98 |$1,544.34 |

*Distros do Linux*  

Storage do Azure Premium, obter o mesmo nível de desempenho para as VMs que executam o Windows e Linux. Iremos suportar muitos tipos de Linux distros e pode ver a lista completa [aqui](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). É importante ter em atenção que os diferentes distros são melhor adequados para diferentes tipos de cargas de trabalho. Verá diferentes níveis de desempenho dependendo distro que a carga de trabalho está em execução. Testar a distros Linux com a sua aplicação e escolha o que funciona melhor.

Quando executar o Linux com o Premium Storage, verifique as atualizações mais recentes sobre os controladores necessários para se certificar de elevado desempenho.

## <a name="premium-storage-disk-sizes"></a>Tamanhos de disco de armazenamento Premium
Armazenamento Premium do Azure oferece atualmente sete tamanhos de disco. Cada tamanho do disco tem um limite de escala diferentes de IOPS, largura de banda e de armazenamento. Escolha o direito de tamanho de disco de armazenamento Premium, consoante os requisitos da aplicação e a tamanho da VM de grande escala. A tabela abaixo mostra os tamanhos de sete discos e as respetivas capacidades. Os tamanhos de P4 e P6 são atualmente só é suportada para discos geridos.

| Tipo de discos Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 32 GB | 64 GB | 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPs por disco       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Débito por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo | 


Quantos discos que escolher depende o disco de tamanho escolhida. Pode utilizar um disco de P50 único ou vários discos P10 para cumprir o requisito de aplicação. Tenha em considerações de conta listadas abaixo quando efetuar a escolha.

*Limites de escala (IOPS e débito)*  
Os limites IOPS e débito de cada tamanho do disco Premium é diferente e independentemente dos limites de dimensionamento VM. Certifique-se de que o IOPS total e o débito dos discos estão dentro dos limites de escala de tamanho VM que escolheu.

Por exemplo, se for um requisito da aplicação um máximo de 250 MB/seg débito e está a utilizar uma VM DS4 com um único disco P30. A VM DS4 pode dar até 256 MB/seg débito. No entanto, um único disco P30 tem limite de débito de 200 MB/seg. Por conseguinte, a aplicação irá ser restrita, 200 MB por segundo devido ao limite de disco. Para ultrapassar este limite, aprovisionar mais do que um discos de dados para a VM ou redimensionar os discos para P40 ou P50.

> [!NOTE]
> Leituras servidas pela cache de não estão incluídas no disco IOPS e débito, por conseguinte, não requerente limites de disco. Cache tem o limite de IOPS e débito separado por VM.
>
> Por exemplo, inicialmente as leituras e escritas são 60MB/seg e 40MB/seg, respetivamente. Ao longo do tempo, a cache warms cópias de segurança e serve mais e muito mais das leituras da cache. Em seguida, pode obter escrita maior débito do disco.
>
>

*Número de discos*  
Determine o número de discos, terá de ao avaliar os requisitos da aplicação. Cada tamanho da VM também tem um limite no número de discos pode anexar à VM. Normalmente, isto é duas vezes o número de núcleos. Certifique-se de que o tamanho da VM que escolher pode suportar o número de discos necessários.

Lembre-se de que os discos de armazenamento Premium têm capacidades de desempenho superiores em comparação com discos de armazenamento Standard. Por conseguinte, se estiver a migrar a aplicação da VM do IaaS do Azure utilizando o armazenamento Standard para o Premium Storage, provavelmente terá menos discos premium para alcançar um desempenho igual ou superior para a sua aplicação.

## <a name="disk-caching"></a>Colocação em cache do disco
VMs de escala elevada que tiram partido do Premium Storage do Azure tem uma tecnologia de colocação em cache de várias camada chamada BlobCache. BlobCache utiliza uma combinação da RAM de Máquina Virtual e o local SSD para colocar em cache. Esta cache está disponível para o Premium Storage persistentes discos e os discos da VM locais. Por predefinição, esta definição de cache é definida como leitura/escrita para discos de SO e só de leitura para os discos de dados alojados no armazenamento Premium. Com a colocação em cache no disco ativada nos discos de armazenamento Premium, a escala elevada VMs pode alcançar extremamente elevados níveis de desempenho que excedam o desempenho de disco subjacente.

> [!WARNING]
> Alterar a definição de cache de um disco do Azure Desanexa e anexa novamente o disco de destino. Se for o disco do sistema operativo, a VM é reiniciada. Pare todas as aplicações/serviços que poderão ser afetados por este interrupção antes de alterar a definição de cache do disco.
>
>

Para obter mais informações sobre como funciona o BlobCache, consulte interior [Premium Storage do Azure](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blogue.

É importante ativar a cache o conjunto correto de discos. Indica se deve ativar a colocação em cache no disco num disco premium ou não irão depender o padrão de carga de trabalho esse disco irá processar. Tabela abaixo mostra as predefinições de definições de cache para o SO e discos de dados.

| **Tipo de disco** | **Definição de Cache predefinida** |
| --- | --- |
| Disco do SO |ReadWrite |
| Disco de dados |Nenhuma |

Seguem-se as definições de cache do disco recomendado para discos de dados

| **Definição de colocação em cache em disco** | **Recomendação sobre quando utilizar esta definição** |
| --- | --- |
| Nenhuma |Configure a cache de anfitriões como None para só de escrita e pesado de escrita de discos. |
| Só de leitura |Configure a cache de anfitriões como só de leitura para só de leitura e de leitura-escrita de discos. |
| ReadWrite |Configure a cache de anfitriões como ReadWrite apenas se a aplicação processa corretamente ao escrever dados em cache persistentes discos quando necessário. |

*Só de leitura*  
Ao configurar a colocação em cache nos dados de armazenamento Premium discos de só de leitura, pode alcançar baixa latência de leitura e obter muito elevado de IOPS de leitura e débito para a sua aplicação. Isto é por duas razões,

1. Leituras efetuada a partir da cache, o que é a memória da VM e o local SSD, são muito mais rápidas do que lê a partir do disco de dados, que está no blob storage do Azure.  
2. Armazenamento Premium não contagem de leituras servidas a partir da cache, para o disco IOPS e débito. Por conseguinte, a aplicação é capaz de alcançar superior ESP totais e débito.

*ReadWrite*  
Por predefinição, os discos de SO têm ReadWrite colocação em cache ativada. Foi recentemente adicionado suporte para a colocação em cache nos dados, bem como discos de ReadWrite. Se estiver a utilizar ReadWrite colocação em cache, tem de ter uma forma adequada para escrever os dados da cache persistentes discos. Por exemplo, o SQL Server processa escrever dados em cache para os discos de armazenamento persistente na sua própria. Utilizar a cache de ReadWrite com uma aplicação que não processa os dados necessários a persistência pode levar a perda de dados, se a VM falhas.

Por exemplo, pode aplicar estas diretrizes ao SQL Server em execução no armazenamento Premium efetuando o seguinte

1. Configure a cache de "ReadOnly" nos discos de armazenamento premium que alojem ficheiros de dados.  
   a.  O fast lê a partir da cache inferior a hora de consulta de SQL Server, uma vez que as páginas de dados são obtidas muito mais rapidamente a partir da cache em comparação com diretamente a partir dos dados de discos.  
   b.  Servir leituras da cache, significa que existe débito adicional disponíveis a partir dos discos de dados premium. SQL Server pode utilizar este débito adicional para obter mais páginas de dados e outras operações como a cópia de segurança/restauro, lote de cargas e Reconstrói do índice.  
2. Configurar "None" em cache nos discos de armazenamento premium que aloja os ficheiros de registo.  
   a.  Ficheiros de registo têm principalmente as operações de escrita pesado. Por conseguinte, não beneficiar da cache só de leitura.

## <a name="disk-striping"></a>Disco Striping
Quando uma escala elevada de que VM está ligada com vários discos persistente de armazenamento premium, os discos podem repartidos em conjunto para agregar os IOPs, a largura de banda e a capacidade de armazenamento.

No Windows, pode utilizar os espaços de armazenamento para discos do stripe em conjunto. Tem de configurar uma coluna para cada disco num agrupamento. Caso contrário, o desempenho global do volume repartido pode ser menor do que o esperado devido a distribuição desigual de tráfego nos discos.

Importante: Através da IU do Gestor de servidor, pode definir o número total de colunas até 8 para um volume repartido. Ao anexar mais de 8 discos, utilize o PowerShell para criar o volume. Utilizar o PowerShell, pode definir o número de colunas igual ao número de discos. Por exemplo, se existirem 16 discos num conjunto único stripe; Especifique 16 colunas no *NumberOfColumns* parâmetro o *New-VirtualDisk* cmdlet do PowerShell.

No Linux, utilize o utilitário MDADM discos do stripe em conjunto. Para obter passos detalhados sobre discos striping no Linux, consulte [configurar RAID de Software no Linux](../articles/virtual-machines/linux/configure-raid.md).

*Tamanho do stripe*  
Uma configuração importante no striping de disco é o tamanho do stripe. O tamanho do stripe ou o tamanho do bloco é o segmento de dados que pode abordar aplicação num volume repartido mais pequeno. O tamanho do stripe que configurar depende do tipo de aplicação e o padrão de pedido. Se escolher o tamanho errado stripe, pode levar a desalinhamento de e/s, o que conduz a degradação do desempenho da sua aplicação.

Por exemplo, se um pedido de e/s gerado pela sua aplicação é maior do que o tamanho do stripe de disco, o sistema de armazenamento escreve-lo através de limites de unidade do stripe em mais de um disco. Quando está na altura de aceder a esses dados, terá de procurar em mais do que um stripe unidades para concluir o pedido. O efeito desse comportamento cumulativo pode levar a degradação do desempenho substanciais. Por outro lado, se o tamanho do pedido de e/s é inferior ao tamanho do stripe, e se é aleatória natureza, os pedidos de e/s podem ser adicionados até no mesmo disco causar um estrangulamento e, em última análise degradar o desempenho de e/s.

Dependendo do tipo de carga de trabalho que da aplicação está em execução, escolha um tamanho adequado stripe. Para pedidos de e/s pequenos aleatórios, utilize um tamanho mais pequeno do stripe. Enquanto que, para e/s sequenciais grandes de pedidos de utilizam um tamanho maior do stripe. Determinar as stripe tamanho recomendações para a aplicação que vai estar em execução no armazenamento Premium. Para o SQL Server, configure o tamanho do stripe de 64KB para cargas de trabalho OLTP e 256KB para cargas de trabalho de armazenamento de dados. Consulte [práticas recomendadas do SQL Server em VMs do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) para obter mais informações.

> [!NOTE]
> Pode stripe em conjunto um máximo de 32 discos de armazenamento premium numa série DS VM e 64 discos de armazenamento premium numa série GS VM.
>
>

## <a name="multi-threading"></a>Os vários segmentos
Azure foi concebido de plataforma de armazenamento Premium para ser paralelo em grande escala. Por conseguinte, uma aplicação multi-thread permite um desempenho muito superior que uma aplicação single-threaded. Uma aplicação multi-thread divide as tarefas de cópia de segurança através de vários threads e aumenta a eficiência da sua execução utilizando os VM e recursos de disco para o máximo.

Por exemplo, se a aplicação estiver a executar um único núcleo de VM com dois threads, a CPU pode alternar entre os dois threads para alcançar a eficiência. Enquanto está em espera um thread num disco e/s para concluir, a CPU pode mudar para o outro thread. Desta forma, podem realizar dois threads mais do que seria de um único thread. Se a VM tem mais do que uma core, mais diminui tempo de execução, uma vez que cada core pode executar tarefas em paralelo.

Não poderá alterar a forma como uma aplicação off-the-shelf implementa único thread ou vários segmentos. Por exemplo, o SQL Server é capaz de processar várias CPU e vários núcleos. No entanto, o SQL Server decide em que condições irá tirar partido de um ou mais threads a processar uma consulta. -Pode executar consultas e criar os índices com vários segmentos. Para uma consulta que envolve a associar a tabelas grandes e ordenar dados antes de o devolver ao utilizador, SQL Server, provavelmente, irá utilizar vários threads. No entanto, um utilizador não é possível controlar se o SQL Server executa uma consulta com um thread único ou vários threads.

Existem definições de configuração que pode alterar a influenciar isto vários segmentos ou paralela de processamento de uma aplicação. Por exemplo, no caso do SQL Server é a configuração de grau de paralelismo máxima. Esta definição denominada MAXDOP, permite-lhe configurar o número máximo de processadores que do SQL Server pode utilizar ao paralela de processamento. Pode configurar MAXDOP para consultas individuais ou as operações do índice. Isto é vantajoso quando pretende efetuar o balanceamento de recursos do sistema para uma aplicação de crítico de desempenho.

Por exemplo, diga a sua aplicação com o SQL Server está a executar uma grande consulta e uma operação do índice ao mesmo tempo. Informe-nos partem do princípio de que pretendia a operação do índice para ser mais performant em comparação com a consulta de grandes dimensões. Nesse caso, pode definir o valor MAXDOP da operação do índice ser superior ao valor de MAXDOP para a consulta. Desta forma, o SQL Server tem mais de número de processadores que podem tirar partido das para a operação do índice em comparação com o número de processadores que pode dedicar à consulta grande. Lembre-se de que não controla o número de threads do SQL Server irá utilizar para cada operação. Pode controlar o número máximo de processadores que está a ser dedicado para os vários segmentos.

Saiba mais sobre [graus de paralelismo](https://technet.microsoft.com/library/ms188611.aspx) no SQL Server. Determinar as definições desse tipo que influencia os vários segmentos na sua aplicação e as respetivas configurações para otimizar o desempenho.

## <a name="queue-depth"></a>Profundidade de fila
A profundidade de fila ou o comprimento da fila ou o tamanho da fila é o número de pedidos de e/s pendentes no sistema. O valor da profundidade de fila determina quantas operações de e/s a aplicação pode linha cópias de segurança, que os discos de armazenamento serão estar a processar. Afetará todos os indicadores de desempenho de aplicação de três que discutimos na viz. neste artigo, IOPS, débito e latência.

Fila de profundidade e tem vários segmentos estritamente relacionados. O valor de profundidade de fila indica quanto os vários segmentos pode ser conseguido através da aplicação. Se a profundidade de fila é grande, aplicação pode executar outras operações em simultâneo, por outras palavras, mais vários segmentos. Se a profundidade de fila é pequena, mesmo que a aplicação tem vários threads, não terá suficiente pedidos lined cópias de segurança para execução em simultâneo.

Normalmente, desative o shelf aplicações não permitem-lhe alterar a profundidade de fila, porque se configurado incorretamente executará mais perigo que boa. Aplicações irão definir o valor à direita da profundidade de fila para obter o desempenho ideal. No entanto, é importante compreender este conceito de modo a que pode resolver problemas de desempenho com a sua aplicação. Também pode observar os efeitos da profundidade de fila pela execução de ferramentas de direcionamento de caminhos no seu sistema.

Algumas aplicações fornecem definições de modo a influenciar a profundidade de fila. Por exemplo, a definição de (máximo grau de paralelismo) MAXDOP no SQL Server explicado na secção anterior. MAXDOP é uma forma de modo a influenciar a profundidade de fila e os vários segmentos, apesar de não alterar diretamente o valor de profundidade de fila do SQL Server.

*Profundidade de fila elevada*  
Linhas de uma profundidade de fila de alta segurança mais operações no disco. O disco sabe o seguinte pedido na respetiva fila de antecedência. Por conseguinte, o disco pode agendar operações antecedência e processá-los numa sequência ideal. Uma vez que a aplicação está a enviar mais pedidos para o disco, o disco pode processar mais IOs paralelas. Em última análise, a aplicação será capaz de alcançar o IOPS superior. Uma vez que a aplicação está a processar mais pedidos, também aumenta o débito total da aplicação.

Normalmente, uma aplicação pode alcançar o débito máximo com 8-16 + IOs pendentes por disco ligado. Se uma profundidade de fila, aplicação é não enviar por push suficiente IOs para o sistema e processará menor quantidade de num determinado período. Por outras palavras, a menos débito.

Por exemplo, no SQL Server, a definição do valor MAXDOP para uma consulta para "4" informa o SQL Server que pode utilizar até quatro núcleos para executar a consulta. SQL Server irá determinar o que é o valor de profundidade de fila melhores e o número de núcleos para a execução de consulta.

*Profundidade de fila ideal*  
Valor de profundidade de fila muito elevado tem também as desvantagens. Se o valor de profundidade de fila é demasiado elevado, a aplicação irá tentar IOPS muito elevado de unidade. A menos que a aplicação tem discos persistentes com IOPS aprovisionado suficientes, isto pode afetar negativamente latências da aplicação. Fórmula a seguir mostra a relação entre o IOPS, a latência e a profundidade de fila.  
    ![](media/premium-storage-performance/image6.png)

Não deve configurar profundidade de fila para qualquer valor elevado, mas para um valor ideal, o que pode fornecer suficiente IOPS para a aplicação sem afetar as latências. Por exemplo, se a latência de aplicação tem de ser 1 milissegundo, necessário para alcançar 5000 IOPS a profundidade de fila é, QD = 5000 x 0,001 = 5.

*Profundidade de fila para o Volume repartido*  
Para um volume repartido, manter uma profundidade de fila suficientemente elevada, de forma a que cada disco tem uma profundidade de fila de pico individualmente. Por exemplo, considere uma aplicação que envia uma profundidade de fila de 2 e existem 4 discos do stripe. Os pedidos de e/s dois passará para os dois discos e restantes dois discos serão inativo. Por conseguinte, deve configure a profundidade de fila de forma a que todos os discos podem estar ocupados. Fórmula abaixo mostra como determinar a profundidade de fila de volumes repartidos.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitação
Aprovisiona de Premium Storage do Azure especificado o número de IOPS e débito consoante os tamanhos de VM e tamanhos de disco que escolher. Sempre que a aplicação tenta unidade IOPS ou débito acima estes limites de que a VM ou disco processa, o armazenamento Premium limitá-lo. Isto manifestos sob a forma de degradação do desempenho na sua aplicação. Isto pode significar latência superior, reduzir o débito ou reduzir o IOPS. Se não limitar o armazenamento Premium, a aplicação foi completamente falhar por exceder o que são capazes de atingirem a total dos respetivos recursos. Por isso, para evitar problemas de desempenho devido à limitação, sempre Aprovisione os recursos suficientes para a sua aplicação. Tenha em consideração que discutimos na secções de tamanhos de disco acima e tamanhos de VM. Direcionamento de caminhos é a melhor forma de descobrir que recursos irá necessitar para alojar a aplicação.

## <a name="benchmarking"></a>Direcionamento de caminhos
Direcionamento de caminhos é o processo de simulando diferentes cargas de trabalho na sua aplicação e medir o desempenho da aplicação para cada carga de trabalho. Utilizando os passos descritos uma secção anterior, se recolheu os requisitos de desempenho da aplicação. Ao executar o direcionamento de caminhos ferramentas em VMs que aloja a aplicação, pode determinar os níveis de desempenho que pode alcançar a aplicação com o Premium Storage. Nesta secção, iremos fornecer exemplos de direcionamento de caminhos de uma VM DS14 padrão aprovisionado com discos Premium Storage do Azure.

Ter utilizámos ferramentas comuns de direcionamento de caminhos Iometer e FIO, para o Windows e Linux, respetivamente. Estas ferramentas gerar vários threads simulando uma produção, como a carga de trabalho e medem o desempenho do sistema. Utilizar as ferramentas também pode configurar parâmetros, como o bloco a profundidade tamanho e a fila, que normalmente não é possível alterar para uma aplicação. Isto dá-lhe uma maior flexibilidade para orientar o máximo desempenho numa escala elevada VM aprovisionado com discos premium para diferentes tipos de cargas de trabalho da aplicação. Para obter mais informações sobre cada ferramenta benchmarking visitam [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Para seguir os exemplos abaixo, crie uma VM DS14 padrão e anexe 11 discos de armazenamento Premium para a VM. 11 discos, configure 10 discos com o anfitrião a colocação em cache como "None" e stripe-los para um volume chamado NoCacheWrites. Configurar o anfitrião como "ReadOnly" colocação em cache no disco restante e criar um volume chamado CacheReads com este disco. Utilizar esta configuração, será capaz de ver o desempenho de leitura e escrita máximo de uma VM DS14 padrão. Para obter passos detalhados sobre como criar uma VM DS14 com discos premium, aceda a [criar e utilizar a conta de armazenamento Premium para um disco de dados de máquina virtual](../articles/virtual-machines/windows/premium-storage.md).

*Aquecer a Cache*  
O disco com o anfitrião só de leitura a colocação em cache poderá dar IOPS superior que o limite de disco. Para obter este desempenho de leitura máximo da cache do anfitrião, primeiro, tem warm cópias de segurança a cache deste disco. Isto garante que o IOs de leitura que ferramenta benchmarking provocarão no volume de CacheReads realmente pedidos com êxito a cache e não o disco diretamente. O resultado de acertos na cache em ESP adicionais da cache único ativada disco.

> **Importante:**  
> Tem warm a cache antes de executar o direcionamento de caminhos, sempre que a VM é reiniciada.
>
>

#### <a name="iometer"></a>Iometer
[Transferir a ferramenta de Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) na VM.

*Ficheiro de teste*  
Iometer utiliza um ficheiro de teste que é armazenado no volume no qual irá executar o teste benchmarking. -Unidades lê e escreve neste ficheiro de teste para medir o disco de IOPS e débito. Iometer cria este ficheiro de teste se não tiver sido fornecido um. Crie um ficheiro de teste de 200GB chamado iobw.tst nos volumes CacheReads e NoCacheWrites.

*Especificações de acesso*  
As especificações, pedir o tamanho de e/s, % leitura/escrita, % aleatório/sequenciais são configurados utilizando o separador de "Acesso especificações" no Iometer. Crie uma especificação de acesso para cada um dos cenários descritos abaixo. Criar as especificações de acesso e "Guardar" apropriadas com o nome como – RandomWrites\_8K, RandomReads\_8 K. Selecione a especificação correspondente ao executar o cenário de teste.

Um exemplo de especificações de acesso para o cenário de escrever IOPS máximo é mostrado abaixo,  
    ![](media/premium-storage-performance/image8.png)

*Especificações de teste IOPS máximo*  
Para demonstrar o IOPs máximo, utilize tamanho mais pequeno do pedido. Utilize o tamanho do pedido de 8K e criar especificações das escritas aleatórias e leituras.

| Especificação de acesso | Tamanho do pedido | % Aleatório | % De leitura |
| --- | --- | --- | --- |
| RandomWrites\_8 K |8K |100 |0 |
| RandomReads\_8 K |8K |100 |100 |

*Especificações de teste de débito máximo*  
Para demonstrar o débito máximo, utilize o maior tamanho do pedido. Utilize o tamanho do pedido de 64 KB e crie especificações das escritas aleatórias e leituras.

| Especificação de acesso | Tamanho do pedido | % Aleatório | % De leitura |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 KB |100 |0 |
| RandomReads\_64K |64 KB |100 |100 |

*Execução do teste Iometer*  
Execute os passos abaixo para transfira a cópia de segurança cache

1. Criar dois especificações de acesso com valores mostrados abaixo,

   | Nome | Tamanho do pedido | % Aleatório | % De leitura |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1MB |100 |0 |
   | RandomReads\_1 MB |1MB |100 |100 |
2. Execute o teste de Iometer para inicializar o disco de cache com os seguintes parâmetros. Utilize três threads de trabalho para o volume de destino e uma profundidade de fila de 128. Defina a duração "Tempo de execução" do teste para 2hrs no separador "Configuração de teste".

   | Cenário | Volume de destino | Nome | Duração |
   | --- | --- | --- | --- |
   | Inicialize o disco de Cache |CacheReads |RandomWrites\_1 MB |2hrs |
3. Execute o teste de Iometer para aquecer os discos de cache com os seguintes parâmetros. Utilize três threads de trabalho para o volume de destino e uma profundidade de fila de 128. Defina a duração "Tempo de execução" do teste para 2hrs no separador "Configuração de teste".

   | Cenário | Volume de destino | Nome | Duração |
   | --- | --- | --- | --- |
   | Transfira a cópia de segurança disco de Cache |CacheReads |RandomReads\_1 MB |2hrs |

Depois de disco de cache é preparado, prossiga com os cenários de teste listados abaixo. Para executar o teste de Iometer, utilizar, pelo menos, três threads de trabalho para **cada** volume de destino. Para cada thread de trabalho, selecione o volume de destino, defina a profundidade de fila e selecione uma das especificações de teste guardado, conforme mostrado na tabela abaixo, para executar o cenário de teste correspondente. A tabela também mostra resultados esperados para o IOPS e débito ao executar estes testes. Para todos os cenários, são utilizados um tamanho de e/s pequeno de 8KB e uma profundidade de fila elevada de 128.

| Cenário de teste | Volume de destino | Nome | resultado |
| --- | --- | --- | --- |
| Um máximo de IOPS de leitura |CacheReads |RandomWrites\_8 K |50 000 IOPS |
| Um máximo de Escrever IOPS |NoCacheWrites |RandomReads\_8 K |64,000 IOPS |
| Um máximo de IOPS combinada |CacheReads |RandomWrites\_8 K |100 000 IOPS |
| NoCacheWrites |RandomReads\_8 K | &nbsp; | &nbsp; |
| Um máximo de Ler MB/seg |CacheReads |RandomWrites\_64K |524 MB/seg |
| Um máximo de Escrever MB/seg |NoCacheWrites |RandomReads\_64K |524 MB/seg |
| Combinado MB/seg |CacheReads |RandomWrites\_64K |1000 MB/seg |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Seguem-se capturas de ecrã do Iometer resultados do teste para cenários combinados de IOPS e débito.

*IOPS máximo combinado de leituras e escritas*  
![](media/premium-storage-performance/image9.png)

*Débito máximo combinado de leituras e escritas*  
![](media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO
FIO é uma ferramenta popular para armazenamento benchmark em VMs do Linux. Tem a flexibilidade para selecionar os tamanhos de e/s diferentes, sequenciais ou aleatórias leituras e escritas. -Cria threads de trabalho ou os processos para efetuar as operações de e/s especificadas. Pode especificar o tipo de operações de e/s, que cada thread de trabalho tem de efetuar a utilizar ficheiros de tarefa. Foi criada uma tarefa ficheiro por cenário ilustrado nos exemplos abaixo. Pode alterar as especificações destes ficheiros de tarefa avaliar cargas de trabalho diferentes em execução no armazenamento Premium. Nos exemplos, estamos a utilizar uma execução de VM de 14 DS Standard **Ubuntu**. A utilizar a mesma configuração descrita no início do [direcionamento de caminhos de secção](#Benchmarking) e transfira a cópia de segurança a cache antes de executar os testes benchmarking.

Antes de começar, [transferir FIO](https://github.com/axboe/fio) e instalá-lo na sua máquina virtual.

Execute o seguinte comando para Ubuntu,

```
apt-get install fio
```

Utilizaremos quatro threads de trabalho para influenciar a operações de escrita e quatro threads de trabalho para despertar operações de leitura de discos. Os trabalhadores de escrita irão influenciar o tráfego no volume "nocache", que tem 10 discos com a cache de definido como "None". Os trabalhadores de leitura irão influenciar o tráfego no volume "readcache", cujo 1 disco com o conjunto de cache para "ReadOnly".

*Escrita máxima IOPS*  
Crie o ficheiro de tarefa com as seguintes especificações para obter o IOPS máximo de escrita. Nome "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Tenha em atenção as coisas de chave siga estão de acordo com as diretrizes de conceção abordadas nas secções anteriores. Estas especificações são essenciais para a unidade de IOPS máximo,  

* Uma profundidade de fila elevada de 256.  
* Um tamanho de pequeno bloco de 8KB.  
* Vários threads efetuar gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO para 30 segundos,  

```
sudo fio --runtime 30 fiowrite.ini
```

Enquanto executa o teste, poderá ver o número de escrita de IOPS a VM e os discos Premium são entrega. Como é mostrado no exemplo abaixo, a VM DS14 é a distribuição respetiva limite de IOPS de 50 000 IOPS de escrita máxima.  
    ![](media/premium-storage-performance/image11.png)

*Leitura máxima IOPS*  
Crie o ficheiro de tarefa com as seguintes especificações para obter o IOPS máximo de leitura. Nome "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Tenha em atenção as coisas de chave siga estão de acordo com as diretrizes de conceção abordadas nas secções anteriores. Estas especificações são essenciais para a unidade de IOPS máximo,

* Uma profundidade de fila elevada de 256.  
* Um tamanho de pequeno bloco de 8KB.  
* Vários threads efetuar gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO para 30 segundos,

```
sudo fio --runtime 30 fioread.ini
```

Enquanto executa o teste, poderá ver o número de leitura de IOPS a VM e os discos Premium são entrega. Como é mostrado no exemplo abaixo, a VM DS14 é a distribuição 64,000 mais de IOPS de leitura. Esta é uma combinação de disco e o desempenho da cache.  
    ![](media/premium-storage-performance/image12.png)

*IOPS máxima leitura e escrita*  
Criar o ficheiro de tarefa com as seguintes especificações para obter o máximo combinado de leitura e escrita IOPS. Nome "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Tenha em atenção as coisas de chave siga estão de acordo com as diretrizes de conceção abordadas nas secções anteriores. Estas especificações são essenciais para a unidade de IOPS máximo,

* Uma profundidade de fila elevada de 128.  
* Um tamanho de pequenos blocos de 4KB.  
* Vários threads efetuar aleatório lê e escreve.

Execute o seguinte comando para iniciar o teste FIO para 30 segundos,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Enquanto executa o teste, poderá ver o número de combinado leitura e escrita IOPS a VM e os discos Premium são entrega. Conforme mostrado no exemplo abaixo, a VM DS14 é a distribuição de IOPS de escrita e leitura de combinado mais de 100 000. Esta é uma combinação de disco e o desempenho da cache.  
    ![](media/premium-storage-performance/image13.png)

*Máximo combinado de débito*  
Para obter o máximo combinado de leitura e escrita débito, utilize um tamanho de bloco maior e a profundidade de fila grande com vários threads efetuar leituras e escritas. Pode utilizar um tamanho de bloco de 64KB e a profundidade de fila de 128.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o Premium Storage do Azure:

* [Armazenamento Premium: Armazenamento de Elevado Desempenho para Cargas de Trabalho de Máquinas Virtuais do Azure](../articles/virtual-machines/windows/premium-storage.md)  

Para utilizadores do SQL Server, leia os artigos sobre melhores práticas de desempenho para o SQL Server:

* [Melhores práticas do desempenho para o SQL Server em Virtual Machines do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Armazenamento Premium do Azure fornece mais elevado desempenho para o SQL Server na VM do Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)