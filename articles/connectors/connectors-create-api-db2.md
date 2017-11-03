---
title: Adicione o conector DB2 nas suas Logic Apps | Microsoft Docs
description: "Descrição geral do conector DB2 com parâmetros de REST API"
services: 
documentationcenter: 
author: gplarsen
manager: erikre
editor: 
tags: connectors
ms.assetid: 1c6b010c-beee-496d-943a-a99e168c99aa
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen; ladocs
ms.openlocfilehash: 4501b3d9a2fdc00582596cb907f7130591e4782e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-db2-connector"></a>Começar a utilizar o conector DB2
Conector do Microsoft para DB2 liga as Logic Apps aos recursos armazenados numa base de dados IBM DB2. Este conector inclui o cliente Microsoft para comunicar com computadores de servidor DB2 remotos através de uma rede TCP/IP. Isto inclui as bases de dados de nuvem, como IBM Bluemix dashDB ou IBM DB2 para o Windows em execução no Azure Virtualização e no local utilizando o gateway de dados no local de bases de dados. Consulte o [suportado lista](connectors-create-api-db2.md#supported-db2-platforms-and-versions) do IBM DB2 plataformas e versões (deste tópico).

O conector DB2 suporta as seguintes operações de base de dados:

* Tabelas de base de dados de lista
* Ler uma linha utilizando SELECIONE
* Ler todas as linhas, SELECIONE a utilizar
* Adicionar uma linha utilizando INSERT
* Alterar uma linha com a ATUALIZAÇÃO
* Remover uma linha com a eliminação

Este tópico mostra como utilizar o conector numa aplicação lógica para processar operações de base de dados.

Para saber mais sobre Logic Apps, consulte o artigo [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="available-actions"></a>Ações disponíveis
O conector DB2 suporta as seguintes ações de aplicação lógica:

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Lista de tabelas
Criar uma aplicação lógica em nenhuma operação é composta por vários passos realizados através do portal do Microsoft Azure.

Na sua aplicação lógica, pode adicionar uma ação para listar as tabelas na base de dados DB2. A ação instrui o conector para processar uma instrução de esquema DB2, tais como `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `Db2getTables`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, defina o **intervalo** para o tipo **7**.  
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva `db2` no **procure mais ações** editar caixa e, em seguida, selecione **DB2 - Get de tabelas (pré-visualização)**.
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. No **DB2 - Get tabelas** painel de configuração, selecione **caixa de verificação** para ativar **ligar através do gateway de dados no local**. Tenha em atenção que as definições de alteração da nuvem no local.
   
   * Escreva o valor para **servidor**, no formato de endereço ou alias de número de porta de dois pontos. Por exemplo, digite `ibmserver01:50000`.
   * Escreva o valor para **base de dados**. Por exemplo, digite `nwind`.
   * Selecione o valor para **autenticação**. Por exemplo, seleccione **básico**.
   * Escreva o valor para **Username**. Por exemplo, digite `db2admin`.
   * Escreva o valor para **palavra-passe**. Por exemplo, digite `Password1`.
   * Selecione o valor para **Gateway**. Por exemplo, seleccione **datagateway01**.
7. Selecione **criar**e, em seguida, selecione **guardar**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. No **Db2getTables** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
9. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_tables**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir uma lista de tabelas.
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Criar ligações
Este conector suporta ligações para as bases de dados alojados no local e na nuvem utilizando as seguintes propriedades de ligação. 

| Propriedade | Descrição |
| --- | --- |
| servidor |Necessário. Aceita um valor de cadeia que representa um endereço de TCP/IP ou alias, no formato IPv4 ou IPv6, seguido (delimitado por vírgula) por um número de porta de TCP/IP. |
| base de dados |Necessário. Aceita um valor de cadeia que representa um nome DRDA da base de dados relacional (RDBNAM). DB2 para z/SO aceita uma cadeia de 16 bytes (base de dados é conhecido como um IBM DB2 para a localização de SO/z). DB2 para i5/SO aceita uma cadeia de carateres de 18 byte (base de dados é conhecido como um IBM DB2 para i relacional base de dados). DB2 para LUW aceita uma cadeia de 8 bytes. |
| Autenticação |Opcional. Aceita um valor de item de lista, Basic ou o Windows (kerberos). |
| o nome de utilizador |Necessário. Aceita um valor de cadeia. DB2 para z/SO aceita uma cadeia de 8 bytes. DB2 para i aceita uma cadeia de 10 bytes. DB2 para Linux ou UNIX aceita uma cadeia de 8 bytes. DB2 para Windows aceita uma cadeia de 30-byte. |
| palavra-passe |Necessário. Aceita um valor de cadeia. |
| gateway |Necessário. Aceita um valor de item de lista, que representa o gateway de dados no local, definido para as Logic Apps dentro do grupo de armazenamento. |

## <a name="create-the-on-premises-gateway-connection"></a>Criar o local de ligação do gateway
Este conector pode aceder a uma base de dados de DB2 no local utilizando o gateway no local. Consulte os tópicos de gateway para obter mais informações. 

1. No **Gateways** painel de configuração, selecione **caixa de verificação** para ativar **ligar através do gateway**. Tenha em atenção que as definições de alteração da nuvem no local.
2. Escreva o valor para **servidor**, no formato de endereço ou alias de número de porta de dois pontos. Por exemplo, digite `ibmserver01:50000`.
3. Escreva o valor para **base de dados**. Por exemplo, digite `nwind`.
4. Selecione o valor para **autenticação**. Por exemplo, seleccione **básico**.
5. Escreva o valor para **Username**. Por exemplo, digite `db2admin`.
6. Escreva o valor para **palavra-passe**. Por exemplo, digite `Password1`.
7. Selecione o valor para **Gateway**. Por exemplo, seleccione **datagateway01**.
8. Selecione **criar** para continuar. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Criar a ligação em nuvem
Este conector pode aceder a uma base de dados de nuvem DB2. 

1. No **Gateways** painel de configuração, deixe o **caixa de verificação** desativado (unclicked) **ligar através do gateway**. 
2. Escreva o valor para **nome da ligação**. Por exemplo, digite `hisdemo2`.
3. Escreva o valor para **nome do servidor DB2**, no formato de endereço ou alias de número de porta de dois pontos. Por exemplo, digite `hisdemo2.cloudapp.net:50000`.
4. Escreva o valor para **nome de base de dados DB2**. Por exemplo, digite `nwind`.
5. Escreva o valor para **Username**. Por exemplo, digite `db2admin`.
6. Escreva o valor para **palavra-passe**. Por exemplo, digite `Password1`.
7. Selecione **criar** para continuar. 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Obter todas as linhas, SELECIONE a utilizar
Pode definir uma ação de aplicação lógica para obter todas as linhas numa tabela DB2. Isto indica que o conector para processar uma instrução DB2 SELECIONE, tais como `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `Db2getRows`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva `db2` no **procure mais ações** editar caixa e, em seguida, selecione **DB2 - Get linhas (pré-visualização)**.
6. No **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**.
7. No **ligações** painel de configuração, selecione **criar nova**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. No **Gateways** painel de configuração, deixe o **caixa de verificação** desativado (unclicked) **ligar através do gateway**.
   
   * Escreva o valor para **nome da ligação**. Por exemplo, digite `HISDEMO2`.
   * Escreva o valor para **nome do servidor DB2**, no formato de endereço ou alias de número de porta de dois pontos. Por exemplo, digite `HISDEMO2.cloudapp.net:50000`.
   * Escreva o valor para **nome de base de dados DB2**. Por exemplo, digite `NWIND`.
   * Escreva o valor para **Username**. Por exemplo, digite `db2admin`.
   * Escreva o valor para **palavra-passe**. Por exemplo, digite `Password1`.
9. Selecione **criar** para continuar.
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
11. Opcionalmente, selecione **Mostrar opções avançadas** para especificar opções de consulta.
12. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. No **Db2getRows** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
14. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir uma lista de linhas.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Adicionar uma linha utilizando INSERT
Pode definir uma ação de aplicação lógica para adicionar uma linha numa tabela DB2. Esta ação instrui o conector para processar uma instrução DB2 INSERT, tais como `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `Db2insertRow`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva **db2** no **procure mais ações** editar caixa e, em seguida, selecione **DB2 - Inserir linha (pré-visualização)**.
6. No **DB2 - Inserir linha (pré-visualização)** ação, selecione **Alterar ligação**. 
7. No **ligações** painel de configuração, selecione uma ligação. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para as colunas todos requeridas (consulte asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, tipo `Area 99999`e escreva `102` para **REGIONID**. 
10. Selecione **Guardar**.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. No **Db2insertRow** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
12. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir a nova linha.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Obter uma linha utilizando SELECIONE
Pode definir uma ação de aplicação lógica para obter uma linha numa tabela DB2. Esta ação instrui o conector para processar uma instrução DB2 SELECIONE onde, tais como `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome** (por exemplo, "**Db2getRow**"), **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista. 
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva **db2** no **procure mais ações** editar caixa e, em seguida, selecione **DB2 - Get linhas (pré-visualização)**.
6. No **obter linhas (pré-visualização)** ação, selecione **Alterar ligação**. 
7. No **ligações** painel configurações, selecione uma ligação existente. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para as colunas todos requeridas (consulte asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Opcionalmente, selecione **Mostrar opções avançadas** para especificar opções de consulta.
11. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. No **Db2getRow** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
13. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir a linha.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Alterar uma linha com a ATUALIZAÇÃO
Pode definir uma ação de aplicação lógica para alterar uma linha numa tabela DB2. Esta ação instrui o conector para processar uma instrução de ATUALIZAÇÃO de DB2, tais como `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `Db2updateRow`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista.
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, escreva **db2** no **procure mais ações** editar caixa e, em seguida, selecione **DB2 - a linha de atualização (pré-visualização)**.
6. No **DB2 - a linha de atualização (pré-visualização)** ação, selecione **Alterar ligação**. 
7. No **ligações** painel configurações, selecione para selecionar uma ligação existente. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para as colunas todos requeridas (consulte asterisco vermelho). Por exemplo, digite `99999` para **AREAID**, tipo `Updated 99999`e escreva `102` para **REGIONID**. 
10. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. No **Db2updateRow** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
12. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir a nova linha.
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Remover uma linha com a eliminação
Pode definir uma ação de aplicação lógica para remover uma linha numa tabela DB2. Esta ação instrui o conector para processar uma instrução DB2 eliminar, tais como `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**.
2. Introduza o **nome**, tais como `Db2deleteRow`, **subscrição**, **grupo de recursos**, **localização**, e **plano do App Service**. Selecione **afixar ao dashboard**e, em seguida, selecione **criar**.

### <a name="add-a-trigger-and-action"></a>Adicionar um acionador e ação
1. No **Designer de aplicações lógicas**, selecione **LogicApp em branco** no **modelos** lista. 
2. No **acionadores** lista, selecione **periodicidade**. 
3. No **periodicidade** acionador, selecione **editar**, selecione **frequência** pendente para selecionar **dia**e, em seguida, selecione **intervalo** para o tipo **7**. 
4. Selecione o **+ novo passo** caixa e, em seguida, selecione **adicionar uma ação**.
5. No **ações** lista, selecione **db2** no **procure mais ações** editar caixa e, em seguida, selecione **DB2 - Eliminar linha (pré-visualização)**.
6. No **DB2 - Eliminar linha (pré-visualização)** ação, selecione **Alterar ligação**. 
7. No **ligações** painel configurações, selecione uma ligação existente. Por exemplo, seleccione **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. No **nome da tabela** lista, selecione o **seta para baixo**e, em seguida, selecione **área**.
9. Introduza valores para as colunas todos requeridas (consulte asterisco vermelho). Por exemplo, digite `99999` para **AREAID**. 
10. Selecione **Guardar**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. No **Db2deleteRow** painel, dentro de **executa todos os** lista em **resumo**, selecione o item listado primeiro (executar mais recente).
12. No **aplicação lógica executar** painel, selecione **detalhes da execução**. Dentro do **ação** lista, selecione **Get_rows**. Ver o valor para **estado**, que deve ser **com êxito**. Selecione o **ligação entradas** para ver as entradas. Selecione o **saídas ligação**e ver as saídas; que deve incluir a linha eliminada.
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>Plataformas de DB2 e versões suportados
Este conector suporta nas seguintes plataformas IBM DB2 e versões, bem como IBM DB2 produtos compatíveis (por exemplo, o IBM Bluemix dashDB) que suportam o Gestor de acesso SQL (SQLAM) versão 10 e 11 da distribuídas relacional da base de dados arquitetura (DRDA):

* IBM DB2 para z/SO 11.1
* IBM DB2 para z/SO 10.1
* IBM DB2 para i 7.3
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/db2/). 

## <a name="next-steps"></a>Passos seguintes
[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explorar os outros conectores disponíveis em Logic Apps no nosso [lista APIs](apis-list.md).

