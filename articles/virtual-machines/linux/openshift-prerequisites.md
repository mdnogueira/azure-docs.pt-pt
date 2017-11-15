---
title: "OpenShift na pré-requisitos do Azure | Microsoft Docs"
description: "Pré-requisitos para implementar OpenShift no Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: c6758e8e1a9d9595ae8efb0b8c5aba0b81b0dc38
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Pré-requisitos comuns para implementar OpenShift no Azure

Este artigo descreve os pré-requisitos comuns para implementar OpenShift origem ou OpenShift contentor plataforma no Azure.

A instalação do OpenShift utiliza Ansible playbooks. Ansible utiliza Secure Shell (SSH) para ligar a todos os anfitriões de cluster para concluir os passos de instalação.

Quando inicia a ligação SSH para os anfitriões remotos, não é possível introduzir uma palavra-passe. Por este motivo, a chave privada não pode ter uma palavra-passe associada ou falha na implementação.

Porque as máquinas virtuais (VMs) implementar através de modelos Azure Resource Manager, a mesma chave pública é utilizada para aceder a todas as VMs. Tem de inserir a chave privada correspondente no VM que executa, bem como todos os os playbooks. Para fazê-lo em segurança, pode utilizar um cofre de chaves do Azure para transferir a chave privada para a VM.

Se for necessário para armazenamento persistente de contentores, volumes persistentes são necessários. OpenShift suporta discos rígidos virtuais (VHDs) do Azure para esta capacidade, mas tem de ser configurado primeiro Azure como o fornecedor de nuvem. 

Neste modelo, OpenShift:

- Cria um objeto VHD de uma conta de armazenamento do Azure.
- Montar o VHD para uma VM e formatar o volume.
- Montar o volume para o pod.

Para esta configuração funcionar, OpenShift tem permissões para efetuar as tarefas anteriores no Azure. Fazê-lo com um principal de serviço. O principal de serviço é uma conta de segurança no Azure Active Directory que são concedidas permissões para recursos.

O principal de serviço tem de ter acesso a contas de armazenamento e as VMs que compõem o cluster. Se todos os recursos de cluster OpenShift implementar num grupo de recursos única, o principal de serviço pode ser concedido permissões para esse grupo de recursos.

Este guia descreve como criar os artefactos associados com os pré-requisitos.

> [!div class="checklist"]
> * Crie um cofre de chaves para gerir as chaves SSH para o cluster OpenShift.
> * Crie um principal de serviço para utilização pelo fornecedor de solução de nuvem do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 
Inicie sessão na sua subscrição do Azure com o [início de sessão az](/cli/azure/#login) de comandos e siga no ecrã as direções, ou clique em **experimente** para utilizar a Shell de nuvem.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Utilize um grupo de recursos dedicado para alojar o Cofre de chaves. Este grupo está separado do grupo de recursos no qual implementar os recursos de cluster OpenShift. 

O exemplo seguinte cria um grupo de recursos denominado *keyvaultrg* no *eastus* localização:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Criar um cofre de chaves para armazenar as chaves SSH para o cluster com o [az keyvault criar](/cli/azure/keyvault#create) comando. O nome do Cofre de chaves deve ser globalmente exclusivo.

O exemplo seguinte cria um cofre de chaves com o nome *keyvault* no *keyvaultrg* grupo de recursos:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
É necessária uma chave SSH para proteger o acesso ao cluster OpenShift origem. Criar um par de chaves SSH com o `ssh-keygen` comando (no Linux ou macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O par de chaves SSH não pode ter uma palavra-passe.

Para obter mais informações sobre chaves SSH no Windows, consulte [das chaves de como criar SSH no Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Armazenar a chave privada SSH no Cofre de chaves do Azure
A implementação de OpenShift utiliza a chave SSH que criou para proteger o acesso ao modelo global de OpenShift. Para ativar a implementação para obter a chave SSH de forma segura, armazene a chave no Cofre de chaves utilizando o seguinte comando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço 
OpenShift comunica com o Azure, utilizando um nome de utilizador e palavra-passe ou um principal de serviço. Um principal de serviço do Azure é uma identidade de segurança que pode utilizar com aplicações, serviços e ferramentas de automatização como OpenShift. Controlar e definir as permissões para as operações o principal de serviço pode efetuar no Azure. Para melhorar a segurança para além de fornecer apenas um nome de utilizador e palavra-passe, este exemplo cria um serviço básico principal.

Criar um serviço principal com [az ad sp criar-para-rbac](/cli/azure/ad/sp#create-for-rbac) e as credenciais que OpenShift necessita de saída.

O exemplo seguinte cria um serviço principal e atribui-permissões de contribuinte a um grupo de recursos denominado myResourceGroup. Se estiver a utilizar o Windows, execute ```az group show --name myResourceGroup --query id``` separadamente e utilizar a saída para feed a - opção de âmbitos.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Tome nota da propriedade appId devolvida do comando:
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Lembre-se de que criar uma palavra-passe segura. Siga as instruções em [Azure AD password rules and restrictions](/azure/active-directory/active-directory-passwords-policy) (Regras e limitações de palavras-passe do Azure AD).

Para obter mais informações sobre principais de serviço, consulte [criar um Azure principal de serviço com o Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Passos seguintes

Este artigo abrangidos os seguintes tópicos:
> [!div class="checklist"]
> * Crie um cofre de chaves para gerir as chaves SSH para o cluster OpenShift.
> * Crie um principal de serviço para utilização pelo fornecedor de solução de nuvem do Azure.

Em seguida, implemente um cluster de OpenShift:

- [Implementar OpenShift origem](./openshift-origin.md)
- [Implementar OpenShift contentor plataforma](./openshift-container-platform.md)

