---
title: "Relatório de atividade de auditoria no portal do Azure Active Directory - pré-visualização| Microsoft Docs"
description: "Introdução aos relatórios de atividade de auditoria na pré-visualização do portal do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: aef5bce6f440f4a0a57763f915d307297f50281b
ms.lasthandoff: 04/10/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Relatório de atividade de auditoria no portal do Azure Active Directory - pré-visualização

Com os relatórios na [pré-visualização](active-directory-preview-explainer.md) do Azure Active Directory, obtém todas as informações de que precisa para determinar o estado de funcionamento do seu ambiente.

A arquitetura de relatórios no Azure Active Directory consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de início de sessão** – Informações sobre a utilização de aplicações geridas e atividades de início de sessão do utilizador
    - **Registos de auditoria** - informações de atividades do sistema sobre gestão de utilizadores e de grupos, as suas aplicações geridas e atividades de diretório.
- **Segurança** 
    - **Inícios de sessão de risco** – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. Para obter mais detalhes, veja Inícios de sessão de risco.
    - **Utilizadores sinalizados para risco** – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. Para obter mais detalhes, veja Utilizadores sinalizados para risco.

Este tópico fornece-lhe uma descrição geral das atividades de auditoria.
 


## <a name="audit-logs"></a>Registos de auditoria

Os registos de auditoria no Azure Active Directory fornecem registos das atividades de sistema para efeitos de conformidade.  
O primeiro ponto de entrada para todos os dados de auditoria é **Registos de auditoria** na secção **Atividade** do **Azure Active Directory**.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/61.png "Registos de auditoria")

Um registo de auditoria tem uma vista de lista predefinida que mostra:

- a data e hora da ocorrência
- o iniciador / ator (*quem*) de uma atividade 
- a atividade (*o quê*) 
- o destino

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/18.png "Registos de auditoria")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/19.png "Registos de auditoria")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/21.png "Registos de auditoria")


Ao clicar num item na vista de lista, obtém todos os detalhes sobre o mesmo.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/22.png "Registos de auditoria")


## <a name="filtering-audit-logs"></a>Filtrar registos de auditoria

Para limitar os dados comunicados a um nível que funcione para si, pode filtrar os dados de auditoria através dos seguintes campos:

- Intervalo de datas
- Iniciado por
- Categoria
- Categoria
- Tipo de recurso de atividade
- Atividade

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/23.png "Registos de auditoria")


O filtro **intervalo de datas** permite-lhe definir um período de tempo para os dados devolvidos.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizado

Quando selecionar um período de tempo personalizado, pode configurar uma hora de início e uma hora de fim.

O filtro **iniciado por** permite-lhe definir o nome de um ator ou o respetivo nome principal universal (UPN).

O filtro **categoria** permite-lhe selecionar um dos seguintes filtros:

- Todos
- Categoria principal
- Diretório principal
- Gestão de palavras-passe personalizada
- Gestão de grupos self-service
- Aprovisionamento de contas
- Reversão de palavras-passe automatizada
- utilizadores convidados
- Serviço MIM

O filtro **tipo de recurso de atividade** permite-lhe selecionar um dos seguintes filtros:

- Todos 
- Grupo
- Diretório
- Utilizador
- Aplicação
- Política
- Dispositivo
- Outros

Quando seleciona **Grupo** como o **tipo de recurso de atividade**, obtém uma categoria de filtro adicional que lhe permite também indicar uma **Origem**:

- Azure AD
- O365




O filtro **atividade** baseia-se na categoria e na seleção de Tipo de recurso de atividade que fizer. Pode selecionar uma atividade específica que queira ver ou selecionar todas. 

| Categoria de Atividade| Tipo de Recurso de Atividade| Atividade |
| :-- | :-: | :-- |
| Diretório do Núcleo| Grupo| Eliminar Definições de Grupo|
| Diretório do Núcleo| Diretório| Atualizar Domínio|
| Diretório do Núcleo| Diretório| Remover Parceiro da Empresa|
| Diretório do Núcleo| Utilizador| Atualizar Função|
| Diretório do Núcleo| Utilizador| Adicionar Função a Partir de Modelo|
| Diretório do Núcleo| Grupo| Adicionar Atribuição de Função de Aplicação a Grupo|
| Diretório do Núcleo| Grupo| Iniciar Aplicação de Licença Baseada em Grupo a Utilizadores|
| Diretório do Núcleo| Aplicação| Adicionar Principal de Serviço|
| Diretório do Núcleo| Política| Atualizar Política|
| Diretório do Núcleo| Política| Adicionar Política ao Principal de Serviço|
| Diretório do Núcleo| Dispositivo| Adicionar Proprietário Registado ao Dispositivo|
| Diretório do Núcleo| Dispositivo| Adicionar Utilizadores Registados ao Dispositivo|
| Diretório do Núcleo| Dispositivo| Atualizar Configuração de Dispositivo|
| Gestão de Palavra-passe Personalizada| Utilizador| Repor Palavra-passe (personalizada)|
| Gestão de Palavra-passe Personalizada| Utilizador| Desbloquear Conta de Utilizador (personalizado)|
| Gestão de Palavra-passe Personalizada| Utilizador| Repor Palavra-passe (Por Administrador)|
| Gestão de Grupos Personalizada| Grupo| Eliminar Pedido Pendente para Aderir a Um Grupo|
| Aprovisionamento de Contas| Aplicação| Processo de Caução|
| Rollover de Palavra-passe Automatizada| Aplicação| Rollover de Palavra-passe Automatizada|
| Utilizadores Convidados| Outros| Convites em Lote Processados|
| Diretório do Núcleo| Diretório| Remover Domínio Verificado|
| Diretório do Núcleo| Diretório| Adicionar Domínio Não Verificado|
| Diretório do Núcleo| Diretório| Adicionar Domínio Verificado|
| Diretório do Núcleo| Diretório| Definir Funcionalidade de Diretório em Inquilino|
| Diretório do Núcleo| Diretório| Definir o Sinalizador Dirsyncenabled|
| Diretório do Núcleo| Diretório| Criar Definições da Empresa|
| Diretório do Núcleo| Diretório| Atualizar Definições da Empresa|
| Diretório do Núcleo| Diretório| Eliminar Definições da Empresa|
| Diretório do Núcleo| Diretório| Definir Localização dos Dados Permitida da Empresa|
| Diretório do Núcleo| Diretório| Definir Funcionalidade Multinacional da Empresa Ativada|
| Diretório do Núcleo| Utilizador| Atualizar Utilizador|
| Diretório do Núcleo| Utilizador| Eliminar Utilizador|
| Diretório do Núcleo| Grupo| Remover Membro do Grupo|
| Diretório do Núcleo| Grupo| Definir Licença de Grupo|
| Diretório do Núcleo| Grupo| Criar Definições de Grupo|
| Diretório do Núcleo| Aplicação| Atualizar Principal de Serviço|
| Diretório do Núcleo| Aplicação| Eliminar Aplicação|
| Diretório do Núcleo| Aplicação| Atualizar Aplicação|
| Diretório do Núcleo| Aplicação| Remover Principal de Serviço|
| Diretório do Núcleo| Aplicação| Adicionar Credenciais de Principal de Serviço|
| Diretório do Núcleo| Aplicação| Remover Atribuição de Função de Aplicação do Principal de Serviço|
| Diretório do Núcleo| Aplicação| Remover Proprietário da Aplicação|
| Diretório do Núcleo| Dispositivo| Remover Proprietário Registado do Dispositivo|
| Gestão de Palavra-passe Personalizada| Utilizador| Progresso da Atividade de Fluxo de Reposição de palavras-passe personalizada|
| Aprovisionamento de Contas| Aplicação| Administração|
| Aprovisionamento de Contas| Aplicação| Operação de Diretório|
| Serviço MIM| Grupo| Remover Membro|
| Diretório do Núcleo| Política| Eliminar Política|
| Utilizadores Convidados| Utilizador| Criação de Inquilinos Viral|
| Diretório do Núcleo| Diretório| Atualizar Segredos Externos|
| Diretório do Núcleo| Diretório| Definir Propriedades do Rights Management|
| Diretório do Núcleo| Diretório| Atualizar Empresa|
| Diretório do Núcleo| Utilizador| Adicionar Utilizador|
| Diretório do Núcleo| Utilizador| Converter Utilizador Federado em Gerido|
| Diretório do Núcleo| Utilizador| Criar Palavra-passe de Aplicação para Utilizador|
| Diretório do Núcleo| Grupo| Adicionar Membro ao Grupo|
| Diretório do Núcleo| Grupo| Adicionar Grupo|
| Diretório do Núcleo| Aplicação| Autorizar Aplicação|
| Diretório do Núcleo| Aplicação| Adicionar Aplicação|
| Diretório do Núcleo| Aplicação| Adicionar Proprietário ao Principal de Serviço|
| Diretório do Núcleo| Aplicação| Remover Oauth2Permissiongrant|
| Diretório do Núcleo| Política| Remover Credenciais de Política|
| Diretório do Núcleo| Dispositivo| Eliminar Configuração de Dispositivo|
| Gestão de Grupos Personalizada| Grupo| Definir Propriedades de Grupo Dinâmicas|
| Gestão de Grupos Personalizada| Grupo| Atualizar Política de Gestão de Ciclo de Vida|
| Aprovisionamento de Contas| Aplicação| Ação de Regra de Sincronização|
| Utilizadores Convidados| Outros| Convites em Lote Carregados|
| Serviço MIM| Grupo| Adicionar Membro|
| Diretório do Núcleo| Utilizador| Definir Propriedades de Licença|
| Diretório do Núcleo| Utilizador| Restaurar Utilizador|
| Diretório do Núcleo| Utilizador| Remover Membro de Função|
| Diretório do Núcleo| Utilizador| Remover Atribuição de Função de Aplicação do Utilizador|
| Diretório do Núcleo| Utilizador| Remover Membro com Âmbito de Função|
| Diretório do Núcleo| Grupo| Atualizar Grupo|
| Diretório do Núcleo| Grupo| Adicionar Proprietário a Grupo|
| Diretório do Núcleo| Grupo| Concluir a Aplicação de Licença Baseada em Grupo a Utilizadores|
| Diretório do Núcleo| Grupo| Remover Atribuição de Função de Aplicação do Grupo|
| Diretório do Núcleo| Grupo| Definir Grupo para Ser Gerido pelo Utilizador|
| Diretório do Núcleo| Aplicação| Adicionar Oauth2Permissiongrant|
| Diretório do Núcleo| Aplicação| Adicionar Atribuição de Função de Aplicação ao Principal de Serviço|
| Diretório do Núcleo| Aplicação| Remover Credenciais de Principal de Serviço|
| Diretório do Núcleo| Política| Remover Política do Principal de Serviço|
| Diretório do Núcleo| Dispositivo| Atualizar Dispositivo|
| Diretório do Núcleo| Dispositivo| Adicionar Dispositivo|
| Diretório do Núcleo| Dispositivo| Adicionar Configuração do Dispositivo|
| Gestão de Palavra-passe Personalizada| Utilizador| Alterar Palavra-passe (personalizada)|
| Gestão de Palavra-passe Personalizada| Utilizador| Utilizador Registado na Reposição de Palavra-passe Personalizada|
| Gestão de Grupos Personalizada| Grupo| Aprovar Pedido Pendente para Aderir a Um Grupo|
| Diretório do Núcleo| Diretório| Remover Domínio Não Verificado|
| Diretório do Núcleo| Diretório| Verificar Domínio|
| Diretório do Núcleo| Diretório| Definir Autenticação de Domínio|
| Diretório do Núcleo| Diretório| Definir Política de Palavras-passe|
| Diretório do Núcleo| Diretório| Adicionar Parceiro a Empresa|
| Diretório do Núcleo| Diretório| Promover Empresa a Parceiro|
| Diretório do Núcleo| Diretório| Definir Parceria|
| Diretório do Núcleo| Diretório| Definir Limiar de Eliminação Acidental|
| Diretório do Núcleo| Diretório| Despromover Parceiro|
| Utilizadores Convidados| Utilizador| Convidar utilizador Externo|
| Aprovisionamento de Contas| Aplicação| Importar|
| Diretório do Núcleo| Aplicação| Remover Proprietário do Principal de Serviço|
| Diretório do Núcleo| Dispositivo| Remover Utilizadores Registados do Dispositivo|
| Diretório do Núcleo| Diretório| Definir Informações da Empresa|
| Diretório do Núcleo| Diretório| Configurar Definições de Federação em Domínio|
| Diretório do Núcleo| Diretório| Criar Empresa|
| Diretório do Núcleo| Diretório| Purgar Propriedades do Rights Management|
| Diretório do Núcleo| Diretório| Definir Funcionalidade Dirsync|
| Diretório do Núcleo| Diretório| Verificar Domínio Verificado de E-mail|
| Diretório do Núcleo| Utilizador| Alterar Licença de Utilizador|
| Diretório do Núcleo| Utilizador| Alterar Palavra-passe do Utilizador|
| Diretório do Núcleo| Utilizador| Repor Palavra-passe do Utilizador|
| Diretório do Núcleo| Utilizador| Adicionar Concessão de Atribuição de Função de Aplicação a Utilizador|
| Diretório do Núcleo| Utilizador| Adicionar Membro a Função|
| Diretório do Núcleo| Utilizador| Eliminar Palavra-passe de Aplicação para Utilizador|
| Diretório do Núcleo| Utilizador| Atualizar Credenciais de Utilizador|
| Diretório do Núcleo| Utilizador| Definir Gestor de Utilizador|
| Diretório do Núcleo| Utilizador| Adicionar Membro com Âmbito a Função|
| Diretório do Núcleo| Grupo| Eliminar Grupo|
| Diretório do Núcleo| Grupo| Remover Proprietário de Grupo|
| Diretório do Núcleo| Grupo| Atualizar Definições de Grupo|
| Diretório do Núcleo| Aplicação| Adicionar Proprietário a Aplicação|
| Diretório do Núcleo| Aplicação| Revogar Autorização|
| Diretório do Núcleo| Política| Adicionar Política|
| Diretório do Núcleo| Dispositivo| Eliminar Dispositivo|
| Gestão de Palavra-passe Personalizada| Utilizador| Bloqueado de Reposição de Palavras-passe personalizada|
| Gestão de Grupos Personalizada| Grupo| Pedido para Aderir a um Grupo|
| Gestão de Grupos Personalizada| Grupo| Criar Política de Gestão de Ciclo de Vida|
| Gestão de Grupos Personalizada| Grupo| Rejeitar Pedido Pendente para Aderir a um Grupo|
| Gestão de Grupos Personalizada| Grupo| Cancelar Pedido Pendente para Aderir a um Grupo|
| Gestão de Grupos Personalizada| Grupo| Renovar Grupo|
| Aprovisionamento de Contas| Aplicação| Exportar|
| Aprovisionamento de Contas| Aplicação| Outros|
| Utilizadores Convidados| Utilizador| Resgatar Convite de Utilizador Externo|
| Utilizadores Convidados| Utilizador| Criação de Utilizador Viral|
| Utilizadores Convidados| Utilizador| Atribuir Utilizador Externo a Aplicação|




## <a name="audit-logs-shortcuts"></a>Atalhos dos registos de auditoria

Para além do **Azure Active Directory**, o portal do Azure proporciona-lhe dois pontos de entrada adicionais para dados de auditoria:

- Utilizadores e grupos
- Aplicações empresariais

Para obter uma lista completa de atividades de relatório de auditoria, veja a [lista de eventos de relatório de auditoria](active-directory-reporting-audit-events.md#list-of-audit-report-events).


### <a name="users-and-groups-audit-logs"></a>Registos de auditoria de utilizadores e grupos

Com os relatórios de auditoria baseados em utilizadores e grupos, poderá obter respostas a perguntas como:

- Que tipos de atualizações os utilizadores aplicaram?

- Quantos utilizadores foram alterados?

- Quantas palavras-passe foram alteradas?

- O que fez um administrador num diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com as alterações na associação?

- Os proprietários do grupo foram alterados?

- Que licenças foram atribuídas a um grupo ou utilizador?

Se quiser apenas rever dados de auditoria que estejam relacionados com utilizadores e grupos, pode encontrar um vista filtrada em **Registos de auditoria**, na secção **Atividade** de **Utilizadores e Grupos**. Este ponto de entrada tem **Utilizadores e Grupos** como o **Tipo de Recurso de Atividade** pré-selecionado.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/93.png "Registos de auditoria")

### <a name="enterprise-applications-audit-logs"></a>Registos de auditoria de aplicações empresariais

Com os relatórios de auditoria baseados em aplicações, poderá obter respostas a perguntas como:

* Quais são as aplicações que foram adicionadas ou atualizadas?
* Quais são as aplicações que foram removidas?
* Um principal de serviço para uma aplicação foi alterado?
* Os nomes das aplicações foram alterados?
* Quem autorizou uma aplicação?

Se quiser apenas rever dados de auditoria que estejam relacionados com aplicações, pode encontrar um vista filtrada em **Registos de auditoria**, na secção **Atividade** do painel **Aplicações empresariais**. Este ponto de entrada tem **Aplicação** como o **Tipo de Recurso de Atividade** pré-selecionado.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/134.png "Registos de auditoria")

Pode filtrar ainda mais esta vista para apenas **grupos** ou **utilizadores**.

![Registos de auditoria](./media/active-directory-reporting-activity-audit-logs/25.png "Registos de auditoria")


## <a name="next-steps"></a>Passos seguintes
Veja o [Guia dos Relatórios do Azure Active Directory](active-directory-reporting-guide.md).


