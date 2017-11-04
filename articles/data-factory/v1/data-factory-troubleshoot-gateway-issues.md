---
title: Resolver problemas do Data Management Gateway | Microsoft Docs
description: "Fornece sugestões para resolver problemas relacionados com o Data Management Gateway."
services: data-factory
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: eee8ee3af5918ddbe7393ff2574833f798ffcb19
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Utilizar o Data Management Gateway para resolver problemas
Este artigo fornece informações sobre como resolver problemas com a utilização do Data Management Gateway.

> [!NOTE]
> Este artigo aplica-se versão 1 do Azure Data Factory, que é geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [autoalojado integração tempo de execução na versão 2 do Data Factory](../create-self-hosted-integration-runtime.md).

Consulte o [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter informações detalhadas sobre o gateway. Consulte o [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções de mover dados de uma base de dados do SQL Server no local para armazenamento de Blobs do Microsoft Azure utilizando o gateway.

## <a name="failed-to-install-or-register-gateway"></a>Falha ao instalar ou registar o gateway
### <a name="1-problem"></a>1. Problema
Pode ver esta mensagem de erro quando instalar e registar um gateway, especificamente, ao transferir o ficheiro de instalação do gateway.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Causa
A máquina em que está a tentar instalar o gateway falhou ao transferir o ficheiro de instalação do gateway mais recente a partir do Centro de transferências devido a um problema de rede.

#### <a name="resolution"></a>Resolução
Verifique as definições de servidor de proxy de firewall para ver se as definições de bloqueiam a ligação de rede do computador para o [Centro de transferências](https://download.microsoft.com/)e atualizar as definições em conformidade.

Em alternativa, pode transferir o ficheiro de instalação para o gateway mais recente do [Centro de transferências](https://www.microsoft.com/download/details.aspx?id=39717) nas outras máquinas que podem aceder ao centro de transferências. Pode, em seguida, copie o ficheiro de instalador para o computador do anfitrião de gateway e executá-lo manualmente para instalar e atualizar o gateway.

### <a name="2-problem"></a>2. Problema
Este erro ocorrer quando está a tentar instalar um gateway clicando **instalar diretamente neste computador** no portal do Azure.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Causa
Já existe um gateway instalado no computador.

#### <a name="resolution"></a>Resolução
Desinstalar o gateway existente no computador e clique em de **instalar diretamente neste computador** ligar novamente.

### <a name="3-problem"></a>3. Problema
Poderá ver este erro ao registar um novo gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Causa
Poderá ver esta mensagem para uma das seguintes razões:

* O formato da chave do gateway é inválido.
* A chave do gateway foi invalidada.
* A chave do gateway tem sido regenerada do portal.  

#### <a name="resolution"></a>Resolução
Certifique-se de que o se estiver a utilizar a chave de gateway correto do portal. Se for necessário, voltar a gerar uma chave e a chave utilizada para registar o gateway.

### <a name="4-problem"></a>4. Problema
Poderá ver a seguinte mensagem de erro quando está a registar um gateway.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Conteúdo ou formato de chave é inválido](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Causa
O conteúdo ou o formato da chave do gateway de entrada é incorreto. Uma das razões pode ser que apenas uma parte da chave que copiou do portal ou estiver a utilizar uma chave inválida.

#### <a name="resolution"></a>Resolução
Gerar uma chave de gateway no portal e utilize o botão Copiar para copiar a chave de toda. Em seguida, cole-o nesta janela para registar o gateway.

### <a name="5-problem"></a>5. Problema
Poderá ver a seguinte mensagem de erro quando está a registar um gateway.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Chave do gateway é inválido ou está vazio](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Causa
A chave do gateway foi regenerada ou o gateway foi eliminado no portal do Azure. Também pode acontecer se o programa de configuração do Data Management Gateway não é mais recente.

#### <a name="resolution"></a>Resolução
Verifique se o programa de configuração do Data Management Gateway é a versão mais recente, pode encontrar a versão mais recente sobre o Microsoft [Centro de transferências](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Se a configuração atual / mais recente e gateway ainda existe no Portal, voltar a gerar a chave do gateway no portal do Azure, utilize o botão Copiar para copiar a chave completa e, em seguida, cole-nesta janela para registar o gateway. Caso contrário, recriar o gateway e comece de novo.

### <a name="6-problem"></a>6. Problema
Poderá ver a seguinte mensagem de erro quando está a registar um gateway.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Chave do gateway é inválido ou está vazio](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Causa
Este erro pode acontecer porque o gateway foi eliminado ou a chave do gateway associado foi regenerada.

#### <a name="resolution"></a>Resolução
Se o gateway foi eliminado, voltar a criar o gateway do portal, clique em **registar**, copie a chave a partir do portal, cole-o e tente registar o gateway.

Se o gateway ainda existe mas a chave foi regenerada, utilize a nova chave para registar o gateway. Se não tiver a chave, regenere a chave novamente a partir do portal.

### <a name="7-problem"></a>7. Problema
Quando estiver a registar um gateway, poderá ter de introduzir o caminho e a palavra-passe para um certificado.

![Especifique o certificado](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Causa
O gateway foi registado em outras máquinas antes. Durante o registo inicial de um gateway, um certificado de encriptação foi associado com o gateway. O certificado pode ser personalizada gerado pelo gateway ou fornecido pelo utilizador.  Este certificado é utilizado para encriptar as credenciais do arquivo de dados (serviço ligado).  

![Exportar certificado](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Ao restaurar o gateway no outro computador anfitrião, o Assistente de registo pede-lhe para este certificado desencriptar credenciais anteriormente encriptadas com este certificado.  Sem este certificado não não possível desencriptar as credenciais, o novo gateway e execuções de atividade de cópia subsequentes associadas a este novo gateway falhará.  

#### <a name="resolution"></a>Resolução
Se exportou o certificado da credencial da máquina de gateway original utilizando o **exportar** botão no **definições** separador no Data Management Gateway do Configuration Manager, utilize o certificado aqui.

Não é possível ignorar esta fase ao recuperar um gateway. Se o certificado está em falta, terá de eliminar o gateway do portal e voltar a criar um novo gateway.  Além disso, atualize todos os serviços ligados que estão relacionados com o gateway por reentering as respetivas credenciais.

### <a name="8-problem"></a>8. Problema
Poderá ver a seguinte mensagem de erro.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Causa
Este erro ocorre quando o gateway está num ambiente que necessita de um proxy HTTP para aceder a recursos da Internet ou a palavra-passe de autenticação do proxy é alterado, mas não é atualizado em conformidade no seu gateway.

#### <a name="resolution"></a>Resolução
Siga as instruções no [considerações de servidor Proxy](#proxy-server-considerations) secção deste artigo e configurar as definições de proxy com o Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Gateway está online com funcionalidade limitada
### <a name="1-problem"></a>1. Problema
Ver o estado do gateway como online com funcionalidade limitada.

#### <a name="cause"></a>Causa
Ver o estado do gateway como online com funcionalidade limitada para uma das seguintes razões:

* Gateway não é possível ligar ao serviço em nuvem através do Service Bus do Azure.
* Serviço em nuvem não é possível ligar ao gateway através do Service Bus.

Quando o gateway está online com funcionalidade limitada, poderá não conseguir utilizar o Assistente de cópia do Data Factory para criar pipelines de dados para copiar dados de ou para os arquivos de dados no local. Como solução, pode utilizar o Editor do Data Factory no portal, no Visual Studio ou no Azure PowerShell.

#### <a name="resolution"></a>Resolução
Resolução para este problema (online com funcionalidade limitada) baseia-se no se o gateway não é possível ligar ao serviço em nuvem ou de outra forma. As secções seguintes fornecem estas resoluções.

### <a name="2-problem"></a>2. Problema
Consulte o erro seguinte.

`Error: Gateway cannot connect to cloud service through service bus`

![Gateway não é possível ligar ao serviço em nuvem](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa
Gateway não é possível ligar ao serviço em nuvem através do Service Bus.

#### <a name="resolution"></a>Resolução
Siga estes passos para configurar o gateway online:

1. Permitir o endereço IP regras de saída no computador gateway e da firewall da empresa. Pode localizar endereços IP a partir do registo de eventos do Windows (ID = = 401): foi efetuada uma tentativa de aceder a um socket de forma proibida pelo respetivos permissões de acesso XX. XX. XX. XX:9350.
* Configure definições de proxy no gateway. Consulte o [considerações de servidor Proxy](#proxy-server-considerations) secção para obter detalhes.
* Ative portas de saída 5671 e 9350-9354 em ambos os a Firewall do Windows no computador gateway e da firewall da empresa. Consulte o [portas e firewall](#ports-and-firewall) secção para obter detalhes. Este passo é opcional, mas recomendamos por razões de desempenho de.

### <a name="3-problem"></a>3. Problema
Consulte o erro seguinte.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Causa
Um erro transitório na conectividade de rede.

#### <a name="resolution"></a>Resolução
Siga estes passos para configurar o gateway online:

1. Aguarde alguns minutos, a conectividade será automaticamente recuperada quando o erro é removido.
* Se o erro persistir, reinicie o serviço de gateway.

## <a name="failed-to-author-linked-service"></a>Falha ao criar o serviço ligado
### <a name="problem"></a>Problema
Poderá ver este erro quando tenta utilizar o Gestor de credenciais no portal para as credenciais para um novo serviço ligado de entrada ou atualizar as credenciais de um serviço ligado existente.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Quando vir este erro, a página de definições do Data Management Gateway Configuration Manager aspeto que poderá ter a captura de ecrã seguinte.

![Não é possível contactar a base de dados](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Causa
O certificado SSL pode ter sido perdido no computador gateway. O computador do gateway não é possível carregar o certificado atualmente utilizado para encriptação SSL. Também poderá ver uma mensagem de erro no registo de eventos que é semelhante para a mensagem seguinte.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Resolução
Siga estes passos para resolver o problema:

1. Inicie o Gestor de configuração do Data Management Gateway.
2. Mudar para o **definições** separador.  
3. Clique em de **alterar** botão para alterar o certificado SSL.

   ![Botão de certificado de alteração](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Selecione um novo certificado como o certificado SSL. Pode utilizar qualquer certificado SSL que é gerado por si ou qualquer organização.

   ![Especifique o certificado](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Falha da atividade de cópia
### <a name="problem"></a>Problema
É possível que repare a falha "UserErrorFailedToConnectToSqlserver" seguinte depois de configurar um pipeline no portal.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Causa
Isto pode acontecer por diferentes motivos e atenuação varia em conformidade.

#### <a name="resolution"></a>Resolução
Permita ligações de TCP de saída através da porta TCP/1433 do lado do cliente Data Management Gateway antes de ligar a uma base de dados do SQL Server.

Se a base de dados de destino é uma base de dados SQL do Azure, verifique o SQL Server as definições da firewall para o Azure, bem como.

Consulte a secção seguinte para testar a ligação ao arquivo de dados no local.

## <a name="data-store-connection-or-driver-related-errors"></a>Ligação de arquivo de dados ou erros relacionados com o controlador
Se vir armazenar erros relacionados com o controlador ou de ligação de dados, conclua os seguintes passos:

1. Inicie o Gestor de configuração do Data Management Gateway no computador gateway.
2. Mudar para o **diagnóstico** separador.
3. No **Testar ligação**, adicione os valores de grupo do gateway.
4. Clique em **teste** para ver se pode ligar à origem de dados no local da máquina de gateway utilizando as informações de ligação e as credenciais. Depois de instalar um controlador, se a ligação de teste continuar a falhar, reinicie o gateway para que este possa escolher a mais recente.

![Ligação de teste no separador de diagnóstico](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Registos do gateway
### <a name="send-gateway-logs-to-microsoft"></a>Enviar registos de gateway para a Microsoft
Quando contactar o Support da Microsoft para obter ajuda na resolução de problemas de gateway, poderá ser-lhe pedido para partilhar os seus registos de gateway. Com a versão do gateway, pode partilhar os registos de gateway necessária com dois cliques de botão no Data Management Gateway do Configuration Manager.    

1. Mudar para o **diagnóstico** separador no Data Management Gateway do Configuration Manager.

    ![Separador de diagnóstico do Gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Clique em **enviar registos de** para ver a seguinte caixa de diálogo.

    ![Registos de envio de Gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Opcional) Clique em **ver registos** rever os registos de eventos viewer.
4. (Opcional) Clique em **privacidade** para rever a declaração de privacidade do Microsoft web services.
5. Quando estiver satisfeito com o que está prestes a carregar, clique em **enviar registos de** , na verdade, enviar os registos dos últimos sete dias para a Microsoft para resolução de problemas. Deverá ver o estado da operação de envio registos conforme mostrado na captura de ecrã seguinte.

    ![Data Management Gateway enviar registos de estado](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Depois de concluída a operação, verá uma caixa de diálogo conforme mostrado na captura de ecrã seguinte.

    ![Data Management Gateway enviar registos de estado](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Guardar o **relatório ID** e partilhá-las com Support da Microsoft. O ID de relatório é utilizado para localizar os registos de gateway que carregou para resolução de problemas.  O ID de relatório é também guardado de eventos viewer.  Pode encontrá-lo ao observar o ID de evento "25" e verifique a data e hora.

    ![Data Management Gateway enviar registos de ID de relatório](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Gateway de arquivo os registos de computador do anfitrião de gateway
Existem alguns cenários em que tiver problemas de gateway e não pode partilhar os registos do gateway diretamente:

* Se instalar manualmente o gateway e registar o gateway.
* Tente registar o gateway com uma chave gerada novamente no Data Management Gateway do Configuration Manager.
* Tente enviar os registos e não é possível ligar o serviço de anfitrião de gateway.

Nestes cenários, pode guardar registos de gateway como um ficheiro zip e partilhá-las quando contactar o suporte da Microsoft. Por exemplo, se receber um erro ao registar o gateway como mostrado na captura de ecrã seguinte.   

![Erro de registo de Gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Clique em de **arquivar registos do gateway** associar para arquivar e guarde os registos e, em seguida, partilhar o ficheiro zip com suporte da Microsoft.

![Registos de arquivo de Gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Localize os registos do gateway
Pode encontrar informações de registo de gateway detalhadas nos registos de eventos do Windows.

1. Iniciar o Windows **Visualizador de eventos**.
2. Localize os registos no **registos de serviços e aplicações** > **Data Management Gateway** pasta.

 Quando estiver a resolver problemas relacionados com o gateway, procure eventos ao nível de erro de eventos viewer.

![O Data Management Gateway se regista no Visualizador de eventos](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
