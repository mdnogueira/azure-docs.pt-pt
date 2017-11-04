---
title: Utilizar servidores NPS existentes para fornecer capacidades de MFA do Azure | Microsoft Docs
description: "A extensão de servidor de políticas de rede de Azure multi-factor Authentication é uma solução simples para adicionar capacidades de vericiation baseado na nuvem em dois passos à sua infraestrutura de autenticação existente."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: joflore
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 3cdeec642590d4ab4ae342e8675c124fa86bc970
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrar a sua infraestrutura existente do NPS com multi-factor Authentication do Azure

A extensão de servidor de políticas de rede (NPS) para o Azure MFA adiciona funcionalidades MFA baseado na nuvem à sua infraestrutura de autenticação utilizando os servidores existentes. Com a extensão NPS, pode adicionar chamada telefónica, mensagem de texto ou verificação de aplicação de telefone para o fluxo de autenticação existente sem ter de instalar, configurar e manter novos servidores. 

Esta extensão foi criada para as organizações que pretendem proteger as ligações VPN sem a implementar o servidor MFA do Azure. A extensão NPS atua como um adaptador entre o RADIUS e baseado na nuvem do MFA do Azure para fornecer um segundo fator de autenticação para federada ou utilizadores sincronizados.

Quando utilizar a extensão NPS para o MFA do Azure, o fluxo de autenticação inclui os seguintes componentes: 

1. **Servidor NAS/VPN** recebe pedidos de clientes VPN e converte-os em pedidos RADIUS para servidores NPS. 
2. **Servidor NPS** estabelece ligação ao Active Directory para efetuar a autenticação primária para os pedidos RADIUS e, após concluído com sucesso, transmite o pedido para quaisquer extensões instaladas.  
3. **Extensão de NPS** aciona um pedido para o MFA do Azure para a autenticação secundária. Assim que a extensão de recebe a resposta e, se o desafio MFA for bem sucedida, o pedido de autenticação for concluída, fornecendo o servidor NPS com tokens de segurança que incluem uma afirmação de MFA, emitiram pelo STS do Azure.  
4. **Azure MFA** comunica com o Azure Active Directory para obter detalhes do utilizador e efetua a autenticação secundária utilizando um método de verificação configurado para o utilizador.

O diagrama seguinte ilustra este fluxo de pedido de autenticação de alto nível: 

![Diagrama de fluxo de autenticação](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planear a sua implementação

A extensão NPS processa automaticamente redundância, pelo que não necessita de uma configuração especial.

Pode criar o número de servidores preparados para o MFA do Azure NPS conforme necessário. Se instalar vários servidores, deve utilizar um certificado de cliente de diferença para cada um deles. Criar um certificado para cada servidor significa que pode atualizar individualmente cada certificado e não se preocupe período de indisponibilidade todos os seus servidores.

Servidores VPN de encaminhar pedidos de autenticação, pelo que precisam de ter em consideração os novos servidores NPS de ativar a MFA do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A extensão NPS destina-se para trabalhar com a infraestrutura existente. Certifique-se de que tem os seguintes pré-requisitos antes de começar.

### <a name="licenses"></a>Licenças

A extensão de NPS para o Azure MFA está disponível para clientes com [licenças para o Azure multi-factor Authentication](multi-factor-authentication.md) (incluído com o Azure AD Premium, EMS ou uma subscrição de MFA).

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 ou posterior.

### <a name="libraries"></a>Bibliotecas

Estas bibliotecas são instaladas automaticamente com a extensão.

-   [Visual C++ Redistributable Packages for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure módulo Active Directory para Windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

O Microsoft Azure Active Directory módulo para o Windows PowerShell está instalado, se ainda não estiver presente, através de um script de configuração executado como parte do processo de configuração. Não é necessário para instalar este módulo antecedência se não estiver já instalado.

### <a name="azure-active-directory"></a>Azure Active Directory

Todas as pessoas com a extensão NPS devem ser sincronizadas para o Azure Active Directory utilizando o Azure AD Connect e tem de estar registada para MFA.

Quando instalar a extensão, tem das credenciais de ID e a administração de diretório para o seu inquilino do Azure AD. Pode encontrar o ID de diretório no [portal do Azure](https://portal.azure.com). Inicie sessão como administrador, selecione o **do Azure Active Directory** ícone à esquerda, em seguida, selecione **propriedades**. Copiar o GUID no **ID de diretório** caixa e guardá-lo. Utilize este GUID como o ID do inquilino quando instalar a extensão NPS.

![Localizar o ID de diretório em Propriedades do Azure Active Directory](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>Preparar o ambiente

Antes de instalar a extensão NPS, pretende preparar o ambiente para processar o tráfego de autenticação.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Ativar a função NPS num servidor associado a um domínio

O servidor NPS liga ao Azure Active Directory e autentica os pedidos MFA. Escolha um servidor para esta função. Recomendamos que escolha um servidor que não a processar pedidos de outros serviços, porque a extensão NPS emite erros para quaisquer pedidos que não são RADIUS.

1. No seu servidor, abra o **Assistente Adicionar funções e funcionalidades** no menu de início rápido do Gestor de servidor.
2. Escolha **instalação baseada em funções ou baseada em funcionalidade** para o seu tipo de instalação.
3. Selecione o **serviços de acesso e política de rede** função de servidor. Pode aparecer uma janela para o informar sobre as funcionalidades necessárias para executar esta função.
4. Continue com o assistente até a página de confirmação. Selecione **instalar**.

Agora que tem um servidor designado para o NPS, também deve configurar este servidor para processar pedidos RADIUS recebidos da solução VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurar a sua solução VPN para comunicar com o servidor NPS

Dependendo do que na solução de VPN que utilizar, os passos para configurar a política de autenticação RADIUS variam. Configure esta política para apontar para o servidor NPS de RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Utilizadores de domínio de sincronização para a nuvem

Este passo pode já estar concluído no seu inquilino, mas está pronto para verificar que o Azure AD Connect tem sincronizado bases de dados recentemente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **do Azure Active Directory** > **do Azure AD Connect**
3. Certifique-se de que o estado de sincronização **ativado** e de que a última sincronização foi há menos de uma hora.

Se precisar de iniciar uma nova round de sincronização,-nas instruções no [sincronização do Azure AD Connect: programador](../active-directory/connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinar quais os utilizadores podem utilizar os métodos de autenticação

Existem dois fatores que afetam os métodos de autenticação estão disponíveis com uma implementação de extensão NPS:

1. O algoritmo de encriptação da palavra-passe utilizado entre o cliente RADIUS (VPN, Netscaler servidor, ou outros) e os servidores NPS.
   - **PAP** suporta todos os métodos de autenticação do MFA do Azure na nuvem: chamada telefónica, mensagem de texto unidirecional, a notificação de aplicação móvel e código de verificação da aplicação móvel.
   - **CHAPV2** e **EAP** suportam a chamada telefónica e notificação da aplicação móvel.
2. Os métodos de entrada que a aplicação de cliente (VPN, Netscaler servidor, ou outros) pode processar. Por exemplo, o cliente VPN possui algum meio para permitir ao utilizador para o tipo num código de verificação de texto ou aplicação móvel?

Quando implementa a extensão NPS, utilize estes fatores para avaliar os métodos estão disponíveis para os seus utilizadores. Se o cliente RADIUS suporta PAP, mas o cliente UX não tem campos de entrada para um código de verificação, em seguida, chamada telefónica notificação da aplicação móvel, sendo as duas opções suportadas.

Pode [desativar métodos de autenticação não suportado](multi-factor-authentication-whats-next.md#selectable-verification-methods) no Azure.

### <a name="enable-users-for-mfa"></a>Permitir que os utilizadores para a MFA

Antes de implementar a extensão NPS completa, terá de ativar a MFA para os utilizadores que pretende efetuar a verificação de dois passos. Mais imediatamente, para testar a extensão como implementá-lo, precisa de, pelo menos, um teste conta totalmente registado para o multi-factor Authentication.

Utilize estes passos para obter uma conta de teste foi iniciada.
1. Inicie sessão no [https://aka.ms/mfasetup](https://aka.ms/mfasetup) com uma conta de teste. 
2. Siga as instruções para configurar um método de verificação.
3. Cria uma política de acesso condicional ou [alterar o estado do utilizador](multi-factor-authentication-get-started-user-states.md) para exigir a verificação de dois passos para a conta de teste. 

Os utilizadores também tem de seguir estes passos para inscrever antes de se podem autenticar com a extensão NPS.

## <a name="install-the-nps-extension"></a>Instale a extensão NPS

> [!IMPORTANT]
> Instale a extensão NPS num servidor diferente que o ponto de acesso VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Transferir e instalar a extensão NPS do MFA do Azure

1.  [Transfira a extensão NPS](https://aka.ms/npsmfa) do Centro de transferências da Microsoft.
2.  Copie o binário para o servidor de políticas de rede que pretende configurar.
3.  Executar *setup.exe* e siga as instruções de instalação. Se encontrar erros, verifique se as dois bibliotecas da secção de pré-requisitos foram instaladas com êxito.

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

O instalador cria um script do PowerShell nesta localização: `C:\Program Files\Microsoft\AzureMfa\Config` (em que C:\ é a unidade de instalação). Este script do PowerShell executa as seguintes ações:

-   Crie um certificado autoassinado.
-   Associe a chave pública do certificado para o serviço principal no Azure AD.
-   Armazene o certificado no arquivo de certificados do computador local.
-   Conceder acesso à chave privada do certificado de utilizador de rede.
-   Reinicie o NPS.

A menos que pretenda utilizar os seus próprios certificados (em vez dos certificados autoassinados que gera o script do PowerShell), execute o Script do PowerShell para concluir a instalação. Se instalar a extensão em vários servidores, cada um deles deve ter o seu próprio certificado.

1. Execute o Windows PowerShell como administrador.
2. Altere os diretórios.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Execute o script do PowerShell criado pelo instalador.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Inicie sessão no Azure AD como um administrador.
5. PowerShell pede o ID do inquilino. Utilize o GUID de ID de diretório que copiou do portal do Azure na secção pré-requisitos.
6. PowerShell mostra uma mensagem de êxito quando o script estiver concluído.  

Repita estes passos em todos os servidores adicionais de NPS que pretende configurar o balanceamento de carga.

>[!NOTE]
>Se utilizar os seus próprios certificados em vez da geração de certificados com o script do PowerShell, certifique-se de que estes são alinhados com a Convenção de nomenclatura de NPS. O nome do requerente tem de ser **CN =\<TenantID\>, UO = Microsoft NPS extensão**. 

## <a name="configure-your-nps-extension"></a>Configurar a extensão NPS

Esta secção inclui as considerações de design e sugestões para implementações de extensão concluída com êxito do NPS.

### <a name="configuration-limitations"></a>Limitações de configuração

- A extensão NPS para o MFA do Azure não inclui as ferramentas para migrar utilizadores e as definições do servidor MFA para a nuvem. Por este motivo, sugerimos que utilizando a extensão para novas implementações em vez de implementação existente. Se utilizar a extensão numa implementação existente, os utilizadores têm de efetuar uma prova de segurança novamente para preencher os detalhes MFA na nuvem.  
- A extensão NPS utiliza o UPN do Active directory no local para identificar o utilizador na MFA do Azure para efetuar a autenticação secundária A extensão pode ser configurada para utilizar um identificador de diferentes, como o ID de início de sessão alternativo ou campos personalizados do Active Directory que não seja o UPN. Consulte [avançado de opções de configuração para a extensão NPS para o multi-factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md) para obter mais informações.
- Nem todos os protocolos de encriptação suportam todos os métodos de verificação.
   - **PAP** suporta chamada telefónica, mensagem de texto unidirecional, notificação da aplicação móvel e o código de verificação da aplicação móvel
   - **CHAPV2** e **EAP** suportam a chamada telefónica e notificação da aplicação móvel

### <a name="control-radius-clients-that-require-mfa"></a>Clientes RADIUS controlo que exigem a MFA

Depois de ativar a MFA para um cliente RADIUS utilizando a extensão de NPS, todas as autenticações do cliente são necessários para executar a MFA. Se pretender ativar a MFA para alguns clientes RADIUS, mas não a outras pessoas, pode configurar dois servidores NPS e instale a extensão no apenas um deles. Configure clientes RADIUS que pretende exigir a MFA enviar pedidos para o servidor NPS configurado com a extensão e outros clientes RADIUS para o servidor NPS não configurado com a extensão.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Preparar para os utilizadores que não estão registados para o MFA

Se tiver utilizadores que não estão registados para o MFA, pode determinar o que acontece quando tentar autenticar. Utilize a definição de registo *REQUIRE_USER_MATCH* no caminho do registo *HKLM\Software\Microsoft\AzureMFA* para controlar o comportamento de funcionalidade. Esta definição não tem uma opção de configuração único:

| Chave | Valor | Predefinição |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | VERDADEIRO/FALSO | Não definido (equivalentes como TRUE) |

O objetivo desta definição consiste em determinar o que fazer quando um utilizador não estiver inscrito para a MFA. Quando a chave não existe, não está definida ou é definido como VERDADEIRO e o utilizador não estiver inscrito e, em seguida, a extensão falha o desafio MFA. Quando a chave está definida como FALSE e o utilizador não estiver inscrito, o processo de autenticação continua sem efetuar o MFA.

Pode optar por criar esta chave e defina-o como falso enquanto os seus utilizadores estão a integração e poderão nem todos os ser inscritos para MFA do Azure ainda. No entanto, uma vez que a chave a definição permite aos utilizadores que não estão registados para a MFA iniciar sessão, deve remover esta chave antes de ir para produção.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Como verificar que o certificado de cliente é instalado como esperado?

Procure o certificado autoassinado criado pelo instalador no arquivo de certificados e verifique se a chave privada tem as permissões concedidas ao utilizador **serviço de rede**. O certificado tem um nome de requerente do **CN \<tenantid\>, UO = Microsoft NPS extensão**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Como verificar que o meu certificado de cliente se encontra associado ao meu inquilino no Azure Active Directory?

Abra a linha de comandos do PowerShell e execute os seguintes comandos:

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Estes comandos imprimir todos os certificados associar o seu inquilino com a instância da extensão NPS na sessão do PowerShell. Procure o certificado ao exportar o certificado de cliente como um ficheiro de "X.509(.cer) com codificação Base 64" sem a chave privada e compará-lo com a lista a partir do PowerShell.

Válido-do e válido-até carimbos, que estão no formato legível por humanos, podem ser utilizados para filtrar misfits óbvios, se o comando devolver mais do que um certificado.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Por que razão os meus pedidos falham com o erro no token ADAL?

Este erro pode ser devido a uma das várias razões. Utilize estes passos para ajudar a resolver problemas:

1. Reinicie o servidor NPS.
2. Certifique-se de que esse certificado de cliente é instalado como esperado.
3. Verifique se o certificado associado ao seu inquilino do Azure AD.
4. Certifique-se de que https://login.microsoftonline.com/ está acessível a partir do servidor que executa a extensão.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Por que motivo autenticação falhar com um erro nos registos HTTP a indicar que o utilizador não foi encontrado

Certifique-se de que o AD Connect está em execução e que o utilizador esteja presente no Active Directory do Windows e do Azure Active Directory.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Por que razão vejo HTTP ligar erros nos registos com todos os meus autenticações falhar?

Certifique-se de que https://adnotifications.windowsazure.com está acessível a partir do servidor que executa a extensão NPS.


## <a name="next-steps"></a>Passos seguintes

- Configurar os IDs de alternativos para início de sessão ou configurar uma lista de exceções para IPs não deve efetuar a verificação de dois passos no [avançado de opções de configuração para a extensão NPS para o multi-factor Authentication](nps-extension-advanced-configuration.md)

- Saiba como integrar [Gateway de ambiente de trabalho remoto](nps-extension-remote-desktop-gateway.md) e [servidores VPN](nps-extension-vpn.md) através da extensão NPS

- [Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure](multi-factor-authentication-nps-errors.md)
