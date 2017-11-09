---
title: Integration runtime no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre integration runtime no Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/15/2017
ms.author: shlo
ms.openlocfilehash: 7851a24e7053e03cc28927ffae3a2b69a3291635
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration runtime no Azure Data Factory
O Integration Runtime (IR) é a infraestrutura de computação que o Azure Data Factory utiliza para proporcionar as seguintes capacidades de integração de dados em diferentes ambientes de rede:

- **Movimento de dados**: mover dados entre arquivos de dados na rede pública e arquivos de dados em redes privadas (no local ou redes privadas virtuais). Oferece suporte para conectores incorporados, conversão de formatos, mapeamento de colunas e transferência de dados dimensionável e de desempenho elevado.
- **Distribuição de atividades**: distribuir e monitorizar atividades de transformação em execução numa panóplia de serviços de computação, como o Azure HDInsight, o Azure Machine Learning, a Base de Dados SQL do Azure, o SQL Server, entre outros.
- **Execução de pacotes do SSIS**: executar, nativamente, pacotes do SQL Server Integration Services (SSIS) num ambiente de computação gerida do Azure.


> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja [Data Factory version 1 documentation](v1/data-factory-introduction.md) (Documentação da versão 1 do Data Factory).

No Data Factory, as atividades definem as ações que vão ser realizadas. Os serviços ligados definem um arquivo de dados ou um serviço de computação de destino. Os runtimes de integração estabelecem a ponte entre a atividade e os serviços ligados.  São referenciados pelo serviço ligado e fornecem o ambiente de computação em que a atividade é executada ou a partir do qual é distribuída.  Desta forma, a atividade pode ser realizada na região mais perto possível do arquivo de dados ou do serviço de computação de destino com o melhor desempenho possível, satisfazendo as necessidades de segurança e conformidade.

## <a name="integration-runtime-types"></a>Tipos de runtimes de integração
O Data Factory oferece três tipos de runtimes de integração e deve escolher aquele que melhor se adequa às capacidades de integração de dados e às necessidades de ambiente de rede de que está à procura.  Estes três tipos são:

- Azure
- Autoalojado
- Azure-SSIS

A tabela seguinte descreve as capacidades e o suporte de rede para cada um dos tipos de runtimes de integração:

Tipo de IR | Rede pública | Rede privada
------- | -------------- | ---------------
Azure | Movimento de dados<br/>Distribuição de atividades | &nbsp;
Autoalojado | Movimento de dados<br/>Distribuição de atividades | Movimento de dados<br/>Distribuição de atividades
Azure-SSIS | Execução de pacotes do SSIS | Execução de pacotes do SSIS

O diagrama seguinte mostra como os diferentes runtimes de integração podem ser utilizados em combinação para oferecer suporte de rede e capacidades de integração de dados avançadas:

![Diferentes tipos de runtimes de integração](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Runtime de integração do Azure
Os runtimes de integração do Azure podem:

- Executar atividades de cópia entre arquivos de dados na cloud
- Distribuir as atividades de transformação seguintes na rede pública: atividade Hive do HDInsight, atividade Pig do HDInsight, atividade MapReduce do HDInsight, atividade Spark do HDInsight, atividade Streaming do HDInsight, atividade Execução de Batch do Machine Learning, atividades Recursos de Atualização do Machine Learning, atividade Procedimento Armazenado, atividade U-SQL do Data Lake Analytics, atividade personalizada .Net, atividade Web, atividade de Pesquisa e atividade Obter Metadados.

### <a name="network-environment"></a>Ambiente de rede
O Runtime de Integração do Azure suporta a ligação a arquivos de dados e a serviços de computação na rede pública com pontos finais públicos acessíveis. Utilize um runtime de integração autoalojado para o ambiente Rede Virtual do Azure.

### <a name="compute-resource-and-scaling"></a>Recurso de computação e dimensionamento
Os runtimes de integração do Azure fornecem uma computação totalmente gerida e sem servidor no Azure.  Não tem de se preocupar com o aprovisionamento da infraestrutura, a instalação de software, as correções ou o dimensionamento de capacidades.  Além disso, apenas paga durante a utilização efetiva.

Os runtimes de integração do Azure proporcionam a computação nativa para mover dados entre arquivos de dados na cloud de forma segura, fiável e de elevado desempenho.  Pode definir o número de unidades de movimento de dados a utilizar na atividade de cópia e o tamanho da computação do IR do Azure é aumentado verticalmente de forma elástica em conformidade, sem que tenha de ajustar explicitamente o tamanho do Runtime de Integração do Azure.

A distribuição de atividades é uma operação simples para encaminhar a atividade para o serviço de computação de destino, para que não seja necessário aumentar verticalmente o tamanho de computação neste cenário.

Para obter informações sobre como criar e configurar um runtime de integração do Azure, veja How to create and configure Azure IR (Como criar e configurar um runtime de integração do Azure) nos guias de procedimentos. 

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Os runtimes de integração autoalojados podem:

- Executar a atividade de cópia entre arquivos de dados na cloud e um arquivo de dados numa rede privada.
- Distribuir as atividades de transformação seguintes para recursos de computação numa rede no local ou numa Rede Virtual do Azure: atividade Hive do HDInsight (BYOC), atividade Pig do HDInsight (BYOC), atividade MapReduce do HDInsight (BYOC), atividade Spark do HDInsight (BYOC), atividade Streaming do HDInsight (BYOC), atividade Execução de Batch do Machine Learning, atividades Recursos de Atualização do Machine Learning, atividade Procedimento Armazenado, atividade U-SQL do Data Lake Analytics, atividade personalizada .Net, atividade de Pesquisa e atividade Obter Dados.

> [!NOTE] 
> Utilize o runtime de integração autoalojado para suportar arquivos de dados que requeiram que utilize o seu próprio controlador, como SAP Hana, MySQL, etc.  Para obter mais informações, veja [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) (Arquivos de dados suportados).

### <a name="network-environment"></a>Ambiente de rede
Se quiser efetuar a integração de dados de forma segura num ambiente de rede privada, que não tem um campo de visão a partir do ambiente de rede pública, pode instalar um runtime de integração autoalojado no ambiente no local atrás da firewall emprsarial ou dentro de uma rede privada virtual.  O runtime de integração autoalojado só faz ligações de saída baseadas em HTTP para a Internet aberta.

### <a name="compute-resource-and-scaling"></a>Recurso de computação e dimensionamento
Os runtimes de integração autoalojados têm de ser instalados num computador no local ou numa máquina virtual que esteja numa rede privada. Atualmente, só suportamos a execução dos runtimes de integração autoalojados em sistemas operativos Windows.  

Para elevada disponibilidade e escalabilidade, pode aumentar horizontalmente o runtime de integração autoalojado ao associar a instância lógica a vários computadores no local no modo ativo-ativo.  Para obter mais informações, veja o tópico “how to create and configure self-hosted IR” (Como criar e configurar os runtimes de integração autoalojados) nos guias de procedimentos.

## <a name="azure-ssis-integration-runtime"></a>Runtime de integração Azure-SSIS
Para fazer a migração lift and shift de cargas de trabalho do SSIS existentes, pode criar um runtime de integração Azure-SSIS para executar nativamente pacotes do SSIS.

### <a name="network-environment"></a>Ambiente de rede
O runtime de integração Azure-SSIS pode ser aprovisionado na rede pública ou numa rede privada.  O acesso aos dados no local é suportado mediante a associação do runtime de integração Azure-SSIS a uma Rede Virtual (VNet) que esteja ligada à sua rede no local. Atualmente, só é suportada a VNet clássica. 

### <a name="compute-resource-and-scaling"></a>Recurso de computação e dimensionamento
O runtime de integração Azure-SSIS é um cluster totalmente gerido de VMs do Azure dedicadas à execução dos pacotes do SSIS. Pode utilizar o seu próprio servidor da Base de Dados SQL do Azure ou da Instância Gerida (pré-visualização privada) para alojar o catálogo dos projetos/pacotes (SSISDB) do SSIS que vão ser ligados ao mesmo. Pode aumentar verticalmente o poder da computação ao especificar o tamanho do nó e aumentá-lo horizontalmente ao definir o número de nós no cluster. Pode gerir o custo da execução do runtime de integração Azure-SSIS ao pará-lo e iniciá-lo, conforme achar mais adequado.

Para obter mais informações, veja o tópico “how to create and configure Azure-SSIS IR” (Como criar e configurar o runtime de integração Azure-SSIS) nos guias de procedimentos.  Depois de criado, pode implementar e gerir os seus pacotes do SSIS com poucas ou nenhumas alterações através de ferramentas familiares, como o SQL Server Data Tools (SSDT) e o SQL Server Management Studio (SSMS), tal e qual como utilizaria o SSIS no local.

Para obter mais informações sobre o runtime Azure-SSIS, veja os artigos seguintes: 

- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-deploy-ssis-packages-azure.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo é uma continuação do tutorial e mostra instruções sobre como utilizar a Instância Gerida do SQL do Azure (pré-visualização privada) e associar o IR a uma VNet. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Join an Azure-SSIS IR to a VNet](join-azure-ssis-integration-runtime-virtual-network.md) (Associar um IR Azure-SSIS a uma VNet). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure (VNet). Também descreve os passos para utilizar o portal do Azure para configurar a VNet, de modo a que o IR Azure-SSIS se possa associar à mesma. 

## <a name="determining-which-ir-to-use"></a>Determinar o runtime de integração a utilizar
Cada atividade de transformação tem um Serviço Ligado de destino, que aponta para um runtime de integração. É a partir desta instância de runtime de integração que a atividade de transformação é distribuída.

Para a atividade Cópia, precisa que os serviços ligados de origem e sink definam a direção do fluxo de dados. É utilizada a lógica seguinte para determinar que instância do runtime de integração é utilizada para fazer a cópia: 

- **Copiar entre duas origens de dados na cloud**: se os serviços ligados de origem e sink utilizarem o runtime de integração do Azure, a atividade Cópia é realizada com o runtime que o serviço ligado sink utiliza.
- **Copiar entre uma origem de dados na cloud e uma origem de dados numa rede privada**: se o serviço ligado de origem ou sink apontar para um runtime de integração autoalojado, a atividade Cópia é executada no mesmo.
- **Copiar entre duas origens de dados numa rede privada**: tanto o serviço ligado de origem, como sink, têm de apontar para a mesma instância do runtime de integração, o qual é utilizado para executar a atividade Cópia.

O diagrama seguinte mostra dois exemplos de atividades de cópia:

- Em Copy activitiy 1, a origem é um serviço ligado do SQL Server que referencia o runtime de integração autoalojado A e cujo sink é um serviço ligado do Armazenamento do Azure que referencia o runtime de integração do Azure B. Quando a atividade de cópia é executada, é-o no runtime de integração autoalojado A.
- Em Copy activity 2, a origem é um serviço ligado da Base de Dados SQL do Azure que referencia o runtime de integração do Azure C e cujo sink é um serviço ligado do Armazenamento do Azure que referencia o runtime de integração do Azure B. Quando a atividade de cópia é executada, é-o no runtime de integração do Azure B, pois é aquele que o serviço ligado sink utiliza.

![Que runtime de integração utilizar](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>Localização do runtime de integração
É na localização do Data Factory que os metadados da fábrica de dados são armazenados e a partir de onde o acionamento do pipeline é iniciado. Atualmente, as localizações do Data Factory suportadas são E.U.A. Leste e E.U.A. Leste 2. No entanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação. Este comportamento é realizado através do runtime de integração disponível globalmente em várias regiões, para garantir a conformidade dos dados, a eficiência e custos de saída de rede reduzidos.

A localização do runtime de integração define a localização da respetiva computação de back-end e, essencialmente, a localização onde são realizados o movimento de dados, a distribuição de atividades e a execução de pacotes do SSIS. A localização do runtime de integração pode ser diferente da localização da fábrica de dados à qual pertence. O diagrama seguinte mostra as definições de localização do Data Factory e os respetivos runtimes de integração:

![Localização do runtime de integração](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Runtime de integração do Azure
O Data Factory utiliza um runtime de integração do Azure na região que está mais perto do sink na mesma região para mover os dados. Veja os mapeamentos na tabela seguinte:

Geografia do arquivo de dados sink | Localização do arquivo de dados sink | Localização utilizada para o Runtime de Integração do Azure
-------------------------------| ----------------| ------------------
Estados Unidos | EUA Leste | EUA Leste
&nbsp; | EUA Leste 2 | EUA Leste 2
&nbsp; | EUA Central | EUA Central
&nbsp; | EUA Centro-Norte | EUA Centro-Norte
&nbsp; | EUA Centro-Sul | EUA Centro-Sul
&nbsp; | EUA Centro-Oeste | EUA Centro-Oeste
&nbsp; | EUA Oeste | EUA Oeste
&nbsp; | EUA Oeste 2 | EUA Oeste
Canadá | Leste do Canadá | Canadá Central
&nbsp; | Canadá Central | Canadá Central
Brasil | Sul do Brasil | Sul do Brasil
Europa | Europa do Norte | Europa do Norte
&nbsp; | Europa Ocidental | Europa Ocidental
Reino Unido | Reino Unido Oeste | Reino Unido Sul
&nbsp; | Reino Unido Sul | Reino Unido Sul
Ásia-Pacífico | Sudeste Asiático | Sudeste Asiático
&nbsp; | Ásia Oriental | Sudeste Asiático
Austrália | Leste da Austrália | Leste da Austrália
&nbsp; | Sudeste da Austrália | Sudeste da Austrália
Japão | Leste do Japão | Leste do Japão
&nbsp; | Oeste do Japão | Leste do Japão
Coreia | Coreia Central | Coreia Central
&nbsp; | Coreia do Sul | Coreia Central
Índia | Índia Central | Índia Central
&nbsp; | Índia Ocidental | Índia Central
&nbsp; | Sul da Índia | Índia Central

Também pode definir a resolução automática para a Localização de um runtime de integração do Azure, o que significa que o Data Factory tenta ao máximo detetar automaticamente a melhor localização a utilizar com base na definição do serviço ligado.

> [!NOTE] 
> Se a região do arquivo de dados de destino não estiver na lista ou não for detetável, a atividade falha em vez de ser executada numa região alternativa, por motivos de conformidade. Neste caso, indique explicitamente a localização alternativa a utilizar para efetuar a cópia.
 
A imagem seguinte mostra um exemplo da localização em vigor quando a localização do runtime de integração do Azure está definida como resolução automática. Quando é executada uma atividade de cópia, o runtime deteta a localização do destino dos dados; neste exemplo, é Oeste do Japão.  Com base na tabela, é utilizado um runtime de integração no Leste do Japão para realizar a cópia de dados real. Se o mesmo runtime de integração for utilizado para ligar ao HDInsight para uma atividade Spark, a submissão da aplicação Spark dá-se na localização do Data Facotry, que, neste exemplo, é E.U.A. Leste, e a execução real da aplicação Spark dá-se na localização do servidor HDInsight. 

![Localização efetiva](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>Runtime de integração autoalojado
O runtime de integração autoalojado é registado logicamente no Data Factory e a computação utilizada para suportar as funcionalidades do mesmo é fornecida por si. Por esse motivo, não existe uma propriedade de localização explícita para o runtime de integração autoalojado. 

Quando é utilizado para realizar o movimento de dados, o runtime de integração autoalojado extrai dados da origem e escreve-os no destino.

### <a name="azure-ssis-ir"></a>Runtime de integração Azure-SSIS
Selecionar a localização certa para o runtime de integração Azure-SSIS é fundamental para obter um elevado desempenho nos seus fluxos de trabalho extract-transform-load (ETL).  Estão disponíveis inicialmente duas localizações para pré-visualização (E.U.A. Leste e Europa do Norte).

- A localização do runtime de integração do Azure-SSIS não tem de ser a mesma da fábrica de dados, mas deve ser a mesma do seu próprio servidor Base de Dados SQL do Azure/Instância Gerida (pré-visualização privada) no qual o SSISDB vai ser alojado. Desta forma, o runtime de integração Azure-SSIS pode aceder facilmente ao SSISDB sem incorrer em tráfegos excessivos entre diferentes localizações.
- Se não tiver um servidor Base de Dados SQL do Azure/Instância Gerida (pré-visualização privada) já existente para alojar o SSISDB, mas tiver origens/destinos de dados no local, deve criar um servidor Base de Dados SQL do Azure/Instância Gerida (pré-visualização privada) novo na mesma localização de uma VNet ligada à sua rede no local.  Assim, pode criar o runtime de integração Azure-SSIS com o servidor Base de Dados SQL do Azure/Instância Gerida (pré-visualização privada) novo e associando essa VNet, tudo na mesma localização, minimizando eficazmente os movimentos de dados em localizações diferentes.
- Se a localização do seu servidor Base de Dados SQL do Azure/Instância Gerida (pré-visualização privada) já existente no qual o SSISDB está alojado não for a mesma de uma VNet ligada à sua rede no local, crie primeiro o runtime de integração Azure-SSIS com um servidor de Base de Dados SQL do Azure/Instância Gerida (pré-visualização privada) já existente e associando outra VNet na mesma localização e, em seguida, configure uma ligação VNet a VNet entre diferentes localizações.


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Create self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Criar o runtime de integração autoalojado)
- [Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Criar um runtime de integração Azure-SSIS). Este artigo é uma continuação do tutorial e mostra instruções sobre como utilizar a Instância Gerida do SQL do Azure (pré-visualização privada) e associar o IR a uma VNet. 
