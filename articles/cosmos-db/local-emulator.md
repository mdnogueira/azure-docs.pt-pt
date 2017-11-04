---
title: Desenvolver localmente com o emulador de BD do Azure Cosmos | Microsoft Docs
description: "Utilizar o emulador de BD do Cosmos do Azure, pode desenvolver e testar a aplicação localmente para gratuitamente, sem criar uma subscrição do Azure."
services: cosmos-db
documentationcenter: 
keywords: Emulador do Cosmos BD do Azure
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2017
ms.author: arramac
ms.openlocfilehash: 5ea254110a24ea3315d614ebca2d43bda0e1a674
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Utilizar o emulador de BD do Cosmos do Azure para desenvolvimento local e o teste

<table>
<tr>
  <td><strong>Binários</strong></td>
  <td>[Transferir MSI](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Hub de docker](https://hub.docker.com/r/microsoft/azure-documentdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Origem de docker</strong></td>
  <td>[Github](https://github.com/azure/azure-documentdb-emulator-docker)</td>
</tr>
</table>
  
O emulador de BD do Cosmos do Azure fornece um ambiente local que emula o serviço de base de dados do Azure Cosmos para fins de desenvolvimento. Utilizar o emulador de BD do Cosmos do Azure, pode desenvolver e testar a aplicação localmente, sem criar uma subscrição do Azure ou incorrer em custos. Quando estiver satisfeito com a forma como a aplicação está a funcionar no emulador de BD do Cosmos do Azure, pode mudar para utilizar uma conta de base de dados do Azure Cosmos na nuvem.

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Instalar o emulador
> * Pedidos de autenticação
> * Utilizar o Explorador de dados no emulador
> * Exportar certificados SSL
> * Chamar o emulador na linha de comandos
> * Executar o emulador do Docker para Windows
> * Recolher ficheiros de rastreio
> * Resolução de problemas

Recomendamos que comece por ver o vídeo seguinte, onde Kirill Gavrylyuk mostra como começar a utilizar o emulador de BD do Cosmos do Azure. Tenha em atenção que o vídeo refere-se para o emulador do emulador do DocumentDB, mas a ferramenta de si próprio foi mudado o emulador de BD do Azure Cosmos desde taping o vídeo. Todas as informações no vídeo estão ainda estão corretas para o emulador de BD do Cosmos do Azure. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>Como funciona o emulador
O emulador de BD do Cosmos do Azure fornece uma emulação de alta-fidelidade do serviço Azure Cosmos DB. Suporta a funcionalidade idêntica como Cosmos BD do Azure, incluindo suporte para criar e consultar documentos JSON, aprovisionamento e dimensionamento coleções e a execução de acionadores e procedimentos armazenados. Pode desenvolver e testar aplicações utilizando o emulador de BD do Azure Cosmos e implementá-las para o Azure à escala global, fazendo apenas uma configuração única alterar para o ponto final de ligação de base de dados do Azure Cosmos.

Enquanto é criado uma emulação local de alta-fidelidade do serviço de base de dados do Azure Cosmos real, a implementação de emulador de BD do Cosmos do Azure é diferente do serviço. Por exemplo, o emulador de BD do Cosmos do Azure utiliza padrão SO componentes, tais como o sistema de ficheiros local para a persistência e pilha de protocolo HTTPS para conectividade. Isto significa que algumas funcionalidades que baseia-se na infraestrutura do Azure, tal como replicação global, latência de dígito milissegundo para leituras/escritas e níveis de consistência ajustáveis pelo não estão disponíveis através do emulador de BD do Cosmos do Azure.

> [!NOTE]
> Neste momento, o Explorador de dados no emulador só suporta a criação de coleções de API do DocumentDB e coleções do MongoDB. O Explorador de dados no emulador não suporta a criação de tabelas e gráficos. 

## <a name="differences-between-the-emulator-and-the-service"></a>Diferenças entre o emulador e o serviço 
Porque o emulador de BD do Cosmos do Azure fornece um ambiente emulado em execução numa estação de trabalho de programador local, existem algumas diferenças de funcionalidade entre o emulador e uma conta de base de dados do Azure Cosmos na nuvem:

* O emulador de BD do Cosmos do Azure suporta apenas uma única conta fixa e uma chave mestra bem conhecida.  Regeneração da chave não é possível no emulador de BD do Cosmos do Azure.
* O emulador de BD do Cosmos do Azure não é um serviço dimensionável e não suporta um grande número de coleções.
* O emulador de BD do Azure Cosmos não simular diferentes [níveis de consistência da base de dados do Azure Cosmos](consistency-levels.md).
* O emulador de BD do Azure Cosmos não simular [replicação de multirregião](distribute-data-globally.md).
* O emulador de BD do Cosmos do Azure não suporta as substituições de quota de serviço que estão disponíveis no serviço de base de dados do Azure Cosmos (por exemplo, os limites de tamanho do documento, armazenamento coleção particionada aumentada).
* Como a sua cópia do emulador de BD do Cosmos do Azure poderá não ser atualizada com as alterações mais recentes com o serviço de base de dados do Azure Cosmos, volte [Planeador de capacidade de base de dados do Azure Cosmos](https://www.documentdb.com/capacityplanner) para calcular com exatidão as necessidades de débito (RUs) de produção da sua aplicação.

## <a name="system-requirements"></a>Requisitos de sistema
O emulador de BD do Azure Cosmos tem os seguintes requisitos de hardware e software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 ou o Windows 10
*   Requisitos mínimos de Hardware
  * 2 GB DE RAM
  * 10 GB de espaço em disco de rígido disponível

## <a name="installation"></a>Instalação
Pode transferir e instalar o emulador de BD do Azure Cosmos do [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) ou pode executar o emulador no Docker para Windows. Para obter instruções sobre como utilizar o emulador no Docker para Windows, consulte [em execução no Docker](#running-on-docker). 

> [!NOTE]
> Para instalar, configurar e executar o emulador de BD do Cosmos do Azure, tem de ter privilégios administrativos no computador.

## <a name="running-on-windows"></a>Em execução no Windows

Para iniciar o emulador de BD do Cosmos do Azure, selecione o botão de início ou prima a tecla Windows. Começa a escrever **emulador de BD do Azure Cosmos**e selecione o emulador na lista de aplicações. 

![Selecione o botão de início ou prima a tecla Windows, começa a escrever * * Azure Cosmos DB emulador * * e selecione o emulador na lista de aplicações](./media/local-emulator/database-local-emulator-start.png)

Quando o emulador está em execução, verá um ícone na área de notificação da barra de tarefas do Windows. ![Notificação de barra de tarefas de emulador local Cosmos BD do Azure](./media/local-emulator/database-local-emulator-taskbar.png)

O emulador de BD do Azure Cosmos por predefinição, é executado no computador local está a escutar na porta 8081 ("localhost").

O emulador de BD do Cosmos do Azure está instalado por predefinição, para o `C:\Program Files\Azure Cosmos DB Emulator` diretório. Também pode iniciar e parar o emulador de linha de comandos. Consulte [referência da ferramenta da linha de comandos](#command-line) para obter mais informações.

## <a name="start-data-explorer"></a>Iniciar o Explorador de dados

Quando inicia o emulador de base de dados do Azure Cosmos automaticamente abre o Explorador de dados de base de dados do Azure Cosmos no seu browser. O endereço é apresentado como [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Se fechar o Explorador e gostaria de voltar a abri-lo mais tarde, pode abrir o URL no browser ou iniciar de emulador de BD do Cosmos do Azure no ícone de tabuleiro de Windows, conforme mostrado abaixo.

![Iniciador do Explorador de dados de emulador local do Azure Cosmos DB](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>A verificar a existência de atualizações
Explorador de dados indica se existe uma nova atualização disponível para transferência. 

> [!NOTE]
> Dados criados numa versão do emulador de BD do Cosmos do Azure não são garantidos esteja acessível ao utilizar uma versão diferente. Se tiver de manter os dados para a longo prazo, recomenda-se que armazene os dados numa conta do Azure Cosmos DB, em vez de no emulador de BD do Cosmos do Azure. 

## <a name="authenticating-requests"></a>Pedidos de autenticação
Tal como com a base de dados do Azure Cosmos na nuvem, todos os pedidos que tornam contra o emulador de BD do Cosmos do Azure tem de ser autenticado. O emulador de BD do Cosmos do Azure suporta uma única conta fixa e uma chave de autenticação conhecidos para a autenticação de chave mestra. Esta conta e chave são as credenciais apenas permitidas para utilização com o emulador de BD do Cosmos do Azure. São:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> A chave mestra suportada pelo Azure Cosmos DB emulador destina-se apenas com o emulador. Não é possível utilizar a conta de base de dados do Azure Cosmos de produção e a chave com o emulador de BD do Cosmos do Azure. 

> [!NOTE] 
> Se tiver iniciado o emulador com a opção de /Key, em seguida, utilize a chave gerada em vez de "C2y6yDjf5/R + ob0N8A7Cgv30VRDJIWEHLM + 4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw = ="

Além disso, apenas que o serviço de base de dados do Azure Cosmos, o emulador de BD do Cosmos do Azure suporta apenas uma comunicação segura através de SSL.

## <a name="running-on-a-local-network"></a>Em execução numa rede local

Pode executar o emulador numa rede local. Para ativar o acesso de rede, especifique a opção de /AllowNetworkAccess, o [linha de comandos](#command-line-syntax), que também requer que especifique /Key = key_string ou /KeyFile = file_name. Pode utilizar /GenKeyFile = file_name para gerar um ficheiro com uma chave aleatória compromisso.  Em seguida, pode passar que para /KeyFile = file_name ou /Key = contents_of_file.

Para ativar o acesso de rede pela primeira vez o utilizador deve encerrar o emulador e eliminar o diretório de dados do emulador (C:\Users\user_name\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Desenvolver com o emulador
Assim que tiver o emulador de BD do Cosmos do Azure em execução no seu ambiente de trabalho, pode utilizar qualquer suportado [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) ou [API REST da Azure Cosmos DB](/rest/api/documentdb/) para interagir com o emulador. O emulador de BD do Cosmos do Azure também inclui um Explorador de dados incorporada que permite-lhe criar coleções para o DocumentDB e MongoDB APIs e ver e editar documentos sem escrever qualquer código.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Se estiver a utilizar [Azure Cosmos DB o suporte de protocolos para MongoDB](mongodb-introduction.md), utilize a seguinte cadeia de ligação:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Pode utilizar ferramentas existentes como [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) para estabelecer ligação com o emulador de BD do Cosmos do Azure. Também pode migrar dados entre o emulador de BD do Cosmos do Azure e o serviço de base de dados do Azure Cosmos utilizando o [ferramenta de migração de dados do Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE] 
> Se tiver iniciado o emulador com a opção de /Key, em seguida, utilize a chave gerada em vez de "C2y6yDjf5/R + ob0N8A7Cgv30VRDJIWEHLM + 4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw = ="

Utilizando o emulador de BD do Cosmos do Azure, por predefinição, pode criar até 25 coleções de partições únicas ou 1 coleção particionada. Para obter mais informações sobre como alterar este valor, consulte [definindo o valor de PartitionCount](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Exportar o certificado SSL

Linguagens .NET e o tempo de execução utilizam o arquivo de certificados do Windows liguem de forma segura para o emulador local da base de dados do Azure Cosmos. Outros idiomas tem o seu próprio método de gestão e a utilização de certificados. Java utiliza o seu próprio [arquivo de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) enquanto utiliza o Python [socket wrappers](https://docs.python.org/2/library/ssl.html).

Para obter um certificado a utilizar com idiomas e tempos de execução que se integra com o arquivo de certificados do Windows, terá de exportá-lo utilizando o Gestor de certificados do Windows. Pode iniciá-la executando certlm.msc ou siga as instruções passo a passo [exportar os certificados de emulador de BD do Azure Cosmos](./local-emulator-export-ssl-certificates.md). Assim que estiver a executar o Gestor de certificados, abra os certificados pessoais, como mostrado abaixo e exportar o certificado com o nome amigável "DocumentDBEmulatorCertificate" como uma BASE-64 codificado ficheiro x. 509 (. cer).

![Certificado SSL de emulador local Cosmos BD do Azure](./media/local-emulator/database-local-emulator-ssl_certificate.png)

O certificado x. 509 pode ser importado para o arquivo de certificados do Java ao seguir as instruções no [a adição de um certificado para o arquivo de certificados de AC do Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Depois do certificado é importado para o arquivo de certificados, Java e MongoDB as aplicações poderão ligar para o emulador de BD do Cosmos do Azure.

Quando ligar para o emulador do Python e Node.js SDKs, verificação de SSL está desativada.

## <a id="command-line"></a>Referência da ferramenta da linha de comandos
Na localização de instalação, pode utilizar a linha de comandos para iniciar e parar o emulador, configure as opções e efetuar outras operações.

### <a name="command-line-syntax"></a>Sintaxe da linha de comandos

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Para ver a lista de opções, escreva `CosmosDB.Emulator.exe /?` na linha de comandos.

<table>
<tr>
  <td><strong>Opção</strong></td>
  <td><strong>Descrição</strong></td>
  <td><strong>Comando</strong></td>
  <td><strong>Argumentos</strong></td>
</tr>
<tr>
  <td>[Sem argumentos]</td>
  <td>Inicia o emulador de BD do Cosmos do Azure com as predefinições.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Ajuda]</td>
  <td>Mostra uma lista de argumentos da linha de comandos suportados.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Encerramento</td>
  <td>Encerramento do emulador de BD do Cosmos do Azure.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Especifica o caminho onde pretende armazenar os ficheiros de dados. Predefinição é de % LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath =&lt;datapath&gt;</td>
  <td>&lt;DataPath&gt;: um caminho acessível</td>
</tr>
<tr>
  <td>Porta</td>
  <td>Especifica o número de porta a utilizar para o emulador.  Predefinição é 8081.</td>
  <td>CosmosDB.Emulator.exe /Port =&lt;porta&gt;</td>
  <td>&lt;porta&gt;: número de porta individual</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Especifica o número de porta a utilizar para API de compatibilidade do MongoDB. Predefinição é 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort =&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: número de porta individual</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Especifica as portas a utilizar para a ligação direta. As predefinições são 10251,10252,10253,10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: lista delimitada por vírgulas de 4 portas</td>
</tr>
<tr>
  <td>Chave</td>
  <td>Chave de autorização para o emulador. Chave tem de ser a codificação base 64 de um vetor de 64 bytes.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;chave&gt;</td>
  <td>&lt;chave&gt;: chave tem de ser a codificação de um vetor de 64 bytes base-64</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Especifica que limitar o comportamento de taxa de pedidos está ativada.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Especifica essa taxa de pedidos de limitação de comportamento estiver desativada.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Não mostra o emulador de interface de utilizador.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Não mostra o Explorador de documentos no arranque.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Especifica o número máximo de coleções particionadas. Consulte [alterar o número de coleções](#set-partitioncount) para obter mais informações.</td>
  <td>CosmosDB.Emulator.exe /PartitionCount =&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: permissão de número máximo de coleções de partições únicas. Predefinição é 25. Máximo permitido é 250.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>Especifica o número predefinido de partições para uma coleção particionada.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount =&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; predefinição é 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Permite o acesso para o emulador através de uma rede. Tem de passar também /Key =&lt;key_string&gt; ou /KeyFile =&lt;file_name&gt; para ativar o acesso de rede.</td>
  <td>CosmosDB.Emulator.exe AllowNetworkAccess /Key =&lt;key_string&gt;<br><br>ou<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile =&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>Não ajuste as regras de firewall quando /AllowNetworkAccess é utilizado.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Gerar uma nova chave de autorização e guarde o ficheiro especificado. A chave gerada pode ser utilizada com as opções de /Key ou /KeyFile.</td>
  <td>CosmosDB.Emulator.exe /GenKeyFile =&lt;caminho para o ficheiro de chave&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Consistência</td>
  <td>Defina o nível de consistência predefinida para a conta.</td>
  <td>CosmosDB.Emulator.exe /Consistency =&lt;consistência&gt;</td>
  <td>&lt;consistência&gt;: valor tem de ser um dos seguintes [níveis de consistência](consistency-levels.md): BoundedStaleness, Eventual, forte ou Session.  O valor predefinido é de sessão.</td>
</tr>
<tr>
  <td>?</td>
  <td>Mostra a mensagem de ajuda.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>Alterar o número de coleções

Por predefinição, pode criar até 25 coleções de partições únicas ou 1 coleção particionada utilizando o emulador de BD do Cosmos do Azure. Ao modificar o **PartitionCount** valor, pode criar até 250 coleções de partições únicas ou 10 coleções particionadas ou qualquer combinação dos dois que não pode exceder 250 partições único (em que 1 particionada coleção = 25 partição única coleção).

Se tentar criar uma coleção após a contagem da partição atual foi excedida, o emulador emite uma exceção de ServiceUnavailable, com a seguinte mensagem.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Para alterar o número de coleções disponíveis para o emulador de BD do Cosmos do Azure, efetue o seguinte:

1. Eliminar todos os dados de emulador de BD do Azure Cosmos locais clicando com o **emulador de BD do Azure Cosmos** ícone no tabuleiro de sistema e, em seguida, clicar **Repor dados...** .
2. Elimine todos os dados de emulador nesta pasta C:\Users\user_name\AppData\Local\CosmosDBEmulator.
3. Sair abrir todas as instâncias clicando com o **emulador de BD do Azure Cosmos** ícone no tabuleiro de sistema e, em seguida, clicar **saída**. Pode demorar um minuto para todas as instâncias sair.
4. Instale a versão mais recente do [emulador de BD do Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Inicie o emulador com o sinalizador de PartitionCount definindo um valor < = 250. Por exemplo: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="running-on-docker"></a>Em execução no Docker

O emulador de BD do Cosmos do Azure pode ser executado num Docker para Windows. O emulador não funcionam em Docker para Oracle Linux.

Assim que tiver [Docker para Windows](https://www.docker.com/docker-windows) instalado, mude para contentores do Windows ao clicar no ícone de Docker na barra de ferramentas e selecionando **mudar para contentores de Windows**.

Em seguida, extraia a imagem do emulador do Docker Hub executando o seguinte comando a partir da sua shell favorito.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
Para iniciar a imagem, execute os seguintes comandos.

Na linha de comandos:
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

A partir do PowerShell:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

A resposta semelhante ao seguinte:

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Agora, utilize o ponto final e a chave mestra da resposta do cliente e importar o certificado SSL para o anfitrião. Para importar o certificado SSL, efetue o seguinte a partir de uma linha de comandos de administrador:

```
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

Fechar a shell interativa assim que o emulador tiver sido iniciado encerrado contentor o emulador.

Para abrir o Explorador de dados, navegue para o seguinte URL no browser. O ponto final do emulador é fornecido na mensagem de resposta mostrada acima.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Resolução de problemas

Utilize as sugestões seguintes para ajudar a resolver problemas que encontrar com o emulador de BD do Cosmos do Azure:

- Se instalou uma nova versão do emulador e estão a experienciar erros, certifique-se de que os dados de reposição. Pode repor os dados ao clicar no ícone do emulador de BD do Azure Cosmos no tabuleiro do sistema e, em seguida, clicando em dados de reposição... Se não corrigir os erros, pode desinstalar e reinstalar a aplicação. Consulte [desinstalar o emulador local](#uninstall) para obter instruções.

- Se o emulador de base de dados do Azure Cosmos falhar, recolher ficheiros de informação a partir da pasta c:\Users\user_name\AppData\Local\CrashDumps, comprimi-los e anexe-os a uma mensagem de e-mail para [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Se ocorrer falhas na CosmosDB.StartupEntryPoint.exe, execute o seguinte comando a partir de uma linha de comandos de administrador:`lodctr /R` 

- Se ocorrer um problema de conectividade, [recolher ficheiros de rastreio](#trace-files), comprimi-los e anexe-os a uma mensagem de e-mail para [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Se receber um **serviço indisponível** mensagem, o emulador poderá falhar ao inicializar a pilha de rede. Verifique se tiver o cliente seguras Pulse Juniper redes cliente ou instalado, como os respetivos controladores de filtro de rede podem fazer com que o problema. Desinstalar os controladores de filtro de rede de terceiros normalmente corrige o problema.

### <a id="trace-files"></a>Recolher ficheiros de rastreio

Para recolher rastreios de depuração, execute os seguintes comandos a partir de uma linha de comandos administrativa:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Veja tabuleiro do sistema para se certificar de que o programa foi encerrado, poderá demorar um minuto. Pode também simplesmente clicar **saída** na interface de utilizador do emulador de BD do Cosmos do Azure.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduza o problema. Se o Explorador de dados não está a funcionar, apenas terá de aguardar que o browser abrir por alguns segundos detetar o erro.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Navegue para `%ProgramFiles%\Azure Cosmos DB Emulator` e localize o ficheiro docdbemulator_000001.etl.
7. Enviar o ficheiro. etl juntamente com repro passos para [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) de depuração.

### <a id="uninstall"></a>Desinstalar o emulador local

1. Sair abrir todas as instâncias do emulador local ao clicar no ícone do emulador de BD do Azure Cosmos no tabuleiro do sistema e, em seguida, clicar em sair. Pode demorar um minuto para todas as instâncias sair.
2. Na caixa de pesquisa do Windows, escreva **aplicações e funcionalidades** e clique em de **aplicações e funcionalidades (definições do sistema)** resultado.
3. Na lista de aplicações, desloque para **emulador de BD do Azure Cosmos**, selecioná-lo, clique em **desinstalação**, em seguida, confirme e clique em **desinstalação** novamente.
4. Quando a aplicação é desinstalada, navegue para C:\Users\<utilizador > \AppData\Local\CosmosDBEmulator e elimine a pasta. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Instalou o emulador do local
> * Rand o emulador do Docker para Windows
> * Pedidos autenticados
> * Utilizar o Explorador de dados no emulador
> * Exportado certificados SSL
> * Chamar o emulador na linha de comandos
> * Ficheiros de rastreio recolhidos

Neste tutorial, aprendeu como utilizar o emulador local para o desenvolvimento local livre. Agora pode avançar para o próximo tutorial e aprender exportar certificados SSL do emulador. 

> [!div class="nextstepaction"]
> [Exportar os certificados de emulador de BD do Cosmos do Azure](local-emulator-export-ssl-certificates.md)
