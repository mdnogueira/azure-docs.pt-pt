---
title: "Implementar a sincronização de palavra-passe com a sincronização do Azure AD Connect | Microsoft Docs"
description: "Fornece informações sobre como funciona a sincronização de palavra-passe e como configurar."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: billmath
ms.openlocfilehash: 048dfa812a5388b2fc47bafd2fae5d6e96414712
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="implement-password-synchronization-with-azure-ad-connect-sync"></a>Implementar a sincronização de palavra-passe com a sincronização do Azure AD Connect
Este artigo fornece informações que tem de sincronizar as palavras-passe de utilizador de uma instância do Active Directory no local sessão a uma instância do Azure Active Directory (Azure AD) baseado na nuvem.

## <a name="what-is-password-synchronization"></a>O que é a sincronização de palavra-passe
A probabilidade que está a ser bloqueados obtenham o seu trabalho devido a uma palavra-passe esquecida está relacionado com o número de palavras-passe diferentes tem de se lembrar. As mais palavras-passe que tem de se lembrar, maior probabilidade para se esqueça de um. Perguntas e chamadas sobre reposições de palavra-passe e outros problemas relacionados com a palavra-passe exigem os maioria dos recursos de suporte técnico.

Sincronização de palavra-passe é uma funcionalidade utilizada para sincronizar utilizador palavras-passe de uma instância do Active Directory no local para um Azure baseados na nuvem instância do AD.
Utilize esta funcionalidade para iniciar sessão nos serviços do Azure AD, como o Office 365, Microsoft Intune, CRM Online e do Azure Active Directory Domain Services (Azure AD DS). A iniciar sessão para o serviço utilizando a mesma palavra-passe que utiliza para iniciar sessão para a instância do Active Directory no local.

![O que é o Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Ao reduzir o número de palavras-passe, os utilizadores precisam para manter a apenas um. Sincronização de palavra-passe ajuda-o a:

* Melhore a produtividade dos utilizadores.
* Reduza os custos de suporte técnico.  

Além disso, se optar por utilizar [federação com o serviços de Federação do Active Directory (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), opcionalmente, pode configurar a sincronização de palavras-passe como uma cópia de segurança no caso de falha da sua infraestrutura do AD FS.

Sincronização de palavra-passe é uma extensão para a funcionalidade de sincronização de diretórios implementada pela sincronização do Azure AD Connect. Para utilizar a sincronização de palavra-passe no seu ambiente, tem de:

* Instalar o Azure AD Connect.  
* Configure a sincronização de diretórios entre a instância do Active Directory no local e a instância do Azure Active Directory.
* Ative a sincronização de palavra-passe.

Para obter mais detalhes, consulte o artigo [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

> [!NOTE]
> Para obter mais detalhes sobre o Azure Active Directory Domain Services configurada para a sincronização FIPS e a palavra-passe, consulte "sincronização de palavra-passe e FIPS" neste artigo.
>
>

## <a name="how-password-synchronization-works"></a>Como funciona a sincronização de palavra-passe
O serviço de domínio do Active Directory armazena as palavras-passe sob a forma de uma representação de valor de hash da palavra-passe de utilizador reais. Um valor de hash é um resultado de uma função matemática unidirecional (o *algoritmo hash*). Não há nenhum método para reverter o resultado de uma função unidirecional para a versão de texto simples de uma palavra-passe. Não é possível utilizar um hash de palavra-passe para iniciar sessão sua rede no local.

Para sincronizar a palavra-passe, a sincronização do Azure AD Connect extrai o hash de palavra-passe na instância do Active Directory no local. Processamento de segurança adicional é aplicado para o hash de palavra-passe antes de que está sincronizado para o serviço de autenticação do Azure Active Directory. As palavras-passe são sincronizadas numa base por utilizador e por ordem cronológica.

O fluxo de dados real do processo de sincronização de palavra-passe é semelhante da sincronização dos dados de utilizador, tais como DisplayName ou endereços de E-Mail. No entanto, as palavras-passe são sincronizadas mais frequentemente do que a janela de sincronização de diretórios padrão para outros atributos. O processo de sincronização de palavra-passe é executado a cada dois minutos. Não é possível modificar a frequência deste processo. Quando sincroniza uma palavra-passe, substitui a palavra-passe de nuvem existente.

Na primeira vez, ativar a funcionalidade de sincronização de palavra-passe, executa uma sincronização inicial das palavras-passe de todos os utilizadores no âmbito. Não é possível definir explicitamente um subconjunto de palavras-passe de utilizador que pretende sincronizar.

Quando altera uma palavra-passe de no local, a palavra-passe atualizada está sincronizada, com maior frequência num fim de minutos.
A funcionalidade de sincronização de palavra-passe automaticamente repete as tentativas de sincronização falhou. Se ocorrer um erro durante uma tentativa de sincronização de uma palavra-passe, é registado um erro no Visualizador de eventos.

A sincronização de uma palavra-passe não tem impacto no utilizador que tem atualmente sessão iniciado.
A sessão do serviço de nuvem atual não é imediatamente afetada por uma alteração de palavra-passe sincronizados ocorre enquanto tem sessão iniciada num serviço em nuvem. No entanto, quando o serviço em nuvem requer que se autentique novamente, tem de fornecer a palavra-passe nova.

Um utilizador tem de introduzir as respetivas credenciais empresariais uma segunda vez para autenticar com o Azure AD, independentemente se inicia sessão na sua rede empresarial. Estes padrão pode ser minimizados, no entanto, se o utilizador seleciona Keep-me assinado na caixa de verificação (KMSI) no início de sessão. Esta seleção define um cookie de sessão que ignora a autenticação para um curto período de tempo. Comportamento KMSI pode ser ativado ou desativado pelo administrador do Azure AD.

> [!NOTE]
> Sincronização de palavra-passe só é suportada para o utilizador de tipo de objeto no Active Directory. Não é suportada para o tipo de objetos iNetOrgPerson.

### <a name="detailed-description-of-how-password-synchronization-works"></a>Descrição detalhada do funcionamento da sincronização de palavra-passe
O seguinte descreve aprofundada como funciona a sincronização de palavra-passe entre o Active Directory e o Azure AD.

![Fluxo de palavra-passe detalhadas](./media/active-directory-aadconnectsync-implement-password-synchronization/arch3.png)


1. Cada dois minutos, o agente de sincronização de palavra-passe no servidor do AD Connect pedidos hashes de palavra-passe armazenadas (o atributo unicodePwd) de um controlador de domínio através do padrão [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) protocolo de replicação utilizado para sincronizar dados entre Controladores de domínio. A conta de serviço tem de ter os replicar as alterações de diretório e permissões replicar diretório alterações todos os AD (concedidas por predefinição numa instalação), para obter a palavra-passe hashes.
2. Antes de enviar, o DC encripta o hash de palavra-passe MD4 utilizando uma chave que é um [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) hash de chave de sessão RPC e um salt. Em seguida, envia o resultado para o agente de sincronização de palavra-passe através de RPC. O DC também transmite o salt para o agente de sincronização utilizando o protocolo de replicação do DC, pelo que o agente irá conseguir desencriptar o envelope.
3.  Depois do agente de sincronização de palavra-passe tiver o envelope encriptado, utiliza [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e salt para gerar uma chave para desencriptar os dados recebidos para o seu formato MD4 original. Em nenhum momento o agente de sincronização de palavra-passe têm acesso para a palavra-passe de texto não encriptado. Ao agente de sincronização de palavra-passe utilização de MD5 está estritamente para compatibilidade de protocolo de replicação com o DC e só é utilizada no local entre o DC e o agente de sincronização de palavra-passe.
4.  O agente de sincronização de palavra-passe expande o hash de binários de 16 bytes palavra-passe para 64 bytes convertendo primeiro o hash para uma cadeia hexadecimal de 32-byte, em seguida, converter esta cadeia novamente para o binário com codificação UTF-16.
5.  O agente de sincronização de palavra-passe adiciona um salt, constituída por salt um comprimento de 10 bytes, para o binário de 64 bytes para proteger ainda mais o hash original.
6.  O agente de sincronização de palavra-passe, em seguida, combina o MD4 hash plus salt e entradas para a [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) função. 1000 iterações do [HMAC SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) é utilizado o algoritmo de hash. 
7.  O agente de sincronização de palavra-passe demora o hash de 32 bytes resultante, concatena o salt e o número de iterações SHA256 ao mesmo (para utilização pelo Azure AD), em seguida, transmite a cadeia do Azure AD Connect para o Azure AD através de SSL.</br> 
8.  Quando um utilizador tenta iniciar sessão Azure AD e introduz a palavra-passe, a palavra-passe é executada através do mesmo MD4 + salt + PBKDF2 + HMAC SHA256 processo. Se o hash resultante corresponda ao hash armazenado no Azure AD, o utilizador ter introduzido a palavra-passe correta e é autenticado. 

>[!Note] 
>O hash MD4 original não é transmitido para o Azure AD. Em vez disso, o hash SHA256 do original MD4 hash é transmitido. Como resultado, se o hash armazenado no Azure AD é obtido, não pode ser utilizado num ataque de passagem do hash no local.

### <a name="how-password-synchronization-works-with-azure-active-directory-domain-services"></a>Como funciona a sincronização de palavra-passe com serviços de domínio do Active Directory do Azure
Também pode utilizar a funcionalidade de sincronização de palavra-passe para sincronizar as palavras-passe de no local para [do Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). Neste cenário, a instância do Azure Active Directory Domain Services autentica os utilizadores na nuvem com todos os métodos disponíveis na sua instância do Active Directory no local. A experiência deste cenário é semelhante ao utilizar a ferramenta de migração de diretório Active Directory (ADMT) num ambiente no local.

### <a name="security-considerations"></a>Considerações de segurança
Quando efetuar a sincronização de palavras-passe, a versão de texto simples da sua palavra-passe não está exposta a funcionalidade de sincronização de palavra-passe, para o Azure AD, ou qualquer um dos serviços associados.

Autenticação de utilizador ocorre no Azure AD, em vez de contra a instância do Active Directory da organização. Se a organização tiver preocupações em dados de palavra-passe em qualquer formulário abandonar o fileparser local, considere o facto de que os dados de palavra-passe SHA256 armazenados no Azure AD – um hash do hash MD4 original – é significativamente mais segura do que o que é armazenado no Active Directory. Além disso, porque não é possível desencriptar este hash SHA256, este não pode ser recuperada ao ambiente do Active Directory da organização e apresentado como uma palavra-passe de utilizador válido num ataque de passagem do hash.





### <a name="password-policy-considerations"></a>Considerações de política de palavra-passe
Existem dois tipos de políticas de palavra-passe que são afetados ao ativar a sincronização de palavra-passe:

* Política de complexidade de palavra-passe
* Política de expiração de palavra-passe

#### <a name="password-complexity-policy"></a>Política de complexidade de palavra-passe  
Quando a sincronização de palavra-passe é ativada, as políticas de complexidade de palavra-passe na instância do Active Directory no local substituem as políticas de complexidade na nuvem para os utilizadores sincronizados. Pode utilizar todas as palavras-passe válidas da sua instância do Active Directory no local para aceder aos serviços do Azure AD.

> [!NOTE]
> As palavras-passe para os utilizadores que são criados diretamente na nuvem estão ainda sujeitos a políticas de palavra-passe tal como definido na nuvem.

#### <a name="password-expiration-policy"></a>Política de expiração de palavra-passe  
Se um utilizador estiver no âmbito da sincronização de palavra-passe, a palavra-passe da conta de nuvem está definida como *nunca expirem*.

Pode continuar a iniciar sessão para os seus serviços em nuvem, utilizando uma palavra-passe sincronizada que expirou no seu ambiente no local. A palavra-passe de nuvem é atualizada da próxima vez que altere a palavra-passe no ambiente no local.

#### <a name="account-expiration"></a>Expiração da conta
Se a sua organização utiliza o atributo de accountExpires como parte da gestão de contas de utilizador, lembre-se de que este atributo não está sincronizado com o Azure AD. Como resultado, uma conta do Active Directory expirada num ambiente configurada para a sincronização de palavra-passe continuarão a estar ativa no Azure AD. Recomendamos que, se a conta tiver expirada, uma ação de fluxo de trabalho deve acionar um script do PowerShell que desativa a conta de utilizador do Azure AD. Por outro lado, quando a conta estiver ativada, a instância do Azure AD deve ser ativada.

### <a name="overwrite-synchronized-passwords"></a>Substituir as palavras-passe sincronizadas
Um administrador pode repor a palavra-passe manualmente através do Windows PowerShell.

Neste caso, a nova palavra-passe substitui a palavra-passe sincronizada e todas as políticas de palavra-passe definidas na nuvem são aplicadas para a nova palavra-passe.

Se alterar a palavra-passe de no local, a nova palavra-passe é sincronizado para a nuvem e substitui a palavra-passe atualizada manualmente.

A sincronização de uma palavra-passe não tem impacto no utilizador do Azure que tenha sessão iniciado. A sessão do serviço de nuvem atual não é imediatamente afetada por uma alteração de palavra-passe sincronizados ocorre enquanto tem sessão iniciada num serviço em nuvem. KMSI expande a duração desta diferença. Quando o serviço em nuvem requer que se autentique novamente, tem de fornecer a palavra-passe nova.

### <a name="additional-advantages"></a>Vantagens adicionais

- Geralmente, a sincronização de palavra-passe é mais simples de implementar do que um serviço de Federação. Não requer quaisquer servidores adicionais e elimina a dependência a um serviço de Federação elevada para autenticar os utilizadores. 
- Também pode ser ativada a sincronização de palavra-passe para além de Federação. Pode ser utilizado como contingência se o serviço de Federação sofre uma falha.












## <a name="enable-password-synchronization"></a>Ativar a sincronização de palavra-passe
Quando instalar o Azure AD Connect, utilizando o **definições rápidas** opção, a sincronização de palavra-passe é ativada automaticamente. Para obter mais detalhes, consulte [introdução ao Azure AD Connect utilizando as definições rápidas](active-directory-aadconnect-get-started-express.md).

Se utilizar definições personalizadas ao instalar o Azure AD Connect, sincronização de palavra-passe está disponível na página de início de sessão do utilizador. Para obter mais detalhes, consulte [instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Ativar a sincronização de palavra-passe](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Sincronização de palavra-passe e FIPS
Se o servidor tiver sido bloqueado para baixo, de acordo com Federal Information Processing Standard (FIPS), MD5 está desativada.

**Para ativar o MD5 para sincronização de palavra-passe, execute os seguintes passos:**

1. Aceda à Sync\Bin %programfiles%\Azure AD.
2. Abra miiserver.exe.config.
3. Vá para o nó de configuração/tempo de execução no fim do ficheiro.
4. Adicione o nó seguinte:`<enforceFIPSPolicy enabled="false"/>`
5. Guarde as alterações.

Para referência, este fragmento é que este deve ter o seguinte aspeto:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre segurança e FIPS, consulte [conformidade AAD Sync de palavra-passe, encriptação e FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-synchronization"></a>Resolver problemas de sincronização de palavra-passe
Se tiver problemas com a sincronização de palavra-passe, consulte [resolver problemas de sincronização de palavra-passe](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="next-steps"></a>Passos seguintes
* [Sincronização do Azure AD Connect: personalizar as opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
