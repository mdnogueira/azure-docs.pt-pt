---
title: "Azure SDK para notas de versão 3.0 do .NET | Microsoft Docs"
description: "Azure SDK para notas de versão 3.0 do .NET"
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
ms.date: 03/07/2017
ms.author: juliako
ms.openlocfilehash: eea4e569ac2d0192ed7872d2fcb9bed03614832b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-30-release-notes"></a>Notas de versão do Azure SDK para .NET 3.0

Este tópico inclui as notas de versão para versão 3.0 do Azure SDK para .NET.

##<a name="azure-sdk-for-net-30-release-summary"></a>Azure SDK para o resumo de versão 3.0 do .NET

Data da versão: 03/07/2017
 
Não existem alterações a 3.0 de SDK do Azure foram introduzidas nesta versão. Também não é nenhum processo de atualização necessário para tirar partido este SDK com projetos do serviço em nuvem existentes. Para permitir a utilização de 3.0 de SDK do Azure sem necessidade de um processo de atualização, 3.0 de SDK do Azure instala os mesmos diretórios como o Azure SDK 2.9. Mais os componentes não mudou a versão principal do 2.9, mas em vez disso, atualizou o número de compilação.

## <a name="visual-studio-2017-rtw"></a>RTW de 2017 do Visual Studio

- No Visual Studio 2017, esta versão do Azure SDK para .NET baseia-se para a carga de trabalho do Azure. Todas as ferramentas que precisa de fazer a programação do Azure irão fazer parte do Visual Studio 2017, passa. Para o Visual Studio 2015 o SDK continuarão a estar disponível através de WebPI. Podemos ter descontinuada Azure SDK para versões do .NET para Visual Studio 2013 agora que o Visual Studio 2017 foi lançado.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

- Alterar o comportamento para só é possível armazenar uma cadeia de ligação parcial com a chave substituída por um token de cadeia de ligação de armazenamento de diagnóstico de serviços em nuvem. A chave de armazenamento real está agora armazenada na pasta de perfil de utilizador para que o acesso pode ser controlado. Visual Studio irá ler a chave de armazenamento da pasta de perfil de utilizador para depuração local e o processo de publicação. 
- Em resposta a alterações descritas acima, o Visual Studio Online equipa avançada o modelo de tarefa de implementação do Cloud Services do Azure, pelo que os utilizadores podem especificar a chave de armazenamento para a definição da extensão de diagnóstico ao publicar para o Azure na integração contínua e Implementação.
- Que fizemos possível armazenar a cadeia de ligação segura e a atomização para o Azure Diagnostics (WAD), para ajudar a resolver problemas com a configuração em environements.
 
### <a name="windows-server-2016-virtual-machines"></a>Máquinas virtuais do Windows Server 2016

- Visual Studio suporta agora a implementação de serviços em nuvem para máquinas virtuais de família de SO 5 (Windows Server 2016). Para serviços em nuvem existente, pode alterar as definições para a nova família de SO de destino. Ao criar novos serviços em nuvem, se optar por criar o serviço através do .net 4.6 ou superior, será predefinido o serviço para utilizar 5 da família de SO.  Para obter mais informações, pode rever o [família de SO convidado suporta tabela](../cloud-services/cloud-services-guestos-update-matrix.md).

### <a name="known-issues"></a>Problemas conhecidos

- 3.0 de SDK .NET do Azure introduziu um problema ao remover o Visual Studio 2017 numa configuração lado a lado com o Visual Studio 2015.  Se tiver o Azure SDK instalado para o Visual Studio 2015, o emulador do Storage do Microsoft Azure e o emulador de computação do Microsoft Azure serão removidos se desinstalar o Visual Studio 2017.  Isto produzirá um erro ao criar e depurar novos projetos de serviços de nuvem no Visual Studio 2015. Para corrigir este problema, reinstale o Azure SDK do instalador de plataforma Web.  O problema será resolvido numa atualização futura 2017 do Visual Studio.  .

 
### <a name="azure-in-role-cache"></a>Cache de função do Azure 

- Suporte para a Cache de função do Azure terminou em 30 de Novembro de 2016. Para obter mais detalhes, clique em [aqui](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).




