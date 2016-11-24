---
title: Gerir pontos finais no Traffic Manager do Azure | Microsoft Docs
description: Este artigo ajuda-o a adicionar, remover, ativar e desativar pontos finais no Traffic Manager do Azure.
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 47a2090e4f5a6da2879201aba5dbcd7beebcfa58

---

# <a name="add-disable-enable-or-delete-endpoints"></a>Adicionar, desativar, ativar ou eliminar pontos finais

A funcionalidade Web Apps no Serviço de Aplicações do Azure já fornece ativação pós-falha e a funcionalidade de encaminhamento de tráfego round robin para sites num centro de dados, independentemente do modo do site. O Traffic Manager do Azure permite-lhe especificar a ativação pós-falha e o encaminhamento de tráfego round robin para sites e serviços em nuvem em diferentes centros de dados. O primeiro passo necessário para fornecer essa funcionalidade é adicionar o ponto final de serviço em nuvem ou do site ao Traffic Manager.

> [!NOTE]
> Este artigo explica como utilizar o portal clássico. O Portal clássico do Azure só suporta a criação e a atribuição de serviços cloud e de aplicações Web como pontos finais. O novo [Portal do Azure](https://portal.azure.com) é a interface preferencial.

Também pode desativar pontos finais individuais que fazem parte de um perfil do Traffic Manager. A desativação de um ponto final deixa-o como parte do perfil, mas o perfil funciona como se o ponto final não estivesse incluído. Esta ação é útil para remover temporariamente um ponto final que está no modo de manutenção ou que vai ser implementado. Quando o ponto final está novamente em funcionamento, pode ativá-lo.

> [!NOTE]
> Desativar um ponto final não tem nada a ver com o estado de implementação no Azure. Um ponto final em bom estado continua ativo para receber tráfego, mesmo quando desativado no Gestor de Tráfego. Além disso, a desativação de um ponto final num perfil não afeta o estado noutro perfil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Para adicionar um ponto final de serviço em nuvem ou de site

1. No painel Gestor de Tráfego no Portal Clássico do Azure, localize o perfil do Gestor de Tráfego que contém as definições do ponto final que pretende modificar. Para abrir a página de definições, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **Pontos Finais** para ver os pontos finais que já fazem parte da configuração.
3. Na parte inferior da página, clique em **Adicionar** para aceder à página **Adicionar Pontos Finais de Serviço**. Por predefinição, a página lista os serviços em nuvem em **Pontos Finais de Serviço**.
4. Para serviços em nuvem, selecione os serviços em nuvem na lista para adicioná-los como pontos finais a este perfil. Limpar o nome do serviço em nuvem remove-o da lista de pontos finais.
5. Para os sites, clique na lista pendente **Tipo de Serviço** e selecione **Aplicação Web**.
6. Selecione os sites na lista para adicioná-los como pontos finais para este perfil. Limpar o nome do site remove-o da lista de pontos finais. Pode selecionar apenas um único Web site para cada datacenter do Azure (também conhecido como região). Ao selecionar o primeiro Web site, os outros Web sites do mesmo datacanter ficam indisponíveis para seleção. Tenha também em atenção que são listados apenas os Web Sites Standard.
7. Depois de selecionar os pontos finais para este perfil, clique na marca de verificação no canto inferior direito para guardar as alterações.

> [!NOTE]
> Depois de adicionar ou remover um ponto final de um perfil com o método de encaminhamento de tráfego *Ativação pós-falha*, a lista de prioridade de ativação pós-falha não pode ser ordenada da forma que pretende. Pode ajustar apenas a ordem da Lista de Prioridade de Ativação Pós-falha na Página de configuração. Para obter mais informações, consulte [Configurar o Encaminhamento de Tráfego de Ativação Pós-falha](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para desativar um ponto final

1. No painel Gestor de Tráfego no Portal Clássico do Azure, localize o perfil do Gestor de Tráfego que contém as definições do ponto final que pretende modificar. Para abrir a página de definições, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **Pontos Finais** para ver os pontos finais que estão incluídos na configuração.
3. Clique no ponto final que pretende desativar e, em seguida, clique em **Desativar** na parte inferior da página.
4. Os clientes continuam a enviar tráfego para o ponto final com a duração do Tempo de Vida (TTL). Pode alterar o TTL na página Configuração do perfil do Gestor de Tráfego.

## <a name="to-enable-an-endpoint"></a>Para ativar um ponto final

1. No painel Gestor de Tráfego no Portal Clássico do Azure, localize o perfil do Gestor de Tráfego que contém as definições do ponto final que pretende modificar. Para abrir a página de definições, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **Pontos Finais** para ver os pontos finais que estão incluídos na configuração.
3. Clique no ponto final que pretende ativar e, em seguida, clique em **Ativar** na parte inferior da página.
4. Os clientes são direcionados para o ponto final ativado, conforme ditado pelo perfil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Para eliminar um ponto final de serviço em nuvem ou do site

1. No painel Gestor de Tráfego no Portal Clássico do Azure, localize o perfil do Gestor de Tráfego que contém as definições do ponto final que pretende modificar. Para abrir a página de definições, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **Pontos Finais** para ver os pontos finais que já fazem parte da configuração.
3. Na página Pontos Finais, clique no nome do ponto final que pretende eliminar do perfil.
4. Na parte inferior da página, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

* [Gerir perfis do Gestor de Tráfego](traffic-manager-manage-profiles.md)
* [Configurar métodos de encaminhamento](traffic-manager-configure-routing-method.md)
* [Resolução de problemas do estado degradado do Gestor de Tráfego](traffic-manager-troubleshooting-degraded.md)
* [Considerações de desempenho para o Gestor de Tráfego](traffic-manager-performance-considerations.md)
* [Operações do Gestor de Tráfego (Referência da API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO2-->


