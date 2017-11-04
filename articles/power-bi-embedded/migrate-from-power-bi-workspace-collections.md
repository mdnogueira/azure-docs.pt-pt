---
title: "Como migrar conteúdo de coleção de área de trabalho do Power BI para Power BI Embedded | Microsoft Docs"
description: "Saiba como migrar a partir de coleções de área de trabalho do Power BI para Power BI Embedded e tirar partido de avanços para incorporar nas aplicações."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Como migrar conteúdo de coleção de área de trabalho do Power BI para Power BI Embedded

Saiba como migrar a partir de coleções de área de trabalho do Power BI para Power BI Embedded. Este artigo fornece orientação para migrar das coleções de área de trabalho do Azure Power BI para Power BI Embedded. Vamos também ver o que pode esperar para alterações de aplicação.

O recurso de coleções de área de trabalho do Power BI continua a estar disponíveis para um período limitado de tempo após o lançamento de disponibilidade geral do Premium do Power BI. Os clientes com um Enterprise Agreement tem acesso ao respetivos coleções de área de trabalho existente através da expiração dos respetivos contratos existentes. Clientes que obteve coleções de área de trabalho do Power BI através dos canais direta ou CSP desfrutar acesso durante um ano de geral disponibilidade do Power BI Premium.

> [!IMPORTANT]
> Enquanto a migração demora uma dependência no serviço Power BI, não há uma dependência no Power BI para os utilizadores da sua aplicação ao utilizar um **token de incorporação**. Não é necessário para se inscrever no Power BI ver o conteúdo incorporado na sua aplicação. Pode utilizá-lo ao incorporar abordagem incorporar o Power BI para os seus clientes.

![Fluxo de Power BI Embedded](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Preparar para a migração

Existem algumas coisas que precisa de fazer para se preparar para migrar do serviço de coleções de área de trabalho do Power BI ao longo para o Power BI Embedded. Precisa de um inquilino disponível, juntamente com um utilizador que tenha uma licença do Power BI Pro.

1. Certifique-se de que tem acesso a um inquilino do Azure Active Directory (Azure AD).

    O inquilino para utilizar?

    * Utilizar o seu inquilino do Power BI empresarial existente?
    * Utilizar um inquilino separado para a sua aplicação?
    * Utilizar um inquilino separado para cada cliente?

    Se optar por criar um novo inquilino para a sua aplicação ou a cada cliente, consulte um dos seguintes:

    * [Criar um inquilino do Azure Active Directory](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Como obter um inquilino do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Crie um utilizador neste novo inquilino que age como a sua conta "original" da aplicação. Que conta tem de se inscrever no Power BI e tem de ter uma licença do Power BI Pro atribuída.

## <a name="accounts-within-azure-ad"></a>Contas no Azure AD

As contas seguintes têm de existir no seu inquilino.

> [!NOTE]
> Estas contas tem de ter o Power BI Pro licenças para poder utilizar áreas de trabalho da aplicação.

1. Um utilizador de administrador inquilino.

    Recomenda-se que a área de trabalho de aplicação embedding tem o administrador de inquilino listado como um membro.

2. Contas para os analistas que criar conteúdo de mensagens em fila.

    Estes utilizadores devem ser atribuídos a áreas de trabalho de aplicação conforme necessário.

3. Uma aplicação *mestre* conta de utilizador ou a conta de serviço.

    O back-end de aplicações armazena as credenciais para esta conta. Utilize o *mestre* conta para adquirir um token do Azure AD para utilização com as APIs REST do Power BI. Esta conta é utilizada para gerar o token de incorporação para a aplicação. O *mestre* conta tem de ser um administrador das áreas de trabalho de aplicação criado para incorporar.

    **Esta conta é apenas uma conta de utilizador normal da sua organização que é utilizada para efeitos de incorporar.**

## <a name="app-registration-and-permissions"></a>Registo de aplicação e permissões

Para efetuar chamadas de REST API, registe uma aplicação com o Azure AD. Configuração de adição é aplicada no portal do Microsoft Azure, para além da página de registo de aplicação do Power BI. Para obter mais informações, consulte [registar uma aplicação do Azure AD para incorporar o conteúdo do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

A recomendação está a registar a aplicação utilizar a aplicação **mestre** conta.

## <a name="create-app-workspaces-required"></a>Criam áreas de trabalho de aplicação (obrigatório)

Se a aplicação está a servir vários clientes, pode tirar partido das áreas de trabalho de aplicações para proporcionar o isolamento melhor. Dashboards e relatórios seria isolados entre os seus clientes. Pode, em seguida, utilizar uma conta do Power BI por área de trabalho de aplicação para obter mais isolar experiências de aplicação entre os seus clientes, mas apenas pode utilizar uma conta para manter simples.

> [!IMPORTANT]
> Não é possível utilizar uma área de trabalho pessoal (um "A minha área de trabalho") para tirar partido das incorporar aos seus clientes.

Precisa de um utilizador que tenha uma licença do Pro para criar uma área de trabalho de aplicação no Power BI. O utilizador do Power BI que cria a área de trabalho de aplicação é um administrador de área de trabalho por predefinição. **A aplicação *mestre* conta tem de ser um administrador da área de trabalho.**

## <a name="content-migration"></a>Migração de conteúdo

Migrar o conteúdo do seu coleções de área de trabalho para o Power BI Embedded pode ser efetuada em paralelo à sua solução atual e não requer qualquer período de inatividade.

A **ferramenta de migração** está disponível para utilização para melhorar a copiar o conteúdo de coleções de área de trabalho do Power BI para Power BI Embedded. Especialmente se tiver muitos relatórios. Para obter mais informações, consulte [ferramenta de migração do Power BI Embedded](migrate-tool.md).

Migração de conteúdo depende principalmente em duas APIs.

1. Transferência PBIX - esta API pode transferir ficheiros PBIX foram carregados para o Power BI depois de Outubro de 2016.
2. Importar PBIX - esta API carregamentos qualquer PBIX ao Power BI.

Para alguns relacionados com fragmentos de código, consulte [fragmentos para a migração de conteúdos a partir do Power BI Embedded de código](migrate-code-snippets.md).

### <a name="report-types"></a>Tipos de relatório

Existem vários tipos de relatórios, cada necessidade de um fluxo de migração diferentes.

#### <a name="cached-dataset-and-report"></a>Conjunto de dados em cache e o relatório

Conjuntos de dados em cache Consulte os ficheiros PBIX que tinham importado dados, por oposição a uma ligação em direto ou a ligação de DirectQuery.

**Fluxo**

1. Chame a API de PBIX transferir da sua área de trabalho de coleção de área de trabalho do Power BI.
2. Guarde o PBIX.
3. Chamada PBIX de importação para a sua área de trabalho do Power BI Embedded.

#### <a name="directquery-dataset-and-report"></a>Conjunto de dados de DirectQuery e relatório

**Fluxo**

1. Chamada GET https://api.powerbi.com/v1.0/collections/ {collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources e guardar a cadeia de ligação que recebeu.
2. Chame a API de PBIX transferir da sua área de trabalho de coleção de área de trabalho do Power BI.
3. Guarde o PBIX.
4. Chamada PBIX de importação para a sua área de trabalho do Power BI Embedded.
5. Atualizar a cadeia de ligação ao chamar - PUBLIQUE https://api.powerbi.com/v1.0/myorg/datasets/ {dataset_id}/Default.SetAllConnections
6. Obter ID de GW ID e a origem de dados, chamando - obter https://api.powerbi.com/v1.0/myorg/datasets/ {dataset_id}/Default.GetBoundGatewayDataSources
7. Atualizar as credenciais do utilizador ao chamar - aplicar o PATCH /datasources/ https://api.powerbi.com/v1.0/myorg/gateways/ {gateway_id} {datasource_id}

#### <a name="old-dataset-and-reports"></a>Conjunto de dados antigo e relatórios

Relatórios carregados antes de Outubro de 2016 não suportam a funcionalidade de transferir PBIX.

**Fluxo**

1. Obter PBIX do seu ambiente de desenvolvimento (o controlo de origem interna).
2. Chamada PBIX de importação para a sua área de trabalho do Power BI Embedded.

#### <a name="push-dataset-and-report"></a>Push de conjunto de dados e relatório

Transferir PBIX não suporta *Push API* conjuntos de dados. Dados de conjunto de dados de API de push não podem ser de coleções de área de trabalho do Power BI convertidos para serem Power BI Embedded.

**Fluxo**

1. Chame a API de "Criar conjunto de dados" com o conjunto de dados Json para criar o conjunto de dados para a sua área de trabalho do Power BI Embedded.
2. Reconstrua o relatório para o conjunto de dados criada *.

É possível utilizar algumas soluções para migrar o push relatório da api de coleções de área de trabalho do Power BI para Power BI Embedded tentando o seguinte:

1. A carregar alguns PBIX fictício para a área de trabalho de coleção de área de trabalho do Power BI.
2. Clonar o push api de relatórios e vinculá-lo para o PBIX fictício do passo 1.
3. Transfira o relatório de API com o PBIX fictício de push.
4. Carregar PBIX fictício para a sua área de trabalho do Power BI Embedded.
5. Crie conjunto de dados de push na sua área de trabalho do Power BI Embedded.
6. Efetuar o reenlace relatório para emitir o conjunto de dados de api.

## <a name="create-and-upload-new-reports"></a>Criar e carregar novos relatórios

Além do conteúdo migrado de coleções de área de trabalho do Power BI, pode criar os seus relatórios e conjuntos de dados utilizando o ambiente de trabalho do Power BI e, em seguida, publicar esses relatórios para uma área de trabalho da aplicação. O utilizador final, os relatórios de publicação tem de ter uma licença do Power BI Pro para publicar uma área de trabalho da aplicação.

## <a name="rebuild-your-application"></a>Reconstruir a aplicação

1. Modificar a sua aplicação para utilizar as APIs REST do Power BI e a localização do relatório no powerbi.com.

2. Reconstrua a sua autenticação AuthN/AuthZ ao utilizar o *mestre* conta para a sua aplicação. Pode tirar partido da utilização de um [token de incorporação](https://msdn.microsoft.com/library/mt784614.aspx) para permitir que este utilizador agir em nome de outros utilizadores.

3. Incorpore os seus relatórios do Power BI Embedded na sua aplicação. Para obter mais informações, consulte [como incorporar dashboards do Power BI, relatórios e mosaicos](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Mapear os seus utilizadores a um utilizador do Power BI

Na sua aplicação, mapear os utilizadores que gere na aplicação para um *mestre* credencial do Power BI para efeitos da sua aplicação. As credenciais para este Power BI *mestre* conta são armazenados na aplicação e ser utilizado para criar incorpore tokens.

## <a name="what-to-do-when-you-are-ready-for-production"></a>O que fazer quando estiver pronto para produção

Quando estiver pronto para mudar para produção, tem de fazer o seguinte:

- Se estiver a utilizar um inquilino separado para o desenvolvimento, em seguida, tem de certificar-se de que as áreas de trabalho de aplicação, juntamente com dashboards e relatórios, estão disponíveis no seu ambiente de produção. Certifique-se de que criou a aplicação no Azure AD para o seu inquilino de produção e atribuir as permissões de aplicação correta, conforme indicado no passo 1.

- Compre uma capacidade que se adeque às suas necessidades. Pode utilizar o [documento de planeamento de capacidade de análise de incorporados](https://aka.ms/pbiewhitepaper) para ajudar a compreender o que poderá ser necessário. Quando estiver pronto para adquirir, pode comprar um Power BI Embedded recurso no portal do Azure.

- Edite a área de trabalho da aplicação e atribua-a uma capacidade em Avançadas.

    ![Atribuir a área de trabalho da aplicação a uma capacidade no site powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Implementar a sua aplicação atualizada para produção e começar a incorporar relatório do Power BI Embedded.

## <a name="after-migration"></a>Após a migração

É necessária alguma limpeza dentro de coleções de área de trabalho do Power BI.

- Remova todas as áreas de trabalho retire a solução implementada no âmbito do serviço do Azure de coleções de área de trabalho do Power BI.
- Elimine as coleções de área de trabalho que existe no Azure.

## <a name="next-steps"></a>Passos seguintes

Parabéns! A aplicação agora é migrada para o Power BI Embedded. Para obter informações sobre como incorporar os seus dashboards do Power BI, relatórios e conjuntos de dados, consulte [como incorporar dashboards do Power BI, relatórios e mosaicos](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Mais perguntas? [Tente pedir a Comunidade do Power BI](http://community.powerbi.com/)