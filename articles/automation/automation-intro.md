---
title: "O que é a Automatização do Azure | Microsoft Docs"
description: "Saiba que valor fornece a Automatização do Azure e obtenha respostas a perguntas comuns para que pode começar a criar, utilizando runbooks e o Automation DSC do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
keywords: "o que é a automatização, automatização do azure, exemplos de automatização do azure"
ms.assetid: 0cf1f3e8-dd30-4f33-b52a-e148e97802a9
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2016
ms.author: magoedte;bwren
ms.openlocfilehash: c9f5c346350a62540ba29444b1b00cc9dc398d56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-overview"></a>Descrição geral da Automatização do Azure
A Automatização do Microsoft Azure fornece uma forma de os utilizadores automatizarem as tarefas manuais, de execução longa, propensas ao erro e frequentemente repetidas que normalmente são executadas num ambiente de nuvem e empresarial. Poupa tempo, aumenta a fiabilidade das tarefas administrativas normais e agenda-as para serem executadas automaticamente em intervalos regulares. Pode automatizar os processos utilizando runbooks ou automatizar a gestão de configuração utilizando a Configuração de Estado Pretendido. Este artigo fornece uma breve descrição geral de Automatização do Azure e responde a algumas perguntas comuns. É possível consultar outros artigos nesta biblioteca para informações mais detalhadas sobre os diferentes tópicos.

## <a name="automating-processes-with-runbooks"></a>Automatizar processos com runbooks
Um runbook é um conjunto de tarefas que efetuam algum processo automatizado na Automatização do Azure. Pode ser um processo simples, como iniciar uma máquina virtual e criar uma entrada de registo, ou pode ter um runbook complexo que combina outros runbooks mais pequenos para efetuar um processo complexo através de vários recursos ou mesmo várias clouds e ambientes no local.  

Por exemplo, pode ter um processo manual existente para truncar uma SQL Database que esteja a alcançar o tamanho máximo que inclua vários passos, tais como ligar ao servidor, ligar à base de dados, obter o tamanho atual da base de dados, verificar se foi excedido o limiar e, em seguida, truncá-lo e notificar o utilizador. Em vez de efetuar manualmente cada um destes passos, pode criar um runbook que executará todas estas tarefas como um único processo. Inicie o runbook, forneça as informações necessárias, tais como o nome do servidor SQL, o nome da base de dados e o e-mail do destinatário e, em seguida, descontraia enquanto o processo é concluído. 

## <a name="what-can-runbooks-automate"></a>O que podem automatizar os runbooks?
Os runbooks na Automatização do Azure são baseados no Windows PowerShell ou no Fluxo de Trabalho do Windows PowerShell, pelo que fazem tudo o que o PowerShell pode fazer. Se uma aplicação ou serviço tiver uma API, um runbook pode trabalhar com a mesma. Se tiver um módulo do PowerShell para a aplicação, pode carregar esse módulo para a Automatização do Azure e incluir estes cmdlets no runbook. Os runbooks da Automatização do Azure executados na nuvem do Azure e podem aceder a quaisquer recursos em nuvem ou recursos externos que podem ser acedidos a partir da nuvem. Ao utilizar a [Função de Trabalho de Runbook Híbrida](automation-hybrid-runbook-worker.md), os runbooks podem ser executados no seu datacenter local para gerir recursos locais. 

## <a name="getting-runbooks-from-the-community"></a>Obter runbooks a partir da comunidade
A [Galeria de Runbooks](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contém runbooks da Microsoft e da comunidade que pode utilizar inalterados no seu ambiente ou personalizados para os seus fins. Também são úteis como referências para aprender a criar os seus próprios runbooks. Até pode contribuir os seus próprios runbooks para a galeria que pensa que outros utilizadores poderão considerar úteis. 

## <a name="creating-runbooks-with-azure-automation"></a>Criar Runbooks com a Automatização do Azure
Pode [criar os seus próprios runbooks](automation-creating-importing-runbook.md) a partir do zero ou modificar runbooks a partir da [Galeria de Runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx), conforme os seus requisitos. Pode escolher de entre quatro [tipos de runbooks](automation-runbook-types.md) diferentes com base nos seus requisitos e na sua experiência com o PowerShell. Se preferir trabalhar diretamente com o código do PowerShell, pode utilizar um [Runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) que edita offline ou com o [editor de texto](http://msdn.microsoft.com/library/azure/dn879137.aspx) no portal do Azure. Se preferir editar um runbook sem estar exposto do código subjacente, pode criar um [Runbook gráfico](automation-runbook-types.md#graphical-runbooks) utilizando o [editor gráfico](automation-graphical-authoring-intro.md) no portal do Azure. 

Prefere ver do que ler? Veja o vídeo abaixo da sessão do Microsoft Ignite de maio de 2015. Nota: apesar de os conceitos e as funcionalidades descritos neste vídeo estarem corretos, a Automatização do Azure tem progredido muito desde que o vídeo foi gravado, agora tem uma IU mais extensa no portal do Azure e suporta funcionalidades adicionais.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3451/player]
> 
> 

## <a name="automating-configuration-management-with-desired-state-configuration"></a>Gestão da configuração da automatização com a Configuração de Estado Pretendido
O [PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) é uma plataforma de gestão que lhe permite gerir, implementar e impor uma configuração para anfitriões físicos e máquinas virtuais utilizando uma sintaxe do PowerShell declarativa. Pode definir configurações num Servidor de Solicitação do DSC central que as máquinas de destino podem obter e aplicar automaticamente. O DSC fornece um conjunto de cmdlets do PowerShell que pode utilizar para gerir recursos e configurações.  

O [Automation DSC do Azure](automation-dsc-overview.md) é uma solução baseada na nuvem para o PowerShell DSC que fornece serviços necessários para ambientes empresariais.  Pode gerir os recursos do DSC na Automatização do Azure e aplicar configurações em máquinas virtuais ou físicas provenientes de um Servidor de Solicitação do DSC na nuvem do Azure.  Fornece também reporting services que o informam de eventos importantes como, por exemplo, quando nós se desviaram da respetiva configuração atribuída e quando uma nova configuração tiver sido aplicada. 

## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Criar as suas configurações do DSC com a Automatização do Azure
As [Configurações do DSC](automation-dsc-overview.md) especificam o estado pretendido de um nó.  Vários nós podem aplicar a mesma configuração para garantir que todos mantêm um estado idêntico.  Pode criar uma configuração utilizando qualquer editor de texto na sua máquina local e, em seguida, importá-la para a Automatização do Azure, onde pode compilá-la e aplicar nós à mesma.

## <a name="getting-modules-and-configurations"></a>Obter módulos e configurações
Pode obter [módulos do PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) que contêm cmdlets que pode utilizar nos seus runbooks e configurações do DSC a partir da [Galeria do PowerShell](http://www.powershellgallery.com/). Pode iniciar esta galeria a partir do portal do Azure e importar os módulos diretamente para a Automatização do Azure ou pode transferi-los e importá-los manualmente. Não é possível instalar os módulos diretamente a partir do portal do Azure, mas pode transferi-los e instalá-los tal como o faria com qualquer outro módulo. 

## <a name="example-practical-applications-of-azure-automation"></a>Aplicações práticas de exemplo da Automatização do Azure
Seguem-se alguns exemplos dos tipos de cenários de automatização com a Automatização do Azure. 

* Crie e copie as máquinas virtuais em diferentes subscrições do Azure. 
* Agende cópias de ficheiros a partir de uma máquina local para um contentor de Blob Storage do Azure. 
* Automatize as funções de segurança, tal como negar pedidos de um cliente quando é detetado um ataque denial-of-service. 
* Certifique-se de que as máquinas alinham continuamente com a política de segurança configurada.
* Gira a implementação contínua do código da aplicação na infraestrutura no local e na nuvem. 
* Crie uma floresta do Active Directory no Azure para o seu ambiente de laboratório. 
* Truncar uma tabela numa SQL Database se a base de dados estiver a atingir o tamanho máximo. 
* Atualize remotamente as definições de ambiente para o Web site do Azure. 

## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Como é que a Automatização do Azure se relaciona com outras ferramentas de automatização?
O [Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) foi concebido para automatizar tarefas de gestão na nuvem privada. Este é instalado localmente no seu datacenter como um componente do [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). O SMA e a Automatização do Azure utilizam o mesmo formato de runbook com base no Windows PowerShell e no Fluxo de Trabalho do Windows PowerShell, mas o SMA não suporta [runbooks gráficos](automation-graphical-authoring-intro.md).  

O [System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) foi concebido para a automatização dos recursos no local. Utiliza um formato de runbook diferente do que a Automatização do Azure e o Service Management Automation e tem uma interface gráfica para criar runbooks sem que seja necessário qualquer scripting. Os seus runbooks são compostos por atividades dos Pacotes de Integração que são escritas especificamente para o Orchestrator. 

## <a name="where-can-i-get-more-information"></a>Onde posso obter mais informações?
Está disponível uma variedade de recursos para obter mais informações sobre a Automatização do Azure e a criação dos seus próprios runbooks. 

* Neste momento, encontra-se na **Biblioteca da Automatização do Azure**. Os artigos nesta biblioteca fornecem uma documentação completa sobre a configuração e a administração da Automatização do Azure e a criação dos seus runbooks. 
* Os [cmdlets do Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) fornecem informações para automatizar as operações do Azure com o Windows PowerShell. Os runbooks utilizam estes cmdlets para funcionar com os recursos do Azure. 
* O [Blogue de Gestão](https://azure.microsoft.com/blog/tag/azure-automation/) fornece as informações mais recentes sobre a Automatização do Azure e outras tecnologias de gestão da Microsoft. Deve subscrever este blogue para se manter atualizado com as informações mais recentes da equipa de Automatização do Azure. 
* O [Fórum de Automatização](http://go.microsoft.com/fwlink/p/?LinkId=390561) permite-lhe publicar perguntas sobre a Automatização do Azure para serem resolvidas pela Microsoft e a Comunidade de automatização. 
* Os [cmdlets da Automatização do Azure](https://msdn.microsoft.com/library/mt244122.aspx) fornecem informações para automatizar tarefas de administração. Contém cmdlets para gerir contas de Automatização, recursos, runbooks, DSC.

## <a name="can-i-provide-feedback"></a>Posso enviar comentários?
**Envie-nos comentários!** Se estiver à procura de uma solução de runbook de Automatização do Azure ou um módulo de integração, publique um Pedido de Script no Centro de Scripts. Se tiver comentários ou pedidos funcionalidades para a Automatização do Azure, publique-os na [Voz do Utilizador](http://feedback.windowsazure.com/forums/34192--general-feedback). Obrigado! 

