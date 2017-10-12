---
title: "Serviços de Federação do Active Directory no Azure | Microsoft Docs"
description: 'Neste documento, vai aprender a implementar o AD FS no Azure para disponibilidade elevada '
keywords: "implementar o AD FS no azure, implementar o azure adfs, azure adfs, azure ad fs, implementar adfs, implementar ad fs, adfs no azure, implementar adfs no azure, implementar AD FS no azure, adfs azure, introdução ao AD FS, Azure, AD FS no Azure, iaas, ADFS, mover adfs para o azure"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 692a188c-badc-44aa-ba86-71c0e8074510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: anandy; billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddd29a1230286de8999175498ee793f3b3ea24e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploying-active-directory-federation-services-in-azure"></a>Implementação do Serviço de Federação do Active Directory no Azure
O AD FS proporciona federação de identidade simplificada e protegida e capacidades de início de sessão único (SSO) na Web. A Federação com o Azure AD ou o O365 permite aos utilizadores autenticarem-se com credenciais no local e aceder a todos os recursos na nuvem. Como resultado, torna-se importante ter uma infraestrutura do AD FS de elevada disponibilidade para garantir acesso aos recursos no local e na nuvem. Implementar o AD FS no Azure pode ajudar a alcançar a elevada disponibilidade necessária com um esforço mínimo.
Existem várias vantagens de implementar o AD FS no Azure, entre as quais:

* **Elevada Disponibilidade** - com o poder dos Conjuntos de Disponibilidade do Azure, garante uma infraestrutura de elevada disponibilidade.
* **Fácil de Dimensionar** – precisa de mais desempenho? Migre facilmente para máquinas mais poderosas com apenas alguns cliques no Azure.
* **Redundância Geográfica Cruzada** - com a Redundância Geográfica do Azure, pode ter a garantia de que a sua infraestrutura é altamente disponível em todo o mundo.
* **Fácil de Gerir** – com as opções de gestão altamente simplificadas no portal do Azure, gerir a sua infraestrutura é muito fácil e descomplicado. 

## <a name="design-principles"></a>Princípios de conceção
![Conceção da implementação](./media/active-directory-aadconnect-azure-adfs/deployment.png)

O diagrama acima apresenta a topologia básica recomendada para começar a implementar a infraestrutura do AD FS no Azure. Os princípios envolvidos nos vários componentes da topologia estão listados abaixo:

* **Servidores DC / AD FS**: se tiver menos de mil utilizadores, pode simplesmente instalar a função do AD FS nos seus controladores de domínio. Se não pretender nenhum impacto no desempenho nos controladores de domínio ou se tiver mais de mil utilizadores, implemente o AD FS em servidores separados.
* **Servidor do WAP** – é necessário para implementar servidores Proxy de Aplicações Web, para que os utilizadores possam contactar o AD FS quando não estão também na rede da empresa.
* **DMZ**: os servidores Proxy de Aplicações Web serão colocados na rede de perímetro e SÓ é permitido acesso TCP/443 entre a rede de perímetro e a sub-rede interna.
* **Balanceadores de Carga**: para garantir a elevada disponibilidade dos servidores do AD FS e Proxy de Aplicações Web, recomendamos que utilize um balanceador de carga interno para os servidores AD FS e o Azure Load Balancer para os servidores Proxy de Aplicações Web.
* **Conjuntos de Disponibilidade**: para fornecer redundância para a sua implementação do AD FS, recomenda-se que agrupe duas ou mais máquinas virtuais num Conjunto de Disponibilidade de cargas de trabalho semelhantes. Esta configuração garante que, durante um evento de manutenção planeado ou não planeado, está disponível, pelo menos, uma máquina virtual.
* **Contas de Armazenamento**: recomenda-se ter duas contas de armazenamento. Ter uma única conta de armazenamento pode levar à criação de um só ponto de falha e pode fazer com que a implementação fique indisponível num cenário improvável em que a conta de armazenamento fique inativa. Ao invés, ter duas contas de armazenamento ajuda a associar uma conta de armazenamento a cada linha de falhas.
* **Segregação de Rede**: os servidores Proxy de Aplicações Web devem ser implementados numa rede de perímetro separada. Pode dividir uma rede virtual em duas sub-redes e, em seguida, implementar o servidor ou servidores Proxy de Aplicações Web numa sub-rede isolada. Pode simplesmente configurar as definições do grupo de segurança de rede para cada sub-rede e permitir apenas a comunicação necessária entre as duas sub-redes. São fornecidos mais detalhes por cenário de implementação abaixo

## <a name="steps-to-deploy-ad-fs-in-azure"></a>Passos para implementar o AD FS no Azure.
Os passos mencionados nesta secção destacam o guia para implementar a infraestrutura do AD FS no Azure descrita abaixo.

### <a name="1-deploying-the-network"></a>1. Implementar a rede
Conforme mencionado acima, pode criar duas sub-redes numa única rede virtual ou criar duas redes virtuais (VNet) completamente diferentes. Este artigo foca-se na implementação de uma única rede virtual e em dividi-la em duas sub-redes. Esta é, neste momento, uma abordagem mais fácil, porque criar duas VNets separadas exigiria um gateway de VNet para VNet para as comunicações.

**1.1 Criar a rede virtual**

![Criar a rede virtual](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)

No portal do Azure, selecione “rede virtual” e pode implementar a rede virtual e uma sub-rede imediatamente com um só clique. A sub-rede INT também é definida e está agora pronta para receber as VMs.
O passo seguinte consiste em adicionar outra sub-rede à rede, por exemplo, a sub-rede de perímetro.
 Para criar a sub-rede de perímetro, basta:


* Selecionar a rede acabada de criar
* Selecionar a sub-rede nas propriedades
* Clicar no botão “adicionar” no painel da sub-rede
* Indicar o nome e a informação de espaço do endereço da sub-rede para criá-la

![Subrede](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)

![Sub-rede de perímetro
](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. Criar os grupos de segurança de rede**

Os Grupos de Segurança de Rede (NSG) contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede para as instâncias da sua VM numa Rede Virtual. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede.
Para efeitos desta orientação, vamos criar dois NSGs, um para a rede interna e outro para o rede de perímetro. Serão chamados NSG_INT e NSG_DMZ, respetivamente.

![Criar o NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Depois de criado o NSG, existiram 0 regras de entrada e 0 regras de saída. Quando as funções nos respetivos servidores estiverem instaladas e funcionais, as regras de entrada e de saída podem ser feitas de acordo com o nível de segurança pretendido.

![Inicializar o NSG](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Depois de criados os NSGs, associe NSG_INT à sub-rede INT e NSG_DMZ à sub-rede de perímetro.
 É apresentada uma captura de ecrã de exemplo abaixo:

![Configurar o NGS](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Clique em “Sub-redes” para abrir o painel das sub-redes
* Selecione a sub-rede a associar ao NSG 

Após a configuração, o painel das Sub-redes deve ter o seguinte aspeto:

![Sub-redes após associar o NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Criar a Ligação ao local**

Vamos precisar de uma ligação ao local para podermos implementar o controlador de domínio (DC) no Azure. O Azure oferece várias opções de conectividade para ligar a infraestrutura no local à infraestrutura do Azure.

* Ponto a site
* Rede Virtual Site a Site
* ExpressRoute

É recomendado utilizar o ExpressRoute. O ExpressRoute permite-lhe criar ligações privadas entre os datacenters do Azure e a sua infraestrutura no local ou num ambiente de colocalização. As ligações do ExpressRoute não passam para a Internet pública. Oferecem mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.
Embora seja recomendado utilizar o ExpressRoute, pode escolher qualquer método de ligação que seja mais adequado para a sua organização. Para saber mais sobre o ExpressRoute e as várias opções de conectividade que este oferece, leia [Descrição geral técnica do ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. Criar contas de armazenamento
Para poder manter a elevada disponibilidade e evitar a dependência a uma única conta de armazenamento, pode criar duas contas de armazenamento. Divida as máquinas em cada conjunto de disponibilidade em dois grupos e, em seguida, atribua uma conta de armazenamento separada a cada um.

![Criar contas de armazenamento](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. Criar conjuntos de disponibilidade
Para cada função (DC/AD FS e WAP), crie conjuntos de disponibilidade que vão conter duas máquinas cada um, no mínimo. Desta forma, é mais fácil alcançar a elevada disponibilidade em cada função. Ao criar os conjuntos de disponibilidade , é essencial decidir o seguinte:

* **Domínios de Falha**: as máquinas virtuais no mesmo domínio de falha partilham a mesma origem de energia e o mesmo comutador de rede física. Recomendamos um mínimo de dois domínios de falha. O valor predefinido é 3 e pode deixá-lo tal como está para efeitos desta implementação
* **Domínios de Atualização**: as máquinas que pertençam ao mesmo domínio de atualização são reiniciadas em conjunto durante uma atualização. O ideal é ter, no mínimo, dois domínios de atualização. O valor predefinido é 5 e pode deixá-lo tal como está para efeitos desta implementação

![Conjuntos de disponibilidade](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Crie os conjuntos de disponibilidade seguintes

| Conjunto de Disponibilidade | Função | Domínios de falha | Domínios de atualização |
|:---:|:---:|:---:|:--- |
| contosodcset |DC/ADFS |3 |5 |
| contosowapset |WAP |3 |5 |

### <a name="4-deploy-virtual-machines"></a>4. Implementar máquinas virtuais
O passo seguinte é implementar máquinas virtuais que vão alojar as diferentes funções da sua infraestrutura. Recomenda-se um mínimo de duas máquinas em cada conjunto de disponibilidade. Crie quatro máquinas virtuais para a implementação básica.

| Máquina | Função | Subrede | Conjunto de disponibilidade | Conta de armazenamento | Endereço IP |
|:---:|:---:|:---:|:---:|:---:|:---:|
| contosodc1 |DC/ADFS |INT |contosodcset |contososac1 |Estático |
| contosodc2 |DC/ADFS |INT |contosodcset |contososac2 |Estático |
| contosowap1 |WAP |Rede de Perímetro |contosowapset |contososac1 |Estático |
| contosowap2 |WAP |Rede de Perímetro |contosowapset |contososac2 |Estático |

Como poderá ter reparado, não foi especificado nenhum NSG. Isto acontece porque o Azure permite-lhe utilizar o NSG ao nível da sub-rede. Depois, pode controlar o tráfego de rede da máquina com o NSG individual associado à sub-rede ou ao objeto NIC. Leia mais em [O que é um Grupo de Segurança de Rede (NSG)?](https://aka.ms/Azure/NSG)
Se estiver a gerir o DNS, recomenda-se o endereço IP estático. Pode utilizar o DNS do Azure e consulte as novas máquinas pelos respetivos FQDNs do Azure em vez de pelos registos DNS do seu domínio.
Depois de concluída a implementação, o painel da máquina virtual deverá ser semelhante ao seguinte:

![Máquinas Virtuais implementadas](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. Configurar os servidores de controlador de domínio / AD FS
 Para autenticar qualquer pedido de entrada, o AD FS tem de contactar o controlador de domínio. Para poupar a viagem dispendiosa do Azure para o DC no local relativamente à autenticação, recomenda-se a implementação de uma réplica do controlador de domínio no Azure. Para obter a elevada disponibilidade, recomenda-se a criação de um conjunto de disponibilidade com, no mínimo, dois controladores de domínio.

| Controlador de domínio | Função | Conta de armazenamento |
|:---:|:---:|:---:|
| contosodc1 |Réplica |contososac1 |
| contosodc2 |Réplica |contososac2 |

* Promover os dois servidores como controladores de domínio de réplica com o DNS
* Configure os servidores do AD FS ao instalar a função do AD FS com o gestor de servidor.

### <a name="6-deploying-internal-load-balancer-ilb"></a>6. Implementar o Balanceador de Carga Interno (Internal Load Balancer, ILB)
**6.1. Criar o ILB**

Para implementar um ILB, selecione Balanceadores de Carga no portal do Azure e clique em “Adicionar” (+).

> [!NOTE]
> Se não vir **Balanceadores de Carga** no menu, clique em **Procurar**, no canto inferior esquerdo do portal, e desloque-se até ver **Balanceadores de Carga**.  Em seguida, clique na estrela amarela para adicioná-lo ao menu. Agora, selecione o ícone de balanceador de carga novo para abrir o painel e iniciar a configuração do balanceador de carga.
> 
> 

![Procurar o balanceador de carga](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nome**: dê um nome adequado ao balanceador de carga
* **Esquema**: uma vez que este balanceador de carga será colocado à frente de servidores AD FS e se destina a ligações de rede interna APENAS, selecione "Interno"
* **Rede Virtual**: escolha a rede virtual a partir na qual está a implementar o AD FS
* **Sub-rede**: selecione a sub-rede interna aqui
* **Atribuição de endereços IP**: estática

![Balanceador de carga interno](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)

Depois de clicar em “Criar” e o ILB ser implementado, deve vê-lo na lista de balanceadores de carga:

![Balanceadores de carga depois do ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)

O passo seguinte é configurar o conjunto de back-ends e a sonda de back-ends.

**6.2. Configurar o conjunto de back-ends do ILB**

Selecione o ILB recentemente criado no painel Balanceadores de Carga. É aberto o painel de definições. 

1. Selecione os conjuntos de back-ends a partir do painel de definições
2. No painel de conjuntos de back-ends, clique em “adicionar máquina virtual”
3. É-lhe apresentado um painel onde pode escolher o conjunto de disponibilidade
4. Escolha o conjunto de disponibilidade do AD FS

![Configurar o conjunto de back-ends do ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)

**6.3. A configurar a sonda**

No painel de definições do ILB, selecione Sondas.

1. Clique em “adicionar”
2. Indique os detalhes da sonda a. **Nome**: nome da sonda b. **Protocolo**: TCP c. **Porta**: 443 (HTTPS) d. **Intervalo**: 5 (valor predefinido) – este é o intervalo a que o ILB vai sondar as máquinas no conjunto de back-ends e. **Limite do limiar de mau estado de funcionamento**: 2 (valor predefinido) – este é o limiar de falhas de sonda consecutivas após o qual o ILB vai declarar uma máquina no conjunto de back-ends como não reativa e parar de enviar o tráfego para a mesma.

![Configurar a sonda do ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)

**6.4. Criar regras de balanceamento de carga**

Para poder balancear eficazmente o tráfego, o ILB deve ser configurado com regras de balanceamento de carga. Para criar uma regra de balanceamento de carga: 

1. Selecione a regra de balanceamento de carga a partir do painel de definições do ILB
2. Clique em “Adicionar” no painel Regras de balanceamento de carga
3. No painel Adicionar regra de balanceamento de carga a. **Nome**: indique um nome para a regra b. **Protocolo**: selecione TCP c. **Porta**: 443 d. **Porta de back-end**: 443 e. **Conjunto de back-ends**: selecione o conjunto que criou para o cluster do AD FS anteriormente f. **Sonda**: selecione a sonda criada para os servidores AD FS criada anteriormente

![Configurar as regras de balanceamento do ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. Atualizar o DNS com o ILB**

Aceda ao seu servidor DNS e crie um CNAME para o ILB. O CNAME deve destinar-se ao serviço de federação com o endereço IP que está a apontar para o endereço IP do ILB. Por exemplo, se o endereço IDP do ILB for 10.3.0.8 e o serviço de federação instalado for fs.contoso.com, crie um CNAME para fs.contoso.com a apontar para 10.3.0.8.
Desta forma, é garantido que todas as comunicações relativas a fs.contoso.com chegam ao ILB e são encaminhadas adequadamente.

### <a name="7-configuring-the-web-application-proxy-server"></a>7. Especificar o servidor Proxy de Aplicações Web
**7.1. Configurar os servidores do Proxy de Aplicações Web para alcançar os servidores AD FS**

Para garantir que os servidores de Proxy de Aplicações Web conseguem aceder aos servidores AD FS atrás do ILB, crie um registo em %systemroot%\system32\drivers\etc\hosts para o ILB. Tenha em atenção que o nome único (DN) deve ser o nome do serviço de federação, como, por exemplo, fs.contoso.com. E a entrada IP deve ser a do endereço IP do ILB (10.3.0.8, como no exemplo).

**7.2. Instalar a função de Proxy de Aplicações Web**

Depois de assegurar que os servidores Proxy de Aplicações Web conseguem contactar os servidores AD FS por detrás do ILB, pode instalar os servidores Proxy de Aplicações Web. Os servidores Proxy de Aplicações Web não podem ser associados ao domínio. Instale as funções Proxy de Aplicações Web nos dois servidores Proxy de Aplicações Web ao selecionar a função Acesso Remoto. O gestor de servidor vai guiá-lo para concluir a instalação do WAP.
Para obter mais informações sobre como implementar o WAP, veja [Install and Configure the Web Application Proxy Server (Instalar e Configurar o Servidor Proxy de Aplicações Web)](https://technet.microsoft.com/library/dn383662.aspx).

### <a name="8--deploying-the-internet-facing-public-load-balancer"></a>8.  Implementar o Balanceador de Carga com Acesso à Internet (Público)
**8.1.  Criar o Balanceador de Carga com Acesso à Internet (Público)**

No portal do Azure, selecione “Balanceadores de carga” e, em seguida, clique em “Adicionar”. No painel Criar balanceador de carga, introduza as seguintes informações

1. **Nome**: nome do balanceador de carga
2. **Esquema**: público – esta opção diz ao Azure que este balanceador de carga vai precisar de um endereço público
3. **Endereço IP**: criar um novo endereço IP (dinâmico)

![Balanceador de carga com acesso à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Após a implementação, o balanceador de carga irá aparecer na lista de Balanceadores de carga.

![Lista de balanceadores de carga](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)

**8.2. Atribuir uma etiqueta de DNS ao IP público**

Clique na entrada do balanceador carga criado recentemente no painel de balanceadores de carga para mostrar o painel da configuração. Siga os passos abaixo para configurar a etiqueta de DNS para o IP público:

1. Clique no endereço de IP público. É aberto o painel do IP público e das respetivas definições
2. Clique em Configuração
3. Forneça uma etiqueta de DNS. Esta etiqueta torna-se a etiqueta de DNS pública a que pode aceder a partir de qualquer lugar, como, por exemplo, contosofs.westus.cloudapp.azure.com. Pode adicionar uma entrada no DNS externo do serviço de federação (como fs.contoso.com) que é resolvida para a etiqueta de DNS do balanceador de carga externo (contosofs.westus.cloudapp.azure.com).

![Configurar o balanceador de carga com acesso à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configurar o balanceador de carga com acesso à Internet (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. Configurar o conjunto de back-ends do Balanceador de Carga com Acesso à Internet (Público)** 

Siga os passos da criação do balanceador de carga interno para configurar o conjunto de back-ends do balanceador de carga com Acesso à Internet (Público) como o conjunto de disponibilidade dos servidores WAP. Por exemplo, contosowapset.

![Configurar o conjunto de back-ends do Balanceador de Carga com Acesso à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)

**8.4. Configurar a sonda**

Siga os passos da configuração do balanceador de carga interno para configurar a sonda do conjunto de back-ends dos servidores WAP.

![Configurar a sonda do Balanceador de Carga com Acesso à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)

**8.5. Criar regra(s) de balanceamento de carga**

Siga os passos do ILB para configurar a regra de balanceamento de carga para TCP 443.

![Configurar as regras de balanceamento do Balanceador de Carga com Acesso à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)

### <a name="9-securing-the-network"></a>9. Proteger a rede
**9.1. Proteger a sub-rede interna**

De um modo geral, precisa das regras seguintes para proteger de forma eficiente a sua sub-rede interna (conforme a ordem apresentada abaixo)

| Regra | Descrição | Fluxo |
|:--- |:--- |:---:|
| AllowHTTPSFromDMZ |Permitir a comunicação HTTPS a partir da rede de perímetro |Entrada |
| DenyInternetOutbound |Sem acesso à Internet |Saída |

![Regras de acesso INT (entrada)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[comment]: <> (![regras de acesso INT (entrada)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [comment]: <> (![regras de acesso INT(saída)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))

**9.2. Proteger a sub-rede de perímetro**

| Regra | Descrição | Fluxo |
|:--- |:--- |:---:|
| AllowHTTPSFromInternet |Permitir HTTPS a partir da Internet para a rede de perímetro |Entrada |
| DenyInternetOutbound |Está tudo bloqueado, exceto HTTPS para a Internet |Saída |

![Regras de acesso EXT (entrada)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[comment]: <> (![regras de acesso EXT (entrada)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [comment]: <> (![regras de acesso EXT (saída)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

> [!NOTE]
> Se a autenticação de certificado de utilizador cliente (autenticação clientTLS com certificados de utilizador X509) for necessária, o AD FS requer que a porta TCP 49443 esteja ativada para acesso de entrada.
> 
> 

### <a name="10-test-the-ad-fs-sign-in"></a>10. Testar o início de sessão do AD FS
A forma mais fácil é testar o AD FS com a página IdpInitiatedSignon.aspx. Para poder fazer isso, é obrigatório ativar o IdpInitiatedSignOn nas propriedades do AD FS. Siga os passos abaixo para verificar a sua configuração do AD FS

1. Execute o cmdlet abaixo no servidor AD FS com o PowerShell, para o definir como ativado.
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true 
2. Em qualquer máquina externa, aceda a https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3. Deverá ver a página do AD FS, conforme mostrado abaixo:

![Página de início de sessão de teste](./media/active-directory-aadconnect-azure-adfs/test1.png)

Após o início de sessão com êxito, é-lhe apresentada uma mensagem de êxito, conforme mostrado abaixo:

![Teste bem-sucedido](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Modelo para implementar o AD FS no Azure
O modelo implementa uma configuração de seis computadores, dois para os Controladores de Domínio, dois para o AD FS e dois para o WAP.

[AD FS no Modelo de Implementação do Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Pode utilizar uma rede virtual existente ou criar uma nova VNET ao implementar este modelo. Os vários parâmetros disponíveis para personalizar a implementação são listados abaixo com a descrição da utilização do parâmetro no processo de implementação. 

| Parâmetro | Descrição |
|:--- |:--- |
| Localização |A região na qual se implementa os recursos, por exemplo, EUA Leste. |
| StorageAccountType |O tipo da Conta de Armazenamento criado |
| VirtualNetworkUsage |Indica se uma nova rede virtual será criada ou se será utilizada uma existente |
| VirtualNetworkName |O nome da Rede Virtual a Criar, obrigatório na utilização de uma rede virtual nova ou existente |
| VirtualNetworkResourceGroupName |Especifica o nome do grupo de recursos onde reside a rede virtual existente. Quando utilizar uma rede virtual existente, esta torna-se um parâmetro obrigatório para que a implementação possa encontrar o ID da rede virtual existente |
| VirtualNetworkAddressRange |O intervalo de endereços da nova VNET, obrigatório se criar uma nova rede virtual |
| InternalSubnetName |O nome da sub-rede interna, obrigatório em ambas as opções de utilização da rede virtual (nova ou existente) |
| InternalSubnetAddressRange |O intervalo de endereços da sub-rede interna, que contém os servidores ADFS e os Controladores de Domínio, obrigatório se criar uma nova rede virtual. |
| DMZSubnetAddressRange |O intervalo de endereços da sub-rede do dmz, que contém os servidores proxy de aplicações Windows, obrigatório se criar uma nova rede virtual. |
| DMZSubnetName |O nome da sub-rede interna, obrigatório em ambas as opções de utilização de rede virtual (nova ou existente). |
| ADDC01NICIPAddress |O endereço IP interno do primeiro Controlador de Domínio, este endereço IP será estaticamente atribuído ao DC e terá de ser um endereço IP válido na sub-rede interna |
| ADDC02NICIPAddress |O endereço IP interno do segundo Controlador de Domínio, este endereço IP será estaticamente atribuído ao DC e terá de ser um endereço IP válido na sub-rede interna |
| ADFS01NICIPAddress |O endereço IP interno do primeiro servidor ADFS, este endereço IP será estaticamente atribuído ao servidor ADFS e terá de ser um endereço IP válido na sub-rede interna |
| ADFS02NICIPAddress |O endereço IP interno do segundo servidor ADFS, este endereço IP será estaticamente atribuído ao servidor ADFS e terá de ser um endereço IP válido na sub-rede interna |
| WAP01NICIPAddress |O endereço IP interno do primeiro servidor WAP, este endereço IP será estaticamente atribuído ao servidor WAP e terá de ser um endereço IP válido na sub-rede do DMZ |
| WAP02NICIPAddress |O endereço IP interno do segundo servidor WAP, este endereço IP será estaticamente atribuído ao servidor WAP e terá de ser um endereço IP válido na sub-rede do DMZ |
| ADFSLoadBalancerPrivateIPAddress |O endereço IP interno do balanceamento de carga ADFS, este endereço IP será estaticamente atribuído ao balanceador de carga e terá de ser um endereço IP válido na sub-rede Interna |
| ADDCVMNamePrefix |Prefixo do nome da Máquina Virtual para os Controladores de Domínio |
| ADFSVMNamePrefix |Prefixo do nome da Máquina Virtual para os servidores ADFS |
| WAPVMNamePrefix |Prefixo do nome da Máquina Virtual para os servidores WAP |
| ADDCVMSize |O tamanho da VM dos Controladores de Domínio |
| ADFSVMSize |O tamanho da VM dos servidores ADFS |
| WAPVMSize |O tamanho da VM dos servidores WAP |
| AdminUserName |O nome do Administrador local das máquinas virtuais |
| AdminPassword |A palavra-passe da conta do Administrador local das máquinas virtuais |

## <a name="additional-resources"></a>Recursos adicionais
* [Conjuntos de Disponibilidade](https://aka.ms/Azure/Availability) 
* [Azure Load Balancer](https://aka.ms/Azure/ILB)
* [Balanceador de carga interno](https://aka.ms/Azure/ILB/Internal)
* [Balanceador de Carga com Acesso à Internet](https://aka.ms/Azure/ILB/Internet)
* [Contas de armazenamento](https://aka.ms/Azure/Storage)
* [Redes Virtuais do Azure](https://aka.ms/Azure/VNet)
* [Ligações para o AD FS e o Proxy de Aplicações Web](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Passos seguintes
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
* [Configurar e gerir o AD FS com o Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [Implementação de AD FS geográficos cruzados de elevada disponibilidade no Azure com o Gestor de Tráfego do Azure](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

