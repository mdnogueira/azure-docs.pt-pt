---
title: "Notas de versão do Azure SDK para .NET 2.5.1"
description: "Notas de versão do Azure SDK para .NET 2.5.1"
services: app-service
documentationcenter: .net,nodejs,java
author: Juliako
manager: erikre
editor: 
ms.assetid: 8d3d815f-bb58-447e-8ff0-f9b9603c7b00
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/10/2016
ms.author: juliako
ms.openlocfilehash: 0a422b02623a18ac6a1eef460bbada681672e69f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-sdk-for-net-251-release-notes"></a>Notas de versão do Azure SDK para .NET 2.5.1
Este documento contém as notas de versão para o Azure SDK para .NET 2.5.1 de versão. 

## <a name="azure-sdk-for-net-251-release-notes"></a>Notas de versão do Azure SDK para .NET 2.5.1
Seguem-se novas funcionalidades e atualizações no SDK do Azure para .NET 2.5.1.

* Relacionados com o novo features\scenarios **extensões de ferramentas Web**. 
  
  * Web sites do Azure foi mudado para o App Service do Azure. 
  * O suporte do Azure API Apps (pré-visualização) foi adicionado para que os clientes possam publicar projetos ASP.NET como API Apps e, em seguida, utilizar o adicionar > gesto de cliente da aplicação API do Azure em c# projetos para gerar código com base na estrutura da aplicação API implementado. 
  * O nó de Web sites no Explorador de servidores foi preterido in lieu of o nó do App Service do Azure, que contém o suporte para o agrupamento de Azure API Apps, Mobile Apps e aplicações Web com base no grupo de recursos.
  * O suporte do Azure Mobile Apps (pré-visualização) foi adicionado para que os clientes podem criar novos projetos de Mobile Apps, adicionar controladores de Mobile Apps, publicar os projetos e remotamente depurar aplicações.
  * Adicionar > gesto de cliente da aplicação API do Azure suporta agora ficheiros locais do Swagger JSON, pelo que os programadores de Web API podem utilizar NuGets de terceiros, como o Swashbuckle para gerar Swagger ou autor-lo manualmente. Desta forma, os programadores de cliente podem utilizar as funcionalidades de geração de código para consumir qualquer ponto final de Swagger em projetos de c#. 
  * Aplicação Web e API Apps caixas de diálogo publicação foram melhoradas para suportar o conceito de portal do Azure de agrupamento de recursos e a seleção/criação de grupos de recursos do Azure e planos do App Service são representados na nova aplicação Web e API Apps aprovisionamento caixa de diálogo. 
  * Nós de Explorador de servidores de aplicação de API do Azure fornecem ligações para a ligação avançada da API Apps no portal do Azure, bem como outras funcionalidades, como o registo de transmissão em fluxo e depuração remota.
    
    Para problemas conhecidos e limitações atuais no Azure SDK .NET 2.5.1 [isto](app-service-release-notes.md#known_issues_2_5_1) secção abaixo.
* Relacionados com o novo features\scenarios **as ferramentas do HDInsight** no Visual Studio estão ativadas nesta versão. 
  
  * Validação de local de hive scripts. Clique no botão Validar script na barra de ferramentas para ver se existem quaisquer erros no seu script. 
  * Melhorado a depuração de tarefas do Hive. Agora pode depurar tarefas do Hive ao aceder a registos Yarn no Visual Studio. Se a sua aplicação tiver problemas de desempenho, investigar registos YARN fornecem informações úteis...
  * (Pré-visualização pública) Conclusão automática de palavra-chave e o IntelliSense suportem para o ramo de registo. Para ajudar a criar Hive scripts, as ferramentas do HDInsight para Visual Studio adicionados conclusão automática de palavra-chave e o IntelliSense suporte para o ramo de registo.
  * Suporte do Storm. Agora, pode utilizar as ferramentas do HDInsight para Visual Studio para desenvolver topologias/Spouts/Bolts do Storm em c#. Em seguida, pode submeter a topologia desenvolvida para um cluster do Storm e ver o estado de topologia/bolt/spout. Pode utilizar os registos de cliente e os registos do sistema para resolver os Storm Bolts/topologias/Spouts. Também pode utilizar recursos existentes de JAVA Storm no HDInsight.
    
    Para obter mais informações, consulte [começar a utilizar as ferramentas Hadoop do HDInsight para Visual Studio](../hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a id="known_issues_2_5_1"></a>Azure SDK para .NET 2.5.1 conhecido problemas e limitações
* As aplicações API do Azure é visível como um destino de implementação para aplicações móveis. As Web Apps deve ser o destino apenas para Mobile Apps até uma versão subsequente. 
* Aprovisionamento de aplicação API do Azure pode resultar em com êxito, mas ligados intermitentemente Falha ao atualizar o progresso na janela de atividade do App Service do Azure. Solução é para verificar o estado da nova aplicação de API do Azure no portal do Azure. 
* Ficheiro > novo projeto > aplicação API > experiência F5 resulta num erro de HTTP, porque não há nenhum default/index.html. Solução é manualmente navegar para o URL de /api/values. 
* Ligados intermitentemente, serão apresentados simplificados ícones do Explorador de servidores. Reiniciar VS resolve este problema. 
* Se é emitida uma exceção durante o aprovisionamento de aplicação Web ou aplicação de API (por exemplo, quota excedida erros ou o nome duplicado de gateway de aplicação API do Azure), os erros mostram algum texto JSON não processado. 
* Problemas de criação do projeto intermitente ao Application Insights está selecionada no momento de criação do projeto.
* Ocasionalmente, o código gerado de cliente da aplicação API do Azure está em falta espaços de nomes, têm de ser incluídos manualmente (ou importado automaticamente através de ajudas de Visual Studio) para código compilar. 
* Projetos de aplicação móvel devem ser publicados para aplicações web, mas tem de escolher um site que criou como uma aplicação móvel no portal do Azure, uma vez que os projetos de aplicação móvel requerem uma base de dados. 
* A página de início para Mobile Apps utiliza o termo "serviço móvel" em vez de "aplicações móveis" 
* Criação do projeto de aplicação móvel pode demorar um minuto para criar. 
* Durante a aplicação API de aprovisionamento (em alguns casos) erro volta a partir da API do Azure ao refletir que as permissões não foi possível definir corretamente, apesar da aplicação API corretamente aprovisionada e está pronta a utilizar. Pode definir manualmente as permissões no portal do Azure.
* Application Insights não é suportada nos modelos de aplicação API e modelos de aplicação móvel.
* Projetos de aplicação de API não podem ser utilizados em conjunto com projetos do serviço de nuvem.
* Modelos de projeto de aplicação API só estão disponíveis em c#.
* Consumo de aplicação API através do menu de contexto de "Adicionar Azure aplicação cliente de API" só é suportado em c#.

