---
title: "Melhores práticas para as empresas mover para o Azure | Microsoft Docs"
description: "Descreve um andaime que as empresas podem utilizar para garantir um ambiente seguro e fácil de gerir."
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: 8692f37e-4d33-4100-b472-a8da37ce628f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 5950bad397e4b0f08f998ea6756e3c258e84b63e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Andaime enterprise do Azure - governação prescritiva subscrição
As empresas adotem cada nuvem pública para a agilidade e a flexibilidade. Estes são utilizar força da codificação da nuvem para gerar receitas ou otimizar os recursos para as empresas. O Microsoft Azure oferece um em diversos serviços de que as empresas podem assemblar como blocos modulares para resolver um grande número de aplicações e cargas de trabalho. 

No entanto, muitas vezes é difícil saber onde começar. Depois de decidir utilizar o Azure, algumas perguntas surgem normalmente:

* "Como cumprir nosso requisitos legais para soberania de dados em determinados países?"
* "Como posso que garante que alguém não inadvertidamente, alterar um sistema crítico?"
* "Como posso saber que cada recurso é de suporte para que posso pode conta-lo e fatura criar uma com precisão?"

Prospect de uma subscrição vazia com rails sem proteção é uma tarefa intimidante. Este espaço em branco pode hamper a mover para o Azure.

Este artigo fornece um ponto de partida técnicas para profissionais da dar resposta à necessidade de governação e equilibrá-lo com a necessidade de agilidade dessas soluções. Introduz o conceito de uma andaime de enterprise que o orienta às organizações implementar e gerir as suas subscrições do Azure. 

## <a name="need-for-governance"></a>Necessidade de governação
Quando mover para o Azure, tem de resolver o tópico de governação antecipadamente para garantir a utilização da nuvem dentro da empresa com êxito. Infelizmente, a hora e bureaucracy de criação de um sistema de governação abrangente significa alguns grupos de empresas ir diretamente para os fornecedores sem envolver TI empresariais. Esta abordagem pode deixar a empresa abra às vulnerabilidades se os recursos não são corretamente geridos. As características de nuvem pública - agilidade, flexibilidade e baseada no consumo preços - são importantes para grupos de empresas que precisam de rapidamente satisfazer os pedidos de clientes (internos e externos). No entanto, TI empresariais precisa de garantir que os dados e sistemas de forma eficaz estão protegidos.

Vida real, andaime é utilizada para criar a base da estrutura. O andaime orienta a esquema geral e fornece pontos de âncora para sistemas mais permanentes seja montado. Um andaime empresarial é o mesmo: um conjunto de controlos flexíveis e capacidades do Azure que fornecem estrutura para o ambiente e âncoras para serviços em nuvem pública. Fornece os construtores (IT e grupos de empresas) uma base para criar e anexar novos serviços.

O andaime baseia-se nas práticas que iremos ter reunidas a partir de várias ações de envolvimento com clientes de vários tamanhos. Intervalo esses clientes, de pequenas organizações desenvolver soluções na nuvem para Fortune 500 empresas e fornecedores independentes de software que estão a migrar e desenvolver soluções na nuvem. O andaime empresarial é "específico" para ser flexível para suportar cargas de trabalho IT tradicionais e cargas de trabalho seja ágil; tal como os programadores criar aplicações de software-como-um-serviço (SaaS) com base nas capacidades do Azure.

O andaime enterprise destina-se para a base de cada nova subscrição no Azure. Permite que os administradores garantir que as cargas de trabalho de cumprir os requisitos de governação mínima de uma organização sem a impedir que os grupos de empresas e os programadores rapidamente a cumprir os seus próprios objetivos.

> [!IMPORTANT]
> Governação é fundamental para o êxito do Azure. Este artigo destina-se a implementação técnica de um andaime enterprise, mas apenas tocar no amplo processo e as relações entre os componentes. Governação da política flui do nível superior para baixo e é determinada pelo que as empresas e pretende alcançar. Naturalmente, a criação de um modelo de governação para o Azure inclui representantes do departamento de TI, mas o mais importante ainda, deve possuir representação segura de líderes do grupo de negócio e gestão de segurança e riscos. No final, um andaime empresarial é mitigar o risco de negócio para facilitar o missão e os objetivos da organização.
> 
> 

A imagem seguinte descreve os componentes do andaime. A base depende de um plano sólido para departamentos, contas e subscrições. Os pillars consistem de políticas de Gestor de recursos e normas de nomenclatura fortes. O resto do andaime provém de núcleos capacidades do Azure e as funcionalidades que ativar um ambiente seguro e fácil de gerir.

![componentes do andaime](./media/resource-manager-subscription-governance/components.png)

> [!NOTE]
> Azure desenvolveu-se rapidamente desde a introdução 2008. Este crescimento necessário Microsoft, as equipas de engenharia rethink sua abordagem para gerir e implementar serviços. O modelo Azure Resource Manager foi introduzido em 2014 e substitui o modelo de implementação clássica. O Resource Manager permite às organizações implementar mais facilmente, organizar e controlar os recursos do Azure. Gestor de recursos inclui parallelization ao criar recursos para a implementação mais rápida de soluções complexas, interdependentes. Também inclui o controlo de acesso granular e a capacidade de recursos de etiqueta com metadados. A Microsoft recomenda a criação de todos os recursos através do modelo do Resource Manager. O andaime enterprise explicitamente foi concebido para o modelo do Resource Manager.
> 
> 

## <a name="define-your-hierarchy"></a>Definir a sua hierarquia
A base do andaime é a inscrição Enterprise do Azure (e o Portal da empresa). A inscrição enterprise define a forma e utilizar serviços do Azure dentro de uma empresa e é a estrutura de governação core. Dentro do enterprise agreement, os clientes conseguem subdividir ainda mais o ambiente para departamentos, contas e, por fim, subscrições. Uma subscrição do Azure é a unidade básica onde estão incluídos todos os recursos. Também define os limites de vários no Azure, tais como o número de núcleos, recursos, etc.

![hierarquia](./media/resource-manager-subscription-governance/agreement.png)

Cada empresa é diferente e a hierarquia na imagem anterior permite uma flexibilidade significativa na forma como o Azure está organizado dentro da empresa. Antes de implementar as orientações incluídas neste documento, deve a hierarquia de modelo e compreender o impacto sobre faturação, acesso a recursos e complexidade.

Os três padrões comuns para inscrições através do Azure são:

* O **funcional** padrão
  
    ![funcional](./media/resource-manager-subscription-governance/functional.png)
* O **unidade de negócio** padrão 
  
    ![empresa](./media/resource-manager-subscription-governance/business.png)
* O **geográfica** padrão
  
    ![geográfica](./media/resource-manager-subscription-governance/geographic.png)

Aplicar andaime ao nível da subscrição para expandir a governação requisitos da empresa para a subscrição.

## <a name="naming-standards"></a>Normas de nomenclatura
O primeiro pilar do andaime é nomenclatura normas. As normas de nomenclatura devidamente concebidas permitem-lhe identificar recursos no portal, uma fatura e scripts. Provavelmente, já ter normas de nomenclatura para a infraestrutura no local. Ao adicionar o Azure ao seu ambiente, deve expandir essas normas de nomenclatura para os recursos do Azure. Padrão de nomenclatura facilitar a gestão mais eficiente de ambiente de todos os níveis.

> [!TIP]
> Para as convenções de nomenclatura:
> * Reveja e que adotar sempre que possível a [orientações de padrões e práticas](../guidance/guidance-naming-conventions.md). Esta orientação ajuda-o a decidir sobre uma norma de nomenclatura significativa.
> * Utilize camelCasing para nomes de recursos (como myResourceGroup e vnetNetworkName). Nota: Existem determinados recursos, tais como contas do storage, onde a única opção consiste em utilizar minúsculas (e não existem outros carateres especiais).
> * Considere a utilização de políticas do Azure Resource Manager (descritas na secção seguinte) para impor as normas de nomenclatura.
> 
> As sugestões anteriores ajudam a implementar uma convenção de nomenclatura consistente.

## <a name="policies-and-auditing"></a>As políticas e de auditoria
O segundo pilar do andaime envolve criar [políticas do Azure](../azure-policy/azure-policy-introduction.md) e [auditoria no registo de atividade](resource-group-audit.md). As políticas do Resource Manager fornecem-lhe a capacidade de gerir o risco no Azure. Pode definir políticas que garantem soberania de dados ao restringir, impor ou auditoria determinadas ações. 

* A política é uma predefinição **permitir** sistema. Controlar as ações por definição e a atribuição de políticas para recursos que negarem ou ações em recursos de auditoria.
* As políticas são descritas por definições de política na linguagem de definição de política (if-a em seguida, em seguida condições).
* Criar políticas com JSON (Javascript Object Notation) ficheiros formatados. Depois de definir uma política, pode atribuí-la a um âmbito específico: subscrição, o grupo de recursos ou o recurso.

Políticas tem várias ações que permitem uma abordagem de detalhado para os cenários. As ações são:

* **Negar**: bloqueia o pedido de recurso
* **Auditoria**: permite que o pedido, mas adiciona uma linha para o registo de atividade (que pode ser utilizado para fornecer alertas ou para acionar runbooks)
* **Acrescentar**: adiciona informações especificada para o recurso. Por exemplo, se não existir uma etiqueta de "CostCenter" num recurso, adicione essa tag com um valor predefinido.

### <a name="common-uses-of-resource-manager-policies"></a>Utilizações comuns das políticas do Gestor de recursos
Políticas de Gestor de recursos do Azure são uma ferramenta poderosa no toolkit do Azure. Estas permitem-lhe evitar custos inesperados, para identificar um centro de custos para recursos através de etiquetagem e certifique-se de que são cumpridos os requisitos de conformidade era. Quando as políticas são combinadas com as funcionalidades de auditorias incorporadas, pode fashion soluções flexíveis e complexas. As políticas permitem empresas para fornecerem controlos para cargas de trabalho "TI tradicionais" e "Agile" cargas de trabalho; tal como desenvolver aplicações de cliente. Os padrões mais comuns, vemos para políticas são:

* **Soberania de conformidade/dados de Georreplicação** -Azure oferece regiões pelo mundo. As empresas, muitas vezes, pretendem controlar onde os recursos são criados (se a assegurar soberania de dados ou apenas para se certificar de recursos são criados perto os consumidores de fim dos recursos).
* **A gestão de custos** -subscrição do Azure um pode conter recursos de vários tipos e escala. As empresas têm, muitas vezes, pretendem Certifique-se de que subscrições padrão evitar a utilização de recursos desnecessariamente grandes, o que podem custo centenas de utilizados no compromisso um mês ou mais.
* **Predefinição governação através de etiquetas necessárias** -necessidade de etiquetas é uma das funcionalidades mais comuns e altamente pretendidas. As empresas através de políticas do Azure Resource Manager são capazes para se certificar de que um recurso é adequadamente etiquetado. As etiquetas mais comuns são: tipo de departamento, o proprietário do recurso e o ambiente (por exemplo - produção, teste, desenvolvimento)

**Exemplos**

"Tradicional IT" subscrição para aplicações de linha de negócio

* Impor departamento e proprietário etiquetas em todos os recursos
* Restringir a criação de recursos para a região americano norte
* Restringir a capacidade de criar G série VMs e Clusters do HDInsight

Ambiente "Seja ágil" para uma unidade de negócio a criação de aplicações em nuvem

* Para cumprir os requisitos de soberania dos dados, permitir a criação de recursos apenas numa região específica.
* Impor a etiqueta de ambiente em todos os recursos. Se um recurso é criado sem uma etiqueta, acrescentar o **ambiente: desconhecido** etiquetas ao recurso.
* Auditoria quando os recursos são criados fora da América do Norte, mas não impedir.
* Auditoria ao custo de alta recursos é criados.

> [!TIP]
> A utilização de políticas de Gestor de recursos entre organizações mais comuns é controlo *onde* recursos podem ser criados e *que* tipos de recursos podem ser criados. Além de proporcionarem controlos no *onde* e *que*, muitas empresas utilizam políticas para se certificar de recursos possui os metadados adequados ao faturar-novamente para consumo. É recomendável aplicar políticas ao nível da subscrição para:
> 
> * Soberania de conformidade/dados de Georreplicação
> * Gestão de custos
> * Etiquetas necessárias (Determined por necessidade empresarial, tais como BillTo, o proprietário da aplicação)
> 
> Pode aplicar políticas adicionais em níveis inferiores de âmbito.
> 
> 

### <a name="audit---what-happened"></a>Auditoria - o que aconteceu?
Para ver como o ambiente está a funcionar, tem de efetuar a auditoria da atividade do utilizador. A maioria dos tipos de recursos no Azure criar registos de diagnóstico que pode analisar através de uma ferramenta de registo ou no Azure Operations Management Suite. Pode recolher registos de atividade entre várias subscrições para fornecer um departamentais ou vista enterprise. Os registos de auditoria são uma ferramenta de diagnóstico importante e um mecanismo fundamental para acionar eventos no ambiente do Azure.

Os registos de atividade de implementações do Resource Manager permitem-lhe determinar o **operações** que demorou local e de quem efetuou-los. Registos de atividade podem ser recolhidos e agregadas ferramentas como a análise de registos.

## <a name="resource-tags"></a>Sinalizadores de recursos
Como os utilizadores na sua organização adicionam recursos à subscrição, torna-se cada vez mais importante associar a recursos com o departamento apropriado, o cliente e o ambiente. Pode anexar os metadados a recursos através do [etiquetas](resource-group-using-tags.md). Utilize etiquetas para fornecer informações sobre o recurso ou o proprietário. As etiquetas permitem não só agregar e grupo de recursos de várias formas, mas utilizar esses dados para fins de estorno. Pode Etiquetar recursos com até 15 chave: pares de valores. 

Os sinalizadores de recursos são flexíveis e devem estar anexados a maioria dos recursos. Exemplos de etiquetas de recursos comuns são:

* BillTo
* Departamento (ou unidade de negócio)
* Ambiente (programação de produção, fase)
* Camada (camada Web, a camada da aplicação)
* Proprietário da aplicação
* ProjectName

![etiquetas](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Para obter mais exemplos das etiquetas, consulte [recomendado convenções de nomenclatura para recursos do Azure](../guidance/guidance-naming-conventions.md).

> [!TIP]
> Considere efetuar uma política que exige deste etiquetagem para:
> 
> * Grupos de recursos
> * Armazenamento
> * Virtual Machines
> * Servidores de ambientes de serviço/web de aplicação
> 
> Esta estratégia etiquetagem identifica nas suas subscrições, é necessário que os metadados para o negócio, financeiro, segurança, gestão de riscos e gestão geral do ambiente. 

## <a name="resource-group"></a>Grupo de recursos
Gestor de recursos permite-lhe colocar recursos em grupos significativos para a afinidade de faturação ou natural de gestão. Conforme mencionado anteriormente, o Azure tem dois modelos de implementação. No modelo de clássico anterior, a unidade básica de gestão foi a subscrição. Foi difícil dividir recursos numa subscrição, que levou a criação de grandes quantidades de subscrições. Com o modelo do Resource Manager, que vimos a introdução de grupos de recursos. Grupos de recursos são contentores de recursos que têm um ciclo de vida comuns ou partilham um atributo, tais como "todos os servidores SQL" ou "A aplicação".

Grupos de recursos não podem ser incluídos em si e recursos só podem pertencer a um grupo de recursos. Pode aplicar determinadas ações em todos os recursos num grupo de recursos. Por exemplo, a eliminação de um grupo de recursos remove todos os recursos no grupo de recursos. Normalmente, colocar uma aplicação completa ou sistema relacionado no mesmo grupo de recursos. Por exemplo, uma aplicação de três camadas denominada Contoso Web aplicação iria conter o servidor web, o servidor de aplicações e o SQL server no mesmo grupo de recursos.

> [!TIP]
> Como organizar os seus grupos de recursos podem variar de cargas de trabalho "TI tradicionais" para cargas de trabalho "TI seja ágil":
> 
> * "Tradicional IT" cargas de trabalho maior frequência estão agrupadas por itens de dentro do mesmo ciclo de vida, como uma aplicação. Permite a gestão de aplicações individuais agrupamento por aplicação.
> * "Seja ágil IT" cargas de trabalho têm tendência para se focarem em aplicações externas orientado para o cliente em nuvem. Os grupos de recursos devem refletir as camadas de implementação (por exemplo, a camada Web, a camada de aplicação) e gestão.
> 
> Compreender a sua carga de trabalho ajuda-o a desenvolver uma estratégia de grupo de recursos.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções
É, provavelmente, pedimos sozinho "que devem ter acesso aos recursos?" e "como controlar este acesso?" Permitir ou desautorizar acesso ao portal do Azure e controlar o acesso aos recursos no portal é fundamental. 

Quando o Azure foi lançado inicialmente, os controlos de acesso a uma subscrição foram básicos: administrador ou Coadministrador. Aceder a uma subscrição no acesso de modelo implícito clássico para todos os recursos no portal. Esta falta de um controlo detalhado gerou a proliferação de subscrições para fornecer um nível de controlo de acesso razoável para uma inscrição do Azure.

Este proliferação de subscrições já não é necessária. Com o controlo de acesso baseado em funções, pode atribuir utilizadores a funções padrão (por exemplo, "leitor" e "escritor" tipos comuns de funções). Também pode definir funções personalizadas.

> [!TIP]
> Para implementar o controlo de acesso baseado em funções:
> * Ligar o seu arquivo de identidade empresarial (normalmente Active Directory) para o Azure Active Directory utilizando a ferramenta AD Connect.
> * Controlo do administrador/Coadministrador da subscrição utilizando uma identidade gerida. **Não** atribuir administrador/coadministrador para um novo proprietário da subscrição. Em alternativa, utilize funções do RBAC para fornecer **proprietário** direitos para um grupo ou individuais.
> * Adicione utilizadores do Azure a um grupo (por exemplo, X os proprietários da aplicação) no Active Directory. Utilize o grupo sincronizado para fornecer os direitos adequados para gerir o grupo de recursos que contém a aplicação de membros do grupo.
> * Siga o princípio de conceder a **menor privilégio** necessários para realizar o trabalho esperado. Por exemplo:
>   * Grupo de implementação: Um grupo que só é possível implementar recursos.
>   * : Máquina virtual um grupo de gestão Que tenha capacidade para reiniciar as VMs (para operações de)
> 
> Estas sugestões ajudam a gerir o acesso de utilizador na sua subscrição.

## <a name="azure-resource-locks"></a>Bloqueios de recursos do Azure
Como a sua organização adiciona serviços principais para a subscrição, torna-se cada vez mais importante certificar-se de que esses serviços estão disponíveis para evitar a interrupção de negócios. [Bloqueios de recurso](resource-group-lock-resources.md) permitem-lhe restringir operações nos recursos de elevado valor onde modificar ou eliminá-los teria um impacto significativo na sua aplicações ou a infraestrutura de nuvem. Pode aplicar bloqueios para uma subscrição, o grupo de recursos ou o recurso. Normalmente, aplicar bloqueios dos recursos, como redes virtuais, gateways e as contas de armazenamento. 

Bloqueios de recursos atualmente suportam dois valores: CanNotDelete e só de leitura. CanNotDelete significa que os utilizadores (com os direitos adequados) podem ainda ler ou modificar um recurso, mas não é possível eliminá-lo. Só de leitura significa que os utilizadores autorizados não é possível eliminar ou modificar um recurso.

Para criar ou eliminar as bloqueios de gestão, tem de ter acesso a `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` ações.
Das funções incorporadas, apenas o proprietário e o administrador de acesso de utilizador são concedidas essas ações.

> [!TIP]
> Opções de rede principal devem ser protegidas com bloqueios. A eliminação acidental de um gateway, VPN site a site seria desastroso para uma subscrição do Azure. Azure não lhe permitem eliminar uma rede virtual que está a ser utilizado, mas aplicar restrições mais é útil precaução. 
> 
> * Rede virtual: CanNotDelete
> * Grupo de segurança de rede: CanNotDelete
> * Políticas: CanNotDelete
> 
> As políticas também são fundamentais para a manutenção dos controlos adequados. Recomendamos que aplique uma **CanNotDelete** bloqueio para as políticas que estão em utilização.

## <a name="core-networking-resources"></a>Recursos de rede principal
Pode ser o acesso aos recursos externos (através da internet) ou interno (na rede da empresa). É fácil para os utilizadores na sua organização inadvertidamente colocar recursos a errado lugar para cima e, potencialmente, abra-los para acesso malicioso. Tal como acontece com dispositivos no local, as empresas tem de adicionar controlos adequados para garantir que os utilizadores do Azure tomar decisões corretas. Para governação de subscrição, podemos identificar recursos principais do que permitem controlar básica de acesso. Os recursos de núcleos é composto por:

* **Redes virtuais** são objetos de contentor para sub-redes. Apesar de não estritamente necessários, muitas vezes, é utilizado ao estabelecer ligação a aplicações a recursos empresariais internos.
* **Grupos de segurança de rede** são semelhantes de uma firewall e fornecer as regras para como um recurso pode "conversar"com através da rede. Fornecem um controlo granular sobre como / se uma sub-rede (ou a máquina virtual) pode ligar à Internet ou outras sub-redes na mesma rede virtual.

![componentes essenciais de rede](./media/resource-manager-subscription-governance/core-network.png)

> [!TIP]
> Funcionamento em rede:
> * Crie redes virtuais dedicados ao acesso externo cargas de trabalho e cargas de trabalho interno com acesso à. Esta abordagem reduz a possibilidade de inadvertidamente colocar as máquinas virtuais que foram concebidas para cargas de trabalho internas num espaço com acesso externo.
> * Configure grupos de segurança de rede para limitar o acesso. No mínimo, bloquear o acesso à internet a partir das redes virtuais internas e bloquear o acesso à rede empresarial a partir de redes virtuais externas.
> 
> Estas sugestões ajudam a implementar recursos de funcionamento em rede seguros.

### <a name="automation"></a>Automatização
Gerir recursos individualmente é uma operação morosa e potencialmente suscetível para determinadas operações de erro. O Azure oferece várias capacidades de automatização, incluindo a automatização do Azure, as Logic Apps e as funções do Azure. [A automatização do Azure](../automation/automation-intro.md) permite aos administradores criar e definir os runbooks para processar tarefas comuns na gestão de recursos. Criar runbooks utilizando um editor de código do PowerShell ou um editor gráfico. Pode produzir fluxos de trabalho de fase multi complexos. A automatização do Azure é frequentemente utilizada para processar tarefas comuns, tais como os recursos a ser encerrado ou a criação de recursos em resposta a um acionador específico sem necessitar de intervenção do homem.

> [!TIP]
> Para automatização:
> * Criar uma conta de automatização do Azure e reveja os runbooks disponíveis (linha de comando e gráfica) disponíveis no [Galeria de Runbooks](../automation/automation-runbook-gallery.md).
> * Importar e personalizar runbooks chaves para utilização própria.
> 
> Um cenário comum é a capacidade de início/encerrar máquinas virtuais com base numa agenda. Existem runbooks de exemplo que estão disponíveis na galeria que processar este cenário e lhe ensinar como expandi-lo.
> 
> 

## <a name="azure-security-center"></a>Centro de Segurança do Azure
Talvez um os bloqueadores maiores na nuvem adoção foi preocupações através da segurança. Gestores de riscos de TI e departamentos de segurança tem de garantir que os recursos no Azure são seguros. 

O [Centro de segurança do Azure](../security-center/security-center-intro.md) fornece uma vista do Estado de segurança dos recursos nas subscrições central e fornece recomendações que ajudam a impedir recursos comprometidos. Pode ativar a políticas mais granulares (por exemplo, aplicar políticas de grupos de recursos específicos que permitem à empresa personalizar a sua postura ao risco que são endereçamento). Por fim, o Centro de segurança do Azure é uma plataforma aberta que permite que parceiros da Microsoft e fornecedores independentes de software criar software plugs no Centro de segurança do Azure para melhorar as respetivas capacidades. 

> [!TIP]
> Centro de segurança do Azure está ativado por predefinição em cada subscrição. No entanto, tem de ativar a recolha de dados provenientes de máquinas virtuais para permitir que o Centro de segurança do Azure instalar o agente e começar a recolha de dados.
> 
> ![Recolha de dados](./media/resource-manager-subscription-governance/data-collection.png)
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Agora que aprendeu sobre governação de subscrição, está na altura para ver estas recomendações na prática. Consulte [exemplos para implementar a governação de subscrição do Azure](resource-manager-subscription-examples.md).

