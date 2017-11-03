---
title: "Utilizando os serviços de balanceamento de carga no Azure | Microsoft Docs"
description: "Este tutorial mostra como criar um cenário, utilizando o portefólio de balanceamento de carga do Azure: Gestor de tráfego, o Gateway de aplicação e o Balanceador de carga."
services: traffic-manager
documentationcenter: 
author: liumichelle
manager: vitinnan
editor: 
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: ae9bd30b76786f94f0d836a39137da696fdb94a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-load-balancing-services-in-azure"></a>Utilizando os serviços de balanceamento de carga no Azure

## <a name="introduction"></a>Introdução

O Microsoft Azure oferece vários serviços para gerir a forma como o tráfego de rede é distribuído e balanceamento de carga. Pode utilizar estes serviços individualmente ou combinar os respetivos métodos, consoante as suas necessidades, para compilar a solução ideal.

Neste tutorial, vamos primeiro definir um caso de utilização do cliente e ver como este pode ser efetuada mais robusto e performant, utilizando o seguinte portefólio de balanceamento de carga do Azure: Gestor de tráfego, o Gateway de aplicação e o Balanceador de carga. Em seguida, podemos fornecer instruções passo a passo para criar uma implementação que georredundante, distribui o tráfego para VMs e ajuda-o a gerir diferentes tipos de pedidos.

Um nível conceptual, cada um destes serviços desempenha um papel distinto na hierarquia de balanceamento de carga.

* **Gestor de tráfego** fornece balanceamento de carga de global DNS. Observa a pedidos recebidos de DNS e responde com um ponto final em bom estado, de acordo com a política de encaminhamento, que o cliente tiver selecionado. Opções para métodos de encaminhamento são:
  * Desempenho de encaminhamento ao enviar o requerente para o ponto final mais próximo em termos de latência.
  * Prioridade de encaminhamento para direcionar todo o tráfego para um ponto final com outros pontos finais como cópia de segurança.
  * Ponderado round robin encaminhamento, que distribui o tráfego com base no peso que é atribuído a cada ponto final.

  O cliente liga-se diretamente para esse ponto final. Traffic Manager do Azure Deteta quando um ponto final está danificado e, em seguida, redireciona os clientes para outra instância bom estado de funcionamento. Consulte [documentação do Traffic Manager do Azure](traffic-manager-overview.md) para saber mais sobre o serviço.
* **Gateway de aplicação** fornece o controlador de entrega de aplicações (ADC) como um oferta de serviço, vários grupos de capacidades de balanceamento de carga de 7 camadas para a sua aplicação. Permite que os clientes otimizar a produtividade do web farm ao descarregar a terminação de SSL de intensivas em CPU para o gateway de aplicação. Outras funcionalidades de encaminhamento de 7 camadas incluem round robin distribuição de tráfego de entrada, afinidade com base no cookie de sessão, URL com base no caminho de encaminhamento e a capacidade de alojar vários sites por trás de um gateway de aplicação único. Gateway de aplicação pode ser configurado como um gateway de acesso à Internet, um gateway meramente internos ou uma combinação de ambos. Gateway de aplicação é Azure totalmente gerido, escalável e altamente disponível. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão.
* **O Balanceador de carga** é uma parte integral da pilha de SDN do Azure, que fornece elevado desempenho, baixa latência camada 4 balanceamento de carga serviços para todos os protocolos UDP e TCP. Gere ligações de entrada e saídas. Pode configurar públicos e internos com balanceamento de carga pontos finais e definir as regras para mapear ligações de entrada para destinos de conjunto back-end através de TCP e HTTP opções de pesquisa de estado de funcionamento para gerir a disponibilidade do serviço.

## <a name="scenario"></a>Cenário

Neste cenário de exemplo, vamos utilizar um site simples, que serve os dois tipos de conteúdo: imagens e páginas Web composto dinamicamente. O Web site tem de ser georredundante e deverá servir os utilizadores a mais próxima (mais baixa latência) localização aos mesmos. O programador da aplicação tem de decidir que os URLs que corresponde ao padrão/imagens / * são servido a partir de um conjunto de dedicado de VMs que são diferentes do resto da web farm.

Além disso, o conjunto predefinido de VM, que serve o conteúdo dinâmico tem de comunicar com uma base de dados de back-end que está alojado num cluster de elevada disponibilidade. A implementação completa é configurada através do Azure Resource Manager.

Utilizar o Gestor de tráfego, o Gateway de aplicação e o Balanceador de carga permite que este Web site alcançar estes objetivos de design:

* **Redundância geográfica várias**: se uma região ficar inativo, Gestor de tráfego encaminha o tráfego da forma totalmente integrada para a região mais próxima sem qualquer intervenção do proprietário da aplicação.
* **Latência reduzida**: porque o Gestor de tráfego direciona automaticamente o cliente para a região mais próxima, o cliente ocorre com a menor latência quando pedir o conteúdo de página Web.
* **Escalabilidade independente**: porque a carga de trabalho de aplicação web é separada por tipo de conteúdo, o proprietário da aplicação pode dimensionar as cargas de trabalho de pedido independentes entre si. Gateway de aplicação assegura que o tráfego é encaminhado para os direita conjuntos com base em regras especificadas e o estado de funcionamento da aplicação.
* **Balanceamento de carga interna**: porque o Balanceador de carga é à frente do cluster de elevada disponibilidade, apenas o Active Directory e bom ponto final para uma base de dados é exposto à aplicação. Além disso, um administrador da base de dados pode otimizar a carga de trabalho por distribuição réplicas ativas e passivas no cluster independentes da aplicação front-end. Balanceador de carga fornece ligações para o cluster de elevada disponibilidade e assegura que apenas as bases de dados bom recebem pedidos de ligação.

O diagrama seguinte mostra a arquitetura deste cenário:

![Diagrama de balanceamento de carga arquitetura](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Neste exemplo é apenas uma das muitas configurações possíveis dos serviços de balanceamento de carga que o Azure oferece. Gestor de tráfego, o Gateway de aplicação e o Balanceador de carga podem ser misto e comparados suit melhor as necessidades de balanceamento de carga. Por exemplo, se a descarga de SSL ou processamento de 7 camadas não é necessário, Balanceador de carga pode ser utilizado em vez de Gateway de aplicação.

## <a name="setting-up-the-load-balancing-stack"></a>Configurar a pilha de balanceamento de carga

### <a name="step-1-create-a-traffic-manager-profile"></a>Passo 1: Criar um perfil do Traffic Manager

1. No portal do Azure, clique em **novo**e, em seguida, procure o marketplace para "Perfil do Traffic Manager."
2. No **perfil do Gestor de tráfego criar** painel, introduza as seguintes informações básicas:

  * **Nome**: forneça um DNS do perfil do Traffic Manager prefixo de nome.
  * **Método de encaminhamento**: selecione a política de método de encaminhamento de tráfego. Para obter mais informações sobre os métodos, consulte [sobre o tráfego do traffic Manager métodos de encaminhamento](traffic-manager-routing-methods.md).
  * **Subscrição**: selecione a subscrição que contém o perfil.
  * **Grupo de recursos**: selecione o grupo de recursos que contém o perfil. Pode ser um grupo de recursos novo ou existente.
  * **Localização do grupo de recursos**: serviço Gestor de tráfego é global e não está vinculado a uma localização. No entanto, tem de especificar uma região para o grupo de onde residem os metadados associados ao perfil do Gestor de tráfego. Esta localização não tem impacto na disponibilidade em runtime do perfil.

3. Clique em **criar** para gerar o perfil do Traffic Manager.

  ![Painel "Criar Gestor de tráfego"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Passo 2: Criar os gateways de aplicação

1. No portal do Azure, no painel esquerdo, clique em **novo** > **redes** > **Gateway de aplicação**.
2. Introduza as seguintes informações básicas sobre o gateway de aplicação:

  * **Nome**: O nome do gateway de aplicação.
  * **Tamanho do SKU**: O tamanho do gateway de aplicação, disponível como pequeno, médio ou grande.
  * **Contagem de instância**: O número de instâncias, um valor de 2 a 10.
  * **Grupo de recursos**: O grupo de recursos que contém o gateway de aplicação. Pode ser um grupo de recursos existente ou um novo.
  * **Localização**: A região do gateway de aplicação, o que é a mesma localização que o grupo de recursos. A localização é importante, porque a rede virtual e o IP público tem de ser a mesma localização que o gateway.
3. Clique em **OK**.
4. Defina a rede virtual, uma sub-rede, IP de front-end e configurações de serviço de escuta para o gateway de aplicação. Neste cenário, o endereço IP Front-end é **pública**, que permite a adicionar como um ponto final para o perfil do Gestor de tráfego mais tarde.
5. Configure o serviço de escuta com uma das seguintes opções:
    * Se utilizar HTTP, não há nada a configurar. Clique em **OK**.
    * Se utilizar o HTTPS, ainda é necessária configuração. Consulte [criar um gateway de aplicação](../application-gateway/application-gateway-create-gateway-portal.md), começando no passo 9. Quando tiver concluído a configuração, clique em **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Configurar o encaminhamento de URL para gateways de aplicação

Quando seleciona um conjunto de back-end, um gateway de aplicação que está configurado com uma regra com base no caminho aceita um padrão do caminho do URL do pedido para além de distribuição de round robin. Neste cenário, que estamos a adicionar uma regra com base no caminho, para direcionar qualquer URL com "/images/\*" ao agrupamento de servidores de imagem. Para obter mais informações sobre como configurar o URL com base no caminho de encaminhamento para um gateway de aplicação, consulte [criar uma regra com base no caminho para um gateway de aplicação](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagrama de camada web de Gateway de aplicação](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Do seu grupo de recursos, vá para a instância do gateway de aplicação que criou na secção anterior.
2. Em **definições**, selecione **conjuntos back-end**e, em seguida, selecione **adicionar** para adicionar as VMs que pretende associar os conjuntos de back-end de camada web.
3. No **adicionar conjunto back-end** painel, introduza o nome do conjunto de back-end e todos os endereços IP das máquinas que residem no conjunto. Neste cenário, estamos a estabelecer ligação dois conjuntos de servidores de back-end de máquinas virtuais.

  ![Painel de "Adicionar conjunto back-end" de Gateway de aplicação](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Em **definições** do gateway de aplicação, selecione **regras**e, em seguida, clique em de **caminho com base** botão para adicionar uma regra.

  ![Botão de "Caminho com base" regras do Gateway de aplicação](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. No **Adicionar regra de caminho com base em** painel, configurar a regra, fornecendo as seguintes informações.

   Definições básicas:

   + **Nome**: O nome amigável da regra que está acessível no portal.
   + **Serviço de escuta**: O serviço de escuta que é utilizado para a regra.
   + **Predefinição conjunto back-end**: O conjunto de back-end para ser utilizado com a regra predefinida.
   + **Predefinições de HTTP**: as definições de HTTP a ser utilizado com a regra predefinida.

   Regras com base no caminho:

   + **Nome**: O nome amigável da regra com base no caminho.
   + **Caminhos**: A regra de caminho que é utilizada para o tráfego de reencaminhamento.
   + **Conjunto back-end**: O conjunto de back-end para ser utilizada com esta regra.
   + **Definição de HTTP**: as definições de HTTP a ser utilizada com esta regra.

   > [!IMPORTANT]
   > Caminhos: Caminhos válidos tem de começar com "/". O caráter universal "\*" é permitido apenas no final. Exemplos válidos são /xyz, /xyz\*, ou /xyz/\*.

   ![Painel de "Adicionar regra com base no caminho" do Gateway de aplicação](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Passo 3: Adicionar gateways de aplicação para os pontos finais de Gestor de tráfego

Neste cenário, o Gestor de tráfego está ligado a gateways de aplicação (conforme configuradas nos passos anteriores) que residem em regiões diferentes. Agora que estão configurados os gateways de aplicação, o passo seguinte é ligá-los para o perfil do Traffic Manager.

1. Abra o perfil do Traffic Manager. Para tal, procure no seu grupo de recursos ou procure o nome do perfil do Traffic Manager do **todos os recursos**.
2. No painel esquerdo, selecione **pontos finais**e, em seguida, clique em **adicionar** para adicionar um ponto final.

  ![Botão de Gestor de tráfego pontos finais "Adicionar"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. No **adicionar ponto final** painel, crie um ponto final, introduzindo as seguintes informações:

  * **Tipo**: selecione o tipo de ponto final para balanceamento de carga. Neste cenário, selecione **ponto final do Azure** porque estamos a estabelecer ligação-lo para as instâncias de gateway de aplicação que foram anteriormente configuradas.
  * **Nome**: introduza o nome do ponto final.
  * **Tipo de recurso de destino**: selecione **endereço IP público** e, em **recurso de destino**, selecione o IP público do gateway de aplicação que foi configurado anteriormente.

   ![Painel "Adicionar ponto final" do Gestor de tráfego](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Agora pode testar a sua configuração acedendo-lo com o DNS do seu perfil do Gestor de tráfego (neste exemplo: TrafficManagerScenario.trafficmanager.net). Pode reenviar pedidos, trazer ou colocar baixo VMs e servidores web que foram criados em regiões diferentes e alterar as definições de perfil do Gestor de tráfego para testar a configuração.

### <a name="step-4-create-a-load-balancer"></a>Passo 4: Criar um balanceador de carga

Neste cenário, o Balanceador de carga distribui ligações a partir da camada web para as bases de dados dentro de um cluster de elevada disponibilidade.

Se o cluster de elevada disponibilidade da base de dados está a utilizar o SQL Server AlwaysOn, consulte [configurar um ou mais sempre na disponibilidade grupo de serviços de escuta](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) para obter instruções passo a passo.

Para obter mais informações sobre como configurar um balanceador de carga interno, consulte [criar um balanceador de carga interno no portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. No portal do Azure, no painel esquerdo, clique em **novo** > **redes** > **Balanceador de carga**.
2. No **criar Balanceador de carga** painel, escolha um nome para o Balanceador de carga.
3. Definir o **tipo** para **interno**e escolha a rede virtual adequado e a sub-rede para o Balanceador de carga estar situada em.
4. Em **atribuição de endereços IP**, selecione **dinâmica** ou **estático**.
5. Em **grupo de recursos**, selecione o grupo de recursos para o Balanceador de carga.
6. Em **localização**, escolha a região adequada para o Balanceador de carga.
7. Clique em **criar** para gerar o Balanceador de carga.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Ligar uma camada de base de dados de back-end para o Balanceador de carga

1. Do seu grupo de recursos, localize o Balanceador de carga que foi criado nos passos anteriores.
2. Em **definições**, clique em **conjuntos back-end**e, em seguida, clique em **adicionar** para adicionar um conjunto de back-end.

  ![Painel "Adicionar conjunto back-end" do Balanceador de carga](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. No **adicionar conjunto back-end** painel, introduza o nome do conjunto de back-end.
4. Adicione máquinas individuais ou um conjunto de disponibilidade para o conjunto de back-end.

#### <a name="configure-a-probe"></a>Configurar uma sonda

1. No seu Balanceador de carga em **definições**, selecione **sondas**e, em seguida, clique em **adicionar** para adicionar uma pesquisa.

 ![Painel "Adicionar sonda" de Balanceador de carga](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. No **adicionar sonda** painel, introduza o nome da sonda.
3. Selecione o **protocolo** da sonda. Para uma base de dados, pode querer uma sonda TCP, em vez de uma pesquisa HTTP. Para obter mais informações sobre pesquisas de Balanceador de carga, consulte [sondas de Balanceador de carga de compreender](../load-balancer/load-balancer-custom-probe-overview.md).
4. Introduza o **porta** da base de dados a ser utilizado para aceder a pesquisa.
5. Em **intervalo**, especifique a frequência de sonda da aplicação.
6. Em **limiar de mau estado de funcionamento**, especifique o número de falhas de sonda contínua que tem de ocorrer para a VM de back-end ser considerado em mau estado de funcionamento.
7. Clique em **OK** para criar a sonda.

#### <a name="configure-the-load-balancing-rules"></a>Configurar as regras de balanceamento de carga

1. Em **definições** do seu Balanceador de carga, selecione **as regras de balanceamento de carga**e, em seguida, clique em **adicionar** para criar uma regra.
2. No **regra de balanceamento de carga de adicionar** painel, introduza o **nome** para a regra de balanceamento de carga.
3. Escolha o **endereço de IP de front-end** do Balanceador de carga, **protocolo**, e **porta**.
4. Em **porta back-end**, especifique a porta a ser utilizado no conjunto de back-end.
5. Selecione o **conjunto back-end** e **sonda** que foram criados nos passos anteriores para aplicar a regra.
6. Em **persistência da sessão**, escolha como pretende que as sessões para manter.
7. Em **tempos limite de inatividade**, especifique o número de minutos antes de um tempo limite de inatividade.
8. Em **IP flutuante**, selecione **desativado** ou **ativado**.
9. Clique em **OK** para criar a regra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Passo 5: Ligar as VMs de camada web ao balanceador de carga

Agora, iremos configurar a porta de front-end Balanceador de carga e endereços IP nas aplicações que estão em execução em VMs a camada web para todas as ligações de base de dados. Esta configuração é específica para as aplicações que são executados nestas VMS. Para configurar o endereço IP de destino e a porta, consulte a documentação da aplicação. Para localizar o endereço IP do front-end, no portal do Azure, avance para o conjunto IP Front-end a **definições do Balanceador de carga** painel.

![Painel de navegação de "Conjunto de IP de front-end" de Balanceador de carga](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Gestor de tráfego](traffic-manager-overview.md)
* [Descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md)
* [Descrição Geral do Balanceador de Carga do Azure (Azure Load Balancer overview)](../load-balancer/load-balancer-overview.md)
