---
title: Azure DevTest Labs FAQ | Microsoft Docs
description: "Encontrar respostas a questões recorrentes sobre o Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: tarcher
ms.openlocfilehash: 1f261f97bbd9233d47eadc7e902e00ee87af9e34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-devtest-labs-faq"></a>FAQ de Azure DevTest Labs
Obtenha respostas a algumas perguntas mais comuns sobre o Azure DevTest Labs.

**Geral**
## <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não é atendida aqui?
Se a sua pergunta não estiver listada aqui, permitem-nos informar, pelo que podemos ajudar a encontrar uma resposta.

* Publique uma pergunta no fim destas perguntas mais frequentes. Envolver a equipa de Cache do Azure e outros membros da Comunidade sobre neste artigo.
* Para aceder uma vasta público-alvo, publique uma pergunta no [fórum MSDN do Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Envolver a equipa do Azure DevTest Labs e outros membros da Comunidade.
* Para pedidos de funcionalidades, submeter os pedidos e ideias para [voz do utilizador do Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Por que motivo devo utilizar Azure DevTest Labs?
Azure DevTest Labs pode guardar o tempo de equipa e dinheiro. Os programadores podem criar os seus próprios ambientes utilizando várias bases de diferentes. Também podem utilizar artefactos para implementar e configurar as aplicações rapidamente. Ao utilizar imagens personalizadas e fórmulas, pode guardar máquinas virtuais (VMs) como modelos e reproduza-los facilmente entre o agrupamento. DevTest Labs também oferece várias políticas configuráveis que os administradores podem utilizar para reduzir as perdas e gerir ambientes de uma equipa de laboratório. Estas políticas incluem o encerramento automático, o limiar de custo, máximos de VMs por utilizador e o tamanho máximo da VM. Para obter uma explicação mais aprofundada de DevTest Labs, consulte o [descrição geral](devtest-lab-overview.md) ou [as vídeo introdutórias](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>O que significa "preocupe-gratuita personalizada"?
Self-Service gratuita para preocupe significa que os programadores e testers criar os seus próprios ambientes conforme necessário. Os administradores têm a segurança de saber que o DevTest Labs pode ajudar a minimizar as perdas e controlo de custos. Os administradores podem especificar são permitidos os tamanhos de VM, o número máximo de VMs, e quando as VMs estão iniciadas e encerrar. DevTest Labs também facilita a monitorizar os custos e definir alertas, para o ajudar a manter ciente de como estão a ser utilizados os recursos de laboratório.

## <a name="how-can-i-use-devtest-labs"></a>Como utilizar o DevTest Labs?
DevTest Labs é útil sempre que necessita de desenvolvimento ou ambientes de teste e pretende reproduzi-las rapidamente, ou geri-los utilizando as políticas de custo guardar.

Seguem-se alguns cenários em que os nossos clientes utilizam DevTest Labs para:

* Gerir dev e ambientes de teste num único local. Utilize as políticas para reduzir os custos e criar imagens personalizadas para partilhar baseia-se entre o agrupamento.
* Desenvolva uma aplicação através da utilização de imagens personalizadas para guardar o estado do disco durante as fases de desenvolvimento.
* Controlar o custo em relação a progresso.
* Crie ambientes de teste em massa para testar a qualidade garantia.
* Utilizar artefactos e fórmulas e configurar facilmente reproduzir uma aplicação em vários ambientes.
* Distribuir as VMs para hackathons (trabalho de colaboração de desenvolvimento ou teste) e, em seguida, facilmente retirar o aprovisionamento-las quando o evento termina.

## <a name="how-am-i-billed-for-devtest-labs"></a>Como estou cobrado para DevTest Labs?
DevTest Labs é um serviço gratuito. Laboratórios de criar e configurar os artefactos, modelos e políticas no DevTest Labs é gratuita. Só paga pelos recursos do Azure utilizados em sua laboratórios, como VMs, contas de armazenamento e redes virtuais. Para obter mais informações sobre o custo de recursos de laboratório, consulte [preços do Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Segurança**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quais são os níveis de segurança diferentes no DevTest Labs?
Acesso de segurança é determinado por [controlo de acesso baseado em funções (RBAC)](../active-directory/role-based-access-built-in-roles.md). Para saber como funciona o acesso, ajuda para saber as diferenças entre uma permissão, uma função e um âmbito, conforme definido pelo RBAC.

* **Permissão**: uma permissão é um acesso definidas para uma ação específica. Por exemplo, uma permissão pode ser acesso de leitura para todas as VMs.
* **Função**: uma função é um conjunto de permissões que podem ser agrupados e atribuído a um utilizador. Por exemplo, um utilizador com uma função de proprietário da subscrição tem acesso a todos os recursos numa subscrição.
* **Âmbito**: um âmbito é um nível na hierarquia de um recurso do Azure. Por exemplo, um âmbito pode ser um grupo de recursos, um laboratório único ou a subscrição completa.

No âmbito da DevTest Labs, existem dois tipos de funções que definem as permissões de utilizador:

* **Proprietário do laboratório**: um proprietário de laboratório tem acesso a todos os recursos no laboratório. Um proprietário de laboratório pode modificar as políticas, ler e escrever em quaisquer VMs, altere a rede virtual e assim sucessivamente.
* **Utilizador do laboratório**: um utilizador do laboratório, pode ver todos os recursos de laboratório, como VMs, políticas e redes virtuais. No entanto, um utilizador do laboratório não é possível modificar as políticas ou quaisquer VMs que foram criadas por outros utilizadores. 

Também pode criar funções personalizadas no DevTest Labs. Para saber como criar funções personalizadas no DevTest Labs, consulte [conceder permissões de utilizador para políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Porque os âmbitos são hierárquicos, quando um utilizador tem permissões num determinado âmbito, é automaticamente concedido ao utilizador essas permissões a cada âmbito de nível inferior no âmbito. Por exemplo, se um utilizador é atribuído a função de proprietário da subscrição, o utilizador tem acesso a todos os recursos numa subscrição. Estes recursos incluem todas as VMs, todas as redes virtuais e todos os laboratórios. Um proprietário da subscrição herda automaticamente a função de proprietário do laboratório. No entanto, o oposto não é verdadeiro. Um proprietário de laboratório tem acesso a um laboratório, que é um âmbito inferior ao nível da subscrição. Por conseguinte, um proprietário do laboratório não pode ver as VMs, redes virtuais ou outros recursos que estão fora do laboratório.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Como posso criar uma função para permitir que os utilizadores efetuar uma tarefa específica?
Para um artigo abrangente sobre como criar funções personalizadas e atribua permissões a uma função, consulte [conceder permissões de utilizador para políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Eis um exemplo de um script que cria a função *DevTest Labs avançadas utilizador*, que tem permissão para iniciar e parar todas as VMs no laboratório:

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  


**Integração de CI/CD e automatização**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs é integrada com os meus toolchain CI/CD?
Se estiver a utilizar o Visual Studio Team Services, pode utilizar um [extensão DevTest Labs tarefas](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) para automatizar o pipeline de versão no DevTest Labs. Algumas das tarefas que pode fazer com esta extensão incluem:

* Criar e implementar uma VM automaticamente. Também pode configurar a VM com a compilação mais recente através da utilização de tarefas de cópia de ficheiros do Azure ou do PowerShell Team Services.
* Capture automaticamente o estado de uma VM depois de testar para reproduzir um erro na mesma VM para uma investigação mais aprofundada.
* Elimine a VM no fim do pipeline de versão, quando este já não é necessário.

Mensagens do blogue seguinte orientações de oferta e informações sobre como utilizar a extensão de Team Services:

* [DevTest Labs e a extensão do Visual Studio Team Services](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Implementar uma nova VM, um laboratório DevTest Labs existente da equipa de serviços](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Gestão de versões de serviços da equipa a utilizar para implementações contínuas para DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Para outra integração contínua (CI) / toolchains entrega contínua (CD), pode conseguir os mesmos cenários por implementar [modelos Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) utilizando [cmdlets Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [.NET SDKs](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Também pode utilizar [APIs REST para DevTest Labs](http://aka.ms/dtlrestapis) para integrar com o seu toolchain.  


**Máquinas virtuais**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Por que motivo não é possível ver as VMs no painel de máquinas virtuais que vejo no DevTest Labs?
Quando cria uma VM no DevTest Labs, é-lhe dada a permissão para aceder a essa VM. Pode ver a VM no painel laboratórios tanto no **máquinas virtuais** painel. Utilizadores atribuídos à função de utilizador do laboratório DevTest Labs podem ver todas as VMs que foram criadas no laboratório no laboratório de **todas as máquinas virtuais** painel. No entanto, os utilizadores que têm a função de utilizador do laboratório DevTest Labs não recebem automaticamente acesso de leitura aos recursos da VM que tem criado outros utilizadores. Por conseguinte, não são apresentadas dessas VMs no **máquinas virtuais** painel.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>O que é a diferença entre uma imagem personalizada e uma fórmula?
Uma imagem personalizada é um disco rígido virtual (VHD). Uma fórmula é uma imagem que pode configurar as definições adicionais e, em seguida, guardar e reproduzir. Uma imagem personalizada poderá ser preferível se pretender criar rapidamente vários ambientes, utilizando a mesma imagem básica, imutável. Uma fórmula poderá ser melhor se pretender reproduzir a configuração da sua VM com os mais recentes bits, como parte de uma rede virtual ou uma sub-rede ou como uma VM de um tamanho específico. Para obter uma explicação mais aprofundada, consulte [comparar imagens personalizadas e as fórmulas da DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como criar várias VMs do mesmo modelo de uma só vez?
Tem duas opções para criar em simultâneo várias VMs do modelo mesmo:
* Pode utilizar o [extensão de tarefas de serviços de equipa do Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Pode [gerar um modelo do Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) enquanto estiver a criar uma VM, e [implementar o modelo do Resource Manager a partir do Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Como mover a minha VMs do Azure existente para a minha laboratório DevTest Labs
Para copiar as VMs existentes para DevTest Labs:

1. Copie o ficheiro VHD de VM existente utilizando um [script do Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Criar a imagem personalizada](devtest-lab-create-template.md) dentro do laboratório DevTest Labs.
3. Crie uma VM no laboratório da sua imagem personalizada.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Pode anexar vários discos para minhas VMs?
Sim, pode anexar vários discos para as suas VMs.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Se pretender utilizar uma imagem de sistema operativo Windows para o meu teste, tenho de comprar uma subscrição do MSDN?
Para utilizar imagens de SO de cliente Windows (Windows 7 ou uma versão posterior) para a programação ou testes no Azure, terá de efetuar um dos seguintes:

- [Comprar uma subscrição do MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Se tiver um Enterprise Agreement, criar uma subscrição do Azure com o [oferta Enterprise programador/teste](https://azure.microsoft.com/en-us/offers/ms-azr-0148p).

Para obter mais informações sobre os créditos do Azure para cada oferta do MSDN, consulte [crédito do Azure mensal para subscritores do Visual Studio](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como posso automatizar o processo de carregamento de ficheiros VHD para criar imagens personalizadas?
Para automatizar o carregamento de ficheiros VHD para criar imagens personalizadas, tem duas opções:

* Utilize [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) copiar ou carregar ficheiros VHD para a conta de armazenamento associado ao laboratório.
* Utilize [Explorador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). Explorador de armazenamento é uma aplicação autónoma que é executado no Windows, OS X e Linux.   

Para localizar a conta de armazenamento de destino associado ao seu laboratório:

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. No menu da esquerda, selecione **grupos de recursos**.
3. Localize e selecione o grupo de recursos que tem associado o laboratório.
4. Em **descrição geral**, selecione uma das contas de armazenamento.
5. Selecione **Blobs**.
6. Procure carregamentos na lista. Se não existe nenhum, volte ao passo 4 e tente outra conta de armazenamento.
7. Utilize o **URL** como destino o comando do AzCopy.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como posso automatizar o processo de eliminação de todas as VMs no meu laboratório?
Pode eliminar as VMs do seu laboratório no portal do Azure. Também pode eliminar todas as VMs no laboratório, utilizando um script do PowerShell. No exemplo seguinte, sob o **valores para alterar** comente, modifique os valores de parâmetros. Pode obter o `subscriptionId`, `labResourceGroup`, e `labName` valores a partir do painel de laboratório no portal do Azure.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Login-AzureRmAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }

**Artefactos**
## <a name="what-are-artifacts"></a>Quais são os artefactos?
Artefactos são elementos personalizáveis, que pode utilizar para implementar as mais recentes bits ou implementar as ferramentas de programador para uma VM. Anexe artefactos à VM quando criar a VM. Depois da VM é aprovisionada, os artefactos implementam e configurar a VM. Vários artefactos pré-existentes estão disponíveis no nosso [público repositório do GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Também pode [criar os seus artefactos](devtest-lab-artifact-author.md).


**Configuração de laboratório**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Como criar um laboratório a partir de um modelo do Resource Manager?
Que oferecemos uma [repositório do GitHub de modelos do Azure Resource Manager laboratório](https://aka.ms/dtlquickstarttemplate) que pode implementar como-está ou modificar para criar modelos personalizados para o seu laboratórios. Cada modelo tem uma ligação para implementar o laboratório como-está na sua própria subscrição do Azure. Em alternativa, pode personalizar o modelo e [implementar utilizando o PowerShell ou a CLI do Azure](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Por que motivo o minhas VMs são criados em grupos de recursos diferente, com nomes arbitrários? Pode mudar o nome ou modificar estes grupos de recursos?
Grupos de recursos são criados desta forma, para que DevTest Labs pode gerir as permissões de utilizador e o acesso a VMs. Apesar de poder mover uma VM para outro grupo de recursos e utilize o nome que pretende, recomendamos que não efetuar alterações aos grupos de recursos. Estamos a trabalhar em melhorar esta experiência para permitir mais flexibilidade.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Laboratórios quantos pode criar na mesma subscrição
Não é um limite no número de laboratórios que podem ser criadas por subscrição específico. No entanto, a quantidade de recursos utilizados por subscrição está limitada. Pode ler sobre o [limites e quotas para as subscrições do Azure](../azure-subscription-service-limits.md) e [como aumentar estes limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Quantas VMs pode criar por laboratório
Não há nenhum limite no número de VMs que podem ser criadas por laboratório específico. No entanto, os recursos (núcleos VM, endereços IP públicos e assim sucessivamente) que são utilizados estão limitados por subscrição. Pode ler sobre o [limites e quotas para as subscrições do Azure](../azure-subscription-service-limits.md) e [como aumentar estes limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como partilharem uma ligação direta para a minha laboratório?

1. No portal do Azure, vá para o laboratório.
2. Copie o URL de laboratório a partir do seu browser e, em seguida, partilhe-a com os seus utilizadores de laboratório.

> [!NOTE]
> Se um utilizador do laboratório é um utilizador externo que tenha um [conta Microsoft](#what-is-a-microsoft-account), mas que não seja um membro de instância do Active Directory da sua organização, o utilizador poderá ver uma mensagem de erro quando tentarem aceder a ligação partilhada. Se um utilizador externo vê uma mensagem de erro, peça ao utilizador que selecione primeiro o respetivo nome no canto superior direito do portal do Azure. Em seguida, no **diretório** secção do menu, o utilizador pode selecionar o diretório onde o laboratório existe.
>
>

## <a name="what-is-a-microsoft-account"></a>O que é uma conta Microsoft?
Uma conta Microsoft é uma conta que utiliza para quase tudo o que fazer com o Microsoft dispositivos e serviços. É um endereço de correio eletrónico e palavra-passe que utiliza para iniciar sessão Skype, Outlook.com, OneDrive, Windows phone e Xbox Live. Uma única conta significa que as suas ficheiros, fotografias, contactos e as definições de podem seguir em qualquer dispositivo.

> [!NOTE]
> Uma conta Microsoft era anteriormente denominado um *Windows Live ID*.
>
>


**Resolução de problemas**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>A minha artefactos falhou durante a criação de VM. Como resolvê-lo?
Para saber como obter registos para o artefacto falha, consulte o artigo [como diagnosticar falhas de artefacto no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Por que motivo não está a minha existente virtual de rede guardar corretamente?
Uma possibilidade é que o nome da sua rede virtual contém pontos finais. Se assim for, experimente remover os períodos ou substituir com hífenes. Em seguida, tente novamente guardar a rede virtual.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Por que motivo recebo um erro de "Recurso principal não foi encontrado" quando aprovisionar uma VM a partir do PowerShell?
Quando um recurso é um elemento principal para outro recurso, o recurso principal tem de existir antes de criar o recurso subordinado. Se o recurso principal não existe, verá um **ParentResourceNotFound** mensagem. Se não especificar uma dependência no recurso principal, o recurso subordinado pode ser implementado antes do elemento principal.

As VMs são recursos subordinados sob um laboratório num grupo de recursos. Quando utilizar modelos do Resource Manager para implementar as VMs com o PowerShell, o nome do grupo de recursos fornecido no PowerShell script deve ser o nome do grupo de recursos do laboratório. Para obter mais informações, consulte [resolver erros comuns de implementação do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Onde posso encontrar mais informações de erro se falhar de uma implementação de VM?
Erros de implementação de VM são capturados nos registos de atividade. Pode encontrar registos de atividade VM em laboratório **registos de auditoria** ou **diagnósticos de máquinas virtuais** no menu de recurso no painel VM o laboratório (o painel aparece depois de selecionar VM a partir do **meu virtual máquinas** lista).

Por vezes, o erro de implementação ocorre antes do início da implementação da VM. Um exemplo é quando o limite de subscrição para um recurso que foi criado com a VM é excedido. Neste caso, os detalhes do erro são capturados nos registos de atividade de nível de laboratório. Atividade registos estão localizados na parte inferior do **políticas de configuração e** definições. Para obter mais informações sobre como utilizar a atividade que inicia sessão no Azure, consulte [ver registos de atividade para auditar as ações em recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
