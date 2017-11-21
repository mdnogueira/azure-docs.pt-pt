---
title: "Controlar as alterações com o Log Analytics do Azure | Microsoft Docs"
description: "A solução de controlo de alterações no Log Analytics ajuda a identificar o software e as alterações de serviço do Windows que ocorrem no seu ambiente."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 528b569ff9ffb3659e9210ea70e3aa06921cfe0d
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>Controlar as alterações de software no seu ambiente com a solução de controlo de alterações

![Símbolo de controlo de alterações](./media/log-analytics-change-tracking/change-tracking-symbol.png)

Este artigo ajuda-o a utilizar a solução de controlo de alterações na análise de registos para identificar facilmente as alterações no seu ambiente. A solução controla as alterações ao software do Windows e Linux, os ficheiros do Windows e as chaves de registo, serviços do Windows e Linux daemons. Identificar as alterações de configuração pode ajudar a identificar problemas operacionais.

Instale a solução para atualizar o tipo de agente que tenha instalado. As alterações ao software instalado, os serviços do Windows e Linux daemons nos servidores monitorizados são lidos. Em seguida, os dados são enviados para o serviço de análise de registos na nuvem para processamento. Lógica é aplicada para os dados recebidos e o serviço em nuvem regista os dados. Ao utilizar as informações no dashboard do controlo de alterações, pode facilmente ver as alterações efetuadas na sua infraestrutura de servidor.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

* Tem de ter um [Windows](log-analytics-windows-agents.md), [do Operations Manager](log-analytics-om-agents.md), ou [Linux](log-analytics-linux-agents.md) agente em cada computador em que pretende monitorizar as alterações.
* Adicionar a solução de controlo de alterações à sua área de trabalho do OMS do [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview). Em alternativa, pode adicionar a solução utilizando as informações [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração adicional.

### <a name="configure-linux-files-to-track"></a>Configurar ficheiros de Linux para controlar
Utilize os seguintes passos para configurar ficheiros para controlar em computadores com Linux.

1. No portal do OMS, clique em **definições** (symbol engrenagem).
2. No **definições** página, clique em **dados**e, em seguida, clique em **controlo de ficheiro do Linux**.
3. Sob controlo de alterações do ficheiro de Linux, escreva o caminho completo, incluindo o nome de ficheiro do ficheiro que pretende controlar e, em seguida, clique em de **adicionar** símbolo. Por exemplo: "/etc/*.conf"
4. Clique em **Guardar**.  

> [!NOTE]
> Controlo de ficheiro de Linux tem capacidades adicionais, incluindo o controlo de diretório, recursão através de diretórios e o caráter universal de controlo.

### <a name="configure-windows-files-to-track"></a>Configurar ficheiros do Windows para controlar
Utilize os seguintes passos para configurar ficheiros para controlar em computadores Windows.

1. No portal do OMS, clique em **definições** (symbol engrenagem).
2. No **definições** página, clique em **dados**e, em seguida, clique em **controlo de ficheiro do Windows**.
3. Sob controlo de alterações do ficheiro de Windows, escreva o caminho completo, incluindo o nome de ficheiro do ficheiro que pretende controlar e, em seguida, clique em de **adicionar** símbolo. Por exemplo: C:\Program Files (x86) \Internet Explorer\iexplore.exe ou C:\Windows\System32\drivers\etc\hosts.
4. Clique em **Guardar**.  
   ![Registo de alterações do ficheiro de Windows](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurar as chaves de registo do Windows para controlar
Utilize os seguintes passos para configurar as chaves de registo para controlar em computadores Windows.

1. No portal do OMS, clique em **definições** (symbol engrenagem).
2. No **definições** página, clique em **dados**e, em seguida, clique em **controlo de registo do Windows**.
3. Sob controlo de alterações do registo de Windows, escreva a chave completa que pretende controlar e, em seguida, clique em de **adicionar** símbolo.
4. Clique em **Guardar**.  
   ![Registo de alterações do registo de Windows](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Explicação de propriedades de coleção de ficheiros do Linux
1. **Tipo**
   * **Ficheiro** (relatório de metadados do ficheiro - tamanho, data de modificação, hash, etc.)
   * **Diretório** (relatório metadados do diretório - tamanho, a data de modificação, etc.)
2. **Ligações** (processamento Linux symlink referências a outros ficheiros ou diretórios)
   * **Ignorar** (ignorar symlinks durante recursão por não incluir os ficheiros/diretórios referenciados)
   * **Siga** (siga a symlinks durante recursão para incluir os ficheiros/diretórios referenciados também)
   * **Gerir** (siga a symlinks e alterar o tratamento de conteúdo devolvido)

   > [!NOTE]   
   > A opção de ligações de "Gerir" não é recomendada. Obtenção de conteúdo do ficheiro não é suportada.

3. **Recurse** (Recurse através de níveis de pasta e controlar todos os ficheiros que cumprem a instrução de caminho)
4. **Sudo** (ativar o acesso aos ficheiros ou diretórios que necessitam de privilégios sudo)

### <a name="limitations"></a>Limitações
A solução de controlo de alterações não suporta atualmente os seguintes itens:

* Pastas (diretórios) para controlo de ficheiro do Windows
* Recursão para o ficheiro de controlo do Windows
* Os carateres universais para o controlo de ficheiro do Windows
* Variáveis de caminho
* Sistemas de ficheiros de rede
* Conteúdo do Ficheiro

Outras limitações:

* O **tamanho de ficheiro máximo** e valores de coluna são não utilizados na implementação atual.
* Se recolher ficheiros mais de 2500 no ciclo de coleção de 30 minutos, o desempenho da solução pode ser degradado.
* Quando o tráfego de rede é elevado, registos de alteração poderão demorar até um máximo de seis horas para apresentar.
* Se modificar a configuração enquanto um computador é encerrado, o computador poderá publicar as alterações do ficheiro que pertenciam a configuração anterior.

### <a name="known-issues"></a>Problemas conhecidos
A solução de controlo de alterações atualmente estão a ocorrer os seguintes problemas:
* Atualizações de correção não são recolhidas para Windows 10 criadores Update e no Windows Server 2016 Core RS3 máquinas.

## <a name="change-tracking-data-collection-details"></a>Alterar os detalhes de recolha de dados de controlo
Registo de alterações recolhe inventário de software e os metadados do serviço do Windows utilizando os agentes que tiver ativado.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para controlo de alterações.

| Plataforma | Direcionar o agente | Agente do Operations Manager | Agente Linux | Storage do Azure | O Operations Manager necessárias? | Dados de agente do Operations Manager enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows e Linux | &#8226; | &#8226; | &#8226; |  |  | &#8226; | cinco minutos para 50 minutos, consoante o tipo de alteração. Para obter mais informações, consulte a tabela seguinte. |


A tabela seguinte mostra a frequência de recolha de dados para os tipos de alterações.

| **Altere o tipo** | **frequência** | **Does****agente****enviar as diferenças quando encontradas?**  |
| --- | --- | --- |
| Registo do Windows | minutos de 50 | Não |
| Ficheiro do Windows | 30 minutos | Sim. Se não houver nenhuma alteração no 24 horas, é enviado um instantâneo. |
| Ficheiro do Linux | 15 minutos | Sim. Se não houver nenhuma alteração no 24 horas, é enviado um instantâneo. |
| Serviços Windows | 30 minutos | Sim, a cada 30 minutos quando se encontram as alterações. A cada 24 horas um instantâneo é enviado, independentemente da alteração. Por isso, o instantâneo é enviado, mesmo que não foram efetuadas alterações. |
| Aplicações daemons do Linux | 5 minutos | Sim. Se não houver nenhuma alteração no 24 horas, é enviado um instantâneo. |
| Software do Windows | 30 minutos | Sim, a cada 30 minutos quando se encontram as alterações. A cada 24 horas um instantâneo é enviado, independentemente da alteração. Por isso, o instantâneo é enviado, mesmo que não foram efetuadas alterações. |
| Software do Linux | 5 minutos | Sim. Se não houver nenhuma alteração no 24 horas, é enviado um instantâneo. |

### <a name="registry-key-change-tracking"></a>Alteração de chave de registo de controlo

Análise de registos efetua o registo do Windows, monitorização e controlo com a solução de controlo de alterações. O objetivo de monitorização de alterações a chaves de registo é identificar pontos de extensibilidade onde podem ativar o código de terceiros e de software maligno. A lista seguinte mostra as predefinições de chaves de registo que são controlados pela solução e por que motivo cada é controlada.

- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - Scripts de monitores que são executados no arranque.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - Scripts de monitores que são executados no encerramento.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - Monitoriza as chaves que são carregadas antes do utilizador iniciar a sessão na respetiva conta do Windows. A chave é utilizada para programas de 32 bits em execução nos computadores de 64 bits.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed componentes
    - Monitores alterações das definições de aplicação.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - Monitores comuns autostart entradas que ligue diretamente no Explorador do Windows e, normalmente, execução no processo com Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - Monitores comuns autostart entradas que ligue diretamente no Explorador do Windows e, normalmente, execução no processo com Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - Monitores comuns autostart entradas que ligue diretamente no Explorador do Windows e, normalmente, execução no processo com Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Monitores para o ícone de sobreposição de registo do processador.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Monitores para o ícone de sobreposição de registo de processador para programas de 32 bits em execução nos computadores de 64 bits.
- HKEY\_LOCAL\_objetos de programa auxiliar de MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser
    - Monitores para o novo browser auxiliar objeto plug-ins para o Internet Explorer. Utilizado para aceder a documentos Object Model (DOM) da página atual e para controlar a navegação.
- HKEY\_LOCAL\_objetos de programa auxiliar de MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser
    - Monitores para o novo browser auxiliar objeto plug-ins para o Internet Explorer. Utilizado para aceder a documentos Object Model (DOM) da página atual e para controlar a navegação para programas de 32 bits em execução nos computadores de 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - Monitores para novas extensões de Internet Explorer, tais como menus de ferramenta personalizada e botões da barra de ferramentas personalizadas.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - Monitores para novas extensões de Internet Explorer, tais como menus de ferramenta personalizada e botões da barra de ferramentas personalizadas para os programas de 32 bits em execução nos computadores de 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Monitoriza os controladores de 32 bits associados wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. É semelhante à secção [controladores] no sistema. Ficheiro INI.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Monitores para os controladores de 32 bits associados wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para programas de 32 bits em execução nos computadores de 64 bits. É semelhante à secção [controladores] no sistema. Ficheiro INI.
- HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - Monitoriza a lista de sistema conhecido ou frequentemente utilizada DLLs; Este sistema impede que pessoas explorá permissões do diretório de aplicação fracos, arrastando em versões de Trojan horse de DLLs do sistema.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - Monitoriza a lista de pacotes capazes de receber notificações de eventos do Winlogon, o modelo de suporte de início de sessão interativo para o sistema operativo Windows.


## <a name="use-change-tracking"></a>Utilizar o registo de alterações
Depois da solução estiver instalada, pode ver o resumo de alterações para os servidores monitorizados através do **alterações** mosaico a **descrição geral** página no OMS.

![imagem de controlo de alterações mosaico](./media/log-analytics-change-tracking/change-tracking-tile.png)

Pode ver as alterações à sua infraestrutura e, em seguida, desagregação em detalhes para as seguintes categorias:

* Alterações por tipo de configuração para o software e serviços do Windows
* Alterações de software para aplicações e atualizações para servidores individuais
* Número total de alterações de software para cada aplicação
* Pacotes de Linux
* Alterações de serviço do Windows para servidores individuais
* Alterações do Linux daemon

![imagem do dashboard do controlo de alterações](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![imagem do dashboard do controlo de alterações](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>Para ver as alterações para qualquer alterar tipo
1. No **descrição geral** página, clique em de **alterações** mosaico.
2. No **Alterar controlo** dashboard, reveja as informações de resumo de uma das seguintes painéis de tipo de alteração e, em seguida, clique num para ver informações detalhadas acerca do mesmo no **pesquisa registo** página.
3. Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por tempo, os resultados detalhados e o histórico de pesquisa de registo. Também pode filtrar por facetas para restringir os resultados.

## <a name="next-steps"></a>Passos seguintes
* Utilize [pesquisas de registo na análise de registos](log-analytics-log-searches.md) para ver dados de registo de alterações de detalhado.
