---
title: "Utilize a ferramenta de migração Power BI Embedded | Microsoft Docs"
description: "A Power BI Embedded ferramenta de migração pode ser utilizada para copiar os relatórios das coleções de área de trabalho do Power BI para Power BI Embedded."
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
ms.openlocfilehash: 0b7b5089045daf6dd88fcd84e316b2bd44f8c927
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>Utilize a ferramenta de migração Power BI Embedded

A Power BI Embedded ferramenta de migração pode ser utilizada para copiar os relatórios das coleções de área de trabalho do Power BI para Power BI Embedded.

Migrar o conteúdo do suas coleções de área de trabalho para o Power BI o serviço pode ser efetuado em paralelo à sua solução atual e não requer qualquer período de inatividade.

## <a name="limitations"></a>Limitações

* Premido conjuntos de dados não podem ser transferidos e terão de ser recriado com as APIs REST do Power BI para o serviço do Power BI.
* Ficheiros PBIX importados antes de 26 de Novembro de 2016 não estará disponível para transferência.

## <a name="download"></a>Transferência

Pode transferir o exemplo de ferramenta de migração de [GitHub](https://github.com/Microsoft/powerbi-migration-sample). Pode optar por transferir um zip do repositório, ou pode clonar localmente. Depois de transferido, pode abrir *sample.sln de migração de powerbi* no Visual Studio para criar e executar a ferramenta de migração.

## <a name="migration-plans"></a>Planos de migração

O plano de migração é apenas de metadados cataloga o conteúdo dentro de coleções de área de trabalho do Power BI e a forma como pretende publicá-los para o Power BI Embedded.

### <a name="start-with-a-new-migration-plan"></a>Começar com um novo plano de migração

Um plano de migração é os metadados dos itens disponíveis no Power BI área de trabalho coleções que pretende, em seguida, mova sobre ao Power BI Embedded. O plano de migração é armazenado como um ficheiro XML.

Deverá começar por criar um novo plano de migração. Para criar um novo plano de migração, efetue o seguinte.

1. Selecione **ficheiro** > **novo plano de migração**.

    ![Novo plano de migração](media/migrate-tool/migrate-tool-plan.png)

2. No **selecionar grupo de recursos de coleções do Power BI área de trabalho** caixa de diálogo, irá querer selecionar o **ambiente** pendente e selecione prod.

3. Será solicitado para iniciar sessão. Irá utilizar o início de sessão de subscrição do Azure.

    > [!IMPORTANT]
    > Este é **não** do Office 365 conta da sua organização que inicia sessão no Power BI com.

4. Selecione a subscrição do Azure que armazena o recurso de coleções de área de trabalho do Power BI.

    ![Selecione a sua subscrição do Azure](media/migrate-tool/migrate-tool-select-resource-group.png)

5. Abaixo a lista de subscrições, selecione o **grupo de recursos** que contém as coleções de área de trabalho e selecione **selecione**.

    ![Selecione o grupo de recursos](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. Selecione **analisar**. Isto irá obter um inventário dos itens na sua subscrição do Azure para que possa começar o seu plano.

    ![Selecione o botão de análise](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > O processo de análise pode demorar vários minutos, consoante o número de coleções de área de trabalho e a quantidade de conteúdo existe na coleção de área de trabalho.

7. Quando **analisar** é concluída, que irá solicitar-lhe guardar o seu plano de migração.

Neste momento, se ligou seu plano de migração para a sua subscrição do Azure. Leitura abaixo para compreender o fluxo de como trabalhar com o seu plano de migração. Isto inclui analisar & planear a migração, transfira, criar grupos e carregamento.

### <a name="save-your-migration-plan"></a>Guardar o seu plano de migração

Pode guardar o seu plano de migração para uma utilização posterior. Isto irá criar um ficheiro XML que continha todas as informações no seu plano de migração.

Para guardar o seu plano de migração, efetue o seguinte.

1. Selecione **ficheiro** > **guardar o plano de migração**.

    ![Guardar a opção de menu do plano de migração](media/migrate-tool/migrate-tool-save-plan.png)

2. Dê um nome de ficheiro de ou utilize o nome de ficheiro gerado e selecione **guardar**.

### <a name="open-an-existing-migration-plan"></a>Abra um plano de migração existente

Pode abrir um plano de migração guardado para continuar a trabalhar na sua migração.

Para abrir o seu plano de migração existente, efetue o seguinte.

1. Selecione **ficheiro** > **abrir o plano de migração existente**.

    ![Abra a opção de menu do plano de migração existente](media/migrate-tool/migrate-tool-open-plan.png)

2. Selecione o ficheiro de migração e selecione **abra**.

## <a name="step-1-analyze-and-plan-migration"></a>Passo 1: Analisar e planear a migração

O **analisar & planear a migração** separador dá-lhe uma vista de que existe atualmente no grupo de recursos da sua subscrição do Azure.

![Analisar & planear separador de migração](media/migrate-tool/migrate-tool-step1.png)

Iremos abordar o *SampleResourceGroup* como exemplo.

### <a name="paas-topology"></a>Topologia de PaaS

Esta é uma listagem das suas *grupo de recursos > coleções de área de trabalho > áreas de trabalho*. As coleções de área de trabalho e o grupo de recursos irão mostrar um nome amigável. As áreas de trabalho irão mostrar um GUID.

Os itens na lista também apresentará uma cor e um número no formato de cardinal (#/ #). Esta definição indica o número de relatórios que pode ser transferido.

Uma cor preta significa que todos os relatórios podem ser transferidos. Uma cor vermelha significa que alguns relatórios não podem ser transferidos. O número de esquerdo irá indicar o número total de relatórios que pode ser transferido. O número à direita indica o número total de relatórios dentro do agrupamento.

Pode selecionar um item na topologia PaaS para apresentar os relatórios na secção dos relatórios.

### <a name="reports"></a>Relatórios

A secção de relatórios irá listar os relatórios disponíveis e indica se pode ser transferido ou não.

![Lista de relatórios em coleções de área de trabalho do Power BI](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>Estrutura de destino

O **destino estrutura** é onde dizer a ferramenta qual coisas serão transferidas para e como carregá-los.

#### <a name="download-plan"></a>Transferir o plano

Será criado automaticamente um caminho para si. Pode alterar este caminho se desejar. Se alterar o caminho, terá de selecionar **caminhos de atualização**.

**Isto não efetua efetivamente a transferência.** Isto é apenas especificar a estrutura do qual os relatórios serão transferidos para.

#### <a name="upload-plan"></a>Carregar o plano

Aqui, pode especificar um prefixo para ser utilizado para as áreas de trabalho de aplicações que serão criadas no âmbito do serviço Power BI. Depois do prefixo será o GUID para a área de trabalho que existiam no Azure.

![Especifique o prefixo de nome de grupo](media/migrate-tool/migrate-tool-upload-plan.png)

**Isto não, na verdade, cria os grupos no âmbito do serviço Power BI.** Isto define apenas a estrutura de nomenclatura para os grupos.

Se alterar o prefixo, terá de selecionar **gerar carregar planear**.

Pode com o botão direito clique num grupo e escolha mudar o nome do grupo no plano de carregamento diretamente, se assim o desejar.

![Mudar o nome da opção de menu de contexto de grupo](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> O nome do *grupo* não pode conter espaços ou carateres inválidos.

## <a name="step-2-download"></a>Passo 2: Transferir

No **transferir** separador, irão ver a lista de relatórios e metadados associados. Pode ver o que é o estado de exportação, juntamente com o estado de exportação anterior.

![Transferir o separador](media/migrate-tool/migrate-tool-download-tab.png)

Tem duas opções.

* Selecione relatórios específicos e selecione **transferir selecionado**
* Selecione **transferir todos os**.

![Transferir o botão seleccionado](media/migrate-tool/migrate-tool-download-options.png)

Para uma transferência com êxito, verá um Estado de *feito* e irá refletir que existe o ficheiro PBIX.

Uma vez concluída a transferência, selecione o **criar grupos** separador.

## <a name="step-3-create-groups"></a>Passo 3: criar grupos

Depois de transferir os relatórios que estão disponíveis, pode ir para o **criar grupos** separador. Este separador irá criar as áreas de trabalho de aplicações no serviço Power BI com base no plano de migração que criou. Vai criar a área de trabalho de aplicação com o nome fornecido no **carregar** separador dentro **analisar & planear a migração**.

![Criar o separador de grupos](media/migrate-tool/migrate-tool-create-groups.png)

Para criar as áreas de trabalho de aplicação, pode escolher que o **criar grupos selecionados** ou **criar todos os grupos em falta**.

Quando seleciona uma destas opções, será solicitado para iniciar sessão. *Deverá utilizar as credenciais para o serviço do Power BI que pretende criar as áreas de trabalho da aplicação.*

![Início de sessão do Power BI no ecrã](media/migrate-tool/migrate-tool-create-group-sign-in.png)

Esta ação irá criar a área de trabalho de aplicações no serviço Power BI. Carregue os relatórios para a área de trabalho da aplicação.

Pode verificar que a área de trabalho de aplicação foi criada por iniciar sessão no Power BI e a validar que a área de trabalho existe. Vai notar que nada na área de trabalho.

![Área de trabalho de aplicações no serviço Power BI](media/migrate-tool/migrate-tool-app-workspace.png)

Depois da área de trabalho é criada, pode mover para o **carregar** separador.

## <a name="step-4-upload"></a>Passo 4: carregar

No **carregar** separador, este irá carregar os relatórios para o serviço do Power BI. Irá ver uma lista dos relatórios que transferimos no separador de transferência, juntamente com o nome do grupo de destino com base no seu plano de migração.

![Carregar o separador](media/migrate-tool/migrate-tool-upload-tab.png)

Pode carregar relatórios seleccionados ou foi possível carregar todos os relatórios. Pode também repor o estado de carregamento para a carregar itens.

Também tem a opção de selecionar o que fazer se existe um relatório com o mesmo nome. Pode escolher entre **abortar**, **ignorar** e **substituir**.

![Opção pendente para o que fazer se existe o relatório](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![Carregar resultados selecionados](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>Duplicar nomes de relatório

Se tiver um relatório que tem o mesmo nome, mas sabe que é um relatório diferente, terá de alterar o **TargetName** do relatório. Pode alterar o nome ao editar manualmente o XML do plano de migração.

Terá de fechar a ferramenta de migração para efetuar a alteração e, em seguida, volte a abrir a ferramenta e o plano de migração.

No exemplo acima, um dos relatórios clonados falhou com a indicação de que um relatório com o mesmo nome existente. Se ficarem Vamos observar o XML do plano de migração, veremos aparecer o seguinte.

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

Para o item falhado, mas pode alterar o nome do SaaSTargetReportName.

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

Iremos pode, em seguida, volte a abrir o plano, na ferramenta de migração e carregar o relatório de falha.

Retroceder para o Power BI, é possível ver que os relatórios e conjuntos de dados foi carregados na área de trabalho de aplicação.

![Lista de relatórios no serviço Power BI](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>Carregar um ficheiro PBIX local

Pode carregar uma versão local de um ficheiro do Power BI Desktop. Terá de fechar a ferramenta, editar o XML e colocar o caminho completo para o PBIX no local a **PbixPath** propriedade.

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

Depois de ter a editar o xml, volte a abrir o plano da ferramenta de migração e carregar o relatório.

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>Relatórios de DirectQuery

Terá de atualizar para atualizar a cadeia de ligação para relatórios de DirectQuery. Isto pode ser feito na *powerbi.com*, ou através de programação pode consultar a cadeia de ligação do Power BI Embedded (Paas). Por exemplo, consulte [DirectQuery extrair a cadeia de ligação do relatório de PaaS](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections).

Em seguida, pode atualizar a cadeia de ligação para o conjunto de dados no serviço Power BI e defina as credenciais da origem de dados. Pode examinar os exemplos seguintes para ver como fazê-lo.

* [Atualizar a cadeia de ligação de DirectQuery no Power BI Embedded](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [Defina credenciais de DirectQuery no Power BI Embedded](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>Passos seguintes

Agora que os seus relatórios tiverem sido migrados do coleções de área de trabalho do Power BI para Power BI Embedded, pode atualizar a sua aplicação e começar a incorporar os relatórios na área de trabalho desta aplicação.

Para obter mais informações, consulte [como migrar conteúdo de coleção de área de trabalho do Power BI para Power BI Embedded](migrate-from-power-bi-workspace-collections.md).

Mais perguntas? [Tente pedir a Comunidade do Power BI](http://community.powerbi.com/)