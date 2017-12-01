---
title: 'O Azure AD Connect: Como recuperar LocalDB problema 10 GB | Microsoft Docs'
description: "Este tópico descreve como recuperar o serviço de sincronização ligar do Azure AD quando encontra LocalDB 10GB limitar o problema."
services: active-directory
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: ab2aed2b9e86dc9b3111dc1fdf9deea2e2930c7c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>O Azure AD Connect: Como recuperar a partir de limite de 10 GB LocalDB
O Azure AD Connect necessita de uma base de dados do SQL Server para armazenar dados de identidade. Pode utilizar a predefinição da LocalDB do SQL Server 2012 Express instalada com o Azure AD Connect ou utilizar o seu SQL completo. O SQL Server Express impõe um limite de tamanho de 10 GB. Ao utilizar a LocalDB e este limite ser atingido, o Serviço de Sincronização do Azure AD Connect já não pode iniciar ou sincronizar corretamente. Este artigo fornece os passos de recuperação.

## <a name="symptoms"></a>Sintomas
Existem dois sintomas comuns:

* Serviço de sincronização ligar do Azure AD **está em execução** , mas não conseguir sincronizar com *"parou-base de dados-disco-completa"* erro.

* Serviço de sincronização ligar do Azure AD **não consegue iniciar**. Ao tentar iniciar o serviço, falhar com eventos 6323 e a mensagem de erro *"o servidor encontrou um erro porque o SQL Server está fora do espaço em disco."*

## <a name="short-term-recovery-steps"></a>Passos de recuperação de curta duração
Esta secção fornece os passos para recuperar o espaço de base de dados necessário para o serviço do Azure AD Connect sincronização retomar a operação. Os passos incluem:
1. [Determinar o estado do serviço de sincronização](#determine-the-synchronization-service-status)
2. [Reduzir a base de dados](#shrink-the-database)
3. [Eliminar dados do histórico de execução](#delete-run-history-data)
4. [Reduza o período de retenção de dados do histórico de execução](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Determinar o estado do serviço de sincronização
Em primeiro lugar, determine se o serviço de sincronização ainda está em execução ou não:

1. Inicie sessão no seu servidor do Azure AD Connect como administrador.

2. Aceda a **Gestor de controlo de serviço**.

3. Verificar o estado de **Microsoft Azure AD Sync**.


4. Se estiver em execução, não parar ou reiniciar o serviço. Ignorar [diminuir a base de dados](#shrink-the-database) passo e vá para [eliminar dados do histórico de execução](#delete-run-history-data) passo.

5. Se não está em execução, tente iniciar o serviço. Se o serviço for iniciado com êxito, ignore [diminuir a base de dados](#shrink-the-database) passo e vá para [eliminar dados do histórico de execução](#delete-run-history-data) passo. Caso contrário, continue com [diminuir a base de dados](#shrink-the-database) passo.

### <a name="shrink-the-database"></a>Reduzir a base de dados
Utilize a operação de encolhimento para libertar espaço suficiente DB para iniciar o serviço de sincronização. -Liberta espaço de base de dados removendo espaços em branco na base de dados. Este passo é melhor esforço, como não é garantido que pode recuperar sempre espaço. Para saber mais sobre a operação de encolhimento, leia este artigo [diminuir uma base de dados](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Ignore este passo se pode obter o serviço de sincronização para ser executada. Não é recomendado reduzir a BD do SQL Server, que pode levar a fraco desempenho devido à fragmentação maior.

O nome da base de dados criada para o Azure AD Connect é **ADSync**. Para efetuar uma operação de encolhimento, tem de iniciar sessão como o administrador do sistema ou DBO da base de dados. Durante a instalação do Azure AD Connect, as seguintes contas são concedidas direitos de administrador do sistema:
* Administradores locais
* A conta de utilizador que foi utilizada para executar a instalação do Azure AD Connect.
* A conta de serviço de sincronização que é utilizada como o contexto de funcionamento do serviço de sincronização ligar do Azure AD.
* O grupo local ADSyncAdmins que foi criada durante a instalação.

1. Cópia de segurança da base de dados ao copiar **ADSync.mdf** e **ADSync_log.ldf** ficheiros localizados em `%ProgramFiles%\Microsoft Azure AD Sync\Data` numa localização segura.

2. Inicie uma nova sessão do PowerShell.

3. Navegue até à pasta `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Iniciar **sqlcmd** utilitário executando o comando `./SQLCMD.EXE -S “(localdb)\.\ADSync” -U <Username> -P <Password>`, utilizando as credenciais de um sysadmin ou a base de dados DBO.

5. Para reduzir a base de dados, na linha de comandos sqlcmd (1 >), introduza `DBCC Shrinkdatabase(ADSync,1);`, seguido `GO` na linha seguinte.

6. Se a operação for concluída com êxito, tente iniciar o serviço de sincronização de novo. Se pode iniciar o serviço de sincronização, aceda a [eliminar dados do histórico de execução](#delete-run-history-data) passo. Caso contrário, contacte o suporte.

### <a name="delete-run-history-data"></a>Eliminar dados do histórico de execução
Por predefinição, o Azure AD Connect mantém até visão de sete dias de dados do histórico de execução. Neste passo, vamos eliminar os dados de histórico de execução para recuperar o espaço de base de dados para que o serviço de sincronização ligar do Azure AD pode começar a sincronizar novamente.

1.  Iniciar **Synchronization Service Manager** acedendo ao serviço de sincronização inicial →.

2.  Vá para o **operações** separador.

3.  Em **ações**, selecione **executa limpar**...

4.  Pode optar por **limpar todas as execuções** ou **encriptado é executado antes de... <date>**  opção. Recomenda-se que comece por limpar executar histórico de dados que são mais antigo do que dois dias. Se continuar a executar o problema de tamanho de base de dados, em seguida, escolha o **limpar todas as execuções** opção.

### <a name="shorten-retention-period-for-run-history-data"></a>Reduza o período de retenção de dados do histórico de execução
Este passo é para reduzir a probabilidade de execução para o problema de limite de 10 GB após vários ciclos de sincronização.

1. Abra uma nova sessão do PowerShell.

2. Executar `Get-ADSyncScheduler` e tome nota da propriedade PurgeRunHistoryInterval, que especifica o período de retenção atual.

3. Executar `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` para definir o período de retenção para dois dias. Ajuste o período de retenção conforme apropriado.

## <a name="long-term-solution--migrate-to-full-sql"></a>Solução de longa duração – migrar para o SQL completo
Em geral, o problema é facto de que o tamanho de base de dados de 10 GB já não é suficiente para o Azure AD Connect sincronizar o seu diretório Active Directory no local ao Azure AD. Recomenda-se que mude para utilizar a versão completa do SQL server. Não é possível substituir diretamente a LocalDB de uma implementação existente do Azure AD Connect pela base de dados da versão completa do SQL. Em vez disso, tem de implementar um novo servidor do Azure AD Connect com a versão completa do SQL. É recomendado que efetue uma migração rotativa, em que o novo servidor do Azure AD Connect (com a base de dados do SQL) seja implementado como um servidor de teste, ao lado do servidor do Azure AD Connect existente (com a LocalDB). 
* Para obter instruções sobre como configurar o SQL remoto com o Azure AD Connect, consulte o artigo [Instalação personalizada do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Para instruções sobre a migração rotativa de atualização do Azure AD Connect, consulte o artigo [Azure AD Connect: atualizar de uma versão anterior para a versão mais recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
