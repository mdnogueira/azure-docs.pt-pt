---
title: "Configurar parâmetros de servidor na base de dados do Azure para PostgreSQL através do portal do Azure | Microsoft Docs"
description: "Este artigo descreve como configurar os parâmetros de servidor na base de dados do Azure para PostgreSQL através do portal do Azure."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 9e8262fbfcde2e69a656e356a7ab241f2d5043ad
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="configure-server-parameters-in-azure-portal"></a>Configurar parâmetros de servidor no portal do Azure
Pode listar, mostrar e atualizar os parâmetros de configuração para uma base de dados do Azure para o servidor de PostgreSQL através do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos necessário:
- [Base de dados do Azure para o servidor de PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visualizar e editar parâmetros
1. Abra o [Portal do Azure](https://portal.azure.com).

2. Selecione a base de dados do Azure para o servidor de PostgreSQL.

3. Sob o **definições** secção, selecione **parâmetros do**. A página mostra uma lista de parâmetros, os respetivos valores e descrições.
![Página de descrição geral para os parâmetros](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selecione o **pendente** botão para ver os valores possíveis para parâmetros de tipo enumerado como client_min_messages.
![Enumerar a lista pendente](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selecione ou coloque o cursor sobre o **posso** botão (informações) para ver o intervalo de valores possíveis para parâmetros de um valor numérico como cpu_index_tuple_cost.
![botão de informações](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Se necessário, utilize o **caixa pesquisa** para restringir a um parâmetro específico. A pesquisa se o nome e descrição dos parâmetros.
![Resultados da pesquisa](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Altere os valores de parâmetro que pretende ajustar. Todas as alterações efetuadas numa sessão são realçadas na roxa. Assim que tiver alterado os valores, pode selecionar **guardar**. Ou pode **rejeitar** as suas alterações.
![Guardar ou alterações de rejeição](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Se guardar os novos valores para os parâmetros, pode sempre reverter tudo para os valores predefinidos, selecionando **repor todas as predefinido**.
![Repor todas as predefinida](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:
- [Descrição geral dos parâmetros de servidor na base de dados do Azure para PostgreSQL](concepts-servers.md)
- [Configurar os parâmetros que utilizam a CLI do Azure](howto-configure-server-parameters-using-cli.md)
