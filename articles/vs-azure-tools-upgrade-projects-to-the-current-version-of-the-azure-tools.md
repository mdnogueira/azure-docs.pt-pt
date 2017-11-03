---
title: "Como atualizar projetos para a versão atual das ferramentas do Azure | Microsoft Docs"
description: "Saiba como atualizar um projeto do Azure no Visual Studio para a versão atual das ferramentas do Azure"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1d64070a-078d-468a-87f4-e6715de6475f
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: kraigb
ms.openlocfilehash: 9a35de7ca0e7161468181b21709e1bd9915d566f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Como atualizar projetos para a versão atual das ferramentas do Azure para Visual Studio
## <a name="overview"></a>Descrição geral
Depois de instalar a versão atual das ferramentas do Azure (ou uma versão anterior que é mais recente que 1.6), os projetos que foram criados utilizando um ferramentas do Azure versão antes de 1.6 (Novembro de 2011) irá ser atualizado automaticamente assim que abri-los. Se tiver criado projetos, utilizando a versão de (Novembro de 2011) versão 1.6 dessas ferramentas e continuará a ter essa versão instalada, pode abrir essas projetos na versão mais antiga e posteriormente, decida se pretende atualizá-los.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Como o projeto é alterado quando que a atualização
Se um projeto é automaticamente atualizado ou especificar que pretende atualizá-lo, o projeto é modificado para trabalhar com as versões atuais de determinadas assemblagens e algumas propriedades também são alteradas, esta secção descreve. Se o seu projeto precisar outras alterações para ser compatível com a versão mais recente das ferramentas, tem de efetuar essas alterações manualmente.

* O ficheiro Web. config para funções da web e o ficheiro App. config para as funções de trabalho sejam atualizados para referenciar a versão mais recente do Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.
* As assemblagens Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll e Microsoft.WindowsAzure.ServiceRuntime.dll são atualizadas para as novas versões.
* Publicar perfis que foram armazenados no ficheiro de projeto do Azure (.ccproj) são movidos para um ficheiro separado, com a extensão .azurePubXml, além de **publicar** subdiretório.
* Algumas propriedades no perfil de publicação são atualizadas para suportar funcionalidades novas e alteradas. **AllowUpgrade** é substituído pela **DeploymentReplacementMethod** porque pode atualizar um serviço em nuvem implementado em simultâneo ou de forma incremental.
* A propriedade **UseIISExpressByDefault** é adicionado e definido como false para que o servidor web que é utilizado para depuração automaticamente não mudará de serviços de informação Internet (IIS) para IIS Express. IIS Express é o servidor web predefinido projetos que são criadas com as versões das ferramentas mais recentes.
* Se a colocação em cache do Azure está alojada numa ou mais das funções do seu projeto, algumas propriedades na configuração do serviço (ficheiro. cscfg) e a definição de serviço (ficheiro. csdef) são alteradas quando um projeto é atualizado. Se o projeto de utilizar o pacote NuGet da colocação em cache do Azure, o projeto é atualizado para a versão mais recente do pacote. Deve abrir o ficheiro Web. config e certifique-se de que a configuração do cliente foi mantida corretamente durante o processo de atualização. Se tiver adicionado as referências a assemblagens de cliente do Azure a colocação em cache sem utilizar o pacote NuGet, estas assemblagens não serão atualizadas; tem de atualizar manualmente estas referências para as novas versões.

> [!IMPORTANT]
> Para o F # projetos, tem de atualizar manualmente as referências a assemblagens do Azure para que o fizerem referência as versões mais recentes desses assemblagens.
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Como atualizar um projeto do Azure para a versão atual
1. Instale a versão atual das ferramentas do Azure para a instalação do Visual Studio que pretende utilizar para o projeto atualizado e, em seguida, abra o projeto que pretende atualizar. Se o projeto foi criado com uma ferramentas do Azure versão antes de 1.6 (Novembro de 2011), o projeto é atualizado automaticamente para a versão atual. Se o projeto foi criado com o de 2011 de Novembro de versão e que versão ainda está instalado, o projeto será aberta nessa versão.
2. No Explorador de soluções, abra o menu de atalho para o nó do projeto, escolha **propriedades**e, em seguida, escolha o **aplicação** separador da caixa de diálogo que aparece.
   
    O **aplicação** separador apresenta a versão de ferramentas que tem associadas ao projeto. Se for apresentada a versão atual das ferramentas do Azure, o projeto já foi atualizado. Se instalou uma versão mais recente das ferramentas que mostra que o separador, um **atualizar** botão aparece.
3. Escolha o **atualizar** botão para atualizar um projeto para a versão atual das ferramentas.
4. Compilar o projeto e, em seguida, resolva quaisquer erros que resultam de alterações de API. Para obter informações sobre como modificar o código para a nova versão, consulte a documentação da API específica.

