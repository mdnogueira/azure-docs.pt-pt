<properties
   pageTitle="Gerir perfis no Traffic Manager do Azure | Microsoft Azure"
   description="Este artigo ajuda-o a criar, desativar, ativar, eliminar e ver o histórico de um perfil do Traffic Manager do Azure."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# Gerir um perfil no Traffic Manager do Azure

Utilize um perfil do Traffic Manager para especificar os serviços em nuvem ou pontos finais de sites que serão monitorizados pelo Traffic Manager, e qual o método de tráfego de encaminhamento que pretende utilizar para distribuir as ligações a esses pontos finais.

## Criar um perfil do Traffic Manager através da Criação Rápida

Pode criar rapidamente um perfil do Traffic Manager, utilizando a Criação Rápida do Portal Clássico do Azure. A Criação Rápida permite-lhe criar perfis com definições básicas de configuração. No entanto, não pode utilizar a Criação Rápida para definições como o conjunto de pontos finais (serviços em nuvem e Web Sites), a ordem de ativação pós-falha para o método de encaminhamento de tráfego de ativação pós-falha ou definições de monitorização. Depois de criar o perfil, pode configurar estas definições no Portal Clássico do Azure. O Traffic Manager suporta até 200 pontos finais por perfil. No entanto, a maioria dos cenários de utilização exigem apenas um pequeno número de pontos finais. 

### Para criar um novo perfil do Traffic Manager

1. **Implemente os serviços em nuvem e Web Sites para o seu ambiente de produção.** Para mais informações sobre serviços em nuvem, consulte [Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para mais informações sobre os serviços em nuvem, consulte [Melhores práticas](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Para obter mais informações sobre os Web Sites, consulte [Web Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Inicie sessão no Portal Clássico do Azure.** Para criar um novo perfil do Traffic Manager, clique em **Novo** na parte inferior esquerda do portal, clique em **Serviços de Rede > Traffic Manager** e clique em **Criação Rápida** para começar a configurar o perfil.
3. **Configure o prefixo DNS.** Escolha um nome de prefixo de DNS exclusivo para o perfil do Traffic Manager. Pode especificar apenas o prefixo para um nome de domínio do Traffic Manager.
4. **Selecione uma subscrição.** Selecione a subscrição do Azure adequada. Cada perfil é associado a uma única subscrição. Se só tiver uma subscrição, esta opção não é apresentada.
5. **Selecione o método de encaminhamento de tráfego.** Selecione o método de encaminhamento de tráfego em **Política de encaminhamento de tráfego**. Para obter mais informações sobre os métodos de encaminhamento de tráfego, consulte [Acerca dos métodos de encaminhamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md).
6. **Clique em “Criar” para criar o novo perfil**. Quando tiver concluído a configuração do perfil, pode localizar o seu perfil no painel do Traffic Manager do Portal Clássico do Azure.
7. **Configure os pontos finais, a monitorização e outras definições no Portal Clássico do Azure.** Uma vez que só pode configurar definições básicas através da Criação Rápida, é necessário configurar definições adicionais, como a lista de pontos finais e a ordem de ativação pós-falha do ponto final, para concluir a configuração pretendida. 


## Desativar, ativar ou eliminar um perfil

Pode desativar um perfil do Traffic Manager existente para que não refira pedidos do utilizador a pontos finais configurados. Quando desativa um perfil do Traffic Manager, o próprio perfil e as informações contidas no perfil permanecerão intactos e podem ser editados na interface do Traffic Manager. Quando pretender reativar o perfil, pode facilmente fazê-lo no Portal Clássico do Azure e as referências serão retomadas. Quando criar um perfil do Traffic Manager no Portal Clássico do Azure, este é ativado automaticamente. Se decidir que um perfil já não é necessário, poderá eliminá-lo.

### Para desativar um perfil

1. Modifique o registo de recursos DNS no servidor DNS de Internet para utilizar o tipo de registo adequado e o ponteiro para outro nome ou endereço IP de uma localização específica na Internet. Por outras palavras, altere o registo de recursos DNS no servidor DNS de Internet para que deixe de utilizar um registo de recurso CNAME que aponta para o nome de domínio do perfil do Traffic Manager.
2. O tráfego deixará de ser direcionado para os pontos finais através das definições de perfil do Traffic Manager.
3. Selecione o perfil que pretende desativar. Para selecionar o perfil, na página do Traffic Manager, realce o perfil clicando na coluna junto ao nome do perfil. Não clique no nome do perfil ou na seta junto ao nome, pois isto leva-o para a página de definições do perfil.
4. Após selecionar o perfil, clique em **Desativar** na parte inferior da página.

### Para ativar um perfil

1. Selecione o perfil que pretende ativar. Para selecionar o perfil, na página do Traffic Manager, realce o perfil clicando na coluna junto ao nome do perfil. Não clique no nome do perfil ou na seta junto ao nome, pois isto leva-o para a página de definições do perfil.
2. Após selecionar o perfil, clique em **Ativar** na parte inferior da página.
3. Modifique o registo de recursos DNS no servidor DNS de Internet para utilizar o tipo de registo CNAME, que mapeia o nome de domínio da empresa para o nome de domínio do perfil do Traffic Manager. Para obter mais informações, consulte [Apontar um domínio da Internet da empresa para um domínio do Traffic Manager](traffic-manager-point-internet-domain.md).
4. O tráfego começará a ser direcionado para os pontos finais novamente.

### Para eliminar um perfil

1. Certifique-se de que o registo de recursos DNS no servidor DNS de Internet já não utiliza um registo de recurso CNAME que aponta para o nome de domínio do perfil do Traffic Manager.
2. Selecione o perfil que pretende eliminar. Para selecionar o perfil, na página do Traffic Manager, realce o perfil clicando na coluna junto ao perfil. Não clique no nome do perfil ou na seta junto ao nome, pois isto leva-o para a página de definições do perfil.
4. Após selecionar o perfil, clique em **Eliminar** na parte inferior da página.

## Ver histórico de alterações do perfil do Traffic Manager

Pode ver o histórico de alterações do perfil do Traffic Manager no Portal Clássico do Azure nos Serviços de Gestão.

### Para ver o histórico de alterações do Traffic Manager

1. No painel da esquerda do Portal Clássico do Azure, clique em **Serviços de Gestão**.
2. Na página Serviços de Gestão, clique em **Registos de Operações**.
3. Na página Registos de Operações, pode filtrar para ver o histórico de alterações do perfil do Traffic Manager. Depois de selecionar as opções de filtragem, clique na marca de verificação para ver os resultados.
   - Para ver as alterações de perfil para todos os seus perfis, selecione a subscrição e o intervalo de tempo e, em seguida, selecione **Traffic Manager** no menu de atalho **Tipo** .
   - Para filtrar por nome de perfil, escreva o nome do perfil no campo**Nome do Serviço** ou selecione-o no menu de atalho.
   - Para ver os detalhes para cada alteração individual, selecione a linha com a alteração que pretende ver e, em seguida, clique em **Detalhes** na parte inferior da página. Na janela **Detalhes da Operação**, pode ver a representação XML do objeto API que foi criado ou atualizado como parte da operação e copie o código XML para a área de transferência.


## Passos seguintes

[Adicionar um ponto final](traffic-manager-endpoints.md)

[Configurar o método de encaminhamento de ativação pós-falha](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de encaminhamento de round robin](traffic-manager-configure-round-robin-routing-method.md)

[Configurar o método de encaminhamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Resolução de problemas do estado degradado do Traffic Manager](traffic-manager-troubleshooting-degraded.md)


<!--HONumber=ago16_HO4-->


