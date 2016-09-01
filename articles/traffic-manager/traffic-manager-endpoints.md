<properties
   pageTitle="Gerir pontos finais no Traffic Manager do Azure | Microsoft Azure"
   description="Este artigo ajuda-o a adicionar, remover, ativar e desativar pontos finais no Traffic Manager do Azure."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# Adicionar, desativar, ativar ou eliminar pontos finais

A funcionalidade Web Apps no Serviço de Aplicações do Azure já fornece ativação pós-falha e a funcionalidade de encaminhamento de tráfego round robin para sites num centro de dados, independentemente do modo do site. O Traffic Manager do Azure permite-lhe especificar a ativação pós-falha e o encaminhamento de tráfego round robin para sites e serviços em nuvem em diferentes centros de dados. O primeiro passo necessário para fornecer essa funcionalidade é adicionar o ponto final de serviço em nuvem ou do site ao Traffic Manager.

>[AZURE.NOTE] Não é possível adicionar localizações externas ou perfis do Traffic Manager como pontos finais utilizando o Portal Clássico do Azure. Tem de utilizar a API REST [Criar Definição](http://go.microsoft.com/fwlink/p/?LinkId=400772) ou o Windows PowerShell [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774).

Também pode desativar pontos finais individuais que fazem parte de um perfil do Traffic Manager. Os pontos finais incluem serviços em nuvem e sites. A desativação de um ponto final deixa-o como parte do perfil, mas o perfil funciona como se o ponto final não estivesse incluído. Esta ação é muito útil para remover temporariamente um ponto final que está no modo de manutenção ou que vai ser implementado. Quando o ponto final está novamente em funcionamento, pode ativá-lo.

>[AZURE.NOTE] Desativar um ponto final não tem nada a ver com o estado de implementação no Azure. Um ponto final em bom estado continuará ativo para receber tráfego, mesmo quando desativado no Traffic Manager. Além disso, a desativação de um ponto final num perfil não afeta o estado noutro perfil.

## Para adicionar um ponto final de serviço em nuvem ou de site


1. No painel Traffic Manager no Portal Clássico do Azure, localize o perfil do Traffic Manager que contém as definições do ponto final que pretende modificar e, em seguida, clique na seta à direita do nome do perfil. Esta ação abre a página de definições do perfil.
2. Na parte superior da página, clique em **Pontos Finais** para ver os pontos finais que já fazem parte da configuração.
3. Na parte inferior da página, clique em **Adicionar** para aceder à página **Adicionar Pontos Finais de Serviço**. Por predefinição, a página lista os serviços em nuvem em **Pontos Finais de Serviço**.
4. Para serviços em nuvem, selecione os serviços em nuvem na lista para ativá-los como pontos finais para este perfil. Limpar o nome do serviço em nuvem remove-o da lista de pontos finais.
5. Para os sites, clique na lista pendente **Tipo de Serviço** e selecione **Aplicação Web**.
6. Selecione os sites na lista para adicioná-los como pontos finais para este perfil. Limpar o nome do site remove-o da lista de pontos finais. Tenha em atenção que só pode selecionar um único site para cada datacenter do Azure (também conhecido como região). Se selecionar um site no datacenter que aloja vários sites, quando selecionar o primeiro site, os outros no mesmo datacenter ficam indisponíveis para seleção. Tenha também em atenção que são listados apenas os Web Sites Standard.
7. Depois de selecionar os pontos finais para este perfil, clique na marca de verificação no canto inferior direito para guardar as alterações.

>[AZURE.NOTE] Se estiver a utilizar o método de encaminhamento de tráfego de *Ativação Pós-falha*, depois de adicionar ou remover um ponto final, certifique-se de ajustar a Lista de Prioridade de Ativação Pós-falha na página Configuração, para refletir a ordem de ativação pós-falha pretendida para a configuração. Para obter mais informações, consulte [Configurar o Encaminhamento de Tráfego de Ativação Pós-falha](traffic-manager-configure-failover-routing-method.md).

## Para desativar um ponto final

1. No painel Traffic Manager no Portal Clássico do Azure, localize o perfil do Traffic Manager que contém as definições do ponto final que pretende modificar e, em seguida, clique na seta à direita do nome do perfil. Esta ação abre a página de definições do perfil.
2. Na parte superior da página, clique em **Pontos Finais** para ver os pontos finais que estão incluídos na configuração.
3. Clique no ponto final que pretende desativar e, em seguida, clique em **Desativar** na parte inferior da página.
4. O tráfego deixará de fluir para o ponto final com base no TTL do DNS configurado para o nome de domínio do Traffic Manager. Pode alterar o TTL na página Configuração do perfil do Traffic Manager.

## Para ativar um ponto final

1. No painel Traffic Manager no Portal Clássico do Azure, localize o perfil do Traffic Manager que contém as definições do ponto final que pretende modificar e, em seguida, clique na seta à direita do nome do perfil. Esta ação abre a página de definições do perfil.
2. Na parte superior da página, clique em **Pontos Finais** para ver os pontos finais que estão incluídos na configuração.
3. Clique no ponto final que pretende ativar e, em seguida, clique em **Ativar** na parte inferior da página.
4. O tráfego começará a fluir novamente para o serviço, como indica o perfil.

## Para eliminar um ponto final de serviço em nuvem ou do site


1. No painel Traffic Manager no Portal Clássico do Azure, localize o perfil do Traffic Manager que contém as definições do ponto final que pretende modificar e, em seguida, clique na seta à direita do nome do perfil. Esta ação abre a página de definições do perfil.
2. Na parte superior da página, clique em **Pontos Finais** para ver os pontos finais que já fazem parte da configuração.
3. Na página Pontos Finais, clique no nome do ponto final que pretende eliminar do perfil.
4. Na parte inferior da página, clique em **Eliminar**.

>[AZURE.NOTE] Não é possível eliminar localizações externas ou perfis do Traffic Manager como pontos finais utilizando o Portal Clássico do Azure. Tem de utilizar o Windows PowerShell. Para obter mais informações, consulte [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## Passos seguintes


[Configurar o método de encaminhamento de ativação pós-falha](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de encaminhamento de round robin](traffic-manager-configure-round-robin-routing-method.md)

[Configurar o método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Resolução de problemas do estado degradado do Traffic Manager](traffic-manager-troubleshooting-degraded.md)

[Operações do Traffic Manager (Referência da API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)



<!--HONumber=ago16_HO4-->


