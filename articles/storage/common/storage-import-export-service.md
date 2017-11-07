---
title: Utilizar Importar/exportar do Azure para transferir dados para e do armazenamento do Azure | Microsoft Docs
description: Saiba como criar importar e exportar tarefas no portal do Azure para transferir dados para e do armazenamento do Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 668f53f2-f5a4-48b5-9369-88ec5ea05eb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: muralikk
ms.openlocfilehash: 221bd7662eb4974395c7f970961d5bfb556417f4
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o Storage do Azure
Neste artigo, fornecemos instruções passo a passo sobre como utilizar o serviço importar/exportar do Azure para transferir de forma segura grandes quantidades de dados para o Blob storage do Azure e ficheiros do Azure por envio unidades de disco para um centro de dados do Azure. Este serviço também pode ser utilizado para transferir dados do storage do Azure para unidades de disco rígido e são enviados para os sites no local. Dados a partir de uma única unidade de disco SATA interna podem ser importados o Blob storage do Azure ou os ficheiros do Azure. 

> [!IMPORTANT] 
> Este serviço apenas aceita interno SATA HDDs ou SSDs apenas. Nenhum outro dispositivo é suportado. Não envie HDDs externos, dispositivos NAS, etc., conforme, serão devolvidos se for possível ou, caso contrário rejeitados.
>
>

Siga o procedimento abaixo se os dados no disco para serem importados para o Storage do Azure.
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>Passo 1: Preparar o unidade/s utilizando a ferramenta de WAImportExport e gerar diário de alterações. o ficheiro/s.

1.  Identifica os dados para ser importado para o Storage do Azure. Isto pode dever-se ao diretórios e ficheiros autónomos num servidor local ou uma partilha de rede.
2.  Dependendo do tamanho total dos dados, obter o número necessário de polegada 2,5 SSD ou 2,5" ou 3.5" SATA II ou III unidades de disco rígido.
3.  Anexe discos rígidos diretamente utilizando SATA ou com externos adaptors USB a uma máquina windows.
4.  Criar um único volume NTFS em cada unidade de disco rígida e atribuir uma letra de unidade ao volume. Não existem mountpoints.
5.  Ative a encriptação de cacifo de bits no NTFS volume. Utilize as instruções no https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx to enable encryption on the windows machine.
6.  Completamente copie dados para estes volumes NTFS único encriptados em discos utilizando copiar e colar ou arraste & drop ou Robocopy ou ferramenta deste tipo.
7.  Transferir WAImportExport V1 do https://www.microsoft.com/en-us/download/details.aspx?id=42659
8.  Deszipe a waimportexportv1 de pasta predefinida. Por exemplo, C:\WaImportExportV1  
9.  Executar como administrador, abra uma linha de comandos ou o PowerShell e altere o diretório para a pasta deszipada. Por exemplo, cd C:\WaImportExportV1
10. Copiar a abaixo da linha de comandos para um bloco de notas e editá-lo para criar uma linha de comandos.
  ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session n. º 1 /sk:***= = /t:D /bk:*** /srcdir:D: \ /dstdir:ContainerName / /skipwrite
    
    /j: o nome de um ficheiro denominado ficheiro de diário de alterações com .jrn extensão. Um ficheiro do diário de alterações é gerado por unidade e, por isso, recomenda-se para utilizar o número de série de disco como o nome de ficheiro do diário de alterações.
    /SK: a chave de conta de armazenamento do azure. /t: Letra de unidade do disco para enviada. Por exemplo, D /bk: é a chave do cacifo de bits de /srcdir a unidade: seguido de letra de unidade do disco para enviada: \. Por exemplo, D:\
    /dstdir: o nome do contentor de armazenamento do Azure para o qual os dados estão a ser importados.
    /skipwrite 
    
11. Repita o passo 10 para cada disco que tem de ser fornecidos.
12. É criado um ficheiro de diário de alterações com o nome fornecido com o parâmetro /j: para cada execução da linha de comandos.

### <a name="step-2-create-an-import-job-on-azure-portal"></a>Passo 2: Crie uma tarefa de importação no Portal do Azure.

1. ARMAZENAMENTO -> de registo para https://portal.azure.com/ e em mais serviços -> "para importar/exportar tarefas" clique **criar tarefa de importação/exportação**.

2. Na secção de noções básicas, selecione "Importar para o Azure", introduza uma cadeia para o nome da tarefa, selecione uma subscrição, introduza ou selecione um grupo de recursos. Introduza um nome descritivo para a tarefa de importação. Tenha em atenção que o nome introduzido pode conter apenas letras minúsculas, números, hífenes e carateres de sublinhado, tem de começar com uma letra e não pode conter espaços. Irá utilizar o nome que escolher para controlar as tarefas enquanto estiverem em curso e depois de serem concluídas.

3. Na secção de detalhes da tarefa, carregar os ficheiros do diário de alterações de unidade que obteve durante o passo de preparação de unidade. Se utilizou waimportexport.exe version1, terá de carregar um ficheiro para cada unidade que está preparado. Selecione a conta de armazenamento que os dados serão importados para a secção de conta de armazenamento de "Destino de importação". A localização de Drop-Off será automaticamente preenchida com base na região da conta de armazenamento selecionada.
   
   ![Criar tarefa de importação - passo 3](./media/storage-import-export-service/import-job-03.png)
4. Return na secção de informações de envio, selecione a operadora na lista pendente e introduza um número de conta operadora válida que tenha criado com esse operadora. Microsoft irá utilizar esta conta para enviar as unidades para, assim que a tarefa de importação estiver concluída. Forneça um nome do contacto completo e válido, telefone, e-mail, morada, cidade, zip, proviince/estado e país/região.
   
5. Na secção de resumo, DataCenter do Azure, endereço de envio é fornecido para ser utilizado para envio discos do Azure DC. Certifique-se de que o nome da tarefa e o endereço completo são mencionadas na etiqueta do envio. 

6. Clique em OK na página de resumo para concluir a criação da tarefa de importação.

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>Passo 3: Incorporadas unidade/s para o Datacenter do Azure endereço fornecido no passo 2 de envio.
FedEx, UPS ou DHL pode ser utilizado para enviar o pacote de controlador do Azure.

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>Passo 4: Atualize a tarefa criada no Step2 com o número do shipment de controlo.
Depois dos discos de envio, volte para o **importar/exportar** página no portal do Azure para atualizar o número de controlo utilizando os passos abaixo, um) navegue até e clique em Importar tarefa b) clique na **Atualizar estado da tarefa e informações de controlo Depois de unidades são fornecidas**. c) selecione a caixa de verificação "Marcar como enviado" d) forneça o número de Carrier e controlo.
Se o número de controlo não forem atualizado dentro de duas semanas a tarefa de criação, a tarefa irá expirar. A evolução da tarefa pode ser controlada no dashboard do portal. Consulte o que significa cada Estado de tarefa na secção anterior por [visualizar o estado da tarefa](#viewing-your-job-status).

## <a name="when-should-i-use-the-azure-importexport-service"></a>Em que casos é que devo utilizar o serviço Importar/Exportar do Azure?
Considere utilizar o serviço importar/exportar do Azure quando carregamento ou transferência de dados através da rede é demasiado lenta ou obter largura de banda de rede adicional é proibitivos em termos de custo.

Pode utilizar este serviço, tal como nos cenários:

* Migrar dados para a nuvem: mover rapidamente grandes quantidades de dados para o Azure e o custo de forma eficaz.
* Distribuição de conteúdo: rapidamente enviar dados para os sites de cliente.
* Cópia de segurança: Efetuar cópias de segurança dos seus dados no local para armazenar no armazenamento do Azure.
* Recuperação de dados: recuperar grande quantidade de dados armazenados no armazenamento e tiver entregues à sua localização no local.

## <a name="prerequisites"></a>Pré-requisitos
Nesta secção, iremos lista os pré-requisitos necessários para utilizar este serviço. Reveja-los cuidadosamente antes de envio suas unidades.

### <a name="storage-account"></a>Conta de armazenamento
Tem de ter uma subscrição do Azure existente e uma ou mais contas de armazenamento para utilizar o serviço de importação/exportação. Cada tarefa pode ser utilizada para transferir dados de ou para apenas uma conta de armazenamento. Por outras palavras, uma tarefa de importação/exportação único não pode abranger várias várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte [como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

### <a name="data-types"></a>Tipos de dados
Pode utilizar o serviço importar/exportar do Azure para copiar dados para **bloco** blobs, **página** blobs, ou **ficheiros**. Por outro lado, só pode exportar **bloco** blobs, **página** blobs ou **acrescentar** blobs storage do Azure utilizar este serviço. O serviço suporta apenas importação de ficheiros do Azure para o armazenamento do Azure. Exportar os ficheiros do Azure não é atualmente suportado.

### <a name="job"></a>Tarefa
Para iniciar o processo de importação para ou exportar do armazenamento, primeiro tem de criar uma tarefa. Uma tarefa pode ser uma tarefa de importação ou uma tarefa de exportação:

* Crie uma tarefa de importação quando pretender transferir os dados no local que tem à sua conta de armazenamento do Azure.
* Crie uma tarefa de exportação quando pretender transferir os dados atualmente armazenados na sua conta de armazenamento para discos rígidos são enviadas para nós. Quando cria uma tarefa, notifique o serviço de importação/exportação que que irá enviar um ou mais unidades de disco rígido para um centro de dados do Azure.

* Para uma tarefa de importação, será de envio unidades de disco rígido que contém os dados.
* Para uma tarefa de exportação, será de envio unidades de disco rígido em branco.
* Pode enviar até 10 unidades de disco rígido por tarefa.

Pode criar uma importação ou exportação tarefa no portal do Azure ou o [API de REST de importação/exportação do Azure Storage](/rest/api/storageimportexport).

### <a name="waimportexport-tool"></a>Ferramenta de WAImportExport
O primeiro passo na criação de um **importar** tarefa está a preparar a unidades que será enviada para a importação. Para preparar as suas unidades, tem de estabelecer a ligação ao servidor local e execute a ferramenta de WAImportExport no servidor local. Esta ferramenta WAImportExport facilita a copiar os dados para a unidade, encriptar os dados na unidade com BitLocker e gerar os ficheiros do diário de alterações de unidade.

Os ficheiros do diário de alterações armazenam informações básicas sobre a tarefa e a unidade como o número de série da unidade e o nome da conta de armazenamento. Este ficheiro de diário de alterações não é armazenado na unidade. É utilizado durante a criação da tarefa de importação. Neste artigo, são fornecidos detalhes passo a passo sobre a criação da tarefa.

A ferramenta de WAImportExport só é compatível com o sistema de operativo do Windows de 64 bits. Consulte o [sistema operativo](#operating-system) na secção versões específicas de SO suportada.

Transfira a versão mais recente do [WAImportExport ferramenta](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). Para obter mais detalhes sobre como utilizar a ferramenta de WAImportExport, consulte o [utilizando a ferramenta de WAImportExport](storage-import-export-tool-how-to.md).

>[!NOTE]
>**A versão anterior:** pode [transferir WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) versão da ferramenta e consulte [guia de utilização de WAImportExpot V1](storage-import-export-tool-how-to-v1.md). WAImportExpot V1 versão da ferramenta de oferecer suporte para **preparar discos quando são escritos dados já previamente no disco**. Também terá de utilizar a ferramenta WAImportExpot V1 se a chave apenas disponível é a chave SAS.

>

### <a name="hard-disk-drives"></a>Unidades de disco rígido
Apenas 2,5 polegada SSD ou 2,5" ou 3.5" SATA II ou HDD interno III é suportados para utilização com o serviço de importação/exportação. Uma tarefa de importação/exportação única pode ter um máximo de 10 HDD/SSD e HDD cada individuais/SSD pode ser de qualquer tamanho. Grande número de unidades de serem distribuído por várias tarefas e não existe nenhum limites no número de tarefas que podem ser criadas. 

Para tarefas de importação, apenas o primeiro volume de dados na unidade será processado. O volume de dados deve ser formatado com NTFS.

> [!IMPORTANT]
> Unidades de disco rígido externas que vêm com um adaptador USB incorporado não são suportadas por este serviço. Além disso, não é possível utilizar o disco dentro as maiúsculas e minúsculas de um HDD externo; Não envie HDDs externos.
> 
> 

Segue-se uma lista de adaptors USB externos utilizados para copiar dados em HDDs internos. Anker 68UPSATAA - 02BU Anker 68UPSHHDS BU Startech SATADOCK22UE Orico 6628SUS3-C-BK (6628 série) Thermaltake BlacX frequente troca SATA externo rígido unidade ancoragem estação (2.0 de USB & eSATA)

### <a name="encryption"></a>Encriptação
Os dados na unidade tiverem de ser encriptados utilizando a encriptação de unidade BitLocker. Esta ação protege os dados enquanto esta se encontra em trânsito.

Para tarefas de importação, existem duas formas para efetuar a encriptação. É a forma primeiro para especificar a opção ao utilizar o ficheiro CSV de conjunto de dados ao executar a ferramenta de WAImportExport durante a preparação de unidade. A segunda forma consiste em ativar a encriptação BitLocker manualmente na unidade e especifique a chave de encriptação no driveset CSV ao executar a linha de comandos de ferramenta WAImportExport durante a preparação de unidade.

Para as tarefas de exportação, depois dos dados são copiados para as unidades, o serviço de encriptar a unidade do utilizar o BitLocker antes de envio-lo novamente para si. A chave de encriptação será fornecida para o utilizador através do portal do Azure.  

### <a name="operating-system"></a>Sistema Operativo
Pode utilizar um dos seguintes sistemas operativos de 64 bits para preparar a unidade de disco rígido com a ferramenta de WAImportExport antes do envio a unidade para o Azure:

Windows 7 Enterprise, Ultimate do Windows 7, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Todos estes sistemas operativos suportam a encriptação de unidade BitLocker.

### <a name="locations"></a>Localizações
O serviço de importação/exportação do Azure suporta a cópia de dados de e para todas as contas de armazenamento do Azure público. Pode enviar unidades de disco rígido para uma das seguintes localizações. Se a sua conta do storage está numa localização pública do Azure que não for especificada aqui, uma localização alternativa de envio será fornecida quando estiver a criar a tarefa utilizando o portal do Azure ou a API de REST de importação/exportação.

Suportado envio localizações:

* EUA Leste
* EUA Oeste
* EUA Leste 2
* EUA Oeste 2
* EUA Central
* EUA Centro-Norte
* EUA Centro-Sul
* EUA Centro-Oeste
* Europa do Norte
* Europa Ocidental
* Ásia Oriental
* Sudeste Asiático
* Leste da Austrália
* Sudeste da Austrália
* Oeste do Japão
* Leste do Japão
* Índia Central
* Sul da Índia
* Índia Ocidental
* Canadá Central
* Leste do Canadá
* Sul do Brasil
* Coreia Central
* Gov (US) - Virginia
* Gov (US) - Iowa
* US DoD Leste
* US DoD Centro
* Leste da China
* China Norte
* Reino Unido Sul

### <a name="shipping"></a>Envio
**Unidades de envio para o Centro de dados:**

Ao criar uma tarefa de importação ou exportação, irá ser fornecido um endereço de envio de uma das localizações suportadas para enviar as suas unidades. O endereço de envio fornecido dependerá da localização da sua conta de armazenamento, mas não pode ser o mesmo que a localização da conta de armazenamento.

FedEx, UPS ou DHL pode ser utilizado para enviar as unidades para o endereço de envio.

**Unidades de envio do Centro de dados:**

Ao criar uma tarefa de importação ou exportação, tem de fornecer um endereço de retorno para a Microsoft a utilizar quando as unidades de envio anterior depois de concluída a tarefa. Certifique-se que fornecer um endereço de retorno válido para evitar atrasos no processamento.

A operadora deve ter controlo apropriados para manter a cadeia de custody. Tem de fornecer uma operadora FedEx, UPS ou DHL válida conta o número a ser utilizada pela Microsoft para as unidades de cópia do envio. Um número de conta FedEx, UPS ou DHL é necessário para unidades de envio anterior a partir de localizações dos EUA e na Europa. Um número de conta DHL é necessário para unidades de envio anterior a partir de localizações Ásia e da Austrália. Pode criar um [FedEx](http://www.fedex.com/us/oadr/) (para E.U.A. e Europa) ou [DHL](http://www.dhl.com/) (Ásia e da Austrália) operadora a conta se não tiver um. Se já tiver um número de conta operadora, verifique se é válido.

No seus pacotes de envio, tem de seguir os termos de licenciamento em [termos de serviço do Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Tenha em atenção que o suporte de dados físicos que são envio poderá ter de cruzada internacionais limites. É responsável por assegurar que os dados e suportes de dados físicos são importados e/ou exportados de acordo com as leis aplicáveis. Antes do envio do suporte de dados físico, consulte o seu advisers para verificar que os dados e suportes de dados podem legalmente enviados para o Centro de dados identificados. Isto irá ajudar a garantir que atinge Microsoft atempadamente. Por exemplo, qualquer pacote que será entre limites internacionais tem uma fatura comercial para ser acompanhado com o pacote (exceto se cruzamento entre limites dentro da União Europeia). Pode imprimir uma cópia manchas a fatura comercial a partir do site da operadora. Exemplo de faturas comerciais são [fatura comerciais DHL](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) e [fatura comerciais FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Certifique-se de que a Microsoft não foram indicou como o exportador.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Como funciona o serviço importar/exportar do Azure?
Pode transferir dados entre o site no local e o armazenamento do Azure utilizando o serviço de importação/exportação do Azure através da criação de tarefas e envio de unidades de disco rígido para um centro de dados do Azure. Cada unidade de disco rígido que são enviados está associada uma única tarefa. Cada tarefa está associada uma única conta de armazenamento. Reveja o [secção pré-requisitos](#pre-requisites) cuidadosamente para saber mais sobre as especificações deste serviço, tais como tipos de dados suportadas, o disco envio, tipos e localizações.

Nesta secção, vamos descrever a um nível elevado, os passos envolvidos na importar e exportar tarefas. Mais à frente do [secção início rápido](#quick-start), fornecemos instruções passo a passo para criar uma importação e exportação de tarefa.

### <a name="inside-an-import-job"></a>Dentro de uma tarefa de importação
Um nível elevado, uma tarefa de importação envolve os seguintes passos:

* Determine os dados a ser importados e o número de unidades que precisa.
* Identifica a localização de blob ou um ficheiro de destino para os seus dados no armazenamento do Azure.
* Utilize a ferramenta de WAImportExport para copiar os dados para um ou mais unidades de disco rígido e encriptá-los com o BitLocker.
* Crie uma tarefa de importação na sua conta de armazenamento de destino utilizando o portal do Azure ou a API de REST de importação/exportação. Se utilizar o portal do Azure, carregar os ficheiros do diário de alterações de unidade.
* Forneça o endereço do remetente e o número de conta operadora a ser utilizado para as unidades de envio anterior para si.
* Envie as unidades de disco rígido para o endereço de envio fornecido durante a criação da tarefa.
* Atualize a entrega controlo número nos detalhes da tarefa de importação e submeter a tarefa de importação.
* Unidades são recebidas e processadas no Centro de dados do Azure.
* Unidades são fornecidas com a sua conta operadora para o endereço do remetente fornecido na tarefa de importação.
  
    ![Figura 1:Import fluxo de trabalho](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Dentro de uma tarefa de exportação
> [!IMPORTANT]
> O serviço suporta apenas a exportação de Blobs do Azure e não suporta a exportação de ficheiros do Azure...
> 
>

Um nível elevado, uma tarefa de exportação envolve os seguintes passos:

* Determine os dados para ser exportada e o número de unidades que precisa.
* Identifique os blobs de origem ou caminhos do contentor dos seus dados no Blob storage.
* Crie uma tarefa de exportação na sua conta de armazenamento de origem utilizando o portal do Azure ou a API de REST de importação/exportação.
* Especifique os blobs de origem ou caminhos do contentor dos seus dados na tarefa de exportação.
* Fornece o endereço do remetente e o número de conta de carrier para a utilizar para as unidades de envio anterior para si.
* Envie as unidades de disco rígido para o endereço de envio fornecido durante a criação da tarefa.
* Atualize a entrega controlo número nos detalhes da tarefa de exportação e submeter a tarefa de exportação.
* As unidades são recebidas e processadas no Centro de dados do Azure.
* As unidades são encriptadas com BitLocker; as chaves estão disponíveis através do portal do Azure.  
* As unidades são fornecidas com a sua conta operadora para o endereço do remetente fornecido na tarefa de importação.
  
    ![Figura 2:Export fluxo de trabalho](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>Visualizar o estado de tarefa e unidade
Pode controlar o estado da sua importação ou exportação tarefas do portal do Azure. Clique em de **importar/exportar** separador. Uma lista das suas tarefas irão aparecer na página.

![Vista de estado da tarefa](./media/storage-import-export-service/jobstate.png)

Irá ver um dos seguintes Estados de tarefas, dependendo de onde a unidade está no processo.

| Estado da tarefa | Descrição |
|:--- |:--- |
| Criação | Depois de uma tarefa é criada, o estado é definido para o criar. Enquanto a tarefa está no estado de criar, assume que o serviço importar/exportar as unidades não foram fornecidas para o Centro de dados. Uma tarefa pode permanecer no estado de criar para até duas semanas, após o qual é automaticamente eliminado pelo serviço. |
| Envio | Depois de enviar o pacote, deve atualizar as informações de controlo no portal do Azure.  Isto irá ativar a tarefa para "Envio". A tarefa irá permanecer no estado de envio para duas semanas. 
| Foi recebido | Depois de todas as unidades foram recebidas no Centro de dados, o estado da tarefa será definido para o recebidos. |
| Transferência de | Depois de pelo menos uma unidade começou a processar, o estado da tarefa será definido para o Transferring. Consulte a secção de Estados de unidade abaixo para obter informações detalhadas. |
| Empacotamento | Depois de todas as unidades concluiu o processamento, a tarefa será colocada no estado de empacotamento até que as unidades são fornecidas para lhe. |
| Foi concluída | Depois de todas as unidades foram fornecidas para o cliente, se a tarefa tiver sido concluída sem erros, a tarefa será definida para o estado de concluído. A tarefa será eliminada automaticamente após 90 dias no estado concluído. |
| Fechado | Depois de todas as unidades foram fornecidas para o cliente, se tiverem sido erros durante o processamento da tarefa, a tarefa será definida para o estado fechado. A tarefa será eliminada automaticamente após 90 dias no estado fechado. |

A tabela abaixo descreve o ciclo de vida de uma unidade individuais, que passa através de uma tarefa de importação ou exportação. O estado atual de cada unidade numa tarefa agora está visível no portal do Azure.
A tabela seguinte descreve cada Estado de cada unidade de uma tarefa pode pass-through.

| Unidade de estado | Descrição |
|:--- |:--- |
| Especificado | Para uma tarefa de importação, quando a tarefa é criada a partir do portal do Azure, o estado inicial para uma unidade é o estado especificado. Para uma tarefa de exportação, uma vez que não existem unidade é especificada quando a tarefa é criada, o estado de unidade inicial é Estado Received. |
| Foi recebido | Transições de unidade para o estado de recebidos quando o operador de serviço de importação/exportação processou as unidades que foram recebidas a partir da empresa de envio para uma tarefa de importação. Para uma tarefa de exportação, o estado da unidade inicial é Estado Received. |
| NeverReceived | A unidade irá mudar para o estado de NeverReceived quando chega o pacote para uma tarefa, mas o pacote não contém a unidade. Pode também mover uma unidade neste estado se duas semanas, uma vez que o serviço recebeu as informações de envio, mas o pacote ainda não chegaram do Centro de dados. |
| Transferência de | Uma unidade será movido para o estado de Transferring quando o serviço começa a transferência de dados da unidade para armazenamento do Microsoft Azure. |
| Foi concluída | Uma unidade será movido para o estado de concluído, quando o serviço com êxito tenha transferido todos os dados sem erros.
| CompletedMoreInfo | Uma unidade será movido para o estado de CompletedMoreInfo quando o serviço detetou alguns problemas ao copiar dados a partir de ou para a unidade. As informações podem incluir erros, avisos e mensagens informativas sobre substituir blobs.
| ShippedBack | A unidade irá mudar para o estado de ShippedBack quando foi enviada a partir de cópia de segurança de centro de dados para o endereço do remetente. |

Esta imagem do portal do Azure apresenta o estado de unidade de uma tarefa de exemplo:

![Vista de estado de unidade](./media/storage-import-export-service/drivestate.png)

A tabela seguinte descreve os Estados de falha de unidade e as ações executadas para cada Estado.

| Unidade de estado | Evento | Resolução / passo seguinte |
|:--- |:--- |:--- |
| NeverReceived | Uma unidade que está marcada como NeverReceived (porque não foi recebida como parte da shipment da tarefa) chega no shipment outro. | A equipa de operações irá mover a unidade para o estado de funcionamento recebidos. |
| N/D | Uma unidade que não faz parte de qualquer tarefa chega ao centro de dados como parte de outra tarefa. | A unidade será marcada como uma unidade adicional e será devolvida ao cliente quando a tarefa associada ao pacote original estiver concluída. |

### <a name="time-to-process-job"></a>Tempo até ao processo de trabalho
A quantidade de tempo que demora a processar uma tarefa de importação/exportação varia dependendo de fatores diferentes, tais como o tempo de envio, tipo, tipo e tamanho dos dados que está a ser copiados e o tamanho dos discos fornecido da tarefa. O serviço de importação/exportação não tem um SLA mas depois dos discos são recebidos o serviço strives concluir a cópia dentro de 7 a 10 dias. Pode utilizar a API REST para controlar o progresso da tarefa mais detalhadamente. Há um parâmetro concluído percentagem na operação de lista de tarefas que lhe dá uma indicação de progresso de cópia. Entrar-nos se necessitar de uma estimativa para concluir uma tarefa de importação/exportação crítico de tempo.

### <a name="pricing"></a>Preços
**Taxa de processamento de unidade**

Há uma taxa de processamento de unidade para cada unidade processada como parte da sua importação ou exportação tarefa. Consulte os detalhes sobre o [preços do Azure para importar/exportar](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Os custos de envio**

Quando são enviados unidades para o Azure, paga um custo o envio para o operador de envio. Quando a Microsoft devolve as unidades para si, o custo de envio é cobrado a conta de carrier que fornecido no momento da criação da tarefa.

**Custos de transação**

Não existem sem custos de transação ao importar dados para armazenamento do Azure. Os encargos de saída padrão são aplicáveis quando os dados são exportados do armazenamento de Blobs. Para obter mais detalhes sobre os custos de transação, consulte [preços de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>Como importar dados para utilização interna SATA HDDs e SSDs de File Storage do Azure?
Siga o procedimento abaixo se os dados no disco para serem importados para o File Storage do Azure.
É o primeiro passo ao importar dados utilizando o serviço importar/exportar do Azure para preparar as suas unidades com a ferramenta de WAImportExport. Siga os passos abaixo para preparar as suas unidades.

1. Identifica os dados para ser importado para o File Storage do Azure. Isto pode dever-se ao diretórios e ficheiros autónomos no servidor local ou uma partilha de rede.  
2. Determine o número de unidades que precisa de, dependendo do tamanho total dos dados. Obter o número necessário de polegada 2,5 SSD ou 2,5" ou 3.5" SATA II ou III unidades de disco rígido.
4. Determine os diretórios e/ou os ficheiros de autónomo que serão copiados para cada unidade de disco rígido.
5. Crie os ficheiros CSV para o conjunto de dados e driveset.
    
  Segue-se um exemplo de ficheiro CSV ao conjunto de dados de exemplo para importar dados como ficheiros do Azure:
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   No exemplo acima, 100M_1.csv.txt serão copiados para a raiz da "partilha de ficheiros". Se a "partilha de ficheiros" não existir, será criada. Todos os ficheiros e pastas sob 50M_original será em modo recursivo copiado para a partilha de ficheiros. Irá ser mantida a estrutura de pastas.

    Saiba mais sobre [preparar o ficheiro CSV de conjunto de dados](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    


    **Ficheiro Driveset CSV**

    O valor do sinalizador driveset é um ficheiro CSV que contém a lista de discos nos quais as letras de unidade estão mapeadas por ordem para a ferramenta recolher corretamente a lista de discos para estar preparado. 

    Segue-se ao exemplo do ficheiro CSV de driveset:
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    No exemplo acima, presume-se que os dois discos estão anexados e básicos NTFS volumes com letra de volume G:\ e H:\ foram criados. A ferramenta irá formatar e encriptar o disco que aloja H:\ e irá não formatar ou não encriptar o disco que aloja volume G:\.

    Saiba mais sobre [preparar ficheiro CSV driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.  Utilize o [WAImportExport ferramenta](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) para copiar os dados para um ou mais unidades de disco rígido.
7.  Pode especificar "Encriptar" no campo de encriptação no drivset CSV para ativar a encriptação BitLocker na unidade de disco rígido. Em alternativa, pode também ativar a encriptação BitLocker manualmente na unidade de disco rígido e especificar "AlreadyEncrypted" e fornecer a chave no driveset CSV ao executar a ferramenta.

8. Não modifique os dados em unidades de disco rígido ou o ficheiro de diário depois de concluir a preparação de disco.

> [!IMPORTANT]
> Cada unidade de disco rígido, preparar resultará num ficheiro diário de alterações. Quando estiver a criar a tarefa de importação no portal do Azure, tem de carregar todos os ficheiros do diário de alterações de unidades que fazem parte dessa tarefa de importação. Unidades sem diário de alterações não serão possível processar os ficheiros.
> 
>

Seguem-se os comandos e exemplos para preparar a unidade de disco rígido utilizando a ferramenta de WAImportExport.

Comando de PrepImport de ferramenta WAImportExport para a primeira sessão de cópia copiar diretórios e/ou os ficheiros com uma nova sessão de cópia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Importar exemplo 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Para **adicionar mais unidades**, um pode criar um novo ficheiro de driveset e execute o comando, tal como indicado abaixo. Para as sessões de cópia subsequentes para as unidades de disco diferentes superior ao especificado no ficheiro. csv de InitialDriveset, especifique um novo ficheiro CSV driveset e fornecê-lo como um valor ao parâmetro "AdditionalDriveSet". Utilize o **mesmo ficheiro de diário** nome e fornecem um **novo ID de sessão**. O formato do ficheiro AdditionalDriveset CSV é igual ao formato InitialDriveSet.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Importar exemplo 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

Para poder adicionar dados adicionais para o mesmo driveset, WAImportExport ferramenta PrepImport comando pode ser chamada para sessões subsequentes cópia copiar ficheiros adicional ou directório: para sessões de cópia subsequentes para as unidades de disco rígido mesmas especificadas no ficheiro. csv de InitialDriveset, especifique o **mesmo ficheiro de diário** nome e fornecem um **novo ID de sessão**; não é necessário para fornecer a chave de conta de armazenamento.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Importar exemplo 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Ver mais detalhes sobre como utilizar a ferramenta de WAImportExport no [preparar unidades de disco rígido para importação](storage-import-export-tool-preparing-hard-drives-import.md).

Além disso, consulte o [fluxo de trabalho de exemplo para preparar os discos rígidos de uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) para obter mais instruções passo a passo.  



## <a name="create-an-export-job"></a>Criar uma tarefa de exportação
Crie uma tarefa de exportação para notificar o serviço de importação/exportação que irá ser envio uma ou mais unidades para o Centro de dados vazias para que os dados podem ser exportados da sua conta de armazenamento para as unidades e as unidades, em seguida, fornecidos para si.

### <a name="prepare-your-drives"></a>Preparar as suas unidades
Verificações prévias seguintes são recomendadas para preparar a unidades para uma tarefa de exportação:

1. Verifique o número de discos necessários utilizando o comando de PreviewExport a ferramenta WAImportExport. Para obter mais informações, consulte [pré-visualização utilização da unidade para uma tarefa de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). Ajuda a utilização da unidade de pré-visualização para os blobs que selecionou, com base no tamanho das unidades que pretende utilizar.
2. Verifique se o pode leitura/escrita no disco rígido que será enviada para a tarefa de exportação.

### <a name="create-the-export-job"></a>Criar a tarefa de exportação
1. Para criar uma tarefa de exportação, navegue até ao mais serviços -> armazenamento -> "para importar/exportar tarefas" no portal do Azure. Clique em **criar tarefa de importação/exportação**.
2. No passo 1, Noções básicas sobre, selecione "Exportar do Azure", introduza uma cadeia para o nome da tarefa, selecione uma subscrição, introduza ou selecione um grupo de recursos. Introduza um nome descritivo para a tarefa de importação. Tenha em atenção que o nome introduzido pode conter apenas letras minúsculas, números, hífenes e carateres de sublinhado, tem de começar com uma letra e não pode conter espaços. Irá utilizar o nome que escolher para controlar as tarefas enquanto estiverem em curso e depois de serem concluídas. Forneça as informações de contacto para a pessoa responsável por esta tarefa de exportação. 

3. Nos detalhes da tarefa do passo 2, selecione a conta de armazenamento que os dados serão exportados na secção de conta de armazenamento. A localização de Drop-Off será automaticamente ser preenchida com base na região da conta de armazenamento selecionada. Especifique que pretende exportar da sua conta de armazenamento para a unidade em branco ou unidades de dados de Blobs. Pode optar por exportar todos os dados de BLOBs na conta de armazenamento ou pode especificar que blobs ou define de blobs para exportar.
   
   Para especificar um blob para exportar, utilize o **igual a** Seletor e especifique o caminho relativo para o blob, começando com o nome do contentor. Utilize *$root* para especificar o contentor de raiz.
   
   Para especificar todos os blobs começando com um prefixo, utilize o **começa por** Seletor e especifique o prefixo, começando com uma barra '/'. O prefixo pode ser o prefixo do nome do contentor, o nome do contentor concluída ou o nome do contentor concluída seguido o prefixo do nome do blob.
   
   A tabela seguinte mostra exemplos de caminhos de blob válido:
   
   | Seletor | Caminho do blob | Descrição |
   | --- | --- | --- |
   | Começa por |/ |Exporta todos os blobs na conta de armazenamento |
   | Começa por |/$root / |Exporta todos os blobs no contentor de raiz |
   | Começa por |/Book |Exporta todos os blobs em qualquer contentor que começa com o prefixo **livro** |
   | Começa por |/Music/ |Exporta todos os blobs no contentor **música** |
   | Começa por |música/love |Exporta todos os blobs no contentor **música** que começam com prefixo **adoram** |
   | Igual a |$root/logo.bmp |Blob de exportações **logo.bmp** no contentor de raiz |
   | Igual a |videos/Story.mp4 |Blob de exportações **story.mp4** no contentor **vídeos** |
   
   Tem de fornecer os caminhos de blob nos formatos válidos para evitar erros durante o processamento, conforme mostrado nesta captura de ecrã.
   
   ![Criar tarefa de exportação - passo 3](./media/storage-import-export-service/export-job-03.png)

4. No passo 3 devolver informações de envio, selecione a operadora na lista pendente e introduza um número de conta operadora válida que tenha criado com esse operadora. Microsoft irá utilizar esta conta para enviar as unidades para, assim que a tarefa de importação estiver concluída. Forneça um nome do contacto completo e válido, telefone, e-mail, morada, cidade, zip, proviince/estado e país/região...
   
 5. Na página Resumo, endereço de envio do Centro de dados do Azure é fornecido para ser utilizado para envio discos do Azure DC. Certifique-se de que o nome da tarefa e o endereço completo são mencionadas na etiqueta do envio. 

6. Clique em OK na página de resumo para concluir a criação da tarefa de importação

7. Depois dos discos de envio, volte para o **importar/exportar** página no portal do Azure, a) navegue e clique na tarefa de importação b) clique em **Atualizar estado da tarefa e informações de controlo, depois de unidades são fornecidas**. 
     c) selecione a caixa de verificação "Marcar como enviado" d) forneça o número de Carrier e controlo.
    
   Se o número de controlo não forem atualizado dentro de duas semanas a tarefa de criação, a tarefa irá expirar.
   
8. Pode controlar o progresso da tarefa no dashboard do portal. Consulte o que significa cada Estado de tarefa na secção anterior por [visualizar o estado da tarefa](#viewing-your-job-status).

   > [!NOTE]
   > Se o blob seja exportado está a ser utilizada no momento da cópia de disco rígido, o serviço importar/exportar do Azure irá tirar um instantâneo do blob e copie o instantâneo.
   > 
   > 
 
9. Depois de receber as unidades com os seus dados exportados, pode ver e copiar as chaves do BitLocker geradas pelo serviço para a unidade. Navegue para exportar a tarefa no portal do Azure e clique no separador de importação/exportação. Selecione a tarefa de exportação da lista e clique na opção de chaves do BitLocker. As chaves do BitLocker aparecem da seguinte forma:
   
   ![Ver as chaves de BitLocker para a tarefa de exportação](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Veja a secção das FAQ abaixo como abrange as perguntas mais comuns que os clientes encontrarem ao utilizar este serviço.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pode copiar File storage do Azure utilizando o serviço importar/exportar do Azure?**

Sim, o serviço importar/exportar do Azure suporta a importação para Storge de ficheiro do Azure. Não suporta a exportação de ficheiros do Azure neste momento.

**O serviço importar/exportar do Azure está disponível para subscrições de CSP?**

Serviço de importação/exportação do Azure suporta subscrições de CSP.

**Posso ignorar o passo de preparação de unidade para uma tarefa de importação ou pode preparar uma unidade sem copiar?**

Qualquer unidade que pretende que são enviados para importar os dados deve estar preparada utilizando a ferramenta de WAImportExport do Azure. Tem de utilizar a ferramenta de WAImportExport para copiar dados para a unidade.

**É necessário efetuar qualquer preparação do disco durante a criação de uma tarefa de exportação?**

Não existem, mas algumas verificações prévias são recomendadas. Verifique o número de discos necessários utilizando o comando de PreviewExport a ferramenta WAImportExport. Para obter mais informações, consulte [pré-visualização utilização da unidade para uma tarefa de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). Ajuda a utilização da unidade de pré-visualização para os blobs que selecionou, com base no tamanho das unidades que pretende utilizar. Verifique também que pode ser lida e escrita no disco rígido que será enviada para a tarefa de exportação.

**O que acontece se enviar acidentalmente um HDD que não está em conformidade com os requisitos suportados?**

O Centro de dados do Azure irá devolver a unidade que não está em conformidade com os requisitos suportados para si. Se apenas algumas das unidades no pacote de satisfazem os requisitos de suporte, esses unidades serão processadas e as unidades que não cumprem os requisitos serão devolvidas para si.

**Pode cancelar a minha tarefa?**

Pode cancelar uma tarefa quando o estado está a criar ou do envio.

**Quanto pode ver o estado de tarefas foi concluídas no portal do Azure?**

Pode ver o estado para as tarefas concluídas até 90 dias. As tarefas concluídas serão eliminadas após 90 dias.

**Se pretender importar ou exportar mais de 10 unidades, o que devo fazer?**

Uma importação ou exportação tarefa pode referenciar apenas 10 unidades numa tarefa única para o serviço de importação/exportação. Se pretender enviar mais de 10 unidades, pode criar várias tarefas. Unidades que estejam associadas a mesma tarefa tem de ser incluídas em conjunto no mesmo pacote.
A Microsoft oferece orientação e assistência quando a capacidade de dados abrange várias disco importar tarefas. Contacte bulkimport@microsoft.com para obter mais informações

**O serviço formatar as unidades antes de a devolvê-las?**

Não. Todas as unidades estão encriptadas com o BitLocker.

**Pode comprar unidades de tarefas de importação/exportação do Microsoft?**

Não. Terá de enviar as seus próprios unidades para ambos os importar e exportar tarefas.

* * Como aceder a dados que são importados por este serviço * *

Os dados na sua conta de armazenamento do Azure podem ser acedidos através do Portal do Azure ou utilizar uma ferramenta autónoma chamado Explorador de armazenamento. https://Docs.microsoft.com/en-us/Azure/vs-Azure-Tools-Storage-Manage-with-Storage-Explorer 

**Depois de concluída a tarefa de importação, o que irá meu aspeto de dados, como na conta de armazenamento? A minha hierarquia directory preservada?**

Quando preparar um disco rígido para uma tarefa de importação, o destino é especificado pelo campo de DstBlobPathOrPrefix no conjunto de dados CSV. Este é o contentor de destino na conta de armazenamento ao qual os dados da unidade de disco rígido são copiados. Dentro deste contentor de destino, diretórios virtuais são criados para as pastas no disco rígido e blobs são criados para ficheiros. 

**Se a unidade tem ficheiros que já existem na minha conta de armazenamento, o serviço substituirá blobs existentes ou ficheiros na minha conta de armazenamento?**

Quando preparar a unidade, pode especificar se os ficheiros de destino devem ser substituídos ou ignoradas utilizar o campo no ficheiro CSV de conjunto de dados chamado disposição: < mudar o nome | substituir o não | substituir >. Por predefinição, o serviço irá mudar o nome de novos ficheiros em vez de substituir blobs existentes ou ficheiros.

**A ferramenta de WAImportExport é compatível com os sistemas operativos de 32 bits?**
Não. A ferramenta de WAImportExport só é compatível com sistemas de operativos do Windows de 64 bits. Consulte a secção de sistemas operativos no [pré-requisitos](#pre-requisites) para uma lista completa das versões de SO suportadas.

**Deve posso incluir qualquer coisa que não a unidade de disco rígido no meu pacote?**

. São enviados apenas as unidades de disco rígido. Não inclua itens, como os cabos de alimentação de energia ou cabos USB.

**É necessário que enviar as minhas unidades utilizando FedEx ou DHL?**

Pode enviar unidades para o Centro de dados utilizando qualquer operadora conhecida como FedEx, DHL, UPS ou nos serviço Postal. No entanto, para as unidades de volta para a partir do Centro de dados de envio, tem de fornecer um número de conta FedEx nos E.U.A. e EU ou um número de conta DHL nas regiões Ásia e da Austrália.

**Existem restrições com envio internationally minha unidade?**

Tenha em atenção que o suporte de dados físicos que são envio poderá ter de cruzada internacionais limites. É responsável por assegurar que os dados e suportes de dados físicos são importados e/ou exportados de acordo com as leis aplicáveis. Antes do envio do suporte de dados físico, consulte o seu consultores, para verificar que os dados e suportes de dados podem legalmente enviados para o Centro de dados identificados. Isto irá ajudar a garantir que atinge Microsoft atempadamente.

**Ao criar uma tarefa, o endereço de envio é uma localização diferente do meu localização da conta de armazenamento. O que devo fazer?**

Algumas localizações de conta de armazenamento estão mapeadas para as localizações de envio alternativo. Anteriormente as localizações de envio disponíveis podem também ser temporariamente mapeadas para localizações alternativas. Verifique sempre o endereço de envio fornecido durante a criação da tarefa antes de envio suas unidades.

**Quando a minha unidade do envio, operadora pede-lhe o data center endereço e o telefone número de contacto. O que posso fornece?**

O número de telefone e o DC endereço é fornecido como parte da criação da tarefa.

**Pode utilizar o serviço de importação/exportação do Azure para copiar as caixas de correio PST e dados do SharePoint para o Office 365?**

Consulte [ficheiros de importação PST ou dados do SharePoint para o Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Pode utilizar o serviço de importação/exportação do Azure para copiar os meus as cópias de segurança offline para o serviço de cópia de segurança do Azure?**

Consulte [fluxo de trabalho de cópia de segurança Offline no Backup do Azure](../../backup/backup-azure-backup-import-export.md).

**O que é o número máximo de HDD no um shipment?**

Qualquer número de HDDs pode estar num shipment e se os discos pertencem a várias tarefas se recomenda uma) tem os discos etiquetados com os nomes de tarefa correspondente. b) atualizar as tarefas com um número de controlo de com -1, o sufixo-2 etc.
  
**O que é o máximo de BLOBs de blocos e o tamanho do Blob de página suportados por disco importação/exportação?**

Tamanho do Blob de bloco máximo é aproximadamente 4.768TB ou 5,000,000 MB.
Tamanho do Blob de página máximo é 1TB.

**Disco para importar/exportar suporta a encriptação AES 256?**

Serviço de importação/exportação do Azure por predefinição encripta com a encriptação bitlocker AES 128, mas isto pode ser aumentado para AES 256 ao encriptar manualmente com o bitlocker antes de dados são copiados. 

Se utilizar [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), segue-se um comando de exemplo
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
Se utilizar [WAImportExport ferramenta](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) especificar "AlreadyEncrypted" e fornecer a chave no driveset CSV.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>Passos seguintes

* [Configurar a ferramenta de WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com o utilitário de linha de comandos AzCopy](storage-use-azcopy.md)
* [Exemplo de API de REST de exportação de importação do Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

