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
ms.openlocfilehash: 82b1916a49cd1e04580fdb848545ce7d52299579
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opções de relatórios para gestão de palavras-passe do Azure AD

Após a implementação de muitas organizações querem saber como ou se SSPR está realmente a ser utilizado. O Azure AD fornece funcionalidades de relatórios que ajudam-na responder a perguntas utilizando relatórios encontra e se o é licenciados corretamente, permitem-lhe criar consultas personalizadas.

![Relatórios][Reporting]

As perguntas seguintes podem ser respondidas pelos relatórios existem no [portal do Azure] (https://portal.azure.com/).

> [!NOTE]
> Tem de ser [um administrador global](active-directory-assign-admin-roles.md) e tem optar ativamente por participar-para estes dados para ser reunidas em nome da sua organização, visitando os registos de separador ou auditoria Reporting Services, pelo menos, uma vez. Até ao fazê-lo, não serão possível recolher dados para a sua organização

* Como muitas pessoas registou-se para a reposição de palavra-passe?
* Quem tiver registado para a reposição de palavra-passe?
* Os dados que são as pessoas a registar?
* Como muitas pessoas redefinir as palavras-passe nos últimos sete dias
* Quais são os utilizadores de métodos mais comuns ou administradores utilizam para repor as palavras-passe?
* Quais são os utilizadores de problemas comuns ou administradores enfrentam ao tentar utilizar a reposição de palavra-passe?
* Os administradores estão a repor as suas próprias palavras-passe com frequência?
* Existe alguma atividade suspeita avançar com a reposição de palavra-passe?

## <a name="power-bi-content-pack"></a>Pacote de conteúdos do Power BI

Se for um utilizador do Power BI é um pacote de conteúdos para o Azure AD que inclua reporting fáceis de utilizar para SSPR. Encontrar mais informações sobre como utilizar e implementar o pacote de conteúdos no artigo [como utilizar o Azure Active Directory Power BI conteúdo pacote](active-directory-reporting-power-bi-content-pack-how-to.md). Em seguida, pode criar os seus próprios dashboards e partilhá-los com outras pessoas na sua organização.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Como visualizar relatórios de gestão de palavra-passe no portal do Azure

A experiência do portal do Azure, temos uma forma melhorada para ver a reposição de palavra-passe e a atividade de registo de reposição de palavra-passe.  Siga os passos abaixo para encontrar que eventos de registo de reposição de reposição de palavra-passe e a palavra-passe:

1. Navegue para [ **portal.azure.com**](https://portal.azure.com)
2. Clique em de **mais serviços** menu o principal navegação esquerda portal do Azure
3. Procurar **do Azure Active Directory** na lista de serviços e selecione-
4. Clique em **utilizadores e grupos** no menu de navegação do Azure Active Directory
5. Clique em de **registos de auditoria** item de navegação no menu de navegação de utilizadores e grupos. Isto mostra todos os eventos de auditoria ocorrer em relação a todos os utilizadores no seu diretório. Pode filtrar esta vista para ver todos os relacionados com a palavra-passe eventos, também.
6. Para filtrar esta vista para apenas eventos relacionados de repor a palavra-passe, clique em de **filtro** botão na parte superior do painel.
7. Do **filtro** menu, selecione o **categoria** lista pendente e altere-o para o **gestão de palavras-passe Self-Service** tipo de categoria.
8. Filtro adicional, opcionalmente, a lista ao escolher o específicos **atividade** que está interessado

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Como obter eventos de gestão de palavra-passe dos relatórios do Azure AD e eventos de API

Os relatórios do Azure AD e eventos API suporta todas as informações de incluídos na palavra-passe reposição de palavra-passe de reposição do registo relatórios e a obter. Ao utilizar esta API, pode transferir a reposição de palavra-passe individuais e eventos de registo para a integração com a tecnologia de relatórios da sua preferência de reposição de palavra-passe.

### <a name="how-to-get-started-with-the-reporting-api"></a>Como começar com a API de relatórios

Para aceder a estes dados, terá de escrever uma pequena aplicação ou script para obtê-lo a partir dos nossos servidores. [Saiba como obter uma introdução do Azure AD API do relatório](active-directory-reporting-api-getting-started.md).

Depois de ter um script de trabalho, poderá ser útil seguinte examinar os eventos de registo e reposição de palavra-passe que pode obter para satisfazer os seus cenários.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): apresenta uma lista de colunas disponíveis para eventos de reposição de palavra-passe
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): apresenta uma lista de colunas disponíveis para eventos de registo de reposição de palavra-passe

### <a name="reporting-api-data-retrieval-limitations"></a>Limitações de obtenção de dados do Reporting Services API

Atualmente, os relatórios do Azure AD e a API de eventos obtém até **75,000 eventos individuais** do [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) e [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) tipos, a expansão de **últimos 30 dias**.

Se precisar de obter ou armazenar os dados para além desta janela, sugerimos a persistência-la numa base de dados externa e utilizando a API para as diferenças que resultam de consulta. É a nossa recomendação para começar a obter estes dados, quando começar a utilizar SSPR na sua organização, manter-externamente e, em seguida, continue a controlar as diferenças a partir deste ponto.

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>Como transferir eventos de registo de reposição de palavra-passe rapidamente com o PowerShell

Além de utilizar os relatórios do Azure AD e a API de eventos diretamente, também pode utilizar o script do PowerShell para eventos de registo recentes no seu diretório abaixo. Isto é útil em caso de pretender ver quem registou recentemente ou gostaria de se certificar de que a sua implementação de reposição de palavra-passe é ocorrer como esperado.

* [Script do PowerShell do Azure AD SSPR registo actividade](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

### <a name="description-of-report-columns-in-azure-portal"></a>Descrição das colunas de relatório no portal do Azure

A lista seguinte explica cada uma das colunas de relatório em detalhe:

* **Utilizador** – o utilizador de uma palavra-passe foi efetuada uma tentativa de reposição de operação de registo.
* **Função** – a função de utilizador no diretório.
* **Data e hora** – a data e hora da tentativa.
* **Dados registada** – registo de reposição dos dados de autenticação que o utilizador fornecido durante a palavra-passe.

### <a name="description-of-report-values-in-azure-portal"></a>Descrição de valores de relatório no portal do Azure

A tabela seguinte descreve os diferentes valores permitidos para cada coluna:

| Coluna | Valores permitidos e os respetivos significados |
| --- | --- |
| Dados registados |**E-mail alternativo** – utilizador utilizado correio eletrónico alternativo de e-mail ou a autenticação para autenticar<p><p>**Telefone do escritório**– telefone do escritório de utilizador utilizado para autenticar<p>**Telemóvel** -telemóvel do utilizador utilizado ou telefone de autenticação para autenticar<p>**Perguntas de segurança** – utilizador utilizado perguntas de segurança para autenticação<p>**Qualquer combinação de acima (por exemplo, correio eletrónico alternativo + telemóvel)** – ocorre quando uma política de 2 porta for especificada e mostra os dois métodos de utilizador utilizada para autenticação de pedido de reposição de palavra-passe. |

## <a name="self-service-password-management-activity-types"></a>Tipos de atividades de gestão de palavra-passe self-service

Os seguintes tipos de atividade são apresentados no **gestão de palavras-passe Self-Service** categoria de evento de auditoria.  Uma descrição para cada uma forma.

* [**Impedidos de reposição de palavra-passe self-service** ](#activity-type-blocked-from-self-service-password-reset) -indica um utilizador tentou repor uma palavra-passe, utilize uma porta específica ou validar um número de telefone mais de 5 vezes totais nas 24 horas.
* [**Alterar palavra-passe (Self-Service)** ](#activity-type-change-password-self-service) -indica um utilizador efetuar um voluntário ou forçado (devido à expiração) Alterar palavra-passe.
* [**Repor palavra-passe (pelo administrador)** ](#activity-type-reset-password-by-admin) -indica um administrador efetuar uma reposição em nome de um utilizador a partir do portal do Azure de palavra-passe.
* [**Repor palavra-passe (Self-Service)** ](#activity-type-reset-password-self-service) -indica a palavra-passe de reposto com êxito de um utilizador a [Portal de reposição de palavra-passe do Azure AD](https://passwordreset.microsoftonline.com).
* [**Progresso de atividade de fluxo de reposição de palavra-passe self-service** ](#activity-type-self-serve-password-reset-flow-activity-progress) -indica a cada passo específico, um utilizador continua através de (tais como transmitir uma palavra-passe específica de reposição de porta de autenticação), o processo de reposição da parte a palavra-passe.
* [**Desbloquear a conta de utilizador (Self-Service)** ](#activity-type-unlock-user-account-self-service) -indica um utilizador desbloqueada com êxito a respetiva conta do Active Directory sem uma reposição de palavra-passe do [Portal de reposição de palavra-passe do Azure AD](https://passwordreset.microsoftonline.com) através do AD desbloqueio de conta sem a funcionalidade de reposição.
* [**O utilizador registado para a reposição de palavra-passe self-service** ](#activity-type-user-registered-for-self-service-password-reset) -indica um utilizador registou as informações necessárias para repor a palavra-passe de acordo com a política de reposição de palavra-passe do inquilino atualmente especificado.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de atividade: impedidos de reposição de palavra-passe self-service

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade** – indica que um utilizador tentou repor uma palavra-passe, utilize uma porta específica ou validar um número de telefone mais de 5 vezes totais nas 24 horas.
* **Atividade Ator** -operações de reposição de utilizador que foi limitado de efetuar adicionais. Pode ser um administrador ou de um utilizador final.
* **Destino de atividade** -operações de reposição de utilizador que foi limitado de efetuar adicionais. Pode ser um administrador ou de um utilizador final.
* **Estados de atividade permitido**
  * _Êxito_ -indica que foi limitado um utilizador de efetuar qualquer reposições adicionais, a tentativa de quaisquer métodos de autenticação adicional, ou a validar os números de telefone adicionais para as próximas 24 horas.
* **Motivo de falha de estado de atividade** - não aplicável

### <a name="activity-type-change-password-self-service"></a>Tipo de atividade: Alterar palavra-passe (Self-Service)

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade** – indica um utilizador efetuar um voluntário ou forçado (devido à expiração) Alterar palavra-passe.
* **Atividade Ator** -o utilizador alterar a palavra-passe. Pode ser um administrador ou de um utilizador final.
* **Destino de atividade** -o utilizador alterar a palavra-passe. Pode ser um administrador ou de um utilizador final.
* **Estados de atividade permitido**
  * _Êxito_ -indica um utilizador foi alterado com êxito a palavra-passe
  * _Falha de_ -indica a falha de um utilizador ao alterar a palavra-passe. Ao clicar em linha permite-lhe ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.
* **Motivo de falha de estado de atividade** - 
  * _FuzzyPolicyViolationInvalidPassword_ -o utilizador selecionado uma palavra-passe que foi automaticamente banned devido a Banned palavra-passe as da Microsoft capacidades de deteção encontrá-las para ser demasiado comum ou especialmente fracos.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: Repor palavra-passe (pelo administrador)

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade** – indica um administrador efetuar uma reposição em nome de um utilizador a partir do portal do Azure de palavra-passe.
* **Atividade Ator** -o administrador que efetuou a reposição em nome de outro utilizador final ou o administrador de palavra-passe. Tem de ser um administrador global, administrador de palavras-passe, o administrador de utilizador ou administrador de suporte técnico.
* **Destino de atividade** -o utilizador cuja palavras-passe foi reposta. Pode ser um utilizador final ou um administrador diferente.
* **Estados de atividade permitido**
  * _Êxito_ -indica um administrador com êxito de reposição de palavra-passe de um utilizador
  * _Falha de_ -indica a falha de um administrador alterar a palavra-passe de um utilizador. Ao clicar em linha permite-lhe ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: Repor palavra-passe (Self-Service)

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade** – indica que um utilizador reposto com êxito a palavra-passe do [Portal de reposição de palavra-passe do Azure AD](https://passwordreset.microsoftonline.com).
* **Atividade Ator** -o utilizador que reponha a palavra-passe. Pode ser um administrador ou de um utilizador final.
* **Destino de atividade** -o utilizador que reponha a palavra-passe. Pode ser um administrador ou de um utilizador final.
* **Estados de atividade permitido**
  * _Êxito_ -indica um utilizador reposto com êxito as suas próprias palavras-passe
  * _Falha de_ -indica uma falha ao repor a sua própria palavra-passe. Ao clicar em linha permite-lhe ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.
* **Motivo de falha de estado de atividade** -
  * _FuzzyPolicyViolationInvalidPassword_ -o administrador selecionado uma palavra-passe que foi automaticamente banned devido a Banned palavra-passe as da Microsoft capacidades de deteção encontrá-las para ser demasiado comum ou especialmente fracos.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: gestão personalizada de progresso de atividade de fluxo de reposição de palavra-passe

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade** – indica cada passo específico, um utilizador continua através de (tais como transmitir uma palavra-passe específica de reposição de porta de autenticação), o processo de reposição da parte a palavra-passe.
* **Atividade Ator** -o utilizador que efetuou a parte da palavra-passe repor fluxo. Pode ser um administrador ou de um utilizador final.
* **Destino de atividade** -o utilizador que efetuou a parte da palavra-passe repor fluxo. Pode ser um administrador ou de um utilizador final.
* **Estados de atividade permitido**
  * _Êxito_ -indica um utilizador concluir com êxito um passo específico do fluxo de reposição de palavra-passe.
  * _Falha de_ -indica a falha de fluxo de reposição de um passo específico a palavra-passe. Ao clicar em linha permite-lhe ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.
* **Permitido por motivos de estado de atividade**
  * Consulte a tabela abaixo para [todas permitido atividade de reposição de razões de estado](#allowed-values-for-details-column)

### <a name="activity-type-unlock-user-account-self-service"></a>Tipo de atividade: desbloquear a conta de utilizador (Self-Service)

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade** – indica que um utilizador desbloqueada com êxito a respetiva conta do Active Directory sem uma reposição de palavra-passe do [Portal de reposição de palavra-passe do Azure AD](https://passwordreset.microsoftonline.com) utilizando a conta do AD desbloquear sem reposição funcionalidade.
* **Atividade Ator** -o utilizador que a conta foi desbloqueada sem uma reposição de palavra-passe. Pode ser um administrador ou de um utilizador final.
* **Destino de atividade** -o utilizador que a conta foi desbloqueada sem uma reposição de palavra-passe. Pode ser um administrador ou de um utilizador final.
* **Estados de atividade permitido**
  * _Êxito_ -indica um utilizador sua própria conta foi desbloqueada com êxito
  * _Falha de_ -indica uma falha ao desbloquear a conta. Ao clicar em linha permite-lhe ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: utilizador registado para a reposição de palavra-passe self-service

A lista seguinte explica esta atividade detalhadamente:

* **Descrição de atividade** – indica um utilizador registou as informações necessárias para repor a palavra-passe de acordo com a política de reposição de palavra-passe do inquilino atualmente especificado. 
* **Atividade Ator** -o utilizador registado para a reposição de palavra-passe. Pode ser um administrador ou de um utilizador final.
* **Destino de atividade** -o utilizador registado para a reposição de palavra-passe. Pode ser um administrador ou de um utilizador final.
* **Estados de atividade permitido**
  * _Êxito_ -indica um utilizador registado com êxito a palavra-passe de reposição de acordo com a política atual. 
  * _Falha de_ -indica uma falha do registo para reposição de palavra-passe. Ao clicar em linha permite-lhe ver o **razão do Estado de atividade** categoria para saber mais sobre o motivo pelo qual a falha ocorreu. Tenha em atenção - isto não significa que um utilizador não é possível repor a sua própria palavra-passe, apenas que não foi possível concluir o processo de registo. Se não houver não verificados dados na respetiva conta que está correta (por exemplo, um número de telefone que não é validado), apesar de não poderem tem verificado este número de telefone, pode ainda utilizam-para repor a palavra-passe. Para obter mais informações, consulte [o que acontece quando um utilizador se regista?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)

## <a name="next-steps"></a>Passos seguintes

* [Como concluir a uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [O registo para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md).
* [Tem uma pergunta de licenciamento?](active-directory-passwords-licensing.md)
* [Os dados que são utilizados pelo SSPR e os dados que deve preencher para os seus utilizadores?](active-directory-passwords-data.md)
* [Os métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que motivo importantes acerca do mesmo?](active-directory-passwords-writeback.md)
* [Quais são todas as opções na SSPR e o que fazer significa?](active-directory-passwords-how-it-works.md)
* [Julgo que algo está danificado. Como posso resolver problemas SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi abrangida algures senão](active-directory-passwords-faq.md)

[Reporting]: ./media/active-directory-passwords-reporting/sspr-reporting.png "Exemplo de auditoria de atividade SSPR inicia sessão no Azure AD"
