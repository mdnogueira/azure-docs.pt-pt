---
title: "Descrição geral da política do Azure | Microsoft Docs"
description: "Política do Azure é um serviço no Azure, o que utilizar para criar, atribuir e, gerir definições de política no seu ambiente do Azure."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/25/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 82721fe984ff7b3c7440b11d7526a9413b0770de
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="what-is-azure-policy"></a>O que é a política do Azure?

Governação de TI cria efeitos de clareza entre objetivos empresariais e projetos IT. Governação de TI boa envolve as iniciativas de planeamento e a definição de prioridades num nível estratégicos. A empresa experiência de um número significativo de problemas de TI nunca parecem obter resolvido? Implementar políticas ajuda-o melhor gerir e impedi-los. Implementar políticas é onde a política do Azure é apresentada no.

Política do Azure é um serviço no Azure que utilizar para criar, atribuir e, gerir as definições de política. Definições de política impor diferentes regras e ações sobre os recursos, pelo que o se esses recursos manter em conformidade com os padrões empresariais e contratos de nível de serviço. Política do Azure executa uma edição de avaliação dos seus recursos, a análise para aqueles que não sejam compatíveis com as definições de política que tem. Por exemplo, pode ter uma política para permitir que apenas determinados tipo de máquinas virtuais. Outro requer que todos os recursos tem uma tag específica. Estas políticas são então avaliadas quando criar e atualizar recursos.

## <a name="how-is-it-different-from-rbac"></a>Como é diferente do RBAC?

Existem alguns principais diferenças entre a política e o controlo de acesso baseado em funções (RABC). RBAC concentra-se nas ações de utilizador em âmbitos diferentes. Por exemplo, podem ser adicionadas à função de contribuinte para um grupo de recursos no âmbito pretendido. A função permite-lhe efetuar alterações nesse grupo de recursos. Política concentra-se nas propriedades de recursos durante a implementação e de já existente recursos. Por exemplo, através de políticas, pode controlar os tipos de recursos que podem ser aprovisionados. Em alternativa, pode restringir as localizações onde os recursos podem ser aprovisionados. Ao contrário do RBAC, a política é permitir que um predefinido e explícita negar sistema.

Utilizar políticas, tem de ser autenticado através do RBAC. Especificamente, a conta tem de:

- `Microsoft.Authorization/policydefinitions/write`permissão para definir uma política.
- `Microsoft.Authorization/policyassignments/write`permissão para atribuir uma política.

Estas permissões não estão incluídas no **contribuinte** função.


## <a name="policy-definition"></a>Definição de política

Cada definição de política tem condições sob as quais é aplicada. Além disso, tem uma ação associada que decorre se as condições são cumpridas.

Na política do Azure, que oferecemos algumas políticas incorporadas que estão disponíveis para si por predefinição. Por exemplo:

- **Requer o SQL Server 12.0**: esta definição de política tem condições/regras para se certificar de que todos os servidores SQL utilizam versão 12.0. A ação está a negar a todos os servidores que não cumpram estes critérios.
- **Permitido SKUs de conta de armazenamento**: esta definição de política tem um conjunto de condições/regras que determinam se é uma conta de armazenamento que está a ser implementada dentro de um conjunto de tamanhos SKU. A ação está a negar a todos os servidores que não cumprem o conjunto de tamanhos SKU definidos.
- **Permitido o tipo de recurso**: esta definição de política tem um conjunto de condições/regras para especificar os tipos de recurso que pode implementar a sua organização. A ação é negar a todos os recursos que não fazem parte desta lista definido.
- **Permitido localizações**: esta política permite-lhe restringir as localizações que a sua organização pode especificar quando implementar recursos. A ação é utilizada para impor os requisitos de conformidade de georreplicação.
- **Permitido SKUs de Máquina Virtual**: esta política permite-lhe especificar um conjunto de máquina virtual SKUs que pode implementar a sua organização.
- **Aplicar a etiqueta e o respetivo valor predefinido**: esta política aplica-se uma tag necessária e o valor predefinido, se não for especificado pelo utilizador.
- **Impor a etiqueta e o respetivo valor**: esta política impõe uma tag necessária e o respetivo valor para um recurso.
- **Não é permitida tipos de recursos**: esta política permite-lhe especificar os tipos de recurso que não é possível implementar a sua organização.

Pode atribuir alguma destas políticas através do portal do Azure, o PowerShell ou a CLI do Azure.

Para saber mais sobre as estruturas de definições de política, consulte este artigo - [estrutura de definição de política](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Atribuição de política

Uma atribuição de política é uma definição de política que tenha sido atribuída para ocorrer dentro de um âmbito específico. Este âmbito foi intervalo de um grupo de gestão a um grupo de recursos. Atribuições de políticas são herdadas por todos os recursos subordinados. Por isso, se uma política é aplicada a um grupo de recursos, é aplicada a todos os recursos nesse grupo de recursos. O termo *âmbito* refere-se a todos os grupos de recursos, subscrições ou grupos de gestão que está atribuída a definição de política.

Para obter mais informações sobre definições de política de configuração e atribuições, consulte [criar uma atribuição de política para identificar recursos incompatíveis no seu ambiente do Azure](assign-policy-definition.md).

## <a name="policy-parameters"></a>Parâmetros de política

Parâmetros de política ajudam a simplificar a gestão de política, reduzindo o número de definições de política que tem de criar. Pode definir parâmetros durante a criação de uma definição de política para o tornar mais genérico. Em seguida, pode reutilizar essa definição de política para diferentes cenários. Fazê-lo mediante a transmissão em diferentes valores de definição de política de atribuição. Por exemplo, especificando um conjunto de localizações para uma subscrição.

Os parâmetros são definidos/criados durante a criação de uma definição de política. Quando está definido um parâmetro, é fornecido um nome e, opcionalmente, indicado um valor. Por exemplo, pode definir um parâmetro para uma política intitulada *localização*. Em seguida, o pode atribuir-lhe valores diferentes, tais como *EastUS* ou *WestUS* ao atribuir uma política.

<!--
Next link should point to new Concept page for Parameters
-->
Para obter mais informações sobre os parâmetros de política, consulte [recursos descrição geral da política - parâmetros](../azure-resource-manager/resource-manager-policy.md#parameters).

## <a name="initiative-definition"></a>Definição de iniciativa
Uma definição de iniciativa é a coleção de definições de política que são adaptados para alcançar o objetivo único abrangente. Definições da iniciativa simplificam a gestão e a atribuição de definições de política. Estes simplificam através do agrupamento de um conjunto de políticas, como um único item. Por exemplo, pode criar uma iniciativa intitulada **Ativar monitorização no Centro de segurança do Azure**, com o objetivo de monitorizar todas as recomendações de segurança disponíveis no seu centro de segurança do Azure.

Sob este iniciativa, terá de definições de política, tais como:

1. **Base de dados do SQL no Centro de segurança não encriptada de monitor** – para monitorização de servidores e bases de dados SQL não encriptadas.
2. **Monitorizar vulnerabilidades do SO no Centro de segurança** – para monitorização de servidores que não satisfaçam a linha de base configurada.
3. **Monitorizar a proteção de ponto final em falta no Centro de segurança** – para monitorização de servidores sem um agente de proteção de ponto final instalada.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Atribuição de iniciativa
Como uma atribuição de política, uma iniciativa atribuição é uma definição de iniciativa atribuída a um âmbito específico. Atribuições de iniciativa reduzem a necessidade de várias definições de iniciativa para cada âmbito. Este âmbito também pode ir desde um grupo de gestão para um grupo de recursos.

O exemplo anterior, o **Ativar monitorização no Centro de segurança do Azure** iniciativa pode ser atribuída a âmbitos diferentes. Por exemplo, uma atribuição pode ser atribuída a **subscriptionA**. Outro pode ser atribuído a **subscriptionB**.

## <a name="initiative-parameters"></a>Parâmetros de iniciativa
Como parâmetros de política, parâmetros iniciativa ajudam a simplificar a iniciativa de gestão, reduzindo a redundância. Parâmetros de iniciativa são, essencialmente, a lista de parâmetros que está a ser utilizado pelas definições de política dentro da iniciativa.

Por exemplo, assumir um cenário em que tem uma definição de iniciativa - **initiativeC**, com duas definições de política. Cada definição de política ter um parâmetro definido:

| Política | nome do parâmetro |Tipo de parâmetro  |Nota |
|---|---|---|---|
| policyA | allowedLocations | Matriz  |Este parâmetro espera uma lista de cadeias para um valor de uma vez que o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |Cadeia |Este parâmetro espera um word para um valor, uma vez que o tipo de parâmetro foi definido como uma cadeia |

Neste cenário, quando definir os parâmetros da iniciativa para **initiativeC**, tem três opções:

1. Utilizar os parâmetros das definições de política dentro deste iniciativa: neste exemplo, *allowedLocations* e *allowedSingleLocation* tornar-se os parâmetros da iniciativa para **initiativeC** .
2. Indique os valores para os parâmetros das definições de política dentro desta definição iniciativa. Neste exemplo, pode fornecer uma lista de localizações para **parâmetro do policyA – allowedLocations** e **parâmetro do policyB – allowedSingleLocation**.
Também pode fornecer valores ao atribuir esta iniciativa.
3. Forneça uma lista de *valor* opções que podem ser utilizadas ao atribuir esta iniciativa. Quando atribui este iniciativa, os parâmetros herdados das definições de política numa iniciativa, só pode ter valores desta lista fornecida.

Por exemplo, poderá criar uma lista de opções de valor na definição iniciativa que contêm *EastUS*, *WestUS*, *CentralUS*, e *WestEurope*. Se, por isso, não é possível introduzir um valor diferente, tal como *Sudeste asiático* durante a atribuição de iniciativa, porque este não faz parte da lista.

## <a name="recommendations-for-managing-policies"></a>Recomendações para a gestão de políticas

Ao criar e gerir definições de política e atribuições, eis alguns pontos que aconselhamos a seguir:

- Se estiver a criar definições de política no seu ambiente, recomendamos que comece com um efeito de auditoria, por oposição a um efeito de negar, para controlar o impacto da sua definição de política nos recursos no seu ambiente. Se tiver scripts já no local para dimensionar automaticamente cópias de segurança das suas aplicações, um efeito de negar a definição pode impedir a essas tarefas automatizações que já tem no local.
- É importante manter organizacionais hierarquias em mente quando criar as definições e atribuições. Recomendamos a criação de definições num nível superior, por exemplo, no grupo de gestão ou o nível de subscrição e atribuir no nível subordinado seguinte. Por exemplo, se criar uma definição de política ao nível do grupo de gestão, os de que a definição de uma atribuição de política pode ser confinada para um nível de subscrição nesse grupo de gestão.
- Aconselhamo-utilizando a camada de preço padrão para compreender melhor o estado de compatibilidade do seu ambiente. Para obter mais informações sobre os nossos modelos de preços e que cada um deles oferecem, observe [preços](https://azure.microsoft.com/pricing/details/azure-policy).
- Recomendamos a utilização sempre iniciativa definições em vez de definições de política, mesmo se tiver apenas uma política em mente. Por exemplo, se tiver uma definição de política – *policyDefA* e criá-la sob a definição de iniciativa - *initiativeDefC*, se optar por criar outra definição de política mais tarde para *policyDefB* com objetivos semelhantes de *policyDefA*, pode adicioná-lo em *initiativeDefC* e controlá-los melhor dessa forma.

   Tenha em atenção que assim que tiver criado uma atribuição de iniciativa da definição iniciativa, as novas definições de política adicionados automaticamente a definição da iniciativa de agregação em assignment(s) a iniciativa em que a definição do iniciativa. No entanto, se existir um novo parâmetro introduzido para a nova definição de política, terá de atualizar a definição da iniciativa e atribuições ao editar a definição de iniciativa ou a atribuição.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral da política do Azure e alguns dos conceitos chaves que está a introduzir, Eis os passos sugeridos:

- [Atribuir uma definição de política](./assign-policy-definition.md)
- [Atribuir uma definição de política utilizando a CLI do Azure](./assign-policy-definition-cli.md)
- [Atribuir uma definição de política com o PowerShell](./assign-policy-definition-ps.md)
