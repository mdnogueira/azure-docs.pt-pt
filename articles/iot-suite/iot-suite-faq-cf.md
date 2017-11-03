---
title: "Ligado a solução de fábrica FAQ – Azure | Microsoft Docs"
description: "Perguntas mais frequentes para a fábrica de ligação do IoT Suite"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: corywink
ms.openlocfilehash: 1f0d6fb377c21f5ece77a878fd285b5dda2f4fbc
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>Perguntas mais frequentes para a fábrica de ligação do IoT Suite solução pré-configurada

Além disso, consulte geral [FAQ](iot-suite-faq.md) IoT Suite.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>Onde posso encontrar o código de origem para a solução pré-configurada?

O código de origem é armazenado no repositório de GitHub seguinte:

* [Solução de fábrica ligado pré-configurada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>O que é OPC UA?

OPC Unified arquitetura (UA), lançada em 2008, é uma interoperabilidade independentes da plataforma, orientada para serviços padrão. OPC UA é utilizado pelo vários sistemas industriais e dispositivos, como da indústria PCs, PLCs e sensores. OPC UA integra-se a funcionalidade das especificações OPC clássico para uma estrutura extensível com segurança incorporadas. É um padrão que é suscitada pelo departamento, a base de OPC. O [OPC Foundation](http://opcfoundation.org/) é uma organização de não-para-lucros com mais do que 440 membros. O objetivo da organização é utilizar especificações OPC para facilitar a interoperabilidade de fornecedor de multi, várias plataforma, segura e fiável através de:

* Infraestrutura
* Especificações
* Tecnologia
* Processos

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Por que motivo Microsoft escolher OPC UA para a solução de fábrica ligado pré-configurada?

Microsoft escolheu OPC UA porque é uma norma da plataforma aberta e não-proprietários, independentes, reconhecido por da indústria e comprovado. É um requisito para soluções de arquitetura de referência de Industrie 4.0 (RAMI4.0) garantindo a interoperabilidade entre um conjunto amplo de processos de produção e de equipamento. Microsoft vê a pedido dos nossos clientes para criar soluções de Industrie 4.0. Suporte para OPC UA ajuda a reduzir a barreira para os clientes atingir os seus objetivos e fornece o valor de negócio imediata aos mesmos.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Como adicionar um endereço IP público para a simulação VM?

Tem duas opções para adicionar o endereço IP:

* Utilizar o script do PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory). Transmita o nome da implementação como parâmetro. Para uma implementação local, utilizar `<your username>ConnFactoryLocal`. O script imprime terminar o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos da sua implementação. Exceto para uma implementação local, o grupo de recursos tem o nome que especificou como solução ou o nome da implementação. Para uma implementação local utilizando o script de compilação, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Agora, adicione um novo **endereço IP público** recurso para o grupo de recursos.

> [!NOTE]
> Em ambos os casos, certifique-se de que instala as correções mais recentes ao seguir as instruções [Ubuntu site](https://wiki.ubuntu.com/Security/Upgrades). Manter atualizado para a instalação, desde que a VM está acessível através de um endereço IP público.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Como posso remover o endereço IP público para a simulação VM?

Tem duas opções para remover o endereço IP:

* Utilize o script do PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 do [repositório](https://github.com/Azure/azure-iot-connected-factory). Transmita o nome da implementação como parâmetro. Para uma implementação local, utilizar `<your username>ConnFactoryLocal`. O script imprime terminar o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos da sua implementação. Exceto para uma implementação local, o grupo de recursos tem o nome que especificou como solução ou o nome da implementação. Para uma implementação local utilizando o script de compilação, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Remover agora o **endereço IP público** recurso do grupo de recursos.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Como posso iniciar sessão para a simulação VM?

O início de sessão na simulação VM só é suportado se tiver implementado a sua solução utilizando o script do PowerShell `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Se implementou a solução de www.azureiotsuite.com, não pode iniciar sessão para a VM. Não é possível iniciar sessão, porque a palavra-passe for gerada aleatoriamente e não pode redefini-lo.

1. Adicione um endereço IP público à VM. Consulte [como adicionar um endereço IP público para a simulação VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Crie uma sessão SSH para a VM utilizando o endereço IP da VM.
1. É o nome de utilizador a utilizar: `docker`.
1. A palavra-passe a utilizar depende a versão utilizada para implementar:
    * Para soluções implementadas utilizando o script de build.ps1 antes de 1 de Junho de 2017, é a palavra-passe: `Passw0rd`.
    * Para soluções implementadas utilizando o script de build.ps1 após 1 de Junho de 2017, pode encontrar a palavra-passe a `<name of your deployment>.config.user` ficheiro. A palavra-passe é armazenada no **VmAdminPassword** definição. A palavra-passe for gerada aleatoriamente no momento da implementação, a menos que especifique-la utilizando o `build.ps1` parâmetro do script`-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Como parar e iniciar a todos os processos de docker na simulação VM?

1. Inicie sessão simulação VM. Consulte [como consigo iniciar sessão na simulação VM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Para verificar os contentores estão ativos, execute: `docker ps`.
1. Para parar todos os contentores de simulação, execute: `./stopsimulation`.
1. Para iniciar todos os contentores de simulação:
    * Exportar uma variável de shell com o nome **IOTHUB_CONNECTIONSTRING**. Utilize o valor do **IotHubOwnerConnectionString** definição o `<name of your deployment>.config.user` ficheiro. Por exemplo:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Execute `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Como atualizar a simulação na VM?

Se tiver efectuado alterações para a simulação, pode utilizar o script do PowerShell `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory) utilizando o `updatedimulation` comando. Este script baseia-se todos os componentes de simulação, parar a simulação na VM, carrega, instala e inicia-los.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Como posso saber se a cadeia de ligação do hub IoT utilizado pela minha solução?

Se tiver implementado a sua solução com o `build.ps1` script no [repositório](https://github.com/Azure/azure-iot-connected-factory), a cadeia de ligação é o valor de **IotHubOwnerConnectionString** no `<name of your deployment>.config.user` ficheiro.

Também pode encontrar a cadeia de ligação no portal do Azure. No recurso do IoT Hub no grupo de recursos da sua implementação, localize as definições de cadeia de ligação.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Os dispositivos do IoT Hub utiliza a simulação de fábrica ligado?

A simulação Self-regista os seguintes dispositivos:

* proxy.Beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.Mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.Seattle.corp.contoso
* Publisher.Beijing.corp.contoso
* Publisher.capetown.corp.contoso
* Publisher.Mumbai.corp.contoso
* Publisher.munich0.corp.contoso
* Publisher.rio.corp.contoso
* Publisher.Seattle.corp.contoso

Utilizar o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [iothub explorer](https://github.com/azure/iothub-explorer) ferramenta, pode verificar que dispositivos estão registados com o hub IoT está a utilizar a sua solução. Para utilizar estas ferramentas, terá da cadeia de ligação para o IoT hub na sua implementação.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Como posso obter dados de registo dos componentes simulação?

Todos os componentes na simulação registar informações nos ficheiros de registo. Estes ficheiros podem ser encontrados na VM, na pasta `home/docker/Logs`. Para obter os registos, pode utilizar o script do PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Este script tem de iniciar sessão para a VM. Terá de fornecer credenciais para o início de sessão. Consulte [como consigo iniciar sessão na simulação VM?](#how-do-i-sign-in-to-the-simulation-vm) para encontrar as credenciais.

O script adiciona/remove um endereço IP público à VM, se ainda não tem um e remove-o. O script coloca todos os ficheiros de registo no arquivo e as transferências de arquivo para a estação de trabalho de desenvolvimento.

Em alternativa, inicie sessão na VM através de SSH e Inspecione os ficheiros de registo em tempo de execução.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Como verificar se a simulação está a enviar dados para a nuvem?

Com o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [iothub explorer](https://github.com/azure/iothub-explorer) ferramenta, pode inspecionar os dados enviados ao IoT Hub do determinados dispositivos. Para utilizar estas ferramentas, terá de saber a cadeia de ligação para o IoT hub na sua implementação. Consulte [como posso saber se a cadeia de ligação do hub IoT utilizado pela minha solução?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Inspecione os dados enviados por um dos dispositivos publicador:

* Publisher.Beijing.corp.contoso
* Publisher.capetown.corp.contoso
* Publisher.Mumbai.corp.contoso
* Publisher.munich0.corp.contoso
* Publisher.rio.corp.contoso
* Publisher.Seattle.corp.contoso

Se vir sem dados enviados ao IoT Hub, não há um problema com a simulação. Como primeiro passo analysis deve analisar os ficheiros de registo dos componentes simulação. Consulte [como posso obter dados de registo dos componentes simulação?](#how-can-i-get-log-data-from-the-simulation-components) Em seguida, tente parar e iniciar a simulação e se ainda não existe nenhum dados enviados, atualize a simulação completamente. Consulte [como atualizar a simulação na VM?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

* [Descrição geral de solução pré-configurada de manutenção preditiva](iot-suite-predictive-overview.md)
* [Descrição geral da solução de fábrica ligado pré-configurada](iot-suite-connected-factory-overview.md)
* [Segurança de IoT a partir do zero](securing-iot-ground-up.md)