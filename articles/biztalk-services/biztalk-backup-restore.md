---
title: "Criar e restaurar uma cópia de segurança nos BizTalk Services | Microsoft Docs"
description: "BizTalk Services inclui a cópia de segurança e restauro. Saiba como criar e restaurar uma cópia de segurança e determine o que obtém uma cópia de segurança. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: c55d1ab124441c42101b4ad60924a9ea28231408
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk Services: Cópia de segurança e Restauro

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

BizTalk Services do Azure inclui capacidades de cópia de segurança e restauro. Este tópico descreve como criar cópias de segurança e restaurar os BizTalk Services utilizando o portal clássico do Azure.

Também pode criar uma cópia dos BizTalk Services utilizando o [API de REST do BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=325584). 

> [!NOTE]
> As ligações híbridas não são guardados em cópia de segurança, independentemente da edição. Tem de recriar as ligações híbridas.


## <a name="before-you-begin"></a>Antes de começar
* Cópia de segurança e restauro poderão não estar disponíveis em todas as edições. Consulte [dos BizTalk Services: gráfico de edições](biztalk-editions-feature-chart.md).
* Utilizar o portal clássico do Azure, pode criar uma cópia de segurança a pedido ou criar uma cópia de segurança agendada. 
* É possível restaurar a cópia de segurança conteúdo para o mesmo serviço BizTalk ou para um novo serviço BizTalk. Para restaurar o BizTalk Service com o mesmo nome, o serviço BizTalk existente tem de ser eliminado e o nome tem de estar disponível. Depois de eliminar um BizTalk Service, pode demorar mais tempo do que pretendia para o mesmo nome para estar disponível. Se não puder aguardar para o mesmo nome para estar disponível, em seguida, restaure para um novo serviço BizTalk.
* BizTalk Services podem ser restaurados para a mesma edição ou uma edição superior. Restaurar os BizTalk Services para uma edição inferior, de quando foi feita a cópia de segurança, não é suportada.
  
    Por exemplo, uma cópia de segurança com a edição básica pode ser restaurada para a edição Premium. Não é possível restaurar uma cópia de segurança com a edição Premium Standard Edition.
* Os números de controlo de EDI são cópias de segurança para manter a continuidade dos números de controlo. Se as mensagens são processadas após a última cópia de segurança, restaurar este conteúdo de cópia de segurança pode fazer com que os números de controlo duplicado.
* Se um lote tem mensagens Active Directory, processo de lote **antes** executar uma cópia de segurança. Ao criar uma cópia de segurança (como necessários ou agendada), as mensagens em lotes nunca são armazenadas. 
  
    **Se uma cópia de segurança foi efetuada com o Active Directory mensagens num batch, estas mensagens não são cópia de segurança e, por conseguinte, foram perdidas.**
* Opcional: No Portal de serviços do BizTalk, pare a quaisquer operações de gestão.

## <a name="create-a-backup"></a>Criar uma cópia de segurança
Uma cópia de segurança pode ser executada em qualquer altura e é totalmente controlada por si. Esta secção lista os passos para criar cópias de segurança utilizando o portal clássico do Azure, incluindo:

[Numa cópia de segurança a pedido](#backupnow)

[Agendar uma cópia de segurança](#backupschedule)

#### <a name="backupnow"></a>Numa cópia de segurança a pedido
1. No portal clássico do Azure, selecione **BizTalk Services**e, em seguida, selecione o BizTalk Service que pretende efetuar cópia de segurança.
2. No **Dashboard** separador, selecione **cópia de segurança** na parte inferior da página.
3. Introduza um nome de cópia de segurança. Por exemplo, introduza *myBizTalkService*BU*data*.
4. Escolha uma conta do blob storage e selecione a marca de verificação para iniciar a cópia de segurança.

Uma vez concluída a cópia de segurança, é criado um contentor com o nome de cópia de segurança que introduzir na conta de armazenamento. Este contentor contém a configuração de cópia de segurança do BizTalk Service.

#### <a name="backupschedule"></a>Agendar uma cópia de segurança
1. No portal clássico do Azure, selecione **BizTalk Services**, selecione o nome do BizTalk Service para agendar a cópia de segurança e, em seguida, selecione o **configurar** separador.
2. Definir o **estado de cópia de segurança** para **automática**. 
3. Selecione o **conta de armazenamento** para armazenar a cópia de segurança, introduza o **frequência** para criar cópias de segurança e quanto tempo para manter as cópias de segurança (**retenção dias**):
   
    ![][AutomaticBU]
   
    **Notas**     
   
   * No **retenção dias**, o período de retenção tem de ser superior à frequência da cópia de segurança.
   * Selecione **mantenha sempre, pelo menos, uma cópia de segurança**, mesmo se for passado o período de retenção.
4. Selecione **Guardar**.

Quando uma tarefa de cópia de segurança agendada é executada, cria um contentor (para armazenar dados de cópia de segurança) na conta de armazenamento que introduziu. O nome do contentor é denominado *nome-data / hora de BizTalk Service*. 

Se o dashboard do BizTalk Service mostra um **falha** Estado:

![Último Estado de cópia de segurança agendado][BackupStatus] 

A hiperligação abre os registos de operação de serviços de gestão para ajudar a resolver. Consulte [BizTalk Services: resolver problemas com os registos de operações](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## <a name="restore"></a>Restauro
Pode restaurar cópias de segurança do portal clássico do Azure ou a partir de [API REST do serviço BizTalk restaurar](http://go.microsoft.com/fwlink/p/?LinkID=325582). Esta secção lista os passos para restaurar a utilizar o portal clássico.

#### <a name="before-restoring-a-backup"></a>Antes de restaurar uma cópia de segurança
* Novo controlo, arquivar e arquivos de monitorização, podem ser introduzidos ao restaurar um BizTalk Service.
* Os mesmos dados EDI Runtime são restaurados. A cópia de segurança do tempo de execução EDI armazena os números de controlo. Os números de controlo restaurada são na sequência do momento da cópia de segurança. Se as mensagens são processadas após a última cópia de segurança, restaurar este conteúdo de cópia de segurança pode fazer com que os números de controlo duplicado.

#### <a name="restore-a-backup"></a>Restaurar uma cópia de segurança
1. No portal clássico do Azure, selecione **novo** > **serviços aplicacionais** > **BizTalk Service** > **restaurar**:
   
    ![Restaurar uma cópia de segurança][Restore]
2. No **URL de cópia de segurança**, selecione o ícone de pasta e expanda a conta de armazenamento do Azure que armazena a cópia de segurança de configuração do BizTalk Service. Expanda o contentor e no painel direito, selecione o correspondente de cópia de segurança de ficheiro. txt. 
   <br/><br/>
   Selecione **abra**.
3. No **restaurar o BizTalk Service** página, introduza um **nome do BizTalk Service** e certifique-se a **URL do domínio**, **edição**, e **região** para o BizTalk Service restaurada. **Criar uma nova instância de base de dados do SQL Server** para a base de dados de controlo:
   
    ![][RestoreBizTalkService]
   
    Selecione a seta seguinte.
4. Verifique o nome da base de dados SQL, introduza o servidor físico onde será criada a base de dados do SQL Server e uma nome de utilizador/palavra-passe para esse servidor.

    Selecione se pretende configurar a edição de base de dados do SQL Server, tamanho e outras propriedades, **configurar definições avançadas da base de dados**. 

    Selecione a seta seguinte.

1. Criar uma nova conta de armazenamento ou introduza uma conta de armazenamento existente para o BizTalk Service.
2. Selecione a marca de verificação para iniciar o restauro.

Depois do restauro concluir com êxito, um novo serviço BizTalk é listado num estado suspenso na página de BizTalk Services no portal clássico do Azure.

### <a name="postrestore"></a>Depois de restaurar uma cópia de segurança
O BizTalk Service está sempre restaurado num **suspenso** estado. Neste estado, pode efetuar alterações de configuração antes do novo ambiente está funcional, incluindo:

* Se tiver criado utilizando o SDK dos BizTalk Services do Azure de aplicações do BizTalk Service, se pretender atualizar as credenciais de controlo de acesso (ACS) nessas aplicações para trabalhar com o ambiente restaurado.
* Restaurar um BizTalk Service para replicar num ambiente existente do BizTalk Service. Nesta situação, se existirem contratos configurados no portal do BizTalk Services original que utilizam uma pasta de origem do FTP, se pretender atualizar os contratos no ambiente recentemente restaurado para utilizar uma pasta FTP de origem diferente. Caso contrário, poderão existir dois contratos diferentes ao tentar efetuar obter a mesma mensagem.
* Se os tiver restaurado ter vários ambientes do BizTalk Service, certifique-se de que o ambiente correto no aplicações do Visual Studio, os cmdlets do PowerShell, REST APIs ou APIs de OM de gestão de parceiros comerciais de destino.
* É uma boa prática configurar cópias de segurança automatizadas no ambiente de serviço BizTalk recentemente restaurado.

Para iniciar o BizTalk Service no portal clássico do Azure, selecione o BizTalk Service restaurada e selecione **retomar** na barra de tarefas. 

## <a name="what-gets-backed-up"></a>O que obtém uma cópia de segurança
Quando é criada uma cópia de segurança, os itens seguintes são uma cópia de segurança:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Itens de cópia de segurança</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal de serviços do BizTalk do Azure</strong></td>
</tr> 
<tr>
<td>Configuração e o tempo de execução</td> 
<td>
<ul>
<li>Detalhes de parceiros e perfil</li>
<li>Contratos de parceiros</li>
<li>Assemblagens personalizadas implementadas</li>
<li>Pontes implementado</li>
<li>Certificados</li>
<li>Transformações implementadas</li>
<li>Pipelines</li>
<li>Modelos criados e guardados no Portal de serviços BizTalk</li>
<li>X12 mapeamentos ST01 e GS01</li>
<li>Números de controlo (EDI)</li>
<li>Valores de AS2 mensagem Dinâmicas</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Serviço BizTalk do Azure</strong></td>
</tr> 
<tr>
<td>Certificado SSL</td> 
<td>
<ul>
<li>Dados de certificado SSL</li>
<li>Palavra-passe de certificado SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Definições do BizTalk Service</td> 
<td>
<ul>
<li>Contagem de unidades de escala</li>
<li>Edição</li>
<li>Versão do produto</li>
<li>Região/Datacenter</li>
<li>Espaço de nomes de serviço de controlo (ACS) de acesso e a chave</li>
<li>Cadeia de ligação de base de dados de controlo</li>
<li>Cadeia de ligação de conta de armazenamento de arquivo</li>
<li>Cadeia de ligação de conta de armazenamento de monitorização</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Itens adicionais</strong></td>
</tr> 
<tr>
<td>Base de dados de controlo</td> 
<td>Quando é criado o BizTalk Service, os detalhes da base de dados de registo são introduzidos, incluindo o servidor de base de dados SQL do Azure e o nome de base de dados de controlo. A base de dados de controlo não é automaticamente uma cópia de segurança.
<br/><br/>
<strong>Importante</strong><br/>
Se a base de dados de controlo é eliminado e as necessidades de base de dados recuperada, tem de existir uma cópia de segurança anterior. Se não existir uma cópia de segurança, a base de dados de controlo e os respetivos dados não são recuperáveis. Nesta situação, crie uma nova base de dados de controlo com o mesmo nome de base de dados. Recomenda-se a Georreplicação.</td>
</tr> 
</table>

## <a name="next"></a>Seguinte
Para criar os BizTalk Services do Azure no portal clássico do Azure, aceda a [BizTalk Services: portal clássico do Azure de utilizar o aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para começar a criar aplicações, veja [BizTalk Services do Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Veja Também
* [Serviço de cópia de segurança de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Restaurar o BizTalk Service a partir de cópia de segurança](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Os BizTalk Services: Programador, básicas, Standard e Premium gráfico de edições](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: Portal clássico do Azure através de aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [Serviços BizTalk: Gráfico de Estado de Aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Serviços BizTalk: Separadores Dashboard, Monitorizar e Dimensionar](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [Serviços BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [Serviços BizTalk: Nome e Chave do Emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

