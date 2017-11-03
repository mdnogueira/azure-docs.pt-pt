---
title: "Notas de versão para BizTalk Services do Azure | Microsoft Docs"
description: Lista os problemas conhecidos dos BizTalk Services do Azure
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 18ed891a9bba2b4011d3492722a2366d96fb3c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-azure-biztalk-services"></a>Notas de versão do BizTalk Services do Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

As notas de versão para o BizTalk Services do Microsoft Azure contêm problemas conhecidos desta versão.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Novidades na atualização de Novembro de serviços BizTalk
* Encriptação de Inativos pode ser ativada no Portal de serviços do BizTalk. Consulte [ativar a encriptação de Inativos no Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Histórico de atualização
### <a name="october-update"></a>Atualização de Outubro
* As contas organizacionais são suportadas:  
  * **Cenário**: registada uma implementação de BizTalk Service utilizando uma conta Microsoft (como user@live.com). Neste cenário, apenas os utilizadores do Microsoft Account podem gerir o BizTalk Service através do portal do BizTalk Services. Não é possível utilizar uma conta organizacional.  
  * **Cenário**: registada uma implementação de BizTalk Service utilizando uma conta organizacional num Azure Active Directory (como user@fabrikam.com ou user@contoso.com). Neste cenário, apenas os utilizadores do Active Directory do Azure dentro da mesma organização podem gerir o BizTalk Service através do portal do BizTalk Services. Não é possível utilizar uma conta Microsoft.  
* Quando cria um BizTalk Service no portal clássico do Azure, estão registados automaticamente no Portal de serviços do BizTalk.
  * **Cenário**: A iniciar sessão no portal clássico do Azure, cria um BizTalk Service e, em seguida, selecione **gerir** durante o período de tempo relativo ao primeiro. Quando abre o portal do BizTalk Services, o BizTalk Service regista automaticamente e está pronto para as implementações.  
    Consulte [registar e atualizar uma implementação de serviço BizTalk o BizTalk Services Portal](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Atualização 14 de Agosto
* Contrato e bridge desacoplamento – comerciais contratos de parceiros e pontes são agora desacopladas no Portal de serviços do BizTalk. Agora, criar contratos e pontes separadamente e em tempo de execução pontes resolver para um contrato com base nos valores na mensagem EDI. Consulte [criar contratos no BizTalk Services do Azure](https://msdn.microsoft.com/library/azure/hh689908.aspx), [criar uma bridge EDI com o Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [criar uma bridge de AS2 utilizando o Portal de serviços de BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx)e [ Como é que pontes resolver contratos no tempo de execução?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* A opção para criar modelos para contratos foi descontinuada.  
* Para o contrato de lado de envio, pode agora especificar conjuntos delimitador diferente para cada esquema. Esta configuração especificada nas definições do protocolo de contrato de lado de envio. Para obter mais informações, consulte [criar um X12 contrato nos BizTalk Services do Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx) e [criar um contrato de EDIFACT no BizTalk Services do Azure](https://msdn.microsoft.com/library/azure/dn606267.aspx). Duas novas entidades são também adicionadas à API do OM do TPM para a mesma finalidade. Consulte [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) e [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Construções XSD padrão, incluindo tipos derivados, são agora suportadas. Consulte [XSD padrão de utilização constrói o Maps](https://msdn.microsoft.com/library/azure/dn793987.aspx) e [utilize derivada tipos de cenários de mapeamento e exemplos](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 suporta novos algoritmos Dinâmicas para a assinatura de mensagem e novos algoritmos de encriptação. Consulte [criar um contrato de AS2 no BizTalk Services do Azure](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Saber problemas

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemas de conectividade depois da atualização do Portal de serviços BizTalk
  Se tiver o Portal de serviços de BizTalk abrir enquanto os BizTalk Services é atualizado para implementar num alterações ao serviço, poderá enfrentam os problemas de conectividade com o Portal de serviços do BizTalk.  
  Como solução, pode reiniciar o browser, eliminar a cache do browser ou iniciar o portal no modo privado.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE não é possível localizar o artefacto se clicar num erro ou aviso num projeto dos BizTalk Services
Instale o Visual Studio 2012 Update 3 RC 1 para corrigir o problema.  

### <a name="custom-binding-project-reference"></a>Referência de projecto do enlace personalizado
Considere as seguintes situações com um projeto dos BizTalk Services numa solução Visual Studio:  

* Na mesma solução Visual Studio, há um projeto dos BizTalk Services e um projeto do enlace personalizado. O projeto de serviço BizTalk contém uma referência a este ficheiro de projeto do enlace personalizado.
* O projeto de serviço BizTalk contém uma referência a um enlace/comportamento personalizado DLL.

'Criar' a solução no Visual Studio com êxito. Em seguida, 'Reconstruir' ou 'Limpar' a solução. Depois disso, quando reconstruir ou limpar novamente, ocorre o seguinte erro:  
  Não é possível copiar o ficheiro <Path to DLL> para "bin\Debug\FileName.dll". O processo não é possível aceder ao ficheiro 'bin\Debug\FileName.dll' porque está a ser utilizado por outro processo.  

#### <a name="workaround"></a>Solução
* Se [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) é instalado, tem duas opções seguintes:
  
  * Reinicie o Visual Studio, ou
  * Reinicie a solução. Em seguida, execute apenas uma compilação na solução.  
* Se [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) não está instalado, abra o Gestor de tarefas, clique nos processos de separador, clique o processo de MSBuild.exe e, em seguida, clique no botão de processo do fim.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Encaminhamento para pontos finais de BasicHttpRelay não é suportado de pontes e BizTalk Services Portal se carateres não imprimíveis são promovidos como cabeçalhos de HTTP
Se utilizar carateres não imprimíveis como parte das propriedades promovidas para mensagens, essas mensagens não podem ser encaminhadas para destinos de reencaminhamento que utilizam o enlace BasicHttpRelay. Além disso, as propriedades promovidas que estão disponíveis como parte do controlo com codificação URL para os blobs e anular codificado destinos.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN é enviada no modo assíncrono, mesmo que esteja marcada a opção de MDN assíncrona de envio
Considerar este cenário – se selecionou a **enviar MDN assíncrona** caixa de verificação e, especifique um URL para enviar o async MDN para e, em seguida, desmarque a **enviar MDN assíncrona** caixa de verificação novamente, o MDN ainda enviada para o especificar o URL, apesar de não estiver selecionada a opção de enviar async MDNs.  
Como solução, tem de limpar o URL especificado antes de desmarcando a **enviar MDN assíncrona** caixa de verificação e, em seguida, implementar o contrato de AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Os carateres de espaço em branco para além de um intercâmbio válido causam uma mensagem vazia sejam enviados para o ponto final de suspensão
Se existirem espaços em branco para além de um segmento IEA, o disassembler trata este como fim do intercâmbio atual e analisa o seguinte conjunto de espaços em branco como uma mensagem seguinte. Uma vez que isto não intercâmbio válido, poderá reparar que é enviada uma mensagem com êxito para o destino de rota e um vazio é enviado o ponto final de suspensão.  

### <a name="tracking-in-biztalk-services-portal"></a>Controlo no Portal de serviços BizTalk
Registo de eventos são capturados até o processamento de mensagens EDI e qualquer correlação. Se uma mensagem falhar fora a fase de protocolo, controlo irá mostrar como concluída com êxito. Nesta situação, consulte a secção de registo no **detalhes** coluna na **controlo** para detalhes do erro.
O X12 receber e enviar definições ([criar um X12 contrato nos BizTalk Services do Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fornecem informações sobre a fase de protocolo.  

### <a name="update-agreement"></a>Contrato de atualização
O Portal de serviços de BizTalk permite-lhe modificar o qualificador de uma identidade quando está configurado um contrato. Isto pode resultar em Propriedades de inconsistence. Por exemplo, é um contrato com ZZ:1234567 e ZZ:7654321 o qualificador. Nas definições de perfil de Portal de serviços do BizTalk, alterar ZZ:1234567 ser 01:ChangedValue. Abra o contrato e 01:ChangedValue é apresentado em vez de ZZ:1234567.
Para modificar o qualificador de uma identidade, elimine o contrato, atualizar **identidades** no perfil de parceiro e, em seguida, recrie o contrato.  

> AZURE. Este comportamento de aviso afeta X12 e AS2.  
> 
> 

### <a name="as2-attachments"></a>AS2 anexos
Os anexos para AS2 não são suportadas mensagens no enviar ou recebem mensagens. Especificamente, os anexos de forma silenciosa são ignorados e o corpo da mensagem é processado como uma mensagem de AS2 regular.  

### <a name="resources-remembering-path"></a>Recursos: Caminho de recordar
Ao adicionar **recursos**, a janela de caixa de diálogo não pode Lembre-se o caminho utilizado anteriormente para adicionar um recurso. Lembrar-se o caminho utilizado anteriormente, tente adicionar o web site do Portal de serviços de BizTalk para **Sites fidedignos** no Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Se o nome da entidade de uma ponte de mudar o nome e feche o projeto sem a guardar as alterações, abrir a entidade novamente resulta num erro
Considere um cenário pela seguinte ordem:  

* Adicionar uma ponte (por exemplo uma Bridge de One-Way de XML) para um projeto dos BizTalk Services  
* Mudar o nome a bridge especificando um valor para a propriedade de nome da entidade. Isto muda o nome do ficheiro de .bridgeconfig associados com o nome que especificou.  
* Feche o ficheiro de .bcs (ao fechar o separador no Visual Studio) sem guardar as alterações.  
* Abra o ficheiro de .bcs novamente a partir do Explorador de soluções.  
  Vai notar que enquanto o ficheiro de .bridgeconfig associado tem o nome da nova especificado, o nome da entidade na superfície do design ainda é o nome antigo. Se tentar abrir a configuração Bridge fazendo duplo clique no componente de bridge, obterá o erro seguinte:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist`Para evitar a execução para este cenário, certifique-se a que guardar as alterações, depois de mudar o nome de entidades de um projeto do BizTalk Service.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Projeto dos BizTalk Services baseia-se com êxito, mesmo se um artefacto foi excluído de um projeto do Visual Studio
Considere um cenário onde adiciona um artefacto (por exemplo, um ficheiro XSD) para um projeto do BizTalk Service, incluir essa artefactos na configuração Bridge (por exemplo, especificando-lo como um tipo de mensagem de pedido) e, em seguida, exclui-lo do projeto do Visual Studio. Nesse caso, compilar o projeto não fornecerá qualquer erro, desde que o artefacto eliminado está disponível no disco na mesma localização a partir de onde foi incluída no projeto do Visual Studio.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>O projeto de serviço BizTalk não verificar a disponibilidade de esquema, ao configurar os pontes
Num projeto BizTalk Service, se um esquema que seja adicionado ao projeto importa outra esquema, o projeto dos BizTalk Services não verifica se o esquema importado é adicionado ao projeto. Se tentar criar um projeto deste tipo, não a obter os erros de compilação.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>A mensagem de resposta para uma ponte de pedido-resposta XML é sempre do conjunto de carateres UTF-8
Nesta versão, o conjunto de carateres da mensagem de resposta de uma Bridge de pedido-resposta XML está sempre definido como UTF-8.
  
### <a name="user-defined-datatypes"></a>Tipos de dados definido pelo utilizador
Os adaptadores de BizTalk Adapter Pack na funcionalidade BizTalk Adapter Service podem utilizar tipos de dados definido pelo utilizador para operações de adaptador.
Quando utilizar tipos de dados definido pelo utilizador, copie os ficheiros (. dll) para unidade: \Programas\Microsoft BizTalk adaptador Service\BAServiceRuntime\bin\ ou a Global Assembly Cache (GAC) no servidor que aloja o serviço BizTalk Adapter Service. Caso contrário, poderão ocorrer o erro seguinte no cliente:  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> É recomendado utilizar GACUtil.exe para instalar um ficheiro para a Cache de assemblagem Global. GACUtil.exe documentos como utilizar esta ferramenta e as opções de linha de comandos do Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Reiniciar o serviço Web Site da placa de BizTalk
Instalar o **o tempo de execução do BizTalk adaptador de serviço*** cria o **BizTalk Adapter Service** web site no IIS que contém o **BAService** aplicação. **BAService** internamente aplicação utiliza o enlace de reencaminhamento para expandir o alcance do ponto final de serviço no local para a nuvem. Para um serviço alojado no local, o ponto final de reencaminhamento correspondente será registado no Service Bus apenas quando inicia o serviço no local.  

Se parar e iniciar uma aplicação, a configuração de início automático de uma aplicação não é respeitada. Por isso, quando **BAService** está parado, tem sempre de reiniciar o **BizTalk Adapter Service** web em vez disso, o site. Não iniciar ou parar o **BAService** aplicação.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Os carateres especiais não devem ser utilizados para nomes de endereço e a entidade de LOB componentes
Não deve utilizar carateres especiais para nomes de endereço e a entidade de componentes LOB. Se o fizer, irá receber um erro ao implementar o projeto do BizTalk Service. Determinados caracteres like '%', o Web site do BizTalk Adapter Service poderá ir para um Estado de paragem e terá de iniciar manualmente.

### <a name="test-map-with-get-context-property"></a>Mapa de teste com a propriedade de contexto de Get
Se uma transformação contém um **obter propriedade de contexto** operação mapa, **mapa de teste** irá falhar. Como solução temporária, substitua o **obter propriedade de contexto** mapa operação com uma cadeia concatenar mapa operação que contêm dados fictícias. Este procedimento irá preencher o esquema de destino e permitir a que testar outras funcionalidades de transformação.

### <a name="test-map-property-does-not-display"></a>Propriedade de mapa de teste não apresentar
O **mapa de teste** propriedades não são apresentados no Visual Studio. Isto pode ocorrer se o **propriedades** janela e a **Explorador de soluções** janela não estiver ancorado em simultâneo. Para resolver este problema, ancorar o **propriedades** e **Explorador de soluções** windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Pendente DateTime reformatar fica a cinzento
Quando uma operação de mapa de reformatar DateTime é adicionada a superfície de desenho e configurada, a formato na lista pendente poderão ser desativada. Isto pode acontecer se a apresentação do computador está definida **médio – 125%** ou **maiores – 150%**. Para resolver, defina a apresentação como **Smaller – 100% (predefinição)** utilizando os passos abaixo:  

1. Abra o **painel de controlo** e clique em **aspeto e personalização**.
2. Clique em **apresentar**.
3. Clique em **Smaller – 100% (predefinição)** e clique em **aplicar**.

O **formato** na lista pendente deve agora a funcionar conforme esperado.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Contratos duplicados no Portal de serviços BizTalk
Considere o seguinte cenário:

1. Crie um contrato com a API do OM da gestão de parceiros comerciais.
2. Abra o contrato no Portal de serviços do BizTalk dois separadores diferentes.
3. Implemente o contrato de ambos os separadores.
4. Como resultado, ambos os contratos implementados resultavam entradas duplicadas no Portal de serviços BizTalk

**Solução**. Abra qualquer uma dos contratos duplicados no Portal de serviços do BizTalk e undeploy.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Pontes não utilizar o certificado atualizado, mesmo depois de um certificado foi atualizado no arquivo de artefactos
Considere os seguintes cenários:  

**Cenário 1: Utilizar certificados com base em thumbprint para proteger a transferência de mensagens a partir de uma ponte para um ponto final de serviço**  
Considere um cenário em que utiliza certificados baseados em thumbprint no seu projeto de BizTalk Service. Atualizar o certificado no Portal de serviços do BizTalk com o mesmo nome mas outra impressão digital, mas não de atualizar o projeto de serviço BizTalk em conformidade. Neste cenário, a bridge poderá continuar a processar as mensagens, porque os dados de certificados mais antigos poderão ainda estar na cache de canal. Depois disso, o processamento da mensagem de falha.  

**Solução**: atualizar o certificado no projeto BizTalk Service e volte a implementar o projeto.  

**Cenário 2: Utilizar baseada no nome comportamentos para identificar os certificados para proteger a transferência de mensagens a partir de uma ponte para um ponto final de serviço**

Considere um cenário onde baseada no nome comportamentos que utiliza para identificar os certificados no seu projeto de BizTalk Service. Atualizar o certificado no Portal de serviços do BizTalk, mas não de atualizar o projeto de serviço BizTalk em conformidade. Neste cenário, a bridge poderá continuar a processar as mensagens, porque os dados de certificados mais antigos poderão ainda estar na cache de canal. Depois disso, o processamento da mensagem de falha.  

**Solução**: atualizar o certificado no projeto BizTalk Service e volte a implementar o projeto.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Pontes continuam a processar mensagens, mesmo quando a base de dados do SQL Server está offline
Pontes os BizTalk Services continuam a processar mensagens de tempo, mesmo que o Microsoft SQL Database do Azure (que armazena as informações em execução como artefactos implementados e pipelines), está offline. Isto acontece porque os BizTalk Services utiliza a artefactos em cache e a configuração de bridge.
Se não pretender que o pontes para processar mensagens quando a base de dados do SQL Server está offline, pode utilizar os cmdlets do PowerShell do BizTalk Services para parar ou suspender o BizTalk Service. Consulte [amostra de gestão de serviço do Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329019) para os cmdlets do Windows PowerShell gerir operações.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Ler a mensagem XML no componente de código personalizado uma ponte inclui um caráter de LM adicional
Considere um cenário em que pretende ler uma mensagem dentro do código personalizado uma ponte de XML. Se utilizar o System.Text.Encoding.UTF8.GetString(bytes) de API .NET um caráter adicional do LM está incluído na saída no início da mensagem. Por isso, se não pretender que o resultado inclui o caráter de LM adicional, tem de utilizar ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Envio de mensagens para uma ponte utilizando WCF não se dimensiona
As mensagens enviadas para uma ponte utilizando WCF não escalar. Em vez disso, deve utilizar HttpWebRequest se pretender que um cliente dimensionável.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>ATUALIZAÇÃO: Erro do fornecedor de tokens depois de atualizar do pré-visualização do BizTalk Services para disponibilidade geral (GA)
Há um EDI ou AS2 um contrato com o Active Directory lotes. Quando o BizTalk Service é atualizado a partir da pré-visualização para DG, pode ocorrer o seguinte:

* Erro: O fornecedor de tokens não foi possível fornecer um token de segurança. Fornecedor de tokens devolvido a mensagem: não foi possível resolver o nome remoto.
* Tarefas de lote foram canceladas.

**Solução**: após o BizTalk Service é atualizado para disponibilidade geral (GA), volte a implementar o contrato.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>ATUALIZAÇÃO: A caixa de ferramentas mostra os ícones de bridge antigo depois de atualizar o SDK dos BizTalk Services
Depois de atualizar uma versão anterior do SDK dos BizTalk Services, que tinha antigos ícones que representa os pontes, a caixa de ferramentas continua a mostrar os ícones antigos para os pontes. No entanto, se adicionar uma ponte a superfície do designer projeto BizTalk Service, a superfície de mostra no ícone novo.  

**Solução**. Pode contornar este problema, eliminando ficheiros. TBD <system drive>: \Users\<utilizador > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>ATUALIZAÇÃO: Atualização BizTalk Portal de pré-visualização GA poderá mostrar um erro que indica que a capacidade EDI não está disponível
Se tiver sessão iniciada no Portal de serviços do BizTalk enquanto os BizTalk Services é atualizado de pré-visualização para DG, poderá obterá o erro seguinte no portal:  

Esta capacidade não está disponível como parte nesta edição dos BizTalk Services do Microsoft Azure. Utilize estas capacidades de comutador para uma edição adequada.  

**Resolução**: fim de sessão do portal, feche e abra o browser e, em seguida, iniciar sessão no portal.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>ATUALIZAÇÃO: Novos dados de controlo não for apresentada depois dos BizTalk Services é atualizado para GA
Assuma um cenário em que tenha uma bridge XML implementado na subscrição de pré-visualização do BizTalk Services. Enviar mensagens para a bridge e os dados de controlo correspondente está disponível no Portal de serviços do BizTalk. Agora, se os bits de tempo de execução do BizTalk Services Portal e BizTalk Services são atualizados para GA e enviar uma mensagem para o ponto final bridge mesmo implementado anteriormente, os dados de controlo não for apresentada de mensagens enviadas depois da atualização.  

### <a name="pipelines-versus-bridges"></a>Pipelines versus pontes
Ao longo deste documento, o termo 'pipelines' e 'pontes' são utilizados-no alternadamente. Essencialmente, ambos significam a mesma coisa, que é, uma unidade de processamento de mensagens implementada dos BizTalk Services.  

### <a name="concepts"></a>Conceitos
[Serviços BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

