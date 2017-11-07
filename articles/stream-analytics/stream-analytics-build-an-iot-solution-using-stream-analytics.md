---
title: "Criar uma solução de IoT utilizando o Stream Analytics | Microsoft Docs"
description: "Tutorial de introdução para a solução de IoT de análise de fluxo de um cenário de tollbooth"
keywords: "solução de IOT, as funções de janela"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: a93693ef7d40025fa96846594a8eb525a50b6885
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Criar uma solução de IoT utilizando o Stream Analytics
## <a name="introduction"></a>Introdução
Neste tutorial, irá aprender a utilizar o Azure Stream Analytics para obter informações em tempo real a partir dos seus dados. Os programadores podem facilmente combinar fluxos de dados, tais como os fluxos de clique, os registos e eventos gerados pelo dispositivo, com registos históricos ou dados de referência derivar informações empresariais. Como um serviço de cálculo de fluxo em tempo real, completamente gerido que está alojado no Microsoft Azure, Azure Stream Analytics fornece resiliência incorporada, latência baixa e escalabilidade para obter a cópia de segurança e em execução em minutos.

Depois de concluir este tutorial, será capaz de:

* Familiarize-se com o portal do Azure Stream Analytics.
* Configurar e implementar uma tarefa de transmissão em fluxo.
* Articule reais problemas e resolvê-los utilizando o idioma de consulta do Stream Analytics.
* Desenvolva a transmissão em fluxo soluções para os seus clientes utilizando o Stream Analytics com confiança.
* Utilize a monitorização e a experiência de registo para resolver problemas.

## <a name="prerequisites"></a>Pré-requisitos
Terá dos seguintes pré-requisitos para concluir este tutorial:

* A versão mais recente do [Azure PowerShell](/powershell/azure/overview)
* Visual Studio 2017, 2015, ou o livre [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* Um [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/)
* Privilégios administrativos no computador
* A transferência do [TollApp.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) do Centro de transferências da Microsoft
* Opcional: Origem código para o gerador de evento TollApp no [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>Introdução de cenário: "Olá, utilização!"
Uma estação de utilização é um phenomenon comuns. Encontre-los em muitas expressways, pontes e túneis por todo o mundo. Cada estação utilização tem vários booths de utilização. Em booths manuais, parar a pagar a utilização a uma attendant. Em booths automatizadas, um sensor sobre cada booth analisa um cartão RFID affixed para windshield do seu vehicle como passar o booth de utilização. É fácil visualizar passage de veículos através destes estações de utilização como um fluxo de eventos durante o qual podem ser efetuadas operações interessantes.

![Imagem de automóveis em booths de utilização](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Dados de entrada
Este tutorial funciona com dois fluxos de dados. Sensores instalados na entrada como e saída das estações de utilização produzem o fluxo primeiro. O fluxo segundo é um conjunto de dados de pesquisa estático com dados de registo vehicle.

### <a name="entry-data-stream"></a>Fluxo de dados de entrada
O fluxo de dados de entrada contém informações sobre carros à medida que entra utilização estações.

| TollID | EntryTime | LicensePlate | Estado | Certifique- | Modelo | VehicleType | VehicleWeight | Utilização | Etiqueta |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CIONAR |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CIONAR |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4 4 |1 |0 |6 |321987654 |

Eis uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| TollID |O ID de booth de utilização que identifica exclusivamente um booth de utilização |
| EntryTime |A data e hora de entrada de vehicle para booth a utilização em UTC |
| LicensePlate |O número de licença plate o veículo |
| Estado |Um Estado nos Estados Unidos |
| Certifique- |Fabricante do automóvel |
| Modelo |O número de modelo do automóvel |
| VehicleType |O 1 para veículos passenger ou 2 para veículos comercial |
| WeightType |Peso vehicle em toneladas; 0 para veículos passenger |
| Utilização |O valor de utilização no EUR |
| Etiqueta |A Tag de i no automóvel que automatiza o pagamento; em branco onde o pagamento foi feito manualmente |

### <a name="exit-data-stream"></a>Fluxo de dados de saída
O fluxo de dados de saída contém informações sobre carros deixa a estação de utilização.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Eis uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| TollID |O ID de booth de utilização que identifica exclusivamente um booth de utilização |
| ExitTime |A data e hora de saída do veículo de booth de utilização em UTC |
| LicensePlate |O número de licença plate o veículo |

### <a name="commercial-vehicle-registration-data"></a>Dados de registo vehicle comercial
O tutorial utiliza um instantâneo estático de uma base de dados de registo vehicle comercial.

| LicensePlate | RegistrationId | Expirou |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| I 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Eis uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| LicensePlate |O número de licença plate o veículo |
| RegistrationId |ID de registo o veículo |
| Expirou |O estado do registo do veículo: 0 se o registo de vehicle está ativo, 1 se o registo está expirado |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurar o ambiente para o Azure Stream Analytics
Para concluir este tutorial, precisa de uma subscrição do Microsoft Azure. A Microsoft oferece uma avaliação gratuita para os serviços do Microsoft Azure.

Se não tiver uma conta do Azure, pode [pedir uma versão de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Para se inscrever numa avaliação gratuita, precisa de um dispositivo móvel que pode receber mensagens de texto e um cartão de crédito válido.
> 
> 

Lembre-se de que siga os passos na secção "Limpar a sua conta do Azure" no final deste artigo, para que pode fazer a melhor utilização da sua crédito do Azure.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Aprovisionar os recursos do Azure necessários para o tutorial
Este tutorial requer dois dos event hubs para receber *entrada* e *sair* fluxos de dados. Base de dados SQL do Azure produz os resultados das tarefas do Stream Analytics. Armazenamento do Azure armazena os dados de referência sobre registos vehicle.

Pode utilizar o script de Setup.ps1 na pasta TollApp no GitHub para criar todos os recursos necessários. Com o intuito de tempo, recomendamos que executá-la. Se gostaria de saber mais sobre como configurar estes recursos no portal do Azure, consulte o apêndice "Configurar o tutorial recursos no portal do Azure".

Transfira e guarde o suporte [TollApp](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) pastas e ficheiros.

Abra um **do Microsoft Azure PowerShell** janela *como administrador*. Se ainda não tiver o Azure PowerShell, siga as instruções em [instalar e configurar o Azure PowerShell](/powershell/azure/overview) instalá-la.

Porque o Windows bloqueia automaticamente. ps1,. dll e .exe ficheiros, tem de definir a política de execução antes de executar o script. Certifique-se a janela do PowerShell do Azure está em execução *como administrador*. Executar **Set-ExecutionPolicy sem restrições**. Quando lhe for pedido, escreva **Y**.

![Captura de ecrã da "Set-ExecutionPolicy sem restrições" em execução na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Executar **Get-ExecutionPolicy** para se certificar de que o comando trabalhado.

![Captura de ecrã da "Get-ExecutionPolicy" em execução na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Vá para o diretório que tem os scripts e aplicações de gerador de dimensões.

![Captura de ecrã da "cd .\TollApp\TollApp" em execução na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Tipo **.\\ Setup.ps1** para configurar a sua conta do Azure, criar e configurar todos requeridos recursos e começar a gerar eventos. O script aleatoriamente seleciona uma região para criar os seus recursos. Para especificar explicitamente uma região, pode passar o **-localização** parâmetro como no exemplo seguinte:

**. \\Setup.ps1-localização "EUA Central"**

![Captura de ecrã da página de início de sessão do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

O script abre o **sessão** página do Microsoft Azure. Introduza as credenciais da conta.

> [!NOTE]
> Se a conta tem acesso a várias subscrições, será pedido para introduzir o nome da subscrição que pretende utilizar para o tutorial.
> 
> 

O script pode demorar alguns minutos para ser executada. Depois de concluído, o resultado deverá ser semelhante à seguinte captura de ecrã.

![Captura de ecrã da saída do script na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Também verá outra janela é semelhante à seguinte captura de ecrã. Esta aplicação está a enviar eventos para Event Hubs do Azure, que é necessário para executar o tutorial. Por isso, não parar a aplicação ou fechar esta janela até concluir o tutorial.

![Captura de ecrã de "Dados de hub de eventos de enviar"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Deverá conseguir ver agora os recursos no portal do Azure. Aceda a <https://portal.azure.com>e inicie sessão com as credenciais da conta. Tenha em atenção que atualmente algumas funcionalidades utiliza o portal clássico. Estes passos irão ser claramente indicados.

### <a name="azure-event-hubs"></a>Azure Event Hubs
No portal do Azure, clique em **mais serviços** na parte inferior do painel esquerdo de gestão. Tipo **dos Event hubs** no campo fornecidas e clique em **dos Event hubs**. Isto inicia uma nova janela do browser para apresentar o **SERVICE BUS** área a **portal clássico**. Aqui pode ver o Hub de eventos criados pelo Setup.ps1 script.

![Service Bus](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Clique em que começa com *tolldata*. Clique em de **EVENT HUBS** separador. O utilizador verá dois dos event hubs com o nome *entrada* e *sair* criada neste espaço de nomes.

![Separador de Hubs de eventos no portal clássico](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

### <a name="azure-storage-container"></a>Contentor de armazenamento do Azure
1. Regressar ao separador abra o browser para o portal do Azure. Clique em **armazenamento** no lado esquerdo do portal do Azure para ver o contentor de armazenamento do Azure que é utilizado no tutorial.
   
    ![Item de menu do armazenamento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)
2. Clique em que comece com *tolldata*. Clique em de **contentores** separador para visualizar o contentor criado.
   
    ![Separador de contentores no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)
3. Clique em de **tolldata** contentor para ver o ficheiro JSON carregado com dados de registo vehicle.
   
    ![Captura de ecrã do ficheiro registration.json no contentor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
1. Volte ao portal do Azure no primeiro separador que foi aberto no browser. Clique em **bases de dados SQL** no lado esquerdo do portal do Azure para ver a base de dados do SQL Server que será utilizado no tutorial e clique em **tolldatadb**.
   
    ![Captura de ecrã da base de dados do SQL Server criado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
2. Copie o nome do servidor sem o número da porta (*servername*. database.windows.net, por exemplo).
    ![Captura de ecrã da base de dados de base de dados SQL criada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15a.png)

## <a name="connect-to-the-database-from-visual-studio"></a>Ligar à base de dados a partir do Visual Studio
Utilize o Visual Studio para aceder a resultados de consulta na base de dados de saída.

Ligar para a base de dados do SQL Server (destino) a partir do Visual Studio:

1. Abra o Visual Studio e, em seguida, clique em **ferramentas** > **ligar à base de dados**.
2. Se lhe for perguntado, clique em **Microsoft SQL Server** como uma origem de dados.
   
    ![Caixa de diálogo de origem de dados de alteração](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. No **nome do servidor** campo, cole o nome que copiou na secção anterior do portal do Azure (ou seja, *servername*. database.windows.net).
4. Clique em **utilizar autenticação do SQL Server**.
5. Introduza **tolladmin** no **nome de utilizador** campo e **123toll!** no **palavra-passe** campo.
6. Clique em **selecione ou introduza um nome de base de dados**e selecione **TollDataDB** como base de dados.
   
    ![Adicionar caixa de diálogo de ligação](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. Clique em **OK**.
8. Abra o Explorador de servidores.
   
    ![Explorador de Servidores](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. Consulte as quatro tabelas na base de dados TollDataDB.
   
    ![Tabelas na base de dados TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Gerador de evento: TollApp projeto de exemplo
O script do PowerShell inicia automaticamente enviar eventos utilizando o programa de aplicação de exemplo TollApp. Não precisa de efetuar quaisquer passos adicionais.

No entanto, se estiver interessado em detalhes de implementação, pode encontrar o código fonte da aplicação TollApp no GitHub [amostras/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Captura de ecrã do código de exemplo apresentado no Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics
1. No portal do Azure, clique no sinal verde no canto superior esquerdo da página para criar uma nova tarefa de Stream Analytics. Selecione **Intelligence + análise** e, em seguida, clique em **tarefa do Stream Analytics**.
   
    ![Botão novo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. Forneça um nome de tarefa, validar a subscrição está Corrija e, em seguida, crie um novo grupo de recursos na mesma região que o armazenamento de hub de eventos (a predefinição é sul Central-nos para o script).
3. Clique em **afixar ao dashboard** e, em seguida, **criar** na parte inferior da página.
   
    ![Criar a opção de tarefa do Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Definir origens de entrada
1. A tarefa irá criar e abrir a página de tarefa. Ou pode clicar na tarefa de análise criadas no dashboard do portal.

2. Clique em de **entradas** separador para definir a origem de dados.
   
    ![O separador de entradas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.png)
3. Clique em **adicionar uma entrada**.
   
    ![Adicionar uma opção de entrada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. Introduza **EntryStream** como **ALIAS de entrada**.
5. Tipo de origem é **fluxo de dados**
6. A origem é **hub de eventos**.
7. **O espaço de nomes do Service bus** deve ser um TollData na lista pendente.
8. **Nome do hub de eventos** deve ser definido como **entrada**.
9. **Nome de política do hub de eventos*é **RootManageSharedAccessKey** (o valor predefinido).
10. Selecione **JSON** para **formato de SERIALIZAÇÃO de eventos** e **UTF8** para **codificação**.
   
    As definições serão aspeto:
   
    ![Definições de hub de eventos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

10. Clique em **criar** na parte inferior da página para concluir o assistente.
    
    Agora que criou o fluxo de entrada, deve seguir os mesmos passos para criar o fluxo de saída. Lembre-se de que introduza valores como na captura de ecrã seguinte.
    
    ![Definições para o fluxo de saída](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    Definiu dois fluxos de entrada:
    
    ![Fluxos de entrada definidos no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.png)
    
    Em seguida, irá adicionar entrada de dados de referência para o ficheiro de blob que contém dados de registo carro.
11. Clique em **adicionar**e, em seguida, siga o mesmo processo para as entradas de fluxo, mas selecionar **dados de referência** em vez de **fluxo de dados** e **Alias de entrada** é **registo**.

12. conta de armazenamento que começa com **tolldata**. O nome do contentor deve ser **tolldata**e o **padrão do caminho** deve ser **registration.json**. Este nome de ficheiro é sensível a maiúsculas e minúsculas e devem ser **minúsculas**.
    
    ![Definições de armazenamento do blogue](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. Clique em **criar** para concluir o assistente.

Agora todas as entradas são definidas.

## <a name="define-output"></a>Definir a saída
1. No painel de descrição geral da tarefa de Stream Analytics, selecione **saídas**.
   
    ![O separador de saída e a opção "Adicionar um resultado"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.png)
2. Clique em **Adicionar**.
3. Definir o **alias de saída** para 'resultado' e, em seguida, **Sink** para **base de dados SQL**.
3. Selecione o nome do servidor que foi utilizado na secção "Ligar à base de dados do Visual Studio" do artigo. O nome de base de dados é **TollDataDB**.
4. Introduza **tolladmin** no **USERNAME** campo, **123toll!** no **palavra-passe** campo, e **TollDataRefJoin** no **tabela** campo.
   
    ![Definições de base de dados SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.png)
5. Clique em **Criar**.

## <a name="azure-stream-analytics-query"></a>Consulta de análise de fluxo do Azure
O **consulta** separador contém uma consulta SQL que transforma os dados de entrada.

![Uma consulta adicionada ao separador consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.png)

Este tutorial tenta responder às perguntas de negócio várias que estão relacionados com a utilização de dados e construções de consultas de Stream Analytics que podem ser utilizadas no Azure Stream Analytics para fornecer uma resposta relevante.

Antes de iniciar a tarefa de Stream Analytics primeiro, vamos explorar alguns cenários e a sintaxe de consulta.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introdução à linguagem de consulta do Azure Stream Analytics
- - -
Imaginemos que precisa contabilizar o número de veículos que introduza uma booth de utilização. Porque se trata de um fluxo contínuo de eventos, tem de definir um "período de tempo." Vamos modificar a questão ser "veículos quantos introduza uma booth de utilização a cada três minutos?". Isto é frequentemente denominado a contagem de em cascata.

Vamos ver a consulta do Azure Stream Analytics que responde a esta pergunta:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Como pode ver, o Azure Stream Analytics utiliza uma linguagem de consulta que é como o SQL e adiciona alguns extensões para especificar os aspetos relacionados com a hora da consulta.

Para obter mais detalhes, leia sobre [tempo gestão](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [modos de janela](https://msdn.microsoft.com/library/azure/dn835019.aspx) construções utilizadas na consulta do MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Testar consultas do Azure Stream Analytics
Agora que tem de escrever a primeira consulta do Azure Stream Analytics, está na altura de testá-lo através da utilização de ficheiros de dados de exemplo localizados na pasta TollApp no seguinte caminho:

**..\\TollApp\\TollApp\\dados**

Esta pasta contém os seguintes ficheiros:

* Entry.JSON
* Exit.JSON
* Registration.JSON

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Pergunta 1: Número de veículos introduzir um booth de utilização
1. Abra o portal do Azure e aceda à sua tarefa do Azure Stream Analytics criada. Clique em de **consulta** separador e cole a consulta da secção anterior.

2. Para validar esta consulta em relação a dados de exemplo, carregar dados para a entrada de EntryStream clicando a... símbolo e selecionando **carregar dados de exemplo do ficheiro**.

    ![Captura de ecrã do ficheiro Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. No painel que aparece selecione o ficheiro (Entry.json) no seu computador local e clique em **OK**. O **teste** ícone agora illuminate e ser clicável.
   
    ![Captura de ecrã do ficheiro Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.png)
3. Confirme que o resultado da consulta conforme esperado:
   
    ![Resultados do teste](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.png)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Pergunta 2: Tempo total para cada carro passar o booth de utilização de relatórios
O tempo médio que necessita de um carro passar a utilização ajuda-o para avaliar a eficiência do processo e a experiência do cliente.

Para determinar o total de tempo, precisa de associar o fluxo de EntryTime com o fluxo de ExitTime. Irá associar os fluxos de colunas TollId e LicencePlate. O **associar** operador requer que especifique leeway temporal que descreve a diferença de tempo aceitável, entre os eventos associados. Irá utilizar **DATEDIFF** função para especificar que os eventos devem ser mais do que 15 minutos entre si. Também irá aplicar a **DATEDIFF** função para sair e as horas de entrada para calcular o real de tempo que um carro passa na estação de utilização. Tenha em atenção a diferença de utilização de **DATEDIFF** quando é utilizado num **SELECIONE** instrução em vez de um **associar** condição.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Para testar esta consulta, atualize a consulta no **consulta** para a tarefa. Adicione o ficheiro de teste para **ExitStream** tal como **EntryStream** foi introduzido acima.
   
2. Clique em **teste**.

3. Selecione a caixa de verificação para testar a consulta e ver o resultado:
   
    ![Resultado do teste](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Pergunta 3: Comunicar todas as veículos comercial com registo expirado
O Azure Stream Analytics pode utilizar instantâneos estáticos de dados para juntar com fluxos de dados temporal. Para demonstrar esta capacidade, utilize a pergunta de exemplo seguinte.

Se um vehicle comercial estiver registado com a empresa de utilização, pode passar através de booth utilização sem ser parada para inspecção. Irá utilizar a tabela de pesquisa de registo Vehicle comercial para identificar todos os veículos comercial que tenham expirado registos.

```
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

Para testar uma consulta, utilizando os dados de referência, terá de definir uma origem de entrada para os dados de referência, que efetuou já.

Para testar esta consulta, cole a consulta no **consulta** separador, clique em **testar**e especifique duas origens de entrada e o registo de dados de exemplo e clique em **testar**.  
   
![Resultado do teste](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

## <a name="start-the-stream-analytics-job"></a>Iniciar a tarefa de Stream Analytics
Agora está na altura de concluir a configuração e iniciar a tarefa. Guardar a consulta de pergunta 3, que irá produzir resultados que corresponde ao esquema do **TollDataRefJoin** tabela de saída.

Vá para a tarefa **DASHBOARD**e clique em **iniciar**.

![Captura de ecrã do botão Iniciar o dashboard de tarefa](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.png)

Na caixa de diálogo que se abre, altere o **iniciar saída** altura de **personalizada tempo**. Altere a hora a uma hora antes da hora atual. Esta alteração garante que todos os eventos do hub de eventos são processados desde o início gerar os eventos no início do tutorial. Agora, clique em de **iniciar** botão para iniciar a tarefa.

![Seleção de tempo personalizado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Iniciar a tarefa pode demorar alguns minutos. Pode ver o estado na página de nível superior para o Stream Analytics.

![Captura de ecrã do Estado da tarefa](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.png)

## <a name="check-results-in-visual-studio"></a>Resultados da verificação no Visual Studio
1. Abra o Explorador de servidores do Visual Studio e clique com botão direito do **TollDataRefJoin** tabela.
2. Clique em **Mostrar dados de tabela** para ver o resultado da tarefa.
   
    ![Seleção de "Mostrar os dados da tabela" no Explorador de servidores](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Aumentar horizontalmente Azure Stream Analytics tarefas
O Azure Stream Analytics foi concebido aprovisionadas o dimensionamento, para que pode processar uma grande quantidade de dados. A consulta do Azure Stream Analytics pode utilizar um **PARTITION BY** cláusula para informar o sistema que este passo irá aumentar horizontalmente. **PartitionId** é uma coluna especial que adiciona o sistema para corresponder ao ID de partição da entrada (hub de eventos).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Parar a tarefa atual, atualize a consulta no **consulta** separador e abra o **definições** gear no dashboard de tarefa. Clique em **escala**.
   
    **UNIDADES de transmissão em fluxo** define a quantidade de capacidade de computação que pode receber a tarefa.
2. Altere a lista de 1 de 6.
   
    ![Captura de ecrã da seleção 6 unidades de transmissão em fluxo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.png)
3. Vá para o **saídas** separador e altere o nome da tabela SQL para **TollDataTumblingCountPartitioned**.

Se iniciar a tarefa agora, Azure Stream Analytics pode distribuir trabalho entre mais recursos de computação e melhorar o débito. Tenha em atenção que a aplicação de TollApp também está a enviar eventos particionados TollId.

## <a name="monitor"></a>Monitorizar
O **MONITOR** área contém estatísticas sobre a tarefa em execução. Conta pela primeira vez, é necessária uma configuração para utilizar o armazenamento na mesma região (nome de utilização, como o resto deste documento).   

![Captura de ecrã do monitor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

Pode aceder ao **registos de atividade** partir do dashboard tarefa **definições** área bem.


## <a name="conclusion"></a>Conclusão
Este tutorial introduzidos o serviço Azure Stream Analytics. -Demonstrado como configurar as entradas e saídas da tarefa de Stream Analytics. Utilizando o cenário de dados de utilização, o tutorial explicado tipos comuns dos problemas que surgir no espaço de dados em movimento e a forma como pode ser resolvidos com consultas de SQL Server como simples no Azure Stream Analytics. O tutorial descrito construções de extensão SQL para trabalhar com dados temporais. -Mostrou como associar fluxos de dados, como enriqueça o fluxo de dados com dados de referência estáticos e como aumentar horizontalmente uma consulta para alcançar o débito mais elevado.

Embora este tutorial fornece uma introdução boa, não estão concluída por qualquer meio. Pode encontrar mais padrões de consulta utilizando a linguagem SAQL em [consultar exemplos de padrões de utilização comuns do Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
Consulte o [documentação online](https://azure.microsoft.com/documentation/services/stream-analytics/) para saber mais sobre o Azure Stream Analytics.

## <a name="clean-up-your-azure-account"></a>Limpar a sua conta do Azure
1. Pare a tarefa de Stream Analytics no portal do Azure.
   
    O script de Setup.ps1 cria dois dos event hubs e uma base de dados do SQL Server. As instruções seguintes ajudá-lo limpar recursos no final do tutorial.
2. Na janela do PowerShell, escreva **.\\ CleanUp.ps1** para iniciar o script que elimina os recursos que utilizou no tutorial.
   
   > [!NOTE]
   > Os recursos são identificados pelo nome. Certifique-se de que cada item reveja cuidadosamente antes de confirmar a remoção.
   > 
   > 


