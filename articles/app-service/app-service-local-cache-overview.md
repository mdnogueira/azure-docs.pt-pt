---
title: "Descrição geral de Cache Local do serviço de aplicações do Azure | Microsoft Docs"
description: "Este artigo descreve como ativar, redimensionar e consultar o estado da funcionalidade de Cache Local do serviço de aplicações do Azure"
services: app-service
documentationcenter: app-service
author: SyntaxC4
manager: yochayk
editor: 
tags: optional
keywords: 
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cfowler
ms.openlocfilehash: 75f2dcb80514105ed663ba1fe5f7adccc05af1fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-local-cache-overview"></a>Descrição geral de Cache Local do serviço de aplicações do Azure
Conteúdo de aplicação web do Azure é armazenado no Storage do Azure e é anexado cópias de segurança de forma durável, como uma partilha de conteúdo. Esta estrutura destina a trabalhar com uma variedade de aplicações e tem os seguintes atributos:  

* O conteúdo é partilhado entre várias instâncias de máquina virtual (VM) da aplicação web.
* O conteúdo é durável e pode ser modificado por aplicações web em execução.
* Ficheiros de registo e ficheiros de dados de diagnóstico estão disponíveis na mesma pasta conteúda partilhada.
* Publicar conteúdo novo diretamente atualiza a pasta de conteúdo. Pode ver imediatamente os mesmos conteúdos através do Web site SCM e a aplicação web em execução (normalmente, algumas tecnologias, como o ASP.NET iniciar um reinício de aplicação web em algumas alterações de ficheiros para obter o conteúdo mais recente).

Apesar de muitas aplicações web utilizam uma ou todas estas funcionalidades, algumas aplicações web apenas terá um arquivo de conteúdo de alto desempenho, só de leitura que possam ser executadas com elevada disponibilidade. Estas aplicações podem beneficiar de uma instância VM de uma cache local específica.

A funcionalidade de Cache Local do serviço de aplicações do Azure fornece uma vista de função da web do seu conteúdo. Este conteúdo é uma cache de write-mas-rejeição do seu conteúdo de armazenamento que é criado o arranque no modo assíncrono no local. Quando a cache estiver pronta, o site é mudado para executar o conteúdo em cache. As aplicações Web que são executadas em Local Cache tem as seguintes vantagens:

* São immune para latências que ocorrem quando acedem a conteúdo no armazenamento do Azure.
* São immune para as atualizações planeadas ou tempos de inatividade não planeados e outras perturbações com armazenamento do Azure que ocorrem nos servidores que servem a partilha de conteúdo.
* Têm menos reinicializações dos aplicação devido a alterações de partilha de armazenamento.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>Como a Local Cache altera o comportamento do serviço de aplicações
* A cache local é uma cópia das pastas /site e /siteextensions da aplicação web. É criado na instância local do VM no arranque de aplicação web. O tamanho da cache local por aplicação web está limitado a 300 MB por predefinição, mas pode aumentar, até 2 GB.
* A cache local é leitura e escrita. No entanto, as modificações são rejeitados quando a aplicação web mova as máquinas virtuais ou obtém reiniciada. Não utilize a Local Cache para aplicações que armazenam dados fundamentais no arquivo de conteúdo.
* As Web apps podem continuar a escrever ficheiros de registo e dados de diagnóstico de forma que atualmente. Ficheiros de registo e dados, no entanto, são armazenados localmente na VM. Em seguida, estes são copiadas periodicamente para o arquivo de conteúdo partilhado. A cópia para o arquivo de conteúdo partilhado é um esforço mais favorável – escrita faz uma cópia poderão ser perdida devido a uma falha repentino de uma instância VM.
* Há uma alteração na estrutura da pasta das pastas LogFiles e os dados para aplicações web que utilizam a Local Cache. Agora existem subpastas as pastas de LogFiles e os dados de armazenamento que se seguem o padrão de nomenclatura de "Identificador exclusivo" + o carimbo de data / hora. Cada um das subpastas corresponde a uma instância VM em que a aplicação web está em execução ou foi executada.  
* Irão publicar as alterações de publicação para a aplicação web através de qualquer um dos mecanismos de publicação para o arquivo de conteúdo partilhado. Isto é propositado uma vez que queremos o conteúdo publicado seja durável. Para atualizar a cache local da aplicação web, tem de ser reiniciada. Aparenta isto como um passo excessivo? Para tornar o ciclo de vida totalmente integrada, consulte as informações neste artigo.
* D:\home aponta para a cache local. D:\Local continuar para apontar para o armazenamento temporário de VM específica.
* A vista de conteúdo predefinido do SCM site continua a ser que o arquivo de conteúdo partilhado.

## <a name="enable-local-cache-in-app-service"></a>Ativar a Cache Local no App Service
Configurar a Local Cache utilizando uma combinação de definições de aplicação reservado. Pode configurar estas definições de aplicação, utilizando os seguintes métodos:

* [Portal do Azure](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configurar a Local Cache utilizando o portal do Azure
<a name="Configure-Local-Cache-Portal"></a>

Ativar a Local Cache numa base por web-aplicação ao utilizar esta definição de aplicação:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Definições de aplicação do portal do Azure: Local Cache](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configurar a Local Cache utilizando o Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

"properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Altere a definição de tamanho na Local Cache
Por predefinição, o tamanho da local cache é **300 MB**. Isto inclui o /site e /siteextensions pastas são copiadas a partir do arquivo de conteúdo, bem como quaisquer pastas de dados e registos criadas localmente. Para aumentar este limite, utilize a definição de aplicação `WEBSITE_LOCAL_CACHE_SIZEINMB`. Pode aumentar o tamanho até **2 GB** (2000 MB) por aplicação web.

## <a name="best-practices-for-using-app-service-local-cache"></a>Melhores práticas para utilizar a Cache Local do serviço de aplicações
Recomendamos que utilize a Local Cache em conjunto com o [ambientes de teste](../app-service/web-sites-staged-publishing.md) funcionalidade.

* Adicionar o *temporária* definição de aplicação `WEBSITE_LOCAL_CACHE_OPTION` com o valor `Always` para sua **produção** ranhura. Se estiver a utilizar `WEBSITE_LOCAL_CACHE_SIZEINMB`, também adicioná-la como uma definição temporária para a ranhura de produção.
* Criar um **transição** espaço e publicar para a ranhura de teste. Normalmente, não definir o bloco de transição para utilizar a Local Cache para ativar um ciclo de vida de teste compilação implementar totalmente integrado para se obter as vantagens da Local Cache para a ranhura de produção de teste.
* O site contra o bloco de transição de teste.  
* Quando estiver pronto, emitir uma [operação de troca](../app-service/web-sites-staged-publishing.md#Swap) entre a transição e produção ranhuras.  
* Definições temporária incluem o nome e temporária para uma ranhura. Por isso, quando obtém alternada a ranhura de teste em produção, herda as definições de aplicação da Local Cache. A ranhura de produção recentemente trocada pela será executada em relação à cache local após alguns minutos e preparada cópias de segurança como parte de aquecimento de ranhura após a troca. Por isso, quando a troca de ranhura estiver concluída, a ranhura de produção está em execução em relação à cache local.

## <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ)
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Como posso saber se a Local Cache aplica-se a minha aplicação web?
Se a sua aplicação web precisa de um arquivo de conteúdo de elevado desempenho e fiável, não utiliza o arquivo de conteúdos para escrever dados críticos no tempo de execução e é inferior a 2 GB de tamanho total, em seguida, a resposta é "Sim"! Para obter o tamanho total das suas pastas de /site e /siteextensions, pode utilizar a extensão do site "Azure Web Apps utilização do disco."

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Como posso saber se o meu site mudou para utilizar a Local Cache?
Se estiver a utilizar a funcionalidade de Local Cache com ambientes de teste, a operação de troca não for concluída até que a Local Cache está preparada. Para verificar se o seu site está em execução em relação a Local Cache, pode verificar a variável de ambiente de processo de trabalho `WEBSITE_LOCALCACHE_READY`. Utilize as instruções no [variável de ambiente de processo de trabalho](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) página para aceder a variável de ambiente de processo de trabalho em várias instâncias.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Posso apenas publicou novas alterações, mas a minha aplicação web aparenta não tê-los. Porquê?
Se a sua aplicação web utiliza a Local Cache, terá de reiniciar o seu site para obter as alterações mais recentes. Não pretende publicar as alterações a um site de produção? Consulte as opções de ranhura na secção de melhores práticas anterior.

### <a name="where-are-my-logs"></a>Onde estão os meus registos?
Com a Cache Local, as pastas de dados e registos de ter um aspeto ligeiramente diferentes. No entanto, a estrutura da sua subpastas permanece o mesmo, exceto que as subpastas estão nestled sob uma subpasta com o formato "VM Identificador exclusivo" + o carimbo de data / hora.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Tenho de Cache Local ativada, mas a minha aplicação web ainda obtém reiniciada. Por que motivo é que? Posso considerar o que ajudou a Local Cache com o reinício da aplicação frequentes.
Local Cache ajudar a impedir o reinício da aplicação web relacionadas com o armazenamento. No entanto, a aplicação web foi ainda são submetidos a reinícios durante as atualizações de infraestrutura planeada da VM. Os reinícios de aplicação global que ocorrer com a Cache Local ativado devem ser menos.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Local Cache excluir os diretórios de ser copiado para a unidade local mais depressa?
Como parte do passo que copia o conteúdo do armazenamento, qualquer pasta denominada repositório está excluída. Isto ajuda-o com cenários em que o conteúdo do site pode conter um repositório de controlo de origem que pode não ser necessária na operação do diário da aplicação web. 
