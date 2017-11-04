---
title: "Criar uma atribuição de política para identificar recursos incompatíveis no seu ambiente do Azure | Microsoft Docs"
description: "Este artigo explica os passos para criar uma definição de política para identificar recursos incompatíveis."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: db5112c858d2a2c54813d9c9a3670a45fcbdb993
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Criar uma atribuição de política para identificar recursos incompatíveis no seu ambiente do Azure
O primeiro passo na conformidade de compreender no Azure é saber onde tem de estar posicionados com os seus próprios recursos atuais. Este guia de introdução os passos do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, irá com êxito identificou máquinas virtuais que não estão a utilizar discos geridos e estão, por conseguinte, *incompatíveis*.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="opt-in-to-azure-policy"></a>Escolher a política do Azure

Política do Azure está agora disponível em pré-visualização pública e tem de registar para pedir acesso.

1. Aceda a política do Azure em https://aka.ms/getpolicy e selecione **inscrever-se** no painel esquerdo.

   ![Pesquisa para a política](media/assign-policy-definition/sign-up.png)

2. Optar ativamente por participar na política do Azure ao selecionar as subscrições no **subscrição** lista que gostaria de trabalhar com. Em seguida, selecione **registar**.

   ![Optar ativamente por participar em utilizar a política do Azure](media/assign-policy-definition/preview-opt-in.png)

   O pedido é aprovado automaticamente para a pré-visualização. Aguarde até 30 minutos para o sistema processe o registo.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Este guia de introdução, iremos criar uma atribuição de política e atribuir a *auditoria máquinas de virtuais sem discos geridos* definição de política.

1. Selecione **atribuições** no painel esquerdo da página política do Azure.
2. Selecione **atribuir política** da parte superior do **atribuições** painel.

   ![Atribuir uma definição de política](media/assign-policy-definition/select-assign-policy.png)

3. No **atribuir política** página, clique em ![botão de definição de política](media/assign-policy-definition/definitions-button.png) junto a **política** campo para abrir a lista de definições disponíveis.

   ![Definições de política disponíveis aberta](media/assign-policy-definition/open-policy-definitions.png)

   Política do Azure vem com já incorporada nas definições de política, pode utilizar. Consulte as definições de política incorporados, tais como:

   - Impor a etiqueta e o respetivo valor
   - Aplicar a etiqueta e o respetivo valor
   - Requer a versão do SQL Server 12.0

4. Pesquisa através das definições de política para localizar o *VMs de auditoria que não utilize discos geridos* definição. Clique nessa política e clique em **atribuir**.

   ![Localizar a definição de política correta](media/assign-policy-definition/select-available-definition.png)

5. Forneça uma apresentação **nome** para a atribuição de política. Neste caso, vamos utilizar *VMs de auditoria que não utilize discos geridos*. Também pode adicionar opcional **Descrição**. A descrição fornece detalhes sobre a forma como esta atribuição de política identifica todas as máquinas virtuais criadas neste ambiente que não utilizam discos geridos.
6. Alterar o escalão de preço para **padrão** para se certificar de que a política é aplicada aos recursos existentes.

   Existem dois escalões de preços dentro de política do Azure – *livres* e *padrão*. Com o escalão gratuito, apenas pode impor políticas de recursos futuras, enquanto com padrão, pode também impor-los em recursos existentes para compreender melhor o seu estado de compatibilidade. Uma vez que estão na pré-visualização limitada, iremos não ainda libertadas um modelo de preços, pelo que não receberão uma fatura para selecionar *padrão*. Para mais informações sobre preços, observe: [preços de política do Azure](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/).

7. Selecione o **âmbito** gostaria que a política aplicada ao.  Um âmbito determina que recursos ou agrupamento de recursos de atribuição de política obtém imposta. Podem ir de uma subscrição para grupos de recursos.
8. Selecione a subscrição (ou grupo de recursos) anteriormente registados quando optado política do Azure. Neste exemplo, estamos a utilizar esta subscrição - **Dev de capacidade de análise do Azure**, mas as opções variam.

   ![Localizar a definição de política correta](media/assign-policy-definition/assign-policy.png)

9. Selecione **atribuir**.

Agora, está pronto para identificar recursos incompatíveis para compreender o estado de compatibilidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos incompatíveis

Selecione **conformidade** no painel esquerdo e procure a atribuição de política que criou.

![Conformidade com a política](media/assign-policy-definition/policy-compliance.png)

Se existirem quaisquer recursos existentes que não são compatíveis com esta atribuição de nova, irão aparecer no **recursos não compatível** separador.

Se uma condição for avaliada em todos os recursos existentes e se trata de saída verdadeiro para algumas delas, esses recursos estão marcados como não conformes com a política. Eis uma tabela de como as diferentes ações que temos disponível hoje funcionam com o resultado da avaliação de condição e o estado de compatibilidade dos seus recursos.

|Recurso  |Se a condição na política for avaliada como  |Ação na política   |Estado de compatibilidade  |
|-----------|---------|---------|---------|
|Existe     |Verdadeiro     |Negar     |Não compatível |
|Existe     |Falso    |Negar     |Em conformidade     |
|Existe     |Verdadeiro     |Acrescentar   |Não compatível |
|Existe     |Falso    |Acrescentar   |Em conformidade     |
|Existe     |Verdadeiro     |Auditoria    |Não compatível |
|Existe     |Falso    |Auditoria    |Não compatível |

## <a name="clean-up-resources"></a>Limpar recursos

Noutros guias de nesta coleção baseiam-se este guia de introdução. Se pretender continuar a trabalhar nos tutoriais subsequentes, não limpeza até os recursos criados neste guia de introdução. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.
1. Selecione **atribuições** no painel esquerdo.
2. Procure a atribuição que acabou de criar.

   ![Elimina uma atribuição](media/assign-policy-definition/delete-assignment.png)

3.  Selecione **eliminar atribuição**.

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, atribuiu uma definição de política para um âmbito para se certificar de que todos os recursos desse âmbito são compatíveis e identificar aqueles não são.

Para saber mais sobre a atribuição de políticas para garantir que **futuras** recursos que obterem criados estão em conformidade, avance para o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./create-manage-policy.md)
