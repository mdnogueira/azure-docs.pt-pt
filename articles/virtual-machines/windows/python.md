---
title: Criar e gerir uma VM do Windows no Azure com o Python | Microsoft Docs
description: Saiba como utilizar o Python para criar e gerir uma VM do Windows no Azure.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: davidmu
ms.openlocfilehash: bb777d41570d7b1dc97402d532519488912948e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Criar e gerir VMs do Windows no Azure com o Python

Um [Máquina Virtual do Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) tem vários recursos do Azure de suporte. Este artigo abrange a criar, gerir e eliminar recursos da VM com o Python. Saiba como:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio
> * Instalar pacotes
> * Criar as credenciais
> * Criar recursos
> * Executar tarefas de gestão
> * Eliminar recursos
> * Executar a aplicação

Demora cerca de 20 minutos para executar estes passos.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Se ainda não o fez, instale [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecione **desenvolvimento do Python** na página de cargas de trabalho e, em seguida, clique em **instalar**. Em resumo, pode ver que **Python 3 64-bit (3.6.0)** é selecionado automaticamente para si. Se já tiver instalado o Visual Studio, pode adicionar a carga de trabalho do Python com o Iniciador do Studio Visual.
2. Depois de instalar e iniciar o Visual Studio, clique em **ficheiro** > **novo** > **projeto**.
3. Clique em **modelos** > **Python** > **aplicação Python**, introduza *myPythonProject* para o nome do o projeto, selecione a localização do projeto e, em seguida, clique em **OK**.

## <a name="install-packages"></a>Instalar pacotes

1. No Explorador de soluções, em *myPythonProject*, faça duplo clique **ambientes do Python**e, em seguida, selecione **ambiente virtual adicionar**.
2. No ecrã Adicionar ambiente Virtual, aceite o nome predefinido do *env*, certifique-se de que *Python 3.6 (64-bit)* está selecionado para o interpretador base e, em seguida, clique em **criar** .
3. Clique com botão direito do *env* ambiente que criou, clique em **Instalar pacote do Python**, introduza *azure* na caixa de pesquisa e, em seguida, prima Enter.

Deverá ver nas janelas do resultado que os pacotes do azure foram instalados com êxito. 

## <a name="create-credentials"></a>Criar as credenciais

Antes de começar este passo, certifique-se de que tem um [principal de serviço do Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Também deve registar o ID da aplicação, a chave de autenticação e o ID do inquilino que precisa num passo posterior.

1. Abra *myPythonProject.py* ficheiro que foi criado e, em seguida, adicione este código para ativar a sua aplicação para ser executada:

    ```python
    if __name__ == "__main__":
    ```

2. Para importar o código que é necessário, adicione estas instruções na parte superior do ficheiro. PY:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Em seguida no ficheiro. PY, adicione as variáveis após as declarações de importação para especificar os valores comuns utilizados no código:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Substitua **id de subscrição** com o identificador de subscrição.

4. Para criar as credenciais do Active Directory que necessita para fazer pedidos, adicione esta função após as variáveis no ficheiro. PY:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Substitua **id da aplicação**, **chave de autenticação**, e **id do inquilino** com os valores que reuniu anteriormente quando criou o seu serviço do Azure Active Directory principal.

5. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Criar recursos
 
### <a name="initialize-management-clients"></a>Inicializar os clientes de gestão

Os clientes de gestão são necessárias para criar e gerir recursos com o SDK Python no Azure. Para criar os clientes de gestão, adicione este código sob o **se** instrução no fim, em seguida, do ficheiro. PY:

```python
resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Criar a VM e recursos de suporte

Todos os recursos tem de estar contidos num [grupo de recursos](../../azure-resource-manager/resource-group-overview.md).

1. Para criar um grupo de recursos, adicione esta função após as variáveis no ficheiro. PY:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Conjuntos de disponibilidade](tutorial-availability-sets.md) tornar mais fácil para si manter as máquinas virtuais utilizadas pela sua aplicação.

1. Para criar um conjunto de disponibilidade, adicione esta função após as variáveis no ficheiro. PY:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

A [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) é necessária para comunicar com a máquina virtual.

1. Para criar um endereço IP público para a máquina virtual, adicione esta função após as variáveis no ficheiro. PY:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Uma máquina virtual tem de ser uma sub-rede de um [rede Virtual](../../virtual-network/virtual-networks-overview.md).

1. Para criar uma rede virtual, adicione esta função após as variáveis no ficheiro. PY:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Para adicionar uma sub-rede para a rede virtual, adicione esta função após as variáveis no ficheiro. PY:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Uma máquina virtual necessita de uma interface de rede para comunicar na rede virtual.

1. Para criar uma interface de rede, adicione esta função após as variáveis no ficheiro. PY:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Agora que criou todos os recursos de suporte, pode criar uma máquina virtual.

1. Para criar a máquina virtual, adicione esta função após as variáveis no ficheiro. PY:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Este tutorial cria uma máquina virtual com uma versão do sistema operativo Windows Server. Para obter mais informações sobre como selecionar outras imagens, consulte [navegar e selecionar imagens da máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Executar tarefas de gestão

Durante o ciclo de vida de uma máquina virtual, poderá querer executar tarefas de gestão, tais como iniciar, parar ou eliminar uma máquina virtual. Além disso, pode querer criar código para automatizar tarefas repetitivas ou complexas.

### <a name="get-information-about-the-vm"></a>Obter informações sobre a VM

1. Para obter informações sobre a máquina virtual, adicione esta função após as variáveis no ficheiro. PY:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Parar a VM

Pode parar uma máquina virtual e manter todas as respetivas definições, mas continuam a ser-lhe cobrados-lo, ou pode parar uma máquina virtual e desalocá-lo. Quando uma máquina virtual está a ser desalocada, todos os recursos associados à mesma também são extremidades desalocadas e faturação para o mesmo.

1. Para parar a máquina virtual sem Desalocação-lo, adicione esta função após as variáveis no ficheiro. PY:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Se pretender desalocar a máquina virtual, altere a chamada de power_off para este código:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Iniciar a VM

1. Para iniciar a máquina virtual, adicione esta função após as variáveis no ficheiro. PY:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Redimensionar a VM

Muitos aspetos de implementação devem ser considerados ao decidir sobre um tamanho para a máquina virtual. Para obter mais informações, consulte [tamanhos de VM](sizes.md).

1. Para alterar o tamanho da máquina virtual, adicione esta função após as variáveis no ficheiro. PY:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Adicionar um disco de dados para a VM

Máquinas virtuais podem ter um ou mais [discos de dados](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) que são armazenadas como VHDs.

1. Para adicionar um disco de dados para a máquina virtual, adicione esta função após as variáveis no ficheiro. PY: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Eliminar recursos

Porque os lhe é cobrados os recursos utilizados no Azure, é sempre boa prática para eliminar os recursos que já não são necessárias. Se pretender eliminar as máquinas virtuais e todos os recursos de suporte, tudo o que precisa de executar é eliminar o grupo de recursos.

1. Para eliminar o grupo de recursos e todos os recursos, adicione esta função após as variáveis no ficheiro. PY:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Para chamar a função que adicionou anteriormente, adicione este código sob o **se** instrução no fim do ficheiro. PY:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Guardar *myPythonProject.py*.

## <a name="run-the-application"></a>Executar a aplicação

1. Para executar a aplicação de consola, clique em **iniciar** no Visual Studio.

2. Prima **Enter** depois do Estado de cada recurso é devolvido. As informações de estado, deverá ver uma **com êxito** estado de aprovisionamento. Depois de criar a máquina virtual, terá a oportunidade para eliminar todos os recursos que criar. Antes de premir **Enter** para iniciar a eliminação de recursos, pode demorar alguns minutos para verificar a criação dos mesmos no portal do Azure. Se tiver o portal do Azure, abrir, poderá ter de atualizar o painel para ver os novos recursos.  

    Deve demorar cerca de cinco minutos para esta aplicação de consola executar totalmente do início seja concluída. Pode demorar alguns minutos depois da aplicação foi concluída antes de todos os recursos e o grupo de recursos são eliminados.


## <a name="next-steps"></a>Passos seguintes

- Se ocorreram problemas com a implementação, um passo seguinte será ver [Troubleshooting resource group deployments with Azure portal (Resolução de problemas com implementações do grupo de recursos com o portal do Azure)](../../resource-manager-troubleshoot-deployments-portal.md)
- Saiba mais sobre o [biblioteca Python do Azure](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

