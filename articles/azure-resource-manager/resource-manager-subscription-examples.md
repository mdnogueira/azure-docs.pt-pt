---
title: "Cenários e exemplos de governação de subscrição | Microsoft Docs"
description: "Fornece exemplos de como implementar a governação de subscrição do Azure para cenários comuns."
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: e8fbeeb8-d7a1-48af-804f-6fe1a6024bcb
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 4ab816d0392816c2293f9d70eb249bbcfa09bfba
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Exemplos para implementar andaime enterprise do Azure
Este tópico fornece exemplos de como uma empresa pode implementar as recomendações para uma [andaime Azure enterprise](resource-manager-subscription-governance.md). Utiliza uma empresa fictícias com o nome Contoso para ilustrar melhores práticas para cenários comuns.

## <a name="background"></a>Em segundo plano
Contoso é uma empresa em todo o mundo, que fornece soluções de cadeia de fornecimento de clientes em tudo de um modelo de "Software como um serviço" para um modelo em pacote implementado no local.  Desenvolvem software em todo o mundo com centros de desenvolvimento importantes no Índia, Estados Unidos e Canadá.

A parte de ISV da empresa está dividida em várias unidades de negócio independentes que gerem os produtos num negócio significativo. Cada unidade de negócio tem as suas próprias programadores, gestores de produto e arquitetos de TI.

A unidade de negócio de serviços de tecnologia de Enterprise (ETS) fornece a capacidade IT centralizada e gere vários centros de dados em unidades de negócio alojam as aplicações. Juntamente com a gestão de centros de dados, a organização ETS fornece e gere a colaboração centralizada (por exemplo, e-mail e os Web sites) e serviços de rede/telefonia. Também gerir orientado para o cliente cargas de trabalho para unidades de negócio mais pequenas que não tenham pessoal operacional.

As pessoas fictícias seguintes são utilizadas neste tópico:

* Dave é o administrador do ETS Azure.
* Alice é Director de desenvolvimento da Contoso na unidade de negócio de cadeia de fornecimento.

Contoso tem de criar uma aplicação de linha de negócios e uma aplicação orientado para o cliente. Decidiu-se executar as aplicações no Azure. Dave lê o [governação de subscrição prescritiva](resource-manager-subscription-governance.md) tópico e está agora pronto para implementar as recomendações.

## <a name="scenario-1-line-of-business-application"></a>Cenário 1: aplicações de linha de negócio
Contoso está a criar um sistema de gestão de código de origem (BitBucket) para ser utilizado pelos programadores por todo o mundo.  A aplicação utiliza a infraestrutura como serviço (IaaS) para alojar e consiste em servidores web e um servidor de base de dados. Os programadores aceder aos servidores nos respetivos ambientes de desenvolvimento, mas não precisam de aceder aos servidores no Azure. Contoso ETS pretende permitir que o proprietário da aplicação e a equipa gerir a aplicação. A aplicação só está disponível na rede empresarial da Contoso. Dave tem de configurar a subscrição para esta aplicação. A subscrição também irá alojar outra software relacionados com o Programador no futuro.  

### <a name="naming-standards--resource-groups"></a>Normas de nomenclatura & grupos de recursos
Dave cria uma subscrição para suportar as ferramentas de programação que são comuns a todas as unidades de negócio. Ele tem de criar nomes significativos para os grupos de recursos e subscrição (para a aplicação e as redes). Ele cria os grupos de subscrição e dos recursos seguintes:

| Item | Nome | Descrição |
| --- | --- | --- |
| Subscrição |Contoso ETS DeveloperTools produção |Suporta as ferramentas de programador comuns |
| Grupo de Recursos |bitbucket-prod-rg |Contém servidor de aplicação web e servidor de base de dados |
| Grupo de Recursos |corenetworks-prod-rg |Contém as redes virtuais e a ligação de gateway site para site |

### <a name="role-based-access-control"></a>Controlo de acesso baseado em funções
Depois de criar a subscrição, Dave pretende Certifique-se de que as equipas de adequado e os proprietários da aplicação podem aceder aos respetivos recursos. Dave reconhece que cada equipa tem requisitos diferentes. Ele utiliza os grupos que foram sincronizados da Contoso no local do Active Directory (AD) para o Azure Active Directory e proporciona o nível adequado de acesso para as equipas.

Dave atribui as seguintes funções da subscrição:

| Função | Atribuído a | Descrição |
| --- | --- | --- |
| [Proprietário](../active-directory/role-based-access-built-in-roles.md#owner) |Gerido ID a partir da Contoso AD |Este ID é controlado com apenas acesso de tempo (JIT) através da ferramenta de gestão de identidades da Contoso e assegura que o acesso de proprietário da subscrição é totalmente auditado |
| [Gestor de segurança](../active-directory/role-based-access-built-in-roles.md#security-manager) |Segurança e riscos departamento de gestão |Esta função permite aos utilizadores ver o Centro de segurança do Azure e o estado dos recursos |
| [Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md#network-contributor) |Equipa de rede |Esta função permite a equipa de rede da Contoso gerir a VPN de Site a Site e as redes virtuais |
| *Função personalizada* |Proprietário da aplicação |Dave cria uma função que concede a capacidade de modificar recursos no grupo de recursos. Para obter mais informações, consulte [funções personalizadas no Azure RBAC](../active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Políticas
Dave tem os seguintes requisitos para gerir recursos na subscrição:

* Porque as ferramentas de desenvolvimento suportam os programadores por todo o mundo, ele não pretende bloquear os utilizadores de criação de recursos em qualquer região. No entanto, ele tem de saber onde os recursos são criados.
* Ele é preocupado com os custos. Por conseguinte, ele pretende impedir que os proprietários da aplicação para criar máquinas de virtuais desnecessariamente dispendiosas.  
* Como esta aplicação serve os programadores em várias unidades de negócio, ele pretende etiquetar cada recurso com o proprietário de unidade e aplicações da empresa. Utilizando estas etiquetas, ETS pode faturar-as equipas de adequado.

Ele cria as seguintes [políticas do Azure](../azure-policy/azure-policy-introduction.md):

| Campo | Efeito | Descrição |
| --- | --- | --- |
| localização |Auditoria |A criação dos recursos em qualquer região de auditoria |
| tipo |negar |Negar a criação de máquinas virtuais de série de G |
| etiquetas |negar |Exigir a tag de proprietário da aplicação |
| etiquetas |negar |Exigir a tag de centro de custos |
| etiquetas |Acrescentar |Acrescentar o nome da etiqueta **BusinessUnit** e valor da etiqueta **ETS** a todos os recursos |

### <a name="resource-tags"></a>Sinalizadores de recursos
Dave compreende que tem de ter informações específicas sobre a fatura para identificar o Centro de custos para a implementação de BitBucket. Além disso, Dave pretende saber todos os recursos que ETS é proprietário.

Adiciona as seguintes [etiquetas](resource-group-using-tags.md) aos grupos de recursos e recursos.

| Nome da etiqueta | Valor da etiqueta |
| --- | --- |
| ApplicationOwner |O nome da pessoa que gere esta aplicação |
| CostCenter |O Centro de custos do grupo que é pagar o consumo do Azure |
| BusinessUnit |**ETS** (a unidade de empresas associada à subscrição) |

### <a name="core-network"></a>Rede principal
A Contoso ETS informações de segurança e riscos equipa de gestão revê proposta planeia de Dave mover a aplicação no Azure. Pretende Certifique-se de que a aplicação não está exposta à internet.  Dave também tem aplicações de programadores que no futuro serão movidas para o Azure. Essas aplicações necessitarem de interfaces de públicas.  Para cumprir estes requisitos, ele fornece redes virtuais internas e externas e um grupo de segurança de rede para restringir o acesso.

Ele cria os seguintes recursos:

| Tipo de recurso | Nome | Descrição |
| --- | --- | --- |
| Rede Virtual |interno vnet |Utilizado com a aplicação de BitBucket e está ligado através do ExpressRoute à rede empresarial da Contoso.  Uma sub-rede (`bitbucket`) fornece a aplicação com um espaço de endereço IP específico |
| Rede Virtual |vnet externo |Disponível para as aplicações futuras que necessitam de pontos finais de destinado ao público |
| Grupo de segurança de rede |bitbucket nsg |Garante que a superfície de ataque desta carga de trabalho é minimizada ao permitir ligações apenas na porta 443 para a sub-rede onde a aplicação se encontra (`bitbucket`) |

### <a name="resource-locks"></a>Bloqueios de recursos
Dave reconhece que a conectividade da rede da empresa da Contoso para a rede virtual interna deve ser protegida a partir de qualquer wayward script ou a eliminação acidental.

Ele cria as seguintes [bloqueio de recurso](resource-group-lock-resources.md):

| Tipo de bloqueio | Recurso | Descrição |
| --- | --- | --- |
| **CanNotDelete** |interno vnet |Impede que os utilizadores a eliminar a rede virtual ou sub-redes, mas não impede a adição de novas sub-redes |

### <a name="azure-automation"></a>Automatização do Azure
Dave tem nada para automatizar para esta aplicação. Embora criado uma conta de automatização do Azure, ele inicialmente usaremos-lo.

### <a name="azure-security-center"></a>Centro de Segurança do Azure
Gestão de serviço de TI da Contoso tem de identificar e processar ameaças rapidamente. Também deve compreender que problemas poderão existir.  

Para cumprir estes requisitos, Dave permite o [Centro de segurança do Azure](../security-center/security-center-intro.md)e fornece acesso à função de Gestor de segurança.

## <a name="scenario-2-customer-facing-app"></a>Cenário 2: cliente com acesso à aplicação
A liderança do negócio na unidade de negócio de cadeia de alimentação identificou vários oportunidades para aumentar o envolvimento com clientes da Contoso através da utilização de um cartão loyalty. Equipa de Alice tem de criar esta aplicação e decide Azure aumenta a sua capacidade para satisfazer a necessidade de negócio. Alice funciona com Dave de ETS para configurar as duas subscrições para desenvolver e funcionamento desta aplicação.

### <a name="azure-subscriptions"></a>Subscrições do Azure
Dave inicia sessão no Portal da empresa do Azure e verá que o departamento de cadeia de fornecimento já existe.  No entanto, dado que este projeto é o primeiro projeto de desenvolvimento para a equipa de cadeia de fornecimento no Azure, Dave reconhece a necessidade de uma nova conta para a equipa de desenvolvimento de Alice.  Ele cria a conta "R & D" para a equipa e atribui o acesso a Alice. Alice iniciar sessão em através do portal do Azure e cria duas subscrições: um para conter os servidores de desenvolvimento e outra para reter os servidores de produção.  A Joana segue as normas de nomenclatura anteriormente estabelecidas ao criar as seguintes subscrições:

| Utilização de subscrição | Nome |
| --- | --- |
| Desenvolvimento |Desenvolvimento de ResearchDevelopment LoyaltyCard SupplyChain de contoso |
| Produção |Contoso SupplyChain operações LoyaltyCard produção |

### <a name="policies"></a>Políticas
Dave e Alice discutem a aplicação e identificam que esta aplicação serve apenas os clientes na região Norte American.  Alice e a equipa se planear utilizar o ambiente de serviço de aplicações do Azure e o SQL do Azure para criar a aplicação. Precisam de criar máquinas virtuais durante o desenvolvimento.  Alice pretende garantir que os programadores têm os recursos que precisam para explorar e examinar problemas sem solicitação no ETS.

Para o **subscrição desenvolvimento**, criarem a seguinte política:

| Campo | Efeito | Descrição |
| --- | --- | --- |
| localização |Auditoria |A criação dos recursos em qualquer região de auditoria |

Limita o tipo de sku que um utilizador pode criar de desenvolvimento, e não necessitam de etiquetas para grupos de recursos ou recursos.

Para o **subscrição de produção**, criarem as políticas seguintes:

| Campo | Efeito | Descrição |
| --- | --- | --- |
| localização |negar |Negar a criação de quaisquer recursos fora centros de dados E.U.A. |
| etiquetas |negar |Exigir a tag de proprietário da aplicação |
| etiquetas |negar |Exigir a etiqueta do departamento |
| etiquetas |Acrescentar |Acrescentar a etiqueta para cada grupo de recursos que indica o ambiente de produção |

Limita o tipo de sku que um utilizador pode criar na produção.

### <a name="resource-tags"></a>Sinalizadores de recursos
Dave compreende que tem de ter informações específicas para identificar os grupos de empresas correto de faturação e de propriedade. Define as etiquetas de recursos para grupos de recursos e recursos.

| Nome da etiqueta | Valor da etiqueta |
| --- | --- |
| ApplicationOwner |O nome da pessoa que gere esta aplicação |
| Departamento |O Centro de custos do grupo que é pagar o consumo do Azure |
| EnvironmentType |**Produção** (apesar da subscrição inclui **produção** no nome, incluindo esta etiqueta permite a identificação fácil quando observar a recursos no portal ou na factura) |

### <a name="core-networks"></a>Redes de núcleo
A Contoso ETS informações de segurança e riscos equipa de gestão revê proposta planeia de Dave mover a aplicação no Azure. Pretende garantir que a aplicação Loyalty Card é corretamente isolada e protegida numa rede DMZ.  Para cumprir este requisito, Dave e Alice criam uma rede virtual externa e um grupo de segurança de rede para isolar a aplicação de cartão de Loyalty da rede empresarial Contoso.  

Para o **subscrição desenvolvimento**, criarem:

| Tipo de recurso | Nome | Descrição |
| --- | --- | --- |
| Rede Virtual |interno vnet |Funciona o ambiente de desenvolvimento de Contoso Loyalty cartão e está ligado através do ExpressRoute à rede empresarial da Contoso |

Para o **subscrição de produção**, criarem:

| Tipo de recurso | Nome | Descrição |
| --- | --- | --- |
| Rede Virtual |vnet externo |Aloja a aplicação Loyalty Card e não está ligado diretamente ao ExpressRoute da Contoso. Código é enviado através do seu sistema de código fonte diretamente para os serviços de PaaS |
| Grupo de segurança de rede |loyaltycard nsg |Garante que a superfície de ataque desta carga de trabalho é minimizada permitindo apenas a comunicação no vinculados no TCP 443.  Contoso é também investigar a utilizar uma Firewall de aplicação Web para a proteção adicional |

### <a name="resource-locks"></a>Bloqueios de recursos
Dave e Alice confer e optar por adicionar alguns dos recursos chaves no ambiente para evitar a eliminação acidental durante um push código errant bloqueios de recursos.

Se criarem o bloqueio seguinte:

| Tipo de bloqueio | Recurso | Descrição |
| --- | --- | --- |
| **CanNotDelete** |vnet externo |Para impedir que as pessoas a eliminar a rede virtual ou sub-redes. O bloqueio não impede a adição de novas sub-redes |

### <a name="azure-automation"></a>Automatização do Azure
Alice e a equipa de desenvolvimento tem um vasto conjunto runbooks para gerir o ambiente para esta aplicação. Os runbooks permitem adições/eliminações de nós para a aplicação e outras tarefas de DevOps.

Para utilizar estes runbooks, elas permitem [automatização](../automation/automation-intro.md).

### <a name="azure-security-center"></a>Centro de Segurança do Azure
Gestão de serviço de TI da Contoso tem de identificar e processar ameaças rapidamente. Também deve compreender que problemas poderão existir.  

Para cumprir estes requisitos, Dave permite que o Centro de segurança do Azure. Ele assegura que o Centro de segurança do Azure está a monitorizar os recursos e fornece acesso para as equipas de DevOps e segurança.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como criar modelos do Resource Manager, consulte [melhores práticas para a criação de modelos Azure Resource Manager](resource-manager-template-best-practices.md).
