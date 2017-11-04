---
title: "Compilação da linha de comandos do Azure | Microsoft Docs"
description: "Compilação da linha de comandos do Azure"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2017
ms.author: kraigb
ms.openlocfilehash: 5fe910e2757dd5ec783538e23e7f52e2f5725b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="building-azure-projects-from-the-command-line"></a>A criar os projetos do Azure na linha de comandos
Utilizar o motor de compilação da Microsoft (MSBuild), pode criar produtos em ambientes de laboratório de compilação em que o Visual Studio não está instalado. MSBuild utiliza um formato XML para ficheiros de projeto do extensível e totalmente suportados pela Microsoft. Utilizar o formato de ficheiro do MSBuild, possa descrever o que itens têm de ser criado para um ou mais plataformas e configurações.

Também pode executar MSBuild na linha de comandos e este tópico descreve o que abordagem. Ao definir propriedades na linha de comandos, pode criar as configurações específicas de um projeto. Da mesma forma, também pode definir os destinos que MSBuild baseia-se. Para obter mais informações sobre parâmetros da linha de comandos e MSBuild, consulte [referência da linha de comandos de MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>Parâmetros de MSBuild
A forma mais simples para criar um pacote está a ser executado MSBuild com o `/t:Publish` opção. Por predefinição, este comando cria um diretório em relação a pasta de raiz para o projeto, tais como `<ProjectDirectory>\bin\Configuration\app.publish\`. Quando criar um projeto do Azure, são gerados dois ficheiros: o pacote de ficheiros próprio e o ficheiro de configuração associada:

* Ficheiro de pacote (`project.cspkg`)
* Ficheiro de configuração (`ServiceConfiguration.TargetProfile.cscfg`)

Por predefinição, cada projeto do Azure inclui um ficheiro de configuração do serviço para o locais compilações (depuração) e outra para compilações de nuvem (teste ou de produção). No entanto, pode adicionar ou remover ficheiros de configuração do serviço conforme necessário. Quando criar um pacote no Visual Studio, é-lhe perguntado qual o ficheiro para incluir juntamente com o pacote de configuração do serviço. Quando criar um pacote através da utilização de MSBuild, o ficheiro de configuração do serviço local é incluído por predefinição. Para incluir um ficheiro de configuração de serviço diferente, defina o `TargetProfile` propriedade do comando de MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Se pretender utilizar um diretório alternativo para o pacote armazenado e ficheiros de configuração, definir o caminho utilizando o `/p:PublishDir=Directory\` opção, incluindo o separador de barra invertida à direita.

## <a name="next-steps"></a>Passos seguintes
Depois do pacote é criado, pode implementá-la para o Azure. Para um tutorial que demonstra como automatizar o processo, consulte [entrega contínua para serviços em nuvem no Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).

