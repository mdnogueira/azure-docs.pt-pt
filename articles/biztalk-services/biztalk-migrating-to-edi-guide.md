---
title: "Migrar o BizTalk Server para EDI soluções BizTalk Services técnico guia | Microsoft Docs"
description: Migrar EDI para MABS; BizTalk Services do Azure da Microsoft
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 1b70fc3d199d7f1521acb534dafec8fb3e69500e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrar o BizTalk Server para EDI soluções para os BizTalk Services: guia técnico

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Autor: Tim Wieman e Nitin Mehrotra

Revisores: Karthik Bharthy

Escritas utilizando: BizTalk Services do Microsoft Azure – Fevereiro de 2014 de versão.

## <a name="introduction"></a>Introdução
Intercâmbio de dados Eletrónicos (EDI) é uma dos meios atuais mais predominantes pelos dados do exchange que as empresas eletronicamente, também denominadas transações de empresa-empresa ou B2B. BizTalk Server tenha tido suporte EDI para através de um decade, desde a versão de BizTalk Sever inicial. Com os serviços do BizTalk, a Microsoft continua o suporte para soluções EDI na plataforma Microsoft Azure. Transações de B2B principalmente são externas para uma organização e, por conseguinte, é mais fácil de implementar se foi implementada numa plataforma de nuvem. Microsoft Azure fornece esta capacidade através dos BizTalk Services.

Embora alguns clientes observar os BizTalk Services como uma plataforma de "greenfield" para novas soluções EDI, muitos clientes tem soluções BizTalk Server EDI atuais, que podem optar por migrar para o Azure. Porque é criado de BizTalk Services EDI com base no mesmas entidades chaves como a arquitetura de BizTalk Server EDI (comerciais parceiros, entidades, contratos), é possível migrar artefactos EDI do BizTalk Server para os BizTalk Services.

Este documento descreve algumas das diferenças envolvidas com artefactos de BizTalk Server EDI migrar para os BizTalk Services. Este documento assume um conhecimento prático do processamento de BizTalk Server EDI e contratos de parceiros comerciais. Para obter mais informações sobre o BizTalk Server EDI, consulte [comerciais parceiro gestão utilizando o BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Qual é a versão dos artefactos do BizTalk Server EDI pode ser migrada para os BizTalk Services?
O módulo de BizTalk Server EDI foi significativamente melhorado para BizTalk Server 2010, quando foi remodeled para incluir parceiros, perfis e contratos. BizTalk Services utiliza o mesmo modelo para organizar os parceiros comerciais e de divisões de negócio dentro desses parceiros comerciais. Como resultado, a migração artefactos EDI do BizTalk Server 2010 e versões posteriores para os BizTalk Services, é um processo muito mais diretamente direta. Para migrar os artefactos EDI associados com versões anteriores ao BizTalk Server 2010, deve primeiro atualizar para o BizTalk Server 2010 e, em seguida, migrar os seus artefactos EDI para os BizTalk Services.

## <a name="scenariosmessage-flow"></a>Fluxo de cenários/mensagem
Como com o BizTalk Server, EDI processamento nos BizTalk Services está construído em torno de uma solução de gestão de parceiros comerciais (TPM). A solução TPM tem os seguintes componentes principais:

* Comerciais parceiros, que representa a organização de uma transação de B2B.
* Perfis, que representam divisões dentro de um parceiro comercial.
* Comerciais contratos de parceiros (ou contratos), que representa o contrato de negócio entre dois parceiros/perfis.

A ilustração seguinte ilustra o semelhanças, bem como as diferenças uma solução de BizTalk Server EDI e a solução de EDI do BizTalk Services:

![][EDImessageflow]

As principais diferenças e semelhanças, entre um fluxo de solução EDI no BizTalk Server e os BizTalk Services são:

* Tal como o BizTalk Server para utilizar um pipeline EDIReceive para receber uma mensagem EDI e um pipeline EDISend para enviar uma mensagem EDI, os BizTalk Services utiliza uma bridge de receber EDI de mensagens em fila para receber e enviar EDI bridge para enviar mensagens EDI. BizTalk Server, pipelines associados a um contrato com o envio ou recebem as portas. Nos BizTalk Services, o contrato de si próprio indica enviar ou receber bridge.
* BizTalk Server, depois do pipeline de EDIReceive processa a mensagem EDI, a mensagem é capturar uma base de dados do SQL Server. O pipeline de EdiSend, em seguida, escolherá a mensagem da base de dados do SQL Server, os processos que e, em seguida, envia-para o parceiro comercial.
  
    BizTalk Services, depois de receber o EDI bridge processa a mensagem EDI, encaminha a mensagem para um processo externo. O processo externo pode estar em execução no Microsoft Azure ou no local. O processo externo deve encaminhar a mensagem para bridge de envio EDI; a bridge de envio não solicita inerentemente a mensagem. Após o processamento da mensagem, a bridge de envio EDI encaminha a mensagem para o parceiro comercial.

BizTalk Services proporciona uma experiência de configuração de fácil de utilizar para rapidamente criar e implementar um contrato de B2B entre comerciais parceiros sem configurar qualquer computação do Microsoft Azure instâncias (funções Web ou de trabalho), as bases de dados de SQL do Microsoft Azure ou qualquer Contas de armazenamento do Microsoft Azure. Cenários mais complexos irão exigir a escrever fluxos de trabalho ou outro processamento de serviço "em torno as extremidades" de um contrato de parceiro comercial, ou seja, antes ou após o processamento de bridge EDI de contrato de parceiro comercial. Em detalhe, as seguintes sequências de eventos ocorrem durante uma mensagem EDI processamento nos BizTalk Services.

1. É recebida uma mensagem EDI de comerciais parceiro, Fabrikam.  Para receber mensagens EDI de parceiros comerciais, os BizTalk Services suporta protocolos de transporte, como o FTP, SFTP, AS2 e HTTP/S.
2. O processamento de lado de contrato de parceiro comercial disassembles mensagem EDI para um formato XML.  Pode encaminhar a mensagem EDI disassembled (no formato XML) para pontos finais do Service Bus, como um ponto final de reencaminhamento do Service Bus, tópico de barramento de serviço, fila de barramento de serviço ou uma ponte de BizTalk Services.
3. As mensagens XML disassembled, em seguida, foi recebidas do ponto final para obter mais processamento personalizado.  Estes pontos finais foi processadas por um componente no local ou uma instância de Microsoft Azure computação adicional processar a mensagem num serviço Windows. o fluxo de trabalho (WF) ou o Windows Communication Foundation (WCF), por exemplo.
4. O "processamento de lado de envio" do parceiro comercial de contrato, em seguida, monta a mensagem XML num ficheiro em formato EDI e envia-a para o parceiro comercial, Contoso.  Para enviar mensagens EDI para parceiros comerciais, os BizTalk Services suporta os mesmos protocolos como são utilizados para receber mensagens EDI.

Ainda mais este documento fornece orientações conceptual no migrar algumas dos artefactos de BizTalk Server EDI diferentes para os BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Envio/receção portas para comerciais parceiros
BizTalk Server, configurar portas de receção e receber localizações para receber mensagens EDI/XML de parceiros comerciais e configurar as portas de envio para enviar mensagens EDI/XML ao parceiro comercial. Em seguida, associar segurança estas portas para um contrato de parceiro comercial utilizando a consola de administração de servidor do BizTalk. Nos BizTalk Services, as localizações onde receber mensagens comerciais parceiros e onde pode envia mensagens para comerciais parceiros estão configuradas como parte do contrato de parceiro comercial propriamente dito (como parte das definições de transporte) no Portal de serviços do BizTalk.  Por isso, não realmente tem o conceito de "portas de envio" e "receber localizações", per se, no BizTalk Services. Para obter mais informações, consulte [criar contratos](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Pipelines (pontes)
BizTalk Server EDI, pipelines são entidades de processamento de mensagens que também podem incluir a lógica personalizada para as capacidades de processamento específico, conforme exigido pela aplicação. Dos BizTalk Services, o equivalente seria uma bridge EDI. No entanto nos BizTalk Services, por agora, os pontes EDI são "fechados".  Ou seja, não é possível adicionar as suas próprias atividades personalizadas para uma bridge EDI. Qualquer processamento personalizado tem de ser feito fora a bridge EDI na sua aplicação, antes ou depois da mensagem introduz a bridge configurada como parte do contrato de parceiro comercial. Pontes EAI têm a opção de efetuar o processamento personalizado. Se pretender que o processamento personalizado, pode utilizar pontes EAI, antes ou depois da mensagem é processada pela bridge EDI. Para obter mais informações, consulte [como incluem o código personalizado no pontes](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Pode inserir um fluxo de publicação/subscrição com o código personalizado e/ou utilizando mensagens de filas e tópicos antes do contrato de parceiro comercial recebe a mensagem ou depois do contrato de processa a mensagem e encaminha o mesmo para um ponto final do Service Bus do Service Bus.

Consulte **cenários/mensagem fluxo** neste tópico para o padrão de fluxo de mensagens.

## <a name="agreements"></a>Contratos
Se estiver familiarizado com o BizTalk Server 2010 Trading contratos de parceiros utilizada para processar EDI, em seguida, os BizTalk Services contratos de parceiros comerciais ter um aspeto familiares. A maioria das definições de contrato é os mesmos e utilize a terminologia do mesma. Em alguns casos, as definições de contrato são muito mais simples em comparação com as mesmas definições no servidor do BizTalk. Os BizTalk Services do Microsoft Azure suporta X12, EDIFACT e AS2 de transporte.

Os BizTalk Services do Microsoft Azure também fornece um **migração de dados de TPM** ferramenta para migrar parceiros comerciais e de contratos do módulo de parceiro de comerciais do BizTalk Server para o Portal de serviços de BizTalk. A ferramenta de migração de dados de TPM está disponível como parte de um pacote de ferramentas, que pode ser transferido a partir de [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). O pacote também inclui um ficheiro Leia-me que fornece instruções sobre como utilizar a ferramenta e informações de resolução de problemas básicas para a ferramenta.

## <a name="schemas"></a>Esquemas
Os BizTalk Services fornecem esquemas EDI que podem ser utilizadas em soluções BizTalk Services.  Além disso, os esquemas EDI do BizTalk Server também podem ser utilizadas com os BizTalk Services porque o nó de raiz do esquema EDI é igual entre o BizTalk Server, bem como os BizTalk Services. Assim, conseguirá diretamente demorar as esquemas EDI do BizTalk Server e utilizá-los nas soluções EDI desenvolver utilizando os BizTalk Services. Também pode transferir os esquemas do [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Maps (transformações)
Mapas no BizTalk Server são denominados transformações nos BizTalk Services. Migrar mapas do BizTalk Server para os BizTalk Services pode ser uma das tarefas mais complexas para alcançar (dependendo da complexidade do mapa). A ferramenta de mapeamento utilizada para os BizTalk Services é diferente do mapeador de BizTalk. Apesar do mapeador de procura, principalmente, os mesmos, o formato de mapa subjacente é diferente. O functoids (chamado **operações de mapa** nos BizTalk Services) disponíveis para os utilizadores são bem diferentes.  Em vigor, não é possível utilizar diretamente um mapa de BizTalk nos BizTalk Services. Além disso, nem todos os functoids disponíveis no BizTalk Server estão disponíveis como as operações do mapa no BizTalk Services.

### <a name="new-transform-operations"></a>Operações de transformação de novo
Embora a lista de operações de mapa de transformação disponíveis possa parecer bastante diferente do mapeador de BizTalk Server, transforma do BizTalk Services tem novas formas de realizar as mesmas tarefas. Por exemplo, transforma do BizTalk Services ter **liste as operações de** disponíveis. Não estava disponível no mapeador de BizTalk.  O **liste as operações de** permitem-lhe criar e a funcionar numa "lista de", onde uma lista de um conjunto de itens (também conhecido como "linhas") e onde cada item pode ter vários membros (também conhecido como "colunas").  Pode ordenar a lista, selecione os itens com base numa condição, etc.

Outro exemplo das novas funcionalidades no transforma do BizTalk Services são o **operações de ciclo**.  É difícil criar ciclos aninhados no mapeador de BizTalk Server.  Assim, as operações de mapa de ciclo são adicionadas para transformações de serviços de BizTalk.

Ainda outro exemplo é o **If-, em seguida,-Else** operação de mapa de expressão.  Fazer uma operação pessoa se-, em seguida, era possível no mapeador do BizTalk, mas necessário vários functoids para realizar uma tarefa seemingly simple.

### <a name="migrating-biztalk-server-maps"></a>BizTalk Server para migrar mapas
Os BizTalk Services do Microsoft Azure fornece uma ferramenta para migrar o servidor de BizTalk mapeia para transformações de BizTalk Services. O **BTMMigrationTool** está disponível como parte do **ferramentas** pacote fornecido com o [transferência do SDK dos BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkId=235057). Para obter mais informações sobre a ferramenta, consulte [converter um mapa de BizTalk uma transformação de BizTalk Services](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Também pode ver um exemplo por Sandro Pereira, MVP do BizTalk, sobre como [migrar mapas de BizTalk Server para transformações de BizTalk Services](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrations
Se pretender migrar orchestration BizTalk Server para o Microsoft Azure de processamento, seriam necessário os orchestrations ser foi reescrita porque o Microsoft Azure não suporta orchestrations de BizTalk Server em execução.  Foi reescrever a funcionalidade de orquestração num serviço Windows Workflow Foundation 4.0 (WF4).  Isto seria um reescrever completa que atualmente não a migração de orchestrations BizTalk Server para WF4. Seguem-se alguns recursos para o fluxo de trabalho do Windows:

* [*Como integrar um serviço de fluxo de trabalho de WCF com tópicos e filas do Service Bus* ](https://msdn.microsoft.com/library/azure/hh709041.aspx) por Paolo Salvatori. 
* [*Criar aplicações com o Windows Workflow Foundation e o Azure* sessão](http://go.microsoft.com/fwlink/p/?LinkId=237314) de conferência a compilação 2011.
* [*Centro de programadores de Foundation de fluxo de trabalho do Windows* ](http://go.microsoft.com/fwlink/p/?LinkId=237315) no MSDN.
* [*Documentação de Windows fluxo de trabalho Foundation 4 (WF4)* ](https://msdn.microsoft.com/library/dd489441.aspx) no MSDN.

## <a name="other-considerations"></a>Outras considerações
Seguem-se algumas considerações que é necessário efetuar ao utilizar os BizTalk Services.

### <a name="fallback-agreements"></a>Contratos de contingência
Processamento de BizTalk Server EDI tem o conceito de "Reversão contratos".  BizTalk Services **não** têm um conceito de contrato de contingência até ao momento.  Consulte os tópicos de documentação do BizTalk [a função de contratos EDI processar](http://go.microsoft.com/fwlink/p/?LinkId=237317) e [configurar Global ou propriedades de contrato de contingência](https://msdn.microsoft.com/library/bb245981.aspx) para obter informações sobre como contingência contratos são utilizados no BizTalk Servidor.

### <a name="routing-to-multiple-destinations"></a>Encaminhamento para vários destinos
Pontes BizTalk Services, no seu estado atual não suporta encaminhamento de mensagens a vários destinos com uma publicação-subscrição modelo. Em vez disso, pode encaminhar mensagens de uma ponte de BizTalk Services para um tópico de barramento de serviço, que, em seguida, pode ter várias subscrições para receber a mensagem mais do que um ponto final.

## <a name="conclusion"></a>Conclusão
Os BizTalk Services do Microsoft Azure é actualizado no marcos regulares para adicionar mais funcionalidades e capacidades. Com cada atualização, vamos ver reencaminhar para suportar a funcionalidade de aumento para facilitar a criar soluções ponto-a-ponto utilizando os BizTalk Services e outras tecnologias do Azure.

## <a name="see-also"></a>Veja Também
[Desenvolver aplicações da empresa com o Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
