---
title: "Configurar aplicações Web no Serviço de Aplicações do Azure"
description: "Como configurar uma aplicação web nos serviços de aplicações do Azure"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.openlocfilehash: fd1a8cf442ea0688e027f8f8028ee8b4e149d8d2
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="configure-web-apps-in-azure-app-service"></a>Configurar aplicações Web no Serviço de Aplicações do Azure
Este tópico explica como configurar uma aplicação web através de [Portal do Azure].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Definições da aplicação
1. No [Portal do Azure], abra o painel da aplicação web.
3. Clique em **Definições da aplicação**.

![Definições da aplicação][configure01]

O **definições da aplicação** painel tem agrupadas em várias categorias de definições.

### <a name="general-settings"></a>Definições gerais
**Versões do Framework**. Se a sua aplicação utilizar qualquer estas estruturas, defina estas opções: 

* **.NET framework**: definir a versão do .NET framework. 
* **PHP**: definir a versão do PHP, ou **OFF** para desativar o PHP. 
* **Java**: selecione a versão de Java ou **OFF** para desativar o Java. Utilize o **contentor Web** opção escolher entre as versões do Tomcat e Jetty.
* **Python**: selecione a versão do Python, ou **OFF** para desativar o Python.

Por motivos de técnicos, ativar a sua aplicação Java desativa as opções de .NET, PHP e Python.

<a name="platform"></a>
**Plataforma**. Seleciona se a sua aplicação web é executada num ambiente de 32 bits ou 64 bits. O ambiente de 64 bits requer modo básicas ou Standard. Gratuito e partilhado modos são sempre executadas num ambiente de 32 bits.

**Web Sockets**. Definir **ON** para ativar o protocolo de WebSocket; por exemplo, se a sua aplicação web utiliza [ASP.NET SignalR] ou [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Always On**. Por predefinição, as aplicações web são descarregadas que estejam Inativos durante um determinado período de tempo. Isto permite que o sistema poupar recursos. No modo básico ou padrão, pode ativar **Always On** manter a aplicação carregada sempre. Se a aplicação for executada WebJobs contínuos ou executa WebJobs acionada através de uma expressão de CRON, deverá ativar **Always On**, ou as tarefas de web podem não ser fiável.

**Gerido Pipeline versão**. Define o IIS [modo de pipeline]. Deixe este conjunto como integrado (predefinição), a menos que tenha uma aplicação de legado que requer uma versão anterior do IIS.

**Auto troca**. Se ativar troca automática para uma ranhura de implementação, serviço de aplicações será automaticamente de comutação a aplicação web em produção quando emitir uma atualização para essa ranhura. Para obter mais informações, consulte [implementar para a transição ranhuras para web apps no App Service do Azure](web-sites-staged-publishing.md).

### <a name="debugging"></a>Depuração
**Depuração remota**. Permite a depuração remota. Quando ativada, pode utilizar o depurador remoto no Visual Studio para ligar diretamente à sua aplicação web. Depuração remota permanecerá ativado para 48 horas. 

### <a name="app-settings"></a>Definições de aplicação
Esta secção contém pares nome/valor que web app carregará em Iniciar cópia de segurança. 

* Para aplicações de .NET, estas definições são inseridas na sua configuração de .NET `AppSettings` em runtime, substituir as definições existentes. 
* As aplicações PHP, Python, Java e nó podem aceder a estas definições como variáveis de ambiente no tempo de execução. Para cada definição de aplicação, são criadas duas variáveis de ambiente; um com o nome especificado, a entrada de definição de aplicação e outro com um prefixo APPSETTING_. Ambos contêm o mesmo valor.

### <a name="connection-strings"></a>Cadeias de ligação
Cadeias de ligação de recursos ligados. 

Para aplicações de .NET, estas cadeias de ligação são inseridas na sua configuração de .NET `connectionStrings` definições em tempo de execução, substituir entradas existentes em que a chave de é igual ao nome de base de dados ligado. 

Para aplicações PHP, Python, Java e nó, estas definições estarão disponíveis como variáveis de ambiente em runtime, o prefixo com o tipo de ligação. Os prefixos de variável de ambiente são os seguintes: 

* SQL Server:`SQLCONNSTR_`
* MySQL:`MYSQLCONNSTR_`
* Base de dados do SQL Server:`SQLAZURECONNSTR_`
* Personalizado:`CUSTOMCONNSTR_`

Por exemplo, se uma cadeia de ligação MySql foram denominada `connectionstring1`, teria de ser acedido através da variável de ambiente `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Documentos predefinidos
O documento predefinido é a página web que é apresentado no URL da raiz para um Web site.  É utilizado o primeiro ficheiro correspondente na lista. 

As aplicações Web podem utilizar módulos que baseada na rota no URL, em vez de a servir conteúdo estático, caso em que não existe não é nenhum documento predefinido como tal.    

### <a name="handler-mappings"></a>Mapeamentos do processador
Utilize esta área para adicionar processadores de script personalizado para processar pedidos para extensões de ficheiro específicas. 

* **Extensão**. A extensão de ficheiro a serem processados, tais como *.php ou handler.fcgi. 
* **Caminho de processador de scripts**. O caminho absoluto do processador de scripts. Pedidos para ficheiros que correspondem a extensão de ficheiro serão processados pelo processador de scripts. Utilize o caminho `D:\home\site\wwwroot` para fazer referência ao diretório de raiz da sua aplicação.
* **Argumentos adicionais**. Argumentos da linha de comandos opcionais para o processador de scripts 

### <a name="virtual-applications-and-directories"></a>Aplicações virtuais e diretórios
Para configurar aplicações virtuais e diretórios, especifique cada diretório virtual e o caminho físico correspondente relativo à raiz do Web site. Opcionalmente, pode selecionar o **aplicação** caixa de verificação para marcar um diretório virtual como uma aplicação.

## <a name="enabling-diagnostic-logs"></a>Ativar registos de diagnóstico
Para ativar os registos de diagnóstico:

1. No painel da sua aplicação web, clique em **todas as definições**.
2. Clique em **registos de diagnóstico**. 

As opções para escrever os registos de diagnóstico de uma aplicação web que suporta o registo: 

* **Registo de aplicações**. Escreve os registos de aplicações para o sistema de ficheiros. Registo lasts durante um período de 12 horas. 

**Nível**. Quando o registo de aplicações estiver ativado, esta opção especifica a quantidade de informações que serão registados (erro, aviso, informações ou verboso).

**Registo de servidor Web**. Os registos são guardados no formato de ficheiro de registo expandido W3C. 

**Mensagens de erro detalhadas**. Mensagens de erro detalhadas guarda ficheiros. htm. Os ficheiros são guardados em /LogFiles/DetailedErrors. 

**Pedidos falhados**. Os registos de falha pedidos para ficheiros XML. Os ficheiros são guardados em LogFiles/W3SVC*xxx*, onde xxx é um identificador exclusivo. Esta pasta contém um ficheiro XSL e um ou mais ficheiros XML. Certifique-se ao transferir o ficheiro XSL, porque fornece uma funcionalidade para formatação e filtrar o conteúdo dos ficheiros XML.

Para ver os ficheiros de registo, tem de criar as credenciais de FTP, da seguinte forma:

1. No painel da sua aplicação web, clique em **todas as definições**.
2. Clique em **as credenciais de implementação**.
3. Introduza um nome de utilizador e palavra-passe.
4. Clique em **Guardar**.

![Definir credenciais de implementação][configure03]

O nome de utilizador FTP completo é "app\username" onde *aplicação* é o nome da sua aplicação web. O nome de utilizador está listado no painel de aplicação web, em **Essentials**.  

![Credenciais de implementação de FTP][configure02]

## <a name="other-configuration-tasks"></a>Outras tarefas de configuração
### <a name="ssl"></a>SSL
No modo básico ou padrão, pode carregar certificados SSL para um domínio personalizado. Para obter mais informações, consulte [ativar HTTPS para uma aplicação web]. 

Para ver os certificados carregados, clique em **todas as definições** > **domínios personalizados e SSL**.

### <a name="domain-names"></a>Nomes de domínio
Adicione nomes de domínio personalizado da sua aplicação web. Para obter mais informações, veja [configurar um nome de domínio personalizado para uma aplicação web no App Service do Azure].

Para ver os nomes de domínio, clique em **todas as definições** > **domínios personalizados e SSL**.

### <a name="deployments"></a>Implementações
* Configure a implementação contínua. Consulte [utilizando o Git para implementar Web Apps no App Service do Azure](app-service-deploy-local-git.md).
* Ranhuras de implementação. Consulte [implementar em ambientes de teste para Web Apps no App Service do Azure].

Para ver as ranhuras de implementação, clique em **todas as definições** > **ranhuras de implementação**.

### <a name="monitoring"></a>Monitorização
No modo básico ou padrão, pode testar a disponibilidade de pontos finais HTTP ou HTTPS a partir de localizações até três geo-distribuição. Um teste de monitorização falha se o código de resposta HTTP é um erro (4xx ou 5xx) ou a resposta demora mais do que 30 segundos. Um ponto final é considerado disponível se os testes de monitorização com êxito a partir de localizações especificadas. 

Para obter mais informações, consulte [como: monitorizar o estado do ponto final web].

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service], onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Configurar um nome de domínio personalizado no Serviço de Aplicações do Azure]
* [Ativar HTTPS para uma aplicação no App Service do Azure]
* [Dimensionar a uma aplicação web no App Service do Azure]
* [Noções básicas de monitorização para Web Apps no App Service do Azure]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configurar um nome de domínio personalizado no Serviço de Aplicações do Azure]: ./app-service-web-tutorial-custom-domain.md
[implementar em ambientes de teste para Web Apps no App Service do Azure]: ./web-sites-staged-publishing.md
[Ativar HTTPS para uma aplicação no App Service do Azure]: ./app-service-web-tutorial-custom-ssl.md
[como: monitorizar o estado do ponto final web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Noções básicas de monitorização para Web Apps no App Service do Azure]: ./web-sites-monitor.md
[modo de pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensionar a uma aplicação web no App Service do Azure]: ./web-sites-scale.md
[Experimentar o App Service]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
