---
title: "Implementação de AD FS de geografia cruzada de elevada disponibilidade no Azure com o Gestor de Tráfego do Azure | Microsoft Docs"
description: 'Neste documento, vai aprender a implementar o AD FS no Azure para disponibilidade elevada '
keywords: "AD FS com o Gestor de Tráfego do Azure, adfs com Gestor de Tráfego do Azure, geográfico, centro de dados múltiplos, centros de dados geográficos, centros de dados geográficos múltiplos, implementar o AD FS no azure, implementar adfs do azure, azure adfs, ad fs azure , implementar adfs, implementar ad fs, adfs no azure, implementar adfs no azure, implementar AD FS no azure, adfs azure, introdução ao AD FS, Azure, AD FS no Azure, iaas, ADFS, mover adfs para o azure"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: a14bc870-9fad-45ed-acd5-a90ccd432e54
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: anandy;billmath
ms.openlocfilehash: 077710049894d2690299ce0fcb0ead9911aa4bb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Implementação de AD FS geográficos cruzados de elevada disponibilidade no Azure com o Gestor de Tráfego do Azure
[Implementação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md) fornece orientação passo a passo sobre como pode implementar uma infraestrutura de AD FS simples para a sua organização no Azure. Este artigo fornece os passos seguintes para criar uma implementação geográfica cruzada do AD FS no Azure com o [Gestor de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md). O Gestor de Tráfego do Azure ajuda-o a criar uma infraestrutura do AD FS de elevado desempenho e elevada disponibilidade propagada a nível geográfico para a sua organização ao utilizar o intervalo de métodos de encaminhamento disponíveis para satisfazer as necessidades diferentes da infraestrutura.

Permite uma infraestrutura do AD FS geográfica cruzada de elevada disponibilidade:

* **Eliminação do ponto único de falha:** com as capacidades de ativação pós-falha do Gestor de Tráfego do Azure, pode conseguir uma infraestrutura do AD FS de elevada disponibilidade, mesmo quando um dos centros de dados numa parte do mundo fica inoperacional
* **Desempenho melhorado:** pode utilizar a implementação sugerida neste artigo para fornecer uma infraestrutura do AD FS de elevado desempenho que pode ajudar os utilizadores autenticar com maior rapidez. 

## <a name="design-principles"></a>Princípios de conceção
![Estrutura global](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Os princípios de estrutura básicos serão iguais aos listados nos princípios de estrutura no artigo da implementação do AD FS no Azure. O diagrama acima mostra uma extensão simples da implementação básica para outra região geográfica. Seguem-se alguns pontos a considerar quando expandir a sua implementação a uma nova região geográfica

* **Rede virtual:** deve criar uma nova rede virtual na região geográfica onde pretende implementar a infraestrutura do AD FS adicional. No diagrama acima pode ver Geo1 VNET e Geo2 VNET como as duas redes virtuais de cada região geográfica.
* **Controladores de domínio e servidores do AD FS no novo VNET geográfico:** é recomendável para implementar controladores de domínio na nova região geográfica para que os servidores do AD FS na nova região não tenham de contactar um controlador de domínio noutra rede distante para concluir uma autenticação e, deste modo, melhorar o desempenho.
* **Contas de armazenamento:** contas de armazenamento associados uma região. Uma vez que vai implementar máquinas numa nova região demográfica, terá de criar novas contas de armazenamento para utilização na região.  
* **Grupos de segurança de rede:** como contas de armazenamento, os Grupos de Segurança de Rede criados numa região não podem ser utilizados noutra região geográfica. Por conseguinte, terá de criar novos grupos de segurança de rede semelhantes aos da primeira região geográfica para a sub-rede DMZ e INT na nova região geográfica.
* **Etiquetas de DNS para os endereços IP públicos:** o Gestor de Tráfego do Azure pode referir-se para os pontos finais APENAS através de etiquetas DNS. Por conseguinte, é necessário criar etiquetas de DNS para os endereços IP públicos dos Load Balancers externos.
* **Gestor de Tráfego do Azure:** o Gestor de Tráfego do Microsoft Azure permite-lhe controlar a distribuição de tráfego de utilizador para os pontos finais do serviço executados em centros de dados diferentes a nível mundial. O Gestor de Tráfego do Azure funciona ao nível do DNS. Utiliza as respostas de DNS para direcionar o tráfego de utilizador final para pontos finais distribuídos globalmente. Em seguida, os clientes podem ligar diretamente a esses pontos finais. Com diferentes opções de encaminhamento de Desempenho, Ponderada e Prioridade, pode facilmente escolher a opção de encaminhamento mais adequada para as necessidades da sua organização. 
* **Conetividade V-net para V-net entre duas regiões:** não é necessário conetividade entre as redes virtuais. Uma vez que cada rede virtual tem acesso a controladores de domínio e tem o servidor AD FS e WAP instalados, podem funcionar sem qualquer conetividade entre as redes virtuais em diferentes regiões. 

## <a name="steps-to-integrate-azure-traffic-manager"></a>Passos para integrar o Gestor de Tráfego do Azure
### <a name="deploy-ad-fs-in-the-new-geographical-region"></a>Implementar o AD FS na nova região geográfica
Siga os passos e as diretrizes indicados no [Implementação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md) para implementar a mesma topologia na nova região geográfica.

### <a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Etiquetas de DNS para os endereços IP públicos de Load Balancers com acesso à Internet (públicos)
Como mencionado acima, o Gestor de Tráfego do Azure só pode referir etiquetas de DNS como pontos finais e, por conseguinte, é importante criar etiquetas de DNS para os endereços IP públicos dos Load Balancers Externos. A captura de ecrã abaixo mostra como pode configurar a sua etiqueta de DNS para o endereço IP público. 

![Etiqueta de DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

### <a name="deploying-azure-traffic-manager"></a>Implementar o Gestor de Tráfego do Azure
Siga os passos abaixo para criar um perfil do Gestor de Tráfego. Para obter mais informações, pode também ver [Gerir um perfil do Gestor de Tráfego do Azure](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Criar um perfil do Gestor de Tráfego:** atribua um nome exclusivo ao seu perfil do Gestor de Tráfego. Este nome do perfil faz parte do nome DNS e funciona como um prefixo para a etiqueta de nome de domínio do Gestor de Tráfego. O nome/prefixo é adicionado à. trafficmanager.net para criar uma etiqueta de DNS para o Gestor de Tráfego. A captura de ecrã abaixo mostra o prefixo de DNS do Gestor de Tráfego que está a ser definido como mysts e a etiqueta de DNS resultante será mysts.trafficmanager.net. 
   
    ![Criar um perfil do Gestor de Tráfego](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
2. **Método de encaminhamento de tráfego:** existem três opções de encaminhamento disponíveis no Gestor de Tráfego:
   
   * Prioridade 
   * Desempenho
   * Ponderada
     
     **Desempenho** é a opção recomendada para obter uma infraestrutura do AD FS com elevada capacidade de resposta. No entanto, pode escolher o método de encaminhamento mais adequado para as suas necessidades de implementação. A funcionalidade do AD FS não é afetada pela opção de encaminhamento selecionada. Veja o artigo [Métodos de encaminhamento de tráfego do Gestor de Tráfego](../traffic-manager/traffic-manager-routing-methods.md) para obter mais informações. Na captura de ecrã de exemplo acima pode ver o método **Desempenho** selecionado.
3. **Configurar pontos finais:** na página do Gestor de Tráfego, clique nos pontos finais e selecione Adicionar. Esta ação irá abrir uma página de ponto final Adicionar semelhante à captura de ecrã abaixo
   
   ![Configurar pontos finais](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
   
   Para as diferentes entradas, siga a orientação abaixo:
   
   **Tipo:** selecione o ponto final do Azure, porque vamos apontar para um endereço IP público do Azure.
   
   **Nome:** crie um nome que pretende associar ao ponto final. Isto não é o nome DNS e tem não qualquer efeito nos registos DNS.
   
   **Tipo de recurso de destino:** selecione o endereço IP público como valor para esta propriedade. 
   
   **Recurso de destino:** isto irá fornecer-lhe uma opção para escolher a partir de etiquetas de DNS diferentes que tem ao seu dispor na sua subscrição. Escolha a etiqueta DNS correspondente para o ponto final que está a configurar.
   
   Adicione um ponto final a cada região geográfica onde pretende que o Gestor de Tráfego do Azure encaminhe tráfego.
   Para obter mais informações e passos detalhados sobre como adicionar/configurar pontos finais no Gestor de Tráfego, veja o artigo [Adicionar, desativar, ativar ou eliminar pontos finais](../traffic-manager/traffic-manager-endpoints.md)
4. **Configurar pesquisa:** na página do Gestor de Tráfego, clique em Configuração. Na página de configuração, tem de alterar as definições do monitor para pesquisar na porta HTTP 80 e o caminho relativo /adfs/probe
   
    ![Configurar a sonda](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 
   
   > [!NOTE]
   > **Certifique-se de que o estado dos pontos finais está ONLINE, assim que a configuração for concluída**. Se todos os pontos finais estiverem num estado «degradado», o Gestor de Tráfego do Azure fará uma tentativa de utilizações para encaminhar o tráfego, partindo do princípio de que o diagnóstico está incorreto e todos os pontos finais estão acessíveis.
   > 
   > 
5. **Modificar registos DNS para o Gestor de Tráfego do Azure:** o seu serviço de federação deve ser um CNAME para o nome DNS do Gestor de Tráfego do Azure. Crie um CNAME nos registos DNS públicos para que quem esteja a tentar contactar o serviço de federação consiga de fato ter acesso ao Gestor de Tráfego do Azure.
   
    Por exemplo, para apontar o serviço de federação fs.fabidentity.com para o Gestor de Tráfego, terá de atualizar o seu registo de recursos DNS para ser o seguinte:
   
    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

## <a name="test-the-routing-and-ad-fs-sign-in"></a>Testar o encaminhamento e início de sessão do AD FS
### <a name="routing-test"></a>Teste de encaminhamento
Um teste muito básico para o encaminhamento é tentar fazer ping do nome DNS do serviço de federação a partir de uma máquina em cada região geográfica. Dependendo do método de encaminhamento escolhido, o ponto final que fizer ping será refletido no visor de ping. Por exemplo, se tiver selecionado o encaminhamento de desempenho, o ponto final mais próximo de uma região do cliente será acessível. Segue-se o instantâneo de dois pings provenientes de duas máquinas de cliente de regiões diferentes, um no Sudeste Asiático e outra em E.U.A. Oeste. 

![Teste de encaminhamento](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

### <a name="ad-fs-sign-in-test"></a>Teste de início de sessão do AD FS
A maneira mais fácil de testar o AD FS é através da página IdpInitiatedSignon.aspx. Para poder fazer isso, é obrigatório ativar o IdpInitiatedSignOn nas propriedades do AD FS. Siga os passos abaixo para verificar a sua configuração do AD FS

1. Execute o cmdlet abaixo no servidor AD FS com o PowerShell, para o definir como ativado. 
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
2. Em qualquer máquina externa, aceda a https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Deverá ver a página do AD FS, conforme mostrado abaixo:
   
    ![Teste de ADFS - desafio de autenticação](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)
   
    e após o início de sessão com êxito, é-lhe apresentada uma mensagem de êxito, conforme mostrado abaixo:
   
    ![Teste de ADFS - autenticação bem sucedida](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)

## <a name="related-links"></a>Ligações relacionadas
* [Implementação básica do AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
* [Gestor de Tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
* [Métodos de encaminhamento de tráfego do Gestor de Tráfego](../traffic-manager/traffic-manager-routing-methods.md)

## <a name="next-steps"></a>Passos seguintes
* [Gerir um perfil no Traffic Manager do Azure](../traffic-manager/traffic-manager-manage-profiles.md)
* [Adicionar, desativar, ativar ou eliminar pontos finais](../traffic-manager/traffic-manager-endpoints.md) 

