---
title: Gerir perfis no Traffic Manager do Azure | Microsoft Docs
description: "Este artigo ajuda-o a criar, desativar, ativar, eliminar e ver o histórico de um perfil do Gestor de Tráfego do Azure."
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 105ba0b27725ccc4846c30a2d8e29fbdebb8d86c

---

# <a name="manage-an-azure-traffic-manager-profile"></a>Gerir um perfil no Traffic Manager do Azure

Os perfis do Gestor de Tráfego utilizam métodos de encaminhamento de tráfego para controlar a distribuição de tráfego para os seus serviços cloud ou pontos finais de Web site. Este artigo explica como criar e gerir estes perfis.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Criar um perfil do Traffic Manager através da Criação Rápida

Pode criar rapidamente um perfil do Traffic Manager, utilizando a Criação Rápida do Portal Clássico do Azure. A Criação Rápida permite-lhe criar perfis com definições básicas de configuração. No entanto, não pode utilizar a Criação Rápida para definições como o conjunto de pontos finais (serviços em nuvem e Web Sites), a ordem de ativação pós-falha para o método de encaminhamento de tráfego de ativação pós-falha ou definições de monitorização. Depois de criar o perfil, pode configurar estas definições no Portal Clássico do Azure. O Traffic Manager suporta até 200 pontos finais por perfil. No entanto, a maioria dos cenários de utilização exigem apenas alguns pontos finais.

### <a name="to-create-a-traffic-manager-profile"></a>Para criar um perfil do Gestor de Tráfego

1. **Implemente os serviços em nuvem e Web Sites para o seu ambiente de produção.** Para mais informações sobre serviços em nuvem, consulte [Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obter mais informações sobre os Web Sites, consulte [Web Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Inicie sessão no Portal Clássico do Azure.** Clique em **Novo** na parte inferior esquerda do portal, clique em **Serviços de Rede > Gestor de Tráfego** e clique em **Criação Rápida** para começar a configurar o perfil.
3. **Configure o prefixo DNS.** Escolha um nome de prefixo de DNS exclusivo para o perfil do Traffic Manager. Pode especificar apenas o prefixo para um nome de domínio do Traffic Manager.
4. **Selecionar a subscrição.** Selecione a subscrição do Azure adequada. Cada perfil é associado a uma única subscrição. Se só tiver uma subscrição, esta opção não é apresentada.
5. **Selecione o método de encaminhamento de tráfego.** Selecione o método de encaminhamento de tráfego em **Política de encaminhamento de tráfego**. Para obter mais informações sobre os métodos de encaminhamento de tráfego, consulte [Acerca dos métodos de encaminhamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md).
6. **Clique em "Criar" para criar o perfil**. Quando tiver concluído a configuração do perfil, pode localizar o seu perfil no painel do Traffic Manager do Portal Clássico do Azure.
7. **Configure os pontos finais, a monitorização e outras definições no Portal Clássico do Azure.** Com a Criação Rápida apenas configura as definições básicas. Tem de configurar as definições adicionais, como a lista de pontos finais e a ordem de ativação pós-falha do ponto final.

## <a name="disable-enable-or-delete-a-profile"></a>Desativar, ativar ou eliminar um perfil

Pode desativar um perfil existente para que o Gestor de Tráfego não refira pedidos do utilizador aos pontos finais configurados. Quando desativa um perfil do Gestor de Tráfego, o perfil e as informações contidas no perfil permanecem intactos e podem ser editados na interface do Gestor de Tráfego.  As referências são retomadas quando voltar a ativar o perfil. Quando criar um perfil do Gestor de Tráfego no Portal Clássico do Azure, este é ativado automaticamente. Se decidir que um perfil já não é necessário, poderá eliminá-lo.

### <a name="to-disable-a-profile"></a>Para desativar um perfil

1. Se estiver a utilizar um nome de domínio personalizado, altere o registo CNAME no seu servidor DNS da Internet, para que já não aponte para o seu perfil do Gestor de Tráfego.
2. O tráfego deixa de ser direcionado para os pontos finais através das definições de perfil do Gestor de Tráfego.
3. Selecione o perfil que pretende desativar. Na página do Gestor de Tráfego, realce o perfil clicando na coluna junto ao nome do perfil. Note que, clicar no nome do perfil ou na seta junto ao nome, abre a página de definições do perfil.
4. Após selecionar o perfil, clique em **Desativar** na parte inferior da página.

### <a name="to-enable-a-profile"></a>Para ativar um perfil

1. Selecione o perfil que pretende desativar. Na página do Gestor de Tráfego, realce o perfil clicando na coluna junto ao nome do perfil. Note que, clicar no nome do perfil ou na seta junto ao nome, abre a página de definições do perfil.
2. Após selecionar o perfil, clique em **Ativar** na parte inferior da página.
3. Se estiver a utilizar um nome de domínio personalizado, crie um registo de recursos CNAME no seu servidor DNS da Internet, para que aponte para o nome de domínio do perfil do Gestor de Tráfego.
4. O tráfego é direcionado para os pontos finais novamente.

### <a name="to-delete-a-profile"></a>Para eliminar um perfil

1. Certifique-se de que o registo de recursos DNS no servidor DNS de Internet já não utiliza um registo de recurso CNAME que aponta para o nome de domínio do perfil do Traffic Manager.
2. Selecione o perfil que pretende desativar. Na página do Gestor de Tráfego, realce o perfil clicando na coluna junto ao nome do perfil. Note que, clicar no nome do perfil ou na seta junto ao nome, abre a página de definições do perfil.
3. Após selecionar o perfil, clique em **Eliminar** na parte inferior da página.

## <a name="view-traffic-manager-profile-change-history"></a>Ver histórico de alterações do perfil do Traffic Manager

Pode ver o histórico de alterações do perfil do Traffic Manager no Portal Clássico do Azure nos Serviços de Gestão.

### <a name="to-view-your-traffic-manager-change-history"></a>Para ver o histórico de alterações do Traffic Manager

1. No painel da esquerda do Portal Clássico do Azure, clique em **Serviços de Gestão**.
2. Na página Serviços de Gestão, clique em **Registos de Operações**.
3. Na página Registos de Operações, pode filtrar para ver o histórico de alterações do perfil do Traffic Manager. Depois de selecionar as opções de filtragem, clique na marca de verificação para ver os resultados.

   * Para ver as alterações de todos os seus perfis, selecione a subscrição e o intervalo de tempo e, em seguida, selecione **Gestor de Tráfego** no menu de atalho **Tipo** .
   * Para filtrar por nome de perfil, escreva o nome do perfil no campo**Nome do Serviço** ou selecione-o no menu de atalho.
   * Para ver os detalhes para cada alteração individual, selecione a linha com a alteração que pretende ver e, em seguida, clique em **Detalhes** na parte inferior da página. Na janela **Detalhes da Operação**, pode ver a representação XML do objeto API que foi criado ou atualizado como parte da operação.

## <a name="next-steps"></a>Passos seguintes

* [Adicionar um ponto final](traffic-manager-endpoints.md)
* [Configurar o método de encaminhamento de ativação pós-falha](traffic-manager-configure-failover-routing-method.md)
* [Configurar o método de encaminhamento de round robin](traffic-manager-configure-round-robin-routing-method.md)
* [Configurar o método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md)
* [Apontar um domínio de Internet da empresa para um nome de domínio do Gestor de Tráfego](traffic-manager-point-internet-domain.md)
* [Resolução de problemas do estado degradado do Gestor de Tráfego](traffic-manager-troubleshooting-degraded.md)



<!--HONumber=Nov16_HO2-->


