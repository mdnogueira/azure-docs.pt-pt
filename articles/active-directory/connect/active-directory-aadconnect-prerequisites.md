---
title: "O Azure AD Connect: Hardware e pré-requisitos | Microsoft Docs"
description: "Este tópico descreve os pré-requisitos e os requisitos de hardware para o Azure AD Connect"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e09017cbd6c4060ea24bb17c751277b4f4c6daf8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prerequisites-for-azure-ad-connect"></a>Pré-requisitos para o Azure AD Connect
Este tópico descreve os pré-requisitos e os requisitos de hardware para o Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar o Azure AD Connect
Antes de instalar o Azure AD Connect, existem algumas coisas que precisa.

### <a name="azure-ad"></a>Azure AD
* Uma subscrição do Azure ou um [subscrição de avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/). Esta subscrição só é necessário para aceder ao portal do Azure e não para utilizar o Azure AD Connect. Se estiver a utilizar o PowerShell ou o Office 365, em seguida, não terá uma subscrição do Azure para utilizar o Azure AD Connect. Se tiver uma licença do Office 365, em seguida, pode também utilizar o portal do Office 365. Com uma licença paga do Office 365, também pode obter no portal do Azure no portal do Office 365.
  * Também pode utilizar o [portal do Azure](https://portal.azure.com). Este portal não necessita de uma licença do Azure AD.
* [Adicionar e verificar o domínio](../active-directory-domains-add-azure-portal.md) que pretende utilizar no Azure AD. Por exemplo, se planear utilizar contoso.com para os seus utilizadores, em seguida, certifique-se de que este domínio ter sido verificado e não apenas a utilizar o domínio predefinido de contoso.onmicrosoft.com.
* Permite a um inquilino do Azure AD por objetos predefinidos 50k. Ao verificar o seu domínio, o limite é aumentado para objetos do 300 k. Se precisar de mais mesmo objetos no Azure AD, terá de abrir um incidente de suporte para que o limite de aumentar ainda mais. Se precisar de mais do que 500 objetos de k, em seguida, é necessário uma licença, tais como o Office 365, Azure AD Basic, Azure AD Premium, ou Enterprise Mobility e segurança.

### <a name="prepare-your-on-premises-data"></a>Prepare os seus dados no local
* Utilize [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) para identificar erros, tais como os duplicados e formatação problemas no seu diretório antes de sincronizar com o Azure AD e o Office 365.
* Reveja [funcionalidades opcionais de sincronização, pode ativar no Azure AD](active-directory-aadconnectsyncservice-features.md) e avaliar as funcionalidades que deve ativar.

### <a name="on-premises-active-directory"></a>Active Directory no local
* O AD versão e a floresta funcional nível de esquema tem de ser Windows Server 2003 ou posterior. Os controladores de domínio podem executar qualquer versão, desde que são cumpridos os requisitos de nível de esquema e de floresta.
* Se planeia utilizar a funcionalidade **repetição de escrita de palavras-passe**, em seguida, os controladores de domínio tem de ser no Windows Server 2008 (com SP mais recente) ou posterior. Se os DCs estão no 2008 (pré-R2), em seguida, tem de aplicar também [correção KB2386717](http://support.microsoft.com/kb/2386717).
* O controlador de domínio utilizado pelo Azure AD tem de ser gravável. É **não suportado** para utilizar um RODC (controlador de domínio só de leitura) e o Azure AD Connect não seguir qualquer redirecionamentos de escrita.
* É **não suportado** para utilizar no local florestas/domínios utilizando SLDs (domínios de etiqueta única).
* É **não suportado** para utilizar no local florestas/domínios utilizando "separada por pontos" (nome contém um período de ".") Nomes de NetBios.
* É recomendado [ativar a Reciclagem do Active Directory](active-directory-aadconnectsync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Servidor do Azure AD Connect
* O Azure AD Connect não pode ser instalado num Small Business Server ou o Windows Server Essentials. O servidor deve ser a utilizar o Windows Server padrão ou melhor.
* O servidor do Azure AD Connect tem de ter uma GUI completa instalada. É **não suportado** para instalar no server core.
* O Azure AD Connect tem de estar instalado no Windows Server 2008 ou posterior. Este servidor pode ser um controlador de domínio ou um servidor membro quando utilizar definições expressas. Se utilizar definições personalizadas, o servidor pode também ser autónomo e não tem de estar associado a um domínio.
* Se instalar o Azure AD Connect no Windows Server 2008 ou Windows Server 2008 R2, em seguida, certifique-se aplicar as correções mais recentes do Windows Update. A instalação não é capaz de começar com um servidor tratam.
* Se planeia utilizar a funcionalidade **sincronização de palavra-passe**, em seguida, o servidor do Azure AD Connect tem de ser no Windows Server 2008 R2 SP1 ou posterior.
* Se planeia utilizar um **conta de serviço gerida de grupo**, em seguida, o servidor do Azure AD Connect tem de ser no Windows Server 2012 ou posterior.
* O servidor do Azure AD Connect tem de ter [.NET Framework 4.5.1](#component-prerequisites) ou posterior e [Microsoft PowerShell 3.0](#component-prerequisites) ou posterior instalado.
* O servidor do Azure AD Connect não pode ter a política de grupo do PowerShell Transcription ativada.
* Se os serviços de Federação do Active Directory está a ser implementado, os servidores onde o AD FS ou o Proxy de aplicações Web são instaladas tem de ser Windows Server 2012 R2 ou posterior. [Gestão remota do Windows](#windows-remote-management) tem de estar ativada nestes servidores para instalação remota.
* Se os serviços de Federação do Active Directory está a ser implementado, é necessário [certificados SSL](#ssl-certificate-requirements).
* Se os serviços de Federação do Active Directory está a ser implementado, em seguida, terá de configurar [resolução de nomes](#name-resolution-for-federation-servers).
* Se tem os administradores globais MFA ativada, em seguida, o URL **https://secure.aadcdn.microsoftonline-p.com** tem de estar na lista de sites fidedignos. Lhe for pedido para adicionar este site à lista de sites fidedignos, quando lhe for pedido para um desafio MFA e não tenha sido adicionado antes. Pode utilizar o Internet Explorer para o adicionar aos sites fidedignos.

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server utilizada pelo Azure AD Connect
* O Azure AD Connect necessita de uma base de dados do SQL Server para armazenar dados de identidade. Por predefinição, é instalado um SQL Server 2012 Express LocalDB (versão leve do SQL Server Express). SQL Server Express tem um limite de tamanho de 10GB que lhe permite gerir aproximadamente 100 000 objetos. Se precisar de gerir um volume maior de objetos de diretório, terá do Assistente de instalação de apontar para uma instalação diferente do SQL Server.
* Se utilizar um servidor separado do SQL Server, aplicam-se estes requisitos:
  * Azure AD Connect suporta todos os tipos do Microsoft SQL Server do SQL Server 2008 (com Service Pack mais recente) ao SQL Server 2016 SP1. Base de dados do Microsoft Azure SQL é **não suportado** como uma base de dados.
  * Tem de utilizar um agrupamento do SQL Server sensível. Estes agrupamentos são identificados com um \_CI_ no respetivo nome. É **não suportado** para utilizar um agrupamento de maiúsculas e minúsculas, identificado por \_CS_ no respetivo nome.
  * Só pode ter um motor de sincronização por instância de SQL. É **não suportado** para partilhar uma instância do SQL Server com a sincronização do FIM/MIM, o DirSync ou o Azure AD Sync.

### <a name="accounts"></a>Contas
* Uma conta de Administrador Global do AD do Azure para o inquilino do Azure AD que pretende integrar. Esta conta tem de ser um **conta profissional ou organização** e não pode ser um **conta Microsoft**.
* Se utilizar definições expressas ou atualizar do DirSync, em seguida, tem de ter uma conta de administrador de empresa do seu Active Directory local.
* [Contas no Active Directory](active-directory-aadconnect-accounts-permissions.md) se utilizar o caminho de instalação de definições personalizadas.

### <a name="connectivity"></a>Conectividade
* O servidor do Azure AD Connect precisa de resolução de DNS de intranet e internet. O servidor DNS tem de ser capaz de resolver os nomes de diretório Active Directory no local e os pontos finais do Azure AD.
* Se tiver firewalls na sua Intranet e terá de abrir portas entre os servidores do Azure AD Connect e os controladores de domínio, em seguida, consulte [as de portas do Azure AD Connect](active-directory-aadconnect-ports.md) para obter mais informações.
* Se o proxy ou firewall limitar os URLs podem ser acedidos, em seguida, os URLs documentados em [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) tem de ser aberto.
  * Se estiver a utilizar o Microsoft Cloud na Alemanha ou a nuvem do Microsoft Azure Government, em seguida, consulte o artigo [considerações de instâncias de serviço de sincronização do Azure AD Connect](active-directory-aadconnect-instances.md) para URLs.
* O Azure AD Connect (versão 1.1.614.0 e após) por predefinição, utiliza TLS 1.2 para encriptar a comunicação entre o motor de sincronização e o Azure AD. Se TLS 1.2 não está disponível no sistema operativo subjacente, o Azure AD Connect incrementalmente retrocede para protocolos mais antigos (TLS 1.1 e TLS 1.0). Por exemplo, o Azure AD Connect, em execução no Windows Server 2008 utiliza TLS 1.0, porque o Windows Server 2008 não suporta o TLS 1.1 ou TLS 1.2.
* Antes da versão 1.1.614.0, Azure AD Connect, por predefinição, utiliza TLS 1.0 para encriptar a comunicação entre o motor de sincronização e o Azure AD. Para alterar para TLS 1.2, siga os passos no [ativar o TLS 1.2 do Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Se estiver a utilizar um proxy de saída para ligar à Internet, a seguinte definição no **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** ficheiro tem de ser adicionado para o Assistente de instalação e o Azure AD Ligar a sincronização para conseguirem estabelecer ligação à Internet e do Azure AD. Este texto têm de ser introduzido na parte inferior do ficheiro. Neste código, &lt;PROXYADRESS&gt; representa o nome de anfitrião ou endereço IP do proxy real.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Se o servidor proxy requer autenticação, em seguida, a [conta de serviço](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) tem de estar localizado no domínio e tem de utilizar o caminho de instalação de definições personalizadas para especificar um [conta de serviço personalizado](active-directory-aadconnect-get-started-custom.md#install-required-components). Também precisa de uma alteração diferentes Machine. config. Com esta alteração em Machine. config, o motor do assistente e sincronização de instalação responder a pedidos de autenticação do servidor proxy. Em todas as páginas do Assistente de instalação, excluindo o **configurar** página assinada no utilizador credenciais são utilizadas. No **configurar** página no final do Assistente de instalação, o contexto é mudada para o [conta de serviço](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) que foi criado por si. A secção de Machine. config deve ter este aspeto.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Quando o Azure AD Connect envia um pedido web para o Azure AD como parte da sincronização de diretórios, do Azure AD pode demorar até 5 minutos a responder. É comum para servidores de proxy para que a configuração de tempo limite de inatividade da ligação. Certifique-se de que a configuração é definida para, pelo menos, 6 minutos ou mais.

Para obter mais informações, consulte a MSDN o [predefinido proxy elemento](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Para obter mais informações consulte Quando tiver problemas com a conectividade, [resolver problemas de conectividade](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Outros
* Opcional: Uma teste de conta de utilizador para verificar a sincronização.

## <a name="component-prerequisites"></a>Pré-requisitos do componente
### <a name="powershell-and-net-framework"></a>PowerShell e .net Framework
O Azure AD Connect depende Microsoft PowerShell e .NET Framework 4.5.1. É necessário esta versão ou uma versão posterior instalada no seu servidor. Dependendo da versão do Windows Server, efetue o seguinte:

* Windows Server 2012R2
  * Microsoft PowerShell está instalado por predefinição. É necessária nenhuma ação.
  * .NET framework 4.5.1 e versões posteriores são disponibilizadas através do Windows Update. Certifique-se de que instalou as atualizações mais recentes para o Windows Server no painel de controlo.
* Windows Server 2008 R2 e Windows Server 2012
  * A versão mais recente do PowerShell da Microsoft está disponível no **Windows Management Framework 4.0**, disponível no [Microsoft Download Center](http://www.microsoft.com/downloads).
  * Estão disponíveis no .NET framework 4.5.1 e versões posteriores [Microsoft Download Center](http://www.microsoft.com/downloads).
* Windows Server 2008
  * A versão mais recente suportada do PowerShell está disponível no **Windows Management Framework 3.0**, disponível no [Microsoft Download Center](http://www.microsoft.com/downloads).
  * Estão disponíveis no .NET framework 4.5.1 e versões posteriores [Microsoft Download Center](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Ativar o TLS 1.2 do Azure AD Connect
Antes da versão 1.1.614.0, Azure AD Connect, por predefinição, utiliza TLS 1.0 para encriptar a comunicação entre o servidor do motor de sincronização e o Azure AD. Pode alterar esta configuração de aplicações .net para utilizar TLS 1.2 por predefinição no servidor. Podem encontrar mais informações sobre TLS 1.2 no [Consultivo de alterações de segurança da Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Não é possível ativar o TLS 1.2 no Windows Server 2008. É necessário o Windows Server 2008-R2 ou posterior. Certifique-se de que tem a correção de .net 4.5.1 instalada para o sistema operativo, consulte [Consultivo de alterações de segurança da Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Poderá ter esta correção ou uma versão posterior já instalado no seu servidor.
2. Se utilizar o Windows Server 2008-R2, em seguida, certifique-se de que TLS 1.2 está ativada. No servidor do Windows Server 2012 e versões posteriores, TLS 1.2 já deve estar ativada.
   ```
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   ```
3. Para todos os sistemas operativos, definir esta chave de registo e reinicie o servidor.
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
   "SchUseStrongCrypto"=dword:00000001
   ```
4. Se também pretender ativar o TLS 1.2 entre o servidor do motor de sincronização e SQL Server remoto, em seguida, certifique-se de que dispõe das versões de necessárias instaladas para [TLS 1.2 suporte para Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Pré-requisitos de configuração e instalação de Federação
### <a name="windows-remote-management"></a>Gestão remota do Windows
Quando utilizar o Azure AD Connect para implementar serviços de Federação do Active Directory ou o Proxy de aplicações Web, consulte estes requisitos:

* Se o servidor de destino é associado a um domínio, em seguida, certifique-se de que geridos remota do Windows está ativado
  * Numa janela elevada do comando PSH, utilize o comando`Enable-PSRemoting –force`
* Se o servidor de destino for um não-associado a um domínio máquina WAP, em seguida, existem alguns requisitos adicionais
  * No computador de destino (máquina WAP):
    * Certifique-se o winrm (Windows Remote Management / WS-Management) está a executar o serviço através do snap-in Serviços
    * Numa janela elevada do comando PSH, utilize o comando`Enable-PSRemoting –force`
  * No computador no qual o assistente está em execução (se a máquina de destino sem domínio não fidedigno ou associados a um domínio):
    * Numa janela elevada do comando PSH, utilize o comando`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * No Gestor de servidor:
      * Adicionar anfitrião de rede de Perímetro WAP ao agrupamento de máquina (Gestor de servidor -> Gerir -> adicionar servidores... Utilize o separador DNS)
      * Separador Gestor de servidor de todos os servidores: clique com o botão direito do rato em servidor do WAP e escolha gerir executados..., introduza credenciais local (e não do domínio) para a máquina WAP
      * Para validar a conetividade PSH remota, no separador de Gestor de servidor de todos os servidores: clique com o botão direito do rato em servidor do WAP e escolha do Windows PowerShell. Deve abrir uma sessão remota do PSH para garantir sessões remotas do PowerShell podem ser estabelecidas.

### <a name="ssl-certificate-requirements"></a>Requisitos de certificado SSL
* É vivamente recomendado que para utilizar o mesmo certificado SSL em todos os nós do farm do AD FS e todos os servidores de proxy de aplicação Web.
* O certificado tem de ser um X509 certificado.
* Pode utilizar um certificado autoassinado em servidores de Federação num ambiente de laboratório de teste. No entanto, num ambiente de produção, recomendamos que obtenha o certificado de uma AC pública.
  * Se utilizar um certificado que não é fidedigno publicamente, certifique-se de que o certificado instalado em cada servidor de Proxy de aplicações Web é fidedigno no servidor local e em todos os servidores de Federação
* A identidade do certificado deve corresponder ao nome de serviço de Federação (por exemplo, sts.contoso.com).
  * A identidade é a uma extensão de nome alternativo (SAN) de assunto do tipo dNSName especificados, ou, se não existem SAN entradas, o nome do requerente como nome comum.  
  * Várias entradas de SAN podem ser presentes no certificado, fornecida uma delas corresponde ao nome do serviço de Federação.
  * Se estiver a planear utilizar a associação à área de trabalho, uma SAN adicional é necessária com o valor **enterpriseregistration.** seguido do sufixo de nome Principal de utilizador (UPN) da sua organização, por exemplo, **enterpriseregistration.contoso.com**.
* Certificados com base no CryptoAPI as chaves de próximas geração (CNG) e os fornecedores de armazenamento de chaves não são suportados. Isto significa que tem de utilizar um certificado com base num CSP (fornecedor de serviços de criptografia) e não um KSP (fornecedor de armazenamento de chaves).
* São suportados certificados de caráter universal.

### <a name="name-resolution-for-federation-servers"></a>Resolução de nomes para os servidores de Federação
* Configure os registos DNS para o nome de serviço de Federação (por exemplo, sts.contoso.com) de AD FS (o servidor DNS interno) da intranet e extranet (DNS público através da sua entidade de registo do domínio). Para o registo DNS da intranet, certifique-se de que usa A registos e não os registos CNAME. Isto é necessário para autenticação do windows funcionar corretamente a partir do seu computador associado a um domínio.
* Se estiver a implementar mais do que um servidor do AD FS ou servidor de Proxy de aplicações Web, em seguida, certifique-se de que configurou o seu Balanceador de carga e que os registos DNS para o nome de serviço de Federação do AD FS (por exemplo, sts.contoso.com) apontam para o Balanceador de carga.
* Para a autenticação integrada do windows funcionar para aplicações de browser utilizando o Internet Explorer na sua intranet, certifique-se de que o nome (por exemplo, sts.contoso.com) do serviço de Federação do AD FS é adicionado à zona da intranet no IE. Isto pode ser controlado através da política de grupo e implementado em todos os seus computadores associados a um domínio.

## <a name="azure-ad-connect-supporting-components"></a>O Azure AD Connect, os componentes de suporte
Segue-se uma lista de componentes do Azure AD Connect instala no servidor onde está instalado o Azure AD Connect. Esta lista é para uma instalação rápida básica. Se optar por utilizar um SQL Server diferente na página de serviços de sincronização de instalação, em seguida, SQL Server Express LocalDB não está instalado localmente.

* Azure AD Connect Health
* Assistente Microsoft Online Services início de sessão para profissionais de TI (instalada, mas não existem dependências no mesmo)
* Utilitários de linha de comandos do Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Pacote de redistribuição do Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisitos de hardware para o Azure AD Connect
A tabela abaixo mostra os requisitos mínimos para o computador de sincronização do Azure AD Connect.

| Número de objetos no Active Directory | CPU | Memória | Tamanho do disco rígido |
| --- | --- | --- | --- |
| Menos de 10 000 |1.6 GHz |4GB |70 GB |
| 10,000–50,000 |1.6 GHz |4GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| Para 100 000 ou mais objetos, a versão completa do SQL Server é necessária | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| Mais de 600 000 |1.6 GHz |32 GB |500 GB |

Os requisitos mínimos para computadores com o AD FS ou servidores de aplicações Web é o seguinte:

* CPU: Dual core com 1.6 GHz ou superior
* MEMÓRIA: 2 GB ou superior
* VM do Azure: Configuração A2 ou superior

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
