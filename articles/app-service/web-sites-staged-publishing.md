---
title: Configurar ambientes para web apps no App Service do Azure de teste | Microsoft Docs
description: "Saiba como utilizar a publicação de teste para web apps no App Service do Azure."
services: app-service
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: b69f6b2190362b970420ba81450978ac9faecd86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes no App Service do Azure de teste
<a name="Overview"></a>

Ao implementar a aplicação web, a aplicação web no Linux, back-end móvel e aplicação de API para [do serviço de aplicações](http://go.microsoft.com/fwlink/?LinkId=529714), pode implementar para uma ranhura de implementação separadas em vez da ranhura de produção predefinido ao executar o **padrão** ou **Premium** modo de plano de serviço de aplicações. As ranhuras de implementação são aplicações realmente em direto com os seus próprios nomes de anfitrião. Elementos de conteúdo e as configurações de aplicação podem ser trocados entre duas ranhuras de implementação, incluindo a ranhura de produção. Implementar a aplicação para uma ranhura de implementação tem as seguintes vantagens:

* Pode validar alterações de aplicações numa ranhura de implementação de teste antes de troca-lo com a ranhura de produção.
* Implementar uma aplicação para uma ranhura pela primeira vez e trocar-o em produção assegura que todas as instâncias da ranhura são preparadas antes de a ser alternados em produção. Isto elimina o período de indisponibilidade quando implementar a sua aplicação. O redirecionamento de tráfego é totalmente integrado e não existem pedidos são ignorados devido a operações de comutação. Este fluxo de trabalho completo pode ser automatizado configurando [automática comutação](#Auto-Swap) quando a validação de pré-troca de não é necessária.
* Depois de uma troca a ranhura de aplicação previamente testada tem agora a aplicação de produção anterior. Se as alterações alternadas para a ranhura de produção não tem o aspeto esperado, pode efetuar a troca mesma imediatamente para obter o seu "último boa site conhecido" novamente.

Cada modo de plano de serviço de aplicações suporta um número diferente de ranhuras de implementação. Para saber o número de ranhuras suporta o modo da sua aplicação, consulte [preços do App Service](https://azure.microsoft.com/pricing/details/app-service/).

* Quando a aplicação tiver várias ranhuras, não é possível alterar o modo.
* Dimensionamento não está disponível para ranhuras de não produção.
* Gestão de recursos ligado não é suportada para as ranhuras de não produção. No [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) apenas, pode evitar esta potencial impacto na ranhura de produção, movendo temporariamente a ranhura de produção não para um modo de plano de serviço de aplicações diferentes. Tenha em atenção antes de pode trocar entre os dois blocos, a ranhura de produção não novamente deve partilhar o mesmo modo com a ranhura de produção.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Adicionar uma ranhura de implementação
A aplicação deve estar em execução a **padrão** ou **Premium** modo por ordem para que possa ativar várias ranhuras de implementação.

1. No [Portal do Azure](https://portal.azure.com/), abra a aplicação [painel de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Escolha o **ranhuras de implementação** opção, em seguida, clique em **adicionar ranhura**.
   
    ![Adicionar uma nova ranhura de implementação][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Se a aplicação já está a não ser o **padrão** ou **Premium** modo, receberá uma mensagem a indicar os modos suportados para ativar a publicação de teste. Neste momento, tem a opção para selecionar **atualizar** e navegue para o **escala** separador da sua aplicação antes de continuar.
   > 
   > 
3. No **adicionar uma ranhura** painel, atribua a ranhura de um nome e optar por clonar a configuração de aplicação a partir de outro bloco de implementação existente. Clique na marca de verificação para continuar.
   
    ![Origem de configuração][ConfigurationSource1]
   
    A primeira adicionar uma ranhura, apenas terá duas opções: configuração de clone da ranhura de predefinição na produção ou não de todo.
    Depois de criar várias ranhuras, será possível clonar a configuração a partir de uma ranhura diferente em produção:
   
    ![Origens de configuração][MultipleConfigurationSources]
4. No painel de recursos da sua aplicação, clique em **ranhuras de implementação**, em seguida, clique numa ranhura de implementação para abrir o painel de recursos nesse bloco, com um conjunto de métricas e a configuração, tal como qualquer outra aplicação. O nome da ranhura de é apresentado na parte superior do painel para relembrá-o de que está a visualizar a ranhura de implementação.
   
    ![Título da ranhura de implementação][StagingTitle]
5. Clique no URL da aplicação no painel da ranhura. Tenha em atenção que a ranhura de implementação tem o seu próprio nome de anfitrião e também é uma aplicação em direto. Para limitar o acesso público para a ranhura de implementação, consulte [aplicação Web do App Service – bloquear o acesso web para as ranhuras de implementação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Não há nenhum conteúdo após a criação da ranhura de implementação. Pode implementar para a ranhura a partir de um ramo do repositório diferente ou um repositório completamente diferente. Também pode alterar a configuração da ranhura. Utilize as credenciais de implementação ou o perfil de publicar associadas a ranhura de implementação para as atualizações de conteúdo.  Por exemplo, pode [publicar esta ranhura com o git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="configuration-for-deployment-slots"></a>Configuração para as ranhuras de implementação
Ao clonar a configuração a partir de outro bloco de implementação, a configuração clonada é editável. Além disso, alguns elementos de configuração irão seguir o conteúdo através de uma troca (não ranhura específica) enquanto outros elementos de configuração e irão permanecer na mesma ranhura após uma troca (específica da ranhura). As listas seguintes mostram a configuração que será alterado quando trocar ranhuras.

**As definições que são trocadas**:

* Sockets de Web de definições gerais - por exemplo, a versão do framework, 32/64 bits,
* Definições de aplicação (pode ser configurado para stick para uma ranhura)
* Cadeias de ligação (pode ser configurado para stick para uma ranhura)
* Mapeamentos do processador
* Definições de monitorização e diagnóstico
* Conteúdo de WebJobs

**As definições que não estão a ser alternadas**:

* Pontos finais de publicação
* Nomes de domínio personalizados
* Certificados SSL e os enlaces
* Definições de dimensionamento
* Programadores de WebJobs

Para configurar uma cadeia de ligação ou definição de aplicação para stick para uma ranhura (não alternada), aceda a **definições da aplicação** painel para uma ranhura específica, em seguida, selecione o **ranhura definição** caixa para a configuração elementos devem stick a ranhura. Tenha em atenção que um elemento de configuração marcar como ranhura específica tem o efeito de estabelecer que o elemento não swappable como em todas as ranhuras de implementação associadas à aplicação.

![Definições de ranhura][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Trocar ranhuras de implementação 
Pode trocar ranhuras de implementação no **descrição geral** ou **ranhuras de implementação** vista do painel de recursos da sua aplicação.

> [!IMPORTANT]
> Antes de trocar uma aplicação de uma ranhura de implementação em produção, certifique-se de que todas as definições específicas do bloco não estão configuradas exatamente como pretende que o destino de comutação.
> 
> 

1. Ao trocar ranhuras de implementação, clique em de **troca** botão na barra de comando da aplicação ou na barra de comando de uma ranhura de implementação.
   
    ![Botão de comutação][SwapButtonBar]

2. Certifique-se de que a origem de comutação e o destino de comutação estão definidas corretamente. Normalmente, o destino de comutação é a ranhura de produção. Clique em **OK** para concluir a operação. Quando concluída a operação, tem sido alternadas ranhuras de implementação.

    ![Concluir troca](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para o **troca com pré-visualização** comutação tipo, consulte [troca com pré-visualização (fase multi swap)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Troca com pré-visualização (fase multi swap)

Troca com pré-visualização ou fase multi troca, simplificar a validação de elementos de configuração específicos da ranhura, tais como cadeias de ligação.
Para cargas de trabalho pesadas, que pretende validar que a aplicação se comporta conforme esperado quando a configuração da ranhura de produção é aplicada, e tem de executar essa validação *antes* a aplicação é alternada em produção. Troca com pré-visualização é o que precisa.

> [!NOTE]
> Troca com pré-visualização não é suportada nas web apps no Linux.

Quando utiliza o **troca com pré-visualização** opção (consulte [trocar ranhuras de implementação](#Swap)), serviço de aplicações efetua o seguinte procedimento:

- Mantém a ranhura de destino inalterada, para a carga de trabalho existente nesse ranhura (por exemplo, produção) não é afetada.
- Aplica-se os elementos de configuração da ranhura de destino para a ranhura de origem, incluindo as definições de aplicação e as cadeias de ligação de específicos da ranhura.
- Reinicia os processos de trabalho a ranhura de origem utilizando estes elementos de configuração acima mencionados.
- Quando concluir a troca: desloca a ranhura de origem de pré-warmed cópia de segurança para a ranhura de destino. Ranhura de destino é movida para a ranhura de origem como uma troca manual.
- Se cancelar a troca: volta os elementos de configuração da ranhura de origem para a ranhura de origem.

Pode pré-visualizar exatamente como a aplicação irá comportar-se com a configuração da ranhura de destino. Depois de concluir a validação, conclua a troca num passo separado. Este passo tem a vantagem adicional que a ranhura de origem já está preparada com a configuração pretendida e os clientes não irão experimentar qualquer período de inatividade.  

Exemplos de cmdlets PowerShell do Azure disponíveis para comutação multi fase são incluídos nos cmdlets PowerShell do Azure para a secção de ranhuras de implementação.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar a troca automática
Troca automática simplifica a cenários de DevOps onde pretende implementar continuamente a sua aplicação com zero frio e períodos de indisponibilidade para os clientes de fim da aplicação. Quando uma ranhura de implementação está configurada para a troca automática em produção, sempre que push a atualização de código para esse ranhura, o serviço de aplicações será automaticamente a trocar a aplicação em produção, depois de já ter preparada na ranhura de.

> [!IMPORTANT]
> Quando ativa a comutação automática para uma ranhura, certifique-se a configuração da ranhura exatamente a configuração pretendida para a ranhura de destino (normalmente, a ranhura de produção).
> 
> 

> [!NOTE]
> Troca automática não é suportada nas web apps no Linux.

É fácil a configuração automática de comutação para uma ranhura. Siga os passos abaixo:

1. No **ranhuras de implementação**, selecione uma ranhura de não produção e escolha **definições da aplicação** no painel de recursos que ranhura.  
   
    ![][Autoswap1]
2. Selecione **no** para **automática comutação**, selecione a ranhura de destino pretendida no **automática trocar a ranhura**e clique em **guardar** na barra de comando. Certifique-se a configuração para a ranhura exatamente a configuração pretendida para a ranhura de destino.
   
    O **notificações** separador irá flash um verde **êxito** depois de concluída a operação.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Para testar a comutação de automaticamente para a sua aplicação, primeiro selecione uma ranhura de destino de não produção no **automática trocar a ranhura** para se familiarizar com a funcionalidade.  
   > 
   > 
3. Execute um push de código para esse ranhura de implementação. Troca automática irá ocorrer após um curto período de tempo e a atualização será apresentada no URL da ranhura de destino.

<a name="Rollback"></a>

## <a name="to-rollback-a-production-app-after-swap"></a>Reverter uma aplicação de produção após a troca
Se os erros são identificados em produção após uma troca de ranhura, reverta ranhuras para os respetivos Estados de pré-comutação de ao trocar dois ranhuras mesmas imediatamente.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Warm-up personalizado antes de comutação
Algumas aplicações podem exigir ações warm-up personalizado. O `applicationInitialization` elemento de configuração em Web. config permite-lhe especificar as ações de inicialização personalizada para ser executada antes de um pedido é recebido. A operação de troca espera que este warm-up personalizado concluir. Eis um fragmento de Web. config de exemplo.

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>

## <a name="to-delete-a-deployment-slot"></a>Para eliminar uma ranhura de implementação
No painel de uma ranhura de implementação, abra o painel da ranhura de implementação, clique em **descrição geral** (a página predefinida) e clique em **eliminar** na barra de comando.  

![Eliminar uma ranhura de implementação][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="azure-powershell-cmdlets-for-deployment-slots"></a>Cmdlets do PowerShell do Azure para as ranhuras de implementação
O Azure PowerShell é um módulo que fornece cmdlets para gerir o Azure através do Windows PowerShell, incluindo suporte para gerir as ranhuras de implementação no App Service do Azure.

* Para obter informações sobre como instalar e configurar o Azure PowerShell e sobre a autenticação do Azure PowerShell com a sua subscrição do Azure, consulte [como instalar e configurar o Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Criar uma aplicação Web
```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Criar uma ranhura de implementação
```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-review-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Inicie uma troca com a revisão (fase multi swap) e aplicar a configuração da ranhura de destino a ranhura de origem
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Cancelar uma troca pendente (troca com a revisão) e restaurar a configuração da ranhura de origem
```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Trocar ranhuras de implementação
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="delete-deployment-slot"></a>Elimine o bloco de implementação
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="azure-command-line-interface-azure-cli-commands-for-deployment-slots"></a>Comandos de Interface de linha de comandos (CLI do Azure) do Azure para as ranhuras de implementação
A CLI do Azure fornece comandos de várias plataformas para trabalhar com o Azure, incluindo suporte para gerir as ranhuras de implementação do serviço de aplicações.

* Para obter instruções sobre como instalar e configurar a CLI do Azure, incluindo informações sobre como ligar a CLI do Azure a sua subscrição do Azure, consulte [instalar e configurar a CLI do Azure](../cli-install-nodejs.md).
* Para listar os comandos disponíveis para o Azure App Service na CLI do Azure, chamar `azure site -h`.

> [!NOTE] 
> Para [Azure CLI 2.0](https://github.com/Azure/azure-cli) comandos para as ranhuras de implementação, consulte [ranhura de implementação do az appservice web](/cli/azure/appservice/web/deployment/slot).

- - -
### <a name="azure-site-list"></a>lista de sites do Azure
Para obter informações sobre as aplicações na subscrição atual, chamar **a lista de sites do azure**, como no exemplo seguinte.

`azure site list webappslotstest`

- - -
### <a name="azure-site-create"></a>criar o site do Azure
Para criar uma ranhura de implementação, chame **do azure site criar** e especifique o nome de uma aplicação existente e o nome da ranhura para criar, como no exemplo seguinte.

`azure site create webappslotstest --slot staging`

Para ativar o controlo de origem para a nova ranhura, utilize o **– git** opção, como no exemplo seguinte.

`azure site create --git webappslotstest --slot staging`

- - -
### <a name="azure-site-swap"></a>troca de site do Azure
Para efetuar a implementação atualizada espaço a aplicação de produção, utilize o **troca de site do azure** comando para efetuar uma operação de troca, como no exemplo seguinte. A aplicação de produção não irá ocorrer algum tempo, nem vai sofrer-uma arranque a frio.

`azure site swap webappslotstest`

- - -
### <a name="azure-site-delete"></a>eliminação de site do Azure
Para eliminar uma ranhura de implementação que já não é necessária, utilize o **eliminação de site do azure** comando, como no exemplo seguinte.

`azure site delete webappslotstest --slot staging`

- - -
> [!NOTE]
> Veja uma aplicação Web em ação. [Experimentar o App Service](https://azure.microsoft.com/try/app-service/) imediatamente e crie uma aplicação de arranque de curta duração — sem cartões de crédito, sem compromissos.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
[Web do App Service do Azure aplicação – bloquear o acesso web para as ranhuras de implementação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
[introdução ao serviço de aplicações no Linux](../app-service/containers/app-service-linux-intro.md)
[avaliação gratuita do Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

