---
title: "Resolução de problemas de ficheiros do Azure no Linux | Microsoft Docs"
description: "Resolução de problemas de ficheiros do Azure no Linux"
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
ms.openlocfilehash: 8fd09c748786f0bfe070c3f41201eddc3d272116
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Resolução de problemas de ficheiros do Azure no Linux

Este artigo apresenta uma lista de problemas comuns que estão relacionados com ficheiros do Microsoft Azure quando se liga a partir de clientes do Linux. Também fornece possíveis causas e soluções para esses problemas.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[permissão negada] disco a quota excedida" quando tenta abrir um ficheiro

No Linux, receberá uma mensagem de erro é semelhante a:

**<filename>[permissão negada] Quota de disco foi excedida**

### <a name="cause"></a>Causa

Atingiu o limite superior de identificadores abertos simultâneos que são permitidos para um ficheiro.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos em simultâneo fechando alguns identificadores e, em seguida, repita a operação. Para obter mais informações, consulte [lista de verificação de armazenamento do Microsoft Azure, desempenho e escalabilidade](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Lenta a cópia de ficheiros e de ficheiros do Azure no Linux

-   Se não tiver um requisito de tamanho de e/s mínimo específico, recomendamos que utilize 1 MB como o tamanho de e/s para um desempenho ideal.
-   Se souber o tamanho do final de um ficheiro que estiver a expandir utilizando escritas e o software não surgir problemas de compatibilidade de uma cauda unwritten no ficheiro contém zeros, em seguida, defina o tamanho do ficheiro seguinte com antecedência em vez de efetuar cada escrita uma escrita expandir.
-   Utilize o método copy direita:
    -   Utilize [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre duas partilhas de ficheiros.
    -   Utilize [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre partilhas de ficheiros no computador local.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Montar error(112): anfitrião encontra-se para baixo" devido a um limite de tempo de restabelecimento de ligação

Quando o cliente está inativo há muito tempo, ocorre um erro de "112" montagem no cliente do Linux. Após o período de tempo inativo alargado, o cliente desligar e a ligação exceder o tempo limite.  

### <a name="cause"></a>Causa

A ligação pode ficar inactiva pelos seguintes motivos:

-   Falhas de comunicação de rede que impedem a estabelecer uma ligação de TCP para o servidor quando é utilizada a opção de montagem "de forma recuperável" predefinida
-   Correções de restabelecimento de ligação recente que não estão presentes na kernels anteriores

### <a name="solution"></a>Solução

Este problema de restabelecimento de ligação do kernel do Linux agora é corrigido como parte das seguintes alterações:

- [Corrija voltar a ligar para não diferir smb3 sessão restabelecimento de ligação de tempo depois de restabelecimento de ligação de socket](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
-   [Chamar o serviço de eco imediatamente após o restabelecimento de ligação de socket](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
-   [CIFS: Corrigir um danos de memória possível restabelecer a ligação](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
-   [CIFS: Corrigir uma possível duplo bloqueio de exclusão mútua durante a restabelecer ligação (para o kernel v4.9 e posterior)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

No entanto, estas alterações podem não ser ainda convertidos para serem todas as distribuições de Linux. Esta correção e outras correções de restabelecimento de ligação são efetuadas as seguintes kernels Linux populares: 4.4.40, 4.8.16 e 4.9.1. Pode obter esta correção através da atualização para uma destas versões de kernel recomendada.

### <a name="workaround"></a>Solução

Pode contornar este problema, especificando uma montagem de disco rígida. Isto força o cliente deve aguardar até é estabelecida uma ligação ou até mesmo explicitamente for interrompido e pode ser utilizado para evitar erros devido a tempos limite de rede. No entanto, esta solução pode fazer com que aguarda indefinida. Esteja preparado para parar ligações conforme necessário.

Se não é possível atualizar para as versões mais recentes de kernel, pode contornar este problema por manter um ficheiro na partilha de ficheiros do Azure que escreve para cada 30 segundos ou menos. Tem de ser uma operação de escrita, tais como a conversão de data criada ou modificada no ficheiro. Caso contrário, poderá obter resultados em cache e a operação não pode acionar o restabelecimento.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Montar error(115): operação agora em curso" quando montar ficheiros do Azure utilizando o SMB 3.0

### <a name="cause"></a>Causa

Algumas distribuições de Linux ainda não suportam funcionalidades de encriptação no SMB 3.0 e os utilizadores podem receber uma mensagem de erro "115" se estes tentarem ficheiros do Azure de montagem, utilizando o SMB 3.0 devido a uma funcionalidade em falta. De momento, apenas é suportado 3.0 de SMB com a encriptação completa quando utilizar Ubuntu 16.04 ou posterior.

### <a name="solution"></a>Solução

Funcionalidade de encriptação para SMB 3.0 para Linux foi introduzida no 4.11 kernel. Esta funcionalidade permite a montagem do Azure da partilha de ficheiros no local ou uma região do Azure diferente. No momento da publicação, esta funcionalidade foi backported Ubuntu 17.04 e Ubuntu 16.10. Se o cliente SMB de Linux não suporta a encriptação, Azure montagem ficheiros utilizando o SMB 2.1 a partir de uma VM com Linux do Azure que se encontra no mesmo centro de dados como a conta de armazenamento de ficheiros.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Desempenho lento numa partilha de ficheiros do Azure montado numa VM com Linux

### <a name="cause"></a>Causa

Uma causa possível de desempenho lento está desativada a colocação em cache.

### <a name="solution"></a>Solução

Para verificar se a colocação em cache está desativada, procure o **cache =** entrada. 

**Cache = none** indica que a colocação em cache está desativada.  Remontar a partilha utilizando o comando de montagem de predefinição ou adicionar explicitamente o **cache = strict** opção para o comando de montagem para garantir que esse predefinido a colocação em cache ou "strict" modo de colocação em cache está ativada.

Em alguns cenários, o **serverino** pode fazer com que a opção de montar a **ls** comando a executar stat em relação a cada entrada de diretório. Este comportamento resulta numa degradação do desempenho quando está a listar um grande diretório. Pode verificar as opções de montagem seu **etc/fstab** entrada:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Também pode verificar se as opções corretas estão a ser utilizadas executando o **sudo montagem | grep cifs** comando e a verificar o resultado, como o seguinte exemplo de saída:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Se o **cache = strict** ou **serverino** opção é não está presente, desmontar e montar ficheiros do Azure novamente executando o comando de montagem das [documentação](../storage-how-to-use-files-linux.md). Em seguida, voltar que o **etc/fstab** entrada tem as opções corretas.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Carimbos de data / hora foram perdidas no copiar os ficheiros do Windows para Linux

Em plataformas Linux/Unix, o **cp -p** comando falha se o ficheiro 1 e 2 são propriedade de diferentes utilizadores.

### <a name="cause"></a>Causa

O sinalizador force **f** no COPYFILE resulta na execução **cp -p -f** no Unix. Este comando também falha preservar o carimbo de hora do ficheiro que não possui.

### <a name="workaround"></a>Solução

Utilize o utilizador da conta de armazenamento para copiar os ficheiros:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>Não é possível estabelecer ligação ou montar uma partilha de ficheiros do Azure

### <a name="cause"></a>Causa

As causas comuns para este problema são:


- Estão a utilizar um cliente de distribuição de Linux incompatível. Recomendamos que utilize as distribuições de Linux seguintes para ligar à partilha de ficheiros do Azure:

    - Ubuntu Server 14.04 + 
    - RHEL 7 + 
    - CentOS 7 + 
    - Debian 8 
    - openSUSE 13.2 + 
    - SUSE Linux Enterprise Server 12

- CIFS utils não estão instalados no cliente.
- O mínimo de versão SMB/CIFS 2.1 não está instalado o cliente.
- A encriptação SMB 3.0 não é suportada no cliente. A encriptação SMB 3.0 está disponível no Ubuntu 16.4 e versão posterior, SUSE 12.3 e versão posterior. Outras distribuições requerem kernel 4.11 e versão posterior.
- Está a tentar ligar a uma conta de armazenamento através da porta TCP 445 que não é suportada.
- Está a tentar a tentar ligar à partilha de ficheiros do Azure a partir de uma VM do Azure e a VM não se encontra na mesma região que a conta de armazenamento.

### <a name="solution"></a>Solução

Para resolver o problema, utilize o [ferramenta de resolução de problemas para os ficheiros de Azure montar erros no Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Esta ferramenta ajuda-o para validar o cliente com o ambiente, para detetar a configuração de cliente incompatível que poderia causar a falha de acesso para ficheiros do Azure, fornece orientação prescritiva sobre corrigir personalizada e, recolhe os rastreios de diagnóstico.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: não é possível aceder '&lt;caminho&gt;': erro de entrada/saída

Quando tenta à lista de partilham de ficheiros num ficheiro do Azure utilizando o comando ls, ls comando bloqueia quando a listagem de ficheiros recebem o erro seguinte:

**ls: não é possível aceder '&lt;caminho&gt;': erro de entrada/saída**


### <a name="solution"></a>Solução
Atualize o kernel do Linux para as seguintes versões que tem de corrigir este problema:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Todas as versões que seja maior ou igual a 4.13

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
