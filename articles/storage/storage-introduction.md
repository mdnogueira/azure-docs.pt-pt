---
title: "Introdução ao Azure Storage | Microsoft Docs"
description: "Uma descrição geral do Storage do Azure, o armazenamento de dados online da Microsoft na nuvem. Saiba como utilizar a melhor solução de armazenamento na nuvem disponível nas aplicações."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/24/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: eec53d5dea1255572d515f1ab2796da681579d19
ms.lasthandoff: 04/29/2017


---
# <a name="introduction-to-microsoft-azure-storage"></a>Introdução ao Storage do Microsoft Azure

## <a name="overview"></a>Descrição geral
O Storage do Azure é a solução de armazenamento na nuvem para aplicações modernas que dependem da durabilidade, da disponibilidade e da escalabilidade para satisfazer as necessidades dos seus clientes. Ao ler este artigo, os programadores, os profissionais de TI e os decisores das empresas podem saber mais sobre:

* O que é o Storage do Azure e como pode tirar partido do mesmo na nuvem, no telemóvel, no servidor e nas aplicações de ambiente de trabalho
* Os tipos de dados que pode armazenar com os serviços do Storage do Azure: dados blob (objeto), dados da tabela NoSQL, mensagens de filas e partilhas de ficheiros.
* Como é gerido o acesso aos seus dados no Storage do Azure
* Como os dados do Storage do Azure se tornam duráveis através da redundância e da replicação
* O passo seguinte para criar a primeira aplicação do Storage do Azure

<!-- after our quick starts are available, replace this link with a link to one of those. 
Had to remove this article, it refers to the VS quickstarts, and they've stopped publishing them. Robin --> 
<!-- To get up and running with Azure Storage quickly, see [Get started with Azure Storage in five minutes](storage-getting-started-guide.md). -->

Para obter detalhes sobre as ferramentas, bibliotecas e outros recursos para trabalhar com o Storage do Azure, consulte o artigo [Passos Seguintes](#next-steps) abaixo.

## <a name="what-is-azure-storage"></a>O que é o Storage do Azure?
A informática em nuvem ativa novos cenários para aplicações que precisam de um armazenamento dimensionável, durável e de elevada disponibilidade, para os seus dados – que é exatamente por isso que a Microsoft desenvolveu o Storage do Azure. Além de permitir que os programadores criem aplicações em grande escala para suportar novos cenários, o Storage do Azure também fornece a base de armazenamento para Virtual Machines do Azure, uma prova adicional da sua robustez.

O Storage do Azure é extremamente dimensionável, pelo que pode armazenar e processar centenas de terabytes de dados para suportar os cenários de macrodados exigidos pela análise científica e financeira e pelas aplicações de suporte de dados. Em alternativa, pode armazenar as pequenas quantidades de dados necessárias para um Web site de pequenas empresas. Sejam quais forem as suas necessidades, paga apenas os dados que está a armazenar. Atualmente, o Storage do Azure armazena dezenas de biliões de objetos de clientes únicos e processa, em média, milhões de pedidos por segundo.

O Storage do Azure é elástico, pelo que pode conceber aplicações para uma grande audiência global e escalar essas aplicações conforme necessário – quer em termos da quantidade de dados armazenados, quer em termos do número de pedidos efetuados nele. Paga apenas o que utiliza e apenas quando o utiliza.

O Storage do Azure utiliza um sistema de criação de partições automático que efetua automaticamente o balanceamento de carga dos dados com base no tráfego. Isto significa que à medida que aumentam as exigências sobre a sua aplicação, o Storage do Azure atribui automaticamente os recursos adequados para as satisfazer.

O Armazenamento do Azure está acessível a partir de qualquer parte do mundo, a partir de qualquer tipo de aplicação, quer esteja a ser executado na cloud, no ambiente de trabalho, num servidor no local, num telemóvel ou num tablet. Pode utilizar o Storage do Azure em cenários móveis em que a aplicação armazena um subconjunto de dados no dispositivo e sincroniza-o com um conjunto completo de dados armazenados na nuvem.

O Storage do Azure suporta clientes que utilizam um conjunto diverso de sistemas operativos (incluindo o Windows e o Linux) e uma variedade de linguagens de programação (incluindo .NET, Java, Node.js, Python, Ruby, PHP e C++ e linguagens de programação móvel) para uma programação conveniente. O Storage do Azure também expõe os recursos de dados através das APIs REST, que estão disponíveis para qualquer cliente com capacidade para enviar e receber dados através de HTTP/HTTPS.

O Premium Storage do Azure fornece suporte de disco de elevado desempenho e de baixa latência para cargas de trabalho E/S intensivas executadas em Virtual Machines do Azure. Com o Premium Storage do Azure, pode anexar vários discos de dados persistentes a um computador virtual e configurá-los para satisfazer os seus requisitos de desempenho. Cada disco de dados está associado a um disco SSD no Premium Storage do Azure para um desempenho E/S máximo. Consulte o artigo [Premium Storage: Armazenamento de Elevado Desempenho para Cargas de Trabalho de Computadores Virtuais do Azure](storage-premium-storage.md) para obter mais detalhes.

## <a name="introducing-the-azure-storage-services"></a>Apresentação dos serviços de Armazenamento do Azure
O Storage do Azure fornece os seguintes quatro serviços: Blob Storage, Table Storage, Armazenamento de filas e File Storage.

* O Blob Storage armazena dados de objetos não estruturados. Um blob pode ser qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro multimédia ou o instalador da aplicação. O Blob Storage também é referido como um Armazenamento de objetos.
* O Table Storage armazena conjuntos de dados estruturados. O Table Storage é um arquivo de dados do atributo chave NoSQL, que permite um rápido desenvolvimento e um acesso rápido às grandes quantidades de dados.
* O Armazenamento de filas fornece mensagens fiáveis para o processamento do fluxo de trabalho e para a comunicação entre os componentes dos Cloud Services.
* O File Storage oferece um armazenamento partilhado para aplicações antigas utilizando o protocolo SMB padrão. Os Cloud Services em nuvem e as Virtual Machines do Azure podem partilhar dados de ficheiros em componentes da aplicação através de partilhas montadas e as aplicações no local podem aceder aos dados de ficheiros numa partilha através da API REST do serviço Ficheiros.

Uma conta do Storage do Azure é uma conta segura que lhe dá acesso a serviços no Storage do Azure. A conta do Storage fornece o espaço de nomes exclusivo para os seus recursos de armazenamento. A imagem abaixo mostra as relações entre os recursos do Storage do Azure numa conta do Storage:

![Recursos do Storage do Azure](./media/storage-introduction/storage-concepts.png)

[!INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[!INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Armazenamento de blobs
Para os utilizadores com grandes quantidades de dados de objetos não estruturados para armazenar na nuvem, o Blob Storage oferece uma solução económica e dimensionável. Pode utilizar o Blob Storage para armazenar conteúdos, tais como:

* Documentos
* Dados de rede social, tais como fotografias, vídeos, música e blogues
* Cópias de segurança de ficheiros, computadores, bases de dados e dispositivos
* Imagens e texto para Web Apps
* Dados de configuração para aplicações em nuvem
* Macrodados, tais como registos e outros grandes conjuntos de dados

Cada blob está organizado num contentor. Os contentores também fornecem uma forma útil de atribuir políticas de segurança a grupos de objetos. Uma conta do Storage pode conter qualquer número de contentores e um contentor pode conter qualquer número de blobs, até ao limite de capacidade de 500 TB da conta do Storage.

O Blob Storage oferece três tipos de blobs: blobs de blocos, blobs de acréscimo e blobs de página (discos).

* Os blobs de blocos estão otimizados para transmitir em fluxo e armazenar os objetos da nuvem e são uma boa opção para armazenar documentos, ficheiros multimédia, cópias de segurança, etc.
* Os blobs de acréscimo são semelhantes aos blobs de blocos, mas estão otimizados para as operações de acréscimo. Um blob de acréscimo apenas pode ser atualizado adicionando um novo bloco ao final. Os blobs de acréscimo são uma boa opção para cenários como o registo, onde novos dados têm de ser escritos apenas no final do blob.
* Os blobs de página estão otimizados para representar discos IaaS e suportar escritas aleatórias e podem ter um tamanho máximo de 1 TB. Um disco IaaS anexado a uma rede de Virtual Machines do Azure é um VHD armazenado como um blob de páginas.

Para grandes conjuntos de dados em que as restrições de rede fazem com que o carregamento ou a transferência de dados para o Blob Storage seja irrealista, pode enviar um disco rígido para a Microsoft para importar ou exportar dados diretamente a partir do datacenter. Consulte o artigo [Utilizar o Serviço de Importação/Exportação do Microsoft Azure para Transferir Dados para o Blob Storage](storage-import-export-service.md).

## <a name="table-storage"></a>Table Storage
As aplicações modernas exigem frequentemente arquivos de dados com maior escalabilidade e flexibilidade do que as gerações anteriores do software necessário. O Table Storage oferece um armazenamento de elevada disponibilidade e extremamente dimensionável para que a aplicação possa ser automaticamente dimensionada para satisfazer o pedido do utilizador. O Armazenamento de tabelas é um arquivo de chaves/atributos NoSQL da Microsoft – tem um design sem esquemas, o que o diferencia das tradicionais bases de dados relacionais. Com um arquivo de dados sem esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O Table Storage é fácil de utilizar para que os programadores possam criar aplicações rapidamente. O acesso aos dados é rápido e rentável para todos os tipos de aplicações.  Geralmente, o custo do Table Storage é significativamente inferior ao SQL tradicional para volumes de dados semelhantes.

O Table Storage é um arquivo de atributo chave, o que significa que cada valor numa tabela é armazenado com um nome de propriedade escrito. O nome da propriedade pode ser utilizado para filtrar e especificar critérios de seleção. Uma coleção de propriedades e os respetivos valores compõem uma entidade. Uma vez que o Table Storage não tem esquemas, duas entidades na mesma tabela podem conter diferentes coleções de propriedades e essas propriedades podem ser de diferentes tipos.

Pode utilizar o Table Storage para armazenar conjuntos de dados flexíveis, tais como os dados do utilizador para Web Apps, livros de endereços, informações do dispositivo e qualquer outro tipo de metadados necessários para o seu serviço.  Pode armazenar quantas entidades pretender numa tabela e uma conta do Storage pode conter quantas tabelas pretender, até ao limite de capacidade da conta do Storage.

Tal como com os Blobs e as Filas, os programadores podem gerir e aceder ao Table Storage através de protocolos REST padrão, no entanto, o Table Storage também suporta um subconjunto do protocolo OData, simplificando capacidades de consulta avançadas e permitindo os formatos JSON e AtomPub (com base em XML).

Para as aplicações baseadas na Internet de hoje, as bases de dados NoSQL, tal como uma oferta do Table Storage, oferecem uma alternativa popular às bases de dados relacionais tradicionais.

## <a name="queue-storage"></a>Armazenamento de filas
Ao conceber aplicações para o dimensionamento, os componentes da aplicação, muitas vezes, são desacoplados para um dimensionamento independente. O Armazenamento de filas oferece uma solução de mensagens fiável para uma comunicação assíncrona entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Uma conta do Storage pode conter qualquer número de filas. Uma fila pode conter qualquer número de mensagens, até ao limite de capacidade da conta do Storage. As mensagens individuais podem ter até 64 KB de tamanho.

## <a name="file-storage"></a>Armazenamento de ficheiros
O File Storage do Azure oferece partilhas de ficheiros SMB com base na nuvem para que possa migrar aplicações antigas que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. Com o File Storage do Azure, as aplicações em execução nos Virtual Machines ou nos Cloud Services do Azure podem montar uma partilha de ficheiros na nuvem, tal como uma aplicação de ambiente de trabalho monta uma partilha SMB típica. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do File Storage em simultâneo.

Uma vez que as partilhas do Armazenamento de ficheiros são partilhas de ficheiros SMB padrão, as aplicações em execução no Azure podem aceder a dados na partilha através das APIs de E/S do sistema de ficheiros. Os programadores, por conseguinte, podem tirar partido do respetivo código existente e competências para migrar as aplicações existentes. Os profissionais de TI podem utilizar os cmdlets do PowerShell para criar, montar e gerir partilhas de File Storage como parte da administração de aplicações Azure.

Tal como os outros serviços do Storage do Azure, o File Storage expõe uma API REST para aceder aos dados numa partilha. As aplicações no local podem chamar a API REST do Armazenamento de Ficheiros para aceder aos dados numa partilha de ficheiros. Desta forma, uma empresa pode optar por migrar algumas aplicações antigas para o Azure e continuar a executar outras dentro da própria organização. Tenha em atenção que montar uma partilha de ficheiros só é possível para aplicações em execução no Azure; uma aplicação no local só pode aceder à partilha de ficheiros através da API REST.

As aplicações distribuídas também podem utilizar o File Storage para armazenar e partilhar dados da aplicação úteis e ferramentas de testes e de desenvolvimento. Por exemplo, uma aplicação pode armazenar ficheiros de configuração e dados de diagnóstico, tais como registos, métricas e informações de falhas de sistema numa partilha do File Storage para que fiquem disponíveis para várias funções e computadores virtuais. Os programadores e os administradores podem armazenar utilitários de que necessitam para criar ou gerir uma aplicação numa partilha do File Storage que está disponível para todos os componentes, em vez de os instalar em cada computador virtual ou instância de função.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Aceder aos recursos de Blob, Tabela, Fila e Ficheiro
Por predefinição, apenas o proprietário da conta do Storage pode aceder a recursos na conta do Storage. Para garantir a segurança dos seus dados, cada pedido efetuado relativamente aos recursos na sua conta tem de ser autenticado. A autenticação depende de num modelo de Chave partilhada. Os blobs também podem ser configurados para suportar a autenticação anónima.

Durante a criação, são atribuídas duas chaves de acesso privado à conta do Storage que são utilizadas para a autenticação. Ter duas chaves garante que a sua aplicação permanece disponível quando regularmente regenera as chaves como uma prática de gestão de chaves de segurança comum.

Se precisa de permitir o acesso controlado pelos utilizadores aos seus recursos de armazenamento, em seguida, pode criar uma assinatura de acesso partilhado. Uma assinatura de acesso partilhado (SAS) é um token que pode ser anexado a um URL que ativa o acesso delegado a um recurso de armazenamento. Qualquer pessoa que tiver o token pode aceder ao recurso ao qual aponta com as permissões que especifica, durante o período de tempo é válido. A partir da versão 2015-04-05, o Storage do Azure suporta dois tipos de assinaturas de acesso partilhado: o serviço SAS e a conta SAS.

O serviço SAS delega o acesso a um recurso em apenas um dos serviços de armazenamento: o serviço Blob, Fila, Tabela ou Ficheiro.

Uma conta SAS delega o acesso aos recursos em um ou mais dos serviços de armazenamento. Pode delegar o acesso às operações de nível de serviço que não estão disponíveis com um serviço SAS. Também pode delegar o acesso às operações de leitura, escrita e eliminação em contentores de blobs, tabelas, filas e partilhas de ficheiros que não são permitidos com um serviço SAS.

Por fim, pode especificar que um contentor e os respetivos blobs, ou um blob específico, estão disponíveis para acesso público. Quando indicar que um contentor ou um blob é público, todas as pessoas podem lê-lo anonimamente. Não é necessária autenticação.  Os blobs e os contentores públicos são úteis para expor recursos, tais como ficheiros multimédia e documentos que estão alojados em Web sites.  Para diminuir a latência de rede para um público global, pode colocar em cache dados blob utilizados por Web sites com o Azure CDN.

Veja [Utilizar Assinaturas de Acesso Partilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md) para obter mais informações sobre assinaturas de acesso partilhado. Consulte o artigo [Gerir o acesso de leitura anónimo a contentores e blobs](storage-manage-access-to-resources.md) e [Autenticação para os Serviços do Storage do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx) para mais informações sobre o acesso seguro à sua conta do Storage.

## <a name="replication-for-durability-and-high-availability"></a>Replicação para durabilidade e elevada disponibilidade
Os dados na sua conta de armazenamento do Microsoft Azure são sempre replicados para garantir a durabilidade e a elevada disponibilidade. A replicação copia os dados, no mesmo centro de dados ou para um segundo centro de dados, consoante a opção de replicação que escolher. A replicação protege os dados e preserva o tempo ativo da sua aplicação em caso de falhas de hardware transitórias. Se os dados são replicados para um segundo centro de dados, esta ação também protege os dados contra uma falha catastrófica na localização primária.

A replicação garante que a sua conta de armazenamento cumpre o [Contrato de Nível de Serviço (SLA) para o Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo em caso de falhas. Leia o SLA para obter mais informações sobre as garantias do Armazenamento do Azure quanto à durabilidade e à disponibilidade.

Quando cria uma conta de armazenamento, tem de selecionar uma das seguintes opções de replicação:

* **Armazenamento localmente redundante (LRS).** O Armazenamento localmente redundante mantém três cópias dos seus dados. O LRS é replicado três vezes num único centro de dados numa única região. O LRS protege os dados de falhas normais de hardware, mas não da falha de um único centro de dados.

    O LRS é oferecido com um desconto. Para a máxima durabilidade, recomendamos que utilize o armazenamento georredundante, descrito abaixo.
* **Armazenamento com redundância de zona (ZRS).** O Armazenamento com redundância de zona mantém três cópias dos seus dados. O ZRS é replicado três vezes em dois ou três locais, numa única região ou em três regiões, fornecendo uma durabilidade superior ao LRS. O ZRS garante que os seus dados são duráveis numa única região.

    O ZRS fornece um nível mais elevado de durabilidade do que o LRS. No entanto, para uma durabilidade máxima, recomendamos que utilize o armazenamento georredundante, descrito abaixo.

  > [!NOTE]
  > Atualmente, o ZRS só está disponível para blobs de blocos e só é suportado para as versões de 14/02/2014 e posteriores.
  >
  > Depois de criar a sua conta do Storage e selecionado o ZRS, não é possível convertê-lo para utilizar qualquer outro tipo de replicação ou vice-versa.
  >
  >
* **Armazenamento georredundante (GRS)**. O GRS mantém seis cópias dos seus dados. Com a GRS, os dados são replicados três vezes numa região principal e também são replicados três vezes numa região secundária a centenas de quilómetros de distância da região primária, fornecendo o nível mais elevado de durabilidade. Se ocorrer uma falha na região primária, o Storage do Azure terá uma ativação pós-falha para a região secundária. O GRS garante que os seus dados são duráveis em duas regiões separadas.

    Para obter informações sobre emparelhamentos primários e secundários por região, veja [Regiões do Azure](https://azure.microsoft.com/regions/).
* **Armazenamento georredundante com acesso de leitura (RA-GRS)**. O Armazenamento georredundante com acesso de leitura replica os dados para uma localização geográfica secundária e também proporciona acesso de leitura aos seus dados na localização secundária. O Armazenamento georredundante com acesso de leitura permite-lhe aceder aos seus dados a partir da localização principal ou secundária, no caso de uma localização ficar indisponível. O Armazenamento georredundante com acesso de leitura é a opção predefinida para a sua conta de armazenamento por predefinição aquando da respetiva criação.

  > [!IMPORTANT]
  > Pode alterar como os dados são replicados depois de criar a sua conta do Storage, a menos que tenha especificado o ZRS quando criou a conta. No entanto, tenha em atenção que pode ser cobrado um custo único adicional relativo à transferência de dados se mudar do LRS para o GRS ou o RA-GRS.
  >
  >

Consulte o artigo [Replicação do Storage do Azure](storage-redundancy.md) para obter detalhes adicionais sobre as opções de replicação do armazenamento.

Para informações sobre os preços da replicação da conta do Storage, consulte o artigo [Preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/). Veja [Regiões do Azure](https://azure.microsoft.com/regions/#services) para obter mais informações sobre os serviços que estão disponíveis em cada região.

Para obter detalhes da arquitetura sobre a durabilidade com o Storage do Azure, consulte o [Artigo SOSP – Storage do Azure: um Serviço de Armazenamento na Nuvem de Elevada Disponibilidade com Consistência Forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferir dados de e para o Armazenamento do Azure
Pode utilizar o utilitário da linha de comandos do AzCopy para copiar os dados de blob, de ficheiro e de tabela na sua conta do Storage ou em várias contas do Storage. Consulte o artigo [Transferir dados com o utilitário da linha de comandos do AzCopy](storage-use-azcopy.md) para obter mais informações.

O AzCopy é criado sobre a [Biblioteca de Movimento de Dados do Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), que está atualmente disponível na pré-visualização.

O serviço de Importação/Exportação do Azure fornece uma forma de importar ou exportar dados do blob de ou para a sua conta do Storage através de um disco rígido enviado por correio para o datacenter do Azure. Para obter mais informações sobre o serviço de Importação/Exportação, consulte o artigo [Utilizar o Serviço de Importação/Exportação do Microsoft Azure para Transferir Dados para o Blob Storage](storage-import-export-service.md).

## <a name="pricing"></a>Preços
[!INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>APIs de armazenamento, bibliotecas e ferramentas
É possível aceder aos recursos do Storage do Azure por qualquer idioma que consiga efetuar pedidos de HTTP/HTTPS. Além disso, o Storage do Azure oferece bibliotecas de programação para vários idiomas populares. Estas bibliotecas simplificam muitos aspetos do trabalho com o Storage do Azure ao processar detalhes como a invocação síncrona e assíncrona, a criação de batches de operações, a gestão de exceções, as tentativas automáticas, o comportamento operacional, etc. Atualmente, as bibliotecas estão disponíveis para os seguintes idiomas e plataformas, com outros no pipeline:

### <a name="azure-storage-data-services"></a>Serviços de dados do Armazenamento do Azure
* [API REST dos Serviços de Armazenamento](http://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Biblioteca de Clientes de Armazenamento para o .NET, o Windows Phone e o Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Biblioteca de Clientes de Armazenamento para C++](https://github.com/Azure/azure-storage-cpp)
* [Biblioteca de Clientes de Armazenamento do Azure para Java/Android](https://azure.microsoft.com/develop/java/)
* [Biblioteca de Clientes de Armazenamento para Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Biblioteca de Clientes de Armazenamento para PHP](https://azure.microsoft.com/develop/php/)
* [Biblioteca de Clientes de Armazenamento para Ruby](https://azure.microsoft.com/develop/ruby/)
* [Biblioteca de Clientes de Armazenamento para Python](https://azure.microsoft.com/develop/python/)
* [Cmdlets de Armazenamento para o PowerShell 1.0](/powershell/module/azurerm.storage/#storage)

### <a name="azure-storage-management-services"></a>Serviços de gestão de Armazenamento do Azure
* [Referência da API REST do Fornecedor de Recursos de Armazenamento](/rest/api/storagerp/)
* [Biblioteca de Clientes do Fornecedor de Recursos de Armazenamento para o .NET](/dotnet/api/microsoft.azure.management.storage)
* [Cmdlets do Fornecedor de Recursos de Armazenamento para o PowerShell 1.0](/powershell/module/azure.storage)
* [API REST da Gestão de Serviços de Armazenamento (Clássica)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Serviços do movimento de dados do Armazenamento do Azure
* [API REST do Serviço de Importação/Exportação de Armazenamento](storage-import-export-service.md)
* [Biblioteca de Clientes do Movimento de Dados de Armazenamento para o .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Ferramentas e utilitários
* [Explorador do Armazenamento do Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Ferramentas de Cliente do Armazenamento do Azure](storage-explorers.md)
* [Azure SDKs e Ferramentas](https://azure.microsoft.com/tools/)
* [Emulador de Armazenamento do Azure](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [Utilitário da Linha de Comandos do AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Storage do Azure, explore estes recursos:

### <a name="documentation"></a>Documentação
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Criar uma conta de armazenamento](storage-create-storage-account.md)

<!-- after our quick starts are available, replace this link with a link to one of those. 
Had to remove this article, it refers to the VS quickstarts, and they've stopped publishing them. Robin --> 
<!--* [Get started with Azure Storage in five minutes](storage-getting-started-guide.md)
-->

### <a name="for-administrators"></a>Para administradores
* [Using Azure PowerShell with Azure Storage (Utilizar o Azure PowerShell com o Armazenamento do Azure)](storage-powershell-guide-full.md)
* [Utilizar a CLI do Azure com o Armazenamento do Azure](storage-azure-cli.md)

### <a name="for-net-developers"></a>Para programadores do .NET
* [Introdução ao armazenamento de Blobs do Azure através do .NET](storage-dotnet-how-to-use-blobs.md)
* [Introdução ao armazenamento de Tabelas do Azure através do .NET](storage-dotnet-how-to-use-tables.md)
* [Introdução ao Armazenamento de filas do Azure através do .NET](storage-dotnet-how-to-use-queues.md)
* [Introdução ao Armazenamento de Ficheiros do Azure no Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Para orogramadores de Java/Android
* [Como utilizar o Armazenamento de blobs do Java](storage-java-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de tabelas do Java](storage-java-how-to-use-table-storage.md)
* [Como utilizar o Armazenamento de filas do Java](storage-java-how-to-use-queue-storage.md)
* [Como utilizar o Armazenamento de ficheiros do Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Para programadores de Node.js
* [Como utilizar o Armazenamento de blobs do Node.js](storage-nodejs-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de tabelas do Node.js](storage-nodejs-how-to-use-table-storage.md)
* [Como utilizar o Armazenamento de filas do Node.js](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Para programadores de PHP
* [Como utilizar o Armazenamento de blobs do PHP](storage-php-how-to-use-blobs.md)
* [Como utilizar o Armazenamento de tabelas do PHP](storage-php-how-to-use-table-storage.md)
* [Como utilizar o Armazenamento de filas do PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Para programadores de Ruby
* [Como utilizar o Armazenamento de blobs do Ruby](storage-ruby-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de tabelas do Ruby](storage-ruby-how-to-use-table-storage.md)
* [Como utilizar o Armazenamento de filas do Ruby](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Para programadores de Python
* [Como utilizar o Armazenamento de blobs do Python](storage-python-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de tabelas do Python](storage-python-how-to-use-table-storage.md)
* [Como utilizar o Armazenamento de filas do Python](storage-python-how-to-use-queue-storage.md)
* [Como utilizar o Armazenamento de ficheiros do Python](storage-python-how-to-use-file-storage.md)

