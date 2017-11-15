---
title: "Publicação de aplicações em redes separadas e localizações de utilização de grupos do conector no Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Aborda como criar e gerir grupos de conectores no Proxy de aplicações do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 477f20bd552460176be92f1db70bb0f76de8bac1
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicações em redes separadas e localizações utilizar grupos de conector

Os clientes utilizam o Proxy de aplicações do Azure AD para mais cenários e aplicações. Por isso que fizemos do Proxy de aplicação ainda mais flexível Ativando topologias mais. Pode criar grupos de conetor da Proxy da aplicação para que pode atribuir conectores específicos para servirem aplicações específicas. Esta capacidade dá-lhe mais controlo e formas para otimizar a implementação de Proxy de aplicações. 

Cada conector do Proxy da aplicação é atribuído a um grupo de conector. Todos os conectores que pertencem ao mesmo grupo de conector atuam como uma unidade separada para elevada disponibilidade e o balanceamento de carga. Todos os conectores pertencem a um grupo de conector. Se não criar grupos, todos os seus conectores estão num grupo predefinido. O administrador pode criar novos grupos e atribuir-lhes conectores no portal do Azure. 

Todas as aplicações são atribuídas a um grupo de conector. Se não criar grupos, todas as suas aplicações são atribuídas a um grupo predefinido. Mas se organizar os conectores em grupos, pode definir cada aplicação para trabalhar com um grupo específico de conector. Neste caso, apenas os conectores nesse grupo servem a aplicação mediante pedido. Esta funcionalidade é útil se as suas aplicações alojadas em localizações diferentes. Pode criar grupos de conector com base na localização, para que as aplicações são sempre servidas pelo conectores que estão fisicamente próximos-los.

>[!TIP] 
>Se tiver uma grande implementação do Proxy de aplicações, não atribua quaisquer aplicações ao grupo predefinido do conector. Dessa forma, os novos conectores não recebem qualquer tráfego em direto até que atribui a um grupo de conector do Active Directory. Esta configuração também lhe permite colocar os conectores de um modo inativo movendo-os novamente para o grupo predefinido, para que possam executar manutenção sem afetar os seus utilizadores.

## <a name="prerequisites"></a>Pré-requisitos
Para os conectores de grupo, tem de certificar-se de [instalados vários conectores](active-directory-application-proxy-enable.md). Quando instala um novo conector, é automaticamente associado a **predefinido** grupo de conector.

## <a name="create-connector-groups"></a>Criar grupos de conector
Utilize estes passos para criar grupos de conector tantos conforme pretender. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **do Azure Active Directory** > **aplicações empresariais** > **proxy de aplicações**.
2. Selecione **novo grupo de conector**. É apresentado o painel do novo grupo de conector.

   ![Selecione o novo grupo de conector](./media/active-directory-application-proxy-connectors-azure-portal/new-group.png)

3. Dê um nome do novo grupo de conector, em seguida, utilize o menu pendente para selecionar os conectores pertencem este grupo.
4. Selecione **Guardar**.

## <a name="assign-applications-to-your-connector-groups"></a>Atribuir as aplicações para os grupos de conector
Utilize estes passos para cada aplicação que já estejam publicados com o Proxy da aplicação. Pode atribuir uma aplicação a um grupo de conector quando publicar pela primeira vez, ou pode utilizar estes passos para alterar a atribuição de sempre que pretender.   

1. A partir do dashboard de gestão para o seu diretório, selecione **aplicações empresariais** > **todas as aplicações** > a aplicação que pretende atribuir a um grupo do conector > **Proxy de aplicações**.
2. Utilize o **conector grupo** menu pendente para selecionar o grupo que pretende que a aplicação para utilizar.
3. Selecione **guardar** para aplicar a alteração.

## <a name="use-cases-for-connector-groups"></a>Casos de utilização de grupos de conector 

Grupos de conector são úteis para vários cenários, incluindo:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Sites com vários centros de dados interligados

Muitas organizações têm um número de interligado centros de dados. Neste caso, que pretende manter o mesmo tráfego no Centro de dados possível, porque as ligações do Centro de dados entre são dispendiosas e lenta. Pode implementar conectores em cada datacenter para servir apenas as aplicações que residem no Centro de dados. Esta abordagem minimiza o Centro de dados entre ligações e proporciona uma experiência de completamente transparente para os seus utilizadores.

### <a name="applications-installed-on-isolated-networks"></a>Aplicações instaladas em redes isoladas

As aplicações podem ser alojadas nas redes que não fazem parte da rede empresarial principal. Pode utilizar grupos de conector para instalar os conectores dedicados em redes isoladas também isolar aplicações à rede. Normalmente, isto acontece quando um fornecedor de terceiros mantém uma aplicação específica para a sua organização. 

Grupos de conetor permitem-lhe instalar os conectores dedicados para essas redes que publicam apenas aplicações específicas, tornando mais fácil e mais segura para outsource gestão de aplicações para os fornecedores de terceiros.

### <a name="applications-installed-on-iaas"></a>Aplicações instaladas no IaaS 

Para as aplicações instaladas no IaaS para acesso à nuvem, os grupos de conector fornecem um serviço comuns para proteger o acesso a todas as aplicações. Grupos de conector não criar dependência adicional na sua rede empresarial ou a experiência de aplicação de fragmento. Os conectores podem ser instalados em todos os centros de dados de nuvem e servem apenas as aplicações que residem nessa rede. Pode instalar vários conectores para alcançarem elevada disponibilidade.

Utilizar como um exemplo de uma organização que tem várias máquinas virtuais ligadas a sua própria IaaS alojados rede virtual. Para permitir aos funcionários utilizar estas aplicações, estas redes privadas estão ligadas à rede empresarial através da VPN de site para site. Isto fornece uma boa experiência para os empregados que estão localizados no local. No entanto, poderá não ser ideal para os empregados remotos, porque requer mais infraestrutura no local para encaminhar o acesso, como pode ver no diagrama abaixo:

![Rede de Iaas AzureAD](./media/application-proxy-publish-apps-separate-networks/application-proxy-iaas-network.png)
  
Com os grupos de conector do Proxy de aplicações do Azure AD, pode ativar a um serviço comuns proteger o acesso a todas as aplicações sem criar dependência adicional na sua rede empresarial:

![Fornecedores de nuvem de Iaas vários AzureAD](./media/application-proxy-publish-apps-separate-networks/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Várias florestas – grupos de conetor diferente para cada floresta

Maioria dos clientes que tenham implementado o Proxy de aplicações estão a utilizar as respetivas single-sign-on (SSO) capacidades, efetuando a delegação restrita de Kerberos (KCD). Para atingir esse objetivo, máquinas o conector tem de estar associado a um domínio que pode delegar os utilizadores para a aplicação. KCD suporta as capacidades de múltiplas florestas. Mas para as empresas que têm diferentes ambientes de várias florestas sem qualquer fidedignidade entre eles, um único conector não pode ser utilizado para todas as florestas. 

Neste caso, conectores específicos podem ser implementados por floresta e definidos para servirem aplicações que foram publicadas para servir apenas utilizadores nessa floresta específica. Cada grupo de conector representa uma floresta diferente. Enquanto o inquilino e a maioria da experiência é unificada para todas as florestas, os utilizadores podem ser atribuídos a respetivas aplicações de floresta utilizando grupos do Azure AD.
 
### <a name="disaster-recovery-sites"></a>Locais de recuperação de desastre

Existem duas abordagens diferentes que pode tomar com um site de recuperação (DR) após desastre, dependendo de como os sites são implementados:

* Se o site de DR está incorporado no modo ativo-ativo onde é exatamente como o site principal e tem as mesmas redes e definições do AD, pode criar os conectores no site de DR no mesmo grupo de conector do site principal. Isto permite ao Azure AD detetar as ativações pós-falha para si.
* Se o site de DR está separado do site principal, pode criar um grupo de conector diferentes no site de DR, e a 1) tiver aplicações de cópia de segurança ou 2) manualmente divert aplicação existente para o grupo de conector de DR, conforme necessário.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Servir várias empresas a partir de um único inquilino

Existem muitas formas diferentes de implementar um modelo no qual um fornecedor de serviço única implementa e mantém do Azure AD relacionadas com os serviços de várias empresas. Os grupos de conector ajudam o administrador segregar conectores e aplicações em diferentes grupos. Uma forma, o que é adequada para pequenas empresas, é ter um único do Azure AD enquanto as empresas diferentes têm os seus próprios nome de domínio e redes de inquilino. Isto também se aplica para cenários de c & d e situações em que um único divisão de IT serve várias empresas por motivos de regulamentação ou horário comercial. 

## <a name="sample-configurations"></a>Configurações de exemplo

Alguns exemplos que pode implementar, incluem os seguintes grupos de conector.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Configuração predefinida-não utilize para grupos de conetor

Se não utilizar grupos de conector, a configuração teria este aspeto:

![AzureAD não existem grupos de conector](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-1.png)
 
Esta configuração é suficiente para pequenas implementações e testes. Também funcionarão corretamente se a sua organização tem uma topologia de rede simples.
 
### <a name="default-configuration-and-an-isolated-network"></a>Configuração predefinida e uma rede isolada

Esta configuração é uma evolução da predefinição, no qual é uma aplicação específica que é executada numa rede isolada, tais como a rede virtual IaaS: 

![AzureAD não existem grupos de conector](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Recomendado configuração – vários grupos específicos e um grupo predefinido para inativo

A configuração recomendada para organizações grandes e complexas é para que o grupo predefinido do conector como um grupo que não a servir quaisquer aplicações e é utilizado para conectores Inativos ou recentemente instalados. Todas as aplicações são servidas através de grupos de conetor personalizado. Isto permite que todos os a complexidade dos cenários descritos acima.

No exemplo abaixo, a empresa tem dois centros de dados, A e B, com dois conectores que servem de cada site. Cada site tem diferentes aplicações que são executadas no mesmo. 

![AzureAD não existem grupos de conector](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Passos seguintes

* [Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-understand-connectors.md)
* [Ativar o início de sessão único](application-proxy-sso-overview.md)


