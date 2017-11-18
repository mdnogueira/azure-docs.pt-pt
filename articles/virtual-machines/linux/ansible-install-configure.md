---
title: "Instalar e configurar Ansible para utilização com máquinas virtuais do Azure | Microsoft Docs"
description: Saiba como instalar e configurar Ansible para gerir recursos do Azure no Ubuntu, CentOS e SLES
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
ms.openlocfilehash: c5257ef5c635080f5eaca371e1882b13cc37e0fd
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Instalar e configurar Ansible para gerir máquinas virtuais no Azure
Este artigo fornece detalhes sobre como instalar Ansible e módulos de SDK Python do Azure necessários para algumas da distros mais comuns do Linux. Pode instalar Ansible em outros distros ao ajustar os pacotes instalados para ajustar a sua plataforma específica. Para criar recursos do Azure de forma segura, também Saiba como criar e definir credenciais para Ansible a utilizar. 

Para mais opções de instalação e passos para plataformas adicionais, consulte o [Ansible instalar guia](https://docs.ansible.com/ansible/intro_installation.html).


## <a name="install-ansible"></a>Instalar Ansible
Em primeiro lugar, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupAnsible* no *eastus* localização:

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

Agora criar uma VM e instalar Ansible para uma da distros seguintes:

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [CentOS 7.3](#centos-73)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a sua VM ao utilizar o `publicIpAddress` anotou na saída da VM operação de criação:

```bash
ssh azureuser@<publicIpAddress>
```

A VM, instale os pacotes necessários para os módulos do SDK Python do Azure e Ansible da seguinte forma:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Mover para [credenciais do Azure crie](#create-azure-credentials).


### <a name="centos-73"></a>CentOS 7.3
Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a sua VM ao utilizar o `publicIpAddress` anotou na saída da VM operação de criação:

```bash
ssh azureuser@<publicIpAddress>
```

A VM, instale os pacotes necessários para os módulos do SDK Python do Azure e Ansible da seguinte forma:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Mover para [credenciais do Azure crie](#create-azure-credentials).


### <a name="sles-12-sp2"></a>SLES 12 SP2
Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a sua VM ao utilizar o `publicIpAddress` anotou na saída da VM operação de criação:

```bash
ssh azureuser@<publicIpAddress>
```

A VM, instale os pacotes necessários para os módulos do SDK Python do Azure e Ansible da seguinte forma:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 python-devel \
    libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Mover para [credenciais do Azure crie](#create-azure-credentials).


## <a name="create-azure-credentials"></a>Criar as credenciais do Azure
Ansible comunica com o Azure com um nome de utilizador e palavra-passe ou um principal de serviço. Um principal de serviço do Azure é uma identidade de segurança que pode utilizar com aplicações, serviços e ferramentas de automatização como Ansible. Controlar e definir as permissões para as operações que pode efetuar o principal de serviço no Azure. Para melhorar a segurança ao longo de apenas a fornecer um nome de utilizador e palavra-passe, este exemplo cria um serviço básico principal.

Criar um serviço principal com [az ad sp criar-para-rbac](/cli/azure/ad/sp#create-for-rbac) e as credenciais que Ansible necessita de saída:

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

Segue-se um exemplo de saída a partir dos comandos do anteriores:

```json
[
  "eec5624a-90f8-4386-8a87-02730b5410d5",
  "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "72f988bf-86f1-41af-91ab-2d7cd011db47"
]
```

Para autenticar para o Azure, também terá de obter o ID de subscrição do Azure com [mostrar de conta az](/cli/azure/account#show):

```azurecli
az account show --query [id] --output tsv
```

Utilize o resultado destes dois comandos no próximo passo.


## <a name="create-ansible-credentials-file"></a>Criar ficheiro de credenciais Ansible
Para fornecer credenciais para Ansible, definir variáveis de ambiente ou criar um ficheiro de credenciais local. Para obter mais informações sobre como definir Ansible credenciais, consulte [fornecer credenciais para módulos do Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules). 

Para um ambiente de desenvolvimento, crie um *credenciais* de ficheiros para Ansible no seu anfitrião VM da seguinte forma:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

O *credenciais* próprio ficheiro combina o ID de subscrição com o resultado da criação de um principal de serviço. O resultado da anterior [az ad sp criar-para-rbac](/cli/azure/ad/sp#create-for-rbac) comando é a mesma ordem conforme necessário para *client_id*, *segredo*, e *inquilino*. O exemplo seguinte *credenciais* ficheiro mostra estes valores que correspondam a saída anterior. Introduza os seus próprios valores da seguinte forma:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>Utilizar variáveis de ambiente de Ansible
Se pretender utilizar ferramentas como Ansible Torre ou Jenkins, pode definir variáveis de ambiente da seguinte forma. Estas variáveis combinam o ID de subscrição com o resultado da criação de um serviço principal. O resultado da anterior [az ad sp criar-para-rbac](/cli/azure/ad/sp#create-for-rbac) comando é a mesma ordem conforme necessário para *AZURE_CLIENT_ID*, *AZURE_SECRET*, e *AZURE_TENANT* . 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Passos seguintes
Tem agora Ansible e o necessários SDK Python do Azure módulos instalados e as credenciais definidas para Ansible para utilizar. Saiba como [criar uma VM com Ansible](ansible-create-vm.md). Também pode aprender como [criar uma VM do Azure completa e que suporta a recursos com Ansible](ansible-create-complete-vm.md).
