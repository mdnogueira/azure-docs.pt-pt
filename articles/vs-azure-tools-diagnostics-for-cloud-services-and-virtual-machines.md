---
title: "Configurar diagnósticos para máquinas virtuais e serviços Cloud do Azure | Microsoft Docs"
description: "Saiba como configurar diagnósticos de depuração do Azure cloude serviços e máquinas virtuais (VMs) no Visual Studio."
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: ghogen
editor: 
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: f00771d89749e7507d7f303f366fe63f537900ff
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Configurar diagnósticos para máquinas virtuais e serviços Cloud do Azure
Quando precisar de resolver problemas de uma máquina virtual ou serviço em nuvem do Azure, pode utilizar o Visual Studio para configurar mais facilmente o diagnóstico do Azure. Diagnóstico de captura de dados de sistema e dados de registo em máquinas virtuais e instâncias de máquina virtual com o seu serviço em nuvem. Dados de diagnóstico são transferidos para uma conta de armazenamento que escolher. Para obter mais informações sobre diagnósticos registo no Azure, consulte o artigo [ativar o registo de diagnóstico para Web Apps no App Service do Azure](app-service/web-sites-enable-diagnostic-log.md).

Neste artigo, vamos mostrar-lhe como utilizar o Visual Studio para ativar e configurar o Azure Diagnostics, manutenção automática antes e após a implementação. Saiba como configurar o diagnóstico em máquinas virtuais do Azure, como selecionar os tipos de informações de diagnóstico para recolher e como visualizar as informações após o qual está a ser recolhido.

Pode utilizar uma das seguintes opções para configurar diagnósticos do Azure:

* Alterar as definições de diagnóstico no **configuração de diagnósticos** caixa de diálogo no Visual Studio. As definições são guardadas num ficheiro denominado diagnostics.wadcfgx (no Azure SDK 2.4 e versões anteriores, o ficheiro é denominado diagnostics.wadcfg). É também possível modificar diretamente o ficheiro de configuração. Se atualizar o ficheiro manualmente, o efeito de tomar de alterações de configuração da próxima vez que implementou o cloud service para o Azure ou executar o serviço no emulador.
* Utilizar o Explorador de nuvem ou Explorador de servidores no Visual Studio para alterar as definições de diagnóstico para um serviço em nuvem ou máquina virtual que está em execução.

## <a name="azure-sdk-26-diagnostics-changes"></a>Alterações de diagnóstico do Azure SDK 2.6
As alterações seguintes aplicam-se para o Azure SDK 2.6 e projetos posteriores no Visual Studio:

* O emulador local agora suporta diagnóstico. Isto significa que pode recolher dados de diagnóstico e certifique-se de que a sua aplicação cria os rastreios direitos ao desenvolver e testar no Visual Studio. A cadeia de ligação `UseDevelopmentStorage=true` ativa a recolha de dados de diagnóstico enquanto estiver a executar o projeto de serviço em nuvem no Visual Studio, utilizando o emulador do storage do Azure. Todos os dados de diagnóstico são recolhidos na conta de armazenamento de armazenamento de desenvolvimento.
* A cadeia de ligação de conta de armazenamento do diagnostics `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` é armazenado no ficheiro de configuração (. cscfg) do serviço. No Azure SDK 2.5, a conta de armazenamento do diagnostics é especificada no ficheiro diagnostics.wadcfgx.

A cadeia de ligação funciona de forma diferente de algumas formas de chaves no Azure SDK 2.6 e mais tarde em comparação com o Azure SDK 2.4 e versões anteriores:

* No Azure SDK 2.4 e versões anteriores, a cadeia de ligação é utilizada como um tempo de execução, o plug-in de diagnóstico para obter as informações de conta de armazenamento para transferir os registos de diagnóstico.
* No Azure SDK 2.6 ou posterior, o Visual Studio utiliza a cadeia de ligação de diagnóstico para configurar a extensão de diagnóstico do Azure com as informações de conta de armazenamento adequado durante a publicação. Pode utilizar a cadeia de ligação para definir as contas de armazenamento diferentes para as configurações de outro serviço que utiliza o Visual Studio durante a publicação. No entanto, porque o diagnóstico Plug-in não está disponível após 2.5 de SDK do Azure, o ficheiro. cscfg por si só não é possível configurar a extensão de diagnóstico. Tem de configurar a extensão separadamente, utilizando ferramentas como o Visual Studio ou o PowerShell.
* Para simplificar o processo de configurar a extensão de diagnóstico com o PowerShell, o resultado de pacote do Visual Studio inclui o XML de configuração pública para a extensão de diagnóstico para cada função. Visual Studio utiliza a cadeia de ligação de diagnóstico para preencher as informações de conta de armazenamento na configuração pública. Os ficheiros de configuração pública são criados na pasta de extensões. Os ficheiros de configuração pública de utilizar o padrão de nomenclatura PaaSDiagnostics. &lt;nome da função\>. PubConfig.xml. Todas as implementações baseadas no PowerShell podem utilizar este padrão para mapear cada configuração para uma função.
* O [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) utiliza a cadeia de ligação no ficheiro. cscfg para aceder aos dados de diagnóstico. São apresentados os dados no **monitorização** separador. A cadeia de ligação é necessária para definir o serviço para mostrar dados de monitorização verbosos no portal.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Migrar projetos ao Azure SDK 2.6 ou posterior
Ao migrar a partir do Azure SDK 2.5 para o Azure SDK 2.6 ou posterior, se tiver uma conta de armazenamento do diagnostics especificada no ficheiro .wadcfgx, a conta de armazenamento permanece nesse ficheiro. Para tirar partido da flexibilidade de utilização de contas de armazenamento diferentes para configurações de armazenamento diferente, adicione manualmente a cadeia de ligação ao seu projeto. Se estiver a migrar um projeto do Azure SDK 2.4 ou anteriormente para o Azure SDK 2.6, as cadeias de ligação de diagnóstico são preservadas. No entanto, tenha em atenção que as alterações na forma como as cadeias de ligação são tratadas num 2.6 de SDK do Azure, descrito na secção anterior.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico
* Se for especificada uma cadeia de ligação de diagnóstico num ficheiro. cscfg, o Visual Studio utiliza-o para configurar a extensão de diagnóstico durante a publicação e ao gerar os ficheiros XML de configuração pública durante empacotamento.
* Se uma cadeia de ligação do diagnóstico não é especificada no ficheiro. cscfg, Visual Studio retrocede para utilizando a conta de armazenamento que é especificada no ficheiro .wadcfgx para configurar a extensão de diagnóstico para a publicação e para gerar o XML de configuração pública ficheiros durante o empacotamento.
* A cadeia de ligação de diagnóstico num ficheiro. cscfg tem precedência sobre a conta do storage no ficheiro .wadcfgx. Se uma cadeia de ligação de diagnóstico é especificado no ficheiro. cscfg, o Visual Studio utiliza essa cadeia de ligação e ignora a conta de armazenamento .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>O que faz a caixa de verificação "Atualizar as cadeias de ligação de armazenamento de desenvolvimento..."?
O **atualizar as cadeias de ligação de armazenamento de desenvolvimento de diagnóstico e de colocação em cache com credenciais de conta de armazenamento do Microsoft Azure durante a publicação para o Microsoft Azure** caixa de verificação é uma forma conveniente para atualizar qualquer armazenamento de desenvolvimento cadeias de ligação de conta com a conta de armazenamento do Azure que especificou durante a publicação.

Por exemplo, se selecionar esta caixa de verificação e a cadeia de ligação de diagnóstico especifica `UseDevelopmentStorage=true`, ao publicar o projeto no Azure, Visual Studio atualiza automaticamente a cadeia de ligação de diagnóstico com a conta de armazenamento que especificou no o Assistente de publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de ligação de diagnóstico, essa conta é utilizada em vez disso.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico no Azure SDK 2.4 e vs anteriores. SDK do Azure 2,5 e posterior
Se estiver a atualizar o seu projeto do Azure SDK 2.4 e anteriormente para o Azure SDK 2.5 ou posterior, tenha em atenção as seguintes diferenças de funcionalidade de diagnóstico:

* **APIs de configuração foram preteridas**. A configuração programática do diagnostics está disponível no Azure SDK 2.4 e anterior, mas foi preterida no Azure SDK 2.5 e posterior. Se a configuração de diagnósticos atualmente está definida no código, tem de reconfigurar essas definições a partir do zero no projeto migrado para obter um diagnóstico para manter a funcionar. O ficheiro de configuração de diagnóstico para o Azure SDK 2.4 é diagnostics.wadcfg. O ficheiro de configuração de diagnóstico para o Azure SDK 2.5 e posterior é diagnostics.wadcfgx.
* **Diagnósticos para aplicações de serviço em nuvem podem ser configurados apenas ao nível da função**. No Azure SDK 2.5 e posterior, não é possível configurar diagnósticos para aplicações de serviço em nuvem a nível de instância.
* **Sempre que implementar a sua aplicação, a configuração de diagnóstico foi atualizada**. Isto pode causar problemas de paridade, se alterar a configuração de diagnósticos Visual Studio no Explorador de servidores e, em seguida, volte a implementar a sua aplicação.
* **No Azure SDK 2.5 e posterior, informações de estado de falha são configuradas no ficheiro de configuração de diagnóstico e não no código**. Se tiver configuradas no código de capturas de falhas, deve transferir manualmente a configuração a partir do código para o ficheiro de configuração. Capturas de falhas não são transferidas durante a migração para o Azure SDK 2.6.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Ative os diagnósticos no projetos do serviço em nuvem antes de implementá-las
No Visual Studio, pode recolher dados de diagnóstico para funções que são executados no Azure quando executa o serviço no emulador do antes da implementação. Todas as alterações às definições de diagnóstico no Visual Studio são guardadas no ficheiro de configuração de diagnostics.wadcfgx. Estas definições especificam a conta do storage onde os dados de diagnóstico são guardados quando implementar o serviço de nuvem.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>Para ativar diagnósticos no Visual Studio antes da implementação

1. No menu de atalho para a função, selecione **propriedades**. A função **propriedades** caixa de diálogo, selecione o **configuração** separador.
2. No **diagnóstico** secção, certifique-se de que o **ativar diagnósticos** caixa de verificação está selecionada.
   
    ![A opção de ativar o diagnóstico de acesso](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Para especificar a conta de armazenamento para os dados de diagnóstico, selecione o botão de reticências (…).
   
    ![Especifique a conta de armazenamento a utilizar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. No **criar cadeia de ligação de armazenamento** diálogo caixa, especifique se pretende estabelecer a ligação utilizando o emulador do storage do Azure, uma subscrição do Azure, ou introduzir manualmente as credenciais.
   
    ![Caixa de diálogo de conta de armazenamento](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Se selecionar **emulador de armazenamento do Microsoft Azure**, a cadeia de ligação está definida como `UseDevelopmentStorage=true`.
   * Se selecionar **sua subscrição**, pode selecionar a subscrição do Azure que pretende utilizar e introduza um nome de conta. Para gerir as subscrições do Azure, selecione **gerir contas**.
   * Se selecionar **introduzir manualmente as credenciais**, introduza o nome e a chave da conta do Azure que pretende utilizar.
5. Para ver o **configuração de diagnósticos** caixa de diálogo, selecione **configurar**. Com exceção do **geral** e **registo diretórios**, cada separador Representa uma origem de dados de diagnóstico que pode recolher. A predefinição **geral** separador oferece o diagnóstico seguinte opções de recolha de dados: **apenas erros**, **todas as informações**, e **plano personalizada**. A predefinição **apenas erros** opção utiliza o mínimo de armazenamento, porque não transferir avisos ou mensagens de rastreio. O **todas as informações** opção transfere mais informações, utiliza mais armazenamento e, por conseguinte, é a opção mais dispendiosa.
   
    ![Ativar o diagnóstico do Azure e a configuração](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. Para este exemplo, selecione o **plano personalizada** opção, pelo que pode personalizar os dados recolhidos.
7. No **Quota de disco em MB** caixa, pode definir a quantidade de espaço em alocar na sua conta de armazenamento para dados de diagnóstico. Pode alterar ou aceite o valor predefinido.
8. Em cada separador de dados de diagnóstico que pretende recolher, selecione o **ativar transferência do \<tipo de registo\>**  caixa de verificação. Por exemplo, se pretender recolher registos de aplicação, no **registos da aplicação** separador, selecione o **ativar a transferência dos registos de aplicação** caixa de verificação. Além disso, especifique as informações que são necessárias para cada tipo de dados de diagnóstico. Para informações de configuração para cada separador, consulte a secção **configurar origens de dados de diagnóstico** posteriormente neste artigo. 
9. Depois de ativar a coleção de todos os dados de diagnóstico que pretende, selecione **OK**.
10. Execute o projeto de serviço em nuvem do Azure no Visual Studio como habitualmente. Como utilizar a aplicação, as informações de registo que ativou são guardadas para a conta de armazenamento do Azure que especificou.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Ative os diagnósticos em máquinas virtuais do Azure
No Visual Studio, pode recolher dados de diagnóstico para máquinas virtuais do Azure.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Para ativar o diagnóstico em máquinas virtuais do Azure

1. No Explorador de servidores, selecione o nó do Azure e, em seguida, ligar a sua subscrição do Azure, se ainda não está ligado.
2. Expanda o **máquinas virtuais** nós. Pode criar uma nova máquina virtual ou selecione um nó existente.
3. No menu de atalho para a máquina virtual que pretende, selecione **configurar**. É apresentada a caixa de diálogo de configuração de máquina virtual.
   
    ![Configurar uma máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Se ainda não estiver instalado, adicione a extensão de diagnóstico de agente de monitorização da Microsoft. Com esta extensão, é possível recolher dados de diagnóstico para a máquina virtual do Azure. Em **extensões instaladas**, no **Selecione uma extensão disponível** caixa de lista pendente, selecione **diagnóstico de agente de monitorização Microsoft**.
   
    ![Instalar uma extensão de máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Outras extensões de diagnóstico estão disponíveis para as máquinas virtuais. Para obter mais informações, consulte [extensões de Máquina Virtual e funcionalidades para o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. Para adicionar a extensão e ver o **configuração de diagnósticos** caixa de diálogo, selecione **adicionar**.
6. Para especificar uma conta de armazenamento, selecione **configurar**e, em seguida, selecione **OK**.
   
    Cada separador (exceto para **geral** e **registo diretórios**) representa uma origem de dados de diagnóstico que pode recolher.
   
    ![Ativar o diagnóstico do Azure e a configuração](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    O separador predefinido, **geral**, oferece as seguintes opções de recolha de dados de diagnóstico: **apenas erros**, **todas as informações**, e **plano personalizada**. A opção predefinida, **apenas erros**, demora a quantidade, pelo menos, de armazenamento porque não transferir avisos ou mensagens de rastreio. O **todas as informações** opção transfere mais informações e é, por conseguinte, a opção mais dispendiosa em termos de armazenamento.
7. Para este exemplo, selecione o **plano personalizada** opção, pelo que pode personalizar os dados recolhidos.
8. O **Quota de disco em MB** caixa especifica a quantidade de espaço que pretende alocar na sua conta de armazenamento para dados de diagnóstico. Pode alterar o valor predefinido se pretender.
9. Em cada separador de dados de diagnóstico que pretende recolher, selecione o **ativar transferência do \<tipo de registo\>**  caixa de verificação.
   
    Por exemplo, se pretender recolher registos de aplicação, selecione o **ativar a transferência dos registos de aplicação** caixa de verificação a **registos da aplicação** separador. Além disso, especifique as informações necessárias para cada tipo de dados de diagnóstico. Para informações de configuração para cada separador, consulte a secção **configurar origens de dados de diagnóstico** posteriormente neste artigo.
10. Depois de ativar a coleção de todos os dados de diagnóstico que pretende, selecione **OK**.
11. Guarde o projeto atualizado.
    
    Uma mensagem no **registo de atividade do Microsoft Azure** janela indica que a máquina virtual foi atualizada.

## <a name="set-up-diagnostics-data-sources"></a>Configurar origens de dados de diagnóstico
Depois de ativar a recolha de dados de diagnóstico, pode escolher exatamente que origens de dados que pretende recolher e as informações recolhidas. As secções seguintes descrevem os separadores no **configuração de diagnósticos** caixa de diálogo e significa que cada opção de configuração.

### <a name="application-logs"></a>Registos de aplicações
Os registos de aplicações tem informações de diagnóstico que são produzidas por uma aplicação web. Se pretender capturar os registos de aplicações, selecione o **ativar a transferência dos registos de aplicação** caixa de verificação. Para aumentar ou diminuir o intervalo entre a transferência de registos da aplicação para a sua conta do storage, altere o **período de transferência (mín)** valor. Também pode alterar a quantidade de informação capturada no registo de definindo a **nível de registo** valor. Por exemplo, seleccione **verboso** para obter mais informações ou selecione **crítico** para capturar apenas erros críticos. Se tiver um fornecedor de diagnóstico específico que emite os registos de aplicações, pode capturar os registos ao Adicionar GUID o fornecedor no **GUID de fornecedor** caixa.

  ![Registos de aplicações](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Para obter mais informações sobre os registos de aplicações, consulte [ativar o registo de diagnóstico para Web Apps no App Service do Azure](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Registos de eventos do Windows
Para capturar os registos de eventos do Windows, selecione o **ativar a transferência de registos de eventos do Windows** caixa de verificação. Para aumentar ou diminuir o intervalo entre a transferência de registos de eventos para a sua conta do storage, altere o **período de transferência (mín)** valor. Selecione as caixas de verificação para os tipos de eventos que pretende controlar.

![Registos de eventos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Se estiver a utilizar o Azure SDK 2.6 ou posterior e pretender especificar uma origem de dados personalizada, introduza-o no  **\<nome da origem de dados\>**  caixa de texto e, em seguida, selecione **adicionar**. A origem de dados é adicionada ao ficheiro diagnostics.cfcfg.

Se estiver a utilizar o Azure SDK 2.5 e pretender especificar uma origem de dados personalizados, pode adicioná-lo para o `WindowsEventLog` secção o diagnostics.wadcfgx, ficheiro, como no exemplo seguinte:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Contadores de desempenho
Informações do contador de desempenho podem ajudar a localizar congestionamentos de sistema e otimizar o desempenho do sistema e aplicação. Para obter mais informações, consulte [criar e utilizar os contadores de desempenho na aplicação do Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx). Para capturar os contadores de desempenho, selecione o **ativar a transferência dos contadores de desempenho** caixa de verificação. Para aumentar ou diminuir o intervalo entre a transferência de registos de eventos para a sua conta do storage, altere o **período de transferência (mín)** valor. Selecione as caixas de verificação para os contadores de desempenho que pretende controlar.

![Contadores de desempenho](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Para monitorizar um contador de desempenho que não está listado, introduza o contador de desempenho utilizando a sintaxe sugerida. e, em seguida, selecione **adicionar**. O sistema operativo na máquina virtual determina quais os contadores de desempenho pode controlar. Para obter mais informações sobre sintaxe, consulte [Especifique um caminho de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Registos de infraestrutura
Registos de infraestrutura tem informações sobre a infraestrutura de diagnóstico do Azure, o módulo de RemoteAccess e RemoteForwarder módulo. Para recolher informações sobre os registos de infraestrutura, selecione o **ativar a transferência dos registos de infraestrutura** caixa de verificação. Para aumentar ou diminuir o intervalo entre a transferência de registos de infraestrutura para a sua conta do storage, altere o **período de transferência (mín)** valor.

![Registos de infraestrutura de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

Para obter mais informações, consulte [recolher dados de registo utilizando o Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Diretórios de registo
Diretórios de registo têm dados recolhidos a partir de diretórios de registo para pedidos de serviços de informação Internet (IIS), pedidos falhados ou pastas que escolher. Para capturar os diretórios de registo, selecione o **ativar a transferência dos diretórios de registo** caixa de verificação. Para aumentar ou diminuir o intervalo entre a transferência de registos para a sua conta do storage, altere o **período de transferência (mín)** valor.

Selecione as caixas de verificação de registos que pretende recolher, tais como **registos de IIS** e **falhou pedido** registos. São fornecidos os nomes de contentor de armazenamento predefinido, mas pode alterar os nomes.

Pode capturar os registos de qualquer pasta. Especifique o caminho no **registo do diretório absoluto** secção e, em seguida, selecione **Adicionar diretório**. Os registos são capturados nos contentores especificados.

![Diretórios de registo](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>Registos ETW
Se utilizar [rastreio de eventos para o Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW) e pretende capturar os registos ETW, selecione o **ativar a transferência dos registos de ETW** caixa de verificação. Para aumentar ou diminuir o intervalo entre a transferência de registos para a sua conta do storage, altere o **período de transferência (mín)** valor.

Os eventos são capturados a partir de origens de eventos e manifestos de eventos que especificar. Para especificar uma origem de evento, o **origens de eventos** secção, introduza um nome e, em seguida, selecione **Adicionar origem de evento**. Da mesma forma, pode especificar um manifesto de eventos no **manifestos de eventos** secção e, em seguida, selecione **adicionar evento manifesto**.

![Registos ETW](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

A estrutura ETW é suportada no ASP.NET através de classes no [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) espaço de nomes. O espaço de nomes do Microsoft.WindowsAzure.Diagnostics que herda e expande padrão [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) classes, permitem a utilização de [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) como um registo Framework no ambiente do Azure. Para obter mais informações, consulte [assumir o controlo de registo e rastreio no Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [ative os diagnósticos no Cloud Services do Azure e máquinas virtuais](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Informações de falhas
Para capturar informações sobre quando falhas de uma instância de função, selecione o **ativar transferência de informações de falhas** caixa de verificação. (Porque o ASP.NET processa a maioria das exceções, isto é geralmente úteis apenas para as funções de trabalho.) Para aumentar ou diminuir a percentagem de espaço de armazenamento dedicado para as informações de estado de falha, altere o **Quota de diretório (%)** valor. Pode alterar o contentor de armazenamento onde são armazenadas as informações de estado de falha e selecione se pretende capturar um **completa** ou **Mini** informação.

Os processos atualmente a ser controlados estão listados na seguinte captura de ecrã. Selecione as caixas de verificação para os processos que pretende capturar. Para adicionar outro processo à lista, introduza o nome do processo e, em seguida, selecione **processo adicionar**.

![Informações de falhas](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

Para obter mais informações, consulte [assumir o controlo de registo e rastreio no Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [4 do Microsoft Azure Diagnostics parte: os componentes de registo personalizado e alterações de Azure Diagnostics 1.3](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>Ver os dados de diagnóstico
Após ter recolhido os dados de diagnóstico para um serviço em nuvem ou máquina virtual, pode vê-la.

### <a name="to-view-cloud-service-diagnostics-data"></a>Para ver os dados de diagnóstico do serviço de nuvem
1. Implementar o serviço em nuvem como habitual e, em seguida, executá-la.
2. Pode ver os dados de diagnóstico num relatório que gera o Visual Studio ou nas tabelas na sua conta de armazenamento. Para ver os dados num relatório, abra o Explorador de nuvem ou Explorador de servidores, abra o menu de atalho do nó para a função que pretende e, em seguida, selecione **ver dados de diagnóstico**.
   
    ![Ver dados de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    É apresentado um relatório que mostre os dados disponíveis.
   
    ![Relatório de diagnóstico do Microsoft Azure no Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Se os dados mais recentes não são apresentados, poderá ter de aguardar o período de transferência para a decorrer.
   
    Para atualizar imediatamente os dados, selecione o **atualizar** ligação. Para que os dados atualizados automaticamente, selecione um intervalo no **atualização automática** caixa de lista pendente. Para exportar os dados de erro, selecione o **exportar para CSV** botão para criar um ficheiro de valores separados por vírgulas que pode abrir dentro de uma folha de cálculo do Excel.
   
    No Explorador de nuvem ou no Explorador de servidores, abra a conta de armazenamento que está associada a implementação.
3. Abra as tabelas de diagnósticos no Visualizador de tabela e, em seguida, reveja os dados recolhidos. Para registos de IIS e registos personalizados, pode abrir um contentor do blob. A tabela seguinte lista as tabelas ou contentores de BLOBs que contêm os dados para os diferentes ficheiros de registo. Para além dos dados para esse ficheiro de registo, as entradas de tabela contenham **EventTickCount**, **DeploymentId**, **função**, e **RoleInstance** , para ajudar a identificar que máquina virtual e a função gerados os dados e quando. 
   
   | Dados de diagnóstico | Descrição | Localização |
   | --- | --- | --- |
   | Registos de aplicações |Os registos que gera o código ao chamar os métodos do **Trace** classe. |WADLogsTable |
   | Registos de eventos |Dados de registos de eventos do Windows nas máquinas virtuais. Windows armazena informações nestes registos, mas as aplicações e serviços também utilizam os registos de erros de relatório ou informações de registo. |WADWindowsEventLogsTable |
   | Contadores de desempenho |É possível recolher dados em qualquer contador de desempenho que está disponível na máquina virtual. O sistema operativo fornece os contadores de desempenho, que incluem estatísticas muitos, como o tempo de processador e utilização de memória. |WADPerformanceCountersTable |
   | Registos de infraestrutura |Registos gerados a partir da infraestrutura de diagnóstico próprio. |WADDiagnosticInfrastructureLogsTable |
   | Registos do IIS |Registos que registam pedidos web. Se o seu serviço em nuvem obtém uma quantidade significativa de tráfego, estes registos podem ser demorados. É uma boa ideia para recolher e armazenar estes dados apenas quando precisar dele. |Pode encontrar os registos de pedido falhou no contentor do blob em wad-iis-failedreqlogs sob um caminho para essa instância, a função e a implementação. Pode encontrar registos completos em wad-iis-logfiles. As entradas para cada ficheiro que são efetuadas na tabela WADDirectories. |
   | Informações de falhas |Fornece imagens binárias do processo do seu serviço em nuvem (normalmente uma função de trabalho). |contentor de BLOBs de capturas de crush WAD |
   | Ficheiros de registo personalizado |Registos de dados que lhe predefinidos. |Pode especificar no código a localização dos ficheiros de registo personalizado na sua conta de armazenamento. Por exemplo, pode especificar um contentor do blob personalizado. |
4. Se são truncados dados de qualquer tipo, pode experimentar aumentar a memória intermédia para que os dados de tipo ou encurtar o intervalo entre as transferências de dados da máquina virtual à sua conta de armazenamento.
5. (Opcional) Remova os dados da conta do storage ocasionalmente para reduzir os custos de armazenamento geral.
6. Quando efetuar uma implementação completa, o ficheiro de diagnostics.cscfg (.wadcfgx para o Azure SDK 2.5) está atualizado no Azure e o serviço em nuvem escolherá quaisquer alterações à sua configuração de diagnóstico. Se atualizar uma implementação existente em vez disso, o ficheiro. cscfg não está atualizado no Azure. Ainda pode alterar as definições de diagnóstico, seguindo os passos na secção seguinte. Para obter mais informações sobre como efetuar uma implementação completa e atualizar uma implementação existente, consulte [publicar Assistente da aplicação Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Para ver os dados de diagnóstico de máquina virtual
1. No menu de atalho para a máquina virtual, selecione **ver dados de diagnóstico**.
   
    ![Ver dados de diagnóstico numa máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    O **resumo de diagnóstico** é apresentada a caixa de diálogo.
   
    ![Resumo de diagnóstico de máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Se os dados mais recentes não são apresentados, poderá ter de aguardar o período de transferência para a decorrer.
   
    Para atualizar imediatamente os dados, selecione o **atualizar** ligação. Para que os dados atualizados automaticamente, selecione um intervalo no **atualização automática** caixa de lista pendente. Para exportar os dados de erro, selecione o **exportar para CSV** botão para criar um ficheiro de valores separados por vírgulas que pode abrir dentro de uma folha de cálculo do Excel.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>Configurar o diagnóstico de serviço de nuvem após a implementação
Se estiver a investigar um problema com um serviço em nuvem que já está em execução, pode querer recolher dados que não especificou antes originalmente implementou a função. Neste caso, pode começar a recolher dados alterando as definições no Explorador de servidores. Pode configurar diagnósticos para uma única instância ou para todas as instâncias de uma função, dependendo se abrir o **configuração de diagnósticos** caixa de diálogo no menu de atalho para a instância de ou para a função. Se configurar o nó de função, quaisquer alterações que efetuar se aplicam a todas as instâncias. Se configurar o nó de instância, quaisquer alterações efetuadas aplicam-se apenas a essa instância.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>Para configurar diagnósticos para um serviço em nuvem em execução
1. No Explorador de servidores, expanda o **serviços em nuvem** nó e, em seguida, expanda a lista de nós para localizar a função ou instância (ou ambos) que pretende investigar.
   
    ![Configurar o diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. No menu de atalho para um nó de instância ou nó de função, selecione **as definições de diagnóstico de atualização**e, em seguida, selecione as definições de diagnóstico que pretende recolher.
   
    Para obter informações sobre as definições de configuração, consulte a secção **configurar origens de dados de diagnóstico** neste artigo. Para obter informações sobre como visualizar os dados de diagnóstico, consulte a secção **ver os dados de diagnóstico** neste artigo.
   
    Se alterar a recolha de dados no Explorador de servidores, as alterações permanecem em vigor até totalmente de Reimplementar o seu serviço em nuvem. Se utilizar a predefinição definições de publicação, as alterações não são substituídas. Definição de publicar a predefinição é para atualizar a implementação existente, em vez de fazer uma reimplementação completa. Para garantir que as definições de limpar no momento da implementação, vá para o **definições avançadas** separador no Assistente de publicação e, em seguida, desmarque a **atualização da implementação** caixa de verificação. Quando implementar novamente com essa caixa de verificação desmarcada, as definições de reverter no ficheiro .wadcfgx (ou .wadcfg) como conjunto através de **propriedades** editor para a função. Se atualizar a implementação, o Azure mantém as definições anteriores.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Resolver problemas de serviço em nuvem do Azure
Se tiver problemas com os projetos do serviço em nuvem, como uma função que obtém bloqueada no estado "ocupado", repetidamente recicla ou emitir um erro de servidor interno, existem ferramentas e técnicas que pode utilizar para diagnosticar e corrigir o problema. Para obter exemplos de problemas e soluções comuns específicos e para uma descrição geral dos conceitos e as ferramentas que pode utilizar para diagnosticar e corrigir estes erros, consulte [dados de diagnóstico de computação do Azure PaaS](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>P&R
**Qual é o tamanho de memória intermédia e qual o tamanho máximo deve ser?**

Em cada instância de máquina virtual, quotas limitam a quantidade de dados diagnóstico pode ser armazenado no sistema de ficheiros local. Além disso, especificar um tamanho de memória intermédia para cada tipo de dados de diagnóstico que está disponíveis. Este tamanho de memória intermédia funciona como uma quota individuais para esse tipo de dados. Para determinar a quota geral e a quantidade de memória que permanece, consulte a parte inferior da caixa de diálogo para o tipo de dados de diagnóstico. Se especificar memórias intermédias maiores ou mais tipos de dados, irá abordar a quota geral. Pode alterar a quota global ao modificar o ficheiro de configuração diagnostics.wadcfg ou .wadcfgx. Os dados de diagnóstico são armazenados no mesmo sistema de ficheiros de dados da sua aplicação. Se a sua aplicação utiliza uma grande quantidade de espaço em disco, não deve aumentar a quota de diagnóstico geral.

**O que é o período de transferência e quanto deve ser?**

O período de transferência é a quantidade de tempo que capture decorrer entre dados. Após cada período de transferência, é mover os dados de sistema de ficheiros local numa máquina virtual para as tabelas na sua conta de armazenamento. Se a quantidade de dados que são recolhidos excede a quota antes do fim de um período de transferência, são rejeitados dados mais antigos. Se está a perda de dados porque os dados excedem o tamanho de memória intermédia ou a quota geral, pode querer diminua o período de transferência.

**O fuso horário são os carimbos de data / hora na?**

Carimbos de data / hora é o fuso horário local do Centro de dados que aloja o serviço em nuvem. São utilizadas as seguintes colunas de carimbo de hora três nas tabelas de registo:

* **PreciseTimeStamp**: timestamp o ETW do evento. Ou seja, a hora é registado o evento do cliente.
* **TIMESTAMP**: O valor para **PreciseTimeStamp** arredondado para baixo até o limite da frequência de carregamento. Por exemplo, se a frequência de carregamento 5 minutos e o evento de 00:17:12 de tempo, TIMESTAMP é 00:15:00.
* **Timestamp**: O carimbo de hora em que a entidade foi criada na tabela do Azure.

**Como gerir os custos aquando da recolha de informações de diagnóstico?**

As predefinições (**nível de registo** definido como **erro**, e **transferência período** definido como **1 minuto**) foram concebidos para minimizar os custos. Aumentam os custos de computação quando recolher mais dados de diagnóstico ou se diminuir o período de transferência. Não recolha mais dados do que o que precisa e não se esqueça de desativar a recolha de dados quando já não precisar dele. Pode sempre ativá-la novamente, mesmo em tempo de execução, como descrito anteriormente neste artigo.

**Como recolher registos de pedido falhou a partir do IIS?**

Por predefinição, o IIS não recolher registos de pedido falhou. Pode configurar o IIS para recolher registos de pedido falhou ao editar o ficheiro Web. config para a função da web.

**Não consigo estou a obter informações de rastreio do RoleEntryPoint métodos como OnStart. O que é incorreto?**

Os métodos de **RoleEntryPoint** são denominados no contexto de WAIISHost.exe, não no IIS. As informações de configuração em Web. config que normalmente permite rastreio não se aplica. Para resolver este problema, adicione um ficheiro. config ao seu projeto de função da web e um nome ao ficheiro para corresponder a assemblagem de saída que contém o **RoleEntryPoint** código. No projeto de função da web de predefinição, o nome do ficheiro. config deve ser WAIISHost.exe.config. Adicione as seguintes linhas ao ficheiro:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

No **propriedades** janela, defina o **copiar para o diretório de saída** propriedade **Copiar sempre**.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o diagnóstico de registo no Azure, consulte o artigo [ative os diagnósticos no Cloud Services do Azure e máquinas virtuais](cloud-services/cloud-services-dotnet-diagnostics.md) e [ativar o registo de diagnóstico para Web Apps no App Service do Azure](app-service/web-sites-enable-diagnostic-log.md).

