<properties
   pageTitle="Configurar a Automatização do Azure"
   description="Descreve os passos que tem de efetuar para configurar a Automatização do Azure para utilização inicial."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="magoedte;bwren" />

# Configurar a Automatização do Azure

Este artigo descreve as ações que deve efetuar para começar a utilizar a Automatização do Azure.

## Contas de Automatização

Quando inicia a Automatização do Azure pela primeira vez, tem de criar pelo menos uma conta de Automatização. As contas de automatização permitem-lhe isolar os seus recursos de Automatização (runbooks, recursos, configurações) dos recursos de Automatização contidos noutras contas de Automatização. Pode utilizar contas de Automatização para separar recursos de Automatização em ambientes lógicos separados. Por exemplo, poderá utilizar uma conta para o desenvolvimento e outra para a produção.

Os recursos de Automatização para cada conta de Automatização estão associados a uma única região do Azure, mas as contas de Automatização podem gerir os recursos do Azure em qualquer região. A razão principal para criar contas de Automatização em diferentes regiões seria se tiver políticas que exigem que os dados e os recursos estejam isolados numa região específica.

>[AZURE.NOTE] As contas de Automatização, e os recursos que estas contêm, que são criadas no portal do Azure não podem ser acedidas no Portal Clássico do Azure. Se pretende gerir estas contas ou os respetivos recursos com o Windows PowerShell, tem de utilizar os módulos do Azure Resource Manager. 
>
>As contas de automatização criadas com o Portal Clássico do Azure podem ser geridas por qualquer um dos portais e por qualquer conjunto de cmdlets. Depois de a conta estar criada, não faz diferença como criar e gerir recursos na conta. Se estiver a planear continuar a utilizar o Portal Clássico do Azure, deve utilizá-lo em vez do portal do Azure para criar as contas de Automatização.


Uma conta de automatização poderá ser suspensa se existir um problema com a sua conta do Azure, tal como um pagamento em atraso. Neste caso, não poderá aceder à conta, todas as tarefas em execução serão suspensas e todas as agendas serão desativadas. Poderá ver a conta, mas não poderá ver quaisquer recursos na mesma. Depois de corrigir o problema e a conta de Automatização ser ativada, terá de ativar as suas agendas e reiniciar todos os runbooks que foram suspensos.


## Configurar a autenticação nos recursos do Azure

Quando acede a recursos do Azure utilizando os [cmdlets do Azure](http://msdn.microsoft.com/library/azure/jj554330.aspx), tem de fornecer autenticação na sua subscrição do Azure. Na Automatização do Azure, isto é feito com uma conta organizacional no Azure Active Directory que configura como administrador da sua subscrição. Em seguida, pode criar uma [credencial](http://msdn.microsoft.com/library/dn940015.aspx) para esta conta de utilizador e utilizá-la com [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) no runbook.

>[AZURE.NOTE] Não é possível utilizar Contas Microsoft, anteriormente conhecidas como LiveIDs, com a Automatização do Azure.

## Criar um novo utilizador do Azure Active Directory para gerir uma subscrição do Azure

1. Inicie sessão no Portal Clássico do Azure como um administrador de serviços para a subscrição do Azure que pretende gerir.
2. Selecione **Active Directory**
3. Selecione o nome de diretório que está associado à sua subscrição do Azure. Se necessário, pode alterar esta associação a partir de **Definições > Subscrições > Editar Diretório**.
4. [Crie um novo utilizador do Active Directory](http://msdn.microsoft.com/library/azure/hh967632.aspx).  Selecione **Novo utilizador na sua organização** para o **Tipo de utilizador** e não selecione **Ativar Multi-Factor Authentication**.
5. Tenha em atenção o nome completo e a palavra-passe temporária do utilizador.
7. Selecione **Definições > Administradores > Adicionar**.
8. Escreva o nome de utilizador completo do utilizador que criou.
9. Selecione a subscrição que pretende que o utilizador efetue a gestão.
10. Termine sessão no Azure e, em seguida, inicie sessão novamente com a conta que acabou de criar. Será solicitado a alterar a palavra-passe do utilizador.
11. Crie um novo [Recurso de Credencial da Automatização do Azure](automation-credentials.md) para a conta de utilizador que criou. O **Tipo de Credencial** deve ser **Credencial do Windows PowerShell**.

## Criar uma conta de automatização

Uma conta de automatização é um contentor para os seus recursos da Automatização do Azure. Fornece uma forma de separar os seus ambientes ou de organizar melhor os fluxos de trabalho. Se já tiver criado uma conta de automatização, pode ignorar este passo.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Clique em **Novo** > **Gestão** > **Conta de Automatização**

3. No painel **Adicionar Conta de Automatização**, configure os detalhes da sua Conta de Automatização. 

>[AZURE.NOTE] Quando uma conta de Automatização é criada utilizando o Portal do Azure, a conta e todos os recursos associados à mesma não são recuperados para o Portal Clássico do Azure. 

Segue-se a lista de parâmetros a configurar:

|Parâmetro            |Descrição |
|:---|:---|
| Nome | O nome da sua Conta de Automatização; tem de ser um valor exclusivo. |
| Grupo de Recursos | Os grupos de recursos tornam mais fácil ver e gerir recursos relacionados do Azure. No Portal do Azure, pode escolher um grupo de recursos existente ou criar um novo para a sua Conta de Automatização, enquanto no Portal Clássico do Azure todas as Contas de Automatização são colocadas num grupo de recursos predefinido. |
| Subscrição | Escolha uma subscrição na lista de subscrições disponíveis. |
| Região | A região especifica onde serão armazenados os recursos de Automatização na conta. Pode escolher qualquer região na lista, isto não afetará a funcionalidade da sua conta. No entanto, os runbooks poderão ser executados mais rapidamente se a região da conta estiver próxima do local onde estão armazenados os outros recursos do Azure. |
| Opções de Conta | Esta opção permite-lhe escolher quais os recursos que serão criados na sua nova Conta de Automatização; selecionar **Sim** irá criar um runbook de tutorial. |

![Criar Conta](media/automation-configuration/automation-01-create-automation-account.png)

>[AZURE.NOTE] Quando uma Conta de Automatização criada com o Portal Clássico do Azure é [movida para um grupo de recursos diferente](../resource-group-move-resources.md) utilizando o Portal do Azure, a Conta de Automatização deixa de estar disponível no Portal Clássico do Azure.



## Utilizar a credencial num runbook

Pode obter a credencial num runbook com a atividade [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) e, em seguida, utilizá-la com [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para ligar à sua subscrição do Azure. Se a credencial for um administrador de várias subscrições do Azure, também deve utilizar [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) para especificar a correta. Isto é apresentado na amostra do Windows PowerShell que normalmente irá aparecer no topo da maior parte dos runbooks da Automatização do Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Deve repetir estas linhas após qualquer [ponto de verificação](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) no runbook. Se o runbook está suspenso e, em seguida, retoma noutra função de trabalho, será necessário efetuar a autenticação novamente.

## Artigos relacionados
- [Automatização do Azure: Autenticação no Azure utilizando o Azure Active Directory](https://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 



<!--HONumber=Jun16_HO2-->


