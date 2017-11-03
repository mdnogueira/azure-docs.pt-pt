---
title: "Encriptação do serviço de armazenamento do Azure para dados Inativos | Microsoft Docs"
description: "Utilizar a funcionalidade de encriptação do serviço do Storage do Azure para encriptar o armazenamento de Blobs do Azure no lado do serviço ao armazenar os dados e desencriptá-lo ao obter os dados."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: tamram
ms.openlocfilehash: 772c36c8310a4bf30c62def507382fe74427e0d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Encriptação do Serviço de Armazenamento do Azure para Dados Inativos
Azure armazenamento serviço encriptação (SSE) para dados Inativos ajuda a proteger e a salvaguardar os seus dados para satisfazer os seus compromissos de conformidade e segurança organizacional. Com esta funcionalidade, o Storage do Azure automaticamente encripta os dados antes de a persistência para armazenamento e desencripta antes da obtenção. A encriptação, desencriptação e a gestão de chaves são completamente transparentes para os utilizadores.

As secções seguintes fornecem orientações detalhadas sobre como utilizar as funcionalidades de encriptação do serviço de armazenamento, bem como os cenários suportados e o utilizador experiências.

## <a name="overview"></a>Descrição geral
Storage do Azure fornece um conjunto completo de capacidades de segurança que em conjunto permitem aos programadores criarem aplicações seguras. Dados podem ser protegidos em trânsito entre uma aplicação e o Azure utilizando [encriptação do lado do cliente](../storage-client-side-encryption.md), HTTPs ou SMB 3.0. Encriptação do serviço de armazenamento fornece encriptação de inativos, processamento de encriptação, desencriptação e gestão de chaves de forma completamente transparente. Todos os dados são encriptados utilizando 256 bits [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), um bloco de maior cifras disponíveis.

SSE funciona ao encriptar os dados quando são escritos no armazenamento do Azure e pode ser utilizado para armazenamento de ficheiros e armazenamento de Blobs do Azure. Funciona para o seguinte:

* Standard Storage: Contas armazenamento de objetivo geral para os Blobs e ficheiros de armazenamento e de contas do Blob storage
* Armazenamento Premium 
* Redundância todos os níveis (LRS, ZRS, GRS, RA-GRS)
* Contas de armazenamento do Azure Resource Manager (mas não clássico) 
* Todas as regiões.

Para obter mais informações, consulte as FAQ.

Para ativar ou desativar a encriptação do serviço de armazenamento para uma conta de armazenamento, inicie sessão no [portal do Azure](https://portal.azure.com) e selecione uma conta de armazenamento. No painel de definições, procure a secção de serviço Blob, conforme mostrado nesta captura de ecrã e clique em encriptação.

![Portal captura de ecrã com a opção de encriptação](./media/storage-service-encryption/image1.png)
<br/>*Figura 1: Ativar SSE para o serviço Blob (passo 1)*

![Portal captura de ecrã com a opção de encriptação](./media/storage-service-encryption/image3.png)
<br/>*Figura 2: Ativar SSE para o serviço de ficheiros (passo 1)*

Depois de clicar na definição de encriptação, pode ativar ou desativar a encriptação do serviço de armazenamento.

![Propriedades de encriptação do portal de captura](./media/storage-service-encryption/image2.png)
<br/>*Figura 3: Ativar SSE para BLOBs e ficheiro de serviço (Step2)*

## <a name="encryption-scenarios"></a>Cenários de encriptação
Encriptação do serviço de armazenamento pode ser ativada um nível de conta de armazenamento. Uma vez ativada, os clientes irão escolher quais os serviços para encriptar. Suporta os seguintes cenários de cliente:

* Encriptação de armazenamento de ficheiros e armazenamento de BLOBs em contas do Resource Manager.
* Encriptação de Blob e o serviço de ficheiros em contas de armazenamento clássicas uma vez migrados para contas de armazenamento do Resource Manager.

SSE tem as seguintes limitações:

* Encriptação de contas de armazenamento clássicas não é suportada.
* Dados existentes - SSE encripta apenas dados recentemente criados após a encriptação está ativada. Se, por exemplo, crie uma nova conta de armazenamento do Resource Manager, mas não ativar encriptação e, em seguida, carregue blobs ou VHDs arquivados a essa conta de armazenamento e, em seguida, ative SSE, os blobs não serão encriptados, a menos que estão foi reescritas ou copiados.
* Suporte de Marketplace - ativar a encriptação de VMs criados do Marketplace com a [portal do Azure](https://portal.azure.com), PowerShell e a CLI do Azure. A imagem de base de VHD irá permanecer desencriptada; No entanto, quaisquer escritas efetuadas após a VM tem aceleradas serão encriptadas.
* Dados de filas e tabela não serão encriptados.

## <a name="getting-started"></a>Introdução
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Passo 1: [criar uma nova conta de armazenamento](../storage-create-storage-account.md).
### <a name="step-2-enable-encryption"></a>Passo 2: Ativar a encriptação.
Pode ativar a encriptação a utilizar o [portal do Azure](https://portal.azure.com).

> [!NOTE]
> Se pretender programaticamente ativar ou desativar a encriptação do serviço de armazenamento numa conta de armazenamento, pode utilizar o [API de REST do fornecedor de recursos do Azure Storage](https://msdn.microsoft.com/library/azure/mt163683.aspx), a [biblioteca cliente de fornecedor de recursos do armazenamento para .NET ](https://msdn.microsoft.com/library/azure/mt131037.aspx), [o azure PowerShell](/powershell/azureps-cmdlets-docs), ou o [CLI do Azure](../storage-azure-cli.md).
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>Passo 3: Copiar dados para a conta de armazenamento
Se ativar SSE para o serviço Blob, blobs escritos para essa conta do storage serão encriptados. Qualquer já localizados nessa conta de armazenamento de blobs não serão encriptados até que estes são foi reescritas. Pode copiar os dados a partir de uma conta de armazenamento para um com SSE encriptado, ou mesmo ativar SSE e copiar os blobs de um contentor para outro a certeza de que anterior dados são encriptados. Pode utilizar qualquer uma das seguintes ferramentas para realizar esta tarefa. Este é o mesmo comportamento para o File Storage bem.

#### <a name="using-azcopy"></a>Utilizar o AzCopy
O AzCopy é um utilitário da linha de comandos do Windows foi concebido para copiar dados para e do armazenamento de Blobs do Microsoft Azure, ficheiros e tabela utilizando comandos simples com um desempenho ideal. Pode utilizar este para copiar os blobs ou ficheiros de uma conta de armazenamento para outra que tenha SSE ativada. 

Para obter mais informações, visite [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md).

#### <a name="using-smb"></a>Utilizando o SMB
Ficheiros do Azure oferece partilhas de ficheiros na nuvem utilizando o protocolo SMB padrão. Podem montar uma partilha de ficheiros a partir de um cliente no local ou no Azure. Depois de montar, ferramentas, tal como Robocopy podem ser utilizadas para copiar ficheiros através de partilhas de ficheiros do Azure. Para obter mais informações, consulte [como montar a partilha de ficheiros do Azure no Windows](../files/storage-how-to-use-files-windows.md) e [como montar a partilha de ficheiros do Azure no Linux](../files/storage-how-to-use-files-linux.md).


#### <a name="using-the-storage-client-libraries"></a>Utilizar as bibliotecas de cliente do armazenamento
Pode copiar o blob ou um ficheiro de dados para e do armazenamento de BLOBs ou entre contas de armazenamento com o nosso conjunto avançado de bibliotecas de cliente de armazenamento, incluindo .NET, C++, Java, Android, Node.js, PHP, Python e Ruby.

Para obter mais informações, visite o nosso [introdução ao Blob storage do Azure através do .NET](../blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Utilizar um Explorador de armazenamento
Pode utilizar um Explorador de armazenamento para criar contas de armazenamento, carregar e transferir dados, ver o conteúdo de blobs e navegar através de diretórios. Pode utilizar um para carregar os blobs à sua conta de armazenamento com a encriptação ativada. Com alguns exploradores de armazenamento, também pode copiar dados existente do armazenamento de BLOBs para um contentor diferente na conta de armazenamento ou uma nova conta de armazenamento que tenha SSE ativada.

Para obter mais informações, visite [exploradores de armazenamento do Azure](../storage-explorers.md).

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>Passo 4: Consultar o estado dos dados encriptados
Foi implementada uma versão atualizada das bibliotecas de cliente de armazenamento que lhe permite consultar o estado de um objecto para determinar se são encriptado ou não. Isto está atualmente disponível apenas para o Blob storage. Suporte para o File storage é no plano. 

Entretanto, pode chamar [obter propriedades de conta](https://msdn.microsoft.com/library/azure/mt163553.aspx) Certifique-se de que a conta de armazenamento tem encriptação ativada ou visualizar as propriedades da conta de armazenamento no portal do Azure.

## <a name="encryption-and-decryption-workflow"></a>Encriptação e desencriptação fluxo de trabalho
Eis uma breve descrição do fluxo de trabalho de encriptação/desencriptação:

* O cliente ativa a encriptação na conta de armazenamento.
* Quando o cliente escreve novos dados (colocar Blob, colocar bloco, colocar página, COLOQUE a ficheiro etc.) para o armazenamento de BLOBs ou ficheiro; cada escrita é encriptada utilizando 256 bits [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), um bloco de maior cifras disponíveis.
* Quando o cliente tem de aceder a dados (obter BLOBs, etc.), os dados são desencriptados automaticamente antes de o devolver ao utilizador.
* Se a encriptação estiver desativada, escritas novas já não são encriptadas e os dados encriptados existentes permanecem encriptados até foi reescrita pelo utilizador. Enquanto estiver ativada a encriptação, escritas para o armazenamento de BLOBs ou o ficheiro serão encriptadas. Esta não altera o estado de dados com o utilizador ativando ou desativando entre ativação/desativação da encriptação da conta do storage.
* Todas as chaves de encriptação são armazenadas, encriptadas e geridas pela Microsoft.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Perguntas mais frequentes sobre a encriptação do serviço de armazenamento para dados Inativos
**P: Posso ter uma conta de armazenamento clássicas existente. Pode ativar SSE no mesmo?**

R: não, SSE só é suportado em contas de armazenamento do Resource Manager.

**P: como pode encriptar dados na minha conta de armazenamento clássico?**

R: pode criar uma nova conta de armazenamento do Resource Manager e copie os dados utilizando [AzCopy](storage-use-azcopy.md) da sua conta de armazenamento clássicas existente à sua conta de armazenamento recentemente criada do Gestor de recursos. 

Se migrar a sua conta de armazenamento clássico para uma conta de armazenamento do Resource Manager, esta operação é instantânea, que altera o tipo da sua conta, mas que não tenha efeito os dados existentes. Quaisquer novos dados escritos serão encriptados apenas depois de ativar a encriptação. Para obter mais informações, consulte [plataforma suportada migração de recursos IaaS do clássico para o Resource Manager](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/). Tenha em atenção que isto só é suportado para os serviços de Blob e o ficheiro.

**P: Posso ter uma conta de armazenamento do Resource Manager existente. Pode ativar SSE no mesmo?**

R: Sim, mas apenas recentemente escritos dados serão encriptados. Não voltar atrás e encriptar os dados que já estavam presentes. Isto ainda não é suportado para a pré-visualização do ficheiro de armazenamento.

**P: gostaria encriptar os dados atuais numa conta de armazenamento do Resource Manager existente?**

R: pode ativar SSE em qualquer altura numa conta de armazenamento do Resource Manager. No entanto, os dados que já foram presentes não serão encriptados. Para encriptar os dados existentes, pode copiá-los para outro nome ou outro contentor e, em seguida, remover as versões não encriptadas.

**P: Posso estou a utilizar o Premium storage; Pode utilizar SSE?**

R: Sim, SSE é suportada no armazenamento Standard e Premium Storage.  Armazenamento Premium não é suportado para o serviço de ficheiros.

**P: se posso criar uma nova conta de armazenamento e ativar SSE, em seguida, criar uma nova VM através dessa conta de armazenamento, significa que a minha VM é encriptado?**

R: Sim. Se eventuais discos criados utilizam a nova conta de armazenamento serão encriptados, desde que são criados após SSE está ativada. Se a VM foi criada utilizando o Azure Marketplace, a imagem de base de VHD permanecerá não encriptada; No entanto, quaisquer escritas efetuadas após a VM tem aceleradas serão encriptadas.

**P: pode criar novas contas de armazenamento com SSE ativada com o Azure PowerShell e da CLI do Azure?**

R: Sim.

**P: quanto mais armazenamento do Azure custo se SSE está ativado?**

R: não há sem custos adicionais.

**P: quem gere as chaves de encriptação?**

R: as chaves são geridas pela Microsoft.

**P: Posso utilizar o meus próprio chaves de encriptação?**

R: estamos a trabalhar para fornecer capacidades para os clientes movam as suas próprias chaves de encriptação.

**P: posso revogar o acesso às chaves de encriptação?**

R: não neste momento; as chaves são completamente geridas pela Microsoft.

**P: é SSE ativada por predefinição, quando criar uma nova conta de armazenamento?**

R: SSE não está ativada por predefinição; Pode utilizar o portal do Azure para ativá-la. Através de programação também pode ativar esta funcionalidade utilizando a API de REST de fornecedor de recursos de armazenamento.

**P: como é diferente do Azure Disk Encryption?**

R: esta funcionalidade é utilizada para encriptar os dados no Blob storage do Azure. O Azure Disk Encryption é utilizado para encriptar o SO e discos de dados em VMs do IaaS. Para obter mais detalhes, visite o nosso [manual de segurança de armazenamento](../storage-security-guide.md).

**P: E se posso ativar SSE e, em seguida, aceda e ativar a Azure Disk Encryption nos discos?**

R: Isto irá funcionar de forma totalmente integrada. Os dados serão encriptados por ambos os métodos.

**P: minha conta de armazenamento é configurada para ser replicado georreplicação redundantly. Se ativar a SSE, será minha cópia redundante também será encriptada?**

R: Sim, todas as cópias da conta do storage são encriptadas e todas as opções de redundância – localmente redundante armazenamento (LRS), armazenamento Redundant de zona (ZRS), armazenamento Georredundante (GRS) e o armazenamento Georredundante com acesso de leitura (RA-GRS) – são suportadas.

**P: Posso não é possível ativar a encriptação na minha conta de armazenamento.**

R: é uma conta de armazenamento do Resource Manager? Contas de armazenamento clássicas não são suportadas. 

**P: SSE só é permitida em regiões específicas?**

R: o SSE está disponível em todas as regiões para o Blob storage. Consulte a secção de disponibilidade para o File storage. 

**P: como posso o contacto certo se posso ter algum problema ou quiser fornecer comentários?**

R: contacte [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) para quaisquer problemas relacionados com a encriptação do serviço de armazenamento.

## <a name="next-steps"></a>Passos Seguintes
Storage do Azure fornece um conjunto completo de capacidades de segurança que em conjunto permitem aos programadores criarem aplicações seguras. Para obter mais detalhes, visite o [manual de segurança de armazenamento](../storage-security-guide.md).

