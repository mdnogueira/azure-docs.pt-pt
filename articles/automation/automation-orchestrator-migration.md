---
title: "Migrar do Orchestrator para a automatização do Azure | Microsoft Docs"
description: "Descreve como migrar pacotes de runbooks e a integração do System Center Orchestrator para automatização do Azure."
services: automation
documentationcenter: 
author: bwren
manager: stevenka
editor: tysonn
ms.assetid: 1a7da58c-7a98-49b5-9d9d-001a9f6e631a
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: bwren
ms.openlocfilehash: 457888b4d38875b912ad87d44e96ab727e3ee3ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrar do Orchestrator para a automatização do Azure (Beta)
Os Runbooks do [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) baseiam-se em atividades pacotes de integração que são escritas especificamente para o Orchestrator, enquanto os runbooks na automatização do Azure são baseados no Windows PowerShell.  [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks) na automatização do Azure tem um aspeto semelhante para os runbooks do Orchestrator com as respectivas actividades que representa os cmdlets do PowerShell, os runbooks subordinados e recursos.

O [Toolkit de migração do System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclui ferramentas para ajudá-lo a conversão de runbooks do Orchestrator para automatização do Azure.  Para além de converter os runbooks si próprios, tem de converter os pacotes de integração com as atividades que os runbooks utilizam módulos de integração com os cmdlets do Windows PowerShell.  

Segue-se o processo básico para converter os runbooks do Orchestrator para automatização do Azure.  Cada um destes passos está descrita em detalhe nas secções abaixo.

1. Transferir o [Toolkit de migração do System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) que contém as ferramentas e os módulos abordados neste artigo.
2. Importar [atividades padrão módulo](#standard-activities-module) para a automatização do Azure.  Isto inclui convertidas versões de atividades padrão do Orchestrator que podem ser utilizadas pelos runbooks convertidos.
3. Importar [módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) na automatização do Azure para os pacotes de integração utilizados pelos seus runbooks que acedem ao System Center.
4. Converter personalizada e pacotes de integração de terceiros utilizando o [conversor de pacote de integração](#integration-pack-converter) e importar para a automatização do Azure.
5. Converter o Orchestrator runbooks utilizando o [conversor de Runbook](#runbook-converter) e instalar na automatização do Azure.
6. Crie manualmente necessários ativos do Orchestrator na automatização do Azure, uma vez que o conversor de Runbook não converter estes recursos.
7. Configurar um [Runbook Worker híbrido](#hybrid-runbook-worker) no seu centro de dados local para executar runbooks convertidos que irão aceder aos recursos locais.

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) armazena e executa runbooks no seu centro de dados locais como o Orchestrator e utiliza os mesmo módulos de integração como automatização do Azure. O [conversor de Runbook](#runbook-converter) converte os runbooks do Orchestrator para runbooks gráficos apesar que não são suportadas no SMA.  É possível instalar o [módulo de atividades padrão](#standard-activities-module) e [módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) para o SMA, mas tem manualmente [reescrever os runbooks](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida
Os Runbooks do Orchestrator são armazenados num servidor de base de dados e executar nos servidores de runbook, ambos no seu centro de dados local.  Os Runbooks na automatização do Azure são armazenados na nuvem do Azure e pode ser executado no seu centro de dados local utilizando um [Runbook Worker híbrido](automation-hybrid-runbook-worker.md).  Trata-se como normalmente, irá executar runbooks convertidos a partir do Orchestrator, uma vez que estes são concebidos para serem executados em servidores locais.

## <a name="integration-pack-converter"></a>Conversor de pacote de integração
O conversor de pacote de integração converte os pacotes de integração que foram criados utilizando o [Orchestrator Integration Toolkit (OIT)](http://technet.microsoft.com/library/hh855853.aspx) aos módulos de integração com base no Windows PowerShell que podem ser importados para a automatização do Azure ou o Service Management Automation.  

Quando executa o conversor de pacote de integração, é-lhe apresentado um assistente que irá permitir-lhe selecionar um ficheiro de pacote (oip) de integração.  Em seguida, o assistente apresenta uma lista de atividades incluídas nesse pacote de integração e permite-lhe selecionar qual serão migradas.  Quando concluir o assistente, cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração original.

### <a name="parameters"></a>Parâmetros
As propriedades de uma atividade no pacote de integração são convertidas em parâmetros do cmdlet correspondente no módulo de integração.  Cmdlets do Windows PowerShell têm um conjunto de [parâmetros comuns de](http://technet.microsoft.com/library/hh847884.aspx) que podem ser utilizados com todos os cmdlets.  Por exemplo,-Verbose parâmetro faz com que um cmdlet para obter informações detalhadas sobre a conclusão da operação de saída.  Não existem cmdlet pode ter um parâmetro com o mesmo nome que um parâmetro comum.  Se uma atividade tem uma propriedade com o mesmo nome que um parâmetro comum, o assistente irá pedir-lhe fornecer outro nome para o parâmetro.

### <a name="monitor-activities"></a>Atividades de monitorização
Monitorizar os runbooks no início do Orchestrator com um [monitorizar a atividade](http://technet.microsoft.com/library/hh403827.aspx) e executar continuamente a aguardar para ser invocado por um determinado evento.  Automatização do Azure não suporta a runbooks do monitor, pelo que quaisquer atividades de monitorização no pacote de integração não serão convertidas.  Em vez disso, é criado um cmdlet de marcador de posição no módulo de integração para a atividade de monitorização.  Este cmdlet não tem nenhuma funcionalidade, mas permite que qualquer runbook convertida que utiliza-o para ser instalada.  Este runbook não será capaz de executar na automatização do Azure, mas pode ser instalado para que pode modificá-la.

### <a name="integration-packs-that-cannot-be-converted"></a>Pacotes de integração que não não possível converter
Não não possível converter os pacotes de integração que não foram criados com OIT com o conversor de pacote de integração. Existem também alguns pacotes de integração fornecidas pela Microsoft que atualmente não é possível converter com esta ferramenta.  Converter versões destes pacotes de integração tem sido [fornecida para transferência](#system-center-orchestrator-integration-modules) para que possa ser instaladas na automatização do Azure ou o Service Management Automation.

## <a name="standard-activities-module"></a>Módulo de atividades padrão
Orchestrator inclui um conjunto de [atividades padrão](http://technet.microsoft.com/library/hh403832.aspx) que não estão incluídas num pacote de integração, mas são utilizadas pelo número de runbooks.  O módulo de atividades padrão é um módulo de integração que inclui um cmdlet equivalente para cada um destas atividades.  Tem de instalar este módulo de integração na automatização do Azure antes de importar todos os runbooks convertidos que utilize uma atividade padrão.

Além de suportar a runbooks convertidos, os cmdlets no módulo de atividades padrão pode ser utilizados por outra familiarizado com o Orchestrator para criar novos runbooks na automatização do Azure.  Enquanto a funcionalidade de todas as atividades padrão pode ser efetuada com os cmdlets, eles podem operar diferente.  Os cmdlets no módulo de atividades padrão convertida irá funcionar da mesma forma como as atividades correspondentes e utilizar os mesmos parâmetros.  Isto pode ajudar o autor de runbook do Orchestrator existente no respetivo transição para runbooks de automatização do Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Módulos de integração do System Center Orchestrator
A Microsoft fornece [pacotes de integração](http://technet.microsoft.com/library/hh295851.aspx) para a criação de runbooks para automatizar componentes do System Center e outros produtos.  Alguns destes pacotes de integração baseiam-se atualmente em OIT, mas atualmente não não possível converter para módulos de integração devido a problemas conhecidos.  [Módulos de integração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) inclui convertidas versões destes pacotes de integração que podem ser importados para a automatização do Azure e o Service Management Automation.  

Pela versão RTM desta ferramenta, versões atualizadas dos pacotes de integração com base no OIT que possam ser convertido com o conversor de pacote de integração serão publicadas.  Também irá ser fornecida orientação para ajudá-lo a conversão de runbooks utilizando atividades dos pacotes de integração não baseados num OIT.

## <a name="runbook-converter"></a>Conversor de Runbook
O conversor de Runbook converte os runbooks do Orchestrator para [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) que podem ser importados para a automatização do Azure.  

O conversor de Runbook é implementado como um módulo do PowerShell com um cmdlet chamado **ConvertFrom SCORunbook** que realiza a conversão.  Quando instalar a ferramenta, criará um atalho para uma sessão do PowerShell que carrega o cmdlet.   

Segue-se o processo básico para converter um runbook do Orchestrator e importe-o para a automatização do Azure.  As secções seguintes fornecem mais detalhes sobre como utilizar a ferramenta e trabalhar com runbooks convertidos.

1. Exporte um ou mais runbooks do Orchestrator.
2. Obter módulos de integração para todas as atividades no runbook.
3. Converta os runbooks do Orchestrator no ficheiro exportado.
4. Rever as informações nos registos para validar a conversão e para determinar as tarefas manuais necessárias.
5. Importe runbooks convertidos para a automatização do Azure.
6. Crie os recursos necessários na automatização do Azure.
7. Edite o runbook na automatização do Azure para modificar quaisquer atividades necessárias.

### <a name="using-runbook-converter"></a>Utilizar o conversor de Runbook
A sintaxe para **ConvertFrom SCORunbook** é o seguinte:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>

* RunbookPath - caminho do ficheiro de exportação que contém os runbooks para converter.
* Módulo - lista delimitada por vírgulas dos módulos de integração com atividades nos runbooks.
* OutputFolder - converter o caminho para a pasta para criar runbooks gráficos.

O comando de exemplo seguintes converte os runbooks num ficheiro de exportação chamado **MyRunbooks.ois_export**.  Estes runbooks utilizar os pacotes de integração do Active Directory e do Data Protection Manager.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"


### <a name="log-files"></a>Ficheiros de registo
O conversor de Runbook irá criar os seguintes ficheiros de registo na mesma localização que o runbook convertido.  Se os ficheiros já existirem, em seguida, estas serão substituídas com informações a partir do último conversão.

| Ficheiro | Conteúdos |
|:--- |:--- |
| Runbook conversor - Progress.log |Passos detalhados de conversão, incluindo informações para cada atividade converter com êxito e de aviso para cada atividade, não convertido. |
| Runbook conversor - Summary.log |Resumo da última conversão, incluindo quaisquer avisos e siga a cópia de segurança tarefas que precisa para efetuar, como criar uma variável é necessária para o runbook convertido. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportar os runbooks do Orchestrator
O conversor de Runbook funciona com um ficheiro de exportação do Orchestrator que contém um ou mais runbooks.  Será criado um runbook de automatização do Azure correspondente para cada runbook do Orchestrator no ficheiro de exportação.  

Para exportar um runbook do Orchestrator, faça duplo clique o nome do runbook no Runbook Designer e selecione **exportar**.  Para exportar todos os runbooks numa pasta, faça duplo clique o nome da pasta e selecione **exportar**.

### <a name="runbook-activities"></a>Atividades do Runbook
O conversor de Runbook converte cada atividade no runbook do Orchestrator para uma atividade correspondente na automatização do Azure.  Para essas atividades que não não possível converter, é criada uma atividade de marcador de posição no runbook com o texto de aviso.  Depois de importar o runbook convertido para a automatização do Azure, tem de substituir quaisquer estas atividades com válido atividades que executam a funcionalidade necessária.

As atividades do Orchestrator no [módulo de atividades padrão](#standard-activities-module) será convertido.  Existem algumas atividades padrão do Orchestrator que não são neste módulo apesar e não são convertidas.  Por exemplo, **enviar evento de plataforma** não tem nenhum equivalente de automatização do Azure, uma vez que o evento é específico do Orchestrator.

[Monitorizar atividades](https://technet.microsoft.com/library/hh403827.aspx) não são convertidos porque não existe nenhum equivalente aos mesmos na automatização do Azure.  A exceção são monitorizar atividades [converter pacotes de integração](#integration-pack-converter) que serão convertidos para a atividade de marcador de posição.

Qualquer atividade de um [converter pacote de integração](#integration-pack-converter) será convertido se fornecer o caminho para o módulo de integração com o **módulos** parâmetro.  Para pacotes de integração do System Center, pode utilizar o [módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Recursos do Orchestrator
O conversor de Runbook só converte runbooks, não outros recursos do Orchestrator como contadores, variáveis ou ligações.  Contadores não são suportados na automatização do Azure.  São suportadas ligações e variáveis, mas deve criá-los manualmente.  Os ficheiros de registo irão informar o se o runbook necessitar esses recursos e especifique os recursos correspondentes que tem de criar na automatização do Azure para o runbook convertido funcionar corretamente.

Por exemplo, um runbook pode utilizar uma variável para preencher um valor específico numa atividade.  O runbook convertido irá converter a atividade e especifique um recurso de variável na automatização do Azure com o mesmo nome que a variável do Orchestrator.  Isto será indicado no **Runbook conversor - Summary.log** ficheiro criado após a conversão.  Terá de criar manualmente este recurso de variável na automatização do Azure antes de utilizar o runbook.

### <a name="input-parameters"></a>Parâmetros de entrada
Os Runbooks do Orchestrator aceitar parâmetros de entrada com o **inicializar dados** atividade.  Se o runbook que está a ser convertido incluir esta atividade, em seguida, um [parâmetro de entrada](automation-graphical-authoring-intro.md#runbook-input-and-output) na automatização do Azure é criado para cada parâmetro na atividade do runbook.  A [controlo de Script de fluxo de trabalho](automation-graphical-authoring-intro.md#activities) é criada a atividade no runbook convertido que obtém e devolve a cada um dos parâmetros.  Consulte todas as atividades no runbook que utilizam um parâmetro de entrada à saída desta atividade.

O motivo que é utilizada esta estratégia é melhor espelhar a funcionalidade do runbook do Orchestrator.  As atividades no novo runbooks gráficos devem consultar diretamente a utilizar uma origem de dados de entrada do Runbook de parâmetros de entrada.

### <a name="invoke-runbook-activity"></a>Atividade invocar Runbook
Os Runbooks do Orchestrator iniciar outros runbooks com o **invocar Runbook** atividade. Se o runbook que está a ser convertido incluir esta atividade e o **aguardar conclusão** opção estiver definida, em seguida, uma atividade de runbook é criada para o mesmo no runbook convertido.  Se o **aguardar conclusão** opção não está definida, em seguida, uma Script de fluxo de trabalho é criada a atividade que utiliza **início AzureAutomationRunbook** para iniciar o runbook.  Depois de importar o runbook convertido para a automatização do Azure, tem de modificar esta atividade com as informações especificadas na atividade.

## <a name="related-articles"></a>Artigos relacionados
* [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Runbook Worker híbrido](automation-hybrid-runbook-worker.md)
* [Atividades padrão do Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
* [Toolkit de migração do transferências do System Center Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
