---
title: "Azure Active Directory pass-through Authentication segurança descrição profunda | Microsoft Docs"
description: "Este artigo descreve como autenticação pass-through do Azure Active Directory (Azure AD) protege as contas no local."
services: active-directory
keywords: "Authentication do Azure AD Connect pass-through, a instalação do Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: a5feadd851b166d0a9a77bd1d124cdd599d5d701
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory pass-through Authentication segurança descrição profunda

Este artigo fornece uma descrição mais detalhada sobre como funciona a autenticação pass-through. Concentra-se mais nos aspetos de segurança da funcionalidade. Este tópico será de interesse para segurança e administradores de TI, segurança e de conformidade diretor officers e outros profissionais de TI responsáveis pela segurança de TI e de conformidade em organizações de pequenas a médias ou grandes empresas.

Tópicos abordados incluem:
- Informações detalhadas sobre como os agentes de autenticação são instalados e registados técnicas.
- Obter informações técnicas detalhadas sobre a encriptação de palavras-passe durante o início de sessão do utilizador.
- Segurança de canais no local agentes de autenticação e o Azure Active Directory (Azure AD).
- Informações detalhadas sobre como os agentes de autenticação são mantidos operacionalmente seguros técnicas.
- Outros tópicos relacionados com segurança.

## <a name="key-security-capabilities"></a>Capacidades de segurança de chave

Estes são os aspetos de segurança de chave desta funcionalidade:
- Baseia-se numa arquitetura tenanted de várias segura que fornece isolamento de pedidos de início de sessão entre inquilinos.
- Palavras-passe no local nunca são armazenadas na nuvem em qualquer outra forma.
- Agentes de autenticação no local, o que oiça e responder a pedidos de validação da palavra-passe, efetuar apenas ligações de saída a partir de dentro da sua rede. Não é necessário para instalar agentes nestes autenticação numa rede de perímetro (DMZ).
- Apenas portas não padrão (80 e 443) são utilizadas para comunicação de saída dos agentes de autenticação para o Azure AD. Nenhuma porta de entrada tem de estar abertas na firewall. 
  - Porta 443 é utilizada para todas as comunicações de saída autenticada
  - Porta 80 só é utilizada para transferir o certificado de listas de revogação (CRL) para garantir que nenhum dos certificados utilizados pela funcionalidade tiverem sido revogados.
  - Consulte [aqui](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites) para a lista completa de requisitos de rede.
- As palavras-passe fornecidas pelo utilizador durante o início de sessão são encriptadas na nuvem, antes de ser aceites no local agentes de autenticação para validação no seu Active Directory.
- O canal HTTPS entre o Azure AD e um agente de autenticação no local é protegido pela autenticação mútua.
- É perfeitamente integrado com as capacidades de proteção de nuvem do Azure AD, como as políticas de acesso condicional (incluindo o multi-factor Authentication), Identity Protection e bloqueio inteligente.

## <a name="components-involved"></a>Componentes envolvidos

Para obter mais informações gerais sobre serviço operacional, Azure AD e segurança dos dados, consulte o [Centro de confiança](https://azure.microsoft.com/support/trust-center/). Os seguintes componentes envolvidos quando a autenticação pass-through é utilizada para o início de sessão do utilizador:
- **Azure AD STS**: um sem monitorização de estado segurança Token serviço (STS) que processa os pedidos de início de sessão e emite tokens de segurança aos browsers dos utilizadores, os clientes ou serviços conforme necessário.
- **Service Bus do Azure**: fornece a capacidade de nuvem comunicação com mensagens empresariais e reencaminhamentos comunicação que o ajuda a estabelecer ligação soluções no local com a nuvem.
- **Agente do Azure AD Connect Authentication (autenticação Agent)**: um componente no local que escuta e responde a pedidos de validação da palavra-passe.
- **Base de dados SQL do Azure**: contém informações sobre os agentes de autenticação do inquilino, incluindo os respetivos metadados e chaves de encriptação.
- **Do Active Directory (AD)**: diretório Active Directory no local, onde estão armazenadas as contas de utilizador (e as palavras-passe).

## <a name="installation-and-registration-of-authentication-agents"></a>Instalação e o registo dos agentes de autenticação

Autenticação agentes estarem instalados e registados com o Azure AD quando é [ativar a autenticação pass-through com o Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature) ou quando a [adicionar agentes de autenticação adicionais para garantir a elevada disponibilidade do pedidos de início de sessão](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability). Obter um trabalho do agente de autenticação envolve três fases principais:

- Instalação do agente de autenticação
- Registo do agente de autenticação
- Inicialização do agente de autenticação

Cada um destes será abordada em detalhe nos seguintes tópicos.

### <a name="authentication-agent-installation"></a>Instalação do agente de autenticação

Apenas os administradores globais podem instalar um agente de autenticação (através do Azure AD Connect ou autónomo) num servidor no local. Instalação adiciona estas duas novas entradas para a **painel de controlo -> programas -> programas e funcionalidades** lista:
- A agente de autenticação própria aplicação. Isto é executado com [serviço de rede](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) privilégios.
- A aplicação do Atualizador utilizada para automática atualizar o agente de autenticação. Isto é executado com [Sistema Local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilégios.


### <a name="authentication-agent-registration"></a>Registo do agente de autenticação

Depois de instalar o agente de autenticação, tem de se registar com o Azure AD. Azure AD é através da atribuição de cada agente de autenticação de um certificado de identidade digital exclusiva que pode utilizar para comunicação segura com o Azure AD.

O procedimento de registo também vincula o agente de autenticação com o seu inquilino, para que o Azure AD sabe que este agente específico de autenticação é a única autorizada para processar pedidos de validação de palavra-passe para o seu inquilino. Este procedimento é repetido para cada novo agente de autenticação que registar.

Eis como agentes de autenticação registar-se com o Azure AD:

![Registo do agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD primeiro os pedidos de um Administrador Global para iniciar sessão Azure AD com as respetivas credenciais. Durante o início de sessão, o agente de autenticação adquire um acesso Token que pode utilizar em nome de Administrador Global.
2. O agente de autenticação, em seguida, gera um par de chaves – uma chave pública e uma chave privada.
    - Este par de chaves é gerado utilizando o padrão **RSA 2048 bits** encriptação.
    - A chave privada nunca sai do servidor no local em que foi instalado o agente de autenticação.
3.  O agente de autenticação faz um pedido de "registo" para o Azure AD através de HTTPS, com os seguintes componentes incluídos no pedido:
    - O acesso Token obteve no passo 1.
    - A chave pública gerada no passo 2.
    - A **solicitação de assinatura de certificado** (CSR ou pedido de certificado). Esta é a aplicar um certificado de identidade digital, com o Azure AD como a autoridade de certificação.
4. Azure AD valida o Token de acesso no pedido de registo e verifica que o pedido veio a partir de um Administrador Global.
5. Azure AD, em seguida, inicia e emite um certificado de identidade digital novamente para o agente de autenticação.
    - O certificado é assinado utilizando **autoridade de certificação de raiz do Azure AD (CA)**. Tenha em atenção que esta AC _não_ nas janelas do **autoridades de certificação de raiz fidedigna** armazenar.
    - Esta AC é utilizada apenas pela funcionalidade de autenticação pass-through. Apenas é utilizada para assinatura CSRs durante o registo do agente de autenticação.
    - Esta AC não é utilizada por qualquer outro serviço no Azure AD.
    - Assunto do certificado (**único nome ou DN**) está definido como o **ID do inquilino**. Este é um GUID que identifica exclusivamente o seu inquilino. Isto cria um âmbito o certificado para utilização com o seu inquilino apenas.
6. Azure AD armazena a chave pública do agente de autenticação numa base de dados SQL do Azure, que apenas do Azure AD tem acesso ao.
7. O certificado (emitido no passo 5) é armazenado no servidor no local no **arquivo de certificados do Windows** (especificamente no  **[CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)**  localização) e é utilizado pelo agente de autenticação e as aplicações do Atualizador.

### <a name="authentication-agent-initialization"></a>Inicialização do agente de autenticação

Quando o agente de autenticação é iniciado, pela primeira vez após o registo ou depois de um servidor reinicie, necessita de uma forma para comunicar com o serviço Azure AD e começar a aceitar pedidos de validação da palavra-passe com segurança.

![Inicialização do agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Eis como obter inicializados agentes de autenticação:



1. O agente de autenticação começa por efetuar um pedido de "arranque de configuração" saído para o Azure AD. 
    - Este pedido de arranque é efetuado através da porta 443 e é através de um canal HTTPS mutuamente autenticado (utilizando o mesmo certificado emitido durante o registo do agente de autenticação).
2. Azure AD responde a este pedido de arranque de configuração, fornecendo um **chave de acesso** para uma fila do Service Bus do Azure que seja exclusiva no seu inquilino (identificado pelo seu ID de inquilino).
3. O agente de autenticação estabelece uma ligação HTTPS saída persistente (através da porta 443) para a fila. 
    - Está agora pronto para obter e processar pedidos de validação da palavra-passe.

Se tiver múltiplos agentes de autenticação registada no seu inquilino, em seguida, o procedimento de inicialização assegura que cada um deles estabelece ligação à mesma fila do Service Bus do Azure. 

## <a name="processing-sign-in-requests"></a>Processamento de pedidos de início de sessão 

O diagrama abaixo mostra como a autenticação pass-through processa pedidos de início de sessão do utilizador.

![Processamento de início de sessão](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

A autenticação pass-through processa um pedido de início de sessão do utilizador da seguinte forma: 

1. Um utilizador tenta aceder a uma aplicação (por exemplo, o Outlook Web App - [https://outlook.office365.com/owa](https://outlook.office365.com/owa).)
2. Se o utilizador não está já iniciou sessão, a aplicação redireciona o browser para a página de início de sessão do Azure AD.
3. O serviço do Azure AD STS responde novamente com a página de início de sessão do utilizador.
4. O utilizador introduz o respetivo nome de utilizador e palavra-passe para a página de início de sessão do Azure AD e clica no botão "Iniciar sessão".
5. O nome de utilizador e palavra-passe são submetidas para o Azure AD STS num pedido POST de HTTPS.
6. Azure AD STS obtém as chaves públicas para todos os agentes de autenticação registada no seu inquilino da base de dados SQL do Azure e encripta a palavra-passe utilizá-los. 
    - Produz palavra-passe encriptada 'N' valores para os agentes de autenticação 'N' registada no seu inquilino.
7. Azure AD STS coloca o pedido de validação de palavra-passe (o nome de utilizador e os valores de palavra-passe encriptada) para a fila do Service Bus do Azure específico do seu inquilino.
8. Uma vez que os agentes de autenticação inicializado estão ligados forma permanente para a fila do Service Bus do Azure, um dos agentes de autenticação disponíveis obtém o pedido de validação da palavra-passe.
9. O agente de autenticação localiza o valor de palavra-passe encriptada que é específico para a chave pública (utilizando um identificador) e desencripta a mesma utilizando a respetiva chave privada.
10. O agente de autenticação tenta validar o nome de utilizador e a palavra-passe em relação a sua do Active Directory no local ao utilizar o  **[Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)**  (com o **dwLogonType** parâmetro definido como **LOGON32_LOGON_NETWORK**). 
    - Esta é a mesma API utilizada pelos serviços de Federação do Active Directory (AD FS), a sessão de utilizadores num cenário de início de sessão Federated.
    - Isto baseia-se no processo de resolução padrão no Windows Server para localizar o Contoller de domínio.
11. O agente de autenticação recebe o resultado do Active Directory (êxito, nome de utilizador ou palavra-passe incorreta, a palavra-passe expirada, o utilizador está bloqueado terminar, e assim sucessivamente).
12. O agente de autenticação reencaminha o resultado para o STS do Azure AD através de um canal HTTPS mutuamente autenticado saído através da porta 443. A autenticação mútua utiliza o mesmo certificado emitido anteriormente para o agente de autenticação durante o registo.
13. Azure AD STS verifica que o resultado deste está correlacionada com o pedido específico início de sessão no seu inquilino.
14. Azure AD STS continua com o procedimento de início de sessão como configurado. Por exemplo, se a validação da palavra-passe foi concluída com êxito, o utilizador foi solicitado para multi-factor Authentication ou redirecionado novamente para a aplicação.

## <a name="operational-security-of-authentication-agents"></a>Segurança operacional dos agentes de autenticação

Para garantir que a autenticação pass-through permanece operacionalmente segura, do Azure AD periodicamente renova os respetivos certificados. Estes renovações são acionadas a partir do Azure AD e não são regidas pelos agentes de autenticação próprios.

![Segurança operacional](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Eis como um agente de autenticação é renovada a fidedignidade com o Azure AD:

1. O agente de autenticação periodicamente efetua o ping do Azure AD (em poucas horas) para verificar se está na altura de renovar o certificado. 
    - Esta verificação é feita através de um canal HTTPS mutuamente autenticado (utilizando o mesmo certificado emitido durante o registo).
2. Se o serviço indica que está na altura de renovar, o agente de autenticação gera um novo par de chaves: uma chave pública e uma chave privada.
    - Estas chaves são geradas com a norma **RSA 2048 bits** encriptação.
    - A chave privada nunca sai do servidor no local.
3. O agente de autenticação, em seguida, envia um pedido de "renovação do certificado" para o Azure AD através de HTTPS, com os seguintes componentes incluídos no pedido:
    - O certificado existente (obtida **CERT_SYSTEM_STORE_LOCAL_MACHINE** localização no arquivo de certificados do Windows). Não há nenhum Administrador Global envolvidos neste procedimento, pelo que não existe nenhum Token acesso necessário em nome de Administrador Global.
    - A chave pública gerada no passo 2.
    - A **solicitação de assinatura de certificado** (CSR ou pedido de certificado). Isto é para se candidatar a um novo certificado de identidade digital, com o Azure AD como a autoridade de certificação.
4. Azure AD valida o certificado existente no pedido de renovação de certificado. Em seguida, verifica se o pedido veio de um agente de autenticação registada no seu inquilino.
5. Se o certificado existente ainda é válido, do Azure AD, em seguida, inicia um novo certificado de identidade digital e emite o novo certificado para o agente de autenticação. 
6. Se o certificado existente tiver expirado, do Azure AD elimina o agente de autenticação da lista do seu inquilino de registado de autenticação de agentes. Em seguida, um Administrador Global tem de instalar manualmente e registar um novo agente de autenticação.
    - O certificado é assinado utilizando **autoridade de certificação de raiz do Azure AD (CA)**.
    - Assunto do certificado (**único nome ou DN**) está definido como o **ID do inquilino** um GUID que identifica exclusivamente o seu inquilino. Ou seja, o certificado tem um âmbito no seu inquilino apenas.
6. Azure AD armazena a chave pública "nova" do agente de autenticação numa base de dados SQL do Azure que apenas tem acesso a. E invalida a chave pública "antiga" associada com o agente de autenticação.
7. O novo certificado (o que foi emitido no passo 5), em seguida, é armazenado no servidor no **arquivo de certificados do Windows** (especificamente no  **[CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)**  localização).
    - Uma vez que o procedimento de renovação de confiança acontece de forma não interativa (sem a presença de um Administrador Global), o agente de autenticação já não tem acesso para atualizar o certificado existente no **CERT_SYSTEM_STORE_LOCAL_MACHINE** localização. Tenha em atenção que o certificado propriamente dito no **CERT_SYSTEM_STORE_LOCAL_MACHINE** localização não é removida durante este procedimento.
8. O novo certificado é utilizado para autenticação a partir deste ponto no. Cada subsequente renovação do certificado substitui o certificado no **CERT_SYSTEM_STORE_LOCAL_MACHINE** localização.

## <a name="auto-update-of-authentication-agents"></a>Atualização automática de agentes de autenticação

A aplicação do Atualizador é atualizada automaticamente o agente de autenticação quando for lançada uma nova versão. Não processa os pedidos de validação da palavra-passe para o seu inquilino. 

Azure AD aloja a nova versão do software como um assinado **pacote do Windows Installer (MSI)**. O MSI é assinado utilizando [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) com **SHA256** como o algoritmo de texto implícita. 

![Atualização automática](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Eis como um agente de autenticação obtém atualizados automaticamente:

1. O Atualizador periodicamente efetua o ping do Azure AD (a cada hora) verifique se existe uma nova versão do agente de autenticação disponíveis. 
    - Esta verificação é feita através de um canal HTTPS mutuamente autenticado (utilizando o mesmo certificado emitido durante o registo). O agente de autenticação e do Atualizador partilham o certificado armazenado no servidor.
2. Se estiver disponível uma nova versão, do Azure AD devolve o MSI assinado novamente para o Atualizador.
3. O Atualizador verifica se o MSI está assinado pela Microsoft.
4. O Atualizador é executado o MSI. Esta ação os seguintes passos (tenha em atenção que é executado o Atualizador com [Sistema Local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilégios):
    - Para o serviço de agente de autenticação.
    - Instala a nova versão do agente de autenticação no servidor.
    - Reinicia o serviço de agente de autenticação.

>[!NOTE]
>Se tiver múltiplos agentes de autenticação registada no seu inquilino, o Azure AD não renovar os respetivos certificados ou atualizá-las ao mesmo tempo. Em vez disso, do Azure AD não gradualmente, por isso, certifique-se de elevada disponibilidade dos pedidos de início de sessão.


## <a name="next-steps"></a>Passos seguintes
- [**Limitações atuais** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) -saber quais os cenários que são suportados e aqueles que não são.
- [**Início Rápido** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - obter cópias de segurança e executar a autenticação pass-through do Azure AD.
- [**Bloqueio do smart** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -capacidade de configurar o bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [**Como funciona** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -aprender as noções básicas como funciona a autenticação pass-through do Azure AD.
- [**Perguntas mais frequentes** ](active-directory-aadconnect-pass-through-authentication-faq.md) -respostas para as perguntas mais frequentes.
- [**Resolver problemas** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**Azure SSO totalmente integrada de AD** ](active-directory-aadconnect-sso.md) -Saiba mais sobre esta funcionalidade complementares.

