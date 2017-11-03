---
title: "Como utilizar a API (Python) - guia de funcionalidades de gestão de serviço"
description: "Saiba como programaticamente efetuar tarefas de gestão comuns do serviço do Python."
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 13249ba9a4b317a3154776b411ce0bb1f316b3bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-management-from-python"></a>Como utilizar o serviço de gestão do Python
Este guia mostra como programaticamente efetuar tarefas de gestão comuns do serviço do Python. O **ServiceManagementService** classe no [Azure SDK para Python](https://github.com/Azure/azure-sdk-for-python) suporta acesso programático para muito a funcionalidade relacionadas com a gestão do serviço que está disponível no [portal clássico do Azure] [ management-portal] (tais como **criar, atualizar e eliminar serviços em nuvem, implementações, os serviços de gestão de dados e as máquinas virtuais**). Esta funcionalidade pode ser útil na criação de aplicações que necessitam de acesso programático para gestão de serviço.

## <a name="WhatIs"></a>Que é o serviço de gestão
A API de gestão de serviço fornece acesso programático para muito a funcionalidade de gestão do serviço, disponível através de [portal clássico do Azure][management-portal]. O Azure SDK para Python permite-lhe gerir os seus serviços em nuvem e as contas de armazenamento.

Para utilizar a API de gestão de serviço, terá de [criar uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"></a>Conceitos
O Azure SDK para Python encapsula num wrapper a [API de gestão de serviços do Azure][svc-mgmt-rest-api], que é uma API REST. Todas as operações API são executadas sobre SSL e mutuamente autenticadas utilizando certificados X.509 v3. É possível aceder ao serviço de gestão a partir de um serviço em execução no Azure ou diretamente através da Internet a partir de qualquer aplicação que possa enviar um pedido HTTPS e receber uma resposta HTTPS.

## <a name="Installation"></a>Instalação
Todas as funcionalidades descritas neste artigo estão disponíveis no `azure-servicemanagement-legacy` pacote, o que pode instalar através do pip. Para obter mais informações sobre a instalação (por exemplo, se estiver familiarizado com o Python), consulte este artigo: [instalar o Python e o Azure SDK](../python-how-to-install.md)

## <a name="Connect"></a>Como: ligar à gestão de serviço
Para ligar ao ponto final do serviço de gestão, é necessário o ID de subscrição do Azure e um certificado de gestão válido. Pode obter o ID de subscrição através de [portal clássico do Azure][management-portal].

> [!NOTE]
> Agora é possível utilizar certificados criados com OpenSSL quando em execução no Windows.  Requer o Python 2.7.4 ou posterior. Recomendamos que os utilizadores utilizem OpenSSL em vez de. pfx, desde o suporte de. pfx certificados provavelmente serão removidos no futuro.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de gestão no Mac/Windows/Linux (OpenSSL)
Pode utilizar [OpenSSL](http://www.openssl.org/) para criar o certificado de gestão.  Na realidade, tem de criar dois certificados, um para o servidor (um `.cer` ficheiro) e outra para o cliente (uma `.pem` ficheiro). Para criar o `.pem` de ficheiros, execute:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Para criar o `.cer` de certificado, execute:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Para obter mais informações sobre os certificados do Azure, consulte [descrição geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros de OpenSSL, consulte a documentação em [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Depois de criar estes ficheiros, terá de carregar o `.cer` ficheiro ao Azure através da ação "Carregar" de separador "Definições" o [portal clássico do Azure][management-portal], e terá de tome nota do local onde guardada a `.pem` ficheiro.

Após obter o ID de subscrição, criou um certificado e carregado o `.cer` ficheiro para o Azure, pode ligar ao ponto final de gestão do Azure através da transmissão do id de subscrição e o caminho para o `.pem` do ficheiro para **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um **ServiceManagementService** objeto. O **ServiceManagementService** classe é a classe principal utilizada para gerir os serviços do Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gestão no Windows (MakeCert)
Pode criar um certificado de gestão autoassinado na sua máquina utilizando `makecert.exe`.  Abra um **linha de comandos do Visual Studio** como um **administrador** e utilize o seguinte comando, substituindo *AzureCertificate* com o nome do certificado que pretende utilizar.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

O comando cria o `.cer` de ficheiros e instala-na **pessoais** arquivo de certificados. Para obter mais informações, consulte [descrição geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md).

Depois de ter criado o certificado, terá de carregar o `.cer` ficheiro ao Azure através da ação "Carregar" de separador "Definições" o [portal clássico do Azure][management-portal].

Após obter o ID de subscrição, criou um certificado e carregado o `.cer` ficheiro para o Azure, pode ligar ao ponto final de gestão do Azure através da transmissão do id de subscrição e a localização do certificado no seu **pessoal** arquivo de certificados para **ServiceManagementService** (novamente, substitua *AzureCertificate* com o nome do seu certificado):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um **ServiceManagementService** objeto. O **ServiceManagementService** classe é a classe principal utilizada para gerir os serviços do Azure.

## <a name="ListAvailableLocations"></a>Como: lista de localizações disponíveis
Para listar as localizações que estão disponíveis para o alojamento de serviços, utilize o **lista\_localizações** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Quando cria um serviço em nuvem ou o serviço de armazenamento tem de fornecer uma localização válida. O **lista\_localizações** método sempre devolve uma lista atualizada das localizações atualmente disponíveis. A partir desta redação, as localizações disponíveis são:

* Europa Ocidental
* Europa do Norte
* Sudeste Asiático
* Ásia Oriental
* EUA Central
* EUA Centro-Norte
* E.U.A. Centro-Sul
* EUA Oeste
* EUA Leste
* Leste do Japão
* Oeste do Japão
* Sul do Brasil
* Leste da Austrália
* Sudeste da Austrália

## <a name="CreateCloudService"></a>Como: criar um serviço em nuvem
Quando cria uma aplicação e executá-lo no Azure, o código e a configuração em conjunto são denominados um Azure [serviço em nuvem] [ cloud service] (conhecido como um *serviço alojado* em sistemas operativos mais recentes do Azure). O **criar\_alojado\_serviço** método permite-lhe criar um novo serviço alojado, fornecendo um nome de serviço alojado (que tem de ser exclusivo no Azure), uma etiqueta (automaticamente com codificação base64), uma descrição e uma localização.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Pode listar todos os serviços alojados para a sua subscrição com o **lista\_alojado\_serviços** método:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Se pretender obter informações sobre um determinado serviço alojado, pode fazê-transferindo o nome do serviço alojado para o **obter\_alojado\_serviço\_propriedades** método:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Depois de criar um serviço em nuvem, pode implementar o código para o serviço com o **criar\_implementação** método.

## <a name="DeleteCloudService"></a>Como: eliminar um serviço em nuvem
Pode eliminar um serviço em nuvem transferindo o nome do serviço para o **eliminar\_alojado\_serviço** método:

    sms.delete_hosted_service('myhostedservice')

Antes de poder eliminar um serviço, tem de ser apagadas todas as implementações para o serviço. (Consulte [como: eliminar uma implementação](#DeleteDeployment) para obter mais informações.)

## <a name="DeleteDeployment"></a>Como: eliminar uma implementação
Para eliminar uma implementação, utilize o **eliminar\_implementação** método. O exemplo seguinte mostra como eliminar uma implementação designada `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"></a>Como: criar um serviço de armazenamento
A [serviço de armazenamento](../storage/common/storage-create-storage-account.md) dá-lhe acesso ao Azure [Blobs](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabelas](../cosmos-db/table-storage-how-to-use-python.md), e [filas](../storage/queues/storage-python-how-to-use-queue-storage.md). Para criar um serviço de armazenamento, é necessário um nome para o serviço (entre 3 e 24 carateres em minúsculas e exclusivo no Azure), uma descrição, uma etiqueta (máximo de 100 carateres, codificados automaticamente para base64) e uma localização. O exemplo seguinte mostra como criar um serviço de armazenamento, especificando uma localização.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Tenha em atenção, no exemplo anterior que o estado do **criar\_armazenamento\_conta** operação pode ser obtida ao transferir o resultado devolvido pelo **criar\_armazenamento\_conta** para o **obter\_operação\_estado** método.  

Pode listar as contas de armazenamento e as respetivas propriedades com o **lista\_armazenamento\_contas** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"></a>Como: eliminar um serviço de armazenamento
Pode eliminar um serviço de armazenamento transferindo o nome do serviço de armazenamento para o **eliminar\_armazenamento\_conta** método. Eliminar um serviço de armazenamento elimina todos os dados armazenados no serviço (blobs, tabelas e filas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"></a>Como: lista de sistemas operativos disponíveis
Para listar os sistemas operativos que estão disponíveis para o alojamento de serviços, utilize o **lista\_operativo\_sistemas** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Em alternativa, pode utilizar o **lista\_operativo\_sistema\_famílias** método, o que os sistemas operativos por família de grupos:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"></a>Como: criar uma imagem de sistema operativo
Para adicionar uma imagem do sistema operativo para o repositório de imagens, utilize o **adicionar\_SO\_imagem** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Para listar as imagens de sistema operativo que estão disponíveis, utilize o **lista\_SO\_imagens** método. Inclui todas as imagens de plataforma e imagens do utilizador:

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"></a>Como: eliminar uma imagem do sistema operativo
Para eliminar uma imagem do utilizador, utilize o **eliminar\_SO\_imagem** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"></a>Como: criar uma máquina virtual
Para criar uma máquina virtual, terá primeiro de criar um [serviço em nuvem](#CreateCloudService).  Em seguida, criar a implementação de máquina virtual utilizando o **criar\_virtual\_máquina\_implementação** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"></a>Como: eliminar uma máquina virtual
Para eliminar uma máquina virtual, tem primeiro de eliminar a implementação utilizando o **eliminar\_implementação** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

O serviço em nuvem, em seguida, pode ser eliminado utilizando o **eliminar\_alojado\_serviço** método:

    sms.delete_hosted_service(service_name='myvm')

## <a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Como: Criar uma Máquina Virtual a partir de uma imagem capturada Máquina Virtual
Para capturar uma imagem de VM, tem primeiro de chamar o **capturar\_vm\_imagem** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Em seguida, para se certificar de que tem capturada com êxito a imagem, utilize o **lista\_vm\_imagens** api e certifique-se a imagem é apresentada nos resultados:

    images = sms.list_vm_images()

Por fim, criar a máquina virtual utilizando a imagem capturada, utilize o **criar\_virtual\_máquina\_implementação** método como anteriormente, mas, desta vez passar o vm_image_name em vez disso,

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Para obter mais informações sobre como capturar uma Máquina Virtual Linux, consulte [como capturar uma Máquina Virtual Linux.](../virtual-machines/linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

Para obter mais informações sobre como capturar uma Máquina Virtual do Windows, consulte [como capturar uma Máquina Virtual do Windows.](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="What's Next"></a>Passos seguintes
Agora que aprendeu as noções básicas de gestão de serviço, pode aceder a [documentação de referência de API completa para o SDK Python](http://azure-sdk-for-python.readthedocs.org/) e efetuar as tarefas complexas facilmente para gerir a sua aplicação de python.

Para obter mais informações, consulte o [Centro para Programadores do Python](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/
