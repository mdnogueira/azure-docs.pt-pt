---
title: "Tarefas de gestão do serviço de nuvem comuns | Microsoft Docs"
description: Saiba como gerir cloud services no portal do Azure. Estes exemplos utilizam o portal do Azure.
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 4650cebe18153e3b10bbec685a66a590348c99e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-cloud-services"></a>Como gerir Cloud Services
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-how-to-manage-portal.md)
> * [Portal Clássico do Azure](cloud-services-how-to-manage.md)
>
>

No **serviços em nuvem (clássica)** área do Azure portal, pode atualizar uma função de serviço ou uma implementação, promover uma implementação faseada para produção, ligar recursos ao seu serviço de nuvem, para que possa ver as dependências de recursos e dimensionar os recursos em conjunto e eliminar um serviço em nuvem ou de uma implementação.

Estão disponíveis mais informações sobre como dimensionar o serviço de nuvem [aqui](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Como: atualizar uma função de serviço de nuvem ou a implementação
Se precisar de atualizar o código de aplicação do serviço em nuvem, utilize **atualizar** no painel de serviço em nuvem. Pode atualizar uma única função ou todas as funções. Para atualizar, pode carregar um ficheiro de configuração do serviço ou o novo pacote de serviço.

1. No [portal do Azure][Azure portal], selecione o serviço de nuvem que pretende atualizar. Este passo é aberto o painel de instância de serviço de nuvem.
2. No painel, clique o **atualização** botão.

    ![Botão de atualização](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Atualize a implementação com um novo ficheiro de pacote de serviço (. cspkg) e o ficheiro de configuração de serviço (. cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Opcionalmente,** atualizar a etiqueta de implementação e a conta de armazenamento.
5. Se quaisquer funções tem apenas uma instância de função, selecione o **implementar mesmo que uma ou mais funções contenham uma única instância** para ativar a atualização continuar.

    Azure apenas pode garantir a disponibilidade do serviço de percentagem de 99,95 durante uma atualização de serviço em nuvem se cada função tem, pelo menos, duas instâncias de função (máquinas virtuais). Com duas instâncias de função, uma máquina virtual processa os pedidos de cliente enquanto o outro está atualizado.

6. Verifique **iniciar a implementação** com a atualização aplicada após concluir o carregamento do pacote.
7. Clique em **OK** para começar a atualizar o serviço.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Como: comutação implementações para promover uma implementação faseada para produção
Quando optar por uma nova versão de um serviço em nuvem, a fase de implementar e testar a nova versão no ambiente de teste do serviço na nuvem. Utilize **comutação** mudar os URLs através do qual as duas implementações são tratadas e promover uma nova versão para produção.

Pode trocar entre implementações o **serviços em nuvem** página ou dashboard.

1. No [portal do Azure][Azure portal], selecione o serviço de nuvem que pretende atualizar. Este passo é aberto o painel de instância de serviço de nuvem.
2. No painel, clique o **comutação** botão.

    ![Troca de serviços cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. O pedido de confirmação seguinte é aberto.

    ![Troca de serviços cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Depois de verificar as informações de implementação, clique em **OK** ao trocar as implementações.

    A troca de implementação acontece rapidamente porque a única coisa que altera os endereços IP virtuais (VIP) para as implementações.

    Para guardar os custos de computação, pode eliminar a implementação de teste depois de verificar que a implementação de produção está a funcionar conforme esperado.

### <a name="common-questions-about-swapping-deployments"></a>Perguntas comuns sobre a troca de implementações

**Quais são os pré-requisitos para a troca de implementações?**

Existem dois chaves pré-requisitos para uma troca de implementação efetuada com êxito:

- Se gostaria de utilizar um endereço IP estático para a ranhura de produção, tem de reservar uma para o bloco de transição. Caso contrário, a troca falha.

- Todas as instâncias das suas funções devem estar em execução antes de efetuar a troca. Pode verificar o estado das suas instâncias no painel de descrição geral do portal do Azure. Em alternativa, pode utilizar o [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) comando no Windows PowerShell.

Tenha em atenção que as atualizações de SO convidado e serviço autorrecuperação operações podem também fazer com que trocas de implementação falhou. Para obter mais informações, consulte [problemas de implementação de serviço de nuvem de resolução de problemas](cloud-services-troubleshoot-deployment-problems.md).

**Uma troca implica o período de indisponibilidade para a minha aplicação? Como posso processá-lo?**

Conforme descrito na última secção, uma troca de implementação é rápida, normalmente, uma vez que é apenas uma alteração de configuração de Balanceador de carga do Azure. Em alguns casos, no entanto, este pode demorar segundos dez ou mais e resultar em falhas de ligação transitório. Para limitar o impacto para os seus clientes, considere implementar [lógica de repetição do cliente](../best-practices-retry-general.md).

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Como: ligar um recurso a um serviço em nuvem
O portal do Azure não ligar recursos em conjunto como atual portal clássico do Azure. Em vez disso, implemente recursos adicionais para o mesmo grupo de recursos que está a ser utilizado pelo serviço de nuvem.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Como: eliminar as implementações e um serviço em nuvem
Antes de poder eliminar um serviço em nuvem, tem de eliminar cada implementação existente.

Para guardar os custos de computação, pode eliminar a implementação de teste depois de verificar que a implementação de produção está a funcionar conforme esperado. É-lhe faturado para custos de computação implementado as instâncias de função que são parados.

Utilize o procedimento seguinte para eliminar uma implementação ou o serviço em nuvem.

1. No [portal do Azure][Azure portal], selecione o serviço de nuvem que pretende eliminar. Este passo é aberto o painel de instância de serviço de nuvem.
2. No painel, clique o **eliminar** botão.

    ![Troca de serviços cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Pode eliminar o serviço em nuvem todo verificando **serviço e as respetivas implementações de nuvem** ou escolha o o **implementação de produção** ou **implementação faseada**.

    ![Troca de serviços cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Clique em de **eliminar** na parte inferior.
5. Para eliminar o serviço em nuvem, clique em **eliminar o serviço em nuvem**. Em seguida, na linha de confirmação, clique em **Sim**.

> [!NOTE]
> Quando um serviço em nuvem é eliminado e monitorização verboso estiver configurado, deve eliminar manualmente os dados da sua conta de armazenamento. Para obter informações sobre onde encontrar as tabelas de métricas, consulte [isto](cloud-services-how-to-monitor.md) artigo.


## <a name="how-to-find-more-information-about-failed-deployments"></a>Como: encontrar mais informações sobre implementações falhadas
O **descrição geral** painel tem uma barra de estado na parte superior. Ao clicar a barra, um novo painel abre-se e apresenta as informações de erro. Se a implementação não contém quaisquer erros, o painel de informações é em branco.

![Troca de serviços cloud](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implementar um serviço em nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).
