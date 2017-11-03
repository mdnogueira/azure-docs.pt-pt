---
title: Adicione o conector Informix nas suas Logic Apps | Microsoft Docs
description: "Descrição geral do conector de Informix com parâmetros de REST API"
services: 
documentationcenter: 
author: gplarsen
manager: anneta
editor: 
tags: connectors
ms.assetid: ca2393f0-3073-4dc2-8438-747f5bc59689
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen; ladocs
ms.openlocfilehash: b2e755b5b1b4939eac90ac55ba8398c5687124c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-informix-connector"></a>Começar a utilizar o conector Informix
Conector do Microsoft para Informix liga as Logic Apps aos recursos armazenados numa base de dados IBM Informix. O conector Informix inclui o cliente Microsoft para comunicar com computadores remotos de servidor Informix através de uma rede TCP/IP. Isto inclui as bases de dados de nuvem, tais como IBM Informix para o Windows em execução no Azure Virtualização e no local utilizando o gateway de dados no local de bases de dados. Consulte o [suportado lista](connectors-create-api-informix.md#supported-informix-platforms-and-versions) do IBM Informix plataformas e versões (deste tópico).

O conector suporta as seguintes operações de base de dados:

* Tabelas de base de dados de lista
* Ler uma linha utilizando SELECIONE
* Ler todas as linhas, SELECIONE a utilizar
* Adicionar uma linha utilizando INSERT
* Alterar uma linha com a ATUALIZAÇÃO
* Remover uma linha com a eliminação

Este tópico mostra como utilizar o conector numa aplicação lógica para processar operações de base de dados.

Para saber mais sobre Logic Apps, consulte o artigo [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="available-actions"></a>Ações disponíveis
Este conector suporta as seguintes ações de aplicação lógica:

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Lista de tabelas
Criar uma aplicação lógica em nenhuma operação é composta por vários passos realizados através do portal do Microsoft Azure.

Na sua aplicação lógica, pode adicionar uma ação para listar tabelas numa base de dados Informix. Esta ação instrui o conector para processar uma instrução de esquema Informix, tais como `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `InformixgetTables`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**.  
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva **informix** no **procure mais ações** editar caixa e, em seguida, selecione **Informix - Get de tabelas (pré-visualização)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. No **Informix - Get tabelas** painel de configuração, selecione **caixa de verificação** para ativar **ligar através do gateway de dados no local**. Tenha em atenção que as definições de alteração da nuvem no local.
   
   * Escreva o valor para **servidor**, no formato de endereço ou alias de número de porta de dois pontos. Por exemplo, digite `ibmserver01:9089`.
   * Escreva o valor para **base de dados**. Por exemplo, digite `nwind`.
   * Selecione o valor para **autenticação**. Por exemplo, seleccione **básico**.
   * Escreva o valor para **Username**. Por exemplo, digite `informix`.
   * Escreva o valor para **palavra-passe**. Por exemplo, digite `Password1`.
   * Selecione o valor para **Gateway**. Por exemplo, seleccione **datagateway01**.
7. Selecione **criar**e, em seguida, selecione **guardar**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. No **InformixgetTables** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
9. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_tables**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir uma lista de tabelas.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Criar ligações
Este conector suporta ligações de base de dados no local e na nuvem utilizando as seguintes propriedades de ligação. 

| Propriedade | Descrição |
| --- | --- |
| servidor |Necessário. Aceita um valor de cadeia representando um endereço de TCP/IP ou alias, no formato IPv4 ou IPv6, seguido (vírgula delimitada por) por um número de porta de TCP/IP. |
| base de dados |Necessário. Aceita um valor de cadeia representando um nome DRDA da base de dados relacional (RDBNAM). Informix aceita uma cadeia de 128 bytes (base de dados é conhecido como um nome de base de dados IBM Informix (dbname)). |
| Autenticação |Opcional. Aceita um valor de item de lista, Basic ou o Windows (kerberos). |
| o nome de utilizador |Necessário. Aceita um valor de cadeia. |
| palavra-passe |Necessário. Aceita um valor de cadeia. |
| gateway |Necessário. Aceita um valor de item de lista, que representa o gateway de dados no local, definido para as Logic Apps dentro do grupo de armazenamento. |

## <a name="create-the-on-premises-gateway-connection"></a>Criar o local de ligação do gateway
Este conector pode aceder a uma base de dados de Informix no local utilizando o gateway de dados no local. Consulte os tópicos de gateway para obter mais informações. 

1. No **Gateways** painel de configuração, selecione **caixa de verificação** para ativar **ligar através do gateway**. Consulte as definições de alteração da nuvem no local.
2. Escreva o valor para **servidor**, no formato de endereço ou alias de número de porta de dois pontos. Por exemplo, digite `ibmserver01:9089`.
3. Escreva o valor para **base de dados**. Por exemplo, digite `nwind`.
4. Selecione o valor para **autenticação**. Por exemplo, seleccione **básico**.
5. Escreva o valor para **Username**. Por exemplo, digite `informix`.
6. Escreva o valor para **palavra-passe**. Por exemplo, digite `Password1`.
7. Selecione o valor para **Gateway**. Por exemplo, seleccione **datagateway01**.
8. Selecione **criar** para continuar. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Criar a ligação em nuvem
Este conector pode aceder a uma nuvem de base de dados Informix. 

1. No **Gateways** painel de configuração, deixe o **caixa de verificação** desativado (unclicked) **ligar através do gateway**. 
2. Escreva o valor para **nome da ligação**. Por exemplo, digite `hisdemo2`.
3. Escreva o valor para **nome do servidor Informix**, no formato de endereço ou alias de número de porta de dois pontos. Por exemplo, digite `hisdemo2.cloudapp.net:9089`.
4. Escreva o valor para **nome de base de dados Informix**. Por exemplo, digite `nwind`.
5. Escreva o valor para **Username**. Por exemplo, digite `informix`.
6. Escreva o valor para **palavra-passe**. Por exemplo, digite `Password1`.
7. Selecione **criar** para continuar. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Obter todas as linhas, SELECIONE a utilizar
Pode criar uma ação de aplicação lógica para obter todas as linhas na tabela Informix. Esta ação instrui o conector para processar uma instrução Informix SELECIONE, tais como `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome** (por exemplo, "**InformixgetRows**"), **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva **informix** no **procure mais ações** editar caixa e, em seguida, selecione **Informix - Get linhas (pré-visualização)**.
6. No **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**.
7. No **ligações** painel de configuração, selecione **criar nova**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. No **Gateways** painel de configuração, deixe o **caixa de verificação** desativado (unclicked) **ligar através do gateway**.
   
   * Escreva o valor para **nome da ligação**. Por exemplo, digite `HISDEMO2`.
   * Escreva o valor para **nome do servidor Informix**, no formato de endereço ou alias de número de porta de dois pontos. Por exemplo, digite `HISDEMO2.cloudapp.net:9089`.
   * Escreva o valor para **nome de base de dados Informix**. Por exemplo, digite `NWIND`.
   * Escreva o valor para **Username**. Por exemplo, digite `informix`.
   * Escreva o valor para **palavra-passe**. Por exemplo, digite `Password1`.
9. Selecione **criar** para continuar.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
11. Opcionalmente, selecione **Mostrar opções avançadas** para especificar opções de consulta.
12. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. No **InformixgetRows** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
14. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir uma lista de linhas.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Adicionar uma linha utilizando INSERT
Pode criar uma ação de aplicação lógica para adicionar uma linha numa tabela Informix. Esta ação instrui o conector para processar uma instrução Informix INSERT, tais como `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `InformixinsertRow`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva **informix** no **procure mais ações** editar caixa e, em seguida, selecione **Informix - Inserir linha (pré-visualização)**.
6. No **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**. 
7. No **ligações** painel de configuração, selecione de selecionar uma ligação. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para as colunas todos requeridas (consulte asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, tipo `Area 99999`e escreva `102` para **REGIONID**. 
10. Selecione **Guardar**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. No **InformixinsertRow** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
12. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir a nova linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Obter uma linha utilizando SELECIONE
Pode criar uma ação de aplicação lógica para obter uma linha numa tabela Informix. Esta ação instrui o conector para processar uma instrução Informix SELECIONE onde, tais como `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `InformixgetRow`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva **informix** no **procure mais ações** editar caixa e, em seguida, selecione **Informix - Get linhas (pré-visualização)**.
6. No **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**. 
7. No **ligações** painel configurações, selecione para selecionar uma ligação existente. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para as colunas todos requeridas (consulte asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Opcionalmente, selecione **Mostrar opções avançadas** para especificar opções de consulta.
11. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. No **InformixgetRow** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
13. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir a linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Alterar uma linha com a ATUALIZAÇÃO
Pode criar uma ação de aplicação lógica para alterar uma linha numa tabela Informix. Esta ação instrui o conector para processar uma instrução de ATUALIZAÇÃO de Informix, tais como `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `InformixupdateRow`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva **informix** no **procure mais ações** editar caixa e, em seguida, selecione **Informix - a linha de atualização (pré-visualização)**.
6. No **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**. 
7. No **ligações** painel configurações, selecione para selecionar uma ligação existente. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para as colunas todos requeridas (consulte asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, tipo `Updated 99999`e escreva `102` para **REGIONID**. 
10. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. No **InformixupdateRow** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
12. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir a nova linha.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Remover uma linha com a eliminação
Pode criar uma ação de aplicação lógica para remover uma linha numa tabela Informix. Esta ação instrui o conector para processar uma instrução Informix eliminar, tais como `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `InformixdeleteRow`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva **informix** no **procure mais ações** editar caixa e, em seguida, selecione **Informix - Eliminar linha (pré-visualização)**.
6. No **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**. 
7. No **ligações** painel configurações, selecione uma ligação existente. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para as colunas todos requeridas (consulte asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. No **InformixdeleteRow** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
12. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir a linha eliminada.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Plataformas de Informix e versões suportados
Este conector suporta as seguintes versões do IBM Informix, quando configurado para suportar ligações de cliente distribuídos relacional da base de dados arquitetura (DRDA).

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/informix/). 

## <a name="next-steps"></a>Passos seguintes
[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explorar os outros conectores disponíveis em Logic Apps no nosso [lista APIs](apis-list.md).

