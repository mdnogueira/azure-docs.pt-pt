---
title: "Notas de versão do Azure SDK para .NET 2.7 e .NET 2.7.1"
description: "Notas de versão do Azure SDK para .NET 2.7 e .NET 2.7.1"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: 877d070a-9bd5-49b3-8fac-6bb5f65c3554
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 9a69253129cdedc4f5d7e736d5bd8d6a68f95a1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Notas de versão do Azure SDK para .NET 2.7 e .NET 2.7.1
## <a name="overview"></a>Descrição geral
Este documento contém as notas de versão para o Azure SDK para .NET 2.7 versão. 

O documento contém também as notas de versão para o Azure SDK para .NET 2.7.1 de versão.

Azure SDK 2.7 só é suportado no Visual Studio 2015 e Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) é o último suportados SDK para Visual Studio 2012.

Para obter informações detalhadas sobre esta versão, consulte [post de anúncio do Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) e [post de anúncio do Azure SDK 2.7.1](http://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Azure SDK para .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Inicie sessão no melhoramentos para Visual Studio 2015
2.7 de SDK do Azure para Visual Studio 2015 suporta as novas funcionalidades de gestão de identidade no Visual Studio 2015.  Isto inclui suporte para as contas de aceder ao Azure através do controlo de acesso baseado em funções, os fornecedores de soluções de nuvem, DreamSpark e outros tipos de conta e subscrição.

As melhorias de início de sessão incluídas com o Azure SDK 2.7 só estão disponíveis no Visual Studio 2015. Suporte para Visual Studio 2013 está incluído no SDK do Azure 2.7.1.

### <a name="mobile-sdk"></a>SDK móvel
Atualizado **Mobile Apps** modelos para refletir mais recentes [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e processo de configuração.

### <a name="service-bus"></a>Service Bus
Correções de erros gerais e melhoramentos. Para obter detalhes sobre atualizações e funcionalidades, consulte as notas de versão da versão mais recente [NuGet do Service Bus](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>Ferramentas do HDInsight
Nesta versão, foram efetuadas as seguintes atualizações. Estas atualizações estão na pré-visualização. Para obter mais informações, consulte [este blogue](http://go.microsoft.com/fwlink/?LinkId=619108).

* Gráficos de ramo de registo para do Hive no Tez tarefas
* Suporte IntelliSense de DML do Hive completo
* Suporte de modelo do PIg
* Modelos de Storm para serviços do Azure

#### <a name="breaking-changes"></a>As alterações de última hora
* Antigo **Storm** projeto tem de ser atualizado quando utilizar esta versão das ferramentas. Para obter mais informações, consulte [este blogue](http://go.microsoft.com/fwlink/?LinkId=619108).
* Visual Studio Web Express já não é suportada. Para obter mais informações, consulte [este blogue](http://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Ferramentas do App Service do Azure
Nesta versão foram efetuadas as seguintes atualizações para extensões de ferramentas do Web. Para obter mais informações consulte [isto](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blogue. 

* Suporte para contas de DreamSpark adicionadas
* Alteração completa para as ferramentas do Azure feitas para suportar as novas APIs de gestão de recursos do Azure
* Adicionado suporte para o App Service do Azure para [Cloud Explorer](#cloud_explorer)

#### <a name="known-issues"></a>Problemas conhecidos
Nós de ranhura de implementação de aplicação Web não aparecem no nó de ranhuras no Explorador de servidores e nós de subordinados de ranhura de implementação de aplicação Web não carregar no Cloud Explorer. Este problema foi resolvido e está preparado para a próxima versão do SDK. 

### <a name="cloud_explorer"></a>Explorador de nuvem para o Visual Studio 2015
Azure SDK 2.7 inclui Cloud Explorer para o Visual Studio 2015 que lhe permite ver os recursos do Azure, verifique as respetivas propriedades e efetuar ações de chave para programadores no Visual Studio. 

Explorador de nuvem suporta o seguinte:

* Vistas de grupo de recursos e o tipo de recurso dos seus recursos do Azure 
* Procurar recursos por nome (disponível na vista de tipo de recurso)
* Suporte para as subscrições e recursos que tenham a função de acesso baseado em controlo (RBAC) aplicado 
* Painel ação integrada que mostra as ações centrados na programação específicos de recursos selecionados. Por exemplo: Anexar Depurador remoto para máquinas virtuais criadas com a pilha do Resource Manager, ver os dados de diagnóstico para máquinas virtuais etc.
* Painel de propriedades integrada, que mostra as propriedades centrados na programação frequentemente necessárias durante a dev/teste 
* A mudança rápida da conta a utilizar ao enumerar os recursos (utilize o comando de definições na barra de ferramentas) 
* Filtragem das subscrições a utilizar ao enumerar os recursos (utilize o comando de definições na barra de ferramentas) 
* Ligações avançadas para o Portal do Azure para a gestão de recursos e os grupos de recursos 

### <a name="azure-resource-manager-tools"></a>Ferramentas do Gestor de recursos do Azure
Foram atualizadas as ferramentas do Gestor de recursos do Azure para trabalhar com o controlo de acesso baseado em ' (RBAC) da função e novos tipos de subscrição.  Incluído com estas alterações, é a capacidade de utilizar novas contas de armazenamento, além do armazenamento clássico, para armazenar artefactos durante a implementação.  

Se estiver a utilizar um projeto do grupo de recursos do Azure de uma versão anterior do SDK com o SDK 2.7, é necessário um novo script de implementação para implementar uma nova conta de armazenamento a utilizar em vez de armazenamento clássico.  Será solicitado antes de serem efetuadas alterações ao seu projeto para adicionar o novo script.  O script antigo será possível mudar o nome e terá de efetuar manualmente quaisquer modificações para o novo script.

### <a name="storage-explorer-tools"></a>Ferramentas do Explorador de armazenamento
* Suporte para visualizar os Blobs de acréscimo. Obter mais informações no [esta mensagem de blogue](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Suporte para a visualização de contas do Premium Storage através do Explorador de servidores. Explorador de servidores apresentará apenas blobs de páginas para contas de armazenamento premium como são o único tipo suportado para contas de armazenamento premium.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Ferramentas de fábrica de dados do Azure para Visual Studio
Introdução ao **as ferramentas do Azure Data Factory** para Visual Studio. Seguem-se as funcionalidades de ativados. Consulte [este blogue](http://go.microsoft.com/fwlink/?LinkId=617530) para obter mais informações.

* **Modelo baseado criação**: selecione cased para utilização com base em modelos, modelos de movimento de dados ou modelos de processamento de dados para implementar uma solução de integração de dados de ponto a ponto e começar a utilizar prática rapidamente com o Data Factory. 
* **Integração com o Explorador de soluções para criar e implementar entidades do Data Factory**: criar e implementar a pipelines e entidades relacionadas como projetos do Visual Studio. 
* **Integração com o visual interação durante a criação da vista Diagrama**: visualmente Criar pipelines e conjuntos de dados com auxiliar da vista de diagrama. 
* **Integração com o Explorador de servidores para navegação e a interação com entidades já implementadas**: tirar partido do Explorador de servidores para procurar já implementada fábricas de dados e de entidades correspondentes. Importe uma fábrica de dados implementado ou qualquer entidade (Pipeline, o serviço ligado, conjuntos de dados) para o seu projeto. 
* **Editar JSON com o intellisense avançada e validação do esquema**: configurar de forma eficiente e editar documentos JSON de entidades do Data Factory com validação intellisense e esquema avançada 
* **Publicação de ambiente de várias**: publicar criadas pipelines para desenvolvimento, teste ou num ambiente Prod através da criação de ficheiros de configuração separadas para cada ambiente.
* **PIg, Hive e .net suporte de processamento de dados baseado no**: suporte para o Pig e Scripts Hive no projeto do Data Factory. Suporte para referenciar o projeto c# para gerir .net atividade.

## <a name="azure-sdk-for-net-271"></a>Azure SDK para .NET 2.7.1
A secção seguinte contém atualizações que foram introduzidas com o Azure SDK para .NET 2.7.1 de versão.

### <a name="hdinsight-tools"></a>Ferramentas do HDInsight
Para obter mais explicação sobre as atualizações de ferramentas do HDInsight, consulte [este blogue](http://go.microsoft.com/fwlink/?LinkId=623831).

* Vista de operador de tarefa do Hive (uma nova funcionalidade de)
  
    Para ajudar a compreender a sua consulta do Hive melhor, a vista operador do Hive funcionalidade foi adicionada. Para ver todos os operadores dentro de um vértice, faça duplo clique nos vértices do gráfico da tarefa. Para ver mais detalhes sobre um operador específico, coloque o cursor sobre esse operador.
* Marcador de erro do ramo de registo (uma nova funcionalidade de)
  
    De forma instantânea, que lhe permite ver os erros de gramática foi adicionada a funcionalidade de marcador de ramo de registo de erro. Além disso, as mensagens de erro foram melhoradas e agora, pode ver erros detalhados gramática instantaneamente (até nesta versão, era necessário que submeter um script de ramo de registo para o cluster e aguarde algum tempo antes de obter detalhes sobre a sua mensagem de erro).  
* Gráfico de topologia do Storm (uma nova funcionalidade de)
  
    Visualizar é muito importante quando pretende ver se a topologia está a funcionar conforme esperado. Nesta versão adicionámos de visualização de gráficos do Storm. Pode visualizar as métricas importantes para a topologia (por exemplo, uma cor indica Meteorologia um determinado Bolt "ocupada" ou não). Pode também faça duplo clique Spout/Bolt para ver mais detalhes.
* Suporte para clusters do HDInsight que foram criadas no Portal do Azure (uma correção de erros)
  
    Agora, pode utilizar o Visual Studio para ver e submeter as tarefas para todos os seus clusters do HDInsight, independentemente de onde o cluster foi criado.
* Mais suporte IntelliSense & mais rápida do Hive metadados carregar (um melhoramento)
  
    Iremos tem melhorado o IntelliSense adicionando mais sugestões de amigável de utilizador. Por exemplo, o alias de tabela pode agora também ser sugerido na IntelliSense para pode escrever a consulta mais facilmente. Além disso, vamos tem melhorado os metadados do Hive carregar para que apenas irá demorar vários segundos para listar todas as bases de dados, tabelas e colunas do seu metastore do Hive.

Para obter mais explicação sobre as atualizações de ferramentas do HDInsight, consulte [este blogue](http://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>Melhoramentos no Visual Studio 2013
* SDK do Azure 2.7.1 permite que o Visual Studio 2013 aceder a contas do Azure e as subscrições através de controlo de acesso baseado em funções, os fornecedores de soluções de nuvem e Dreamspark.
* Com o Azure SDK 2.7.1, a nova janela de ferramenta de Cloud Explorer agora também está disponível no Visual Studio 2013.

### <a name="known-issues"></a>Problemas conhecidos
Instalar o 2.6 de SDK do Azure ou 2.7.1 para o Visual Studio Community 2013 num não - inglês SO apresentará um aviso que os recursos em inglês e não inglesas do Visual Studio podem não corresponder. Este aviso pode ser dispensado em segurança. Esta situação ocorrerá apenas se a máquina não continha uma instalação anterior do Visual Studio Community 2013 e estiver a instalar o SDK num não - inglês SO. Depois do pacote de idiomas aplica-se os recursos RTM para Visual Studio, mas antes de esta aplica-se a atualização 4, é apresentado o aviso. Dispensar o aviso permitirá que o pacote de idiomas continuar e concluir a aplicar a versão de atualização 4 do conteúdo do pacote de idiomas.

Projetos do LightSwitch não são compatibile com esta versão. Este problema será resolvido com a versão seguinte do SDK.

## <a name="also-see"></a>Consulte também
[SDK do Azure 2.7.1 post de anúncio](http://go.microsoft.com/fwlink/?LinkId=623850)

[Post de anúncio 2.7 do SDK do Azure](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Suporte e informações de extinção para o Azure SDK para .NET e APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

