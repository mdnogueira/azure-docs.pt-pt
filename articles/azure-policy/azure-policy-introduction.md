---
title: "Descrição geral da política do Azure | Microsoft Docs"
description: "Política do Azure é um serviço no Azure, o que utilizar para criar, atribuir e, gerir definições de política no seu ambiente do Azure."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 01b0915cdf37ddc00a4e6a38c99ce7f6f8c4f9c9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-policy"></a>O que é a política do Azure?

Governação de TI cria efeitos de clareza entre objetivos empresariais e projetos IT. Governação de TI boa envolve as iniciativas de planeamento e a definição de prioridades num nível estratégicos. Se a sua empresa sofre um número significativo de problemas de TI nunca parecem obter resolvido, implementar políticas ajuda-o a melhor gerir e impedi-los. Este é onde a política do Azure é apresentada no.

Política do Azure é um serviço no Azure que utilizar para criar, atribuir e, gerir as definições de política. Definições de política impor diferentes regras e ações sobre os recursos, pelo que o se esses recursos manter em conformidade com os padrões empresariais e contratos de nível de serviço. Este é ao executar uma edição de avaliação dos seus recursos, para analisar para os quais aqueles que não são compatíveis com as definições de política que tem.

## <a name="policy-definition"></a>Definição de política

Cada definição de política tem condições sob as quais é imposta e uma ação associada, que demora coloque se as condições são cumpridas.

Na política do Azure, que oferecemos algumas políticas incorporadas que estão disponíveis para si por predefinição. Por exemplo:

- **Requer o SQL Server 12.0**: esta definição de política tem condições/regras para se certificar de que todos os servidores SQL utilizam versão 12.0. A ação está a negar a todos os servidores que não cumpram estes critérios.
- **Permitido SKUs de conta de armazenamento**: esta definição de política tem um conjunto de condições/regras que determinam se é uma conta de armazenamento que está a ser implementada dentro de um conjunto de tamanhos SKU. A ação está a negar a todos os servidores que não cumprem o conjunto de tamanhos SKU definidos.
- **Permitido o tipo de recurso**: esta definição de política tem um conjunto de condições/regras para especificar os tipos de recurso que pode implementar a sua organização. A ação é negar a todos os recursos que não fazem parte desta lista definido.

Para saber mais sobre as estruturas de definições de política, consulte este artigo - [estrutura de definição de política](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Atribuição de política
Uma atribuição de política é uma definição de política que tenha sido atribuída para ocorrer dentro de um âmbito específico. Este âmbito foi intervalo de um grupo de gestão a um grupo de recursos.

Para obter mais informações sobre definições de política de configuração e atribuições, consulte [descrição geral da política de recurso](../azure-resource-manager/resource-manager-policy.md).

## <a name="policy-parameters"></a>Parâmetros de política
Parâmetros de política ajudam a simplificar a gestão de política, reduzindo o número de definições de política que tem de criar. Pode definir parâmetros durante a criação de uma definição de política para o tornar mais genérico. Em seguida, pode reutilizar essa definição de política para diferentes cenários mediante a transmissão valores diferentes (por exemplo, especificar um conjunto de localizações para uma subscrição) quando atribuição da política.

Os parâmetros são definidos/criados durante a criação de uma definição de política. Quando está definido um parâmetro, é fornecido um nome e, opcionalmente, indicado um valor. Por exemplo, pode definir um parâmetro para uma política como uma localização e, em seguida, atribua-lhe valores diferentes, tais como *EastUS* ou *WestUS* ao atribuir uma política.

Para obter mais informações sobre os parâmetros de política, consulte [recursos descrição geral da política - parâmetros](../azure-resource-manager/resource-manager-policy.md#parameters).

## <a name="initiative-definition"></a>Definição de iniciativa
Uma definição de iniciativa é a coleção de definições de política que são adaptados para alcançar o objetivo único abrangente. Por exemplo, pode criar uma iniciativa intitulada **Ativar monitorização no Centro de segurança do Azure**, com o objetivo de monitorizar todas as recomendações de segurança disponíveis no seu centro de segurança do Azure.

Sob este iniciativa, terá de definições de política, tais como:

1. **Base de dados do SQL no Centro de segurança não encriptada de monitor** – para monitorização de servidores e bases de dados SQL não encriptadas.
2. **Monitorizar vulnerabilidades do SO no Centro de segurança** – para monitorização de servidores que não satisfaçam a linha de base configurada.
3. **Monitorizar a proteção de ponto final em falta no Centro de segurança** – para monitorização de servidores sem um agente de proteção de ponto final instalada.

## <a name="initiative-assignment"></a>Atribuição de iniciativa
Como uma atribuição de política, uma iniciativa atribuição é uma definição de iniciativa atribuída a um âmbito específico. Atribuições de iniciativa reduzem a necessidade de várias definições de iniciativa para cada âmbito. Este âmbito também pode ir desde um grupo de gestão para um grupo de recursos.

O exemplo anterior, o **Ativar monitorização no Centro de segurança do Azure** iniciativa pode ser atribuída a âmbitos diferentes. Por exemplo, uma atribuição pode ser atribuída a **subscriptionA**, enquanto outro pode ser atribuído a **subscriptionB**.

## <a name="initiative-parameters"></a>Parâmetros de iniciativa
Como parâmetros de política, parâmetros iniciativa ajudam a simplificar a iniciativa de gestão, reduzindo a redundância. Parâmetros de iniciativa são, essencialmente, a lista de parâmetros que está a ser utilizado pelas definições de política dentro da iniciativa.

Por exemplo, assumir um cenário em que tem uma definição de iniciativa - **initiativeC**, com duas definições de política. Cada definição de política ter um parâmetro definido:

|Política  |nome do parâmetro     |Tipo de parâmetro  |Nota                                                                                                |
|--------|----------------------|-------|----------------------------------------------------------------------------------------------------------------|
|policyA |allowedLocations      |Matriz  |Este parâmetro espera uma lista de cadeias para um valor de uma vez que o tipo de parâmetro foi definido como uma matriz |
|policyB |allowedSingleLocation |Cadeia |Este parâmetro espera um word para um valor, uma vez que o tipo de parâmetro foi definido como uma cadeia          |

Neste cenário, quando definir os parâmetros da iniciativa para **initiativeC**, tem três opções:

1. Tirar partido dos parâmetros das definições de política dentro deste iniciativa: neste exemplo, *allowedLocations* e *allowedSingleLocation* tornar-se os parâmetros da iniciativa para  **initiativeC**.
2. Indique os valores para os parâmetros das definições de política dentro desta definição iniciativa. Neste exemplo, pode fornecer uma lista de localizações para **parâmetro do policyA – allowedLocations** e **parâmetro do policyB – allowedSingleLocation**.
Também pode fornecer valores ao atribuir esta iniciativa.
3. Forneça uma lista de *valor* opções que podem ser utilizadas ao atribuir esta iniciativa. Isto significa que quando atribui este iniciativa, os parâmetros herdados das definições de política numa iniciativa, só pode ter valores desta lista fornecida.

Por exemplo, se a lista fornecida de valor opções ao criar a definição da iniciativa, tem – *EastUS*, *WestUS*, *CentralUS*, e *WestEurope* , não será possível para um valor diferente de entrada, tais como *Sudeste asiático* durante a atribuição de iniciativa, porque este não faz parte da lista.

## <a name="recommendations-for-managing-policies"></a>Recomendações para a gestão de políticas

Ao criar e gerir definições de política e atribuições, eis alguns pontos que aconselhamos a seguir:

- Se estiver a criar definições de política no seu ambiente, recomendamos que comece com controlar de efeito, por oposição a um efeito de negar, para manter uma auditoria o impacto da definição de política no seu ambiente. Se tiver scripts já no local para dimensionar automaticamente cópias de segurança das suas aplicações, um efeito de negar a definição pode impedir a essas tarefas automatizações que já tem no local.
- É importante manter organizacionais hierarquias em mente quando criar as definições e atribuições. Recomendamos a criação de definições num nível superior, por exemplo, no grupo de gestão ou o nível de subscrição e atribuir no nível subordinado seguinte. Por exemplo, se criar uma definição de política ao nível do grupo de gestão, os de que a definição de uma atribuição de política pode ser confinada para um nível de subscrição.
- Aconselhamo-utilizando a camada de preço padrão para compreender melhor o estado de compatibilidade do seu ambiente.
- Recomendamos a utilização sempre iniciativa definições em vez de definições de política, mesmo se tiver apenas uma política em mente. Por exemplo, se tiver uma definição de política – *policyDefA* e criá-la sob a definição de iniciativa - *initiativeDefC*, se optar por criar outra definição de política com objetivos como do *policyDefA*, simplesmente, pode adicioná-la em *initiativeDefC* e controlá-los melhor dessa forma.

   Tenha em atenção que, assim que tiver criado uma atribuição de iniciativa de uma definição de iniciativa, as novas definições de política adicionados à definição de iniciativa será automaticamente de agregação em assignment(s) a iniciativa em que a definição do iniciativa. No entanto, se existir um novo parâmetro introduzido para a nova definição de política, terá de atualizar a definição da iniciativa e atribuições para refletir esta ao editar a definição ou a atribuição.

## <a name="next-steps"></a>Passos seguintes:
Agora que tem uma descrição geral da política do Azure e alguns dos conceitos chaves que está a introduzir, Eis os passos sugeridos:

- [Atribuir uma definição de política](./assign-policy-definition.md)
- [Atribuir uma definição de política utilizando a CLI do Azure](./assign-policy-definition-cli.md)
- [Atribuir uma definição de política com o PowerShell](./assign-policy-definition-ps.md)
