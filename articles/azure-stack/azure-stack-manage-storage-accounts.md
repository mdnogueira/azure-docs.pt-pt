---
title: Gerir contas de armazenamento do Azure pilha | Microsoft Docs
description: Saiba como localizar, gerir, recuperar e recuperar as contas de armazenamento de pilha do Azure
services: azure-stack
documentationcenter: 
author: AniAnirudh
manager: darmour
editor: 
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/6/2017
ms.author: anirudha
ms.openlocfilehash: 6e14bd6312135b45984a82099e68a934ec2a4a70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Gerir contas de armazenamento na pilha do Azure
Saiba como gerir contas de armazenamento na pilha do Azure para localizar, recuperar e recuperar a capacidade de armazenamento com base nas necessidades de negócio.

## <a name="find"></a>Localizar uma conta de armazenamento
A lista de contas do storage na região pode ser visualizada na pilha do Azure por:

1. Num browser da Internet, navegue para https://adminportal.local.azurestack.external.
2. Inicie sessão no portal de administração do Azure pilha como um operador da nuvem (utilizando as credenciais fornecidas durante a implementação)
3. Encontrar no dashboard predefinido – o **gestão região** lista e clique em região pretende explorar. Por exemplo **(local**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Selecione **armazenamento** do **fornecedores de recursos** lista.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Agora, no painel de administrador do fornecedor de recursos de armazenamento – desloque para baixo até o **contas do Storage** separador e clique no mesmo.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   A página resultante é a lista de contas do storage nessa região.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Por predefinição, são apresentadas as primeiras 10 contas. Pode optar por obter mais informações, clicando a **carregar mais** ligação na parte inferior da lista.

OU

Se estiver interessado numa conta do storage específico – pode **filtrar e obter as contas relevantes** apenas.


**Para filtrar para contas:**

1. Clique em **filtro** na parte superior do painel.
2. No painel de filtro, permite-lhe especificar **nome da conta**, **ID de subscrição** ou **estado** para otimizar a lista de contas do storage a apresentar. Utilize-os conforme apropriado.
3. Clique em **atualização**. A lista deverá atualizar em conformidade.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Para repor o filtro: clique em **filtro**, desmarque terminar as seleções e atualizar.

Caixa de texto de pesquisa (na parte superior do painel de lista de contas de armazenamento) permite-lhe realce o texto seleccionado na lista de contas. Este é realmente útil caso quando o id de nome completo ou não está facilmente disponível.

Pode utilizar aqui o texto livre para ajudar a localizar a conta que está interessado.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Ver detalhes da conta
Depois de localizar as contas que está interessado visualização, pode clicar em da conta específica para ver certos detalhes. Um novo painel abre-se com os detalhes da conta, tais como: o tipo da conta, a hora de criação, localização, etc.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Recuperar uma conta eliminada
Pode ser uma situação em que precisar de recuperar de uma conta eliminada.

Na pilha de Azure há uma forma muito simple para o fazer:

1. Navegue para a lista de contas de armazenamento. Consulte [localizar uma conta de armazenamento](#find) neste tópico para obter mais informações.
2. Localize essa conta específica na lista. Se pretender filtrar.
3. Verifique o *estado* da conta. Deverá indicar **eliminado**.
4. Clique na conta que abre o painel de detalhes de conta.
5. Por cima deste painel, localize o **recuperar** botão e clique no mesmo.
6. Clique em **Sim** para confirmar.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. A recuperação está agora em *... processar espera* para uma indicação de que foi efetuada com êxito.
   Também pode clicar no ícone "sino" na parte superior do portal para ver indicações de progresso.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Assim que a conta recuperada está sincronizada com êxito, podem ser utilizada novamente.

### <a name="some-gotchas"></a>Alguns Gotchas
* A conta eliminada mostra o estado como **fora de retenção**.
  
  Isto significa que a conta eliminada excedeu o período de retenção e poderá não ser recuperável.
* A conta eliminada não for apresentada na lista de contas.
  
  Isto pode significar que a conta eliminada já foi libertados. Neste caso este não pode ser recuperado. Consulte [recuperar a capacidade](#reclaim) neste tópico.

## <a name="set-the-retention-period"></a>Definir o período de retenção
A definição de período de retenção permite um operador da nuvem especificar um período de tempo em dias (entre 0 e 9999 dias) durante o qual qualquer conta eliminada, potencialmente, pode ser recuperada. O período de retenção de predefinição está definido como 15 dias. Definir o valor para "0" significa que qualquer conta eliminada imediatamente está fora de retenção e marcado para recolha de lixo periódica.

**Para alterar o período de retenção:**

1. Num browser da internet, navegue para https://adminportal.local.azurestack.external.
2. Inicie sessão no portal de administração do Azure pilha como um operador da nuvem (utilizando as credenciais fornecidas durante a implementação)
3. Encontrar no dashboard predefinido – o **gestão região** lista e clique em região pretende explorar – por exemplo **(local**).
4. Selecione **armazenamento** do **fornecedores de recursos** lista.
5. Clique em **definições** na parte superior para abrir o painel definição.
6. Clique em **configuração** , em seguida, edite o valor de período de retenção.

   Definir o número de dias e guarde-o.
   
   Este valor é imediatamente em vigor e está definido para a região de toda.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Recuperar a capacidade
Uma dos efeitos de lado de ter um período de retenção é que uma conta eliminada continua a capacidade de consumir até que se trata de fora do período de retenção. Como um operador da nuvem poderá necessitar de uma forma de recuperar o espaço de conta eliminado apesar do período de retenção ainda não expirou.

Pode recuperar a capacidade de utilizar o portal ou PowerShell.

**Para recuperar a capacidade através do portal:**
1. Navegue para o painel de contas de armazenamento. Consulte [localizar uma conta de armazenamento](#find).
2. Clique em **recuperar espaço** na parte superior do painel.
3. Leia a mensagem e, em seguida, clique em **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Aguarde que a notificação de sucesso Consulte no ícone de campainha no portal.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Atualize a página de contas de armazenamento. As contas eliminadas já não são apresentadas na lista porque a serem foram removidas.

Pode também utilizar o PowerShell para substituir explicitamente o período de retenção e recuperar imediatamente a capacidade.

**Para recuperar a capacidade através do PowerShell:**   

1. Certifique-se de que tem o Azure PowerShell instalada e configurada. Caso contrário, utilize as instruções seguintes: 
   * Para instalar a versão mais recente do Azure PowerShell e associá-lo à sua subscrição do Azure, consulte [como instalar e configurar o Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Para obter mais informações sobre cmdlets do Azure Resource Manager, consulte [utilizar o Azure PowerShell com o Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Execute o seguinte cmdlet:

> [!NOTE]
> Se executar este cmdlet pode eliminar permanentemente a conta e o respetivo conteúdo. Não é recuperável. Utilize esta opção com cuidado.


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


Para obter mais detalhes, consulte [documentação do powershell de pilha do Azure.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="migrate-a-container"></a>Migrar um contentor
Devido à utilização do armazenamento desigual por inquilinos, um operador da nuvem poderá encontrar um ou mais subjacente inquilino as partilhas utilizando mais espaço que outros. Se isto ocorrer, o operador da nuvem pode tentar Liberte algum espaço na partilha de stressed migrando manualmente alguns contentores de BLOBs para outra partilha. 

Tem de utilizar o PowerShell para migrar os contentores.
> [!NOTE]
>A migração de contentor do blob não suporta a migração em direto e atualmente é uma operação offline. Durante a migração e até ter concluído os blobs no contentor subjacentes não podem ser utilizados e são "offline". 

**Para migrar contentores utilizando o PowerShell:**

1. Certifique-se de que tem o Azure PowerShell instalada e configurada. Caso contrário, utilize as instruções seguintes:
    * Para instalar a versão mais recente do Azure PowerShell e associá-lo à sua subscrição do Azure, consulte [como instalar e configurar o Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Para obter mais informações sobre cmdlets do Azure Resource Manager, consulte [utilizar o Azure PowerShell com o Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Obter o nome do farm: 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. Obter as partilhas de: 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. Obter os contentores para uma determinada partilha. Tenha em atenção que a contagem e a intenção são parâmetros opcionais:
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   Em seguida, examine $containers:

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. Obter as melhor partilhas de destino para a migração do contentor:

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    Em seguida, examine $destinationshares:

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. Iniciar a migração para um contentor, tenha em atenção de que esta é uma implementação de async, para que um possa cíclicas todos os contentores numa partilha e controlar o estado com o id da tarefa devolvido.

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    Em seguida, examine $jobId:

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. Verifique o estado da tarefa de migração pelo id da tarefa. Quando concluir a migração de contentor, MigrationStatus está definido como "Concluído".

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. Pode cancelar uma tarefa de migração em curso. Este novo, é uma operação assíncrona e pode ser controlado utilizando $jobid:

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    Pode verificar o estado de cancelar a migração novamente:

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  