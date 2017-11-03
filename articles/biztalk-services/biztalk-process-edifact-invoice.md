---
title: 'Tutorial: Processar faturas EDIFACT utilizando os BizTalk Services do Azure | Microsoft Docs'
description: "Como criar e configurar a aplicação de caixa de conector ou a API e utilizá-lo numa aplicação lógica no App Service do Azure"
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: 4597ee28e4c3b797c0ab050b21a126a95d9e8191
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Tutorial: Processo EDIFACT faturas utilizando os BizTalk Services do Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Pode utilizar o Portal de serviços de BizTalk para configurar e implementar X12 e contratos EDIFACT. Neste tutorial, vamos ver como criar um contrato de EDIFACT para trocar faturas entre parceiros comerciais. Este tutorial foi escrito em torno de uma solução de negócio ponto-a-ponto que envolve dois parceiros comerciais, Northwind e Contoso trocar mensagens EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>Exemplo com base neste tutorial
Este tutorial foi escrito em torno de uma amostra **enviar EDIFACT faturas utilizando os BizTalk Services**, que está disponível para transferência a partir de [Galeria de códigos do MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Pode utilizar o exemplo e seguir este tutorial para compreender a forma como foi criado o exemplo. Em alternativa, pode utilizar este tutorial para criar a sua própria solução zero cópia de segurança. Este tutorial destina-se para a segunda abordagem para que compreende como esta solução foi criada. Além disso, quanto possível, o tutorial é consistente com o exemplo e utiliza os mesmos nomes para os artefactos (por exemplo, esquemas, transformações) como utilizadas no exemplo.  

> [!NOTE]
> Porque esta solução envolve a enviar uma mensagem a partir de uma bridge EAI para uma bridge EDI, reutiliza a [BizTalk Services Bridge encadeamento exemplo](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) exemplo.  
> 
> 

## <a name="what-does-the-solution-do"></a>O que faz a solução?
Nesta solução, Northwind recebe faturas EDIFACT da Contoso. Estes faturas não estão no formato padrão de EDI. Por isso, antes de enviar a fatura para Northwind, tem de ser transformado para um documento de fatura (também denominada INVOIC) EDIFACT. Na receção, Northwind tem de processar a fatura EDIFACT e devolver uma mensagem de controlo (também denominada CONTRL) para Contoso.

![][1]  

Para alcançar este cenário de negócio, a Contoso utiliza funcionalidades fornecidas com os BizTalk Services do Microsoft Azure.

* A Contoso utiliza pontes EAI para transformar a fatura original para EDIFACT INVOIC.
* A bridge EAI, em seguida, envia a mensagem para uma bridge de envio EDI implementada como parte de um contrato configurado no Portal de serviços do BizTalk.
* Bridge de envio EDI processa a INVOIC EDIFACT e encaminha o mesmo para Northwind.
* Depois de receber a fatura, Northwind devolve um CONTRL da mensagem para o EDI recebe bridge implementada como parte do contrato.  

> [!NOTE]
> Opcionalmente, esta solução também demonstra como utilizar a criação de batches de mensagens em fila para enviar as faturas em lotes, em vez de enviarem cada fatura separadamente.  
> 
> 

Para concluir o cenário, iremos utilizar filas do Service Bus para enviar fatura da Contoso Northwind ou receber a confirmação da Northwind. Estas filas podem ser criadas utilizando uma aplicação de cliente, que está disponível como uma transferência e está incluída no pacote de exemplo que está disponível como parte deste tutorial.  

## <a name="prerequisites"></a>Pré-requisitos
* Tem de ter um espaço de nomes do Service Bus. Para obter instruções sobre como criar um espaço de nomes, consulte [como: criar ou modificar um espaço de nomes de serviço do Service Bus](https://msdn.microsoft.com/library/azure/hh674478.aspx). Informe-nos partem do princípio de que já tem um espaço de nomes do Service Bus aprovisionado, denominado **edifactbts**.
* Tem de ter uma subscrição do BizTalk Services. Para obter instruções, consulte [cria um BizTalk Service através do portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=302280). Para este tutorial, vamos assumir tiver uma subscrição do BizTalk Services, denominada **contosowabs**.
* Registe a sua subscrição do BizTalk Services no Portal de serviços do BizTalk. Para obter instruções, consulte [registar uma implementação de serviço BizTalk no Portal de serviços BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)
* Tem de ter o Visual Studio instalado.
* Tem de ter o SDK dos BizTalk Services instalado. Pode transferir o SDK do [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Passo 1: Criar as filas do Service Bus
Esta solução utiliza as filas do Service Bus para trocar mensagens entre parceiros comerciais. Contoso e Northwind enviar mensagens para as filas de onde os pontes EAI e/ou EDI aceder aos mesmos. Para esta solução, precisa de três filas do Service Bus:

* **northwindreceive** – Northwind recebe a fatura da Contoso através desta fila.
* **contosoreceive** – Contoso recebe a confirmação da Northwind através desta fila.
* **suspenso** – todas as mensagens suspensos são encaminhadas para esta fila. As mensagens são suspensas se falharem durante o processamento.

Pode criar estas filas do Service Bus, utilizando uma aplicação de cliente incluída no pacote de exemplo.  

1. A localização onde transferiu o exemplo, abra **Tutorial enviar faturas utilizando BizTalk Services EDI Bridges.sln**.
2. Prima **F5** para criar e iniciar o **Tutorial cliente** aplicação.
3. No ecrã, introduza o espaço de nomes de ACS de barramento de serviço, o nome do emissor e a chave do emissor.
   
   ![][2]  
4. Uma caixa de mensagem pede ao utilizador que serão criadas três filas no seu espaço de nomes do Service Bus. Clique em **OK**.
5. Deixe o cliente do Tutorial em execução. Abrir, clique em **Service Bus** > ***o espaço de nomes do Service Bus*** > **filas**e certifique-se de que as três filas são criadas.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Passo 2: Criar e implementar o contrato de parceiro comercial
Crie um contrato de parceiro comercial entre Contoso e Northwind. Um contrato de parceiro comercial define um contrato de compromissos entre os parceiros de dois negócios, tais como o esquema de mensagem a utilizar, o mensagens de protocolo a utilizar, etc. Um contrato de parceiro comercial inclui duas pontes EDI, um para enviar mensagens para comerciais parceiros (chamado de **bridge EDI enviar**) e outra para receber mensagens de parceiros de negociação (chamado o **EDI receber bridge**).

No contexto desta solução, a bridge de envio EDI corresponde ao lado de envio do contrato e é utilizada para enviar a fatura EDIFACT da Contoso para Northwind. Da mesma forma, a bridge de receção EDI corresponde ao do lado da receção do contrato e é utilizado para confirmações de receção da Northwind.  

### <a name="create-the-trading-partners"></a>Criar os parceiros comerciais
Para começar, crie parceiros comerciais para Contoso e Northwind.  

1. No Portal de serviços do BizTalk, no **parceiros** separador, clique em **adicionar**.
2. Na página do novo parceiro, introduza **Contoso** como um nome de parceiro e, em seguida, clique em **guardar**.
3. Repita o passo para criar o segundo parceiro, **Northwind**.  

### <a name="create-the-agreement"></a>Criar o contrato
Contratos de parceiros comerciais são criados entre perfis de negócio da comerciais parceiros. Esta solução utiliza os perfis de parceiro predefinidos que são criados automaticamente quando os parceiros foi criada.  

1. No Portal de serviços do BizTalk, clique em **contratos** > **adicionar**.
2. No novo contrato **definições gerais** página, especifique os valores, conforme mostrado na imagem abaixo e, em seguida, clique em **continuar**.
   
   ![][3]  
   
   Depois de clicar em **continuar**, dois separadores, **receber definições** e **enviar definições** fique disponível.
3. Crie o contrato de envio entre Contoso e Northwind. Este contrato é regida pelas como Contoso envia a fatura EDIFACT para Northwind.
   
   1. Clique em **enviar definições**.
   2. Manter os valores predefinidos no **URL de entrada**, **transformar**, e **Batching** separadores.
   3. No **protocolo** separador no **esquemas** secção, carregue o **EFACT_D93A_INVOIC.xsd** esquema. Este esquema está disponível com o pacote de exemplo.
      
      ![][4]  
   4. No **transporte** separador, especifique os detalhes para as filas do Service Bus. Para o contrato de lado de envio, utilizamos o **northwindreceive** fila para enviar a fatura EDIFACT para Northwind e o **suspenso** fila para encaminhar as mensagens que falharem durante o processamento e são suspenso. Criou estes filas em **passo 1: criar as filas do Service Bus** (deste tópico).
      
      ![][5]  
      
      Em **transporte definições > tipo de transporte** e **definições de mensagens de suspensão > tipo de transporte**, selecione de Service Bus do Azure e forneça os valores, conforme mostrado na imagem.
4. Crie o contrato de receção entre Contoso e Northwind. Este contrato é regida pelas como Contoso recebe a confirmação da Northwind.
   
   1. Clique em **receber definições**.
   2. Manter os valores predefinidos no **transporte** e **transformar** separadores.
   3. No **protocolo** separador no **esquemas** secção, carregue o **EFACT_4.1_CONTRL.xsd** esquema. Este esquema está disponível com o pacote de exemplo.
   4. No **rota** separador, criar um filtro para garantir que apenas as confirmações da Northwind são encaminhadas para Contoso. Em **rota definições**, clique em **adicionar** para criar o filtro de encaminhamento.
      
      ![][6]  
      
      1. Fornecer valores para **nome da regra**, **regra rota**, e **destino rota** conforme mostrado na imagem.
      2. Clique em **Guardar**.
   5. No **rota** separador novamente, especifique onde confirmações suspensos (confirmações falharem durante o processamento) são encaminhadas para. Definir o tipo de transporte ao Service Bus do Azure, encaminhar o tipo de destino para **fila**, tipo de autenticação para **assinatura de acesso partilhado** (SAS), forneça a cadeia de ligação de SAS para o espaço de nomes do Service Bus e, em seguida, introduza o nome da fila como **suspenso**.
5. Por fim, clique em **implementar** para implementar o contrato. Tenha em atenção os pontos finais onde enviar e receber contratos implementados.
   
   * No **enviar definições** separador em **URL de entrada**, tenha em atenção o ponto final. Para enviar uma mensagem da Contoso para Northwind utilizando a bridge de envio EDI, terá de enviar uma mensagem para este ponto final.
   * No **receber definições** separador em **transporte**, tenha em atenção o ponto final. Para enviar uma mensagem Northwind para Contoso utilizando o EDI receber bridge, terá de enviar uma mensagem para este ponto final.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Passo 3: Criar e implementar o projeto dos BizTalk Services
No passo anterior, implementou o envio EDI e contratos de processar EDIFACT faturas e confirmações de receção. Estes contratos podem apenas as mensagens de processo está em conformidade com o esquema de mensagem EDIFACT padrão. No entanto, pelo cenário para esta solução, Contoso envia uma fatura para Northwind um esquema proprietárias internas. Por isso, antes da mensagem é enviada para a bridge de envio EDI, este deve ser transformado do esquema interna o esquema de fatura EDIFACT padrão. O projeto de BizTalk serviços EAI efetua que.

O projeto dos BizTalk Services, **InvoiceProcessingBridge**, que as transformações a mensagem também é incluído como parte de exemplo que transferiu. O projeto inclui os artefactos seguintes:

* **INHOUSEINVOICE. XSD** – esquema da fatura interna que é enviada para Northwind.
* **EFACT_D93A_INVOIC. XSD** – esquema da fatura EDIFACT padrão.
* **EFACT_4.1_CONTRL. XSD** – esquema da confirmação de EDIFACT Northwind envia para Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – a transformação que mapeia o esquema da fatura internas para o esquema de fatura EDIFACT padrão.  

### <a name="create-the-biztalk-services-project"></a>Criar o projeto dos BizTalk Services
1. Na solução Visual Studio, expanda o projeto de InvoiceProcessingBridge e, em seguida, abra o **MessageFlowItinerary.bcs** ficheiro.
2. Clique em qualquer parte na tela e defina o **URL do serviço BizTalk** na caixa de propriedade para especificar o nome de subscrição do BizTalk Services. Por exemplo, `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Da caixa de ferramentas, arraste um **Xml One-Way Bridge** à tela. Definir o **nome da entidade** e **endereço relativo** propriedades de bridge ao **ProcessInvoiceBridge**. Faça duplo clique em **ProcessInvoiceBridge** para abrir a superfície de configuração bridge.
4. Dentro do **tipos de mensagens** , clique no sinal de adição (**+**) botão para especificar o esquema da mensagem a receber. Porque a mensagem a receber para a bridge EAI é sempre a fatura interna, defina esta opção para **INHOUSEINVOICE**.
   
   ![][8]  
5. Clique em de **transformação Xml** forma e, na caixa de propriedade, para o **Maps** propriedade, clique nas reticências (**...** ) botão. No **mapas de seleção** caixa de diálogo, selecione o **INHOUSEINVOICE_to_D93AINVOIC** transformar o ficheiro e, em seguida, clique em **OK**.
   
   ![][9]  
6. Volte ao **MessageFlowItinerary.bcs**e da caixa de ferramentas, arraste um **ponto final de serviço externo bidirecional** à direita do **ProcessInvoiceBridge**. Definir o respetivo **nome da entidade** propriedade **EDIBridge**.
7. No Explorador de soluções, expanda o **MessageFlowItinerary.bcs** e faça duplo clique o **EDIBridge.config** ficheiro. Substitua o conteúdo a **EDIBridge.config** com o seguinte.
   
   > [!NOTE]
   > Por que motivo é necessário editar o ficheiro. config? O ponto final de serviço externo que foi adicionadas à tela do estruturador de bridge representa os pontes EDI que foi implementada anteriormente. Pontes EDI são pontes bidirecionais, com uma atividade send e recebem lado. No entanto, a bridge EAI adicionámos para o estruturador de bridge é uma ponte unidirecional. Por isso, para lidar com os padrões de exchange mensagem diferente das duas pontes, utilizamos um comportamento de bridge personalizado, incluindo a respetiva configuração no ficheiro. config. Além disso, o comportamento personalizado também processa a autenticação para o ponto final bridge de envio EDI. Este comportamento personalizado está disponível como um exemplo em separado [BizTalk Services Bridge encadeamento exemplo - EAI para EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Esta solução reutiliza a amostra.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. Atualizar o ficheiro de EDIBridge.config para incluir os detalhes de configuração
   
   * Em  *<behaviors>* , forneça o espaço de nomes do ACS e a chave associada a subscrição do BizTalk Services.
   * Em  *<client>* , forneça o ponto final em que o contrato de envio EDI é implementado.
   
   Guardar as alterações e feche o ficheiro de configuração.
9. Da caixa de ferramentas, clique em de **conector** e associar o **ProcessInvoiceBridge** e **EDIBridge** componentes. Selecione o conector e, na caixa de propriedades, defina **condição de filtro** para **todos os correspondência**. Isto garante que todas as mensagens processadas pela bridge EAI são encaminhadas para a bridge EDI.
   
   ![][10]  
10. Guarde as alterações à solução.  

### <a name="deploy-the-project"></a>Implementar o projeto
1. No computador onde criou o projeto dos BizTalk Services, transfira e instale o certificado SSL para a sua subscrição do BizTalk Services. Em BizTalk Services, clique em **Dashboard**e, em seguida, clique em **Transferir certificado de SSL**. Faça duplo clique o certificado e siga o pedido para concluir a instalação. Certifique-se de que instala o certificado no **autoridades de certificação de raiz fidedigna** arquivo de certificados.
2. No Explorador de soluções do Visual Studio, clique com botão direito do **InvoiceProcessingBridge** projeto e, em seguida, clique em **implementar**.
3. Forneça os valores, conforme mostrado na imagem e, em seguida, clique em **implementar**. Pode obter as credenciais de ACS para os BizTalk Services clicando **informações de ligação** do dashboard do BizTalk Services.
   
   ![][11]  
   
   No painel de resultados, copie o ponto final em que a bridge EAI é implementada, por exemplo, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Este URL de ponto final que irá precisar posteriormente.  

## <a name="step-4-test-the-solution"></a>Passo 4: Testar a solução
Neste tópico, vamos ver como testar a solução utilizando o **Tutorial cliente** fornecida como parte do exemplo de aplicação.  

1. No Visual Studio, prima F5 para iniciar o **Tutorial cliente**.
2. O ecrã tem de ter valores pré-preenchida do passo em que criámos as filas do Service Bus. Clique em **Seguinte**.
3. Na janela do seguinte, forneça as credenciais de ACS para a subscrição do BizTalk Services e os pontos finais onde EAI e EDI (receber) pontes são implementados.
   
   Tinha copiou o ponto final de bridge EAI no passo anterior. Para EDI receber o ponto final de bridge, no Portal de serviços do BizTalk, vá para o contrato > receber definições > transporte > ponto final.
   
   ![][12]  
4. Na janela do seguinte, em Contoso, clique o **enviar internas fatura** botão. No ficheiro de abrir a caixa de diálogo, abra o ficheiro INHOUSEINVOICE.txt. Examinar o conteúdo do ficheiro e, em seguida, clique em **OK** para enviar a fatura.
   
   ![][13]  
5. Em alguns segundos a fatura é recebida no Northwind. Clique em de **mensagem vista** ligação para ver a fatura que recebeu por Northwind. Repare como a fatura que recebeu por Northwind é no esquema EDIFACT padrão enquanto um enviada pelo Contoso foi um esquema interna.
   
   ![][14]  
6. Selecione a fatura e, em seguida, clique em **Enviar confirmação**. Na caixa de diálogo que aparece, tenha em atenção que o ID de intercâmbio é a mesmo a fatura recebida e a confirmação de que está a ser enviados. Clique em OK no **Enviar confirmação** caixa de diálogo.
   
   ![][15]  
7. Em alguns segundos, a confirmação é recebeu com êxito no Contoso.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Passo 5 (opcional): fatura EDIFACT enviar em lotes
Pontes BizTalk Services EDI também suporta a criação de batches de mensagens de saída. Esta funcionalidade é útil para receber os parceiros que preferem para receber um lote de mensagens em fila (cumprir determinado critério) em vez de mensagens individuais.

O aspeto mais importante quando trabalha com lotes é a versão real do lote, também denominado os critérios de versão. Os critérios de versão podem basear-se na forma como o parceiro de receção pretende receber mensagens. Se a criação de batches estiver ativada, a bridge EDI não envia a mensagem a enviar para o parceiro de receção até que os critérios de versão é concluído. Por exemplo, um critério de lotes com base na mensagem tamanho dispatches um lote apenas quando ' N' mensagens são em lotes. Um critério de batch também pode ser o baseados no tempo, que é enviado um lote a uma hora fixa todos os dias. Nesta solução, vamos tentar os critérios de com base do tamanho da mensagem.

1. No Portal de serviços do BizTalk, clique no contrato que criou anteriormente. Clique em definições de envio > criação de batches > Adicionar Batch.
2. Para o nome do batch, introduza **InvoiceBatch**, forneça uma descrição e, em seguida, clique em **seguinte**.
3. Especifique um critério de batch, que define quais as mensagens têm de ser em lotes. Nesta solução, vamos batch todas as mensagens. Por isso, selecionar a utilização avançada de opção de definições e introduza **1 = 1**. Esta é uma condição que será sempre verdadeira e, por conseguinte, todas as mensagens serão possível criar batch. Clique em **Seguinte**.
   
   ![][17]  
4. Especifique um critério de versão do batch. Na caixa de lista, selecione **MessageCountBased**e para **contagem**, especifique **3**. Isto significa que será enviado um lote de mensagens de três para Northwind. Clique em **Seguinte**.
   
   ![][18]  
5. Reveja o resumo e, em seguida, clique em **guardar**. Clique em **implementar** Reimplementar o contrato.
6. Volte à **Tutorial cliente**, clique em **enviar internas fatura**e siga as instruções para enviar a fatura. Vai notar que não existem fatura é recebida no Northwind porque o tamanho do lote não for cumprido. Repita este passo mais duas vezes, para que tenha três fatura mensagens enviadas para Northwind. Isto satisfaça os critérios de versão de batch de 3 mensagens e deverá ver uma fatura na Northwind.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

