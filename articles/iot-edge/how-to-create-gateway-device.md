---
title: Criar um dispositivo de gateway com o Azure IoT Edge | Microsoft Docs
description: "Utilize o limite de IoT do Azure para criar um dispositivo de gateway que pode processar as informações de vários dispositivos"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e1337ddf5ed84a06a62e2faa198f3e8fb49bc3bd
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-iot-edge-gateway-device-to-process-data-from-other-iot-devices---preview"></a>Criar um dispositivo de gateway do limite de IoT para processar dados de outros dispositivos de IoT – pré-visualização

Existem duas formas de utilizar dispositivos de IoT Edge como gateways:

* Ligar a jusante dispositivos que utilizam [dispositivos IoT do Azure SDK][lnk-devicesdk];
* Ligue os dispositivos que não utilizam o protocolo IoT Hub.

Quando utiliza um dispositivo de limite de IoT como um gateway de obter as seguintes vantagens:

* **Ligação de multiplexação**. Todos os dispositivos a ligação ao IoT Hub através de um dispositivo de limite de IoT irão utilizar a mesma ligação subjacente.
* **Tráfego suavização**. O dispositivo de limite de IoT irão implementar automaticamente término exponencial em caso de limitação, enquanto as mensagens localmente a persistência do IoT Hub. Isto faz com que a solução resiliente para picos de tráfego.
* **Suporte offline limitado**. O dispositivo de gateway irá armazenar localmente as mensagens que não não possível entregar ao IoT Hub.

Neste artigo que iremos chamar *gateway de IoT* um dispositivo de limite de IoT utilizado como um gateway.

>[!NOTE]
>Atualmente:
> * Dispositivos a jusante não são capazes de autenticar com o gateway se o gateway está desligado do IoT Hub; e
> * Não é possível ligar dispositivos de limite de IoT para gateways de periferia de IoT.

## <a name="use-the-azure-iot-device-sdk"></a>Utilizar o dispositivo IoT do Azure SDK

O hub de limite que está instalado em todos os dispositivos de IoT Edge expõe as primitivas seguintes para dispositivos a jusante:

* mensagens do dispositivo para nuvem e da nuvem para o dispositivo;
* métodos diretos; e
* operações do dispositivo duplo.

>[!NOTE]
>Atualmente, os dispositivos a jusante não são capazes de utilizar o carregamento de ficheiros ao ligar através de um gateway de IoT.

Quando ligar dispositivos a um gateway de IoT Edge utilizando o SDK de dispositivo do IoT do Azure, tem de:

* Configurar o dispositivo a jusante com uma cadeia de ligação que faz referência ao nome do anfitrião do dispositivo de gateway; e
* Certifique-se de que o dispositivo a jusante confianças o certificado utilizado para aceitar a ligação ao dispositivo de gateway.

Quando instala o tempo de execução de limite de IoT do Azure utilizando o script de controlo, é criar um certificado para o hub de limite, como fez no tutorial instalar IoT limite num dispositivo simulado no [Windows] [ lnk-tutorial1-win] e [Linux][lnk-tutorial1-lin]. Este certificado é utilizado pelo Edge hub para aceitar ligações a receber TLS e tem de ser fidedigno para o dispositivo a jusante ao ligar ao dispositivo de gateway.

Pode criar qualquer infraestrutura de certificados que permite a fidedignidade necessária para a topologia de gateway do dispositivo. Neste artigo, partimos do pressuposto a mesmo certificado a configuração que pretende utilizar para ativar [segurança de AC de x. 509] [ lnk-iothub-x509] no IoT Hub, que envolve um certificado de AC de x. 509 associado a um IoT hub específico (o  *Proprietário do hub IoT AC*) e uma série de certificados, assinada com esta AC, instalado nos dispositivos de limite de IoT.

>[!IMPORTANT]
>Atualmente, os dispositivos de IoT Edge e a jusante só pode utilizar [SAS tokens] [ lnk-iothub-tokens] para autenticar com o IoT Hub. Os certificados serão utilizados apenas para validar a ligação de TLS entre o dispositivo de gateway e de folha.

A configuração utiliza **proprietário do hub IoT AC** como:
* um certificado de assinatura para o programa de configuração do runtime IoT Edge em todos os dispositivos de IoT Edge e como
* um certificado de chave pública instalado nos dispositivos a jusante.

Este procedimento resultará numa solução que permite que todos os dispositivos utilizar qualquer dispositivo de limite de IoT como um gateway, desde que estão ligados ao mesmo IoT hub.

### <a name="create-the-certificates-for-test-scenarios"></a>Criar os certificados para cenários de teste

Pode utilizar o exemplo do Powershell e scripts de Bash descritas [gerir exemplo de certificado de AC] [ lnk-ca-scripts] para gerar um autoassinado **proprietário do hub IoT AC** e certificados de dispositivo sessão iniciada com o mesmo.

1. Siga o passo 1 de [gerir exemplo de certificado de AC] [ lnk-ca-scripts] para instalar os scripts.
2. Siga o passo 2 para gerar o **proprietário do hub IoT AC**, este ficheiro será utilizado pelos dispositivos a jusante para validar a ligação.

Utilize as seguintes instruções para gerar um certificado para o seu dispositivo de gateway.

#### <a name="bash"></a>Bash

* Executar `./certGen.sh create_edge_device_certificate myGateway` para criar o novo certificado de dispositivo.  
  Esta ação irá criar o.\certs\new-edge-device.* ficheiros que contêm a chave pública e o PFX e a.\private\new-edge-device.key.pem que contém a chave privada do dispositivo.  
* `cat new-edge-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-edge-device-full-chain.cert.pem`Para obter a chave pública.
* `./private/new-edge-device.cert.pem`contém a chave privada do dispositivo.

#### <a name="powershell"></a>PowerShell

* Executar `New-CACertsEdgeDevice myGateway` para criar o novo certificado de dispositivo.
  Esta ação irá criar ficheiros myEdgeDevice * que contém a chave pública, a chave privada e o PFX deste certificado.  Quando lhe for pedido para introduzir uma palavra-passe durante o processo de assinatura, introduza "1234".


>[!IMPORTANT]
>Este exemplo destinam-se apenas a fins de teste. Para cenários de produção, consulte [proteger a implementação de IoT] [ lnk-iothub-secure-deployment] para as Azure IoT diretrizes sobre como proteger a sua solução de IoT e aprovisionar o certificado em conformidade.

### <a name="configure-an-iot-edge-device-as-a-gateway"></a>Configurar um dispositivo de limite de IoT como um gateway

Para configurar o seu dispositivo IoT Edge como um gateway apenas terá de configurar para utilizar o certificado de dispositivo que criou na secção anterior.

Partimos do pressuposto os seguintes nomes de ficheiro dos scripts de exemplo acima:

| Saída | Scripts de bash | PowerShell |
| ------ | ----------- | ---------- |
| Certificado do dispositivo | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Chave privada do dispositivo | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Cadeia de certificados de dispositivo | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| Proprietário do hub IoT AC | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 Analogously para a instalação descrita no implementar o Azure IoT Edge num dispositivo simulado [Windows] [ lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin], terá de Forneça as informações acima para o tempo de execução do limite de IoT. 
 
 No Linux:

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

No Windows:

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

Por predefinição os scripts de exemplo não defina uma frase de acesso para a chave privada do dispositivo. Se definir uma frase de acesso, adicione o seguinte parâmetro:

        --device-ca-passphrase {passphrase}

O script solicitará para definir uma frase de acesso para o certificado de agente de limite.
Tem de reiniciar o tempo de execução do limite de IoT após este comando.

### <a name="configure-an-iot-hub-device-application-as-a-downstream-device"></a>Configurar uma aplicação de dispositivos do IoT Hub como um dispositivo a jusante

Um dispositivo a jusante pode ser qualquer aplicação utilizando o [dispositivos IoT do Azure SDK][lnk-devicesdk], tal como aquele simple descrito em [ligar o seu dispositivo ao seu IoT hub através do .NET] [ lnk-iothub-getstarted].

Em primeiro lugar, tem de confiança de uma aplicação de dispositivo a jusante a **proprietário do hub IoT AC** certificado para validar as ligações de TLS para os dispositivos de gateway. Normalmente, é possível executar este passo de duas formas: ao nível do SO ou (para alguns idiomas) ao nível da aplicação.

Por exemplo, para aplicações de .NET, pode adicionar o seguinte fragmento de um certificado no formato PEM armazenado no caminho de confiança `certPath`.

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

Tenha em atenção que os scripts de exemplo referenciados acima gera a chave pública no ficheiro `certs/azure-iot-test-only.root.ca.cert.pem` (Bash), ou `RootCA.pem` (Powershell).

Executar este passo ao nível do SO é diferente entre o Windows e entre as distribuições do Linux.

O segundo passo é ao inicializar o sdk do dispositivo IoT Hub com uma cadeia de ligação que faça referência a nome de anfitrião do dispositivo de gateway.
Isto é feito, acrescentando o `GatewayHostName` propriedade para a cadeia de ligação do dispositivo. Por exemplo, aqui está uma cadeia de ligação do dispositivo de exemplo para um dispositivo, é acrescentado a `GatewayHostName` propriedade:

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

Estes dois passos irão ativar a sua aplicação de dispositivo ligar ao dispositivo de gateway.

## <a name="use-other-protocols"></a>Utilizar outros protocolos

Uma das funções principais de um dispositivo de gateway é interpretar os protocolos de dispositivos a jusante. Para ligar dispositivos que não utilizam o protocolo IoT Hub, pode desenvolver um módulo de limite de IoT que executa este tradução e liga-se em nome do dispositivo ao IoT Hub a jusante.

Pode optar por criar um *transparente* ou *opaco* gateway:

| &nbsp; | Gateway transparente | Gateway opaco|
|--------|-------------|--------|
| Identidades que estão armazenadas no registo de identidade do IoT Hub | Identidades de todos os dispositivos ligados | Apenas a identidade de dispositivo de gateway |
| Dispositivo duplo | Cada dispositivo ligado tem o seu próprio dispositivo duplo | Apenas o gateway tem um módulo e dispositivos duplos |
| Métodos Direct e mensagens da nuvem para dispositivo | A nuvem pode resolver individualmente cada dispositivo ligado | Nuvem apenas pode resolver o dispositivo de gateway |
| [Limitações do IoT Hub e quotas][lnk-iothub-throttles-quotas] | Aplicar a cada dispositivo | Se aplicam ao dispositivo de gateway |

Quando utiliza um padrão de gateway opaco, todos os dispositivos que se ligam através desse gateway partilham a mesma fila de nuvem para o dispositivo, que pode conter no máximo 50 mensagens. Segue-se que o padrão de gateway opaco deve ser utilizado apenas quando muito alguns dispositivos se ligam através de cada gateway de campo e os respetivos tráfego de nuvem para o dispositivo é reduzido.

Quando implementar um gateway opaco, o módulo de tradução do protocolo utiliza a cadeia de ligação do módulo.

Quando implementar um gateway transparente, o módulo cria várias instâncias do cliente do dispositivo IoT Hub, utilizando as cadeias de ligação para os dispositivos a jusante.

## <a name="next-steps"></a>Passos seguintes

- [Compreender os requisitos e ferramentas para o desenvolvimento de módulos de limite de IoT][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/CACertToolEdge/tools/CACertificates/CACertificateOverview.md