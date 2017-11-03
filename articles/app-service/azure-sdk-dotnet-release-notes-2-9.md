---
title: "Notas de versão do Azure SDK para .NET 2.9"
description: "Notas de versão do Azure SDK para .NET 2.9"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 199f0906f73d693d7cd4b73c928f23ae83b99596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Notas de versão do Azure SDK para .NET 2.9

Este tópico inclui as notas de versão para versões 2.9 e 2.9.6 do Azure SDK para .NET.

##<a name="azure-sdk-for-net-296-release-summary"></a>Azure SDK para .NET 2.9.6 resumo da versão

Data da versão: 16/11/2016
 
Não existem alterações para o Azure SDK 2.9 foram introduzidas nesta versão. Também não é nenhum processo de atualização necessário para tirar partido este SDK com projetos do serviço em nuvem existentes.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 Release Candidate

- No Visual Studio 2017 RC, esta versão do Azure SDK para .NET baseia-se na carga de trabalho do Azure. Todas as ferramentas que precisa de fazer a programação do Azure irão fazer parte do Visual Studio 2017 RC passa. Para o Visual Studio 2015 e Visual Studio 2013, o SDK continuarão a estar disponível através de WebPI. Iremos irá ser discontinuing Azure SDK para versões do .NET para Visual Studio 2013, quando o Visual Studio 2017 lançadas como um produto final. Siga esta ligação para transferir RC de 2017 do Visual Studio: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

- Alterar o comportamento para só é possível armazenar uma cadeia de ligação parcial com a chave substituída por um token de cadeia de ligação de armazenamento de diagnóstico de serviços em nuvem. A chave de armazenamento real está agora armazenada na pasta de perfil de utilizador para que o acesso pode ser controlado. Visual Studio irá ler a chave de armazenamento da pasta de perfil de utilizador para depuração local e o processo de publicação. 
- Em resposta a alterações descritas acima, o Visual Studio Online equipa avançada o modelo de tarefa de implementação do Cloud Services do Azure, pelo que os utilizadores podem especificar a chave de armazenamento para a definição da extensão de diagnóstico ao publicar para o Azure na integração contínua e Implementação.
- Que fizemos possível armazenar a cadeia de ligação segura e a atomização para o Azure Diagnostics (WAD), para ajudar a resolver problemas com a configuração em environements.
 
### <a name="windows-server-2016-virtual-machines"></a>Máquinas virtuais do Windows Server 2016

- Visual Studio suporta agora a implementação de serviços em nuvem para máquinas virtuais de família de SO 5 (Windows Server 2016). Para serviços em nuvem existente, pode alterar as definições para a nova família de SO de destino. Ao criar novos serviços em nuvem, se optar por criar o serviço através do .net 4.6 ou superior, será predefinido o serviço para utilizar 5 da família de SO.  Para obter mais informações, pode rever o [família de SO convidado suporta tabela](https://azure.microsoft.com/en-us/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Problemas conhecidos

- O SDK .NET do Azure 2.9.6 introduziu uma restrição que bloqueia a implementação de projetos, utilizando as estruturas de .NET não suportadas (por exemplo, o .NET 4.6) para qualquer família de SO < 5. Uma solução é fornecida [aqui](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Cache de função do Azure 

- Suporte para a Cache de função do Azure extremidades em 30 de Novembro de 2016. Para obter mais detalhes, clique em [aqui](https://azure.microsoft.com/en-us/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>A pilha de modelos Azure Resource Manager para o Azure

- Tiver introduzimos pilha do Azure como um destino de implementação para os modelos do Azure Resource Manager.


## <a name="azure-sdk-for-net-29-summary"></a>Azure SDK para .NET 2.9 resumo

## <a name="overview"></a>Descrição geral
Este documento contém as notas de versão para o Azure SDK para .NET 2.9 versão. 

Para obter informações detalhadas sobre as atualizações nesta versão, consulte o [post de anúncio do Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>A pré-visualização do Azure SDK 2.9 para Visual Studio 2015 Update 2 e Visual Studio "15"
Esta atualização inclui as seguintes correções de erros:

* Problema relacionado com a REST API de geração de cliente no qual a cadeia "Type desconhecido" deveria aparecer como o nome da pasta de geração de código e/ou o nome do espaço de nomes largadas no código gerado.
* Problema relacionado com a WebJobs agendadas em que as informações de autenticação estivesse a falhar a ser transmitido para o Programador de processo de aprovisionamento.

Esta atualização inclui a nova funcionalidade seguinte:

* Suporte para serviços de aplicacionais secundário no separador "Serviços" da caixa de diálogo de aprovisionamento do serviço de aplicações. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Ferramentas do Azure Data Lake para Visual Studio 2015 Update 2
Este atualizações incluem o seguinte:

* **Ferramentas do Azure Data Lake** para Visual Studio é agora intercalado com o Azure SDK para a versão do .NET. A ferramenta é instalada automaticamente ao instalar o SDK do Azure. 
  
    A ferramenta é atualizada frequentemente, visite [aqui](http://aka.ms/datalaketool) para obter as atualizações.
* **Explorador de servidores** agora permite-lhe ver todos os e criar algumas entidades de metadados do U-SQL. Para obter mais informações, consulte [isto](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blogue.

## <a name="hdinsight-tools"></a>Ferramentas do HDInsight
**As ferramentas do HDInsight** para Visual Studio agora suporta HDInsight versão 3.3, incluindo a mostrar gráficos Tez e outros idiomas corrige.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Esta versão adiciona [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) suporte para modelos do Resource Manager.

## <a name="see-also"></a>Consultar também
[Post de anúncio do Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

