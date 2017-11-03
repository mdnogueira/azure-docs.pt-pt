---
title: "X12 mensagens para a integração do enterprise B2B - Azure Logic Apps | Microsoft Docs"
description: "Mensagens de Exchange X12 no formato EDI para integração do enterprise B2B com Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 1bfaa7b31bfed3ada22c83516839ebd95a351854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="exchange-x12-messages-for-enterprise-integration-with-logic-apps"></a>Mensagens de Exchange X12 para a integração empresarial com logic apps

Antes de pode trocar X12 mensagens para o Azure Logic Apps, tem de criar um X12 contrato e armazenar esse contrato na sua conta de integração. Eis os passos sobre como criar um X12 contrato.

> [!NOTE]
> Esta página abrange o X12 as funcionalidades para o Azure Logic Apps. Para obter mais informações, consulte [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Antes de começar

Segue-se os itens que precisa de:

* Um [conta integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) que já foi definida e associados à subscrição do Azure
* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) que são definidos na sua conta de integração e configurada com o X12 identificador em **identidades de negócio**    
* Necessária [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) para carregar para o seu [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md)

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md), [adicionar parceiros](logic-apps-enterprise-integration-partners.md)e tem um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que pretende utilizar, pode criar um X12 contrato seguindo estes passos.

## <a name="create-an-x12-agreement"></a>Criar um X12 contrato

1.  Inicie sessão no [portal do Azure](http://portal.azure.com "portal do Azure"). No menu à esquerda, selecione **mais serviços**. 

    > [!TIP]
    > Se não vir **mais serviços**, poderá ter de expanda o menu primeiro. Na parte superior do menu fechado, selecione **Mostrar menu**.

    ![No menu à esquerda, selecione "Mais serviços"](./media/logic-apps-enterprise-integration-x12/account-1.png)

2.  Na caixa de pesquisa, escreva "integração" como o filtro. Na lista de resultados, selecione **contas de automatização**.  

    ![Filtrar por "integração", selecione "Contas de automatização"](./media/logic-apps-enterprise-integration-x12/account-2.png)

3. No **contas de automatização** painel que abre, selecione a conta de integração em que pretende adicionar o contrato.
Se não vir quaisquer contas de automatização, [criar um primeiro](../logic-apps/logic-apps-enterprise-integration-accounts.md "tudo sobre contas de automatização").

    ![Selecione onde criar o contrato de conta de integração](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Selecione **descrição geral**, em seguida, selecione o **contratos** mosaico. Se não tiver um mosaico de contratos, adicione primeiro o mosaico. 

    ![Escolha o que mosaico "Contratos"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

5. No painel contratos que se abre, escolha **adicionar**.

    ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)     

6. Em **adicionar**, introduza um **nome** para o contrato. Para o tipo de contrato, selecione **X12**. Selecione o **anfitrião parceiro**, **identidade do anfitrião**, **convidado parceiro**, e **identidade de convidado** para o contrato. Para obter mais detalhes de propriedade, consulte a tabela neste passo.

    ![Forneça os detalhes do contrato](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Propriedade | Descrição |
    | --- | --- |
    | Nome |Nome do contrato |
    | Tipo de contrato | Deve ser X12 |
    | Parceiro de anfitrião |Um contrato tem de parceiro de um anfitrião e convidado. O parceiro de anfitrião representa a organização que configura o contrato. |
    | Identidade do anfitrião |Um identificador para o parceiro de anfitrião |
    | Parceiro de convidado |Um contrato tem de parceiro de um anfitrião e convidado. O parceiro de convidado representa a organização que está a decorrer empresas com o parceiro de anfitrião. |
    | Identidade de convidado |Um identificador para o parceiro de convidado |
    | Receber definições |Estas propriedades aplicam-se a todas as mensagens recebidas por um contrato. |
    | Enviar definições |Estas propriedades aplicam-se a todas as mensagens enviadas por um contrato. |  

  > [!NOTE]
  > Resolução de X12 contrato depende o qualificador do remetente e o identificador e o qualificador do recetor e o identificador definido no parceiro e a mensagem a receber de correspondência. Se alterar estes valores para o seu parceiro, atualizá o contrato.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar a forma como os identificadores de contrato receberam mensagens

Agora que definiu as propriedades de contrato, pode configurar como o presente contrato identifica e processa mensagens a receber foi recebidas pelo seu parceiro através deste contrato.

1.  Em **adicionar**, selecione **receber definições**.
Configure estas propriedades com base no seu contrato com o parceiro que trocas de mensagens consigo. Para descrições das propriedades, consulte as tabelas desta secção.

    **Receber definições** está organizado estas secções: identificadores de confirmação, esquemas, Envelopes, números de controlo, validações definições e interno.

2. Depois de terminar, certifique-se guardar as definições ao escolher **OK**.

O contrato está agora pronto para processar mensagens a receber em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

![Defina as propriedades de identificador](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propriedade | Descrição |
| --- | --- |
| ISA1 (qualificador de autorização) |Selecione o valor de qualificador de autorização na lista pendente. |
| ISA2 |Opcional. Introduza o valor de informações de autorização. Se o valor introduzido para ISA1 diferente 00, introduza um mínimo de um caráter alfanumérico e um máximo de 10. |
| ISA3 (qualificador de segurança) |Selecione o valor de qualificador de segurança da lista pendente. |
| ISA4 |Opcional. Introduza o valor de informações de segurança. Se o valor introduzido para ISA3 diferente 00, introduza um mínimo de um caráter alfanumérico e um máximo de 10. |

### <a name="acknowledgment"></a>Confirmação

![Defina as propriedades de confirmação](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Devolve uma confirmação técnica para o remetente intercâmbio |
| FA esperado |Devolve uma confirmação funcional para o remetente intercâmbio. Em seguida, selecione se pretende que o 997 ou 999 em que as confirmações, com base na versão de esquema |
| Incluir AK2/IK2 ciclo |Activa a geração de ciclos de AK2 no funcionais em que as confirmações para conjuntos de transação aceite |

### <a name="schemas"></a>Esquemas

Selecione um esquema para cada tipo de transação (ST1) e a aplicação remetente (GS2). O pipeline de receção disassembles a mensagem a receber, os valores correspondentes para ST1 e GS2 na mensagem a receber com os valores que aqui definida e o esquema da mensagem a receber com o esquema definido aqui.

![Selecione o esquema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione o X12 versão |
| Tipo de transação (ST01) |Selecione o tipo de transação |
| Aplicação do remetente (GS02) |Selecione a aplicação do remetente |
| Esquema |Selecione o ficheiro de esquema que pretende utilizar. Esquemas são adicionadas à sua conta de integração. |

> [!NOTE]
> Configurar necessários [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que é carregado para o [conta integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especifique o separador de um conjunto de transação: escolher identificador Standard ou o separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Propriedade | Descrição |
| --- | --- |
| Utilização de ISA11 |Especifica o separador a utilizar um conjunto de transação: <p>Selecione **identificador padrão** para utilizar um ponto (.) para notação decimal, em vez da notação decimal do documento de entrada no EDI receber pipeline. <p>Selecione **repetição separador** para especificar o separador para repetido ocorrências de um elemento de dados simples ou uma estrutura de dados repetido. Por exemplo, normalmente, o carat (^) é utilizado como o separador de repetição. Para esquemas HIPAA, só pode utilizar o carat. |

### <a name="control-numbers"></a>Números de controlo

![Selecione como lidar com os duplicados número de controlo](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propriedade | Descrição |
| --- | --- |
| Não permitir o número de controlo intercâmbio duplicados |Bloquear interchanges duplicados. Verifica o número de controlo do intercâmbio (ISA13) para o número de controlo do intercâmbio recebida. Se for detetada uma correspondência, o pipeline de receção não processou o intercâmbio. Pode especificar o número de dias para efetuar a verificação, concedendo um valor para *procurar ISA13 duplicado (dias)*. |
| Não permitir duplicados de número de controlo de grupo |Bloco interchanges com números de controlo do grupo de duplicados. |
| Não permitir duplicados número de controlo do conjunto de transação |Bloco interchanges com números de controlo do conjunto de transação duplicado. |

### <a name="validations"></a>Validações

![Definir propriedades de validação para mensagens recebidas](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Quando concluir cada linha de validação, o outro é adicionado automaticamente. Se não especificar quaisquer regras, validação utiliza a linha "Predefinida".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Efetue a validação de EDI em tipos de dados, tal como definido pelo esquema EDI propriedades, restrições de comprimento, os elementos de dados vazio e separadores à direita. |
| Validação expandida |Se o tipo de dados não EDI, é o requisito de elemento de dados e validação permitido repetição, enumerações e dados de validação de comprimento de elemento (mínimo/máximo). |
| Permitir zeros à esquerda/à direita |Manter quaisquer adicionais esquerda ou à direita zero e carateres de espaço. Não remova estes carateres. |
| Compactar zeros à esquerda/à direita |Remova zero direita ou à esquerda e carateres de espaço. |
| À direita de separador de política |Gere separadores à direita. <p>Selecione **não permitido** para proibir delimitadores à direita e de separadores no intercâmbio recebido. Se tiver o intercâmbio delimitadores à direita e de separadores, o intercâmbio está declarado não válido. <p>Selecione **opcional** para aceitar interchanges com ou sem os delimitadores à direita e de separadores. <p>Selecione **obrigatório** quando o intercâmbio tem de ter os delimitadores à direita e de separadores. |

### <a name="internal-settings"></a>Definições internas

![Selecione as definições de internas](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propriedade | Descrição |
| --- | --- |
| Converter o formato decimal implícito "Nn" para um valor numérico de 10 base |Converte um número EDI for especificado com o formato "Nn" num valor numérico base 10 |
| Criar etiquetas XML vazias se separadores à direita são permitidos |Selecione esta caixa de verificação para que o remetente de intercâmbio incluem as etiquetas XML vazias para separadores à direita. |
| Divisão intercâmbio como conjuntos de transação - suspender conjuntos de transação com o erro|Analisa a cada transação definida num intercâmbio para um documento XML separado aplicando o envelope adequado para o conjunto de transação. Suspende apenas as transações em que a validação falha. |
| Intercâmbio de divisão como conjuntos de transação - suspender intercâmbio no erro|Analisa a cada transação definida num intercâmbio para um documento XML separado aplicando o envelope adequado. Suspende intercâmbio completo quando um ou mais conjuntos de transação o intercâmbio falharem a validação. | 
| Preservar intercâmbio - suspender conjuntos de transação com o erro |Deixa o intercâmbio intactas, cria um documento XML para o intercâmbio de batch de todo. Suspende apenas os conjuntos de transação que não obedeçam a validação, ao continuar a processar todos os outros conjuntos de transação. |
| Preservar intercâmbio - suspender intercâmbio no erro |Deixa o intercâmbio intactas, cria um documento XML para o intercâmbio de batch de todo. Suspende o intercâmbio completo quando um ou mais conjuntos de transação o intercâmbio falharem a validação. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar como o seu contrato envia mensagens

Pode configurar a forma como este contrato identifica e processa mensagens de saída que enviar para o seu parceiro através deste contrato.

1.  Em **adicionar**, selecione **enviar definições**.
Configure estas propriedades com base no seu contrato com o seu parceiro a quem trocas de mensagens consigo. Para descrições das propriedades, consulte as tabelas desta secção.

    **Enviar definições** está organizado estas secções: identificadores, reconhecimento, esquemas, Envelopes, conjuntos de carateres e separadores, números de controlo e a validação.

2. Depois de terminar, certifique-se guardar as definições ao escolher **OK**.

O contrato está agora pronto para processar mensagens de saída que está em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

![Defina as propriedades de identificador](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propriedade | Descrição |
| --- | --- |
| Qualificador de autorização (ISA1) |Selecione o valor de qualificador de autorização na lista pendente. |
| ISA2 |Introduza o valor de informações de autorização. Se este valor é diferente de 00, em seguida, introduza um mínimo de um caráter alfanumérico e um máximo de 10. |
| Qualificador de segurança (ISA3) |Selecione o valor de qualificador de segurança da lista pendente. |
| ISA4 |Introduza o valor de informações de segurança. Se este valor é diferente de 00, para a caixa de texto valor (ISA4), em seguida, introduza um mínimo de um valor alfanumérico e um máximo de 10. |

### <a name="acknowledgment"></a>Confirmação

![Defina as propriedades de confirmação](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Devolva uma confirmação técnica (TA1) para o remetente intercâmbio. Esta definição especifica que o parceiro de anfitrião que está a enviar a mensagem pedidos uma confirmação do parceiro no contrato de convidado. Estes em que as confirmações são esperadas pelo parceiro de anfitrião com base nas definições de receber o contrato. |
| FA esperado |Devolva uma confirmação funcional (FA) para o remetente intercâmbio. Selecione se pretende que as confirmações 997 ou 999, com base nas versões de esquema que estiver a trabalhar com. Estes em que as confirmações são esperadas pelo parceiro de anfitrião com base nas definições de receber o contrato. |
| Versão FA |Selecione a versão de FA |

### <a name="schemas"></a>Esquemas

![Selecione o esquema a utilizar](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione o X12 versão |
| Tipo de transação (ST01) |Selecione o tipo de transação |
| ESQUEMA |Selecione o esquema a utilizar. Esquemas estão localizadas na sua conta de integração. Se selecionar esquema pela primeira vez, este configura automaticamente a versão e transação tipo  |

> [!NOTE]
> Configurar necessários [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que é carregado para o [conta integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especifique o separador de um conjunto de transação: escolher identificador Standard ou o separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propriedade | Descrição |
| --- | --- |
| Utilização de ISA11 |Especifica o separador a utilizar um conjunto de transação: <p>Selecione **identificador padrão** para utilizar um ponto (.) para notação decimal, em vez da notação decimal do documento de entrada no EDI receber pipeline. <p>Selecione **repetição separador** para especificar o separador para repetido ocorrências de um elemento de dados simples ou uma estrutura de dados repetido. Por exemplo, normalmente, o carat (^) é utilizado como o separador de repetição. Para esquemas HIPAA, só pode utilizar o carat. |

### <a name="control-numbers"></a>Números de controlo

![Especificar as propriedades de número de controlo](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propriedade | Descrição |
| --- | --- |
| Número de versão de controlo (ISA12) |Selecione a versão do X12 padrão |
| Indicador de utilização (ISA15) |Selecione o contexto de um intercâmbio.  Os valores são informações, dados de produção, ou dados de teste |
| Esquema |Gera os segmentos GS e ST de um intercâmbio codificado X12 que envia para o Pipeline de envio |
| GS1 |Opcional, selecione um valor para o código funcional na lista pendente |
| GS2 |Remetente de aplicação opcional, |
| GS3 |Opcional, recetor de aplicação |
| GS4 |Opcional, selecione CCYYMMDD ou YYMMDD |
| GS5 |Opcional, selecione HHMM, HHMMSS ou HHMMSSdd |
| GS7 |Opcional, selecione um valor para a responsável Agência na lista pendente |
| GS8 |Opcional, versão do documento |
| Intercâmbio do número de controlo (ISA13) |Necessário, introduza um intervalo de valores para o número de controlo do intercâmbio. Introduza um valor numérico com um mínimo de 1 e um máximo de 999999999 |
| Número de controlo de grupo (GS06) |Necessário, introduza um intervalo de números para o número de controlo de grupo. Introduza um valor numérico com um mínimo de 1 e um máximo de 999999999 |
| Número de controlo de conjunto de transação (ST02) |Necessário, introduza um intervalo de números para o número de controlo de conjunto de transação. Introduza um intervalo de valores numéricos, com um mínimo de 1 e um máximo de 999999999 |
| Prefixo |Opcional, designado para o intervalo de números de controlo de conjunto de transação utilizados na confirmação. Introduza um valor numérico para os campos de dois média e um valor alfanumérico (se pretendido) para os campos de prefixo e o sufixo. Os campos central são necessários e contém os valores mínimos e máximo para o número de controlo |
| Sufixo |Opcional, designado para o intervalo de números de controlo de conjunto de transação utilizados numa confirmação. Introduza um valor numérico para os campos de dois média e um valor alfanumérico (se pretendido) para os campos de prefixo e o sufixo. Os campos central são necessários e contém os valores mínimos e máximo para o número de controlo |

### <a name="character-sets-and-separators"></a>Conjuntos de caráter e separadores

Que o conjunto de caracteres, pode introduzir um conjunto de delimitadores diferente para cada tipo de mensagem. Se um conjunto de carateres não está especificado para um esquema de message especificada, o conjunto de carateres predefinido é utilizado.

![Especifique os delimitadores para tipos de mensagens](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Propriedade | Descrição |
| --- | --- |
| Caráter definido para ser utilizado |Para validar as propriedades, selecione o X12 conjunto de carateres. As opções são UTF8, Basic e expandidas. |
| Esquema |Selecione um esquema a partir da lista pendente. Depois de concluir cada linha, é automaticamente adicionada uma nova linha. Para o esquema selecionado, selecione o conjunto de separadores que pretende utilizar, com base nas seguintes descrições de separador. |
| Tipo de entrada |Selecione um tipo de entrada da lista pendente. |
| Separador de componente |Para separar os elementos de dados composto, introduza um único caráter. |
| Separador de elemento de dados |Para separar os elementos de dados simples dentro de elementos de dados composto, introduza um único caráter. |
| Caráter de substituição |Introduza um caráter de substituição utilizado para substituir todos os carateres de separador de dados payload ao gerar a saída X12 mensagem. |
| Segmento terminador |Para indicar o fim de um segmento EDI, introduza um único caráter. |
| Sufixo |Selecione o caráter que é utilizado com o identificador de segmento. Se designar um sufixo, o elemento de dados do segmento terminador pode estar vazio. Se o terminador de segmento for deixado em branco, tem de designar um sufixo. |

> [!TIP]
> Para fornecer valores de caráter especial, editar o contrato como JSON e forneça o valor de ASCII para o caráter especial.

### <a name="validation"></a>Validação

![Definir propriedades de validação para o envio de mensagens](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Quando concluir cada linha de validação, o outro é adicionado automaticamente. Se não especificar quaisquer regras, validação utiliza a linha "Predefinida".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Efetue a validação de EDI em tipos de dados, tal como definido pelo esquema EDI propriedades, restrições de comprimento, os elementos de dados vazio e separadores à direita. |
| Validação expandida |Se o tipo de dados não EDI, é o requisito de elemento de dados e validação permitido repetição, enumerações e dados de validação de comprimento de elemento (mínimo/máximo). |
| Permitir zeros à esquerda/à direita |Manter quaisquer adicionais esquerda ou à direita zero e carateres de espaço. Não remova estes carateres. |
| Compactar zeros à esquerda/à direita |Remova esquerda ou à direita zero carateres. |
| À direita de separador de política |Gere separadores à direita. <p>Selecione **não permitido** para proibir delimitadores à direita e de separadores no intercâmbio enviado. Se tiver o intercâmbio delimitadores à direita e de separadores, o intercâmbio está declarado não válido. <p>Selecione **opcional** enviar interchanges com ou sem os delimitadores à direita e de separadores. <p>Selecione **obrigatório** se o intercâmbio enviado tem de ter os delimitadores à direita e de separadores. |

## <a name="find-your-created-agreement"></a>Encontrar o contrato criado

1.  Depois de concluir a definição em todas as propriedades de contrato, o **adicionar** painel, escolha **OK** para concluir a criação do seu contrato e regressar ao painel de conta da integração.

    O contrato recém-adicionado agora aparece na sua **contratos** lista.

2.  Também pode ver os contratos na descrição geral da sua integração conta. No painel da conta de integração, escolha **descrição geral**, em seguida, selecione o **contratos** mosaico.

    ![Escolha o que mosaico "Contratos" para ver todos os contratos](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Ver o swagger
Consulte o [swagger detalhes](/connectors/x12/). 

## <a name="learn-more"></a>Saiba mais
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise")  

