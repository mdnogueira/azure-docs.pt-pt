---
title: "Instalar o Azure AD Connect através da utilização de uma base de dados ADSync | Microsoft Docs"
description: "Este tópico descreve como utilizar uma base de dados ADSync existente."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.reviewer: cychua
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: billmath
ms.openlocfilehash: d005042fffcf8f4ff99876961a55d254fd4fb2d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalar o Azure AD Connect, utilizando uma base de dados ADSync
O Azure AD Connect requer uma base de dados do SQL Server para armazenar dados. Pode utilizar o predefinido do que SQL Server 2012 Express LocalDB instalado com o Azure AD Connect ou utilizar a sua própria versão completa do SQL Server. Anteriormente, quando tiver instalado o Azure AD Connect, uma nova base de dados com o nome ADSync sempre foi criado. Com o Azure AD Connect versão 1.1.613.0 (ou após), tem a opção para instalar o Azure AD Connect-lo a apontar para uma base de dados ADSync existente.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Vantagens da utilização de uma base de dados ADSync
Por que aponta para uma base de dados ADSync existente:

- Exceto informações de credenciais, configuração de sincronização armazenada na base de dados ADSync (incluindo as regras de sincronização personalizados, os conectores, filtragem e configuração de funcionalidades opcionais) é automaticamente recuperada e utilizada durante a instalação . As credenciais utilizadas pelo Azure AD Connect para sincronizar as alterações no local com o AD e o Azure AD são encriptadas e só pode ser acedido pelo servidor do Azure AD Connect anterior.
- Todos os dados de identidade (associada à metaverso e espaços conectores) e os cookies de sincronização armazenados na base de dados ADSync também são recuperados. O recentemente instalado o Azure AD Connect servidor pode continuar a sincronizar a partir de onde o servidor do Azure AD Connect anterior parou, em vez de ter a necessidade de efetuar uma sincronização completa.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Cenários onde é vantajoso utilizar uma base de dados ADSync
Estas vantagens são úteis nos seguintes cenários:


- Tiver uma implementação existente do Azure AD Connect. O servidor do Azure AD Connect existente já não está a funcionar, mas o SQL server que contém a base de dados ADSync ainda está a funcionar. Pode instalar um novo servidor do Azure AD Connect e apontar para a base de dados ADSync existente. 
- Tiver uma implementação existente do Azure AD Connect. O SQL server que contém a base de dados ADSync já não está a funcionar. No entanto, tem uma cópia de segurança recente da base de dados. Pode restaurar a base de dados ADSync pela primeira vez a um novo SQL server. Após o qual, pode instalar um novo servidor do Azure AD Connect e apontar para a base de dados ADSync restaurada.
- Tiver uma implementação existente do Azure AD Connect que está a utilizar LocalDB. Devido ao limite de 10 GB imposto LocalDB, que pretende migrar para o SQL completo. Pode criar cópias de segurança da base de dados ADSync da LocalDB e restaurá-lo para um SQL server. Após o qual, pode reinstalar um novo servidor do Azure AD Connect e apontar para a base de dados ADSync restaurada.
- Está a tentar configurar o servidor de transição e pretender certificar-se de que corresponde a respetiva configuração do servidor do Active Directory atual. Pode criar cópias de segurança da base de dados ADSync e restaurá-lo para outro servidor SQL. Após o qual, pode reinstalar um novo servidor do Azure AD Connect e apontar para a base de dados ADSync restaurada.

## <a name="prerequisite-information"></a>Informações de pré-requisitos

Notas importantes a efetuar tenha em atenção de antes de continuar:


- Certifique-se de que revê os pré-requisitos para instalar o Azure AD Connect em Hardware e pré-requisitos e conta as permissões necessárias para instalar o Azure AD Connect. As permissões necessárias para instalar o Azure AD Connect, utilizando o modo de "utilizar base de dados existente" é igual à instalação "personalizada".
- A versão do Azure AD Connect utilizada para a instalação deve satisfazer os seguintes critérios:
    - 1.1.613.0 ou acima, e
    - Igual ou superior à versão do Azure AD Connect utilizado pela última vez com a base de dados ADSync. Se a versão do Azure AD Connect utilizada para a instalação for superior à versão do utilizado pela última vez com a base de dados ADSync, uma sincronização completa poderá ser necessária.  Isto é necessário se existirem alterações de regra de esquema ou sincronização entre duas versões. 
- A base de dados ADSync utilizado deve conter um Estado de sincronização que é relativamente recente. A última atividade de sincronização com a base de dados existente do ADSync deve estar no últimas três semanas.
- Ao instalar o Azure AD Connect utilizando o método "utilizar base de dados existente", o método de início de sessão configurado no servidor do Azure AD Connect anterior não é preservado. Além disso, não é possível configurar o método de início de sessão durante a instalação. Só pode configurar o método de início de sessão após a conclusão da instalação.
- Não é possível ter vários servidores do Azure AD Connect partilham a mesma base de dados ADSync. O método "utilizar base de dados existente" permite-lhe reutilizar a base de dados ADSync existente com um novo servidor do Azure AD Connect. Não suporta a partilha.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Passos para instalar o Azure AD Connect com o modo de "utilizar base de dados existente"
1.  Transferir o instalador do Azure AD Connect (AzureADConnect.MSI) para o Windows server. Faça duplo clique o instalador do Azure AD Connect para começar a instalar o Azure AD Connect.
2.  Depois de concluída a instalação de MSI, inicia o Assistente do Azure AD Connect com a configuração de modo rápida. Feche o ecrã clicando no ícone de saída.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db1.png)
3.  Inicie uma nova linha de comandos ou a sessão do PowerShell. Navegue até à pasta <drive>\Programas\Microsoft do Azure AD Connect. Execute o comando.\AzureADConnect.exe /useexistingdatabase para iniciar o Assistente do Azure AD Connect no modo de configuração de "Utilizar base de dados existente".
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
4.  São greeted com o bem-vindo ao Azure AD Connect ecrã. Depois de aceitar os termos de licenciamento e o aviso de privacidade, clique em **continuar**.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db3.png)
5.  No **instalar os componentes necessários** ecrã, o **utilizar um servidor existente do SQL Server** opção está ativada. Especifique o nome do SQL server que aloja a base de dados ADSync. Se a instância do motor de SQL Server utilizada para alojar a base de dados ADSync não for a instância predefinida no SQL server, tem de especificar o nome de instância do motor SQL. Além disso, se não estiver ativada a navegação do SQL Server, também tem de especificar o número de porta de instância de motor do SQL Server. Por exemplo:         
![Bem-vindo](media/active-directory-aadconnect-existing-database/db4.png)           

6.  No **ligar para o Azure AD** ecrã, tem de fornecer as credenciais de um administrador global do diretório do Azure AD. A recomendação está a utilizar uma conta do domínio onmicrosoft.com predefinido. Esta conta é utilizada apenas para criar uma conta de serviço no Azure AD e deixa de ser utilizada uma vez concluído o assistente.
![Ligar](media/active-directory-aadconnect-existing-database/db5.png)
 
7.  No **ligar os diretórios** ecrã, a floresta de AD existente configurada para a sincronização de diretórios está listada com uma entre vermelho junto-lo. Para sincronizar alterações no local, floresta do AD, uma conta do AD DS é necessária. O Assistente do Azure AD Connect não é possível obter as credenciais da conta do AD DS armazenados na base de dados ADSync porque as credenciais são encriptadas e só podem ser desencriptadas pelo servidor do Azure AD Connect anterior. Clique em **alteração credenciais** para especificar a conta do AD DS para a floresta do AD.
![Diretórios](media/active-directory-aadconnect-existing-database/db6.png)
 
 
8.  Na caixa de diálogo de pop-up, é possível (i) fornecem uma credencial de administrador de empresa e permitir que o Azure AD Connect, criar a conta do AD DS para si ou (ii) da conta do AD DS de criar manualmente e forneça a credencial para o Azure AD Connect. Assim que tiver selecionado uma opção e forneça as credenciais necessárias, clique em **OK** para fechar a caixa de diálogo de pop-up.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db7.png)
 
 
9.  Depois das credenciais são fornecidas, o ícone vermelho entre é substituído por um ícone verde marcas de escala. Clique em **Seguinte**.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db8.png)
 
 
10. No **pronto para configurar** ecrã, clique em **instalar**.
![Bem-vindo](media/active-directory-aadconnect-existing-database/db9.png)
 
 
11. Uma vez concluída a instalação, o servidor do Azure AD Connect é ativado automaticamente para o modo de teste. É recomendado que reveja a configuração do servidor e pendente exportações inesperado alterações antes de desativar o modo de teste. 

## <a name="next-steps"></a>Passos seguintes

- Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](active-directory-aadconnect-whats-next.md).
- Saiba mais acerca destas funcionalidades que foram ativadas com a instalação: [Impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).
- Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](active-directory-aadconnectsync-feature-scheduler.md).
- Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
