---
title: Utilize Ansible para criar uma VM com Linux completa no Azure | Microsoft Docs
description: "Saiba como utilizar Ansible para criar e gerir um ambiente de máquina virtual completado do Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: 8f0e2fff8ea32874729cf9c4645d547df2449089
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Criar um ambiente de máquina virtual completado do Linux no Azure com Ansible
Ansible permite-lhe automatizar a implementação e configuração de recursos no seu ambiente. Pode utilizar Ansible para gerir as máquinas virtuais (VMs) no Azure, os mesmos como faria com qualquer outro recurso. Este artigo mostra como criar um ambiente de Linux completado e de recursos com Ansible de suporte. Também pode aprender como [criar uma VM básica com Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Pré-requisitos
Para gerir recursos do Azure com Ansible, precisa do seguinte:

- Ansible e os módulos de SDK Python do Azure instalados no sistema anfitrião.
    - Instalar Ansible [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73), e [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Credenciais do Azure e Ansible configurado para utilizá-los.
    - [Criar as credenciais do Azure e configurar Ansible](ansible-install-configure.md#create-azure-credentials)
- CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. 
    - Se precisar de atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). Também pode utilizar [nuvem Shell](/azure/cloud-shell/quickstart) partir do seu browser.


## <a name="create-virtual-network"></a>Criar a rede virtual
A secção seguinte um manual de comunicação social Ansible cria uma rede virtual denominada *myVnet* no *10.0.0.0/16* espaço de endereços:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

Para adicionar uma sub-rede, a secção seguinte cria uma sub-rede designada *mySubnet* no *myVnet* rede virtual:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Criar endereço IP público
Para aceder aos recursos através da Internet, crie e atribua um endereço IP público à VM. A secção seguinte um manual de comunicação social Ansible cria um endereço IP público com o nome *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Criar o grupo de segurança de rede
Grupos de segurança de rede controlar o fluxo de tráfego de rede que entra e sai da VM. A secção seguinte um manual de comunicação social Ansible cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup* e define uma regra para permitir o tráfego na porta TCP 22 SSH:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>Criar o cartão de interface de rede virtual
Uma placa de interface de rede virtual (NIC) liga-se a VM para uma determinada rede virtual, o endereço IP público e o grupo de segurança de rede. A secção seguinte um manual de comunicação social Ansible cria uma NIC virtual com o nome *myNIC* ligados aos recursos da rede virtuais que criou:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Criar a máquina virtual
O último passo consiste em criar uma VM e utilizar todos os recursos criados. A secção seguinte um manual de comunicação social Ansible cria uma VM chamada *myVM* e anexa o NIC virtual com o nome *myNIC*. Introduza os seus próprios dados de chaves públicos no *key_data* emparelhe da seguinte forma:

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys: 
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Concluir o manual de comunicação social Ansible
Para reunir todas estas secções, crie um manual de comunicação social Ansible denominado *azure_create_complete_vm.yml* e cole o seguinte conteúdo:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible necessita de um grupo de recursos para implementar todos os recursos em. Crie um grupo de recursos com [az group create](/cli/azure/vm#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *eastus* localização:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para criar o ambiente de VM completado com Ansible, execute o manual de comunicação social da seguinte forma:

```bash
ansible-playbook azure_create_complete_vm.yml
```

O resultado semelhante ao seguinte exemplo mostra que a VM foi criada com êxito:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Passos seguintes
Este exemplo cria um ambiente de VM completado, incluindo os recursos de rede virtuais necessários. Para obter um exemplo mais direto criar uma VM para recursos de rede existentes com opções predefinidas, consulte [criar uma VM](ansible-create-vm.md).
