---
title: "Utilizar a CLI do Azure para criar uma atribuição de política para identificar recursos incompatíveis no seu ambiente do Azure | Microsoft Docs"
description: "Utilize o PowerShell para criar uma atribuição de política do Azure para identificar recursos incompatíveis."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 764554a6afcc7912c53fc5000a6af44abb2adc99
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Criar uma atribuição de política para identificar recursos incompatíveis no seu ambiente do Azure com a CLI do Azure

O primeiro passo na conformidade de compreender no Azure é saber onde tem de estar posicionados com os seus próprios recursos atuais. Este guia de introdução os passos do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, irá com êxito identificou que máquinas virtuais não estiver a utilizar discos geridos e estão, por conseguinte, *incompatíveis*.
.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="opt-in-to-azure-policy"></a>Escolher a política do Azure

Política do Azure está agora disponível em pré-visualização pública e tem de registar para pedir acesso.

1. Aceda a política do Azure em https://aka.ms/getpolicy e selecione **inscrever-se** no painel esquerdo.

   ![Pesquisa para a política](media/assign-policy-definition/sign-up.png)

2. Optar ativamente por participar na política do Azure ao selecionar as subscrições no **subscrição** lista que gostaria de trabalhar com. Em seguida, selecione **registar**.

   ![Optar ativamente por participar em utilizar a política do Azure](media/assign-policy-definition/preview-opt-in.png)

   O pedido é aprovado automaticamente para a pré-visualização. Aguarde até 30 minutos para o sistema processe o registo.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Este guia de introdução, iremos criar uma atribuição de política e atribuir as máquinas virtuais de auditoria sem uma definição discos geridos. Esta definição de política identifica recursos que não está em conformidade com as condições definidas na definição de política.

Siga estes passos para criar uma nova atribuição de política.

Ver todas as definições de política e localizar a definição de política "Máquinas virtuais dos auditoria sem discos geridos":

```azurecli
az policy definition list
```

Política do Azure vem com já incorporada nas definições de política, pode utilizar. Poderá ver definições de política incorporados, tais como:

- Impor a etiqueta e o respetivo valor
- Aplicar a etiqueta e o respetivo valor
- Requer a versão do SQL Server 12.0

Em seguida, forneça as seguintes informações e execute o seguinte comando para atribuir a definição de política:

- Apresentar **nome** para a atribuição de política. Neste caso, vamos utilizar *auditoria máquinas de virtuais sem discos geridos*.
- **Política** – esta é a definição de política, com base desativado que estiver a utilizar para criar a atribuição. Neste caso, é a definição de política – *auditoria máquinas de virtuais sem discos geridos*
- A **âmbito** - um âmbito determina que recursos ou agrupamento de recursos de atribuição de política obtém imposta. Podem ir de uma subscrição para grupos de recursos.

  Utilizar a subscrição (ou grupo de recursos) que registou anteriormente quando optado política do Azure, neste exemplo, estamos a utilizar este ID de subscrição - **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** e o nome do grupo de recursos - **FabrikamOMS**. Lembre-se de que alterá-las para o ID da subscrição e o nome do grupo de recursos que está a funcionar.

Este é o que o comando deve ter o seguinte aspeto:

```azurecli
az policy assignment create --name Audit Virtual Machines without Managed Disks Assignment --policy Audit Virtual Machines without Managed Disks --scope /subscriptions/
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

Uma atribuição de política é uma política que tenha sido atribuída para ocorrer dentro de um âmbito específico. Este âmbito também pode ir desde um grupo de gestão para um grupo de recursos.

## <a name="identify-non-compliant-resources"></a>Identificar recursos incompatíveis

Para ver os recursos que não são compatíveis com esta nova atribuição de:

1. Navegue de volta para a página de política do Azure.
2. Selecione **conformidade** no painel esquerdo e procure o **atribuição de política** que criou.

   ![Conformidade com a política](media/assign-policy-definition/policy-compliance.png)

   Se existirem quaisquer recursos existentes que não são compatíveis com esta nova atribuição, são apresentados no **recursos não compatível** separador, conforme mostrado acima.

## <a name="clean-up-resources"></a>Limpar recursos

Noutros guias de nesta coleção baseiam-se este guia de introdução. Se pretender continuar a trabalhar nos tutoriais subsequentes, não limpeza até os recursos criados neste guia de introdução. Se não pretender continuar, elimine a atribuição de que criou ao executar o comando:

```azurecli
az policy assignment delete –name  Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, é atribuído a uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas, para se certificar de que recursos cria no **futuras** estão em conformidade, avance para o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./create-manage-policy.md)
