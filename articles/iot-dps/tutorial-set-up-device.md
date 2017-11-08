---
title: "Configurar o dispositivo para o serviço de aprovisionamento de dispositivos do Azure IoT Hub | Microsoft Docs"
description: "Configurar o dispositivo para aprovisionar através do serviço de aprovisionamento de dispositivos do IoT Hub durante o processo de fabrico de dispositivo"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7031409aa63f5d64d5bb7a1b9dcac50a97718630
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Configurar um dispositivo para aprovisionar utilizando o serviço de aprovisionamento de dispositivos do Azure IoT Hub

O tutorial anterior, aprendeu a configurar o serviço de aprovisionamento de dispositivos do Azure IoT Hub para aprovisionar automaticamente os seus dispositivos ao seu IoT hub. Este tutorial fornece orientações para configurar o seu dispositivo durante o processo de fabrico, para que pode configurar o serviço de aprovisionamento de dispositivos para o seu dispositivo com base no respetivo [módulo de segurança de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security), e o dispositivo pode ligar ao seu serviço de aprovisionamento de dispositivos quando efetua o arranque pela primeira vez. Este tutorial aborda os processos para:

> [!div class="checklist"]
> * Selecione um módulo de Hardware de segurança
> * Criar o SDK de cliente de aprovisionamento de dispositivos para o HSM selecionado
> * Extraia os artefactos de segurança
> * Definir a configuração do serviço de aprovisionamento de dispositivos no dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, crie a instância de serviço de aprovisionamento de dispositivos e um IoT hub utilizando as instruções mencionadas no tutorial [configurar nuvem para aprovisionamento de dispositivos](./tutorial-set-up-cloud.md).


## <a name="select-a-hardware-security-module"></a>Selecione um módulo de Hardware de segurança

O [SDK de cliente do serviço de aprovisionamento de dispositivos](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) fornece suporte para dois tipos de módulos de segurança de Hardware (ou HSMs): 

- [Trusted Platform Module (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module).
    - TPM é uma norma estabelecida para a maioria das plataformas de dispositivos baseados no Windows, bem como alguns dispositivos baseado no Linux/Ubuntu. Como um fabricante do dispositivo, pode escolher deste HSM, se tiver qualquer uma destas sos em execução nos seus dispositivos e, se estiver à procura de um padrão estabelecido para HSMs. Com TPM chips, apenas é possível inscrever cada dispositivo individualmente para o serviço de aprovisionamento de dispositivos. Para fins de desenvolvimento, pode utilizar o simulador TPM no seu computador de desenvolvimento do Windows ou Linux.

- [X. 509](https://cryptography.io/en/latest/x509/) com base em módulos de hardware de segurança. 
    - HSMs baseados em x. 509 são chips relativamente mais recentes, com o trabalho evoluir atualmente na Microsoft no RIoT ou REPARTIR chips que implementam os certificados x. 509. Com o x. 509 chips, pode efetuar a inscrição em massa no portal. Também suporta determinadas não - Windows sos como embedOS. Para fins de desenvolvimento, o SDK do cliente do serviço de aprovisionamento de dispositivos suporta um simulador de dispositivo de x. 509. 

Como um fabricante do dispositivo, tem de selecionar hardware segurança módulos/chips que se baseiam em qualquer um dos tipos de anteriores. Atualmente, não são suportados outros tipos de HSMs no SDK do cliente do serviço de aprovisionamento de dispositivos.   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>Criar o SDK de cliente de aprovisionamento de dispositivos para o HSM selecionado

O SDK cliente do serviço de aprovisionamento de dispositivos ajuda a implementar o mecanismo de segurança selecionados no software. Os passos seguintes mostram como utilizar o SDK para o chip HSM selecionado:

1. Se seguiu o [início rápido para criar o dispositivo simulado](./quick-create-simulated-device.md), que configurou o pronto para criar o SDK. Se não, siga os passos de quatro primeiras da secção intitulada [preparar o ambiente de desenvolvimento](./quick-create-simulated-device.md#setupdevbox). Estes passos clonar o repositório do GitHub para o SDK cliente do serviço de aprovisionamento de dispositivos, bem como instalar o `cmake` ferramenta de compilação. 

1. Crie o SDK para o tipo de HSM que selecionou para o seu dispositivo, utilizando um dos seguintes comandos na linha de comandos:
    - Para dispositivos com TPM:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm ..
        ```

    - Para simulador TPM:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm_simulator ..
        ```

    - Para dispositivos de x. 509 e simulador:
        ```cmd/sh
        cmake -Ddps_auth_type=x509 ..
        ```

1. O SDK fornece suporte predefinido para os dispositivos com Windows ou Ubuntu implementações para TPM e x. 509 HSMs. Para estes suportada HSMs, avance para a secção intitulada [extrair os artefactos de segurança](#extractsecurity) abaixo. 
 
## <a name="support-custom-tpm-and-x509-devices"></a>Suportar dispositivos TPM e x. 509 personalizados

O SDK cliente do sistema de aprovisionamento de dispositivos fornece suporte predefinido para todos TPM e x. 509 os dispositivos que não executem o Windows ou Ubuntu. Para esses dispositivos, precisa de escrever o código personalizado para o chip HSM específico, conforme mostrado nos passos seguintes:

### <a name="develop-your-custom-repository"></a>Desenvolver o seu repositório personalizado

1. Desenvolva um repositório do GitHub para aceder ao seu HSM. Este projeto tem produzir uma biblioteca estática para o SDK de aprovisionamento de dispositivos consumir.
1. A biblioteca tem de implementar as funções definidas no ficheiro de cabeçalho do seguinte: um. Para o TPM personalizado, implementar funções definidas no `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_tpm_impl.h`.
    b. Para x. 509 personalizado, implementar funções definidas no `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_x509_impl.h`. 
1. O repositório HSM também tem de conter um `CMakeLists.txt` ficheiro na raiz para o repositório que deve ser criado.

### <a name="integrate-with-the-device-provisioning-service-client"></a>Integrar com o dispositivo cliente do serviço de aprovisionamento

Depois da biblioteca com êxito baseia-se no seu próprio, pode mover o IoThub C-SDK e pull no seu repositório:

1. Forneça o repositório do GitHub de HSM personalizado, o caminho de biblioteca e o nome no comando cmake seguinte:
    ```cmd/sh
    cmake -Ddps_auth_type=<custom_hsm> -Ddps_hsm_custom_repo=<github_repo_name> -Ddps_hsm_custom_lib=<path_and_name_of library> <PATH_TO_AZURE_IOT_SDK>
    ```
   Substitua o `<custom_hsm>` neste comando com uma `tpm` ou `x509`. Este comando cria um marcador para o repositório HSM personalizado no interior do `cmake` diretório. Tenha em atenção que o HSM personalizado ainda deve ser baseado no TPM ou x. 509 mecanismos de segurança.

1. Abra o SDK no visual studio e crie-o. 

    - O processo de compilação clones o repositório personalizado e baseia-se a biblioteca.
    - O SDK irá tentar ligar-se contra o HSM personalizado definido no comando cmake.

1. Execute o `\azure-iot-sdk-c\dps_client\samples\dps_client_sample\dps_client_sample.c` exemplo para verificar se o seu HSM estiver implementado corretamente.

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>Extraia os artefactos de segurança

O passo seguinte é para extrair os artefactos de segurança para o HSM no seu dispositivo.

1. Para um dispositivo TPM, terá de saber o **chave de endossamento** associados do fabricante chip TPM. Pode derivar uma exclusivo **ID de registo** para o seu dispositivo TPM por hash a chave de endossamento. 
2. Para um dispositivo de x. 509, terá de obter os certificados emitidos para os dispositivos - certificados de entidade final individuais para inscrições de dispositivos, ao certificados de raiz para inscrições através do grupo de dispositivos.

Destes artefactos de segurança são necessários para inscrever os dispositivos para o serviço de aprovisionamento de dispositivos. O serviço de aprovisionamento, em seguida, aguarda que qualquer um destes dispositivos de arranque e estabelecer ligação com o mesmo em qualquer momento posterior no tempo. Consulte [como gerir inscrições de dispositivos](how-to-manage-enrollments.md) para obter informações sobre a utilização destes artefactos de segurança para criar as inscrições. 

Quando inicia o seu dispositivo pela primeira vez, o cliente SDK interage com o chip para extrair os artefactos de segurança do dispositivo e verifica o registo com o serviço de aprovisionamento de dispositivos. 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Definir a configuração do serviço de aprovisionamento de dispositivos no dispositivo

O último passo no processo de fabrico dispositivo é escrever uma aplicação que utiliza o SDK do cliente do serviço de aprovisionamento de dispositivos para registar o dispositivo com o serviço. Este SDK fornece as seguintes APIs para as suas aplicações a utilizar:

```C
typedef void(*DPS_REGISTER_DEVICE_CALLBACK)(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* user_context); // Callback to notify user of device registration results.
DPS_CLIENT_LL_HANDLE DPS_Client_LL_Create (const char* dps_uri, const char* scope_id, DPS_TRANSPORT_PROVIDER_FUNCTION protocol, DPS_CLIENT_ON_ERROR_CALLBACK on_error_callback, void* user_ctx); // Creates the IOTHUB_DPS_LL_HANDLE to be used in subsequent calls.
void DPS_Client_LL_Destroy(DPS_CLIENT_LL_HANDLE handle); // Frees any resources created by the IoTHub Device Provisioning Service module.
DPS_RESULT DPS_LL_Register_Device(DPS_LL_HANDLE handle, DPS_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, DPS_CLIENT_REGISTER_STATUS_CALLBACK status_cb, void* status_ctx); // Registers a device that has been previously registered with Device Provisioning Service
void DPS_Client_LL_DoWork(DPS_LL_HANDLE handle); // Processes the communications with the Device Provisioning Service and calls any user callbacks that are required.
```

Não se esqueça de inicializar as variáveis `dps_uri` e `dps_scope_id` tal como mencionado no [simular primeiro sequência de arranque para a secção de dispositivo deste início rápido](./quick-create-simulated-device.md#firstbootsequence), antes de os utilizar. O registo de aprovisionamento de dispositivos cliente API `DPS_Client_LL_Create` estabelece ligação com o serviço de aprovisionamento de dispositivos global. O *ID âmbito* é gerado pelo serviço e garantias de exclusividade. É imutável e utilizado para identificar exclusivamente o IDs de registo. O `iothub_uri` permite que o registo de cliente do IoT Hub API `IoTHubClient_LL_CreateFromDeviceAuth` para estabelecer ligação com o IoT hub à direita. 


Estas APIs ajudam o seu dispositivo para ligar e registar com o serviço de aprovisionamento de dispositivos quando arrancarem, obter informações sobre o seu IoT hub e, em seguida, ligar ao mesmo. O ficheiro `dps_client/samples/dps_client_sample/dps_client_sample.c` mostra como utilizar estas APIs. Em geral, terá de criar a seguinte estrutura para o registo de cliente:

```C
static const char* dps_uri = "global.azure-devices-provisioning.net";
static const char* dps_scope_id = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...    
    security_device_init(); // initialize your HSM 

    DPS_CLIENT_LL_HANDLE handle = DPS_Client_LL_Create(dps_uri, dps_scope_id, dps_transport, on_dps_error_callback, &user_info); // Create your DPS client

    if (DPS_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
            // The dps_register_callback is called when registration is complete or fails
            DPS_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    DPS_Client_LL_Destroy(handle); // Clean up the DPS client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

Pode refinar a aplicação de registo de cliente do serviço de aprovisionamento de dispositivos através de um dispositivo simulado em primeiro lugar, utilize uma configuração de serviço de teste. Assim que a aplicação está a funcionar no ambiente de teste, pode criá-lo para o seu dispositivo específico e copie o ficheiro executável para a imagem do dispositivo. Não iniciar o dispositivo ainda, terá de [inscrever o dispositivo com o serviço de aprovisionamento de dispositivos](./tutorial-provision-device-to-hub.md#enrolldevice) antes de iniciar o dispositivo. Consulte os passos abaixo para obter este processo. 

## <a name="clean-up-resources"></a>Limpar recursos

Neste momento, pode configurar os serviços de aprovisionamento de dispositivos e de IoT Hub no portal. Se pretender abandonar o programa de configuração de aprovisionamento de dispositivo e/ou atrasar utilizando qualquer um destes serviços, recomendamos a encerrá-las para evitar incorrer em custos desnecessários.

1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu Serviço Aprovisionamento de Dispositivos. Na parte superior do painel **Todos os recursos**, clique em **Eliminar**.  
1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Na parte superior do painel **Todos os recursos**, clique em **Eliminar**.  


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Selecione um módulo de Hardware de segurança
> * Criar o SDK de cliente de aprovisionamento de dispositivos para o HSM selecionado
> * Extraia os artefactos de segurança
> * Definir a configuração do serviço de aprovisionamento de dispositivos no dispositivo

Avançar para o próximo tutorial para saber como aprovisionar o seu IoT hub do dispositivo ao inscrevê-lo para o serviço do Azure IoT Hub dispositivo aprovisionamento para o aprovisionamento de automática.

> [!div class="nextstepaction"]
> [Aprovisionar o dispositivo ao seu IoT hub](tutorial-provision-device-to-hub.md)

