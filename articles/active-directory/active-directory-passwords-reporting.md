---
title: "Relatórios: O Azure AD SSPR | Microsoft Docs"
description: "Eventos de reposição de relatórios na palavra-passe self-service do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 8599b843bb1d5692c15f9344d0c46940b7cd5a81
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opções de relatórios para gestão de palavras-passe do Azure AD

Após a implementação, muitas organizações querem saber como realmente está a ser utilizado ou se de reposição de palavra-passe self-service (SSPR). A funcionalidade de relatórios do Azure Active Directory (Azure AD) fornece ajuda a responder a perguntas utilizando prebuilt relatórios. Se estiver a adequadamente a licenciado, também pode criar consultas personalizadas.

![Relatórios][Reporting]

As perguntas seguintes podem ser respondidas pelos relatórios que existe no [portal do Azure] (https://portal.azure.com/):

> [!NOTE]
> Tem de ser [um administrador global](active-directory-assign-admin-roles-azure-portal.md), e tem optar ativamente por participar-para estes dados para ser reunidas em nome da sua organização. Para optar ativamente por participar no, tem de visitar o **relatórios** separador ou a auditoria regista, pelo menos, uma vez. Até lá, os dados não são recolhidos para a sua organização.
>

* Como muitas pessoas registou-se para a reposição de palavra-passe?
* Quem tiver registado para a reposição de palavra-passe?
* Os dados que são as pessoas a registar?
* Como muitas pessoas redefinir as palavras-passe nos últimos sete dias
* Quais são os métodos mais comuns que os utilizadores ou administradores utilizam para repor as palavras-passe?
* Quais são os utilizadores de problemas comuns ou administradores enfrentam ao tentar utilizar a reposição de palavra-passe?
* Os administradores estão a repor as suas próprias palavras-passe com frequência?
* Existe alguma atividade suspeita avançar com a reposição de palavra-passe?

## <a name="power-bi-content-pack"></a>Pacote de conteúdos do Power BI

Se a um utilizador do Power BI, há um pacote de conteúdos para o Azure AD que inclua reporting fáceis de utilizar para SSPR. Para obter mais informações sobre como utilizar e implementar o pacote de conteúdos, consulte [como utilizar o pacote de conteúdos do Azure Active Directory Power BI](active-directory-reporting-power-bi-content-pack-how-to.md). Com o pacote de conteúdos, pode criar os seus próprios dashboards e partilhá-los com outras pessoas na sua organização.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Como visualizar relatórios de gestão de palavra-passe no portal do Azure

A experiência de portal do Azure, podemos tem melhorado da forma que pode ver a reposição de palavra-passe e a atividade de registo de reposição de palavra-passe. Utilize os seguintes passos para localizar a palavra-passe de reposição e eventos de registo de reposição de palavra-passe:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no painel esquerdo.
3. Procurar **do Azure Active Directory** na lista de serviços e selecioná-lo.
4. Selecione **utilizadores e grupos**.
5. Selecione **registos de auditoria** do **utilizadores e grupos** menu. Isto mostra todos os eventos de auditoria que ocorreram em relação a todos os utilizadores no seu diretório. Pode filtrar esta vista para ver todos os eventos relacionados com a palavra-passe.
6. Para filtrar esta vista para ver apenas os eventos relacionados com reposição de palavra-passe, selecione o **filtro** botão na parte superior do painel.
7. Do **filtro** menu, selecione o **categoria** pendente listar e altere-o para o **gestão de palavras-passe Self-Service** tipo de categoria.
8. Opcionalmente, continuar a filtrar a lista ao escolher o específicos **atividade** estiver interessado em.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Como obter eventos de gestão de palavra-passe dos relatórios do Azure AD e eventos de API

Os relatórios do Azure AD e eventos API suporta a obtenção de todas as informações incluídas na reposição de palavra-passe e os relatórios de registo de reposição de palavra-passe. Ao utilizar esta API, pode transferir palavras-passe individuais reposição de palavra-passe de reposição do registo eventos e e integrá-las com a tecnologia de relatórios da sua preferência.

### <a name="how-to-get-started-with-the-reporting-api"></a>Como começar com a API de relatórios

Para aceder a estes dados, terá de escrever uma aplicação de pequena ou script para obtê-lo a partir dos nossos servidores. Para obter mais informações, consulte [começar com o Azure AD API do relatório](active-directory-reporting-api-getting-started.md).

Depois de ter um script de trabalho, poderá ser útil examinar os eventos de registo e reposição de palavra-passe que pode obter para satisfazer os seus cenários:

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): apresenta uma lista de colunas disponíveis para eventos de reposição de palavra-passe.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): apresenta uma lista de colunas disponíveis para eventos de registo de reposição de palavra-passe.

### <a name="reporting-api-data-retrieval-limitations"></a>Limitações de obtenção de dados do Reporting Services API

Atualmente, os relatórios do Azure AD e a API de eventos obtém até *75,000 eventos individuais* do [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) e [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) tipos. Os intervalos de API a *últimos 30 dias*.

Se precisar de obter ou armazenar os dados para além desta janela, sugerimos a persistência-la numa base de dados externo utilizando a API para as diferenças que resultam de consulta. Recomendamos que comece a obter estes dados, quando começar a utilizar SSPR na sua organização. Manter-externamente e, em seguida, continue a controlar as diferenças a partir desse ponto.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrição das colunas do relatório no portal do Azure

A lista seguinte explica cada uma das colunas de relatório no portal do Azure em detalhe:

* **Utilizador**: O utilizador de uma palavra-passe foi efetuada uma tentativa de reposição de operação de registo.
* **Função**: A função de utilizador no diretório.
* **Data e hora**: A data e hora da tentativa.
* **Dados registada**: os dados de autenticação que o utilizador fornecido durante a palavra-passe de registo de reposição.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrição dos valores de relatório no portal do Azure

A tabela seguinte descreve os diferentes valores que pode ser definido para cada coluna no portal do Azure:

| Coluna | Valores permitidos e os respetivos significados |
| --- | --- |
| Dados registados |**E-mail alternativo**: O utilizador utilizado um correio eletrónico alternativo ou o e-mail de autenticação para se autenticar.<p><p>**Telefone do escritório**: O utilizador utilizado um telefone do escritório para se autenticar.<p>**Telemóvel**: O utilizador utilizado telemóvel ou telefone de autenticação para se autenticar.<p>**Perguntas de segurança**: O utilizador utilizado perguntas de segurança para se autenticar.<p>**Qualquer combinação de métodos anteriores, por exemplo, alternativa e-mail + telemóvel**: ocorre quando uma política de porta de dois for especificada e mostra os dois métodos de utilizador utilizada para autenticação de pedido de reposição de palavra-passe. |

## <a name="self-service-password-management-activity-types"></a>Tipos de atividades de gestão de palavra-passe Self-Service

Os seguintes tipos de atividade são apresentados no **gestão de palavras-passe Self-Service** categoria de evento de auditoria:

* [Impedidos de reposição de palavra-passe self-service](#activity-type-blocked-from-self-service-password-reset): indica que um utilizador tentou repor uma palavra-passe, utilize uma porta específica ou validar um número de telefone mais de cinco vezes totais nas 24 horas.
* [Alterar palavra-passe (Self-Service)](#activity-type-change-password-self-service): indica que um utilizador efetuar um voluntário ou forçado (devido à expiração) Alterar palavra-passe.
* [Repor palavra-passe (pelo administrador)](#activity-type-reset-password-by-admin): indica que um administrador efetuar uma reposição em nome de um utilizador a partir do portal do Azure de palavra-passe.
* [Repor palavra-passe (Self-Service)](#activity-type-reset-password-self-service): indica que um utilizador reposto com êxito a palavra-passe do [portal de reposição de palavra-passe do Azure AD](https://passwordreset.microsoftonline.com).
* [Progresso de atividade de fluxo de reposição de palavra-passe self-service](#activity-type-self-serve-password-reset-flow-activity-progress): indica cada passo específico, um utilizador continua através de, tais como a transmissão de uma palavra-passe específica repor a porta de autenticação, como o processo de reposição da parte a palavra-passe.
* [Desbloquear a conta de utilizador (Self-Service)](#activity-type-unlock-user-account-self-service): indica que um utilizador desbloqueada com êxito, para a conta do Active Directory, sem uma reposição de palavra-passe do [portal de reposição de palavra-passe do Azure AD](https://passwordreset.microsoftonline.com) utilizando o Active Directory Funcionalidade de diretório de conta desbloquear sem reposição.
* [O utilizador registado para a reposição de palavra-passe self-service](#activity-type-user-registered-for-self-service-password-reset): indica que um utilizador registou as informações necessárias para repor a palavra-passe de acordo com a política de reposição de palavra-passe do inquilino atualmente especificado.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de atividade: impedidos de reposição de palavra-passe self-service

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade**: indica que um utilizador tentou repor uma palavra-passe, utilize uma porta específica ou validar um número de telefone mais de cinco vezes totais nas 24 horas.
* **Ator atividade**: operações de reposição de utilizador que foi limitado de efetuar adicionais. O utilizador pode ser um administrador ou de um utilizador final.
* **Destino de atividade**: operações de reposição de utilizador que foi limitado de efetuar adicionais. O utilizador pode ser um administrador ou de um utilizador final.
* **Estado da atividade**:
  * _Êxito_: indica que um utilizador estava limitado de efetuar qualquer reposições adicionais, tentar quaisquer métodos de autenticação adicional ou a validar os números de telefone adicionais para as próximas 24 horas.
* **Motivo de falha de estado de atividade**: não aplicável.

### <a name="activity-type-change-password-self-service"></a>Tipo de atividade: Alterar palavra-passe (Self-Service)

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade**: indica que um utilizador efetuar um voluntário ou forçado (devido à expiração) Alterar palavra-passe.
* **Ator atividade**: O utilizador alterar a palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Destino de atividade**: O utilizador alterar a palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Estados de atividade**:
  * _Êxito_: indica que um utilizador alterado com êxito a palavra-passe.
  * _Falha de_: indica que um utilizador não foi possível alterar a palavra-passe. Pode selecionar a linha para ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.
* **Motivo de falha de estado de atividade**: 
  * _FuzzyPolicyViolationInvalidPassword_: O utilizador selecionado uma palavra-passe que foi automaticamente banned porque as capacidades de deteção de palavra-passe Banned Microsoft encontrado que seja demasiado comum ou especialmente fracos.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: Repor palavra-passe (pelo administrador)

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade**: indica que um administrador efetuar uma reposição em nome de um utilizador a partir do portal do Azure de palavra-passe.
* **Ator atividade**: O administrador que efetuou a reposição em nome de outro utilizador final ou o administrador de palavra-passe. Tem de ser um administrador global, administrador de palavras-passe, o administrador de utilizador ou administrador de suporte técnico.
* **Destino de atividade**: O utilizador cuja palavras-passe foi reposta. O utilizador pode ser um utilizador final ou um administrador diferente.
* **Estados de atividade**:
  * _Êxito_: indica que um administrador com êxito de reposição de palavra-passe de um utilizador.
  * _Falha de_: indica que um administrador não foi possível alterar a palavra-passe de um utilizador. Pode selecionar a linha para ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: Repor palavra-passe (Self-Service)

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade**: indica que um utilizador reposto com êxito a palavra-passe do [portal de reposição de palavra-passe do Azure AD](https://passwordreset.microsoftonline.com).
* **Ator atividade**: O utilizador que reponha a palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Destino de atividade**: O utilizador que reponha a palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Estados de atividade**:
  * _Êxito_: indica que um utilizador reposto com êxito as suas próprias palavras-passe.
  * _Falha de_: indica que um utilizador não conseguiu repor os seus próprios palavra-passe. Pode selecionar a linha para ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.
* **Motivo de falha de estado de atividade**: 
  * _FuzzyPolicyViolationInvalidPassword_: O administrador selecionado uma palavra-passe que foi automaticamente banned porque as capacidades de deteção de palavra-passe Banned Microsoft encontrado que seja demasiado comum ou especialmente fracos.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: gestão personalizada de progresso de atividade de fluxo de reposição de palavra-passe

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade**: indica cada passo específico, um utilizador continua através de (tais como transmitir uma palavra-passe específica de reposição de porta de autenticação), como o processo de reposição da parte a palavra-passe.
* **Ator atividade**: fluxo de reposição de utilizador que efetuou a parte da palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Destino de atividade**: fluxo de reposição de utilizador que efetuou a parte da palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Estados de atividade**:
  * _Êxito_: indica que um utilizador concluída com êxito um passo específico do fluxo de reposição de palavra-passe.
  * _Falha de_: indica que um passo específico a palavra-passe repostos fluxo falhou. Pode selecionar a linha para ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.
* **Por motivos de estado de atividade**: consulte a tabela seguinte para [todos os reposição permitido atividade Estado motivos pelos quais](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo de atividade: desbloquear uma conta de utilizador (Self-Service)

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade**: indica que um utilizador desbloqueada com êxito, para a conta do Active Directory, sem uma reposição de palavra-passe do [portal de reposição de palavra-passe do Azure AD](https://passwordreset.microsoftonline.com) utilizando a funcionalidade do Active Directory de desbloqueio de conta sem reposição.
* **Ator atividade**: O utilizador que a conta foi desbloqueada sem uma reposição de palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Destino de atividade**: O utilizador que a conta foi desbloqueada sem uma reposição de palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Permitido Estados de atividade**:
  * _Êxito_: indica que um utilizador desbloqueada com êxito a sua própria conta.
  * _Falha de_: indica que um utilizador não conseguiu desbloquear a conta. Pode selecionar a linha para ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: utilizador registado para a reposição de palavra-passe self-service

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade**: indica que um utilizador registou as informações necessárias para repor a palavra-passe de acordo com a política de reposição de palavra-passe do inquilino atualmente especificado. 
* **Ator atividade**: O utilizador registado para a reposição de palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Destino de atividade**: O utilizador registado para a reposição de palavra-passe. O utilizador pode ser um administrador ou de um utilizador final.
* **Permitido Estados de atividade**:
  * _Êxito_: indica que um utilizador registado com êxito para a palavra-passe de reposição de acordo com a política atual. 
  * _Falha de_: indica que um utilizador não conseguiu registar para a reposição de palavra-passe. Pode selecionar a linha para ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu. 

     >[!NOTE]
     >Falha não significa um utilizador não é possível repor a sua própria palavra-passe. Significa não tenha a concluir o processo de registo. Se não houver não verificados dados na respetiva conta que está correta, tais como um número de telefone que não é validado, apesar de não verificou este número de telefone, ainda pode utilizá-lo para repor a palavra-passe. Para obter mais informações, consulte [o que acontece quando um utilizador se regista?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Reponha ou altere a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Reporting]: ./media/active-directory-passwords-reporting/sspr-reporting.png "Exemplo de auditoria de atividade SSPR inicia sessão no Azure AD"
