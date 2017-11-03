---
title: "Relatórios no Azure Active Directory o aprovisionamento de conta de utilizador automáticas para aplicações SaaS | Microsoft Docs"
description: "Saiba como verificar o estado das tarefas de aprovisionamento da conta de utilizador automáticas e como resolver problemas de aprovisionamento de utilizadores individuais."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: asmalser-msft
ms.openlocfilehash: 86b9a3d93745045904c6038583b9bc6ebac5667e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Relatórios sobre o aprovisionamento da conta de utilizador automáticas


Azure Active Directory inclui uma [conta de utilizador do serviço de fornecimento](active-directory-saas-app-provisioning.md) que ajuda a automatizar o aprovisionamento anular o aprovisionamento de contas de utilizador em aplicações SaaS e outros sistemas, para fins de gestão do ciclo de vida de identidade de ponto a ponto. Azure AD suporta utilizador previamente integrada aprovisionamento conectores para todas as aplicações e sistemas na secção "Em destaque" o [Galeria de aplicações do Azure AD](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Este artigo descreve como verificar o estado de aprovisionamento de tarefas após ter sido definidos cópias de segurança e como resolver problemas de aprovisionamento de utilizadores individuais e grupos.

## <a name="overview"></a>Descrição geral

Os conectores de aprovisionamento são principalmente configurar e configuradas utilizando a [portal de gestão do Azure](https://portal.azure.com), seguindo o [fornecido documentação](active-directory-saas-tutorial-list.md) para a aplicação onde o aprovisionamento da conta de utilizador for pretendido. Depois de configurada e em execução, o aprovisionamento de tarefas para uma aplicação pode ser comunicado utilizando um dos dois métodos:

* **Portal de gestão do Azure** -este artigo descreve principalmente a obtenção de informações de relatório do [portal de gestão do Azure](https://portal.azure.com), que fornece tanto um relatório de resumo de aprovisionamento, bem como aprovisionamento detalhadas registos para uma aplicação específica de auditoria.

* **API de auditoria** -Azure Active Directory também fornece uma API de auditoria que permite a obtenção programática dos registos de auditoria de aprovisionamento detalhadas. Consulte [auditoria do Azure Active Directory referência da API](active-directory-reporting-api-audit-reference.md) para documentação específica para utilizar esta API. Embora este artigo não abrange como utilizar a API especificamente, de detalhe os tipos de eventos que são registados no registo de auditoria de aprovisionamento.

### <a name="definitions"></a>Definições

Este artigo utiliza os seguintes termos definidos abaixo:

* **Sistema de origem** -o repositório de utilizadores que sincroniza a partir do Azure AD aprovisionamento do serviço. Azure Active Directory é o sistema de origem para a maioria das pré-integrado aprovisionamento conectores, no entanto, existem algumas exceções (exemplo: a sincronização de entrada do Workday).

* **Sistema de destino** -o repositório de utilizadores que sincroniza a para o Azure AD que o serviço de fornecimento. Isto é, geralmente, uma aplicação SaaS (exemplos: Salesforce, ServiceNow, Google Apps, Dropbox para empresas), mas, em alguns casos, pode ser um sistema local como o Active Directory (exemplo: sincronização de entrada do Workday para o Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Introdução ao aprovisionamento relatórios a partir do portal de gestão do Azure

Para obter informações de relatório para uma aplicação específica de aprovisionamento, comece por iniciar o [portal de gestão do Azure](https://portal.azure.com) e navegar para a aplicação empresarial para o qual o aprovisionamento estiver configurado. Por exemplo, se estiver a aprovisionar utilizadores LinkedIn elevação, é o caminho de navegação para os detalhes da aplicação:

**Azure Active Directory > aplicações da empresa > todas as aplicações > LinkedIn elevar**

Aqui, pode aceder ao relatório de resumo de aprovisionamento e os registos de auditoria de aprovisionamento, ambos descrito abaixo.


### <a name="provisioning-summary-report"></a>Relatório de resumo de aprovisionamento

O relatório de resumo de aprovisionamento é visível no **aprovisionamento** separador fornecido a aplicação. Está localizado na secção Detalhes de sincronização por baixo **definições**e fornece as seguintes informações:

* O número total de utilizadores e / grupos que foram sincronizadas e estão atualmente no âmbito de aprovisionamento entre o sistema de origem e de sistema de destino.

* A última vez em que a sincronização foi executada. As sincronizações ocorram, normalmente, a cada 40 de 20 minutos, depois de uma sincronização completa foi concluída.

* Se pretende ou não uma sincronização completa inicial foi concluída.

* Se o processo de aprovisionamento foi colocado em quarentena, e o motivo para o estado de quarentena Novidades por exemplo, (se não conseguir comunicar com o sistema de destino devido a credenciais de administrador inválido)

O relatório de resumo aprovisionamento deve ser a primeira impressão de administradores local para verificar o estado de funcionamento operacional da tarefa de aprovisionamento.

 ![Relatório de resumo](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

### <a name="provisioning-audit-logs"></a>Aprovisionamento de registos de auditoria
Todas as atividades executadas pelo serviço de aprovisionamento são registadas nos registos de auditoria do Azure AD, que podem ser visualizados no **registos de auditoria** separador sob o **aprovisionamento da conta** categoria. Os tipos de eventos de atividade registados incluem:

* **Importar eventos** -um evento de "Importar" é registado sempre que o Azure AD que o serviço de fornecimento obtém informações sobre um grupo ou utilizador individual de um sistema de origem ou o sistema de destino. Durante a sincronização, os utilizadores são obtidos a partir do sistema de origem em primeiro lugar, com os resultados registados como "Importar" eventos. Os IDs correspondentes obtidas utilizadores, em seguida, são consultados contra o sistema de destino para verificar se existirem, com os resultados também registados como "Importar" de eventos. Estes eventos de registo todos os atributos de utilizador mapeada e os respetivos valores que foram vistos pelo Azure AD que o serviço de fornecimento no momento do evento. 

* **Eventos de regra de sincronização** - estes eventos reportam os resultados das regras de mapeamento de atributos e qualquer configurado filtros de âmbito, depois de dados do utilizador foi importados e avaliados a partir dos sistemas de origem e de destino. Por exemplo, se um utilizador num sistema de origem é considerado para estar no âmbito de aprovisionamento e considera-que não existe no sistema de destino, em seguida, este evento regista que o utilizador será aprovisionado no sistema de destino. 

* **Exportar eventos** -um evento de "exportação" é registado sempre que o Azure AD que o serviço de fornecimento escreve um objeto de grupo ou conta de utilizador para um sistema de destino. Estes eventos de registo todos os atributos de utilizador e os respetivos valores que foram escritas pelo Azure AD aprovisionamento do serviço no momento do evento. Se tiver ocorrido um erro ao escrever o objeto de grupo ou conta de utilizador no sistema de destino, será apresentado aqui.

* **Processar os eventos de caução** -escrows processo ocorrerem quando o serviço de aprovisionamento detetar uma falha ao tentar uma operação e começa a para repetir a operação num intervalo de término de tempo. Um evento de "caução" é registado sempre que foi extinguida uma operação de aprovisionamento.

Quando observar os eventos para um utilizador individual de aprovisionamento, ocorrem os eventos normalmente pela seguinte ordem:

1. Evento de importação: utilizador obtido a partir do sistema de origem.

2. Evento de importação: o sistema de destino está a ser consultado para verificar a existência do utilizador obtido.

3. Evento de regra de sincronização: os dados de utilizador de sistemas de origem e de destino são avaliados em comparação com o atributo configurado regras de mapeamento e o âmbito de filtros para determinar a ação, se existirem, deve ser executada.

4. Exportar eventos: se o evento de regra de sincronização ditado que deve ser uma ação efetuada (por exemplo, adicionar, atualizar, eliminar), em seguida, os resultados da ação são registados num evento de exportação.

![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Procurar o aprovisionamento de eventos para um utilizador específico

É o caso de utilização mais comuns para os registos de auditoria de aprovisionamento para verificar o estado de aprovisionamento de uma conta de utilizador individuais. Procurar os eventos de aprovisionamento último para um utilizador específico:

1. Vá para o **registos de auditoria** secção.

2. Do **categoria** menu, selecione **aprovisionamento da conta**.

3. No **intervalo de datas** menu, selecione de que pretende procurar, o intervalo de datas

4. No **pesquisa** barra, introduza o ID de utilizador do utilizador que pretende procurar. O formato do valor de ID deve corresponder ao que selecionou como o ID de principal correspondente na configuração de mapeamento de atributos (por exemplo, userPrincipalName ou empregado ID número). O valor de ID necessário vai estar visível na coluna destino (s).

5. Prima Enter para procurar. Eventos mais recentes aprovisionamento serão devolvidos pela primeira vez.

6. Se os eventos são devolvidos, tenha em atenção os tipos de atividade e foi concluída com êxito ou falha. Se não existem resultados são devolvidos, significa que o utilizador não existe, ou não ainda foi detetado pelo processo de aprovisionamento se uma sincronização completa ainda não foi concluído.

7. Clique em eventos individuais para ver os detalhes expandidos, incluindo todas as propriedades de utilizador que foram obtidas, avaliadas ou escritas como parte do evento.


### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Sugestões para ver os registos de auditoria de aprovisionamento

Para melhor legibilidade no portal do Azure, selecione o **colunas** botão e escolha estas colunas:

* **Data** -mostra a data do evento ocorreu.
* **Destino (s)** -mostra o ID de utilizador e nome de aplicação são os assuntos do evento.
* **Atividade** -o tipo de atividade, conforme descrito anteriormente.
* **Estado** - se o evento com êxito ou não.
* **Motivo do estado** -um resumo do que aconteceu no evento aprovisionamento.


## <a name="troubleshooting"></a>Resolução de problemas

Os registos de auditoria e relatório de resumos aprovisionamento desempenham um papel chave ajudar os administradores a resolver problemas de aprovisionamento da conta de utilizador vários.

Para orientações sobre como resolver problemas de aprovisionamento de utilizadores automática baseada em cenários, consulte [problemas de configuração e o aprovisionamento de utilizadores a uma aplicação](active-directory-application-provisioning-content-map.md).


## <a name="additional-resources"></a>Recursos Adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
