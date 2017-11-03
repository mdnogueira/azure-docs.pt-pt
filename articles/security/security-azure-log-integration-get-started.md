---
title: "Introdução à integração de registos do Azure | Microsoft Docs"
description: "Saiba como instalar o serviço de integração de registos do Azure e integrar registos a partir do armazenamento do Azure, os registos de auditoria do Azure e alertas do Centro de segurança do Azure."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 07/26/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9d39ecd513386b75b4b640721f80991caaf9ade8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Integração de registos do Azure com o registo de diagnóstico do Azure e reencaminhamento de eventos do Windows
Integração de registo do Azure (AzLog) permite-lhe integrar registos não processados a partir dos seus recursos do Azure para os sistemas Security Information and Event Management (SIEM) no local. Esta integração torna possível ter um dashboard unificado de segurança para todos os seus recursos, no local ou na nuvem, para que pode agregar, correlacionar, analisar e alerta para eventos de segurança associadas com as suas aplicações.
>[!NOTE]
Para obter mais informações sobre a integração de registo do Azure, pode rever o [descrição geral da integração de registo do Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-overview).

Este artigo irá ajudá-lo a começar com a integração de registo do Azure ao concentrar-se a instalação do serviço Azlog e integrar o serviço de diagnóstico do Azure. O serviço de integração de registo do Azure, em seguida, poderá recolher informações de registo de eventos do Windows a partir do canal de eventos de segurança do Windows provenientes de máquinas virtuais implementadas no IaaS do Azure. Isto é muito semelhante de "Reencaminhamento de eventos" que pode ter sido utilizada no local.

>[!NOTE]
>A capacidade para colocar o resultado de integração de registos do Azure no SIEM é fornecida por SIEM próprio. Consulte o artigo [integrar integração de registo do Azure com o SIEM no local](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) para obter mais informações.

Para ser muito claro – o serviço de integração de registo do Azure é executado num computador físico ou virtual que está a utilizar o Windows Server 2008 R2 ou superior de sistema operativo (Windows Server 2012 R2 ou Windows Server 2016 são preferenciais).

O computador físico pode ser executado no local (ou num site do fornecedor de alojamento). Se optar por executar o serviço de integração de registo do Azure numa máquina virtual, que a máquina virtual pode estar localizada no local ou numa nuvem pública, como o Microsoft Azure.

A máquina física ou virtual a executar o serviço de integração de registo do Azure necessita de conectividade de rede na nuvem pública do Azure. Os passos neste artigo fornecem detalhes sobre a configuração.

## <a name="prerequisites"></a>Pré-requisitos
No mínimo, a instalação do AzLog requer os seguintes itens:
* Um **subscrição do Azure**. Se não tiver uma conta, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* A **conta de armazenamento** que podem ser utilizados para o registo de diagnóstico do Windows Azure (a pode utilizar uma conta de armazenamento pré-configuradas ou criar um novo – Vamos demonstrar como configurar a conta de armazenamento neste artigo)
  >[!NOTE]
  Dependendo do seu cenário, uma conta de armazenamento não pode ser necessária. Cenário abordado neste artigo um é necessário o diagnóstico do Azure.
* **Dois sistemas**: uma máquina com o serviço de integração de registo do Azure e um computador que será monitorizado e têm as respetivas informações de registo enviadas para a máquina de serviço Azlog.
   * Uma máquina que pretende monitorizar – esta é uma VM em execução como um [Máquina Virtual do Azure](../virtual-machines/virtual-machines-windows-overview.md)
   * Uma máquina com o serviço de integração de registos do Azure; Esta máquina irá recolher todas as informações de registo mais tarde serão importadas para o SIEM.
    * Este sistema pode estar no local ou no Microsoft Azure.  
    * Tem de estar em execução um x64 versão do Windows server 2008 R2 SP1 ou superior e ter o .NET 4.5.1 instalado. Pode determinar a versão do .NET instalada seguindo o artigo intitulado [como: determinar que versões do .NET Framework estão instaladas](https://msdn.microsoft.com/library/hh925568)  
    Tem de ter conectividade para a conta de armazenamento do Azure utilizada para o registo de diagnóstico do Azure. Será, fornecemos instruções neste artigo sobre como pode confirmar este conectividade

Para uma demonstração rápida do processo de uma criação de uma máquina virtual utilizando o portal do Azure observe o vídeo abaixo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]



## <a name="deployment-considerations"></a>Considerações sobre implementação
Enquanto estiver a testar a integração de registo do Azure, pode utilizar qualquer sistema que cumpra os requisitos mínimos do sistema operativo. No entanto, para um ambiente de produção a carga pode exigir a planear aumentar ou reduzir.

Pode executar várias instâncias do serviço de integração de registo do Azure (uma instância por máquina física ou virtual), se o volume de eventos é elevada. Além disso, pode carregar Saldo contas de armazenamento de diagnóstico do Azure para Windows (WAD) e o número de subscrições fornecer às instâncias de devem basear-se nas suas da capacidade.
>[!NOTE]
Neste momento, não temos recomendações específicas para quando aumentar horizontalmente instâncias das máquinas de integração de registos do Azure (ou seja, as máquinas que estejam a executar o serviço de integração de registos do Azure) ou para contas de armazenamento ou subscrições. Dimensionamento decisões deve ser baseada no seu as observações de desempenho em cada uma destas áreas.

Também tem a opção para dimensionar o serviço de integração de registo do Azure para o ajudar a melhorar o desempenho. As métricas de desempenho seguintes podem ajudar a dimensionar as máquinas que optar por executar o serviço de integração de registos do Azure:
* Numa máquina 8-processador (principal), uma única instância Azlog integrador consegue processar cerca de 24 milhões de eventos por dia (~1M/hour).

* Numa máquina 4-processador (principal), uma única instância Azlog integrador consegue processar cerca de 1,5 milhões de eventos por dia (~62.5K/hour).

## <a name="install-azure-log-integration"></a>Instalar a integração de registos do Azure
Para instalar a integração de registo do Azure, terá de transferir o [integração de registos do Azure](https://www.microsoft.com/download/details.aspx?id=53324) ficheiro de instalação. Percorrer a rotina de configuração e decidir se pretende fornecer informações de telemetria para a Microsoft.  

![Instalação de ecrã com caixa de telemetria selecionada](./media/security-azure-log-integration-get-started/telemetry.png)

*
> [!NOTE]
> Recomendamos que permitem à Microsoft recolher dados de telemetria. Pode desativar a recolha de dados de telemetria desmarcando esta opção.
>


O serviço de integração de registo do Azure recolhe dados de telemetria a partir do computador no qual está instalado.  

Os dados telemétricos recolhidos são:

* Exceções que ocorrem durante a execução de integração de registos do Azure
* Métricas sobre o número de consultas e eventos processados
* Estatísticas sobre qual Azlog.exe Opções da linha de comandos estão a ser utilizadas

O processo de instalação é abrangido as vídeo abaixo.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>Publique os passos de instalação e validação
Depois de concluir a rotina de configuração básica, está pronto passo para efetuar a instalação de post e passos de validação:
1. Abra uma janela do PowerShell elevada e navegue para **c:\Program Files\Microsoft Azure registo integração**
2. É o primeiro passo que precisa de tomar para obter os Cmdlets de AzLog importados. Pode fazê-lo executando o script **LoadAzlogModule.ps1** (tenha em atenção a ". \ \" no comando seguinte). Tipo **.\LoadAzlogModule.ps1** e prima **ENTER**.  
Deverá ver algo semelhante que é apresentado na figura abaixo. </br></br>
![Instalação de ecrã com caixa de telemetria selecionada](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. Agora, é necessário configurar AzLog para utilizar um ambiente do Azure específico. Um "ambiente do Azure" é "type" de centro de dados em nuvem do Azure que pretende trabalhar. Enquanto existirem vários ambientes do Azure neste momento, as opções relevantes atualmente são **AzureCloud** ou **AzureUSGovernment**.   No seu ambiente de PowerShell elevada, certifique-se de que está no **c:\program files\Microsoft a integração de registo do Azure\** </br></br>
    Uma vez, execute o comando: </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud``(para o Azure comercial)

      >[!NOTE]
      Quando o comando for bem sucedida, não irá receber quaisquer comentários.  Se pretender utilizar a nuvem de US Government Azure, teria de utilizar **AzureUSGovernment** (para a variável-Name) para a nuvem do Governo dos EUA. Outras nuvens do Azure não são suportados neste momento.  
4. Para poder monitorizar um sistema, terá do nome da conta de armazenamento em utilização para o Azure Diagnostics.  No portal do Azure, navegue para **máquinas virtuais** e procure a máquina virtual que irá monitorizar. No **propriedades** secção, escolha **definições de diagnóstico**.  Clique em **agente** e anote o nome da conta de armazenamento especificado. Terá este nome de conta para o passo posterior.
![Definições de diagnóstico do Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

      ![Definições de diagnóstico do Azure](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)
      >[!NOTE]
      Se a monitorização não foi activada durante a criação da máquina virtual, terá a opção para ativá-la conforme mostrado acima.
5. Agora iremos irá mudar a nossa atenção volta para a máquina de integração de registos do Azure. Temos de verificar se tem conectividade para a conta de armazenamento do sistema onde instalou a integração de registo do Azure. O computador físico ou máquina virtual com o serviço de integração de registo do Azure tem acesso à conta de armazenamento para obter informações registadas pelo diagnósticos do Azure, conforme configurado no cada um dos sistemas monitorizados.  
  1. Pode transferir o Explorador de armazenamento do Azure [aqui](http://storageexplorer.com/).
  2. Percorrer a rotina de configuração
  3. Após a conclusão da instalação clique **seguinte** e deixe a caixa de verificação **iniciar o Explorador de armazenamento do Microsoft Azure** marcada.  
  4. Inicie sessão no Azure.
  5. Certifique-se de que pode ver a conta de armazenamento que configurou para o Azure Diagnostics.  
![Contas de armazenamento](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
   6. Tenha em atenção que existem algumas opções em contas de armazenamento. Uma delas é **tabelas**. Em **tabelas** deverá ver um chamado **WADWindowsEventLogsTable**. </br></br>
   ![Contas de armazenamento](./media/security-azure-log-integration-get-started/storage-explorer.png) </br>

## <a name="integrate-azure-diagnostic-logging"></a>Integrar o registo de diagnóstico do Azure
Neste passo, irá configurar o computador que executa o serviço de integração de registo do Azure para ligar à conta de armazenamento que contém os ficheiros de registo.
Para concluir este passo temos algumas coisas adiantado.  
* **FriendlyNameForSource:** este é um nome amigável que pode aplicar a conta de armazenamento que configurou a máquina virtual para armazenar informações de diagnóstico do Azure
* **StorageAccountName:** este é o nome da conta do storage que especificou quando configurou diagnotics do Azure.  
* **StorageKey:** esta é a chave de armazenamento para a conta de armazenamento onde as informações de diagnóstico do Azure são armazenadas para esta máquina virtual.  

Execute os passos seguintes para obter a chave de armazenamento:
 1. Navegue para o [portal do Azure](http://portal.azure.com).
 2. No painel de navegação da consola do Azure, desloque-se na parte inferior e clique em **mais serviços.**

 ![Mais serviços](./media/security-azure-log-integration-get-started/more-services.png)
 3. Introduza **armazenamento** no **filtro** caixa de texto. Clique em **contas do Storage** (Isto irá aparecer depois de introduzir **armazenamento**)

   ![caixa de filtro](./media/security-azure-log-integration-get-started/filter.png)
 4. Será apresentada uma lista de contas do storage, faça duplo clique na conta que atribuiu ao armazenamento de registo.

   ![lista de contas de armazenamento](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. Clique em **chaves de acesso** no **definições** secção.

  ![chaves de acesso](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. Cópia **chave1** e colocá-la numa localização segura que tem acesso para o passo seguinte.

   ![duas chaves de acesso](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. No servidor que instalou a integração de registo do Azure, abra uma linha de comandos elevada (tenha em atenção que estamos a utilizar uma janela de linha de comandos elevada aqui, não uma consola elevada do PowerShell).
 8. Navegue para **c:\Program Files\Microsoft Azure registo integração**
 9. Execute ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` </br> Por exemplo ``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`` se pretender que o ID de subscrição apareçam de eventos XML, acrescentar o ID de subscrição para o nome amigável: ``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`` ou, por exemplo,``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]  
Aguarde até 60 minutos, em seguida, ver os eventos que são solicitados da conta do storage. Para ver, abrir **Visualizador de eventos > registos do Windows > eventos reencaminhados** no integrador de Azlog.

Aqui pode ver um vídeo passar por passos abrangidos acima.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>E se dados não é apresentada na pasta eventos reencaminhados?
Se depois de uma hora de dados é não ser apresentado no **eventos reencaminhados** pasta, em seguida:

1. Verifique o computador que executa o serviço de integração de registo do Azure e confirme que se pode aceder ao Azure. Para testar a conectividade, tente abrir o [portal do Azure](http://portal.azure.com) do browser.
2. Certifique-se a conta de utilizador **Azlog** tem permissão de escrita na pasta **users\Azlog**.
  <ol type="a">
   <li>Abra **Explorador do Windows** </li>
  <li> Navegue para **c:\users** </li>
  <li> Clique em **c:\users\Azlog** </li>
  <li> Clique em **segurança**  </li>
  <li> Clique em **NT Service\Azlog** e verifique as permissões da conta. Se a conta está em falta a partir deste separador ou se as permissões adequadas não estão atualmente a mostrar pode conceder direitos de conta neste separador.</li>
  </ol>
3.Certifique-se a conta de armazenamento adicionada no comando **Adicionar origem Azlog** está listado quando executar o comando **lista de origem Azlog**.
4. Aceda a **Visualizador de eventos > registos do Windows > aplicação** ver se existem erros comunicados pela integração de registos do Azure.


Caso se depare com problemas durante a instalação e configuração, abra uma [pedido de suporte](../azure-supportability/how-to-create-azure-support-request.md), selecione **integração de registo** como o serviço para o qual está a pedir suporte.

Outra opção de suporte é o [fórum MSDN do Azure registo integração](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Aqui a Comunidade pode suportar entre si com perguntas, respostas, sugestões e truques sobre como obter o máximo partido da integração do registo do Azure. Além disso, a equipa de integração de registo do Azure monitoriza neste fórum e irão ajudá-lo sempre que possível.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a integração de registo do Azure, consulte os seguintes documentos:

* [Integração de registo do Microsoft Azure para os registos do Azure](https://www.microsoft.com/download/details.aspx?id=53324) – Centro de transferências para obter detalhes, requisitos de sistema e instalar as instruções na integração de registos do Azure.
* [Introdução à integração de registos do Azure](security-azure-log-integration-overview.md) – este documento apresenta-lhe integração de registos do Azure, as suas capacidades principais e como funciona.
* [Passos de configuração do parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – este blogue mostra-lhe como configurar a integração de registos do Azure para trabalhar com soluções de parceiros Splunk, HP ArcSight e IBM QRadar. Esta é a nossa orientação sobre como configurar os componentes do SIEM atual. Verifique junto do fabricante do SIEM primeiro para obter detalhes adicionais.
* [Registos do Azure integração perguntas mais frequentes (FAQ) do sobre](security-azure-log-integration-faq.md) -FAQ este respondem a dúvidas sobre a integração de registos do Azure.
* [Integrar o Centro de segurança de alertas com o Azure registo integração](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra como sincronizar alertas do Centro de segurança, juntamente com eventos de segurança de máquina virtual recolhidos pelo diagnósticos do Azure e os registos de atividade do Azure, com a sua análise de registos ou solução SIEM.
* [Novas funcionalidades de diagnóstico do Azure e os registos de auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta mensagem de blogue apresenta-lhe os registos de auditoria do Azure e outras funcionalidades que o ajudam a obterem informações sobre as operações dos seus recursos Azure.
