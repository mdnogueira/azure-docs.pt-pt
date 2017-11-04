---
title: "Resolução de problemas de ficheiros do Azure no Windows | Microsoft Docs"
description: "Resolução de problemas de ficheiros do Azure no Windows"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: genli
ms.openlocfilehash: 5aacc8a920c9343c5efa89128aabb1505fc2d9aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Resolução de problemas de ficheiros do Azure no Windows

Este artigo apresenta uma lista de problemas comuns que estão relacionados com ficheiros do Microsoft Azure quando ligar a partir de clientes do Windows. Também fornece possíveis causas e soluções para esses problemas. Além dos passos de resolução de problemas neste artigo, pode também utilizar [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) para garantir que o ambiente de cliente do Windows tem pré-requisitos corretos. AzFileDiagnostics automatiza a deteção da maioria dos sintomas mencionadas neste artigo e ajuda a configurar o ambiente para obter um desempenho ideal. Também pode encontrar estas informações no [Troubleshooter as partilhas de ficheiros do Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) que fornece os passos para ajudá-lo com problemas de partilhas de ficheiros de ligar/mapeamento/a montagem do Azure.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Erro de 53, erro 67 ou erro 87 quando montar ou desmontar uma partilha de ficheiros do Azure

Quando tentar montar uma partilha de ficheiros no local ou a partir do Centro de dados diferentes, poderá receber os seguintes erros:

- Ocorreu um erro de sistema 53. O caminho de rede não foi encontrado.
- Ocorreu um erro de sistema 67. Não é possível localizar o nome da rede.
- Ocorreu um erro de sistema 87. O parâmetro está incorreto.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação sem encriptação

Por motivos de segurança, as ligações às partilhas de ficheiros do Azure estão bloqueadas se o canal de comunicação não é encriptado e se a tentativa de ligação não é feita a partir do mesmo centro de dados onde residem as partilhas de ficheiros do Azure. Encriptação de canal de comunicação é fornecida apenas se o SO de cliente do utilizador suporta a encriptação SMB.

Windows 8, Windows Server 2012 e versões posteriores do sistema de cada negociar pedidos que incluem o SMB 3.0, que suporta a encriptação.

### <a name="solution-for-cause-1"></a>Solução para causa 1

Ligar a partir de um cliente que suporta um dos seguintes:

- Preenche os requisitos do Windows 8 e Windows Server 2012 ou versões posteriores
- Estabelece ligação a partir de uma máquina virtual no mesmo centro de dados como a conta de armazenamento do Azure que é utilizada para a partilha de ficheiros do Azure

### <a name="cause-2-port-445-is-blocked"></a>Causa 2: A porta 445 está bloqueada

Erro de sistema 53 ou erro de sistema 67 pode ocorrer se a porta 445 comunicação saída para um centro de dados de ficheiros do Azure está bloqueada. Para ver o resumo de ISPs que permitem ou não permitir acesso de porta 445, aceda à [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para saber se é este o motivo por trás de mensagem de "Erro de sistema 53", pode utilizar Portqry para consultar o ponto final TCP:445. Se o ponto final TCP:445 é apresentado como filtrada, é bloqueada a porta TCP. Eis um exemplo de consulta:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Se a porta TCP 445 está bloqueada por uma regra ao longo do caminho de rede, verá a seguinte saída:

  `TCP port 445 (microsoft-ds service): FILTERED`

Para obter mais informações sobre como utilizar Portqry, consulte [descrição do utilitário de linha de comandos a Portqry.exe](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Solução para causa 2

Trabalhar com o seu departamento de TI para abrir a porta 445 saída para [intervalos de IP de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>Causa 3: NTLMv1 está ativado

Erro de sistema 53 ou erro de sistema 87 pode ocorrer se estiver ativada NTLMv1 comunicação no cliente. Ficheiros do Azure suporta apenas a autenticação NTLMv2. Ter NTLMv1 ativado cria um cliente menos seguras. Por conseguinte, a comunicação está bloqueada para ficheiros do Azure. 

Para determinar se esta é a causa do erro, certifique-se de que a seguinte subchave de registo está definida para um valor de 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para obter mais informações, consulte o [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) tópico no TechNet.

### <a name="solution-for-cause-3"></a>Solução para causa 3

Reverter o **LmCompatibilityLevel** valor para o valor predefinido de 3 na seguinte subchave do registo:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Erro 1816 "insuficiente quota está disponível para processar este comando" quando copiar para uma partilha de ficheiros do Azure

### <a name="cause"></a>Causa

Ocorre um erro 1816 quando atingir o limite superior de identificadores abertos simultâneos que são permitidos para um ficheiro no computador onde a partilha de ficheiros está a ser montada.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos em simultâneo fechando alguns identificadores e tente novamente. Para obter mais informações, consulte [lista de verificação de armazenamento do Microsoft Azure, desempenho e escalabilidade](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Lenta a cópia de ficheiros e de ficheiros do Azure no Windows

Poderá ver um desempenho lento ao tentar transferir os ficheiros para o serviço de ficheiros do Azure.

- Se não tiver um requisito de tamanho de e/s mínimo específico, recomendamos que utilize 1 MB como o tamanho de e/s para um desempenho ideal.
-   Se souber o tamanho do final de um ficheiro que estiver a expandir com escritas e o software não tem problemas de compatibilidade quando a cauda unwritten no ficheiro contém zeros, em seguida, defina o tamanho do ficheiro seguinte com antecedência em vez de efetuar cada escrita uma escrita expandir.
-   Utilize o método copy direita:
    -   Utilize [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre duas partilhas de ficheiros.
    -   Utilize [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre partilhas de ficheiros no computador local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerações para Windows 8.1 ou Windows Server 2012 R2

Para clientes que estejam a executar o Windows 8.1 ou Windows Server 2012 R2, certifique-se de que o [KB3114025](https://support.microsoft.com/help/3114025) correção está instalada. Esta correção melhora o desempenho da criação e feche identificadores.

Pode executar o script seguinte para verificar se a correção foi instalada:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se a correção está instalada, é apresentada a seguinte saída:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Imagens do Windows Server 2012 R2 no Azure Marketplace tem correções KB3114025 instalado por predefinição, a partir de Dezembro de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Não existem pasta com uma letra de unidade na **meu computador**

Se mapear uma partilha de ficheiros do Azure como um administrador através de utilização de rede, a partilha parece estar em falta.

### <a name="cause"></a>Causa

Por predefinição, o Explorador de ficheiros do Windows não é executado como administrador. Se executar o net utilização a partir de uma linha de comandos administrativa, mapear a unidade de rede como administrador. Dado que as unidades mapeadas centrada no utilizador, a conta de utilizador que é registada no não apresenta as unidades se estão montados numa conta de utilizador diferente.

### <a name="solution"></a>Solução
Monte a partilha a partir de uma linha de comandos de não administrador. Em alternativa, pode seguir [neste tópico do TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o **EnableLinkedConnections** valor de registo.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Comando net use falha se a conta de armazenamento contém uma barra

### <a name="cause"></a>Causa

O comando net use interpreta uma barra (/) como uma opção de linha de comandos. Se o nome da sua conta de utilizador for iniciado com uma barra, o mapeamento de unidade falha.

### <a name="solution"></a>Solução

Pode utilizar qualquer um dos seguintes passos para resolver o problema:

- Execute o seguinte comando do PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  De um ficheiro batch, pode executar o comando desta forma:

  `Echo new-smbMapping ... | powershell -command –`

- Coloque aspas à volta a chave para contornar este problema –, a menos que a barra é o primeiro caráter. Se for, utilize o modo interativo e introduza a palavra-passe em separado ou voltar a gerar as chaves para obter uma chave que não iniciar com uma barra.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplicação ou serviço não é possível aceder a uma unidade montada de ficheiros do Azure

### <a name="cause"></a>Causa

Unidades estão montadas por utilizador. Se a aplicação ou serviço está em execução com uma conta de utilizador diferente daquela que montar a unidade, a aplicação não verão a unidade.

### <a name="solution"></a>Solução

Utilize uma das seguintes soluções:

-   Monte a unidade da mesma conta de utilizador que contém a aplicação. Pode utilizar uma ferramenta como o PsExec.
- Transmita o nome da conta de armazenamento e a chave de nome de utilizador e palavra-passe os parâmetros do net utilizam o comando.

Depois de seguir estas instruções, poderá receber a seguinte mensagem de erro quando executa a utilização de rede para a conta de serviço do sistema/rede: "erro de sistema 1312 foi excedido. Uma sessão de início de sessão especificado não existe. -Pode já ter foi terminada." Se isto ocorrer, certifique-se de que o nome de utilizador que é transmitida ao utilizar net inclui informações do domínio (por exemplo: "[nome da conta de armazenamento]. file.core.windows .net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Está a copiar um ficheiro para um destino que não suporta encriptação"

Quando um ficheiro é copiado através da rede, o ficheiro é desencriptado no computador de origem, transmitido em texto não encriptado e encriptado novamente no destino. No entanto, poderá ver o seguinte erro quando está a tentar copiar um ficheiro encriptado: "Está a copiar o ficheiro para um destino que não suporta encriptação."

### <a name="cause"></a>Causa
Este problema pode ocorrer se estiver a utilizar o sistema de encriptação de ficheiros (EFS). Podem ser copiados os ficheiros encriptados por BitLocker para ficheiros do Azure. No entanto, os ficheiros do Azure não suporta NTFS EFS.

### <a name="workaround"></a>Solução
Para copiar um ficheiro através da rede, tem de desencriptá-lo primeiro. Utilize um dos seguintes métodos:

- Utilize o **copiar /d** comando. Permite que os ficheiros encriptados seja guardada como ficheiros desencriptados no destino.
- Defina a seguinte chave de registo:
  - Caminho = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo de valor = DWORD
  - Nome = CopyFileAllowDecryptedRemoteDestination
  - Valor = 1

Lembre-se de que a chave de registo a definição afeta todas as operações de cópia que são efetuadas para partilhas de rede.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
