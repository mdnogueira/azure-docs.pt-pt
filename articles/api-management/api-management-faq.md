---
title: "FAQ sobre gestão de API do Azure | Microsoft Docs"
description: "Saiba as respostas a perguntas comuns, padrões e melhores práticas na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: a9740cf527e4a9811b510ad5c96e5ab769efc2d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-faqs"></a>Perguntas mais frequentes sobre gestão de API do Azure
Obter as respostas a perguntas comuns, padrões e melhores práticas para a API Management do Azure.

## <a name="contact-us"></a>Contacte-nos
* [Como pode posso fazer a equipa de gestão de API do Microsoft Azure de uma pergunta?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
* [O que o significa de uma funcionalidade está em pré-visualização?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Como posso proteger a ligação entre o gateway de gestão de API e os meus serviços de back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Como copiar o meu instância de serviço de API Management para uma nova instância?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Posso gerir os meus instância da API Management programaticamente?](#can-i-manage-my-api-management-instance-programmatically)
* [Como adicionar um utilizador ao grupo de administradores?](#how-do-i-add-a-user-to-the-administrators-group)
* [Por que motivo é a política que pretendo adicionar disponível no editor de políticas?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Como utilizar o controlo de versões de API na API Management?](#how-do-i-use-api-versioning-in-api-management)
* [Como configurar vários ambientes numa única API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Pode utilizar SOAP com a API Management?](#can-i-use-soap-with-api-management)
* [É a constante de endereço IP de gateway de gestão de API? Posso utilizá-lo em regras de firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Pode configurar um servidor de autorização do OAuth 2.0 com a segurança do AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [Que método de encaminhamento utilizar a API Management em implementações em várias localizações geográficas?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Pode utilizar um modelo Azure Resource Manager para criar uma instância de serviço de API Management?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Pode utilizar um certificado SSL autoassinado para um back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Por que motivo recebo uma falha de autenticação ao tentar clonar um repositório de GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Gestão de API funciona com o Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Por que motivo é necessário uma sub-rede dedicada no Gestor de recursos de estilo VNETs quando a API Management está implementada para-las?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [O que é o tamanho da sub-rede mínimo necessário ao implementar a API Management para uma VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Posso mover um serviço de gestão de API de uma subscrição para outro?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Existem restrições ou problemas conhecidos com importar meu API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Como pode posso fazer a equipa de gestão de API do Microsoft Azure de uma pergunta?
Pode contactar-nos através de uma destas opções:

* Publique as suas perguntas nos nossos [fórum MSDN de gestão de API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Envie um e-mail para < mailto:apimgmt@microsoft.com >.
* Envie-num pedido de funcionalidade [fórum de comentários do Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>O que o significa de uma funcionalidade está em pré-visualização?
Quando é uma funcionalidade de pré-visualização, significa está a procura ativamente comentários sobre a forma como a funcionalidade está a funcionar para si. Uma funcionalidade em pré-visualização é funcionalmente concluída, mas é possível que iremos irá efetuar uma quebra alterar em resposta a comentários de clientes. Recomendamos que não dependem uma funcionalidade que está em pré-visualização no seu ambiente de produção. Se tiver quaisquer comentários sobre as funcionalidades de pré-visualização, indique através de uma das opções de contactos no [como pode posso fazer a equipa de gestão de API do Microsoft Azure uma pergunta?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Como posso proteger a ligação entre o gateway de gestão de API e os meus serviços de back-end?
Tem várias opções para proteger a ligação entre o gateway de gestão de API e os serviços de back-end. Pode:

* Utilize a autenticação básica de HTTP. Para obter mais informações, consulte [as definições da API configurar](api-management-howto-create-apis.md#configure-api-settings).
* Utilizar a autenticação mútua de SSL, conforme descrito em [como proteger serviços de back-end utilizando o cliente de autenticação de certificado na API Management do Azure](api-management-howto-mutual-certificates.md).
* Utilize Adicionar à lista branca IP do seu serviço de back-end. Se tiver uma instância de gestão de API de camada Standard ou Premium, o endereço IP do gateway permanece constante. Pode definir a lista branca, para permitir que este endereço IP. Pode obter o endereço IP da sua instância de gestão de API no Dashboard do portal do Azure.
* Ligar a instância de API Management para uma rede Virtual do Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Como copiar o meu instância de serviço de API Management para uma nova instância?
Tem várias opções se pretender copiar uma instância de API Management para uma nova instância. Pode:

* Utilize a cópia de segurança e restaurar a função na API Management. Para obter mais informações, consulte [como implementar a recuperação após desastre utilizando serviço cópia de segurança e restaurar na API Management do Azure](api-management-howto-disaster-recovery-backup-restore.md).
* Criar a suas próprias cópia de segurança e restaurar a funcionalidade utilizando o [API de REST de gestão de API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Utilize a API REST para guardar e restaurar as entidades da instância de serviço que quiser.
* Transferir a configuração do serviço utilizando o Git e, em seguida, carregue-o para uma nova instância. Para obter mais informações, consulte [como guardar e configurar a configuração do serviço de API Management utilizando o Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Posso gerir os meus instância da API Management programaticamente?
Sim, pode gerir API Management através de programação utilizando:

* O [REST API de gestão de API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* O [biblioteca de gestão de serviço ApiManagement do Microsoft Azure SDK](http://aka.ms/apimsdk).
* O [implementação de serviços](https://msdn.microsoft.com/library/mt619282.aspx) e [gestão de serviço](https://msdn.microsoft.com/library/mt613507.aspx) cmdlets do PowerShell.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Como adicionar um utilizador ao grupo de administradores?
Eis como pode adicionar um utilizador ao grupo de administradores:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Vá para o grupo de recursos à instância de gestão de API que pretende atualizar.
3. Na API Management, atribua o **contribuinte de gestão de Api** função ao utilizador.

Agora a contribuinte adicionado recentemente pode utilizar o Azure PowerShell [cmdlets](https://msdn.microsoft.com/library/mt613507.aspx). Eis como iniciar sessão como administrador:

1. Utilize o `Login-AzureRmAccount` cmdlet para iniciar sessão.
2. Definir o contexto para a subscrição que tenha o serviço utilizando `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Obter um URL de início de sessão único utilizando `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Utilize o URL para aceder ao portal de administração.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Por que motivo é a política que pretendo adicionar disponível no editor de políticas?
Se a política que pretende adicionar aparece desativada ou sombreados no editor de políticas, não se esqueça de que está no âmbito correto para a política. Cada declaração de política foi concebida para a utilização de âmbitos específicos e secções de política. Para rever as secções de política e os âmbitos de uma política, consulte a secção de utilização da política no [políticas de gestão de API](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-use-api-versioning-in-api-management"></a>Como utilizar o controlo de versões de API na API Management?
Tem algumas opções para utilizar o controlo de versões de API na API Management:

* Na API Management, pode configurar as APIs para representar versões diferentes. Por exemplo, poderá ter duas APIs diferentes, MyAPIv1 e MyAPIv2. Um programador pode escolher a versão que o programador pretende utilizar.
* Também pode configurar a sua API com um URL de serviço não inclui um segmento de versão, por exemplo, https://my.api. Em seguida, configure um segmento de versão de cada operação [Rewrite URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) modelo. Por exemplo, pode ter uma operação com um [modelo URL](api-management-howto-add-operations.md#url-template) chamado /resource e um [Rewrite URL](api-management-howto-add-operations.md#rewrite-url-template) modelo chamado/v1/recursos. Pode alterar o valor de segmento de versão em separado para cada operação.
* Se gostaria de manter um segmento de versão "predefinido" no URL do serviço de API, no selecionada operations, definir uma política que utiliza o [definir o serviço de back-end](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) política para alterar o caminho de pedido de back-end.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Como configurar vários ambientes numa única API?
Configurar vários ambientes, por exemplo, um ambiente de teste e de ambiente de produção, uma única API, tem duas opções. Pode:

* APIs de anfitrião diferentes no mesmo inquilino.
* Aloje as APIs do mesmas em diferentes inquilinos.

### <a name="can-i-use-soap-with-api-management"></a>Pode utilizar SOAP com a API Management?
[Pass-through SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) suporte está agora disponível. Os administradores podem importar WSDL do seu serviço de SOAP e API Management do Azure irá criar um front-end SOAP. Documentação do portal de programador, a consola de teste, políticas e análise está disponível para os serviços SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>É a constante de endereço IP de gateway de gestão de API? Posso utilizá-lo em regras de firewall?
Em camadas Standard e Premium, o endereço IP público (VIP) do inquilino de gestão de API é estático para a duração do inquilino, com algumas exceções. As alterações do endereço IP nestas circunstâncias:

* O serviço é eliminado e, em seguida, voltar a criar.
* A subscrição de serviço é [suspenso](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ou [avisado](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (por exemplo, para nonpayment) e, em seguida, reinstated.
* Adicionar ou remover a rede Virtual do Azure (pode utilizar a rede Virtual apenas ao programador e o escalão Premium).

Para implementações de multirregião, o endereço regional alterado se a região é vacated e, em seguida, reinstated (pode utilizar a implementação de multirregião apenas na camada Premium).

Inquilinos de escalão Premium que estão configurados para implementação de multirregião são atribuídos um endereço IP público por região.

Pode obter o endereço (ou endereços IP, numa implementação de multirregião) na página de inquilino no portal do Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Pode configurar um servidor de autorização do OAuth 2.0 com a segurança do AD FS?
Para saber como configurar um servidor de autorização do OAuth 2.0 com segurança serviços de Federação do Active Directory (AD FS), consulte [utilizando ADFS na API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Que método de encaminhamento utilizar a API Management em implementações em várias localizações geográficas?
API Management utiliza o [método de encaminhamento de tráfego de desempenho](../traffic-manager/traffic-manager-routing-methods.md#priority) nas implementações em várias localizações geográficas. Tráfego de entrada é encaminhado para o gateway da API mais próximo. Se uma região ficar offline, o tráfego de entrada automaticamente é encaminhado para o gateway mais próximo seguinte. Saiba mais sobre os métodos de encaminhamento no [métodos de encaminhamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Pode utilizar um modelo Azure Resource Manager para criar uma instância de serviço de API Management?
Sim. Consulte o [serviço de gestão de API do Azure](http://aka.ms/apimtemplate) modelos de início rápido.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Pode utilizar um certificado SSL autoassinado para um back-end?
Sim. Isto pode ser feito através do PowerShell ou submetendo diretamente para a API. Isto irá desativar a validação da cadeia de certificados e permite-lhe utilizar certificados autoassinados ou em privado assinada quando comunicar da gestão de API com os serviços de back-end.

#### <a name="powershell-method"></a>Método do PowerShell ####
Utilize o [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (para o novo back-end) ou [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (para o back-end existente) cmdlets do PowerShell e defina o `-SkipCertificateChainValidation` parâmetro `True`. 

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Método de atualização de API direto ####
1. Criar um [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) entidade utilizando a API Management.       
2. Definir o **skipCertificateChainValidation** propriedade **verdadeiro**.     
3. Se já não pretender permitir que os certificados autoassinados, eliminar a entidade de back-end ou defina o **skipCertificateChainValidation** propriedade **falso**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Por que motivo recebo uma falha de autenticação ao tentar clonar um repositório de Git?
Se utilizar o Gestor de credenciais do Git, ou se está a tentar clonar um repositório de Git, utilizando o Visual Studio, poderá executar para um problema conhecido com a caixa de diálogo de credenciais do Windows. A caixa de diálogo limita o comprimento de palavra-passe 127 carateres e trunca a palavra-passe geradas pela Microsoft. Estamos a trabalhar encurtar a palavra-passe. Por agora, utilize Git Bash para clonar o repositório de Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Gestão de API funciona com o Azure ExpressRoute?
Sim. Gestão de API funciona com o Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Por que motivo é necessário uma sub-rede dedicada no Gestor de recursos de estilo VNETs quando a API Management está implementada para-las?
O requisito de sub-rede dedicado para gestão de API provém de facto, o que está incorporada no modelo de implementação clássica (PAAS V1 layer). Enquanto pode implementar para uma VNET do Resource Manager (V2 layer), existem consequências ao. O modelo de implementação clássico no Azure não é fortemente conjugado com o modelo do Resource Manager e, por isso, se criar um recurso na camada V2, a camada de V1 desconheça-lo e problemas podem acontecer, tais como a tentar utilizar um IP que já foi atribuído a um NIC de gestão de API  (criada no V2).
Para saber mais sobre a diferença dos modelos clássica e Resource Manager no Azure consulte [diferença nos modelos de implementação](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>O que é o tamanho da sub-rede mínimo necessário ao implementar a API Management para uma VNET?
O tamanho de sub-rede mínimos necessário para implementar a gestão de API é [/29](../virtual-network/virtual-networks-faq.md#configuration), que é o tamanho mínimo da sub-rede que suporte do Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Posso mover um serviço de gestão de API de uma subscrição para outro?
Sim. Para saber como, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Existem restrições ou problemas conhecidos com importar meu API?
[Problemas e restrições conhecidos](api-management-api-import-restrictions.md) para abrir API(Swagger), WSDL e WADL formatos.
