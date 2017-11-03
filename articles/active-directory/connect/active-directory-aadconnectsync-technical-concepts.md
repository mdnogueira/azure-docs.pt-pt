---
title: "Sincronização do Azure AD Connect: conceitos técnicos | Microsoft Docs"
description: "Explica os conceitos técnicos de sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi;andkjell
ms.openlocfilehash: e178c5fa453c753aabdc38449273f46a90e1194a
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Sincronização do Azure AD Connect: Conceitos Técnicos
Este artigo é um resumo do tópico [arquitetura de compreender](active-directory-aadconnectsync-technical-concepts.md).

Sincronização do Azure AD Connect baseia-se na plataforma de sincronização de metadiretório sólida.
As secções seguintes apresentam os conceitos para a sincronização de metadiretório.
Criar após MIIS, ILM e FIM, os serviços de sincronização do Active Directory do Azure fornece a plataforma seguinte para ligar a origens de dados, a sincronização dos dados entre origens de dados, bem como o aprovisionamento e desaprovisionamento das identidades.

![Conceitos Técnicos](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

As secções seguintes fornecem mais detalhes sobre os seguintes aspetos do serviço de sincronização do FIM:

* conector
* Fluxo de atributos
* Espaço de conector
* Metaverso
* Aprovisionamento

## <a name="connector"></a>conector
Os módulos de código que são utilizados para comunicar com um diretório ligado são denominados conectores (anteriormente conhecidos como agentes de gestão (MAs)).

Estes estão instalados no computador que executa a sincronização do Azure AD Connect. Os conectores permitem sem agente converse ao utilizar protocolos de sistema remoto em vez de depender da implementação de agentes especializados. Isto significa menor risco e tempos de implementação, especialmente quando lidar com sistemas e aplicações críticas.

Na imagem acima, o conector é synonymous com o espaço de conector, mas abrange todas as comunicações com o sistema externo.

O conector é responsável por todos os importar e exportar funcionalidade para o sistema e liberta os programadores necessidade de compreender como ligar a cada sistema nativamente ao utilizar o aprovisionamento declarativo para personalizar transformações de dados.

Importa e exporta apenas ocorre quando agendada, permitindo insulation adicional de alterações a ocorrerem no sistema, uma vez que as alterações não automaticamente propagadas a origem de dados ligada. Além disso, os programadores também podem criar os seus próprios conectores para ligar à praticamente qualquer origem de dados.

## <a name="attribute-flow"></a>Fluxo de atributos
Metaverso é a vista consolidada de todas as identidades associadas de circundantes espaços conectores. A figura acima, o fluxo de atributos é descrito por linhas com arrowheads do fluxo de entrada e saída. Fluxo de atributos é o processo de cópia ou transformar dados a partir de um sistema para outro e todos os fluxos (entrados ou saídos) de atributos.

Fluxo de atributos ocorre entre o metaverso e o espaço de conector bidirecional quando as operações de (completo ou delta) de sincronização agendadas para execução.

Fluxo de atributos ocorre apenas quando estes sincronizações são executadas. Fluxos de atributos são definidos nas regras de sincronização. Estes podem ser (ISR na imagem acima) entrada ou saída (OSR na imagem acima).

## <a name="connected-system"></a>Sistema ligado
Sistema ligado (também conhecido como diretório ligado) é referir-se ao sistema remoto do Azure AD Connect, sincronização foi ligado ao ler e escrever dados de identidade e de.

## <a name="connector-space"></a>Espaço de conector
Cada origem de dados ligada é representada como um subconjunto filtrado dos objetos e atributos no espaço de conector.
Isto permite que o serviço de sincronização operar localmente, sem a necessidade de contactar o sistema remoto ao sincronizar os objetos e restringe a interação para importações e exporta apenas.

Quando a origem de dados e o conector têm a capacidade para fornecer uma lista de alterações (uma importação delta), em seguida, a eficiência operacional aumenta significativamente como apenas as alterações desde o último ciclo de consulta são trocados. O espaço de conector insulates da origem de dados ligada de alterações propagar automaticamente exigindo que a agenda de conector importa e exporta. Este insurance adicionado concede tranquilidade durante o teste, pré-visualização ou confirmar a atualização seguinte.

## <a name="metaverse"></a>Metaverso
Metaverso é a vista consolidada de todas as identidades associadas de circundantes espaços conectores.

Como identidades ligadas em conjunto e autoridade é atribuída para vários atributos através de mapeamentos de fluxo de importação, o objeto de metaverso central começa a informações de agregação de vários sistemas. Deste fluxo de atributos de objeto, mapeamentos transportem as informações para sistemas de saída.

Objetos são criados quando um sistema autoritativo projetos-los para o metaverso. Assim que todas as ligações são removidas, o objeto de metaverso é eliminado.

Não não possível editar diretamente a objetos no metaverso. Todos os dados no objeto tem de ser contribuíram através do fluxo de atributos. Metaverso mantém persistentes conectores com cada espaço de conector. Estes conectores não necessitam de reavaliação para cada sincronização executar. Isto significa que sincronização do Azure AD Connect não tem de localizar o objeto remoto correspondente cada vez. Isto evita a necessidade de agentes dispendiosas impedir que as alterações aos atributos que normalmente seriam responsáveis por correlacionar os objetos.

Quando detetar origens de dados nova, que poderão ter pré-existentes objetos que necessitam de ser geridos, sincronização do Azure AD Connect utiliza um processo denominado uma regra de associação para avaliar os possíveis candidatos com a qual estabelecer uma ligação.
Depois da ligação for estabelecida, esta avaliação não acionador e fluxo de atributos normal pode ocorrer entre a origem de dados ligada remoto e o metaverso.

## <a name="provisioning"></a>Aprovisionamento
Quando um projetos de origem autoritária pode ser criado um novo objeto para o metaverso um novo objeto de espaço de conector no outro conector que representa uma origem de dados ligada a jusante.

Isto inerentemente estabelece uma ligação e fluxo de atributos para poder continuar bidirecional.

Sempre que uma regra determina se um novo objeto de espaço de conector tem de ser criada, é chamado de aprovisionamento. No entanto, porque esta operação só ocorre no espaço de conector, este não passa para a origem de dados ligada até uma exportação é efetuada.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização do Azure AD Connect: Personalizar as opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
