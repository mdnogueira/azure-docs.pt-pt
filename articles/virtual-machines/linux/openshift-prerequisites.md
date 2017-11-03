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
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Pré-requisitos comuns para OpenShift no Azure

Quando implementar OpenShift no Azure, existem alguns pré-requisitos comuns independentemente se estiver a implementar OpenShift origem ou OpenShift contentor plataforma.

A instalação do OpenShift é feita através de Ansible playbooks. Ansible utiliza SSH para ligar a todos os anfitriões que irão fazer parte do cluster para concluir os passos de instalação.
Quando a ligação SSH é iniciada para os anfitriões remotos, não é possível introduzir uma frase de acesso. Por este motivo, a chave privada não pode ter uma frase de acesso associado à mesma ou implementação irá falhar.
Uma vez que todas as VMs implementadas através de modelos do Resource Manager, a mesma chave pública é utilizada para aceder a todas as VMs. É necessário inserir a chave privada correspondente no VM que está a executar, bem como todos os os playbooks.
Para fazê-lo em segurança, utilizamos o Cofre de chaves do Azure para transferir a chave privada para a VM.

Se for necessário para armazenamento persistente de contentores, Volumes persistente (PV) são necessários. Estes PVs tem de estar associada a alguma forma de armazenamento persistente. OpenShift suporta discos do Azure (VHDs) para esta capacidade mas Azure primeiro tem de ser configurado como o fornecedor de nuvem. Neste modelo, irá OpenShift:

- Criar um objeto VHD uma conta de armazenamento do Azure
- Montar o VHD para uma VM e formate o volume
- Montar o volume de Pod

Para isto funcionar, OpenShift tem permissões para efetuar as tarefas anteriores no Azure. Para tal, é necessário um Principal de serviço. O serviço Principal (SP) é uma conta de segurança no Azure Active Directory que são concedidas permissões para recursos.
O Principal de serviço tem de ter acesso a contas de armazenamento e as VMs que compõem o cluster. Se todos os recursos de cluster OpenShift são implementados num único grupo de recursos, o SP pode ser concedido permissões para esse grupo de recursos.

Este guia descreve como criar os artefactos associados com os pré-requisitos.

> [!div class="checklist"]
> * Crie um KeyVault para gerir as chaves SSH para o cluster OpenShift.
> * Crie um Principal de serviço para utilização pelo fornecedor de nuvem do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 
Inicie sessão na sua subscrição do Azure com o [início de sessão az](/cli/azure/#login) de comandos e siga no ecrã indicações ou clique em **experimente** para utilizar a Shell de nuvem.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Recomenda-se que um grupo de recursos dedicado é utilizado para alojar o Cofre de chaves - separado do grupo de recursos que os recursos de cluster OpenShift serão implementados numa. 

O exemplo seguinte cria um grupo de recursos denominado *keyvaultrg* no *eastus* localização.

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Criar um KeyVault para armazenar as chaves SSH para o cluster com o [az keyvault criar](/cli/azure/keyvault#create) comando. O nome do Cofre de chaves deve ser globalmente exclusivo.

O exemplo seguinte cria um keyvault denominado *keyvault* no *keyvaultrg* grupo de recursos.

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
É necessária uma chave SSH para proteger o acesso ao cluster OpenShift origem. Crie um par de chaves SSH utilizando o `ssh-keygen` comando (no Linux ou Mac).
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O par de chaves SSH que criar não pode ter uma frase de acesso.

Para mais informações sobre chaves SSH no Windows, [das chaves de como criar SSH no Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Armazenar a chave privada SSH no Cofre de chaves
A implementação de OpenShift utiliza a chave SSH que criou para proteger o acesso ao modelo global de OpenShift. Para ativar a implementação para obter a chave SSH de forma segura, armazene a chave no Cofre de chaves utilizando o seguinte comando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço 
OpenShift comunica com o Azure com um nome de utilizador e palavra-passe ou um principal de serviço. Um principal de serviço do Azure é uma identidade de segurança que pode utilizar com aplicações, serviços e ferramentas de automatização como OpenShift. Controlar e definir as permissões para as operações que pode efetuar o principal de serviço no Azure. Para melhorar a segurança ao longo de apenas a fornecer um nome de utilizador e palavra-passe, este exemplo cria um serviço básico principal.

Criar um serviço principal com [az ad sp criar-para-rbac](/cli/azure/ad/sp#create-for-rbac) e as credenciais que OpenShift necessita de saída.

O exemplo seguinte cria um serviço principal e atribui-permissões de contribuinte a um grupo de recursos denominado myResourceGroup. Se utilizar o Windows, execute ```az group show --name myResourceGroup --query id``` separadamente e utilizar a saída para feed a - opção de âmbitos.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Tome nota da propriedade appId devolvida do comando.
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
 > Não crie uma palavra-passe insegura.  Siga as instruções em [Azure AD password rules and restrictions](/azure/active-directory/active-directory-passwords-policy) (Regras e limitações de palavras-passe do Azure AD).

Para obter mais informações sobre principais de serviço, consulte [criar um Azure principal de serviço com o Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="next-steps"></a>Passos seguintes

Este artigo abrangidos os seguintes tópicos:
> [!div class="checklist"]
> * Crie um KeyVault para gerir as chaves SSH para o cluster OpenShift.
> * Crie um Principal de serviço para utilização pelo fornecedor de nuvem do Azure.

Agora, aceda a implementar um cluster de OpenShift

- [Implementar OpenShift origem](./openshift-origin.md)
- [Implementar OpenShift contentor plataforma](./openshift-container-platform.md)

