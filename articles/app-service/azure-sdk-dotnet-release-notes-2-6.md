---
title: "Notas de versão do Azure SDK para .NET 2.6"
description: "Notas de versão do Azure SDK para .NET 2.6"
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 21817b09440fc98a54dc45c9129d104b01fa387d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Notas de versão do Azure SDK para .NET 2.6
Este documento contém as notas de versão para o Azure SDK para .NET 2.6 versão. 

Com o Azure SDK 2.6 pode desenvolver aplicações de serviço em nuvem (PaaS) direcionado para o .NET 4.5.2 ou .NET 4.6, desde que instale manualmente o .NET Framework de destino na função de serviço de nuvem. Consulte [instale o .NET numa função de serviço em nuvem](http://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Atualizações do Service Bus
* Hubs de eventos: 
  
  * Agora permite o controlo de acesso de destino quando enviar eventos resultariam da exposição de ponto final do publicador adicionais para os Event Hubs.
  * Estabilidade adicional e melhoramento adicionada a funcionalidade de Event Hubs.
  * A adição de suporte do protocolo Amqp através de WebSocket para as mensagens e do Event Hubs.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>Atualiza as ferramentas do HDInsight para Visual Studio
* **Melhoramento de IntelliSense**: Sugestão de metadados remota
  
    Ferramentas do HDInsight para Visual Studio suporta agora obtenção de metadados remota quando estiver a editar o script de ramo de registo. Por exemplo, pode escrever **SELECIONAR * FROM** e todos os nomes de tabela serão apresentados. Além disso, os nomes de coluna serão apresentados depois de especificar uma tabela.
* **Suporte de emulador do HDInsight**
  
    Ferramentas do HDInsight para Visual Studio suportam agora ligar para o emulador do HDInsight, pelo que pode desenvolver os scripts Hive localmente sem introduzir qualquer custo, em seguida, execute os scripts em seus clusters do HDInsight. 
  
    Para obter mais informações, consulte [neste manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **Ferramentas do HDInsight para Visual Studio suporte para clusters do Hadoop genéricos** (pré-visualização)
  
    Ferramentas do HDInsight para Visual Studio suportam agora clusters do Hadoop genéricos, pelo que pode utilizar as ferramentas do HDInsight para Visual Studio para fazer o seguinte:
  
  * ligar ao cluster, 
  * escrever a consulta do Hive com suporte para conclusão-IntelliSense/automática avançada, 
  * ver todas as tarefas do cluster com uma IU intuitiva. 
    
    Para obter mais informações, consulte [neste manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Atualizações da Cache de função
* **Cache de função** foi atualizado para utilizar **SDK de armazenamento do Microsoft Azure** versão 4.3. Até agora, o **Cache com função** estava a utilizar o SDK de armazenamento do Azure versão 1.7.
  
    Os clientes utilizando o Azure SDK 2.5 ou abaixo deverá atualizar para o Azure SDK 2.6 e mover para a nova versão do SDK de armazenamento do Azure. 
  
    Neste momento Storage do Azure versão 2011-08-18 está agendado para ser removido 1 de Agosto de 2016. As migrações de Cache de função do Azure SDK 2.5 ou abaixo para 2.6 tem de ser concluídas neste momento. Para obter mais informações sobre a extinção do Storage do Azure versão 2011-08-18, consulte [os atualização do remoção de versão do serviço de armazenamento do Microsoft Azure: extensão 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> Estamos a anunciar a extinção de 30 de Novembro de 2016, para o serviço de Cache gerida do Azure e a Cache de função do Azure. Recomendamos a migração para a Cache de Redis do Azure em preparação para esta extinção. Para obter mais informações sobre as datas e orientações de migração, consulte [oferta de que a Cache do Azure é mais adequada para mim?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Ferramentas do App Service do Azure
Os itens seguintes foram atualizados na versão 2.6 de SDK do Azure.

* Publicação do Azure melhorada para incluir as aplicações de API do Azure como um destino de implementação.
* API Apps a funcionalidade de aprovisionamento para permitir que os utilizadores com a funcionalidade de aprovisionamento e a criação da aplicação API.
* Explorador de servidores é alterada para refletir o novo nó de serviço de aplicações Web, móveis e API Apps, agrupado por grupo de recursos.
* Adicione gesto de cliente da aplicação API do Azure adicionado à maioria dos c# projetos que irão ativar a geração automática de aplicações API Swagger ativado em execução na subscrição do Azure do utilizador.
* Ferramentas de API Apps e nós de serviço de aplicações no Explorador de servidores estão apenas disponíveis no Visual Studio 2013. 

## <a name="azure-resource-manager-tools-updates"></a>Atualizações de ferramentas do Gestor de recursos do Azure
As ferramentas do Gestor de recursos do Azure foram atualizadas para incluir os modelos de máquinas virtuais, redes e armazenamento. O JSON de experiência de edição foi atualizado para incluir uma nova vista de contorno para modelos e a capacidade de editar os modelos utilizando fragmentos JSON. Modelos de implementação a partir do Visual Studio, utilize um script do PowerShell fornecido com o projeto, pelo que quaisquer alterações efetuadas para o script serão utilizadas pelo Visual Studio.

## <a name="diagnostics-improvements-for-cloud-services"></a>Melhoramentos de diagnóstico para serviços Cloud
Azure SDK 2.6 novamente oferece suporte para recolher registos de diagnóstico no emulador de computação do Azure e transferência-los para armazenamento de desenvolvimento. Regista qualquer diagnóstico (incluindo registos de eventos de rastreio para registos do Windows (ETW), os contadores de desempenho, infraestrutura de registos de eventos do windows e registos de rastreio de aplicação) gerado quando a aplicação está em execução no emulador pode ser transferido para o armazenamento de desenvolvimento para verificar se o registo de diagnóstico está a funcionar no seu computador local. 

A conta de armazenamento do Diagnostics agora pode ser especificada no ficheiro de configuração (. cscfg) do serviço, tornando mais fácil de utilizar as contas de armazenamento do diagnostics diferente para os diferentes ambientes. Existem algumas diferenças entre a forma como a cadeia de ligação funcionavam na 2.4 de SDK do Azure e o Azure SDK 2.6 relevantes. Para obter mais informações sobre como utilizar a cadeia de ligação de armazenamento de diagnóstico e como afeta os seus projetos consulte [configurar diagnósticos para uma Cloud Services do Azure](http://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>As alterações de última hora
### <a name="azure-resource-manager-tools"></a>Ferramentas do Gestor de recursos do Azure
* O **projetos de implementação de nuvem** tipo de projeto disponível no 2.5 de SDK do Azure foi mudado para **grupo de recursos do Azure**.
* **Projetos de implementação de nuvem** , mas o tipo de projetos criado no 2.5 de SDK do Azure pode ser utilizado no 2.6 a implementar o modelo a partir do Visual Studio falhará. No entanto, implementar com o script do PowerShell continuarão a funcionar como anteriormente.  Para obter informações sobre como utilizar **projetos de implementação de nuvem** no 2.6 ler este artigo [publique](http://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Problemas conhecidos
* Recolher registos de diagnóstico no emulador do necessita de um sistema de operativo de 64 bits. Registos de diagnóstico não serão recolhidos quando em execução num sistema operativo de 32 bits. Isto não afeta quaisquer outras funcionalidades do emulador. 
* Azure SDK 2.6, lançadas em 29/4/2015 tinha dois problemas: 
  
  * Não foi possível carregar a aplicação universal no Visual Studio 2015 quando o Azure SDK 2.6 foi instalado na máquina.
  * Um projeto de serviço em nuvem de depuração provocaria a falha no Visual Studio 2013 e o Visual Studio 2015, onde o Visual Studio deixar de responder e falha ao apresentar uma caixa de diálogo com a mensagem "Configurar diagnósticos para emulador".
    
    Uma atualização para o Azure SDK 2.6 foi libertada 18/5/2015. A versão atualizada é 2.6.30508.1601; contém correções para dois problemas descritos acima. Pode identificar a compilação do SDK no painel de controlo -> programas e funcionalidades -> Ferramentas do Microsoft Azure para o Microsoft Visual Studio 2013 – v 2.6. A coluna de versão irá apresentar o número de compilação.
    
    Se ainda estão a enfrentar os problemas acima, instale a versão mais recente do Azure 2.6 SDK para [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Veja Também
[Suporte e informações de extinção para o Azure SDK para .NET e APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

