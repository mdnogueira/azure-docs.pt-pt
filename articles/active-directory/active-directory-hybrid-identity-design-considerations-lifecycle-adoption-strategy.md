---
title: "Azure Active Directory híbrida identidade considerações de design - determinar a estratégia de adoção de ciclo de vida de identidade híbrida | Microsoft Docs"
description: "Ajuda a definir as tarefas de gestão de identidade híbrida, de acordo com as opções disponíveis para cada fase do ciclo de vida."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 18b40486a66d8e092a8af299460145989a1ab99d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Determinar a estratégia de adoção de ciclo de vida de identidade híbrida
Nesta tarefa, vai definir a estratégia de gestão de identidade para a sua solução de identidade híbrida cumprir os requisitos de negócio que definiu no [determinar as tarefas de gestão de identidade híbrida](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Para definir as tarefas de gestão de identidade híbrida, de acordo com o ciclo de vida de identidade de ponto a ponto, apresentados anteriormente neste passo, terá de considerar as opções disponíveis para cada fase do ciclo de vida.

## <a name="access-management-and-provisioning"></a>Aprovisionamento e gestão de acesso
Com uma solução de gestão de acesso de conta boa, a organização pode controlar precisamente quem tem acesso às quais as informações em toda a organização.

Controlo de acesso é uma função crítica de um sistema de aprovisionamento centralizado e o ponto único. Para além de proteger informações confidenciais, controlos de acesso expõem contas existentes que tenham autorizações não aprovadas ou são já não é necessário. Para controlar as contas de obsoletas, as informações de conta em conjunto de ligações aprovisionamento sistema com autoritativas informações sobre os utilizadores que possuem as contas. Informações de identidade do utilizador autoritativo, normalmente, são mantidas na bases de dados e diretórios de recursos humanos.

Contas de empresas de TI sofisticadas incluem centenas de parâmetros que definem as autoridades e estes detalhes podem ser controlados pelo seu sistema de aprovisionamento. Os novos utilizadores podem ser identificados com os dados que fornecem a partir da origem de autoritativa. A capacidade de aprovação do pedido de acesso inicia processos que aprovar (ou rejeitar) recursos de aprovisionamento para os mesmos.

| Fase de gestão do ciclo de vida | No local | Nuvem | Híbrido |
| --- | --- | --- | --- |
| Aprovisionamento e gestão de contas |Ao utilizar a função de servidor Serviços de domínio do Active Directory® (AD DS), pode criar uma infraestrutura escalável, segura e gerível para a gestão de recursos e utilizadores e fornecer suporte para aplicações com diretório ativado como o Microsoft® Exchange Server. <br><br> [Pode aprovisionar grupos no AD DS através do Gestor de identidade](https://technet.microsoft.com/library/ff686261.aspx) <br>[Pode aprovisionar utilizadores no AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Os administradores podem utilizar o controlo de acesso para gerir o acesso de utilizador a recursos partilhados por motivos de segurança. No Active Directory, o controlo de acesso é administrado ao nível do objeto por definição diferentes níveis de acesso ou permissões, para objetos, tais como sem acesso, leitura, escrita ou controlo total. Controlo de acesso no Active Directory define como diferentes utilizadores pode utilizar os objetos do Active Directory. Por predefinição, as permissões em objetos no Active Directory estão definidas para a definição mais segura. |Tem de criar uma conta para todos os utilizadores que acedem um serviço em nuvem da Microsoft. Também pode alterar as contas de utilizador ou eliminá-las quando já não forem necessárias. Por predefinição, os utilizadores não têm permissões de administrador, mas pode optar por atribuir-lhes. Para obter mais informações, consulte [gerir os utilizadores no Azure AD](active-directory-create-users.md). <br><br> No Azure Active Directory, uma das principais funcionalidades é a capacidade para gerir o acesso aos recursos. Estes recursos podem fazer parte do diretório, como no caso de permissões para gerir objetos através de funções no diretório ou recursos que são externos ao diretório, tais como aplicações SaaS, serviços do Azure e sites do SharePoint ou recursos no local. <br><br> Acesso de center do Azure Active Directory da solução de gestão é o grupo de segurança. O proprietário do recurso (ou o administrador do diretório), pode atribuir um grupo para fornecer um determinado direitos de acesso aos recursos que possuem. Os membros do grupo serão fornecidos o acesso e o proprietário do recurso pode delegar o direito de gerir a lista de membros de um grupo para alguém – como um Gestor de departamento ou um administrador de suporte técnico<br> <br> Os grupos de gestão no tópico do Azure AD fornece mais informações sobre gerir o acesso através de grupos. |Expandir as identidades do Active Directory para a nuvem através de sincronização e a Federação |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções
Acesso baseado em funções (RBAC) utiliza funções de controlar e aprovisionar políticas para avaliar, testar e impor os processos empresariais e as regras para conceder acesso aos utilizadores. Os administradores de chaves criar políticas de aprovisionamento e atribuir utilizadores a funções e que define os conjuntos de elegibilidade para recursos para estas funções. RBAC expande a solução de gestão de identidade para utilizar processos baseada em software e reduzir intervenção manual do utilizador no processo de aprovisionamento.
RBAC do Azure AD permite à empresa a restringir a quantidade de operações que uma pessoa pode fazê-lo Depois do João tem acesso ao Portal de gestão do Azure. Ao utilizar o RBAC para controlar o acesso ao portal, administradores de TI AC delegar o acesso utilizando as seguintes abordagens de gestão de acesso:

* **Atribuição de função com base no grupo**: pode atribuir acesso a grupos do Azure AD que podem ser sincronizados do Active Directory local. Isto permite-lhe tirar partido de investimentos existentes que sua organização tem efetuadas nas ferramentas e processos para gerir grupos. Também pode utilizar a funcionalidade de gestão de grupo delegada do Azure AD Premium.
* **Tire partido funções no Azure incorporado**: pode utilizar três funções — proprietário, Contribuidor, leitor e, para garantir que os utilizadores e grupos têm permissão para efetuar apenas as tarefas que precisam para as respetivas tarefas.
* **Granular acesso aos recursos**: pode atribuir funções de utilizadores e grupos para uma determinada subscrição, grupo de recursos ou um recurso do Azure individuais como um Web site ou a base de dados. Desta forma, pode certificar-se de que os utilizadores têm acesso a todos os recursos que precisam e sem acesso a recursos que não precisa de gerir.

## <a name="provisioning-and-other-customization-options"></a>Aprovisionamento e outras opções de personalização
A equipa pode utilizar os planos de negócios e os requisitos para decidir quanto personalizar a solução de identidade. Por exemplo, uma grande empresa poderão ser necessários um plano de agregação Escalamento faseado para fluxos de trabalho e personalizadas adaptadores que é baseada numa linha de tempo para o aprovisionamento de forma incremental a aplicações que são utilizadas em grande escala em localizações geográficas. Pode fornecer outro plano de personalização para dois ou mais aplicações a ser aprovisionado numa organização inteira, depois de testar com êxito. Interação de aplicação do utilizador pode ser personalizada e procedimentos para o aprovisionamento de recursos podem ser alterados para acomodar o aprovisionamento automatizado.

Pode desaprovisionar para remover um componente ou serviço. Por exemplo, o desaprovisionamento uma conta significa que a conta foi eliminada de um recurso.

O modelo de híbrida de aprovisionamento de recursos combina pedido e abordagens baseada em funções, que são ambas suportadas pelo Azure AD. Para um subconjunto dos funcionários ou sistemas geridos, uma empresa pode pretender automatizar o acesso com a atribuição baseada em funções. Uma empresa também pode processar todos os outros pedidos de acesso ou exceções através de um modelo baseado em pedidos. Algumas empresas podem começar com atribuição manual e evoluir na direção de um modelo de híbrida, com uma intenção de uma implementação totalmente baseada em funções num momento futuro.

Outras empresas poderão encontrar impractical por razões de negócio alcançar completa baseada em funções de aprovisionamento e uma abordagem de híbrida como um objetivo reclamado de destino. Ainda outras empresas poderão ser satisfeito com apenas baseado em pedidos de aprovisionamento e não quer investir esforço adicional para definir e gerir políticas de aprovisionamento baseado em funções, automatizadas.

## <a name="license-management"></a>Gestão de licenças
Gestão de baseado no grupo de licenças no Azure AD permite aos administradores atribuir utilizadores a um grupo de segurança e AD do Azure atribui automaticamente licenças para todos os membros do grupo. Se um utilizador é, subsequentemente, adicionado ou removido do grupo, uma licença será automaticamente atribuída ou removida conforme adequada.

Pode utilizar grupos de sincronizar a partir do AD no local ou gerir no Azure AD. O emparelhamento esta cópia de segurança com n Self-Service de grupo de gestão do Azure AD premium pode delegar facilmente atribuição de licenças para os decisores adequado. Pode ter a certeza que problemas, como os conflitos de licença e dados de localização em falta são automaticamente ordenados.

## <a name="self-regulating-user-administration"></a>Administração de utilizador Self-regulating
Quando inicia a sua organização aprovisionar os recursos em todas as organizações internas, implementar a capacidade de administração de utilizador Self-regulating. Pode realizar as vantagens e as vantagens de aprovisionamento de utilizadores entre limites organizacionais. Neste ambiente, uma alteração de estado do utilizador é refletida automaticamente no direitos de acesso através de limites da organização e localizações geográficas. Pode reduzir os custos de aprovisionamento e simplificar os processos de acesso e aprovação. A implementação realiza o potencial de implementação de controlo de acesso baseado em funções para a gestão de acesso de ponto a ponto na sua organização. Pode reduzir os custos administrativos através de procedimentos automatizados para que rege o aprovisionamento de utilizadores. Pode melhorar a segurança ao automatizar a imposição de políticas de segurança e simplificar e centralizar a gestão de ciclo de vida de utilizador e aprovisionamento de recursos para populações de grandes dimensões de utilizador.

> [!NOTE]
> Para obter mais informações, consulte Configurar o Azure AD para gestão de acesso de aplicação de self-service
> 
> 

Baseada no licenciamento (baseado em elegibilidade) do Azure AD serviços trabalho por ativar uma subscrição no seu inquilino do Azure AD/serviço de diretório. Depois da subscrição está ativa as capacidades de serviço podem ser geridas pelos administradores do serviço de diretório/e utilizadas pelos utilizadores licenciados. Para obter mais informações, consulte como funciona o Azure AD licenciamento trabalho?
Integração com outros fornecedores 3rd

Do Azure Active Directory fornece o início de sessão único em e modulação de segurança de acesso de aplicação para milhares de aplicações SaaS e aplicações de web no local. Para obter uma lista detalhada de Galeria de aplicações do Azure Active Directory para aplicações SaaS suportadas, consulte a lista de compatibilidades de Federação do Azure Active Directory: fornecedores de identidade de terceiros que podem ser utilizados para implementar o início de sessão único

## <a name="define-synchronization-management"></a>Definir a gestão de sincronização
A integração dos diretórios no local com o Azure AD torna os utilizadores mais produtivos, ao fornecer-lhes uma identidade comum para acederem a recursos na nuvem e no local. Esta integração utilizadores e organizações podem beneficiar das seguintes opções:

* As organizações podem fornecer aos utilizadores com uma identidade híbrida comum no local ou serviços baseados na nuvem tirar partido do Windows Server Active Directory e, em seguida, ligar ao Azure Active Directory.
* Os administradores podem fornecer o acesso condicional com base nos recursos de aplicação, dispositivo e a identidade do utilizador, localização de rede e a autenticação multifator.
* Os utilizadores podem tirar partido as respetivas identidades comuns através de contas no Azure AD para o Office 365, o Intune, aplicações SaaS e aplicações de terceiros.
* Os programadores podem criar aplicações que tiram partido do modelo de identidade comum integrar aplicações no Active Directory no local ou do Azure para aplicações baseadas na nuvem

A figura seguinte tem um exemplo de uma vista detalhada do processo de sincronização de identidade.

![](./media/hybrid-id-design-considerations/identitysync.png)

Processo de sincronização de identidade

Reveja a tabela seguinte para comparar as opções de sincronização:

| Opção de gestão de sincronização | Vantagens | Desvantagens |
| --- | --- | --- |
| Baseadas na sincronização (através de DirSync ou AADConnect) |Utilizadores e grupos sincronizados a partir da nuvem e no local <br>  **Controlo de política**: políticas de conta podem ser definidas através do Active Directory, o que permite ao administrador para gerir políticas de palavra-passe, estação de trabalho, restrições, os controlos de limite de bloqueio e mais, sem ter de executar tarefas adicionais na nuvem.  <br>  **Controlo de acesso**: pode restringir o acesso ao serviço em nuvem, para que os serviços podem ser acedidos através do ambiente empresarial, através de servidores online ou por ambos. <br>  Reduzido chamadas de suporte: se os utilizadores tiverem menos palavras-passe lembrar-se, são menos provável que se esqueça-los. <br>  Segurança: Identidades de utilizador e as informações estão protegidos porque todos os servidores e serviços utilizados no início de sessão, controladas e controlado no local. <br>  Suporte para autenticação forte: pode utilizar a autenticação forte (também denominada de autenticação de dois fatores) com o serviço em nuvem. No entanto, se utilizar autenticação forte, tem de utilizar o início de sessão único. | |
| Baseada em Federação (através do AD FS) |Ativado por serviço de tokens de segurança (STS). Quando configurar um STS para fornecer acesso de início de sessão único com um serviço em nuvem da Microsoft, que vai criar uma confiança federada entre os STS no local e o domínio federado especificadas no seu inquilino do Azure AD. <br> Permite aos utilizadores finais utilizam o mesmo conjunto de credenciais para obter acesso a vários recursos <br>os utilizadores finais não é necessário que manter a vários conjuntos de credenciais. Ainda, os utilizadores têm de fornecer as credenciais para cada um dos participantes recursos., cenários B2B e B2C suportados. |Requer técnico especializados para implementação e manutenção de dedicado no local servidores AD FS. Existem restrições sobre a utilização de autenticação forte, se planear utilizar o AD FS para os STS. Para obter mais informações, consulte [configurar opções avançadas para o AD FS 2.0](http://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Para obter mais informações, consulte [integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
> 
> 

## <a name="see-also"></a>Veja Também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

