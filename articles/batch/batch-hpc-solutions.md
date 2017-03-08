---
title: "Soluções Batch e HPC na cloud - Azure | Microsoft Docs"
description: "Saiba mais sobre cenários de computação em lote e de alto desempenho (HPC e Macrocomputação) e as opções das soluções no Azure"
services: batch, virtual-machines, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: aab5401d-2baf-4cf2-bf20-ad224de33888
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: 288bd4dc0bde4cb610efc4a6d015e5b9499eb0ee
ms.lasthandoff: 02/28/2017


---
# <a name="batch-and-hpc-solutions-for-large-scale-computing-workloads"></a>Soluções de Batch e HPC para cargas de trabalho de computação em grande escala

O Azure oferece soluções na nuvem eficientes e dimensionáveis para computação de lotes e de alto desempenho (HPC), também conhecida como *Macrocomputação*. Saiba mais aqui sobre cargas de trabalho de Macrocomputação e os serviços do Azure para suportá-las ou vá diretamente para [cenários de soluções](#scenarios) posteriormente neste artigo. Este artigo destina-se principalmente a decisores, gestores de TI e fabricantes independentes de software, mas outros programadores e profissionais de TI podem utilizá-lo para se familiarizarem com estas soluções.

As organizações têm problemas informáticos em grande escala, incluindo a arquitetura e a análise da engenharia, a composição de imagens, modelação complexa, simulações Monte Carlo , cálculos de riscos financeiros, entre outros. O Azure ajuda as organizações a resolver estes problemas com os recursos, a escala e a agenda de que precisam. Com o Azure, as organizações podem:

* Criar soluções híbridas, expandir um cluster HPC no local para descarregamento de cargas de trabalho de pico para a nuvem
* Executar ferramentas e cargas de trabalho de clusters HPC completamente no Azure
* Utilizar serviços geridos e dimensionáveis do Azure, como o [Batch](https://azure.microsoft.com/documentation/services/batch/), para executar cargas de trabalho de computação intensivas sem ter de implementar e gerir a infraestrutura de computação

Apesar de estar fora do âmbito deste artigo, o Azure também fornece aos programadores e parceiros um conjunto completo de capacidades, opções de arquitetura e ferramentas de desenvolvimento para criar fluxos de trabalho de Macrocomputação em grande escala e personalizados. E um ecossistema de parceiros crescente está pronto para ajudá-lo a tornar as suas cargas de trabalho de Macrocomputação produtivas na nuvem do Azure.

## <a name="batch-and-hpc-applications"></a>Aplicações Batch e HPC
Ao contrário de aplicações Web e muitas aplicações de linha de negócio, as aplicações em lote e HPC têm um início e um fim definidos, e estes podem ser executados com base numa agenda ou a pedido, por vezes, para horas ou mais tempo. A maioria enquadra-se em duas categorias principais: *intrinsecamente paralelas* (por vezes denominadas "constrangedoramente paralelas", porque os problemas que podem resolver são executados em paralelo em vários computadores ou processadores) e *fortemente conjugados*. Veja a tabela seguinte para obter mais informações sobre estes tipos de aplicação. Algumas soluções do Azure abordam o trabalho melhor para um tipo ou outro.

> [!NOTE]
> Em soluções Batch e HPC, uma instância em execução de uma aplicação é normalmente denominada um *trabalho*, e cada trabalho pode ser dividido em *tarefas*. E os recursos de computação em cluster para a aplicação são muitas vezes denominados *nós de computação*.
> 
> 

| Tipo | Características | Exemplos |
| --- | --- | --- |
| **Intrinsecamente paralelo**<br/><br/>![Intrinsecamente paralelo][parallel] |• Computadores individuais executam independentemente a lógica de aplicação<br/><br/> • Adicionar computadores permite que a aplicação amplie e reduza o tempo de computação<br/><br/>• A aplicação consiste em executáveis separados ou está dividida num grupo de serviços invocado por um cliente (uma arquitetura orientada para serviços ou SOA, aplicação) |• Modelação de riscos financeiros<br/><br/>•Composição de imagem e processamento de imagem<br/><br/>• Codificação e transcodificação de suporte de dados<br/><br/>• Simulações Monte Carlo<br/><br/>• Testes de software |
| **Fortemente conjugados**<br/><br/>![Fortemente conjugados][coupled] |• A aplicação necessita de nós de computação para interagir ou trocar resultados intermédios<br/><br/>• Os nós de computação podem comunicar com a Interface de Passagem de Mensagens (MPI), um protocolo de comunicações comuns para computação paralela<br/><br/>• A aplicação é sensível à latência da rede e largura de banda<br/><br/>• O desempenho de aplicações pode ser melhorado com tecnologias de rede de alta velocidade, tais como InfiniBand e acesso remoto direto à memória (RDMA) |• Modelação de reservatório de petróleo ou gás<br/><br/>• Arquitetura e análise de engenharia, como dinâmica computacional fluída<br/><br/>• Simulações físicas, como acidentes de viação e reações nucleares<br/><br/>• Previsão meteorológica |

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Considerações para a execução de aplicações em lote e HPC na nuvem
Pode migrar prontamente várias aplicações que foram concebidas para serem executadas em clusters HPC no local para o Azure ou para um ambiente híbrido (em vários locais). No entanto, poderão existir algumas limitações ou considerações, incluindo:

* **Disponibilidade dos recursos na nuvem** - dependendo do tipo de recursos de computação em nuvem que utiliza, poderá não confiar na disponibilidade contínua das máquinas enquanto um trabalho é executado. O processamento do estado e a anotação da verificação do progresso são técnicas comuns para processar possíveis falhas transitórias e mais necessárias quando são utilizados recursos na nuvem.
* **Acesso a dados** - as técnicas de acesso a dados normalmente disponíveis em clusters empresariais, tais como NFS, podem precisar de configuração especial na nuvem. Ou poderá ter de adotar práticas e padrões de acesso a dados diferentes para a nuvem.
* **Movimento de dados** - para aplicações que processam grandes quantidades de dados, são necessárias estratégias para mover os dados para o armazenamento na nuvem e para recursos de computação. Poderá precisar de rede de alta velocidade entre vários locais, como o [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Considere também as limitações legais, de regulamentação ou de política para armazenar ou aceder a esses dados.
* **Licenciamento** - entre em contacto com o fornecedor de qualquer aplicação comercial para obter as restrições de licenciamento, ou outras, para executar na nuvem. Nem todos os fornecedores oferecem licenciamento pay as you go. Poderá ter de planear um servidor de licenciamento na nuvem da sua solução ou ligar a um servidor de licenças no local.

### <a name="big-compute-or-big-data"></a>Macrocomputação ou Macrodados?
A linha divisória entre as aplicações de Macrocomputação e Macrodados nem sempre é evidente, e algumas aplicações podem ter características das duas. Ambas envolvem executar computação em grande escala, normalmente em clusters de computadores. Mas as abordagens de solução e ferramentas de suporte podem ser diferentes.

• **Macrocomputação** tende a envolver aplicações que dependem de energia e memória da CPU, tais como simulações de engenharia, modelação de riscos financeiros e composição digital. A infraestrutura de uma solução de Macrocomputação poderá incluir computadores com processadores especializados com vários núcleos para efetuar computação não processada e hardware de rede de alta velocidade para ligar os computadores.

• Os **Macrodados** solucionam problemas de análise de dados que envolvem grandes quantidades de dados que não podem ser geridos por um único computador ou sistema de gestão de base de dados. Alguns exemplos incluem grandes volumes de registos Web ou de outros dados de business intelligence. Os Macrodados tendem a depender mais da capacidade de disco e do desempenho de E/S do que no poder da CPU. Também existem ferramentas de Macrodados especializadas, como Apache Hadoop, para gerir o cluster e a partição dos dados. (Para obter informações sobre o Azure HDInsight e outras soluções Hadoop do Azure, veja [Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## <a name="compute-management-and-job-scheduling"></a>Gestão de computação e agendamento de trabalho
Muitas vezes, a execução de aplicações Batch e HPC inclui um *gestor de clusters* e um *agendador de trabalho* para ajudar a gerir os recursos de computação em cluster e alocá-los às aplicações que executam os trabalhos. Estas funções podem ser conseguidas através de ferramentas separadas ou uma ferramenta ou serviço integrado.

* **Gestor de clusters** - aprovisiona, inicia e gere recursos de computação (ou nós de computação). Um gestor de clusters poderá automatizar a instalação de imagens do sistema operativo e aplicações em nós de computação, dimensionar recursos de computação de acordo com as exigências e monitorizar o desempenho dos nós.
* **Agendador de trabalho** - especifica os recursos (como processadores ou memória) de que uma aplicação precisa e as condições de quando é executada. Um agendador de trabalho mantém uma fila de trabalhos e atribui recursos aos mesmos, com base numa prioridade atribuída ou outras características.

As ferramentas de clustering e de agendamento para clusters baseados em Linux e baseados em Windows podem migrar bem para o Azure. Por exemplo, o [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), solução de cluster de cálculo gratuita da Microsoft para cargas de trabalho Windows e Linux HPC, oferece várias opções para a execução no Azure. Também pode criar clusters do Linux para executar as ferramentas de código aberto, como Torque e SLURM. Também pode levar soluções de grelha comerciais para o Azure, como o [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/), o [IBM Spectrum Symphony and Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/) e o [Univa Grid Engine](http://www.univa.com/products/grid-engine).

Conforme mostrado nas seguintes secções, também pode tirar partido de serviços do Azure para gerir recursos de computação e agendar trabalhos sem (ou além de) ferramentas tradicionais de gestão de clusters.

## <a name="scenarios"></a>Cenários
Seguem-se três cenários comuns para executar cargas de trabalho de Macrocomputação no Azure através de soluções de cluster HPC existentes, de serviços do Azure ou de uma combinação dos dois. As considerações-chave para escolher cada cenário são listadas, mas não são exaustivas. Posteriormente neste artigo, pode obter mais informações sobre os serviços do Azure disponíveis que pode utilizar na sua solução.

| Cenário | Por que motivo escolhê-lo? |
| --- | --- | --- |
| **Impulsar um cluster HPC para o Azure**<br/><br/>[![Impulsão de cluster][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Saiba mais:<br/>• [ às instâncias de trabalho do Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configurar um cluster de cálculo híbrido com o HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [ Aceder ao Azure Batch com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/> |• Combinar o [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) ou outro cluster no local com recursos do Azure adicionais numa solução híbrida.<br/><br/>• Expandir as cargas de trabalho de Macrocomputação para serem executadas em instâncias de máquina virtual de Plataforma como Serviço (PaaS) (atualmente apenas para o Windows Server).<br/><br/>• Aceder a um servidor de licenças ou arquivo de dados no local, com uma rede virtual do Azure opcional |
| **Criar um cluster HPC inteiramente no Azure**<br/><br/>[![Cluster no IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Saiba mais:<br/>• [Soluções de cluster HPC no Azure](big-compute-resources.md)<br/><br/> |• Implementar rápida e consistentemente as suas aplicações e ferramentas de cluster em máquinas virtuais padrão ou personalizadas de infraestrutura como um serviço (IaaS) do Windows ou Linux.<br/><br/>• Executar várias cargas de trabalho de Macrocomputação com a solução de agendamento de trabalho por si escolhida.<br/><br/>• Utilizar serviços Azure adicionais, incluindo redes e armazenamento para criar soluções completas baseadas na nuvem. |
| **Aumentar horizontalmente uma aplicação paralela para o Azure**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Saiba mais:<br/>• [Noções Básicas do Azure Batch](batch-technical-overview.md)<br/><br/>• [Introdução à biblioteca do Azure Batch para .NET](batch-dotnet-get-started.md) |• Programar com o [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) para aumentar horizontalmente várias cargas de trabalho de Macrocomputação para serem executadas em conjuntos de máquinas virtuais do Windows ou do Linux.<br/><br/>• Utilizar um serviço de plataforma do Azure para gerir a implementação e o dimensionamento automático de máquinas virtuais, agendamento de trabalhos, recuperação após desastre, movimento de dados, gestão de dependência e implementação de aplicações. |

## <a name="azure-services-for-big-compute"></a>Serviços do Azure para Macrocomputação
Seguem-se mais informações sobre computação, dados, redes e serviços relacionados que pode combinar para soluções e fluxos de trabalho de Macrocomputação. Para obter orientações aprofundadas sobre os serviços do Azure, veja a [documentação](https://azure.microsoft.com/documentation/) dos serviços do Azure. Os [cenários](#scenarios) anteriores no artigo mostram apenas algumas formas de utilizar estes serviços.

> [!NOTE]
> O Azure apresenta regularmente novos serviços que podem ser úteis para o seu cenário. Se tiver dúvidas, contacte um [parceiro Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) ou envie um e-mail para *bigcompute@microsoft.com*.
> 
> 

### <a name="compute-services"></a>Serviços de computação
Os serviços de computação do Azure são o núcleo de uma solução de Macrocomputação e os diferentes serviços de computação oferecem vantagens para diferentes cenários. Num nível básico, estes serviços oferecem modos diferentes para que as aplicações sejam executadas em instâncias de computação baseadas em máquina virtual que o Azure fornece com a tecnologia do Windows Server Hyper-V. Estas instâncias podem executar ferramentas e sistemas operativos Windows e Linux padrão e personalizados. O Azure dá-lhe a escolher os [tamanhos das instâncias](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) com configurações diferentes de núcleos CPU, memória, capacidade do disco e outras características. Dependendo das suas necessidades, pode dimensionar as instâncias para milhares de núcleos e, em seguida, reduzir verticalmente quando precisar de menos recursos.

> [!NOTE]
> Tire partido das [instâncias de computação intensivas como a série H](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) do Azure para melhorar o desempenho e escalabilidade das cargas de trabalho HPC. Estas instâncias também suportam aplicações MPI paralelas que requerem uma rede de aplicação de baixa latência e de alta débito. Também estão disponível VMs da [série N](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/) com NVIDIA GPUs para expandir o intervalo de cenários de computação e de visualização no Azure.  
> 
> 

| Serviço | Descrição |
| --- | --- |
| **[Máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Fornecem infraestrutura como serviço (IaaS) de computação com a tecnologia Microsoft Hyper-V<br/><br/>• Permitem-lhe aprovisionar e gerir de forma flexível computadores na nuvem persistentes, a partir de imagens Windows Server ou Linux padrão a partir do [Azure Marketplace](https://azure.microsoft.com/marketplace/), ou discos de dados e imagens que fornece<br/><br/>• Podem ser implementadas e geridas como [Conjuntos de Dimensionamento VM](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) para criar serviços em grande escala a partir de máquinas virtuais idênticas, com dimensionamento automático para aumentar ou diminuir automaticamente a capacidade<br/><br/>• Executam ferramentas de cluster de cálculo e aplicações no local, inteiramente na nuvem<br/><br/> |
| **[Serviços em nuvem](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Podem executar as aplicações de Macrocomputação em instâncias de função de trabalho, que são máquinas virtuais que executam uma versão do Windows Server e são geridas completamente pelo Azure<br/><br/>• Ativam aplicações dimensionáveis e fiáveis com sobrecarga administrativa reduzida, em execução num modelo de plataforma como serviço (PaaS)<br/><br/>• Podem requerer ferramentas ou desenvolvimento adicionais para integrar com soluções de cluster HPC no local |
| **[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Executa cargas de trabalho paralelas e em lote em grande escala, num serviço completamente gerido<br/><br/>• Fornece o agendamento de trabalho e dimensionamento automático de um conjunto gerido de máquinas virtuais<br/><br/>• Permite aos programadores criar e executar aplicações como um serviço ou ativar aplicações existentes na nuvem<br/> |

### <a name="storage-services"></a>Serviços de armazenamento
Normalmente, uma solução de Macrocomputação opera num conjunto de dados de entrada e gera dados para obter os respetivos resultados. Alguns dos serviços de armazenamento do Azure utilizados em soluções de Macrocomputação incluem:

* [Blob, tabela e armazenamento de filas](https://azure.microsoft.com/documentation/services/storage/) - gira grandes quantidades de dados não estruturados, dados NoSQL e mensagens para o fluxo de trabalho e comunicação, respetivamente. Por exemplo, poderá utilizar o armazenamento de blobs para grandes conjuntos de dados técnicos ou para as imagens de entrada ou ficheiros de suporte de dados que a aplicação processa. Poderá utilizar filas para comunicação assíncrona numa solução. Veja [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md).
* [Armazenamento de ficheiros do Azure](https://azure.microsoft.com/services/storage/files/) - partilha ficheiros e dados comuns no Azure com o protocolo SMB padrão, que é necessário para algumas soluções de cluster HPC.
* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) - proporciona um Sistema de Ficheiros Distribuído do Apache Hadoop de hiperescala para a nuvem, particularmente útil para análise de lotes, em tempo real e interativa.

### <a name="data-and-analysis-services"></a>Serviços de dados e análise
Alguns cenários de Macrocomputação envolvem fluxos de dados em grande escala ou geram dados que precisam de processamento ou análise adicional. O Azure disponibiliza vários serviços de análise e de dados, incluindo:

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - cria fluxos de trabalho (pipelines) condicionados por dados que associam, agregam e transformam dados de arquivos de dados no local, baseados na nuvem e na Internet.
* [Base de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/) - fornece as funcionalidades-chave de um sistema de gestão de base de dados relacional do Microsoft SQL Server num serviço gerido.
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - implementa e aprovisiona clusters do Windows Server ou baseados em Linux Hadoop Apache na nuvem para gerir, analisar e elaborar relatórios sobre macrodados.
* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) - ajuda-o a criar, testar, utilizar e gerir soluções de análise preditiva num serviço completamente gerido.

### <a name="additional-services"></a>Serviços adicionais
A sua solução de Macrocomputação poderá necessitar de outros serviços do Azure para ligar a recursos no local ou outros ambientes. Os exemplos incluem:

* [Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) - cria uma secção logicamente isolada no Azure para ligar recursos do Azure uns aos outros ou ao seu datacenter no local. Com uma rede virtual de vários locais, as aplicações de Macrocomputação podem aceder a dados no local, a serviços do Active Directory e a servidores de licença.
* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) - cria uma ligação privada entre os datacenters da Microsoft e a infraestrutura no local ou num ambiente de colocalização. O ExpressRoute oferece mais segurança, mais fiabilidade, velocidades mais rápidas e latências mais baixas do que as ligações típicas através da Internet.
* [Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) - fornece vários mecanismos para que as aplicações comuniquem ou troquem dados, quer estes estejam no Azure, noutra plataforma em nuvem ou num centro de dados.

## <a name="next-steps"></a>Passos seguintes
* Veja [Recursos Técnicos para Batch e HPC](big-compute-resources.md) para obter conselhos técnicos para criar a sua solução.
* Discuta as suas opções do Azure com parceiros, incluindo a Computação Cíclica, Redimensionamento e o UberCloud.
* Leia sobre soluções de Macrocomputação do Azure fornecidas por [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/),[ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/) [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).
* Para os anúncios mais recentes, veja o [blogue da equipa do Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e o [blogue do Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

