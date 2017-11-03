---
title: "O aprovisionamento de utilizador de aplicação do SaaS no Azure AD automatizado | Microsoft Docs"
description: "Uma introdução à forma como pode utilizar o Azure AD para aprovisionar automaticamente, anular o aprovisionamento e atualizar continuamente as contas de utilizador entre várias aplicações de SaaS de terceiros."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 3fe57e9c22d04a3557978093ce3fe86613c5c1d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizar utilizador aprovisionamento e desaprovisionamento para aplicações de SaaS no Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>O que é o utilizador o aprovisionamento automatizado para aplicações SaaS?
Azure Active Directory (Azure AD) permite-lhe automatizar a criação, a manutenção e a remoção de identidades de utilizador na nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) aplicações como o Dropbox, Salesforce, ServiceNow e muito mais.

**Seguem-se alguns exemplos de que esta funcionalidade permite-lhe fazer:**

* Crie automaticamente novas contas nos sistemas direitos para pessoas novo quando que se associam a sua equipa ou organização.
* Desative automaticamente contas nos sistemas direita quando as pessoas deixam a equipa ou organização.
* Certifique-se de que as identidades nas suas aplicações e sistemas são mantidas atualizadas com base nas alterações no diretório ou o sistema de recursos humanos.
* Aprovisionar objetos de não utilizadores, tais como grupos, para aplicações que suportam-los.

**Aprovisionamento de utilizadores automatizada inclui também a funcionalidade seguinte:**

* A capacidade para fazer corresponder identidades existentes entre sistemas de origem e de destino.
* As opções de personalização a ajuda do Azure AD ajustam as configurações atuais das aplicações e sistemas que a sua organização está atualmente a utilizar.
* Alertas de e-mail opcional para o aprovisionamento de erros.
* Registos de atividade e relatórios para ajudar na monitorização e resolução de problemas.

## <a name="why-use-automated-provisioning"></a>Porquê utilizar o aprovisionamento automatizado?
Alguns motivações comuns para utilizar esta funcionalidade incluem:

* Para evitar os custos, inefficiencies e erro humano associado com processos de aprovisionamento manual.
* Para evitar os custos associados de alojamento e manter soluções de aprovisionamento personalizadas desenvolvidas e scripts
* Para proteger a sua organização removendo instantaneamente as identidades dos utilizadores de aplicações de SaaS chaves quando deixam a organização.
* Para importar facilmente um número em massa de utilizadores para uma determinada aplicação SaaS ou sistema.
* Para desfrutar a conveniência de ter a sua solução de aprovisionamento executar retire as mesmas políticas de acesso da aplicação que definiu para o Azure AD Single Sign-On.


## <a name="how-does-automatic-provisioning-work"></a>Como funciona o aprovisionamento automático?
    
O **serviço do Azure de aprovisionamento de AD** Aprovisiona utilizadores para aplicações SaaS e outros sistemas, através da ligação a pontos finais de API de gestão de utilizador fornecidos pelo fornecedor de cada aplicação. Estes pontos finais API de gestão de utilizador permite que o Azure AD para programaticamente criar, atualizar e remover utilizadores. Para aplicações selecionadas, que o serviço de aprovisionamento também pode criar, atualizar e remover objetos de relacionadas com identidade adicionais, tais como grupos e funções. 

![Aprovisionamento](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*figura 1: O Azure AD aprovisionamento do serviço*

![Aprovisionamento de saída](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*figura 2: fluxo de trabalho do Azure AD para aplicações de SaaS populares de aprovisionamento de utilizadores de "Saída"*

![Aprovisionamento de entrada](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*figura 3: fluxo de trabalho de aplicações de gestão de Capital humana (HCM) popular para o Azure Active Directory e o Windows Server Active Directory de aprovisionamento de utilizador de "Entrada"*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>As aplicações e sistemas posso utilizar com o aprovisionamento de utilizador automáticas do Azure AD?

Funcionalidades do Azure AD pré-integrado suporte para uma variedade de aplicações de SaaS populares e sistemas de recursos humanos, bem como suporte genérico para aplicações que implementam partes específicas do [SCIM 2.0 padrão](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-scim-provisioning).

Todas as aplicações "Em destaque" na Galeria de aplicações do Azure AD de suportar o aprovisionamento automatizado do utilizador. [A lista de aplicações em destaque pode ser visualizada aqui.](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

Por ordem para uma aplicação suportar o aprovisionamento de utilizadores automatizada, este deve primeiro fornecer o utilizador necessário pontos finais de gestão que permitem que programas externos automatizar a criação, a manutenção e a remoção de utilizadores. Por conseguinte, nem todas as aplicações de SaaS são compatíveis com esta funcionalidade. Para aplicações que suportam as APIs de gestão de utilizador, a equipa de engenharia do Azure AD, em seguida, será capaz de criar um conector aprovisionamento às aplicações e este trabalho é definido pelas necessidades atuais e potenciais clientes. 

Contactar o Azure AD engenharia equipa para pedir suporte aprovisionamento para aplicações adicionais, enviar uma mensagem através de [fórum de comentários do Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning). 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como configurar o aprovisionamento automático para uma aplicação?

Configuração do Azure AD para uma aplicação selecionada é iniciado no serviço de fornecimento de  **[portal do Azure](https://potal.azure.com)**. No **do Azure Active Directory > aplicações da empresa** secção, selecione **adicionar**, em seguida, **todos os**e, em seguida, adicione o seguinte dependendo do seu cenário:

* Todas as aplicações a **em destaque aplicações** aprovisionamento automático do secção suporte

* Utilize a opção "não Galeria a aplicação" para integrações de SCIM desenvolvidos personalizada

![Galeria](./media/active-directory-saas-app-provisioning/gallery.png)

No ecrã de gestão de aplicações, aprovisionamento está configurado no **aprovisionamento** separador.

![Definições](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **Credenciais de administrador** tem de ser fornecido para o Azure AD aprovisionamento do serviço que irão permiti-lhe estabelecer ligação com a API fornecida pela aplicação de gestão do utilizador.

* **Mapeamentos de atributos** podem ser configurados que especificam qual campos no sistema de origem (exemplo: do Azure AD) irão ter os respetivos conteúdos sincronizados com os campos no sistema de destino (exemplo: ServiceNow). Se a aplicação de destino suportar, esta secção permitirá opcionalmente, configure o aprovisionamento dos grupos para além das contas de utilizador. "Propriedades correspondentes" permitem-lhe para selecionar os campos são utilizados para corresponder a contas entre os sistemas. "[Expressões](active-directory-saas-writing-expressions-for-attribute-mappings.md)" permitem-lhe modificar e transformar os valores obtidos a partir do sistema de origem antes de estas são escritas para sistema de destino. Para obter mais informações, consulte [personalizar mapeamentos de atributos](active-directory-saas-customizing-attribute-mappings.md).

![Definições](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **Filtros de âmbito** informar o serviço de aprovisionamento que utilizadores e grupo no sistema de origem deve ser aprovisionado e/ou desaprovisionado ao sistema de destino. Existem dois aspetos a filtros são avaliados em conjunto de âmbito que determinem quem se encontra no âmbito de aprovisionamento:

* **Filtro de valores de atributo** -usando o menu "Âmbito de objeto de origem" nos mapeamentos de atributos permite a filtragem de valores de atributo específico. Por exemplo, pode especificar que apenas os utilizadores com um atributo de "Departamento" de "Vendas" devem estar no âmbito de aprovisionamento.

* **Filtro em atribuições de** -o menu de "Âmbito" o aprovisionamento > da secção definições do portal permite-lhe especificar se devem ser apenas "atribuídos" os utilizadores e grupos no âmbito de aprovisionamento ou se devem ser todos os utilizadores no diretório do Azure AD aprovisionado. Para obter informações sobre "atribuir" utilizadores e grupos, consulte [atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* **Definições** controlam o funcionamento do serviço aprovisionamento para uma aplicação, incluindo se está em execução ou não.

* **Registos de auditoria** fornecer registos de cada operação efetuada pelo Azure AD que o serviço de fornecimento. Para obter mais detalhes, consulte o [guia de relatórios aprovisionamento](active-directory-saas-provisioning-reporting.md).

![Definições](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>O que acontece durante o aprovisionamento?

1. Quando ativar o modo de aprovisionamento de uma aplicação pela primeira vez, são efetuadas as seguintes ações:
   * Azure AD irá tentar corresponder a quaisquer utilizadores existentes na aplicação SaaS com as respetivas identidades correspondentes no diretório. Quando um utilizador é correspondido, são *não* automaticamente ativado para o início de sessão único. Ordem de para um utilizador ter acesso à aplicação, estes devem ser explicitamente atribuídas para a aplicação no Azure AD, diretamente ou através de associação ao grupo.
   * Se já tiver especificado os utilizadores que devem ser atribuídos à aplicação e se não for possível encontrar contas existentes para os utilizadores do Azure AD, do Azure AD irá aprovisionar novas contas para os mesmos na aplicação.
2. Assim que a sincronização inicial foi concluída conforme descrito acima, o Azure AD irá verificar a cada 20 minutos para as seguintes alterações:
   * Se os novos utilizadores foram atribuídos à aplicação (diretamente ou através da associação de grupo), em seguida, terem sido aprovisionados com uma nova conta na aplicação SaaS.
   * Se o acesso de um utilizador foi removido, em seguida, a conta na aplicação SaaS está marcada como desativado (os utilizadores são nunca totalmente eliminados, que protege, de perda de dados em caso de uma configuração incorreta).
   * Se um utilizador recentemente foi atribuído à aplicação e já tinham uma conta na aplicação SaaS, essa conta está marcada como ativada e podem ser atualizadas algumas propriedades do utilizador, se estiverem Desatualizadas em comparação com o diretório.
   * Se tem sido alteradas informações de um utilizador (tais como o número de telefone, escritório) no diretório, em seguida, essas informações serão também atualizadas na aplicação SaaS.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Frequência do Azure AD escrever alterações de diretório para a aplicação de SaaS?**

Depois de uma sincronização completa inicial foi concluída, o Azure AD, normalmente, o serviço de fornecimento verifica a existência de alterações a cada 20 minutos. 

Se a aplicação de SaaS está a devolver vários erros (tal como no caso de credenciais de administrador inválido), em seguida, do Azure AD lenta gradualmente a frequência para até uma vez por dia até que os erros sejam corrigidos. Quando isto acontecer, a tarefa de aprovisionamento do Azure AD entra no estado "quarentena" e indica o resultado no [relatório de resumo de aprovisionamento](active-directory-saas-provisioning-reporting.md).

**Quanto tempo necessário para aprovisionar os meus utilizadores?**

Depois de uma sincronização completa inicial foi concluída, as alterações incrementais é normalmente ocorrem dentro de 40 de 20 minutos. Se estiver a tentar aprovisionar a maior parte do seu diretório, em seguida, depende do número de utilizadores e grupos que tiver. Desempenho está dependente de desempenho de utilizador APIs de gestão que utiliza os serviços de aprovisionamento para ler os dados do sistema de origem e escrever os dados de sistema de destino. 

Desempenho também é mais lento se existem erros de muitos (registadas no ficheiro de [registos de auditoria](active-directory-saas-provisioning-reporting.md)) e o serviço de aprovisionamento tornou-se num estado "quarentena".

**O que é uma sincronização completa inicial e, por que motivo é necessário mais de sincronizações subsequentes?**

Na primeira vez o Azure AD aprovisionamento do serviço é executado para uma determinada aplicação, cria um "instantâneo" dos utilizadores (e opcionalmente agrupa) no diretório de origem. Este instantâneo ativa o serviço de aprovisionamento reduzir o número de ida e volta para a origem e destino APIs e permite sincronizações de diferenças"subsequentes" comportar-se de forma mais eficiente. 

A sincronização inicial completa dos utilizadores, muitas vezes, pode ser concluída em minutos para diretórios muito pequenos, mas pode demorar várias horas para diretórios de maior. Diretórios de empresa com centenas de milhares de utilizadores podem demorar várias horas a sincronização inicial concluir. No entanto, após a sincronização inicial, as sincronizações de diferenças"subsequentes" acontecer muito mais rapidamente.

> [!NOTE]
> Para aplicações que suportam o aprovisionamento de grupos e associações a grupos, a ativação desta significativamente aumenta o tempo que demora para uma sincronização completa concluir. Se não pretender aprovisionar os nomes dos grupos e associações a grupos à sua aplicação, pode desativar esta no [mapeamentos de atributos](active-directory-saas-customizing-attribute-mappings.md) da sua configuração de aprovisionamento.

**Como posso acompanhar o progresso da tarefa de aprovisionamento atual?**

Consulte o [guia de relatórios aprovisionamento](active-directory-saas-provisioning-reporting.md).

**Como posso saber se os utilizadores não conseguem obter aprovisionada corretamente?**

Todas as falhas são registadas no Azure AD registos de auditoria. Para obter mais informações, consulte o [guia de relatórios aprovisionamento](active-directory-saas-provisioning-reporting.md).

**Como posso submeter comentários para a equipa de engenharia?**

Contacte-nos através do [fórum de comentários do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Artigos relacionados
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Personalizar os mapeamentos de atributos para o aprovisionamento de utilizador](active-directory-saas-customizing-attribute-mappings.md)
* [Escrever expressões para mapeamentos de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de âmbito para o aprovisionamento de utilizador](active-directory-saas-scoping-filters.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](active-directory-scim-provisioning.md)
* [Notificações de aprovisionamento de contas](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS](active-directory-saas-tutorial-list.md)

