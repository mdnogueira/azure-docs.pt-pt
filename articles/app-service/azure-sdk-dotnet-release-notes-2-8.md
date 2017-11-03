---
title: "Notas de versão do Azure SDK para .NET 2.8"
description: "Notas de versão do Azure SDK para .NET 2.8"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: de7207ff-ba4f-4008-9141-8742fcaa3254
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 0b9f55d69c824e86245738a082f95fc529583f58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK para .NET 2.8, 2.8.1 e 2.8.2
## <a name="overview"></a>Descrição geral
Este artigo contém as notas de versão (que inclui problemas conhecidos e alterações) para o Azure SDK para .NET 2.8, 2.8.1 e 2.8.2 versões. 

Para a lista completa das novas funcionalidades e atualizações efetuadas nesta versão, consulte o [Azure SDK 2.8 para Visual Studio 2013 e o Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) anúncio. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK para NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Transfira o Azure SDK para .NET 2.8
[Azure SDK para .NET 2.8 para Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK para .NET 2.8 para o Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Suporta a .NET 4.5.2
#### <a name="known-issues"></a>Problemas conhecidos
2.8 de SDK .NET do Azure permite-lhe criar .NET 4.5.2 pacotes do serviço em nuvem. No entanto 4.5.2 do .NET framework não será instalado no predefinido a versão de imagens de SO convidado até Janeiro de 2016 SO convidado. Antes dessa, 4.5.2 do .NET framework estarão disponível através de uma versão de lançamento de SO convidado separada – Novembro de 2015-02. Consulte o [versões de SO de convidado do Azure e matriz de compatibilidade de SDK](../cloud-services/cloud-services-guestos-update-matrix.md) página para controlar quando é lançada a imagem.  Depois da imagem do Novembro de 2015-02 é libertada pode optar por utilizar essa imagem atualizando o seu ficheiro de ficheiro (. cscfg) de configuração do serviço em nuvem. Na configuração de serviço ficheiro definir o atributo osVersion do elemento ServiceConfiguration para a cadeia "WA-convidado-SO-4.26_201511-02". Se optar por optar por utilizar esta imagem, em seguida, já não obterá as atualizações automáticas para o SO convidado. Para obter as atualizações automáticas de osVersion deve ser definido como "*" e a .NET 4.5.2 só estará disponível através de atualizações automáticas de Janeiro de 2016.

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>Problemas conhecidos
Durante uma **modelo da fábrica de dados** projeto criação que envolvem os dados de exemplo, o script de shell de energia do azure poderão falhar se a versão de shell de energia do azure instalado no computador é após 0.9.8.

Para poder criar com êxito este tipo de projeto, tem de instalar [versão do azure de power shell 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Ferramentas do Gestor de recursos do Azure
#### <a name="breaking-changes"></a>As alterações de última hora
O script do PowerShell fornecido pelo projeto do grupo de recursos do Azure foi atualizado nesta versão para trabalhar com os novos cmdlets do Azure PowerShell versão 1.0.  Este script novo não funcionará com o Visual Studio ao utilizar uma versão do SDK antes 2.8.  

Scripts de projetos criados em versões anteriores do SDK não serão executada no Visual Studio ao utilizar o SDK 2.8.  Todos os scripts irão continuar a trabalhar fora do Visual Studio com a versão adequada de cmdlets PowerShell do Azure.  

O SDK 2.8 requer a versão 1.0 dos cmdlets do PowerShell do Azure.  Todas as outras versões do SDK requerem a versão 0.9.8 dos cmdlets do PowerShell do Azure.  Para obter mais informações consulte [isto](http://go.microsoft.com/fwlink/?LinkID=623011) blogue.

### <a name="web-tools-extensions"></a>Web das ferramentas de extensões
#### <a name="known-issues"></a>Problemas conhecidos
Os seguintes problemas conhecidos serão resolvidos na versão seguinte.

* Serviço de aplicações relacionadas com o Cloud e Explorador de servidores não funcionam gesto para ambientes de não produção (como o Azure China ou clientes de pilha do Azure). Para os clientes nas seguintes áreas afetados, transferir o perfil de publicação do portal do Azure irá ativar a capacidade de publicação. Versão futura irá reparar gestos como "Depurador anexar" e "Ver registos de transmissão em fluxo" para o Azure China e os clientes de pilha. 
* Os clientes poderão ver erros durante a criação de quando as informações de aplicação de instância à qual são implementar está numa região diferente EUA leste do serviço de aplicações. Estes cenários, criar um serviço de aplicações no portal e transferir o perfil de publicação, irão ativar cenários de publicação. 

### <a name="azure-hdinsight-tools"></a>Ferramentas do Azure HDInsight
#### <a name="new-updates"></a>Novas atualizações
* Pode executar a consulta do Hive no cluster através de HiveServer2 com quase nenhuma sobrecarga e ver que a tarefa inicia sessão em tempo real.
* Utilizar a nova vista do Hive tarefas execução pode aprofundar para a tarefa mais aprofundada, encontrar mais detalhes e identificar potenciais problemas.

Para informações, consulte [Azure SDK 2.8 para Visual Studio 2013 e o Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK para .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Problemas conhecidos do Visual Studio 2013 e o Visual Studio 2015
1. Publica WebJob accionada ranhuras irá mostrar e o erro e não conjunto uma agenda, mas irá emitir o trabalho Web no Azure. Os clientes que estão que necessite de uma tarefa agendada, em seguida, podem utilizar o Portal do Azure para configurar o agendamento do trabalho Web. 
2. Os clientes de Python podem ter problemas de depurador. Equipa de serviço está a implementar uma correção para este mas se a clientes serem afetados,. permitem que a Microsoft sabe nos fóruns do ou no blogue do anúncio ou secção de comentários notas de versão. 
3. Os clientes em determinados regiões (por exemplo, Sul da Índia) irão ocorrer erros de aprovisionamento do serviço de aplicações. Isto é consistente com o portal e os clientes que ocorrer este problema podem utilizar o portal do Azure para pedir acesso para publicar estas georregiões. Uma vez que pedem acesso a estas regiões utilizando o aprovisionamento portal do Azure deve funcionar. 

## <a name="azure-sdk-for-net-282"></a>Azure SDK para .NET 2.8.2
Após a instalação do 2.8.2 ferramentas, os clientes podem ter o seguinte problema.         

* Se estiver a utilizar o Windows 10 e não tiver instalado o Internet Explorer, poderá receber um erro "Internet Explorer não foi possível encontrar".
  Para resolver o problema, instale o Internet Explorer utilizando a caixa de diálogo Adicionar/remover componentes do Windows.

Se observar este problema, utilize a funcionalidade de enviar um smile comunicá-lo.

Para obter mais informações, consulte [isto](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) post.

## <a name="other-updates"></a>Outras atualizações
Para outras atualizações, consulte [post de anúncio do Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Consulte também
[Post de anúncio do Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Suporte e informações de extinção para o Azure SDK para .NET e APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx)

