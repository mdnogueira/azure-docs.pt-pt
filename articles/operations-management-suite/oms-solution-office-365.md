---
title: "Solução do Office 365 no Operations Management Suite (OMS) | Microsoft Docs"
description: "Este artigo fornece detalhes sobre a configuração e a utilização da solução do Office 365 no OMS.  Inclui uma descrição detalhada dos registos do Office 365 criados na análise de registos."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: 711071eaff7ab5e5199793663aa3cbb36a1e8d8a
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="office-365-solution-in-operations-management-suite-oms"></a>Solução do Office 365 no Operations Management Suite (OMS)

![Logótipo do Office 365](media/oms-solution-office-365/icon.png)

A solução do Office 365 para o Operations Management Suite (OMS) permite-lhe monitorizar o seu ambiente do Office 365 na análise de registos.  

- Monitorizar as atividades dos utilizadores em contas do Office 365 para analisar padrões de utilização, bem como para identificar tendências comportamentais. Por exemplo, pode extrair os cenários de utilização específicos, tais como ficheiros que são partilhados fora da sua organização ou sites do SharePoint mais populares.
- Monitorizar atividades de administrador para controlar as alterações de configuração ou operações de privilégio elevado.
- Detetar e investigue o comportamento de utilizador indesejável, que pode ser personalizado para as suas necessidades organizacionais.
- Demonstrar a auditoria e de conformidade. Por exemplo, pode monitorizar operações de acesso do ficheiro em ficheiros confidenciais que podem ajudá-lo com o processo de auditoria e de conformidade.
- Efetue a resolução de problemas operacionais utilizando a pesquisa do OMS por cima de dados de atividade do Office 365 da sua organização.

## <a name="prerequisites"></a>Pré-requisitos
É necessário o seguinte antes desta solução que está a ser instalado e configurado.

- Subscrição do Office 365 organizacional.
- Credenciais para uma conta de utilizador que seja um Administrador Global.
- Para receber dados de auditoria, deve [configurar a auditoria](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) na sua subscrição do Office 365.  Tenha em atenção que [auditoria da caixa de correio](https://technet.microsoft.com/library/dn879651.aspx) está configurado separadamente.  Ainda pode instalar a solução e recolher outros dados se não estiver configurada a auditoria.
 


## <a name="management-packs"></a>Pacotes de gestão
Esta solução não instala quaisquer pacotes de gestão em grupos de gestão ligado.
  

## <a name="configuration"></a>Configuração
Uma vez que [adicionar a solução Office 365 à sua subscrição](../log-analytics/log-analytics-add-solutions.md), tem de estabelecer a ligação à sua subscrição do Office 365.

1. Adicionar a solução de gestão de alertas a sua área de trabalho do OMS utilizando o processo descrito no [adicionar soluções](../log-analytics/log-analytics-add-solutions.md).
2. Aceda a **definições** no portal do OMS.
3. Em **origens ligadas**, selecione **do Office 365**.
4. Clique em **ligar o Office 365**.<br>![Connnect Office 365](media/oms-solution-office-365/configure.png)
5. Inicie sessão no Office 365 com uma conta que seja um Administrador Global para a sua subscrição. 
6. A subscrição será listada com as cargas de trabalho que a solução irá monitorizar.<br>![Connnect Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>Recolha de dados
### <a name="supported-agents"></a>Agentes suportados
A solução Office 365 não obter dados a partir de qualquer o [agentes OMS](../log-analytics/log-analytics-data-sources.md).  Obtém dados diretamente a partir do Office 365.

### <a name="collection-frequency"></a>Frequência da recolha
Office 365 envia um [webhook notificação](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) com dados detalhados para análise de registos sempre que é criado um registo.

## <a name="using-the-solution"></a>Utilizar a solução
Quando adiciona a solução Office 365 a sua área de trabalho do OMS, a **do Office 365** mosaico será adicionado ao seu dashboard do OMS. Este mosaico apresenta uma contagem e uma representação gráfica do número de computadores no seu ambiente e a respetiva conformidade de atualização.<br><br>
![Mosaico de resumo do Office 365](media/oms-solution-office-365/tile.png)  

Clique em de **do Office 365** mosaico para abrir o **do Office 365** dashboard.

![Dashboard do Office 365](media/oms-solution-office-365/dashboard.png)  

O dashboard inclui as colunas da tabela seguinte. Cada coluna apresenta os alertas de dez principais por contagem correspondentes aos critérios dessa coluna para o âmbito especificado e o intervalo de tempo. Pode executar uma pesquisa de registo que fornece a lista completa, clicando em ver todos os na parte inferior da coluna ou ao clicar no cabeçalho da coluna.

| Coluna | Descrição |
|:--|:--|
| Operações | Fornece informações sobre os utilizadores do Active Directory da sua subscrições do Office 365 monitorizados todos os. Também poderá ver o número de atividades que ocorrem ao longo do tempo.
| Troca | Mostra a repartição das atividades de Exchange Server, tais como permissões de caixa de correio adicionar ou caixa de correio de conjunto. |
| SharePoint | Mostra as principais atividades que os utilizadores executar em documentos do SharePoint. Quando lhe desagregar neste mosaico, a página de pesquisa mostra os detalhes destas atividades, como o documento de destino e a localização desta atividade. Por exemplo, para um evento de aceder ao ficheiro, será capaz de ver o documento que está a ser acedido, o nome da conta associada e o endereço IP. |
| Azure Active Directory | Inclui atividades de utilizador superior, como repor a palavra-passe de utilizador e de tentativas de início de sessão. Quando desagregar, será capaz de ver os detalhes destas atividades, como o estado de resultado. Esta ação é principalmente útil se pretender monitorizar atividades suspeitas no seu Azure Active Directory. |




## <a name="log-analytics-records"></a>Registos do Log Analytics

Todos os registos criados na área de trabalho de análise de registos pela solução do Office 365 tem um **tipo** de **OfficeActivity**.  O **OfficeWorkload** propriedade determina o serviço do Office 365, o registo refere-se para - Exchange, AzureActiveDirectory, SharePoint ou OneDrive.  O **RecordType** propriedade especifica o tipo de operação.  As propriedades irão variar para cada tipo de operação e são apresentadas nas tabelas abaixo.

### <a name="common-properties"></a>Propriedades comuns
As seguintes propriedades são comuns a todos os registos do Office 365.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo | *OfficeActivity* |
| ClientIP | O endereço IP do dispositivo que foi utilizado quando a atividade foi registada. O endereço IP é apresentado no formato de endereço de um IPv4 ou IPv6. |
| OfficeWorkload | Serviço do Office 365 que referencia o registo.<br><br>AzureActiveDirectory<br>Troca<br>SharePoint|
| Operação | O nome da atividade de utilizador ou administrador.  |
| OrganizationId | O GUID para o inquilino do Office 365 da sua organização. Este valor será sempre o mesmo para a sua organização, independentemente do serviço do Office 365 em que ocorre. |
| recordType | Tipo de operação efetuada. |
| ResultStatus | Indica se a ação (especificada na propriedade operação) foi concluída com êxito ou não. Os valores possíveis são bem sucedida, PartiallySucceded ou falha. Para a atividade de administrador do Exchange, o valor é o verdadeiro ou FALSO. |
| ID de utilizador | O UPN (nome Principal de utilizador) do utilizador que efetuou a ação que resultaram num registo que está a ser registado; Por exemplo, my_name@my_domain_name. Tenha em atenção que os registos para a atividade realizada por contas de sistema (tais como SHAREPOINT\system ou NTAUTHORITY\SYSTEM) também estão incluídos. | 
| UserKey | Um ID alternativo para o utilizador identificado na propriedade de ID de utilizador.  Por exemplo, esta propriedade é preenchida com o passport ID exclusivo (PUID) eventos realizadas por utilizadores no SharePoint, no OneDrive para empresas e no Exchange. Esta propriedade também pode especificar o mesmo valor como a propriedade de ID de utilizador para eventos que ocorrem noutros serviços e eventos realizados de contas do sistema|
| UserType | O tipo de utilizador que executar a operação.<br><br>Admin<br>Aplicação<br>DcAdmin<br>Normal<br>Reservado<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base do Azure Active Directory
As seguintes propriedades são comuns a todos os registos do Azure Active Directory.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| recordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | O tipo de evento do Azure AD. |
| ExtendedProperties | As propriedades expandidas do evento do Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Início de sessão de conta do Active Directory do Azure
Estes registos são criados quando um utilizador do Active Directory tenta iniciar sessão.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| recordType     | AzureActiveDirectoryAccountLogon |
| Aplicação | A aplicação que aciona o evento de início de sessão de conta, tais como Office 15. |
| Cliente | Detalhes sobre o cliente do dispositivo, o SO do dispositivo e o browser do dispositivo foi utilizado para do evento de início de sessão de conta. |
| LoginStatus | Esta propriedade é de OrgIdLogon.LoginStatus diretamente. O mapeamento de várias falhas de início de sessão interessantes foi ser feito através de algoritmos de alerta. |
| UserDomain | As informações de identidade do inquilino (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Estes registos são criados quando são efetuadas alteração ou adições de objetos do Active Directory do Azure.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| recordType     | AzureActiveDirectory |
| AADTarget | O utilizador que a ação (identificada pela propriedade operação) foi executada em. |
| Ator | O utilizador ou principal de serviço que efetuou a ação. |
| ActorContextId | O GUID da organização que o ator pertence. |
| ActorIpAddress | Endereço IP de ator em formato de endereço IPV4 ou IPV6. |
| InterSystemsId | O GUID que controlam as ações em componentes do serviço do Office 365. |
| IntraSystemId |   O GUID que é gerado pelo Azure Active Directory para controlar a ação. |
| SupportTicketId | O cliente suporta o ID de permissão para a ação "act-em-nome-de" situações. |
| TargetContextId | O GUID da organização que o utilizador de destino pertence. |


### <a name="data-center-security"></a>Segurança do Centro de dados
Estes registos são criados a partir dos dados de auditoria de segurança do Centro de dados.  

| Propriedade | Descrição |
|:--- |:--- |
| EffectiveOrganization | O nome do inquilino que foi direcionado para o elevação/cmdlet. |
| ElevationApprovedTime | O carimbo de quando foi aprovada a elevação. |
| ElevationApprover | O nome de um Gestor de Microsoft. |
| ElevationDuration | A duração para o qual a elevação estava ativa. |
| ElevationRequestId |  Um identificador exclusivo para o pedido de elevação. |
| ElevationRole | A função a elevação foi pedida para. |
| ElevationTime | A hora de início de elevação. |
| Start_Time | A hora de início da execução de cmdlet. |


### <a name="exchange-admin"></a>Administrador do Exchange
Estes registos são criados quando são efetuadas alterações à configuração do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| recordType     | ExchangeAdmin |
| ExternalAccess |  Especifica se o cmdlet foi executado por um utilizador na sua organização, pela equipa de centro de dados da Microsoft ou uma conta de serviço do Centro de dados, ou por um administrador delegado. O valor falso indica que o cmdlet foi executado por alguém na sua organização. O valor True indica que o cmdlet foi executado por técnico do Centro de dados, uma conta de serviço do Centro de dados ou um administrador delegado. |
| ModifiedObjectResolvedName |  Este é o nome amigável de utilizador do objeto que foi modificado pelo cmdlet. Isto é registado apenas se o cmdlet modifica o objeto. |
| OrganizationName | O nome do inquilino. |
| OriginatingServer | O nome do servidor a partir da qual foi executado o cmdlet. |
| Parâmetros | O nome e valor para todos os parâmetros que foram utilizados com o cmdlet que é identificado na propriedade de operações. |


### <a name="exchange-mailbox"></a>Caixa de correio do Exchange
Estes registos são criados quando são efetuadas alterações ou adições de caixas de correio do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| recordType     | ExchangeItem |
| ClientInfoString | Informações sobre o cliente de e-mail que foi utilizado para efetuar a operação, por exemplo, uma versão do browser, versão do Outlook e informações de dispositivos móveis. |
| Client_IPAddress | O endereço IP do dispositivo que foi utilizado quando a operação foi iniciada. O endereço IP é apresentado no formato de endereço de um IPv4 ou IPv6. |
| ClientMachineName | O nome do computador que aloja o cliente do Outlook. |
| ClientProcessName | O cliente de e-mail que foi utilizado para aceder a caixa de correio. |
| ClientVersion | A versão do cliente de e-mail. |
| InternalLogonType | Reservado para utilização interna. |
| Logon_Type | Indica o tipo de utilizador que aceder a caixa de correio e efetuar a operação que foi registada. |
| LogonUserDisplayName |    O nome amigável de utilizador do utilizador que executou a operação. |
| LogonUserSid | O SID do utilizador que executou a operação. |
| MailboxGuid | O GUID do Exchange da caixa de correio que foi acedido. |
| MailboxOwnerMasterAccountSid | Conta mestre SID da conta de proprietário a caixa de correio. |
| MailboxOwnerSid | O SID do proprietário da caixa de correio. |
| MailboxOwnerUPN | O endereço de e-mail da pessoa que detém a caixa de correio que foi acedida. |


### <a name="exchange-mailbox-audit"></a>Auditoria de caixa de correio do Exchange
Estes registos são criados quando é criada uma entrada de auditoria da caixa de correio.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| recordType     | ExchangeItem |
| Item | Representa o item no qual a operação foi efetuada | 
| SendAsUserMailboxGuid | O GUID do Exchange da caixa de correio que foi acedido para enviar correio eletrónico como. |
| SendAsUserSmtp | Endereço SMTP do utilizador que está a ser representado. |
| SendonBehalfOfUserMailboxGuid | O GUID do Exchange da caixa de correio que foi acedido para enviar correio eletrónico no nome de. |
| SendOnBehalfOfUserSmtp | Endereço SMTP do utilizador em cujo nome é enviada a mensagem de e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Grupo de auditoria de caixa de correio do Exchange
Estes registos são criados quando são efetuadas alterações ou adições de grupos do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informações sobre cada item no grupo. |
| CrossMailboxOperations | Indica se a operação envolvidos mais do que uma caixa de correio. |
| DestMailboxId | Defina apenas se o parâmetro de CrossMailboxOperations é verdadeiro. Especifica a caixa de correio do destino GUID. |
| DestMailboxOwnerMasterAccountSid | Defina apenas se o parâmetro de CrossMailboxOperations é verdadeiro. Especifica o SID para a conta do principal SID do proprietário da caixa de correio de destino. |
| DestMailboxOwnerSid | Defina apenas se o parâmetro de CrossMailboxOperations é verdadeiro. Especifica o SID da caixa de correio de destino. |
| DestMailboxOwnerUPN | Defina apenas se o parâmetro de CrossMailboxOperations é verdadeiro. Especifica o UPN do proprietário da caixa de correio de destino. |
| DestFolder | A pasta de destino, para operações como Move. |
| Pasta | A pasta onde está localizado a um grupo de itens. |
| Pastas |     Informações sobre as pastas de origem envolvidas numa operação; Por exemplo, se pastas são selecionadas e, em seguida, eliminadas. |


### <a name="sharepoint-base"></a>Base do SharePoint
Estas propriedades são comuns a todos os registos do SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica que um evento ocorreu no SharePoint. Os valores possíveis são SharePoint ou ObjectModel. |
| ItemType | O tipo de objeto que foi acedido ou modificado. Consulte a tabela de ItemType para obter detalhes sobre os tipos de objetos. |
| MachineDomainInfo | Informações sobre as operações de sincronização do dispositivo. Esta informação é comunicada apenas se estiver presente no pedido. |
| MachineId |   Informações sobre as operações de sincronização do dispositivo. Esta informação é comunicada apenas se estiver presente no pedido. |
| Site_ | O GUID do site onde está localizada o ficheiro ou pasta acedidos pelo utilizador. |
| Source_Name | A entidade que acionou a operação auditada. Os valores possíveis são SharePoint ou ObjectModel. |
| UserAgent | Informações sobre o cliente ou o browser do utilizador. Estas informações são fornecidas pelo cliente ou browser. |


### <a name="sharepoint-schema"></a>Esquema do SharePoint
Estes registos são criados quando são efetuadas alterações de configuração para o SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Cadeia opcional de eventos personalizados. |
| Event_Data |  Payload opcional de eventos personalizados. |
| ModifiedProperties | A propriedade está incluída para eventos de administrador, como adicionar um utilizador como um membro de um site ou um grupo de administração de coleção de sites. A propriedade inclui o nome da propriedade que foi modificada (por exemplo, o grupo de administrador do Site), o novo valor da propriedade modificado (esse utilizador que foi adicionado como um administrador do site) e o valor anterior do objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operações de ficheiros do SharePoint
Estes registos são criados em resposta às operações de ficheiro no SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A extensão de ficheiro de um ficheiro que é copiado ou movido. Esta propriedade é apresentada apenas para eventos FileCopied e FileMoved. |
| DestinationFileName | O nome do ficheiro que é copiado ou movido. Esta propriedade é apresentada apenas para eventos FileCopied e FileMoved. |
| DestinationRelativeUrl | O URL da pasta de destino onde um ficheiro é copiado ou movido. A combinação de valores para parâmetros SiteURL, DestinationRelativeURL e o DestinationFileName é igual ao valor da propriedade ObjectID, que é o nome de caminho completo para o ficheiro que foi copiado. Esta propriedade é apresentada apenas para eventos FileCopied e FileMoved. |
| SharingType | O tipo de partilha permissões atribuídas ao utilizador que o recurso foi partilhado. Este utilizador é identificado pelo parâmetro UserSharedWith. |
| Site_Url | O URL do site onde está localizada o ficheiro ou pasta acedidos pelo utilizador. |
| SourceFileExtension | A extensão de ficheiro do ficheiro que foi acedido pelo utilizador. Esta propriedade está em branco se o objeto que foi acedido é uma pasta. |
| SourceFileName |  O nome do ficheiro ou pasta acedidos pelo utilizador. |
| SourceRelativeUrl | O URL da pasta que contém o ficheiro acedido pelo utilizador. A combinação de valores para os parâmetros SiteURL, SourceRelativeURL e SourceFileName é igual ao valor da propriedade ObjectID, que é o nome de caminho completo para o ficheiro acedido pelo utilizador. |
| UserSharedWith |  O utilizador que um recurso foi partilhado. |




## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos de atualizações que esta solução recolhe.

| Consulta | Descrição |
| --- | --- |
|Contagem de todas as operações na sua subscrição do Office 365 |Tipo = OfficeActivity &#124; medida existente pela operação |
|Utilização de sites do SharePoint|Tipo = OfficeActivity OfficeWorkload = sharepoint &#124; medir existente como contagem por SiteUrl &#124; Ordenar asc contagem|
|Operações de acesso de ficheiros por tipo de utilizador|Tipo = OfficeActivity OfficeWorkload = sharepoint operação = FileAccessed &#124; medida existente pelo UserType|
|Procurar com uma palavra-chave específica|Tipo = OfficeActivity OfficeWorkload = azureactivedirectory "MyTest"|
|Monitor ações externas no Exchange|Tipo = OfficeActivity OfficeWorkload = exchange ExternalAccess = true|



## <a name="troubleshooting"></a>Resolução de problemas

Se a sua solução do Office 365 não está a recolher dados conforme esperado, verifique o respetivo estado no portal do OMS em **definições** -> **origens ligadas** -> **do Office 365**. A tabela seguinte descreve cada Estado.

| Estado | Descrição |
|:--|:--|
| Ativa | A subscrição do Office 365 está ativa e a carga de trabalho é ligada com êxito à sua área de trabalho do OMS. |
| Pendente | A subscrição do Office 365 estiver ativa, mas a carga de trabalho ainda não está ligada à sua área de trabalho do OMS com êxito. Na primeira vez que o se ligar a subscrição do Office 365, todas as cargas de trabalho estará neste estado até ligados com êxito. Aguarde 24 horas para todas as cargas de trabalho mudar para o Active Directory. |
| Inativos | A subscrição do Office 365 está num estado inativo. Verifique a sua página de administração do Office 365 para obter mais detalhes. Depois de ativar a subscrição do Office 365, desassociá-lo a partir da sua área de trabalho do OMS e ligá-lo novamente para começar a receber dados. |



## <a name="next-steps"></a>Passos seguintes
* Utilizar as Pesquisas de Registos no [Log Analytics](../log-analytics/log-analytics-log-searches.md) para ver dados de atualizações detalhados.
* [Criar os seus próprios dashboards](../log-analytics/log-analytics-dashboards.md) para apresentar consultas de pesquisa do Office 365 favoritas.
* [Criar alertas](../log-analytics/log-analytics-alerts.md) proativamente seja notificado das atividades importantes do Office 365.  
