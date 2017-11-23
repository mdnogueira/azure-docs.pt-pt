---
title: "Governação no Azure | Microsoft Docs"
description: "Saiba mais sobre serviços informáticos baseada na nuvem que incluem uma seleção grande de instâncias de computação e serviços que podem ser dimensionados acima e abaixo automaticamente para satisfazer as necessidades da sua aplicação ou a empresa."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 03e86448a1b0a13addab789bf2aea62e5d84149b
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="governance-in-azure"></a>Governação no Azure

Sabemos que a segurança está tarefa um na nuvem e como importante é que encontrará exatas e atempadas informações sobre a segurança do Azure. Uma das razões melhor a utilizar o Azure para as suas aplicações e serviços é tirar partido do respetivo grade diversidade de capacidades e ferramentas de segurança. Estas ferramentas e capacidades ajudam possibilitam a criar soluções seguras na plataforma do Azure segura.

Para o ajudar a compreender melhor a matriz de governação controlos implementados no Microsoft Azure de perspetivas do cliente e do Microsoft operations neste artigo, "Governação no Azure", é escrito para fornecer um abrangente de olhos a governação funcionalidades disponíveis com o Microsoft Azure.

## <a name="azure-platform"></a>Plataforma Azure

Azure é uma plataforma de serviço de nuvem pública que suporta uma seleção abrangente dos sistemas operativos, programação idiomas, estruturas, ferramentas, bases de dados e dispositivos. Pode ser executado contentores de Linux com a integração de Dockers; criar aplicações com JavaScript, Python, .NET, PHP, Java e Node.js; compilação back-ends para iOS, Android e Windows dispositivos. Serviços em nuvem pública do Azure suportam as mesmas tecnologias milhões de programadores e profissionais de TI já dependem e de confiança.

Quando criar ou migrar os recursos IT à, um fornecedor de serviços de nuvem pública são depender capacidades da organização para proteger as suas aplicações e dados com os serviços e os controlos fornecem para gerir a segurança dos seus recursos baseados na nuvem.

Infraestrutura do Azure foi concebida de instalação de aplicações para o alojamento de milhões de clientes em simultâneo, e fornece uma base de confiança no qual as empresas podem cumprir os seus requisitos de segurança. Além disso, Azure fornece muitas opções de segurança e a capacidade de controlá-las, de modo a que possa personalizar a segurança para satisfazer os requisitos exclusivos de implementações da sua organização.

Este documento irá ajudá-lo a compreender como capacidades de governação do Azure o podem ajudar a satisfazer estes requisitos.

## <a name="abstract"></a>Abstrato

Governação de nuvem do Microsoft Azure fornece uma auditoria integrada e uma abordagem de consultoria para rever e indicar organizações na respetiva utilização de plataforma do Azure. Governação de nuvem do Microsoft Azure refere-se a processos de tomada de decisão, os critérios e políticas envolvidos no planeamento, arquitetura, a aquisição, a implementação, a operação e a gestão de uma nuvem de computação.

Para criar um plano de governação de nuvem do Microsoft Azure, terá de tomar uma visão detalhada das pessoas, processos e as tecnologias atualmente no local e, em seguida, criar estruturas que tornam mais fácil para que as TI consistentemente suportar necessidades ao fornecer aos utilizadores finais com a flexibilidade para utilizar as funcionalidades de elevado desempenho do Microsoft Azure.

Este documento descreve como pode alcançar um nível elevado de governação dos seus recursos de TI no Microsoft Azure. Este documento pode ajudá-lo a compreender as funcionalidades de segurança e governação incorporadas no Microsoft Azure.

Seguem-se main os problemas de governação abordados neste documento:

- Implementação de políticas, processos e procedimentos de acordo com os objetivos da organização.

- Segurança e compatibilidade contínuas com as normas da organização

- Monitorização e alertas

## <a name="implementation-of-policies-processes-and-procedures"></a>Implementação de políticas, processos e procedimentos 

Gestão estabelecida funções e responsabilidades para supervisionam implementação da política de segurança de informações e continuidade operacional em todo o Azure. Gestão do Microsoft Azure é responsável por supervisionar e práticas de continuidade dentro do respetivo respetivas equipas (incluindo terceiros) e mesmo conformidade com as políticas de segurança, processos e normas de segurança.

Seguem-se os fatores que evoluiu e deu lugar:

- Aprovisionamento de Contas

- Controlos de subscrição

- Controlos de acesso com base de função

- Gestão de recursos

- Controlo de recursos

- Controlo de recursos críticos

- Acesso a API para informações de faturação

- Controlos de rede

## <a name="account-provisioning"></a>Aprovisionamento de contas

Definir a hierarquia de conta é um passo principais para utilizar e estrutura do Azure dentro de uma empresa de serviços e é a estrutura de governação core. Em caso de clientes com o contrato enterprise, os clientes mais podem subdividir o ambiente para departamentos, contas e, por fim, subscrições.

![Aprovisionamento de Contas](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Se não tiver um enterprise agreement, considere utilizar [etiquetas do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) ao nível da subscrição para definir a hierarquia. Uma subscrição do Azure é a unidade básica onde estão incluídos todos os recursos. Também define os limites de vários no Azure, tais como o número de núcleos, recursos, etc. Subscrições podem conter [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), que pode conter recursos. [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) princípios de aplicam a essas três níveis.

Cada empresa é diferente e hierarquia utilizando as etiquetas do Azure em caso dos clientes empresariais não permite uma flexibilidade significativa na forma como o Azure está organizado dentro da empresa. Antes de implementar recursos no Microsoft Azure, deve modelo hierarquia e compreender o impacto sobre faturação, acesso a recursos e complexidade.

## <a name="subscription-controls"></a>Controlos de subscrição

Subscrição controla como a utilização de recursos é comunicada e cobrada. Subscrições podem ser configurado para separado de faturação e pagamento. Como mencionada conta do Azure com um anterior, pode ter várias subscrições. Subscrições podem ser utilizadas para determinar a utilização de recursos do Azure de vários departamentos numa empresa.

Por exemplo, se tiver de uma empresa departamento de TI HR e Marketing departamentos e estes departamentos têm diferentes projetos em execução. Com base na utilização de recursos do Azure como máquinas virtuais por cada departamento, podem ser-lhe faturadas em conformidade. Por este, pode controlar as finanças de cada departamento.

As subscrições do Azure estabelecer três parâmetros:

- um ID exclusivo de subscritor

- uma localização de faturação

- Conjunto de recursos disponíveis

Para um indivíduo, que inclui um ID da conta Microsoft, um número de cartão de crédito e o conjunto completo de serviços do Azure – embora, Microsoft impõe limites de consumo, dependendo do tipo de subscrição.

Hierarquias de inscrição do Azure definem como os serviços estão estruturados dentro de um contrato Enterprise. O Portal da empresa permite aos clientes dividir o acesso aos recursos do Azure associada com um Enterprise Agreement, com base nas hierarquias flexíveis personalizáveis para necessidades exclusivas da organização. O padrão de hierarquia deve corresponder à gestão e a estrutura geográfica da organização para que o acesso de faturação e de recursos associado pode estar com precisão tidas em conta.

Os três padrões de alto nível são unidade funcional, empresariais e departamentos geográficos, utilizando como uma construção administrativa para agrupamentos de conta. Dentro de cada departamento, as contas podem ser atribuídas subscrições, que crie silos de faturação e limites de chaves várias no Azure (por exemplo, o número de VMs, contas de armazenamento, etc.).

![Controlos de subscrição](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Para organizações com um Enterprise Agreement, as subscrições do Azure, siga uma hierarquia de nível de quatro:

- administrador de inscrição Enterprise

- administrador do departamento

- proprietário da conta

- Administrador de serviços

Esta hierarquia é regida pelas seguintes:

- Relação de faturação

- Administração de conta

- Função de acesso baseado em controlo (RBAC) para artefactos

- Limites/limites

- Limites

  - Utilização e faturação (com base em números de oferta cartão de taxas)

  - Limites

  - Rede Virtual

- Ligado ao 1 AAD (1 AAD estar associada a várias subscrições)

- Associado a uma conta de inscrição enterprise

## <a name="role-based-access-controls"></a>Controlos de acesso baseado em funções

Quando o Azure foi lançado inicialmente, os controlos de acesso a uma subscrição foram básicos: administrador ou Coadministrador. Aceder a uma subscrição no acesso de modelo implícito clássico para todos os recursos no portal. Esta falta de um controlo detalhado gerou a proliferação de subscrições para fornecer um nível de controlo de acesso razoável para uma inscrição do Azure.

![Controlos de acesso baseado em funções](./media/governance-in-azure/security-governance-in-azure-fig3.png)

Este proliferação de subscrições já não é necessária. Com o controlo de acesso baseado em funções, pode atribuir utilizadores a funções padrão (por exemplo, "leitor" e "escritor" tipos comuns de funções). Também pode definir funções personalizadas.

[Azure baseada em funções controlo de acesso (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) permite uma gestão pormenorizada de acesso para o Azure. Utilizando o RBAC, pode conceder apenas a quantidade de acesso precisa aos utilizadores para realizar os trabalhos. Segurança e orientado para empresas devem focar-se em fornecer aos funcionários as permissões exatas que precisam. Demasiados permissões expõem uma conta para os atacantes. Permissões insuficientes significam que os funcionários não é possível obter o trabalho feito de forma eficiente. Azure baseada em funções controlo de acesso (RBAC) ajuda a resolver este problema, oferecendo gestão de acesso detalhada para o Azure. RBAC Ajuda-o a segregar funções na sua equipa e conceder apenas a quantidade de acesso aos utilizadores que precisam para desempenhar as suas funções. Em vez de dar everybody sem restrições permissões na sua subscrição do Azure ou recursos, pode permitir que apenas determinadas ações.

Por exemplo, utilize o RBAC para permitir que um empregado gerir máquinas virtuais numa subscrição, enquanto outro pode gerir bases de dados SQL dentro da mesma subscrição.

RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recursos:

- **Proprietário** tem acesso total a todos os recursos, incluindo o direito para delegar o acesso a outras pessoas.

- **Contribuidor** pode criar e gerir todos os tipos de recursos do Azure, mas não é possível conceder acesso a outras pessoas.

- **Leitor** pode ver os recursos do Azure existentes.

O resto das funções do RBAC do Azure permite a gestão de recursos do Azure específicos. Por exemplo, a função de contribuinte de Máquina Virtual permite ao utilizador criar e gerir máquinas virtuais. Se não atribuir-lhes acesso para a rede virtual ou a sub-rede que a máquina virtual estabelece ligação ao.

[Funções incorporadas do RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) lista as funções disponíveis no Azure. Especifica as operações e o âmbito de cada função incorporada concede a utilizadores.

Conceder acesso ao atribuir a função RBAC adequada aos utilizadores, grupos e aplicações num determinado âmbito. O âmbito de uma atribuição de função pode ser uma subscrição, um grupo de recursos ou um único recurso. Uma função atribuída a um âmbito principal também concede acesso a subordinados contidos.

Por exemplo, um utilizador com acesso a um grupo de recursos pode gerir todos os recursos que contém, como Web sites, sub-redes e máquinas virtuais.

RBAC do Azure suporta apenas operações de gestão de recursos do Azure no portal do Azure e APIs do Azure Resource Manager. Não é possível autorizar o todas as operações de nível de dados para recursos do Azure. Por exemplo, podem autorizar alguém para gerir contas de armazenamento, mas não para o blobs ou tabelas dentro de uma conta de armazenamento não é possível. Da mesma forma, uma base de dados do SQL Server pode ser gerido, mas não as tabelas dentro da mesma.

Se quiser saber mais sobre como o RBAC pode ajudá-lo a gerir o acesso, consulte o artigo [O que é o Controlo de Acesso Baseado em Funções](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).

Também pode [criar uma função personalizada](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) tem de se nenhuma das funções incorporadas cumprir específica de acesso de controlo de acesso em funções do Azure (RBAC). Funções personalizadas podem ser criadas utilizando [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), [Interface de linha de comandos do Azure (CLI)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli)e o [REST API](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Tal como funções incorporadas, funções personalizadas podem ser atribuídas a utilizadores, grupos e aplicações na subscrição, o grupo de recursos e âmbitos de recursos.

Pode conceder até duas mil atribuições de funções em cada subscrição.

## <a name="resource-management"></a>Gestão de Recursos

O modelo de implementação clássica fornecido originalmente pelo Azure. Neste modelo, cada recurso existia independentemente; não ocorreu nenhuma forma de agrupar os recursos relacionados. Em vez disso, era necessário manualmente controlar quais os recursos que efetuou a sua solução ou uma aplicação e não se esqueça de geri-los numa abordagem coordenada.

Para implementar uma solução, era necessário que criar cada recurso individualmente através do portal do Azure ou criar um script que implementados todos os recursos na ordem correta. Para eliminar uma solução, era necessário eliminar cada recurso individualmente. Facilmente não foi possível aplicar e atualizar as políticas de controlo de acesso para recursos relacionados. Por fim, não é possível aplicar etiquetas a recursos a etiqueta-los com os termos que o ajudam a monitorizar os recursos e gerir a faturação.

Em 2014, o Azure introduzida qual adicionou o conceito de um grupo de recursos do Resource Manager. Um grupo de recursos é um contentor para os recursos que partilham um ciclo de vida comuns. O modelo de implementação Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os serviços para a sua solução como um grupo, em vez de processamento estes serviços individualmente.

- Pode implementar a solução durante todo o ciclo de vida repetidamente e tem confiança em como os recursos são implementados num estado consistente.

- Pode aplicar o controlo de acesso a todos os recursos no seu grupo de recursos e as políticas são aplicadas automaticamente quando são adicionados novos recursos ao grupo de recursos.

- Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.

- Pode utilizar o JavaScript Object Notation (JSON) para definir a infraestrutura para a sua solução. O ficheiro JSON é conhecido como um modelo do Resource Manager.

- Pode definir as dependências entre os recursos, de modo a que sejam implementados na ordem correta.

![Gestão de recursos](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Gestor de recursos permite-lhe colocar recursos em grupos significativos para a afinidade de faturação ou natural de gestão. Conforme mencionado anteriormente, o Azure tem dois modelos de implementação. O anteriores no [modelo clássico](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model), a unidade básica de gestão foi a subscrição. Foi difícil dividir recursos numa subscrição, que levou a criação de grandes quantidades de subscrições. Com o modelo do Resource Manager, que vimos a introdução de grupos de recursos.

Um grupo de recursos é um contentor que retém recursos relacionados para uma solução do Azure. [O grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) podem incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.

Para obter recomendações sobre modelos, veja o artigo [Melhores práticas para criar modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

O Azure Resource Manager analisa as dependências para garantir que os recursos são criados pela ordem correta. Se um recurso depende de um valor de outro recurso (como uma máquina virtual necessita de uma conta de armazenamento para discos), pode definir uma dependência.

>[!Note]
>Para obter mais informações, consulte [Definir dependências nos modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies).

Também pode utilizar o modelo para atualizar a infraestrutura. Por exemplo, pode adicionar um recurso à solução e adicionar regras de configuração para os recursos que já estão implementados. Se o modelo especificar a criação de um novo mas esse recurso já existir, o Azure Resource Manager efetua uma atualização em vez de criar um novo recurso. O Azure Resource Manager atualiza o recurso existente para o mesmo estado que teria como novo.

O Resource Manager fornece extensões para cenários quando necessitar de operações adicionais como a instalação de software que não está incluído na configuração.

## <a name="resource-tracking"></a>Controlo de recursos

Como os utilizadores na sua organização adicionam recursos à subscrição, torna-se cada vez mais importante associar a recursos com o departamento apropriado, o cliente e o ambiente. Pode anexar os metadados a recursos através de etiquetas. Utilizar [etiquetas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) para fornecer informações sobre o recurso ou o proprietário. As etiquetas permitem não só agregar e grupo de recursos de várias formas, mas utilizar esses dados para fins de estorno.

Utilize etiquetas quando tem uma coleção complexa de grupos de recursos e recursos e precisa de visualizar esses elementos da forma mais adequada para si. Por exemplo, pode etiquetar recursos que desempenham uma função semelhante na sua organização ou pertencem ao mesmo departamento.

Sem etiquetas, os utilizadores da organização podem criar vários recursos que poderá ser difícil identificar e gerir mais tarde. Por exemplo, pode pretender eliminar todos os recursos para um projeto. Se esses recursos não são etiquetados para o projeto, tem de encontrá-los manualmente. A etiquetagem pode ser um meio importante para reduzir os custos desnecessários associados à sua subscrição.

Os recursos não precisam de residir no mesmo grupo de recursos para partilhar uma etiqueta. Pode criar a sua própria taxonomia de etiquetas para se certificar de que todos os utilizadores da sua organização utilizam etiquetas comuns em vez de aplicarem inadvertidamente etiquetas ligeiramente diferentes (por exemplo, “depart” em vez de “departamento”).

As políticas de recursos permitem-lhe criar regras padrão para a sua organização. Pode criar políticas que se certifique-se de recursos são etiquetados com os valores adequados.

> [!Note]
> Para obter mais informações, consulte [iniciativa de política de etiquetas de faturação](../azure-policy/scripts/billing-tags-policy-init.md).

Também pode ver recursos com etiquetas através do Portal do Azure.

O [relatório de utilização](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) para a sua subscrição inclui nomes de etiquetas e valores, o que lhe permite dividir os custos por etiquetas.

> [!Note]
> Para obter mais informações sobre etiquetas, consulte [iniciativa de política de etiquetas de faturação](../azure-policy/scripts/billing-tags-policy-init.md).

As seguintes limitações aplicam-se às etiquetas:

- Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de chave/valor de etiqueta. Esta limitação aplica-se apenas a etiquetas diretamente aplicadas ao recurso ou grupo de recursos. Um grupo de recursos pode conter muitos recursos com 15 pares de chave/valor de etiqueta.

- O nome da etiqueta está limitado a 512 carateres.

- O valor da etiqueta está limitado a 556 carateres.

- As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.

Se tiver mais de 15 valores que têm de ser associados a um recurso, utilize uma cadeia JSON para o valor da etiqueta. A cadeia JSON pode conter muitos valores que são aplicados a uma chave de etiqueta única.

### <a name="tags-and-billing"></a>As etiquetas e faturação

As etiquetas permitem-lhe agrupar os dados de faturação. Por exemplo, se estiver a executar várias VMs para diferentes organizações, utilize as etiquetas para utilização do grupo pelo centro de custos. Também pode utilizar etiquetas para categorizar os custos pelo ambiente de tempo de execução; Por exemplo, a utilização de faturação para VMs em execução no ambiente de produção.

Pode obter informações sobre etiquetas através de [utilização de recursos do Azure e RateCard APIs](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) ou o ficheiro de valores separados por vírgulas (CSV) de utilização. Transferir o ficheiro de utilização do [portal de contas do Azure](https://account.windowsazure.com/) ou [EA portal](https://ea.azure.com/).

>[!Note]
> Para obter mais informações sobre acesso programático para as informações de faturação, consulte [obter informações acerca do consumo de recursos do Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Para operações de REST API, consulte [referência de API de REST de faturação do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando transferir a utilização de CSV para serviços que suportam etiquetas com faturação, as etiquetas são apresentadas na coluna de etiquetas.

## <a name="critical-resource-controls"></a>Controlos de recurso crítico

Como a sua organização adiciona serviços principais para a subscrição, torna-se cada vez mais importante certificar-se de que esses serviços estão disponíveis para evitar a interrupção de negócios. [Bloqueios de recurso](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) permitem-lhe restringir operações nos recursos de elevado valor onde modificar ou eliminá-los teria um impacto significativo na sua aplicações ou a infraestrutura de nuvem. Pode aplicar bloqueios para uma subscrição, o grupo de recursos ou o recurso. Normalmente, aplicar bloqueios dos recursos, como redes virtuais, gateways e as contas de armazenamento.

Bloqueios de recursos atualmente suportam dois valores: CanNotDelete e só de leitura. CanNotDelete significa que os utilizadores (com os direitos adequados) podem ainda ler ou modificar um recurso, mas não é possível eliminá-lo. Só de leitura significa que os utilizadores autorizados não é possível eliminar ou modificar um recurso.

Bloqueios de Gestor de recursos são aplicadas apenas a operações acontecer na plane a gestão, que consiste em operações de envio para <https://management.azure.com>. Os bloqueios restringe como recursos executar as suas próprias funções. Alterações de recurso estão limitadas, mas as operações de recurso não estão restringidas. Por exemplo, um bloqueio de só de leitura de uma base de dados do SQL Server impede-o de eliminar ou modificar a base de dados, mas não o impede de criar, atualizar ou eliminar dados na base de dados.

Aplicar **ReadOnly** pode originar resultados inesperados porque algumas operações que parecem como leitura operações necessitam de ações adicionais. Por exemplo, colocar uma **ReadOnly** bloqueio numa conta de armazenamento impede que todos os utilizadores listar as chaves. A lista de operação de chaves é processada através de um pedido POST porque as chaves devolvidas estão disponíveis para operações de escrita.

![Controlos de recurso crítico](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Para obter outro exemplo, colocar um bloqueio de só de leitura um recurso de serviço de aplicações impede o Explorador de servidores do Visual Studio a apresentação de ficheiros para o recurso porque esse interação requer acesso de escrita.

Ao contrário do controlo de acesso baseado em funções, utilize as bloqueios de gestão para aplicar uma restrição em todos os utilizadores e funções. Para saber mais sobre a definição de permissões de utilizadores e funções, consulte [controlo de acesso baseado em funções do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Ao aplicar um bloqueio num âmbito principal, todos os recursos desse âmbito herdam o bloqueio do mesmo. Recursos mesmo que adicionar mais tarde herdam o bloqueio do principal. O bloqueio mais restritivo na herança tem precedência.

Para criar ou eliminar as bloqueios de gestão, tem de ter acesso a Microsoft.Authorization/ _ou Microsoft.Authorization/locks/_ ações. Das funções incorporadas, apenas **proprietário** e **administrador de acesso de utilizador** concedidas essas ações.

## <a name="api-access-to-billing-information"></a>Acesso a API para as informações de faturação

Utilize as APIs de faturação do Azure para retirar dados de utilização e de recursos para as ferramentas de análise de dados preferencial. A utilização de recursos do Azure e RateCard APIs podem ajudá-lo com precisão prever e gerir os custos. As APIs são implementadas como um fornecedor de recursos e a parte da família de APIs expostas pelo Azure Resource Manager.

### <a name="azure-resource-usage-api-preview"></a>Utilização de recursos do Azure API (pré-visualização)

Utilizar o Azure [API de utilização de recursos](https://msdn.microsoft.com/library/azure/mt219003) para obter os seus dados de consumo estimado do Azure. A API inclui:

- **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com/) ou através de [cmdlets Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os chamadores tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função ou o leitor de faturação, leitor, proprietário ou contribuinte obter acesso aos dados de utilização de uma subscrição do Azure específica.

- **Hora a hora ou agregações diárias** - os chamadores podem especificar registos se pretendem os seus dados de utilização do Azure hora a hora ou diária de registos. A predefinição é diária.

- **Os metadados de instância (inclui os sinalizadores de recurso)** – obter detalhes de nível de instância, como o uri de recurso completamente qualificado (/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} /..), a informações do grupo de recursos e as etiquetas de recursos. Estes metadados ajuda-o a deterministicamente e através de programação alocar utilização por etiquetas, para casos de utilização como charging de cruzada.

- **Metadados do recurso** -detalhes de recursos, tais como o nome de medição, categoria de medição, categoria de subchaves de medição, unidade e região dar o autor da chamada uma melhor compreensão sobre o que estava a ser consumido. Estamos a trabalhar também para assegurar a terminologia de metadados de recursos através do portal do Azure, a utilização do Azure CSV, EA CSV e outras experiências de destinado ao público, permitem-lhe correlacionar dados através de experiências de faturação.

- **Utilização para todos os tipos de oferta** – dados de utilização estão disponíveis para oferecem todos os tipos como pay as you go, o MSDN, o compromisso monetário, o crédito monetário e o EA.

**Recursos do Azure API RateCard (pré-visualização)**

Utilize a API de RateCard de recursos do Azure para obter a lista de recursos do Azure disponíveis e as informações de preços estimadas para cada. A API inclui:

- **Controlo de acesso baseado em funções do Azure** - configurar as políticas de acesso no portal do Azure ou através de cmdlets do Azure PowerShell para especificar quais os utilizadores ou aplicações podem obter acesso aos dados RateCard. Os chamadores tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função ou o leitor, proprietário ou contribuinte obter acesso aos dados de utilização para uma determinada subscrição do Azure.

- **Suporte para pay as you go, o MSDN, o compromisso monetário e o crédito monetário oferece (EA não suportada)** -esta API fornece informações de taxa de nível de oferta do Azure. O autor da chamada desta API tem de passar as informações de oferta para obter os detalhes do recurso e taxas. Estamos atualmente não é possível fornecer taxas EA porque EA ofertas tem personalizado taxas por inscrição. Seguem-se alguns dos cenários que são efetuados possíveis com a combinação da utilização e as APIs de RateCard:

- **Azure gastam durante o mês** - utilize a combinação de padrões de utilização e gaste RateCard APIs para obter informações sobre a melhor nuvem durante o mês. Pode analisar os registos de hora a hora e diários de estimativas de utilização e encargos.

- **Configure alertas** – utilizar a utilização e as APIs de RateCard para obter o consumo de nuvem estimado e custos e configure baseada em recursos ou monetário com base em alertas.

- **Prever fatura** – Get seu consumo estimado nuvem gastam e aplicar algoritmos de machine learning para prever a que a fatura seria no fim do ciclo de faturação.

- **Pré-consumo de análise de custos** – utilizar a API de RateCard para prever a quantidade a fatura seria para a sua utilização esperada ao mover as cargas de trabalho para o Azure. Se tiver cargas de trabalho existentes em nuvens privadas ou outros nuvens, também é possível mapear a utilização com o Azure gastam a taxas para obter uma estimativa melhor do Azure. Esta estimativa dá-lhe a capacidade de dinâmico na oferta e comparar entre os tipos de oferta diferentes para além de pay as you go, como o compromisso monetário e crédito monetário. Além disso, a API dá-lhe a capacidade para ver as diferenças de custo por região e permite-lhe fazer uma análise de custos investiguem para o ajudar a tomar decisões de implementação.

- **Análise de investiguem** -pode determinar se é mais económico executar cargas de trabalho noutra região, ou outra configuração do recurso do Azure. Os custos de recursos do Azure podem divergir com base na região do Azure que está a utilizar.

- Também pode determinar se o outro tipo de oferta do Azure fornece uma melhor taxa sobre um recurso do Azure.

## <a name="networking-controls"></a>Controlos de rede

Pode ser o acesso aos recursos externos (através da internet) ou interno (na rede da empresa). É fácil para os utilizadores na sua organização inadvertidamente colocar recursos a errado lugar para cima e, potencialmente, abra-los para acesso malicioso. Tal como no local / dispositivos, as empresas tem de adicionar controlos adequados para garantir que os utilizadores do Azure tomar decisões corretas.

![Controlos de rede](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Para governação de subscrição, podemos identificar recursos principais do que permitem controlar básica de acesso. Os recursos de núcleos é composto por:

### <a name="network-connectivity"></a>Conectividade de rede

[Redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) são objetos de contentor para sub-redes. Apesar de não estritamente necessários, muitas vezes, é utilizado ao estabelecer ligação a aplicações a recursos empresariais internos. O serviço de rede Virtual do Azure permite-lhe ligar de forma segura a recursos do Azure uns aos outros com redes virtuais (VNets).

Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da nuvem do Azure dedicado à sua subscrição. Também pode ligar VNets à sua rede no local.

Seguem-se capacidades para redes virtuais do Azure:

- **Isolamento**: as VNets estão isoladas entre si. Pode criar VNets separadas para o desenvolvimento, teste e produção, que utilizam os mesmos blocos de endereços CIDR. Por outro lado, pode criar várias VNets que utilizam diferentes blocos de endereços CIDR e ligar redes em conjunto. Pode segmentar uma VNet em várias sub-redes. O Azure oferece resolução dos nomes internos para VMs e serviços em nuvem instâncias de função ligadas a uma VNet. Opcionalmente, pode configurar uma VNet para utilizar os seus próprios servidores DNS, em vez de utilizar a resolução do nome interno do Azure.

- **Conectividade Internet**: instâncias de função de todas as do Azure máquinas virtuais (VMS) e serviços em nuvem ligadas a uma VNet tem acesso à Internet, por predefinição. Também pode ativar o acesso de entrada a recursos específicos, conforme necessário.

- **Conectividade de recursos do Azure**: recursos do Azure, tais como serviços em nuvem e as VMs podem ser ligados para a mesma VNet. Os recursos podem ligar-se entre si utilizando endereços IP privados, mesmo que se encontrem em sub-redes diferentes. O Azure oferece encaminhamento predefinido entre sub-redes, VNets e redes no local, pelo que não tem de configurar e gerir as rotas.

- **Conectividade de VNet**: VNets podem ser ligadas entre si, permitindo recursos ligados a qualquer VNet para comunicar com qualquer recurso no qualquer outra VNet.

- **No local conectividade**: VNets podem ser ligadas a redes no local através de ligações de rede privada entre a rede e o Azure ou através de uma ligação de VPN de site a site através da Internet.

- **Filtragem de tráfego**: VM e serviços em nuvem tráfego de rede de instâncias de função pode ser filtrado entrada e saída por endereço IP de origem e porta, endereço IP de destino e porta e protocolo.

- **Encaminhamento**: Opcionalmente, pode substituir predefinição do Azure encaminhamento por configurar as seus próprios rotas ou utilizar rotas BGP através de um gateway de rede.

## <a name="network-access-controls"></a>Controlos de acesso de rede

[Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são como uma firewall e fornecem regras para como um recurso pode "conversar"com através da rede. Fornecem um controlo granular sobre como / se uma sub-rede (ou a máquina virtual) pode ligar à Internet ou outras sub-redes na mesma rede virtual.

Os grupos de segurança de rede (NSG) contêm uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos ligados a Redes Virtuais do Azure (VNet). Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou a interfaces de rede individuais (NIC) ligadas a VMs (Resource Manager).

Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma. É possível restringir ainda mais o tráfego ao associar também um NSG a uma VM ou a uma NIC.

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>Segurança e compatibilidade contínuas com as normas organizacionais

Cada empresa tem necessidades diferentes e cada negócio será reap distintas beneficia de soluções de nuvem. Ainda assim, os clientes de todos os tipos têm as mesmas básicas preocupações mover para a nuvem. Pretende manter o controlo dos respetivos dados, e querem que os dados que serão guardadas segura e privadas, tudo, mantendo em simultâneo transparência e conformidade.

O que os clientes pretendem de fornecedores de nuvem é:

- **Proteger os nossos dados** enquanto confirmar que a nuvem pode fornecer aumenta a segurança dos dados e controlo administrativo, líderes das IT ainda são preocupações que migrar para a nuvem irá deixá-los mais vulnerável a hackers que as soluções de internas atuais.

- **Manter os nossos dados** serviços de Cloud privados elevar os desafios de privacidade exclusivo para as empresas. Como ver empresas para a nuvem para poupar nos custos da infraestrutura e melhorar a respetiva flexibilidade, estes também preocupar com a perda do controlo de onde os dados são armazenados, que está a aceder ao mesmo e como é utilizado.

- **Dê-nos controlo** mesmo à medida que possam tirar partido da nuvem para implementar soluções inovadoras mais, as empresas são muito preocupadas com a perda do controlo dos respetivos dados. As divulgações recentes das agências governamentais aceder aos dados de cliente, através de meios legais e muito legais, certifique-algumas CIOs wary de armazenar os seus dados na nuvem.

- **Promover a transparência** enquanto de segurança, privacidade e controlo são importantes para os decisores de empresas, também pretende a capacidade para verificar independentemente como os respetivos dados sejam armazenados, acedidos e protegidos.

- **Manter a conformidade** como empresas expandir a sua utilização de tecnologias de nuvem, a complexidade e o âmbito de padrões e regulamentos continuarem a evoluir. As empresas precisam de saber o que irão cumprir as normas de conformidade e de que esse conformidade irá evoluir como regulamentos alteração ao longo do tempo.

## <a name="security-configuration-monitoring-and-alerting"></a>Configuração de segurança, monitorização e alertas

Os subscritores do Azure poderão gerir os respetivos ambientes de nuvem a partir de vários dispositivos, incluindo estações de trabalho de gestão, PCs de programadores e, até mesmo, dispositivos de utilizador final com privilégios que tenham permissões específicas de tarefas. Em alguns casos, as funções administrativas são efetuadas através das consolas baseadas na web, tais como o portal do Azure. Noutros casos, poderão existir ligações diretas para o Azure a partir de sistemas no local através de Redes Privadas Virtuais (VPNs), Serviços de Terminal, protocolos de aplicações cliente ou (através de programação) a API de Gestão de Serviço do Azure (SMAPI). Além disso, os pontos finais de cliente podem ser um domínio associado ou isolado e não gerido, como tablets ou smartphones.

Apesar de estas diversas capacidades de gestão e acesso fornecerem um conjunto avançado de opções, esta variabilidade pode acarretar um risco significativo para uma implementação de nuvem. Pode ser difícil gerir, controlar e auditar as ações administrativas. Este variabilidade também poderá acarretar ameaças de segurança através do acesso não regulado para os pontos finais de cliente que são utilizados para gerir os Cloud Services. A utilização das estações de trabalho gerais ou pessoais para desenvolver e gerir a infraestrutura abre vetores de ameaças imprevisíveis, como navegação na Web (por exemplo, ataques de tipo “watering hole”) ou e-mail (engenharia social e phishing).

A monitorização, o registo e a auditoria fornecem uma base para controlar e compreender as atividades administrativas, mas podem nem sempre ser viáveis para auditar todas as ações em detalhe devido à quantidade de dados gerados. A auditoria da eficácia das políticas de gestão é a melhor prática.

Segurança do Azure governação de GPOs do AD DS para controlar Windows todos os administradores das interfaces, tais como a partilha de ficheiros. Inclua estações de trabalho de gestão nos processos de auditoria, monitorização e de registo. Controle todos os acessos e utilizações de administrador e programador.

### <a name="azure-security-center"></a>Centro de segurança do Azure

O [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornece uma vista do Estado de segurança dos recursos nas subscrições central e fornece recomendações que ajudam a impedir recursos comprometidos. Pode ativar a políticas mais granulares (por exemplo, aplicar políticas de grupos de recursos específicos que permitem à empresa personalizar a sua postura ao risco que são endereçamento).

![Centro de Segurança do Azure](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Centro de segurança fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança. Depois de ativar [políticas de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies) para recursos de uma subscrição, o Centro de segurança analisa a segurança dos seus recursos para identificar potenciais vulnerabilidades. As informações sobre a configuração da rede estão disponíveis de forma instantânea.

Centro de segurança do Azure representa uma combinação de melhor prática análise e segurança gestão de políticas para todos os recursos dentro de uma subscrição do Azure. Esta ferramenta poderosa e fáceis de utilizar permite que as equipas de segurança e riscos officers evitar, detetar e responder a ameaças de segurança como automaticamente recolhe e analisa os dados de segurança dos seus recursos do Azure, rede e soluções de parceiros, como os programas antimalware e firewalls.

Além disso, o Centro de segurança do Azure aplica análises avançadas, incluindo machine learning e análise comportamental tirando partido de ameaças globais de produtos da Microsoft e serviços, a unidade Microsoft Crimes digitais (DCU), o Microsoft Security Response Center (MSRC) e de feeds externos. [Governação de segurança](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) podem ser aplicadas amplamente ao nível da subscrição ou narrowed requisitos específicos, granulares aplicada a recursos individuais através da definição de política.

Por fim, o Centro de segurança do Azure analisa o funcionamento de segurança de recursos com base nessas políticas e utiliza-o para fornecer insightful dashboards e os alertas de eventos, tais como a deteção de software maligno ou a ligação de IP maliciosa tentativas.

>[!Note]
> Para obter mais informações sobre como aplicar recomendações, leia [Implementing security recommendations in Azure Security Center (Implementar recomendações de segurança no Centro de Segurança do Azure)](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Centro de segurança recolhe dados das suas máquinas virtuais para avaliar o respetivo estado de segurança, fornecer recomendações de segurança e alertá-lo a ameaças. Quando aceder primeiro ao centro de segurança, a recolha de dados está ativada em todas as máquinas virtuais na sua subscrição. Recomenda-se a recolha de dados, mas pode ativamente por [desativar a recolha de dados](https://docs.microsoft.com/azure/security-center/security-center-faq) na política de centro de segurança.

Por fim, o Centro de segurança do Azure é uma plataforma aberta que permite que parceiros da Microsoft e fornecedores independentes de software criar software plugs no Centro de segurança do Azure para melhorar as respetivas capacidades.

Centro de segurança do Azure monitoriza os seguintes recursos do Azure:

- Máquinas virtuais (VMs) (incluindo os serviços em nuvem)

- Redes Virtuais do Azure

- Serviço do SQL do Azure

- Integrado com a sua subscrição do Azure como uma firewall de aplicação web em VMs e de soluções de parceiros [ambiente de serviço de aplicações](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme).

### <a name="operations-management-suite"></a>Operations Management Suite

Segurança de informações do OMS software desenvolvimento e o serviço da equipa e [programa governação](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) suporta os requisitos de negócio e em conformidade com as leis e regulamentos conforme descrito em [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) e [compatibilidade de centro de fidedignidade da Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Como OMS estabelecer requisitos de segurança, identifica os controlos de segurança, gere e monitoriza riscos também estão descritas não existe. Anual, vamos rever as políticas, normas, diretrizes e procedimentos.

Cada membro de equipa de desenvolvimento do OMS recebe formação de segurança da aplicação formal. Internamente, iremos utilizar um sistema de controlo de versão para o desenvolvimento de software. Cada projeto de software está protegido pelo sistema de controlo de versão.

A Microsoft tem uma equipa de segurança e conformidade supervisiona e avalia todos os serviços Microsoft. Officers de segurança de informações, certifique-se a equipa e que não estiverem associados a engenharia departamentos que desenvolver OMS. Os officers de segurança têm as seus próprios cadeia de gestão e realize avaliações independentes dos produtos e serviços para garantir a segurança e conformidade.

O Operations Management Suite (também conhecido como OMS) é uma coleção de serviços de gestão concebidos desde o início para a cloud. Em vez de implementar e gerir recursos no local, os componentes do OMS inteiramente estão alojados no Azure. A configuração é mínima e pode começar a utilizá-lo numa questão de minutos.

![Suite do Operations Manager](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Lá porque os serviços do OMS são executados na cloud, não significa que não consigam gerir eficazmente o seu ambiente no local.

Colocar um agente em todas as janelas ou computador com Linux no seu centro de dados e irá enviar dados para análise de registos em que pode ser analisado juntamente com todos os outros dados recolhidos a partir da nuvem ou em serviços no local. Utilize a cópia de segurança do Azure e o Azure Site Recovery para tirar partido da nuvem para cópia de segurança e a elevada disponibilidade para recursos no local.

Normalmente, os runbooks na cloud não podem aceder aos recursos no local, mas pode também instalar um agente num ou mais computadores que alojarão os runbooks no seu datacenter. Quando inicia um runbook, basta especificar se quer que seja executado na cloud ou numa função de trabalho.

A funcionalidade principal do OMS é disponibilizada por um conjunto de serviços que são executados no Azure. Cada serviço proporciona uma função de gestão específica e pode combinar serviços para alcançar cenários de gestão diferentes.

![Suite do Operations Manager](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Gestor de operação do Azure expande as funcionalidades ao fornecer soluções de gestão. [As soluções de gestão](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) prepackaged conjuntos de lógica que implementam um cenário de gestão tirar partido de um ou mais serviços do OMS.

![Gerir a operação do Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Estão disponíveis diferentes soluções da Microsoft e de parceiros, que podem ser adicionados facilmente à sua subscrição do Azure para aumentar o valor acrescentado do investimento feito no OMS.

Como parceiro, pode criar as suas próprias soluções para suportar as suas aplicações e serviços e fornecem aos utilizadores através do Azure Marketplace ou modelos de início rápido.

## <a name="performance-alerting-and-monitoring"></a>Monitorização e alertas de desempenho

### <a name="alerting"></a>Alertas

Os alertas são um método de registos, métricas de recurso do Azure ou eventos de monitorização e a ser notificado quando uma condição especifique for cumprido.

**Alertas nas diferentes serviços do Azure**

Alertas estão disponíveis em diferentes serviços, incluindo:

- Application Insights: Permite o teste web e métricas alertas.

>[!Note]
> Consulte [definir alertas no Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) e [monitorizar a disponibilidade e capacidade de resposta de qualquer Web site](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- Análise de registos (Operations Management Suite): Permite que o encaminhamento de atividade e os registos de diagnóstico para análise de registos. Operations Management Suite permite métrica, registo e outros tipos de alerta.

>[!Note]
> Para obter mais informações, consulte alertas no [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- Monitor do Azure: Permite que os alertas com base nos valores métricos e eventos de registo de atividade. Pode utilizar o [API REST da Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) para gerir alertas.

>[!Note]
> Para obter mais informações, consulte [utilizando o portal do Azure, PowerShell ou a interface de linha de comandos para criar alertas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitorização

Problemas de desempenho na sua aplicação em nuvem podem afetar a sua empresa. Com os vários componentes interligados e versões frequentes, degradations pode acontecer em qualquer altura. E se estiver a desenvolver uma aplicação, os utilizadores, geralmente, detetar problemas que não foi encontrado no teste. Deve conhecer sobre estes problemas imediatamente e tem as ferramentas para diagnosticar e corrigir os problemas. Microsoft Azure tem uma série de ferramentas para identificar estes problemas.

**Como posso monitorizar as minhas aplicações em nuvem do Azure?**

Há uma série de ferramentas para a monitorização de serviços e aplicações do Azure. Algumas das funcionalidades correspondentes sobrepõem-se. Este é parcialmente por motivos de histórico e parcialmente devido a diária esbater pessoal entre desenvolvimento e a operação de uma aplicação.

Seguem-se as ferramentas principais:

- **Monitor do Azure** ferramenta básico para monitorizar os serviços em execução no Azure. Proporciona ao nível da infraestrutura dados sobre o débito de um serviço e o ambiente surrounding. Se estiver a gerir as suas aplicações tudo no Azure, decidir se pretende aumentar ou reduzir verticalmente de recursos, em seguida, o Monitor de Azure dá-lhe a utilizar para iniciar.

- **Application Insights** podem ser utilizadas para desenvolvimento e como uma solução de monitorização de produção. Funciona ao instalar um pacote na sua aplicação e, pelo que lhe dê uma vista mais interna de que está a suceder. Os dados incluem tempos de resposta das dependências, rastreios de exceção, a depuração de instantâneos, perfis de execução. Fornece ferramentas inteligentes poderosas para analisar este telemetria para ajudar a depurar uma aplicação e para ajudar a compreender de que os utilizadores estão a fazer com o mesmo. Pode saber se um pico de pedidos de tempos de resposta é devido a algo numa aplicação ou algum problema resourcing externo. Se utilizar o Visual Studio e a aplicação está com falha, é pode ser tomados direito para as linhas de problema de código, para que o pode corrigir.

- **Análise de registo** destina-se os utilizadores que precisam para otimizar o desempenho e planear a manutenção em aplicações em execução na produção. Baseia-se no Azure. Este recolhe e agrega dados de várias origens, embora com um atraso de 10 a 15 minutos. Fornece uma solução de gestão de IT holística do Azure, no local e a infraestrutura de terceiros baseados na nuvem (tais como os Amazon Web Services). Fornece ferramentas mais rica para analisar os dados de mais origens, permite consultas complexas em todos os registos e proativamente podem alertá em condições especificadas. Mesmo pode recolher dados personalizados para o repositório central para que possa consultar e visualizá-lo.

- **System Center Operations Manager (SCOM)** é para gerir e monitorizar as instalações de nuvem de elevado. Poderá estar já familiarizado com o mesmo como uma ferramenta de gestão no local Windows Server e Hyper-V com base-nuvens, mas também pode integrar e gerir aplicações do Azure. Entre outras coisas, poderá instalar Application Insights num aplicações em direto existentes. Se uma aplicação ficar inativo, indica em segundos.


## <a name="next-steps"></a>Passos seguintes

- [Melhores práticas para a criação de modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

- [Exemplos para implementar a governação de subscrição do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-examples).

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/).
