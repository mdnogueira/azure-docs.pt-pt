---
title: "Tarefas de gestão comuns serviço de nuvem (clássica) | Microsoft Docs"
description: "Saiba como gerir cloud services no portal clássico do Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 41a30e50-067c-485b-96fd-434a6d057abc
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 2ee76dfcb579e53975b1f61a6590f8d78dc0961b
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

No **serviços em nuvem** área da clássica do Azure portal, pode atualizar uma função de serviço ou uma implementação, promover uma implementação faseada para produção, ligar recursos ao seu serviço de nuvem, para que possa ver as dependências de recursos e dimensionar os recursos em conjunto e eliminar um serviço em nuvem ou de uma implementação.

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Como: atualizar uma função de serviço de nuvem ou a implementação
Se precisar de atualizar o código de aplicação do serviço em nuvem, utilize **atualizar** no dashboard, **serviços em nuvem** página, ou **instâncias** página. Pode atualizar uma única função ou todas as funções. Terá de carregar um novo pacote de serviço e o ficheiro de configuração do serviço.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), no dashboard, **serviços em nuvem** página, ou **instâncias** página, clique em **atualização**.

    ![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. No **etiqueta de implementação**, introduza um nome para identificar a implementação (por exemplo, mycloudservice4). Pode encontrar a etiqueta de implementação em **início rápido** no dashboard.
3. No **pacote**, utilize **procurar** para carregar o ficheiro de pacote de serviço (. cspkg).
4. No **configuração**, utilize **procurar** para carregar o ficheiro de configuração de serviço (. cscfg).
5. No **função**, selecione **todos os** se pretende atualizar todas as funções do serviço em nuvem. Para efetuar uma atualização da função único, selecione a função que pretende atualizar. Mesmo se selecionar uma função específica para atualizar, as atualizações no ficheiro de configuração de serviço são aplicadas a todas as funções.
6. Se a atualização de alterar o número de funções ou o tamanho de uma função, selecione o **permitir atualização se mudar de tamanhos de função ou o número de funções** caixa de verificação para ativar a atualização continuar.

    Tenha em atenção que se alterar o tamanho de uma função (ou seja, o tamanho de uma máquina virtual que aloja uma instância de função) ou o número de funções, cada instância de função (máquina virtual) tem de ser novamente instalada e qualquer locais dados serão perdidos.

7. Se quaisquer funções de serviço tem apenas uma instância de função, selecione o **atualizar mesmo que um ou mais funções contenham uma caixa de verificação de instância única** para ativar a atualização continuar.

    Azure apenas pode garantir a disponibilidade do serviço de percentagem de 99,95 durante uma atualização de serviço em nuvem se cada função tem, pelo menos, duas instâncias de função (máquinas virtuais). Permite que uma máquina virtual processar pedidos de cliente, enquanto o outro está a ser atualizado.

8. Clique em **OK** (marca de verificação) para começar a atualizar o serviço.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Como: comutação implementações para promover uma implementação faseada para produção
Utilize **comutação** para promover uma implementação de transição de um serviço em nuvem para produção. Quando decidir implementar uma nova versão de um serviço em nuvem, pode testar e testar a nova versão no ambiente de teste do serviço na nuvem enquanto os seus clientes estão a utilizar a versão atual na produção. Quando estiver pronto para promover a nova versão para produção, pode utilizar **comutação** mudar os URLs que são resolvidas duas implementações.

Pode trocar entre implementações o **serviços em nuvem** página ou dashboard.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **serviços em nuvem**.
2. Na lista de serviços em nuvem, clique no serviço de nuvem para o selecionar.
3. Clique em **comutação**.

    O pedido de confirmação seguinte é aberto.

    ![Troca de serviços cloud](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Depois de verificar as informações de implementação, clique em **Sim** ao trocar as implementações.

    A troca de implementação acontece rapidamente porque a única coisa que altera os endereços IP virtuais (VIP) para as implementações.

    Para guardar os custos de computação, pode eliminar a implementação no ambiente de teste, quando tiver a certeza de que a nova implementação de produção está a funcionar conforme esperado.

### <a name="common-questions-about-swapping-deployments"></a>Perguntas comuns sobre a troca de implementações

**Quais são os pré-requisitos para a troca de implementações?**

Existem dois chaves pré-requisitos para uma troca de implementação efetuada com êxito:

- Se gostaria de utilizar um endereço IP estático para a ranhura de produção, tem de reservar uma para o bloco de transição. Caso contrário, a troca irá falhar.

- Todas as instâncias das suas funções devem estar em execução antes de efetuar a troca. Pode verificar o estado das suas instâncias no portal clássico do Azure ou utilizando [o comando Get-AzureRole no Windows PowerShell](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0).

Tenha em atenção que as atualizações de SO convidado e serviço autorrecuperação do operations podem também fazer com que trocas de implementação falhar. Consulte [problemas de implementação de serviço de nuvem de resolução de problemas](cloud-services-troubleshoot-deployment-problems.md) para obter mais detalhes.

**Uma troca implica o período de indisponibilidade para a minha aplicação? Como posso processá-lo?**

Tal como descrito na última secção, uma troca de implementação, normalmente, é muito rápida, uma vez que é apenas uma alteração de configuração de Balanceador de carga do Azure. Em alguns casos, no entanto, este pode demorar segundos dez ou mais e resultar em falhas de ligação transitório. Para limitar o impacto para os seus clientes, considere implementar [lógica de repetição do cliente](../best-practices-retry-general.md).

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Como: ligar um recurso a um serviço em nuvem
Para mostrar as dependências dos seus serviços cloud noutros recursos, pode associar uma instância da Base de dados SQL do Azure ou de uma conta de armazenamento ao serviço cloud. Pode ligar e desassociar recursos no **recursos ligados** página e, em seguida, monitorizar a respetiva utilização no dashboard do serviço de nuvem. Se uma conta de armazenamento ligado tem monitorização ativada, pode monitorizar o Total de pedidos no dashboard do serviço de nuvem.

Utilize **ligação** para associar um novo ou existente da base de dados do SQL Server instância ou armazenamento conta ao seu serviço de nuvem. Em seguida, pode dimensionar a base de dados, juntamente com a função do serviço em nuvem que está a ser utilizado no **escala** página. (Uma conta do storage automaticamente dimensiona à medida que aumenta de utilização). Para obter mais informações, consulte [como dimensionar um serviço em nuvem e os recursos ligados](cloud-services-how-to-scale.md).

Também pode monitorizar, gerir e dimensionar a base de dados no **bases de dados** nó do portal clássico do Azure.

"Ligar" um recurso neste sentido, não ligar a aplicação ao recurso. Se criar uma nova base de dados utilizando **ligação**, terá de adicionar as cadeias de ligação para o código da aplicação e, em seguida, atualize o serviço em nuvem. Também terá de adicionar as cadeias de ligação se a sua aplicação utilizar recursos numa conta do storage ligadas.

O procedimento seguinte descreve como ligar uma nova instância de base de dados SQL, implementada num servidor de base de dados SQL nova, um serviço em nuvem.

### <a name="to-link-a-sql-database-instance-to-a-cloud-service"></a>Para ligar uma instância de base de dados SQL para um serviço em nuvem
1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **serviços em nuvem**. Em seguida, clique no nome do serviço de nuvem para abrir o dashboard.
2. Clique em **ligado recursos**.

    O **recursos ligados** é aberta a página.

    ![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Clique em **um recurso de ligação** ou **ligação**.

    O **recurso de ligação** inicia o assistente.

    ![Ligação Page1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Clique em **criar um novo recurso** ou **ligar um recurso existente**.
5. Escolha o tipo de recurso para ligar. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **base de dados SQL**. (Apenas o portal clássico do Azure suporta a ligação uma conta de armazenamento para um serviço em nuvem.)
6. Para concluir a configuração de base de dados, siga as instruções em Ajuda para o **bases de dados SQL** área do portal clássico do Azure.

    Pode seguir o progresso da operação de ligação na área de mensagens.

    Quando a ligação estiver concluída, pode monitorizar o estado do recurso ligado no dashboard do serviço de nuvem. Para obter informações sobre como dimensionar uma base de dados do SQL Server ligado, consulte [como dimensionar um serviço em nuvem e os recursos ligados](cloud-services-how-to-scale.md).

### <a name="to-unlink-a-linked-resource"></a>Desassociar um recurso ligado
1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **serviços em nuvem**. Em seguida, clique no nome do serviço de nuvem para abrir o dashboard.
2. Clique em **recursos ligados**e, em seguida, selecione o recurso.
3. Clique em **desassociar**. Em seguida, clique em **Sim** na linha de confirmação.

    Desassociar uma base de dados do SQL Server não tem efeito em ligações da aplicação à base de dados ou a base de dados. Ainda pode gerir a base de dados no **bases de dados SQL** área do portal clássico do Azure.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Como: eliminar as implementações e um serviço em nuvem
Antes de poder eliminar um serviço em nuvem, tem de eliminar cada implementação existente.

Para guardar os custos de computação, pode eliminar a implementação de teste depois de verificar que a implementação de produção está a funcionar conforme esperado. São cobrados computação custos para instâncias de função, mesmo se um serviço em nuvem não está em execução.

Utilize o procedimento seguinte para eliminar uma implementação ou o serviço em nuvem.

1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **serviços em nuvem**.
2. Selecione o serviço em nuvem e, em seguida, clique em **eliminar**. (Para selecionar um serviço em nuvem sem abrir o dashboard, clique em qualquer lugar, exceto o nome na entrada de serviço em nuvem.)

    Se tiver uma implementação no teste ou de produção, verá um menu de opções semelhantes às seguintes na parte inferior da janela. Antes de poder eliminar o serviço em nuvem, tem de eliminar todas as implementações existentes.

    ![Eliminar Menu](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)

3. Para eliminar uma implementação, clique em **eliminar a implementação de produção** ou **eliminar a implementação de teste**. Em seguida, na linha de confirmação, clique em **Sim**.
4. Se pretender eliminar o serviço em nuvem, repita o passo 3, se necessário, para eliminar a implementação de outra.
5. Para eliminar o serviço em nuvem, clique em **eliminar o serviço em nuvem**. Em seguida, na linha de confirmação, clique em **Sim**.

> [!NOTE]
> Se a monitorização verboso estiver configurado para o seu serviço de nuvem, o Azure não a eliminar os dados de monitorização da sua conta de armazenamento, ao eliminar o serviço em nuvem. Terá de eliminar manualmente os dados. Para obter informações sobre onde encontrar as tabelas de métricas, consulte "como: acesso verboso dados fora do portal clássico do Azure de monitorização" no [como os serviços de nuvem de Monitor](cloud-services-how-to-monitor.md).
>
>

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure.md).
* Saiba como [implementar um serviço em nuvem](cloud-services-how-to-create-deploy.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate.md).
