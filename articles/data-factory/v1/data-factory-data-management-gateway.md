---
title: "Gateway de gestão de dados do Data Factory | Microsoft Docs"
description: Configure um gateway de dados para mover dados entre no local e na nuvem. Utilize o Data Management Gateway no Azure Data Factory para mover os dados.
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: c22b8c07e6fb27af663c177ad4045615ab209ee1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="data-management-gateway"></a>Data Management Gateway
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [autoalojado integração tempo de execução na versão 2](../create-self-hosted-integration-runtime.md). 

O Data management gateway é um agente de cliente que tem de instalar no seu ambiente no local para copiar dados entre os arquivos de dados de nuvem e no local. Os dados no local arquivos suportados pela fábrica de dados estão listados no [origens de dados suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) secção.

Este artigo complementa as instruções de [mover dados entre no local e na nuvem arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) artigo. As instruções, vai criar um pipeline que utiliza o gateway para mover dados de uma base de dados do SQL Server no local para um blob do Azure. Este artigo fornece informações detalhadas de aprofundada sobre o data management gateway. 

Pode ampliar um data management gateway ao associar várias máquinas no local com o gateway. Pode dimensionar até ao aumentar o número de tarefas de movimento de dados que podem ser executados em simultâneo num nó. Esta funcionalidade também está disponível para um gateway lógico com um único nó. Consulte [dimensionamento o data management gateway no Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artigo para obter detalhes.

> [!NOTE]
> Atualmente, o gateway suporta apenas a atividade de cópia e a atividade de procedimento armazenado no Data Factory. Não é possível utilizar o gateway de uma atividade personalizada para aceder às origens de dados no local.      

## <a name="overview"></a>Descrição geral
### <a name="capabilities-of-data-management-gateway"></a>Capacidades do data management gateway
O Data management gateway fornece as seguintes capacidades:

* Modelo origens de dados no local e na nuvem origens de dados dentro da mesma fábrica de dados e mover dados.
* Tem um painel único de transparência para monitorização e gestão com visibilidade para o estado do gateway da página de fábrica de dados.
* Gerir o acesso a origens de dados no local em segurança.
  * Não existem alterações necessárias para a firewall da empresa. Gateway só faz baseado em HTTP de ligações de saída para abrir a internet.
  * Encripta as credenciais para os arquivos de dados no local com o certificado.
* Mover dados de forma eficiente – os dados são transferidos em paralelo, problemas de rede resiliente para intermitente com automática repetir lógica.

### <a name="command-flow-and-data-flow"></a>Fluxo de comando e o fluxo de dados
Quando utiliza uma atividade de cópia para copiar dados no local e na nuvem, a atividade utiliza um gateway para transferir dados de origem de dados no local para se nuvem e vice-versa.

Eis o fluxo de dados de alto nível para e resumo dos passos para a cópia com o gateway de dados: ![fluxo de dados utilizando o gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Programador de dados cria um gateway para um Azure Data Factory com o [portal do Azure](https://portal.azure.com) ou [Cmdlet do PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2. Programador de dados cria um serviço ligado para um arquivo de dados no local, especificando o gateway. Como parte da configuração de serviço ligado, o Programador de dados utiliza a aplicação de credenciais de definição para especificar tipos de autenticação e credenciais.  A caixa de diálogo de aplicação de credenciais de definição comunica com o arquivo de dados para testar a ligação e o gateway para guardar as credenciais.
3. Gateway encripta as credenciais com o certificado associado ao gateway (fornecido pelo programador de dados), antes de guardar as credenciais na nuvem.
4. Serviço de fábrica de dados comunica com o gateway para agendamento e a gestão de tarefas através de um canal de controlo que utiliza uma fila de barramento de serviço do Azure partilhado. Quando uma tarefa de atividade de cópia tem de ser arrancou, Data Factory coloca em fila de pedido juntamente com informações de credencial. Gateway arranca, a tarefa depois da fila de consulta.
5. O gateway desencripta as credenciais com o mesmo certificado e, em seguida, estabelece ligação ao arquivo de dados no local com o tipo de autenticação adequado e as credenciais.
6. O gateway copia dados a partir de um arquivo local para um armazenamento na nuvem, ou vice versa dependendo de como a atividade de cópia é configurada no pipeline de dados. Para este passo, o gateway comunica diretamente com os serviços de armazenamento baseado em nuvem, como o armazenamento de Blobs do Azure através de um canal seguro do (HTTPS).

### <a name="considerations-for-using-gateway"></a>Considerações sobre a utilização do gateway
* Uma única instância do data management gateway pode ser utilizada para várias origens de dados no local. No entanto, **uma única instância de gateway está associada a apenas um Azure data factory** e não pode ser partilhado com outro data factory.
* Pode ter **apenas uma instância do data management gateway** instaladas num único computador. Suponhamos, tiver dois fábricas de dados que precisem de aceder a origens de dados no local, terá de instalar gateways em dois computadores no local. Por outras palavras, um gateway está associado a uma fábrica de dados específico
* O **gateway não precisa de estar no mesmo computador como origem de dados**. No entanto, a existência de gateway próximo à origem de dados reduz o tempo para o gateway ligar à origem de dados. Recomendamos que instale o gateway num computador que é diferente da que aloja a origem de dados no local. Quando a origem de dados e de gateway estão em computadores diferentes, o gateway não competem para ter recursos com a origem de dados.
* Pode ter **vários gateways em computadores diferentes, ligar à mesma origem de dados no local**. Por exemplo, pode ter dois gateways que serve dois fábricas de dados mas a mesma origem de dados no local é registada em ambas as fábricas de dados.
* Se já tiver um gateway instalado no seu servir de computador um **Power BI** cenário, instale um **gateway separada para o Azure Data Factory** noutra máquina.
* Gateway tem de ser utilizado, mesmo quando utilize **ExpressRoute**.
* Tratar a origem de dados como uma origem de dados no local (que é protegido por uma firewall), mesmo quando utilize **ExpressRoute**. Utilize o gateway para estabelecer conectividade entre o serviço e a origem de dados.
* Tem **utilizar o gateway** , mesmo que esteja o arquivo de dados na nuvem num **VM do IaaS do Azure**.

## <a name="installation"></a>Instalação
### <a name="prerequisites"></a>Pré-requisitos
* Suportado **sistema operativo** versões são o Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Instalação do data management gateway num controlador de domínio não é atualmente suportada.
* .NET framework 4.5.1 ou superior, é necessário. Se estiver a instalar o gateway num computador Windows 7, instale o .NET Framework 4.5 ou posterior. Consulte [requisitos de sistema do .NET Framework](https://msdn.microsoft.com/library/8z6watww.aspx) para obter mais detalhes.
* O recomendada **configuração** para o gateway máquina é, pelo menos, 2 GHz, 4 núcleos, 8 GB de RAM e o disco de 80 GB.
* Se o computador anfitrião hibernates, o gateway não responde a pedidos de dados. Por conseguinte, configurar apropriadas **esquema de energia** no computador antes de instalar o gateway. Se o computador está configurado para hibernação, a instalação do gateway pede-lhe uma mensagem.
* Tem de ser um administrador no computador para instalar e configurar o data management gateway com êxito. Pode adicionar utilizadores adicionais para o **o data management gateway utilizadores** grupo do Windows local. Os membros deste grupo são capazes de utilizar o **Gestor de configuração do Data Management Gateway** ferramenta para configurar o gateway.

Como ocorrem execuções de atividade de cópia numa frequência específica, a utilização de recursos (CPU, memória) na máquina segue também o mesmo padrão das horas de ponta e tempos de inatividade. Utilização de recursos também depende descontos elevados a quantidade de dados a ser movidos. Quando várias tarefas de cópia estão em curso, pode ver a utilização de recursos, aceda a cópia de segurança durante períodos máximos.

### <a name="installation-options"></a>Opções de instalação
O Data management gateway pode ser instalado das seguintes formas:

* Ao transferir um pacote de configuração MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).  O MSI também pode ser utilizado para atualizar o gateway de gestão de dados existente para a versão mais recente, com todas as definições preservadas.
* Ao clicar em **transferir e instalar o gateway de dados** ligação configuração MANUAL ou **instalar diretamente neste computador** configuração EXPRESS. Consulte [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como utilizar a configuração rápida. O passo manual leva-o para o Centro de transferências.  As instruções para transferir e instalar o gateway do Centro de transferências estão na secção seguinte.

### <a name="installation-best-practices"></a>Melhores práticas de instalação:
1. Configure o esquema de energia no computador anfitrião para o gateway para que a máquina não hibernação. Se o computador anfitrião hibernates, o gateway não responde a pedidos de dados.
2. Fazer uma cópia de segurança do certificado associado ao gateway.

### <a name="install-the-gateway-from-download-center"></a>Instalar o gateway do Centro de transferências
1. Navegue para [página de transferência do Microsoft Data Management Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Clique em **transferir**, selecione a versão adequada (**32-bit** vs. **64-bit**) e clique em **seguinte**.
3. Execute o **MSI** diretamente ou guarde-o para o disco rígido e a execução.
4. No **boas-vindas** página, selecione um **idioma** clique **seguinte**.
5. **Aceitar** o contrato de licença de utilizador final e clique em **seguinte**.
6. Selecione **pasta** para instalar o gateway e clique em **seguinte**.
7. No **pronto para instalar** página, clique em **instalar**.
8. Clique em **concluir** para concluir a instalação.
9. Obter a chave do portal do Azure. Consulte a secção seguinte para obter instruções passo a passo.
10. No **registar gateway** página do **Gestor de configuração do Data Management Gateway** em execução no seu computador, efetue os seguintes passos:
    1. Cole a chave de texto.
    2. Opcionalmente, clique em **chave do gateway mostrar** para ver o texto da chave.
    3. Clique em **registar**.

### <a name="register-gateway-using-key"></a>Registar utilizando a chave de gateway
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Se já não criou um gateway lógico no portal
Para criar um gateway no portal e obter a chave a partir de **configurar** página, siga passos de instruções de [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo.    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Se já tiver criado o gateway lógico no portal
1. No portal do Azure, navegue para o **Data Factory** página e clique em **serviços ligados** mosaico.

    ![Page da fábrica de dados](media/data-factory-data-management-gateway/data-factory-blade.png)
2. No **serviços ligados** página, selecione lógico **gateway** que criou no portal.

    ![gateway lógico](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. No **Data Gateway** página, clique em **transferir e instalar o gateway de dados**.

    ![Transferir a ligação no portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. No **configurar** página, clique em **recrie chave**. Clique em Sim numa mensagem de aviso depois de ler este cuidadosamente.

    ![Recriar chave](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Clique no botão de cópia junto a chave. A chave é copiada para a área de transferência.

    ![Chave de cópia](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Os ícones do tabuleiro de sistema / notificações
A imagem seguinte mostra algumas dos ícones do tabuleiro que vê.

![ícones do tabuleiro de sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Se mova o cursor sobre a mensagem de notificação/ícone de tabuleiro de sistema, consulte os detalhes sobre o estado da operação de atualização de gateway/numa janela de pop-up.

### <a name="ports-and-firewall"></a>Portas e firewall
Existem duas firewalls precisa de considerar: **firewall empresarial** em execução no router central da organização, e **firewall do Windows** configurado como um daemon no computador local onde o gateway é instalado.  

![firewalls](./media/data-factory-data-management-gateway/firewalls2.png)

Ao nível da firewall da empresa, tem de configurar as portas de saída e domínios seguintes:

| Nomes de domínio | Portas | Descrição |
| --- | --- | --- |
| *. servicebus.windows.net |443, 80 |Utilizado para comunicação com o back-end do serviço de movimento de dados |
| *. core.windows.net |443 |Utilizado para cópia de teste utilizando o Blob do Azure (se configurada)|
| *. frontend.clouddatahub.net |443 |Utilizado para comunicação com o back-end do serviço de movimento de dados |
| *. servicebus.windows.net |9350-9354, 5671 |Reencaminhamento de barramento de serviço opcional através de TCP utilizada pelo Assistente de cópia |


Ao nível de firewall do Windows, estas portas de saída normalmente estão ativadas. Se não, pode configurar as portas e domínios em conformidade na máquina do gateway.

> [!NOTE]
> 1. Com base na sua origem / sinks, poderá ter de domínios adicionais da lista branca e portas de saída na firewall empresarial/Windows.
> 2. Para algumas bases de dados de nuvem (por exemplo: [SQL Database do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [do Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), etc.), poderá ter de endereço IP da lista branca de computador do Gateway na respetiva configuração da firewall.
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Copiar dados de um arquivo de dados de origem para um arquivo de dados dependente
Certifique-se de que as regras de firewall estão ativadas corretamente da firewall da empresa, firewall do Windows no computador gateway, e os dados de arquivo em si. Ativar estas regras permite ao gateway ligar à origem de ambos e sink com êxito. Ative regras para cada arquivo de dados que esteja envolvido na operação de cópia.

Por exemplo, a cópia de **um arquivo de dados no local para um receptor de SQL Database do Azure ou um receptor de Azure SQL Data Warehouse**, efetue os seguintes passos:

* Permitir saída **TCP** comunicações na porta **1433** para firewall da empresa e a firewall do Windows.
* Configure as definições de firewall do servidor SQL do Azure para adicionar o endereço IP do computador gateway à lista de endereços IP permitidos.

> [!NOTE]
> Se a firewall não permite a saída da porta é 1433, o Gateway não é possível aceder SQL do Azure de diretamente. Neste caso, pode utilizar [testado cópia](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) à base de dados do SQL Azure / armazém de dados do SQL do Azure. Neste cenário, precisaria de que apenas HTTPS (porta 443) para o movimento de dados.
>
>


### <a name="proxy-server-considerations"></a>Considerações sobre o servidor proxy
Se o seu ambiente de rede empresarial utiliza um servidor proxy para aceder à internet, configure o data management gateway para utilizar as definições de proxy adequado. Pode definir o proxy durante a fase de registo inicial.

![Conjunto de proxy durante o registo](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Gateway utiliza o servidor proxy para ligar ao serviço em nuvem. Clique em **alteração** ligação durante a configuração inicial. Pode ver o **definição de proxy** caixa de diálogo.

![Proxy de conjunto utilizando o Gestor de configuração](media/data-factory-data-management-gateway/SetProxySettings.png)

Existem três opções de configuração:

* **Não utilizar o proxy**: Gateway não explicitamente utilizar qualquer proxy para ligar a serviços em nuvem.
* **Utilizar o proxy de sistema**: Gateway utiliza a definição que está configurado no diahost.exe.config e diawp.exe.config de proxy.  Se não existe nenhum proxy está configurado no diahost.exe.config e diawp.exe.config, gateway se ligar ao serviço em nuvem diretamente, sem passar através do proxy.
* **Utilizar proxy personalizado**: configurar a definição a utilizar para o gateway, em vez de utilizar configurações de diahost.exe.config e diawp.exe.config de proxy HTTP.  Endereço e a porta são necessários.  Nome de utilizador e palavra-passe são opcionais, dependendo da definição de autenticação do proxy.  Todas as definições são encriptadas com o certificado da credencial do gateway e armazenadas localmente no computador do anfitrião de gateway.

O serviço de anfitrião do data management gateway reiniciará automaticamente depois de guardar as definições de proxy atualizadas.

Depois do gateway tiver sido registado com êxito, se pretender ver ou atualizar as definições de proxy, utilize o Gestor de configuração do Data Management Gateway.

1. Iniciar **Gestor de configuração do Data Management Gateway**.
2. Mudar para o **definições** separador.
3. Clique em **alteração** ligação na **HTTP Proxy** secção para iniciar o **definir o Proxy de HTTP** caixa de diálogo.  
4. Depois de clicar no **seguinte** botão, verá uma caixa de diálogo de aviso solicitando a sua permissão Guardar a definição de proxy e reinicie o serviço de anfitrião de Gateway.

Pode ver e atualizar o proxy HTTP utilizando a ferramenta Gestor de configuração.

![Proxy de conjunto utilizando o Gestor de configuração](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Se configurar um servidor proxy com a autenticação NTLM, o serviço de anfitrião de Gateway é executado sob a conta de domínio. Se alterar a palavra-passe para a conta de domínio mais tarde, não se esqueça de atualizar as definições de configuração para o serviço e reiniciá-lo em conformidade. Devido a este requisito, sugerimos que utilize uma conta de domínio dedicado para aceder ao servidor proxy que não requerem que Atualize a palavra-passe com frequência.
>
>

### <a name="configure-proxy-server-settings"></a>Configurar definições do servidor proxy
Se selecionar **utilizar o proxy de sistema** definir para o proxy HTTP, o gateway utiliza a definição no diahost.exe.config e diawp.exe.config de proxy.  Se não for especificado nenhum proxy diahost.exe.config e diawp.exe.config, gateway se ligar ao serviço em nuvem diretamente, sem passar através do proxy. O procedimento seguinte fornece instruções para atualizar o ficheiro diahost.exe.config.  

1. No Explorador de ficheiros, efetue uma cópia de segurança de C:\Program Files\Microsoft Data gestão Gateway\2.0\Shared\diahost.exe.config para fazer uma cópia de segurança do ficheiro original.
2. Inicie o Notepad.exe executar como administrador e abrir o ficheiro de texto "C:\Program Files\Microsoft Data gestão Gateway\2.0\Shared\diahost.exe.config. Localize a etiqueta predefinida para system.net conforme mostrado no seguinte código:

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   Em seguida, pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo seguinte:

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   Propriedades adicionais são permitidas no interior da tag de proxy para especificar as definições necessárias como scriptLocation. Consulte [proxy (definições de rede) do elemento](https://msdn.microsoft.com/library/sa91de1e.aspx) na sintaxe.

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. Guarde o ficheiro de configuração na localização original, em seguida, reinicie o serviço de anfitrião do Data Management Gateway, o que escolhe as alterações. Para reiniciar o serviço: utilizar a miniaplicação serviços no painel de controlo ou do **Gestor de configuração do Data Management Gateway** > clique no **parar o serviço** botão, em seguida, clique no **iniciar Serviço**. Se o serviço não iniciar, é provável que foi adicionada uma sintaxe de tag XML inválida para o ficheiro de configuração de aplicação que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar **ambos** diahost.exe.config e diawp.exe.config.  


Para além destes pontos, terá também de certificar-se de que o Microsoft Azure está na lista de permissões da sua empresa. A lista de endereços IP do Microsoft Azure válidos pode ser transferida do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Sintomas possíveis para problemas relacionados com o servidor de proxy e firewall
Se encontrar erros semelhantes dos seguintes, trata-se provavelmente devido a configuração incorreta do servidor proxy ou uma firewall, que bloqueia o gateway se liguem à fábrica de dados para se autenticar. Consulte a secção anterior para garantir que a firewall e servidor proxy estão configuradas corretamente.

1. Ao tentar registar o gateway, receberá o seguinte erro: "não conseguiu registar a chave do gateway. Antes de tentar registar novamente a chave do gateway, confirme que o data management gateway está num estado ligado e o serviço de anfitrião do Data Management Gateway foi iniciado."
2. Quando abrir o Gestor de configuração, consulte o estado como "Desligado" ou "Ligar". Quando visualiza registos de eventos do Windows, sob "Visualizador de eventos" > "Aplicação e registos de serviços" > "Data Management Gateway", vê mensagens de erro, tais como o seguinte erro:`Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Abra a porta 8050 para encriptação de credenciais
O **definição credenciais** aplicação utiliza a porta de entrada **8050** às credenciais de reencaminhamento para o gateway, quando configurar um serviço ligado no local no portal do Azure. Durante a configuração do gateway, por predefinição, a instalação do gateway abre-lo no computador gateway.

Se estiver a utilizar uma firewall de terceiros, pode abrir a porta 8050 manualmente. Caso se depare com problema firewall durante a configuração do gateway, pode utilizar o seguinte comando para instalar o gateway sem configurar a firewall.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Se optar por não abrir a porta 8050 no computador gateway, utilize mecanismos que não a utilização de **definição credenciais** aplicação a configurar credenciais do arquivo de dados. Por exemplo, pode utilizar [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) cmdlet do PowerShell. Consulte [definição credenciais e segurança](#set-credentials-and-securityy) secção na forma como as credenciais do arquivo de dados pode ser definida.

## <a name="update"></a>Atualizar
Por predefinição, o data management gateway é atualizado automaticamente quando estiver disponível uma versão mais recente do gateway. O gateway não for atualizado até terminar todas as tarefas agendadas. Não existem mais tarefas são processadas pelo gateway até a operação de atualização está concluída. Se a atualização falhar, gateway é revertido para a versão antiga.

Consulte a hora de atualização agendada nos seguintes locais:

* A página de propriedades do gateway no portal do Azure.
* Página inicial do Gestor configuração do Data Management Gateway
* Mensagem de notificação do tabuleiro de sistema.

O separador de início do Data Management Gateway Configuration Manager apresenta o agendamento de atualização e a última vez que o gateway foi instalado/atualizar.

![Atualizações agendadas](media/data-factory-data-management-gateway/UpdateSection.png)

Pode instalar a atualização de imediato ou aguarde que o gateway ser atualizados automaticamente na hora agendada. Por exemplo, a imagem seguinte mostra a mensagem de notificação apresentada no Gestor de configuração do Gateway, juntamente com o botão de atualização que podem clicar para instalá-lo imediatamente.

![Atualização no DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

A mensagem de notificação no tabuleiro do sistema deverá ter um aspeto conforme mostrado na imagem seguinte:

![Mensagem de tabuleiro do sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Ver o estado da operação de atualização (manual ou automática) no tabuleiro do sistema. Quando iniciar o Gestor de configuração do Gateway próxima vez que, verá uma mensagem na barra de notificação que o gateway foi atualizado juntamente com uma ligação para [o que é novo tópico](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Para desativar/ative a funcionalidade de atualização automática
Pode desativar/ative a funcionalidade de atualização automática, efetuando os seguintes passos:

[Para o gateway de nó único]
1. Inicie o Windows PowerShell no computador gateway.
2. Mudar para a pasta C:\Program Files\Microsoft Data gestão Gateway\2.0\PowerShellScript.
3. Execute o seguinte comando para ativar a atualização automática de funcionalidades DESATIVADO (desativar).   

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off
    ```
4. Para ativá-la novamente:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on  
    ```
[Para vários nós de elevada disponibilidade e dimensionável gateway (pré-visualização)](data-factory-data-management-gateway-high-availability-scalability.md)
1. Inicie o Windows PowerShell no computador gateway.
2. Mudar para a pasta C:\Program Files\Microsoft Data gestão Gateway\2.0\PowerShellScript.
3. Execute o seguinte comando para ativar a atualização automática de funcionalidades DESATIVADO (desativar).   

    Para o gateway com a funcionalidade de elevada disponibilidade (pré-visualização), é necessário um param AuthKey adicional.
    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. Para ativá-la novamente:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 
    ```

## <a name="configuration-manager"></a>Configuration Manager
Depois de instalar o gateway, pode iniciar o Gestor de configuração do Data Management Gateway de uma das seguintes formas:

1. No **pesquisa** janela, escreva **Data Management Gateway** para aceder a este utilitário.
2. Executar o executável **ConfigManager.exe** na pasta: **C:\Program Files\Microsoft Data gestão Gateway\2.0\Shared**

### <a name="home-page"></a>página inicial
A Home page permite-lhe executar as seguintes ações:

* Ver o estado do gateway (ligado ao serviço de nuvem, etc.).
* **Registar** utilizando uma chave a partir do portal.
* **Parar** e iniciar o **serviço de anfitrião do Data Management Gateway** no computador gateway.
* **Agendar atualizações** num momento específico dos dias.
* Ver a data quando o gateway foi **atualizado pela última vez**.

### <a name="settings-page"></a>Página de definições
A página de definições permite-lhe executar as seguintes ações:

* Ver, alterar e exportar **certificado** utilizado pelo gateway. Este certificado é utilizado para encriptar as credenciais da origem de dados.
* Alteração **porta HTTPS** para o ponto final. O gateway abre uma porta para definir as credenciais da origem de dados.
* **Estado** do ponto final
* Vista **certificado SSL** é utilizado para comunicação SSL entre o portal e o gateway para definir credenciais para as origens de dados.  

### <a name="diagnostics-page"></a>Página de diagnóstico
A página de diagnóstico permite-lhe executar as seguintes ações:

* Ativar verboso **registo**, ver os registos no Visualizador de eventos e enviar os registos para a Microsoft se Ocorreu uma falha.
* **Ligação de teste** a uma origem de dados.  

### <a name="help-page"></a>Página de ajuda
A página de ajuda apresenta as seguintes informações:  

* Breve descrição do gateway
* Número de versão
* Ligações para ajuda online, a declaração de privacidade e o contrato de licença.  

## <a name="monitor-gateway-in-the-portal"></a>Gateway de monitor no portal
No portal do Azure, pode ver o instantâneo de quase em tempo real de utilização de recursos (CPU, memória, network(in/out), etc.) numa máquina do gateway.  

1. No portal do Azure, navegue para a home page da fábrica de dados e clique em **serviços ligados** mosaico. 

    ![Home page da fábrica de dados](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Selecione o **gateway** no **serviços ligados** página.

    ![Página Serviços ligados](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. No **Gateway** página, pode ver a memória e utilização da CPU do gateway.

    ![Utilização da memória e CPU do gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Ativar **definições avançadas** para ver mais detalhes, como a utilização de rede.
    
    ![Avançadas de monitorização do gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

A tabela seguinte fornece descrições das colunas do **nós de Gateway** lista:  

Propriedade de monitorização | Descrição
:------------------ | :---------- 
Nome | Nome do gateway lógico e nós associadas ao gateway. O nó é uma máquina no local do Windows que tem o gateway instalado no mesmo. Para obter informações sobre a presença de mais do que um nó (até quatro nós) num único gateway lógico, consulte [Data Management Gateway - elevada disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md).    
Estado | Estado do gateway a lógico e os nós de gateway. Exemplo: Online/Offline/limitado/etc. Para obter informações sobre estes Estados terem, consulte [estado do Gateway](#gateway-status) secção. 
Versão | Mostra a versão do gateway lógico e cada nó de gateway. A versão do gateway lógico é determinada com base na versão de maioria de nós no grupo. Se existirem nós com diferentes versões no programa de configuração do gateway lógico, apenas os nós com o mesmo número de versão que a função de gateway lógico corretamente. Outras pessoas estão no modo de limitado e tem de ser atualizados manualmente (apenas no caso de falha de atualização automática). 
Memória disponível | Memória disponível no nó de gateway. Este valor é um instantâneo em tempo real near. 
Utilização da CPU | Utilização da CPU de um nó de gateway. Este valor é um instantâneo em tempo real near. 
Funcionamento em rede (na/saída) | Utilização de um nó de gateway de rede. Este valor é um instantâneo em tempo real near. 
Tarefas simultâneas (em execução / limitar) | Número de tarefas ou tarefas em execução em cada nó. Este valor é um instantâneo em tempo real near. Limite representa as máximas tarefas simultâneas para cada nó. Este valor é definido com base no tamanho da máquina. Pode aumentar o limite de aumentar verticalmente a execução da tarefa em simultâneo em cenários avançados, onde CPU/memória/rede é em utilizados, mas as atividades são exceder o tempo limite. Esta capacidade também está disponível com um gateway de nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada).  
Função | Existem dois tipos de funções no gateway de vários nós – emissor e de trabalho. Todos os nós são trabalhadores, o que significa que podem todos ser utilizados para executar tarefas. Não há apenas um nó de emissor, o que é utilizado para extrair as tarefas/tarefas dos serviços cloud e emiti-los a nós de trabalho diferente (incluindo o próprio).

Nesta página, verá algumas definições que tornam mais sentido quando existem dois ou mais nós (cenário de escalamento horizontal) no gateway. Consulte [Data Management Gateway - elevada disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md) para obter detalhes sobre como configurar um gateway de vários nós.

### <a name="gateway-status"></a>Estado do gateway
A tabela seguinte fornece os possíveis Estados de um **o nó de gateway**: 

Estado  | Comentários/cenários
:------- | :------------------
Online | Nó ligado ao serviço Data Factory.
Offline | Nó está offline.
A atualizar | O nó está a ser atualizados automaticamente.
Limitado | Devido ao problema de conectividade. Pode ser devido ao problema de 8050 de porta HTTP, o problema de conectividade do barramento de serviço ou o problema de sincronização de credenciais. 
Inativos | Nó está a ser uma configuração diferente da configuração dos outros nós da maioria.<br/><br/> Um nó pode ficar inativo quando não é possível ligar a outros nós. 


A tabela seguinte fornece os possíveis Estados de um **gateway lógico**. O estado do gateway depende Estados de nós de gateway. 

Estado | Comentários
:----- | :-------
Tem de ser registado | Nenhum nó ainda está registada para este gateway lógico
Online | Nós de gateway estão online
Offline | Nenhum nó num estado online.
Limitado | Nem todos os nós deste gateway estão em bom estado de funcionamento. Este estado é um aviso que algumas nó pode estar inativa! <br/><br/>Pode ser devido a um problema de sincronização no nó emissor/trabalho credencial. 

## <a name="scale-up-gateway"></a>Aumentar verticalmente gateway
Pode configurar o número de **tarefas de movimento de dados simultâneas** que podem ser executadas num nó de aumentar verticalmente a capacidade de mover dados entre no local e nuvem arquivos de dados. 

Quando a memória disponível e a CPU não são bem utilizados, mas a capacidade de inatividade é 0, deve aumentar verticalmente aumento do número de tarefas simultâneas que podem ser executadas num nó. Também poderá dimensionar quando as atividades são exceder o tempo limite porque o gateway está sobrecarregado. Nas definições avançadas de um nó de gateway, pode aumentar a capacidade máxima para um nó. 
  

## <a name="troubleshooting-gateway-issues"></a>Resolução de problemas de gateway
Consulte [resolução de problemas de gateway](data-factory-troubleshoot-gateway-issues.md) artigo para obter informações/sugestões para resolver problemas com a utilizar o data management gateway.  

## <a name="move-gateway-from-one-machine-to-another"></a>Mover o gateway de uma máquina para outra
Esta secção fornece os passos para mover cliente do gateway de uma máquina para outro computador.

1. No portal, navegue para o **home page da fábrica de dados**e clique em de **serviços ligados** mosaico.

    ![Ligação de Gateways de dados](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Selecione o gateway no **GATEWAYS dados** secção o **serviços ligados** página.

    ![Página dos serviços ligada com o gateway selecionado](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. No **gateway de dados** página, clique em **transferir e instalar o gateway de dados**.

    ![Transferir a ligação de gateway](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. No **configurar** página, clique em **transferir e instalar o gateway de dados**e siga as instruções para instalar o data gateway no computador.

    ![Configurar página](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Manter o **Gestor de configuração do Microsoft Data Management Gateway** abrir.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. No **configurar** página no portal, clique em **recrie chave** na barra de comandos e clique em **Sim** para a mensagem de aviso. Clique em **botão Copiar** junto ao texto da chave que copia a chave para a área de transferência. O gateway no computador antigo deixa de funcionar como em breve recriar a chave.  

    ![Recriar chave](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Colar o **chave** na caixa de texto no **registar Gateway** página do **Gestor de configuração do Data Management Gateway** no seu computador. (opcional) Clique em **chave do gateway mostrar** caixa de verificação para ver o texto da chave.

    ![Chave de cópia e o registo](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Clique em **registar** para registar o gateway com o serviço em nuvem.
9. No **definições** separador, clique em **alteração** para selecionar o mesmo certificado que foi utilizado com o gateway antigo, introduza o **palavra-passe**e clique em **concluir**.

   ![Especifique o certificado](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Pode exportar um certificado do gateway antigo efetuando os seguintes passos: Inicie o Gestor de configuração do Data Management Gateway no computador antigo, mude para o **certificado** separador, clique em **exportar** e siga as instruções.
10. Após o registo com êxito do gateway, deverá ver o **registo** definido como **registada** e **estado** definido como **iniciado** na Home page do Gestor de configuração do Gateway.

## <a name="encrypting-credentials"></a>Encriptação de credenciais
Para encriptar as credenciais no Editor de fábrica de dados, efetue os seguintes passos:

1. Iniciar o web browser o **computador gateway**, navegue para [portal do Azure](http://portal.azure.com). Procure a fábrica de dados, se necessário, abra a fábrica de dados no **DATA FACTORY** página e, em seguida, clique em **autor & implementar** para iniciar o Editor do Data Factory.   
2. Clique em existente **serviço ligado** na vista de árvore, consulte a respetiva definição JSON ou criar um serviço ligado que necessita de um gateway de gestão de dados (por exemplo: SQL Server ou Oracle).
3. No editor de JSON, para o **gatewayName** propriedade, introduza o nome do gateway.
4. Introduza o nome do servidor para o **origem de dados** propriedade no **connectionString**.
5. Introduza o nome de base de dados para o **catálogo inicial** propriedade no **connectionString**.    
6. Clique em **encriptar** botão na barra de comandos que inicia a, clique em-depois de **Gestor de credenciais** aplicação. Deverá ver o **definição credenciais** caixa de diálogo.

    ![Caixa de diálogo de credenciais de definição](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. No **definição credenciais** diálogo caixa, efetue os seguintes passos:
   1. Selecione **autenticação** que pretende que o serviço fábrica de dados a utilizar para ligar à base de dados.
   2. Introduza o nome do utilizador que tem acesso à base de dados para o **USERNAME** definição.
   3. Introduza a palavra-passe para o utilizador para o **palavra-passe** definição.  
   4. Clique em **OK** para encriptar as credenciais e fechar a caixa de diálogo.
8. Deverá ver uma **encryptedCredential** propriedade no **connectionString** agora.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
Se aceder ao portal a partir de uma máquina que é diferente do computador gateway, tem de se certificar de que a aplicação Gestor de credenciais pode ligar à máquina do gateway. Se a aplicação não é possível contactar o computador do gateway, não permite a para definir credenciais para a origem de dados e para testar a ligação à origem de dados.  

Quando utiliza o **definição credenciais** aplicação, o portal encripta as credenciais com o certificado especificado no **certificado** separador do **Gestor de configuração do Gateway**  no computador gateway.

Se estiver à procura de uma abordagem baseada em API para encriptar as credenciais, pode utilizar o [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) cmdlet do PowerShell para encriptar as credenciais. O cmdlet utiliza o certificado que gateway está configurado para utilizar para encriptar as credenciais. Adicionar credenciais encriptado para o **EncryptedCredential** elemento do **connectionString** no JSON. Utilizar o JSON com o [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) cmdlet ou no Editor do Data Factory.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Há uma abordagem mais para definir as credenciais utilizando o Editor do Data Factory. Se criar um serviço ligado do SQL Server utilizando o editor e introduza as credenciais em texto simples, as credenciais estão encriptadas com um certificado que o serviço fábrica de dados é proprietário. Não utilize o certificado que o gateway está configurado para utilizar. Embora esta abordagem poderá ser um pouco mais rápida em alguns casos, é menos segura. Por conseguinte, recomendamos que siga esta abordagem apenas para fins de desenvolvimento/teste.

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Esta secção descreve como criar e registar um gateway utilizando cmdlets do PowerShell do Azure.

1. Iniciar **Azure PowerShell** no modo de administrador.
2. Inicie sessão sua conta do Azure executando o seguinte comando e introduzir as suas credenciais do Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Utilize o **New-AzureRmDataFactoryGateway** cmdlet para criar um gateway lógico da seguinte forma:

    ```PowerShell
    $MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Comando de exemplo e de saída**:

    ```
    PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. No Azure PowerShell, mude para a pasta: **C:\Program Files\Microsoft Data gestão Gateway\2.0\PowerShellScript\**. Executar **RegisterGateway.ps1** associada com a variável local **$Key** conforme mostrado no comando seguinte. Este script regista o agente de cliente instalado no seu computador com o gateway lógico que criou anteriormente.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Pode registar o gateway num computador remoto, utilizando o parâmetro IsRegisterOnRemoteMachine. Exemplo:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Pode utilizar o **Get-AzureRmDataFactoryGateway** cmdlet para obter a lista de Gateways de fábrica de dados. Quando o **estado** mostra **online**, significa que o gateway está pronto a utilizar.

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Pode remover um gateway com o **remover AzureRmDataFactoryGateway** descrição cmdlet e a atualização para um gateway com o **conjunto AzureRmDataFactoryGateway** cmdlets. Para a sintaxe e outros detalhes sobre estes cmdlets, consulte a referência de Cmdlet do Data Factory.  

### <a name="list-gateways-using-powershell"></a>Gateways de lista com o PowerShell

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Remover gateway com o PowerShell

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>Passos seguintes
* Consulte [mover dados entre no local e na nuvem arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) artigo. As instruções, vai criar um pipeline que utiliza o gateway para mover dados de uma base de dados do SQL Server no local para um blob do Azure.  
