---
title: "Implementar o gateway de fábrica ligado - Azure | Microsoft Docs"
description: "Como implementar um gateway no Windows ou Linux para ativar a conetividade à fábrica de ligado a solução pré-configurada."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: f6a69ecbeb09dc042eff7c1f95ee518e701b0507
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>Implementar um gateway no Windows ou Linux para a solução de fábrica ligado pré-configurada

O software necessário para implementar um gateway para a solução de fábrica ligado pré-configurada tem dois componentes:

* O *OPC Proxy* estabelece uma ligação ao IoT Hub. O *OPC Proxy* , em seguida, aguarda para mensagens de comando e controlo de Browser OPC integrada que é executado no portal de solução de fábrica ligado.
* O *OPC publicador* estabelece ligação a servidores OPC UA no local existentes e reencaminha as mensagens de telemetria dos mesmos ao IoT Hub.

Ambos os componentes são open source e estão disponíveis como origem no GitHub e como contentores de Docker:

| GitHub | DockerHub |
| ------ | --------- |
| [Publicador OPC][lnk-publisher-github] | [Publicador OPC][lnk-publisher-docker] |
| [OPC Proxy][lnk-proxy-github] | [OPC Proxy][lnk-proxy-docker] |

Nenhum destinado ao público endereço IP ou holes na firewall do gateway são necessários para o componente. O OPC Proxy e OPC publicador a utilizar apenas saídas portas 443, 5671 e 8883.

Os passos neste artigo mostram como implementar um gateway com o Docker numa [Windows](#windows-deployment) ou [Linux](#linux-deployment). O gateway permite a conectividade com a solução de fábrica ligado pré-configurada.

> [!NOTE]
> O software de gateway que é executado no contentor do Docker é [Azure IoT Edge].

## <a name="windows-deployment"></a>Implementação do Windows

> [!NOTE]
> Se ainda não tiver um dispositivo de gateway, a Microsoft recomenda que comprar um gateway comercial a partir de um dos respetivos parceiros. Visite o [catálogo de dispositivos do IoT do Azure] para obter uma lista de dispositivos de gateway compatíveis com a solução de fábrica ligado. Siga as instruções que vêm com o dispositivo para configurar o gateway. Em alternativa, utilize as seguintes instruções para configurar manualmente a um dos seus gateways existentes.

### <a name="install-docker"></a>Instalar Docker

Instalar [Docker para Windows] no seu dispositivo de gateway baseado no Windows. Durante a configuração do Windows Docker, selecione uma unidade no computador anfitrião para partilhar com Docker. A seguinte captura de ecrã mostra a partilha de unidade D no seu sistema do Windows:

![Instalar Docker][img-install-docker]

Em seguida, crie uma pasta denominada **docker** na raiz da unidade partilhada.
Também pode efetuar este passo depois de instalar o docker do **definições** menu.

### <a name="configure-the-gateway"></a>Configurar o gateway

1. É necessário o **iothubowner** cadeia de ligação do seu Azure IoT Suite ligado a implementação de fábrica para concluir a implementação do gateway. No [portal do Azure], navegue até ao seu IoT Hub no grupo de recursos que criou quando implementou a solução de fábrica ligado. Clique em **políticas de acesso partilhado** para acesso a **iothubowner** cadeia de ligação:

    ![Localize a cadeia de ligação do IoT Hub][img-hub-connection]

    Copiar o **cadeia de ligação - chave primária** valor.

1. Configurar o gateway do IoT Hub, executando os módulos de dois gateway **depois** numa linha de comandos com:

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;**  é o nome para dar ao Editor de UA OPC no formato **publicador.&lt; o nome de domínio completamente qualificado&gt;**. Por exemplo, se denomina-se a sua rede de fábrica **myfactorynetwork.com**, a **ApplicationName** valor é **publisher.myfactorynetwork.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;**  é o **iothubowner** cadeia de ligação que copiou no passo anterior. Esta cadeia de ligação é apenas utilizada neste passo, não precisa de-lo nos passos seguintes:

    Utilize o d: mapeada\\pasta docker (o `-v` argumento) mais tarde para manter os dois certificados x. 509 que utilizam os módulos de gateway.

### <a name="run-the-gateway"></a>Executar o gateway

1. Reinicie o gateway com os seguintes comandos:

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Por motivos de segurança, os certificados x. 509 dois continuada de d:\\docker contém a chave privada. Limitar o acesso a esta pasta para as credenciais (normalmente **administradores**) que utiliza para executar o contentor de Docker. Faça duplo clique de d:\\pasta docker, escolha **propriedades**, em seguida, **segurança**e, em seguida, **editar**. Dê **administradores** controlo total e remover todas as pessoas pessoa:

    ![Conceder permissões de partilha de Docker][img-docker-share]

1. Verifique a conectividade de rede. Numa linha de comandos, introduza o comando `ping publisher.<your fully qualified domain name>` para enviar um ping o gateway. Se o destino estiver inacessível, adicione o endereço IP e o nome do seu gateway para o ficheiro de anfitriões no seu gateway. O ficheiro de anfitriões está localizado no **Windows\\System32\\controladores\\etc** pasta.

1. Em seguida, tente estabelecer ligação ao publicador através de um cliente de OPC UA local em execução no gateway. O ponto final de OPC UA URL é `opc.tcp://publisher.<your fully qualified domain name>:62222`. Se não tiver um cliente de OPC UA, pode transferir e utilizar um [cliente de OPC UA open source].

1. Quando tiver concluído com êxito estes testes locais, navegue para o **ligar o seu próprio servidor de UA OPC** página no portal de solução de fábrica ligado. Introduza o URL de ponto final do fabricante (`tcp://publisher.<your fully qualified domain name>:62222`) e clique em **Connect**. Receber um aviso de certificado, em seguida, clique em **continuar.** Em seguida, obter um erro que o publicador não confia UA Web cliente. Para resolver este erro, copie o **UA Web cliente** certificado do **D:\\docker\\rejeitado certificados\\certificados** pasta para o **D:\\docker\\UA aplicações\\certificados** pasta no gateway. Não é necessário reiniciar do gateway. Repita este passo. Agora pode ligar ao gateway de nuvem e está pronto para adicionar servidores de OPC UA à solução.

### <a name="add-your-opc-ua-servers"></a>Adicionar os servidores de OPC UA

1. Navegue para o **ligar o seu próprio servidor de UA OPC** página no portal de solução de fábrica ligado. Siga os passos da secção anterior para estabelecer fidedignidade entre o portal de fábrica ligado e o servidor de OPC UA ends. Este passo estabelece uma confiança de certificados do portal do factory ligado e o servidor de OPC UA mútua e cria uma ligação.

1. Procurar a árvore de nós de OPC UA do seu servidor de OPC UA, os nós OPC com o botão direito e selecione **publicar**. Para publicar funcione desta forma, o servidor de OPC UA e o publicador tem de ser na mesma rede. Por outras palavras, se o nome de domínio completamente qualificado do publicador é **publisher.mydomain.com** , em seguida, o nome de domínio completamente qualificado do servidor OPC UA tem de ser, por exemplo, **myopcuaserver.mydomain.com**. Se o programa de configuração for diferente, pode adicionar manualmente nós para o ficheiro de publishesnodes.json encontrado no **D:\\docker** pasta. O ficheiro publishesnodes.json é gerado automaticamente no primeiro bem-sucedida publicar de um nó OPC.

1. Agora flui de telemetria do dispositivo de gateway. Pode ver a telemetria no **fábrica localizações** vista do portal fábrica ligados em **nova fábrica**.

## <a name="linux-deployment"></a>Implementação do Linux

> [!NOTE]
> Se ainda não tiver um dispositivo de gateway, a Microsoft recomenda que comprar um gateway comercial a partir de um dos respetivos parceiros. Visite o [catálogo de dispositivos do IoT do Azure] para obter uma lista de dispositivos de gateway compatíveis com a solução de fábrica ligado. Siga as instruções que vêm com o dispositivo para configurar o gateway. Em alternativa, utilize as seguintes instruções para configurar manualmente a um dos seus gateways existentes.

[Instalar Docker] no seu dispositivo de gateway do Linux.

### <a name="configure-the-gateway"></a>Configurar o gateway

1. É necessário o **iothubowner** cadeia de ligação do seu Azure IoT Suite ligado a implementação de fábrica para concluir a implementação do gateway. No [portal do Azure], navegue até ao seu IoT Hub no grupo de recursos que criou quando implementou a solução de fábrica ligado. Clique em **políticas de acesso partilhado** para acesso a **iothubowner** cadeia de ligação:

    ![Localize a cadeia de ligação do IoT Hub][img-hub-connection]

    Copiar o **cadeia de ligação - chave primária** valor.

1. Configurar o gateway do IoT Hub, executando os módulos de dois gateway **depois** de uma shell com:

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;**  é o nome da aplicação OPC UA cria o gateway no formato **publicador.&lt; o nome de domínio completamente qualificado&gt;**. Por exemplo, **publisher.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;**  é o **iothubowner** cadeia de ligação que copiou no passo anterior. Esta cadeia de ligação é apenas utilizada neste passo, não precisa de-lo nos passos seguintes:

    Utilizar o **/ partilhado** pasta (a `-v` argumento) mais tarde para manter os dois certificados x. 509 que utilizam os módulos de gateway.

### <a name="run-the-gateway"></a>Executar o gateway

1. Reinicie o gateway com os seguintes comandos:

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 --rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Por motivos de segurança, os certificados x. 509 dois continuada no **/ partilhado** pasta contém a chave privada. Limitar o acesso a esta pasta para as credenciais que utiliza para executar o contentor de Docker. Definir as permissões para **raiz** apenas, utilize o `chmod` shell de comandos na pasta.

1. Verifique a conectividade de rede. A partir de uma shell, introduza o comando `ping publisher.<your fully qualified domain name>` para enviar um ping o gateway. Se o destino estiver inacessível, adicione o endereço IP e o nome do seu gateway para o ficheiro de anfitriões no seu gateway. O ficheiro de anfitriões está localizado no **/etc** pasta.

1. Em seguida, tente estabelecer ligação ao publicador através de um cliente de OPC UA local em execução no gateway. O ponto final de OPC UA URL é `opc.tcp://publisher.<your fully qualified domain name>:62222`. Se não tiver um cliente de OPC UA, pode transferir e utilizar um [cliente de OPC UA open source].

1. Quando tiver concluído com êxito estes testes locais, navegue para o **ligar o seu próprio servidor de UA OPC** página no portal de solução de fábrica ligado. Introduza o URL de ponto final do fabricante (`tcp://publisher.<your fully qualified domain name>:62222`) e clique em **Connect**. Receber um aviso de certificado, em seguida, clique em **continuar.** Em seguida, obter um erro que o publicador não confia UA Web cliente. Para resolver este erro, copie o **UA Web cliente** certificado do **partilhado/rejeitado certificados/certificados** pasta para o **/shared/UA aplicações/certificados** pasta no gateway. Não é necessário reiniciar do gateway. Repita este passo. Agora pode ligar ao gateway de nuvem e está pronto para adicionar servidores de OPC UA à solução.

### <a name="add-your-opc-ua-servers"></a>Adicionar os servidores de OPC UA

1. Navegue para o **ligar o seu próprio servidor de UA OPC** página no portal de solução de fábrica ligado. Siga os passos da secção anterior para estabelecer fidedignidade entre o portal de fábrica ligado e o servidor de OPC UA ends. Este passo estabelece uma confiança de certificados do portal do factory ligado e o servidor de OPC UA mútua e cria uma ligação.

1. Procurar a árvore de nós de OPC UA do seu servidor de OPC UA, os nós OPC com o botão direito e selecione **publicar**. Para publicar funcione desta forma, o servidor de OPC UA e o publicador tem de ser na mesma rede. Por outras palavras, se o nome de domínio completamente qualificado do publicador é **publisher.mydomain.com** , em seguida, o nome de domínio completamente qualificado do servidor OPC UA tem de ser, por exemplo, **myopcuaserver.mydomain.com**. Se o programa de configuração for diferente, pode adicionar manualmente nós para o ficheiro de publishesnodes.json encontrado no **/ partilhado** pasta. O publishesnodes.json é gerado automaticamente no primeiro bem-sucedida publicar de um nó OPC.

1. Agora flui de telemetria do dispositivo de gateway. Pode ver a telemetria no **fábrica localizações** vista do portal fábrica ligados em **nova fábrica**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a arquitetura da solução fábrica ligado pré-configuradas, consulte [instruções sobre a solução pré-configurada de fábrica ligada][lnk-walkthrough].

Saiba mais sobre o [implementação de referência do publicador OPC](iot-suite-connected-factory-publisher.md).

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker para Windows]: https://www.docker.com/docker-windows
[catálogo de dispositivos do IoT do Azure]: https://catalog.azureiotsuite.com/?q=opc
[portal do Azure]: http://portal.azure.com/
[cliente de OPC UA open source]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Instalar Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT Edge]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy