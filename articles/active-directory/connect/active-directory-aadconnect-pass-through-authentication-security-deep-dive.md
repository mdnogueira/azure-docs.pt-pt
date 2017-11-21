---
title: "Azure Active Directory pass-through Authentication segurança detalhada | Microsoft Docs"
description: "Este artigo descreve como autenticação de pass-through do Azure Active Directory (Azure AD) protege as contas no local"
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
ms.openlocfilehash: 5d2c581b25d34ea1b83f54292b5b65b2286e5a9d
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory pass-through Authentication segurança detalhada

Este artigo fornece uma descrição mais detalhada como funciona o Azure Active Directory (Azure AD) a autenticação pass-through. Concentra-se nos aspetos de segurança da funcionalidade. Este artigo é para segurança e administradores de TI, compatibilidade diretor e officers de segurança e outros profissionais de TI responsáveis pela segurança de TI e de conformidade em pequena a média dimensionadas organizações ou empresas de grandes dimensão.

Os tópicos abordados incluem:
- Técnicas informações detalhadas sobre como instalar e registar os agentes de autenticação.
- Obter informações técnicas detalhadas sobre a encriptação de palavras-passe durante o início de sessão do utilizador.
- A segurança dos canais no local agentes de autenticação e o Azure AD.
- Informações detalhadas sobre como manter os agentes de autenticação operacionalmente segura técnicas.
- Outros tópicos relacionados com segurança.

## <a name="key-security-capabilities"></a>Capacidades de segurança de chave

Estes são os aspetos de segurança de chave desta funcionalidade:
- Baseia-se numa arquitetura tenanted de várias segura que fornece isolamento de pedidos de início de sessão entre inquilinos.
- Palavras-passe no local nunca são armazenadas na nuvem em qualquer outra forma.
- Agentes de autenticação no local que oiça e responder a pedidos de validação da palavra-passe só fazem ligações de saída a partir de dentro da sua rede. Não é necessário para instalar agentes nestes autenticação numa rede de perímetro (DMZ).
- Apenas portas não padrão (80 e 443) são utilizadas para comunicação de saída dos agentes de autenticação para o Azure AD. Não precisa abrir portas de entrada na sua firewall. 
  - Porta 443 é utilizada para todas as comunicações de saída autenticada.
  - Porta 80 é utilizada apenas para transferir o certificado de listas de revogação (CRL) para se certificar de que nenhuma dos certificados utilizados por esta funcionalidade foram revogadas.
  - Para obter a lista completa dos requisitos de rede, consulte [autenticação pass-through do Active Directory do Azure: início rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites).
- Palavras-passe que os utilizadores forneçam durante o início de sessão são encriptadas na nuvem, antes dos agentes de autenticação no local aceitar para validação no Active Directory.
- O canal HTTPS entre o Azure AD e o agente de autenticação no local é protegido pela utilização de autenticação mútua.
- A funcionalidade de forma totalmente integrada integra-se com capacidades de proteção da nuvem do Azure AD, como as políticas de acesso condicional (incluindo o multi-factor Authentication do Azure), a proteção de identidade e bloqueio inteligente.

## <a name="components-involved"></a>Componentes envolvidos

Para obter mais informações gerais sobre serviço operacional, Azure AD e a segurança de dados, consulte o [Centro de confiança](https://azure.microsoft.com/support/trust-center/). Os seguintes componentes envolvidos quando utiliza a autenticação pass-through para a sessão do utilizador:
- **Azure AD STS**: um serviço de token sem monitorização de estado de segurança (STS), que processa os pedidos de início de sessão e emite tokens de segurança aos browsers dos utilizadores, os clientes ou serviços conforme necessário.
- **Service Bus do Azure**: fornece a capacidade de nuvem comunicação com mensagens empresariais e reencaminhamentos comunicação que o ajuda a estabelecer ligação soluções no local com a nuvem.
- **Agente do Azure AD Connect autenticação**: um componente no local que escuta e responde a pedidos de validação da palavra-passe.
- **Base de dados SQL do Azure**: contém informações sobre os agentes de autenticação do inquilino, incluindo as chaves de encriptação e de metadados.
- **Do Active Directory**: do Active Directory no local, onde estão armazenadas as contas de utilizador e as palavras-passe.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalação e o registo dos agentes de autenticação

Autenticação agentes estarem instalados e registados com o Azure AD quando lhe é:
   - [Ativar a autenticação pass-through através do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Adicionar mais agentes de autenticação para garantir a disponibilidade elevada de pedidos de início de sessão](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Obter um trabalho do agente de autenticação envolve três fases principais:

1. Instalação do agente de autenticação
2. Registo do agente de autenticação
3. Inicialização do agente de autenticação

As secções seguintes abordam estas fases em detalhe.

### <a name="authentication-agent-installation"></a>Instalação do agente de autenticação

Apenas os administradores globais podem instalar um agente de autenticação (através da utilização do Azure AD Connect ou autónomo) num servidor no local. Instalação adiciona duas novas entradas para a **painel de controlo** > **programas** > **programas e funcionalidades** lista:
- A agente de autenticação própria aplicação. Esta aplicação é executada com [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) privilégios.
- A aplicação do Atualizador que é utilizada para o agente de autenticação de atualização automática. Esta aplicação é executada com [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilégios.

### <a name="authentication-agent-registration"></a>Registo do agente de autenticação

Depois de instalar o agente de autenticação, tem de se registar com o Azure AD. Azure AD atribui cada agente de autenticação de um certificado exclusivo, a identidade digital que pode utilizar para comunicação segura com o Azure AD.

O procedimento de registo também vincula o agente de autenticação com o seu inquilino. Isto garante que o Azure AD sabe que este agente específico de autenticação é a única autorizada para processar pedidos de validação de palavra-passe para o seu inquilino. Este procedimento é repetido para cada novo agente de autenticação que registar.

Os agentes de autenticação utilize os seguintes passos para registar-se com o Azure AD:

![Registo do agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD pedidos pela primeira vez que um administrador global iniciar sessão Azure AD com as respetivas credenciais. Durante o início de sessão, o agente de autenticação adquire um token de acesso que pode utilizar em nome de administrador global.
2. O agente de autenticação, em seguida, gera um par de chaves: uma chave pública e uma chave privada.
    - O par de chaves é gerado através de encriptação de RSA 2048 bits padrão.
    - A chave privada permanece no servidor no local onde reside o agente de autenticação.
3. O agente de autenticação faz um pedido de "registo" para o Azure AD através de HTTPS, com os seguintes componentes incluídos no pedido:
    - O token de acesso que obteve no passo 1.
    - A chave pública gerada no passo 2.
    - Um certificado de assinatura de pedido (CSR ou pedido de certificado). Este pedido aplica-se um certificado de identidade digital, com o Azure AD como a autoridade de certificação (AC).
4. Azure AD valida o token de acesso no pedido de registo e verifica que o pedido veio a partir de um administrador global.
5. Azure AD, em seguida, inicia e envia um certificado de identidade digital novamente para o agente de autenticação.
    - A AC de raiz no Azure AD é utilizado para assinar o certificado. 

     >[!NOTE]
     > Esta AC é _não_ nas autoridades de certificação de raiz fidedigna Windows armazenar.
    - A AC é utilizada apenas pela funcionalidade de autenticação pass-through. A AC é utilizada apenas para assinar CSRs durante o registo do agente de autenticação.
    -  Nenhum dos outros serviços do Azure AD utilizar esta AC.
    - Assunto do certificado (nome único ou DN) está definido para o ID do inquilino. Este DN é um GUID que identifica exclusivamente o seu inquilino. Este DN âmbitos o certificado para utilização apenas com o seu inquilino.
6. Azure AD armazena a chave pública do agente de autenticação numa base de dados SQL do Azure, que apenas do Azure AD tem acesso ao.
7. O certificado (emitido no passo 5) é armazenado no servidor no local no arquivo de certificados do Windows (especificamente no [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) localização). É utilizado pelo agente de autenticação e as aplicações do Atualizador.

### <a name="authentication-agent-initialization"></a>Inicialização do agente de autenticação

Quando o agente de autenticação é iniciado, pela primeira vez após o registo ou depois de um servidor reinicie, necessita de uma forma para comunicar de forma segura com o serviço do Azure AD e começar a aceitar pedidos de validação da palavra-passe.

![Inicialização do agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Eis como agentes de autenticação são inicializados:

1. O agente de autenticação faz um pedido de arranque do sistema de saída para o Azure AD. 
    - Este pedido é efetuado através da porta 443 e é através de um canal HTTPS mutuamente autenticado. O pedido utiliza o mesmo certificado que foi emitido durante o registo do agente de autenticação.
2. Azure AD responde ao pedido ao fornecer uma chave de acesso a uma fila do Service Bus do Azure que seja exclusivo no seu inquilino e que é identificado pelo seu ID de inquilino.
3. O agente de autenticação estabelece uma ligação HTTPS saída persistente (através da porta 443) para a fila. 
    - O agente de autenticação está agora pronto para obter e processar pedidos de validação de palavra-passe.

Se tiver múltiplos agentes de autenticação registada no seu inquilino, em seguida, o procedimento de inicialização assegura que cada um deles estabelece ligação à mesma fila de barramento de serviço. 

## <a name="process-sign-in-requests"></a>Processar pedidos de início de sessão 

O diagrama seguinte mostra como a autenticação pass-through processa pedidos de início de sessão do utilizador.

![Processo início de sessão](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

A autenticação pass-through processa um pedido de início de sessão do utilizador da seguinte forma: 

1. Um utilizador tenta aceder a uma aplicação, por exemplo, [Outlook Web App](https://outlook.office365.com/owa).
2. Se o utilizador não está já iniciou sessão, a aplicação redireciona o browser para a página de início de sessão do Azure AD.
3. O serviço de Azure AD STS responde fazer uma cópia com o **sessão do utilizador** página.
4. O utilizador introduz o nome de utilizador e palavra-passe para o **sessão do utilizador** página e, em seguida, seleciona o **início de sessão** botão.
5. O nome de utilizador e palavra-passe são submetidas para o Azure AD STS num pedido POST de HTTPS.
6. Azure AD STS obtém as chaves públicas para todos os agentes de autenticação registada no seu inquilino da base de dados SQL do Azure e encripta a palavra-passe utilizando-los. 
    - Produz os valores de palavra-passe "N" encriptado para agentes "N" autenticação registados no seu inquilino.
7. Azure AD STS coloca o pedido de validação da palavra-passe, que consiste o nome de utilizador e os valores de palavra-passe encriptada, para a fila do Service Bus específico do seu inquilino.
8. Porque os agentes de autenticação inicializado estão ligados forma permanente para a fila de barramento de serviço, um dos agentes de autenticação disponíveis obtém o pedido de validação da palavra-passe.
9. O agente de autenticação localiza o valor de palavra-passe encriptada que é específica para a chave pública, utilizando um identificador e desencripta a mesma, utilizando a respetiva chave privada.
10. O agente de autenticação tenta validar o nome de utilizador e a palavra-passe contra do Active Directory no local utilizando o [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) com o **dwLogonType** parâmetro definido como **LOGON32_LOGON_NETWORK**. 
    - Esta API está a API mesma que é utilizada por serviços de Federação do Active Directory (AD FS) para iniciar sessão dos utilizadores um cenário de início de sessão federado.
    - Esta API baseia-se no processo de resolução padrão no Windows Server para localizar o controlador de domínio.
11. O agente de autenticação recebe o resultado do Active Directory, tais como êxito, nome de utilizador ou palavra-passe incorreta ou a palavra-passe expirou.
12. O agente de autenticação reencaminha o resultado para o STS do Azure AD através de um canal HTTPS mutuamente autenticado saído através da porta 443. A autenticação mútua utiliza o certificado emitido anteriormente para o agente de autenticação durante o registo.
13. Azure AD STS verifica que o resultado deste está correlacionada com o pedido específico início de sessão no seu inquilino.
14. Azure AD STS continua com o procedimento de início de sessão como configurado. Por exemplo, se a validação da palavra-passe foi concluída com êxito, o utilizador poderá ser solicitado para multi-factor Authentication ou redirecionado novamente para a aplicação.

## <a name="operational-security-of-the-authentication-agents"></a>Segurança operacional dos agentes de autenticação

Para garantir que a autenticação pass-through permanece operacionalmente segura, do Azure AD periodicamente renovação de certificados de autenticação agentes. Azure AD aciona a renovação. As renovações não são regidas pelos agentes de autenticação próprios.

![Segurança operacional](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Para renovar a confiança de um agente de autenticação com o Azure AD:

1. O agente de autenticação periodicamente efetua o ping do Azure AD em poucas horas para verificar se está na altura de renovar o certificado. 
    - Esta verificação é feita através de um canal HTTPS mutuamente autenticado e utiliza o mesmo certificado que foi emitido durante o registo.
2. Se o serviço indica que está na altura de renovar, o agente de autenticação gera um novo par de chaves: uma chave pública e uma chave privada.
    - Estas chaves são geradas através de encriptação de RSA 2048 bits padrão.
    - A chave privada nunca sai do servidor no local.
3. O agente de autenticação, em seguida, envia um pedido de "renovação do certificado" para o Azure AD através de HTTPS, com os seguintes componentes incluídos no pedido:
    - O certificado existente que é obtido a partir da localização CERT_SYSTEM_STORE_LOCAL_MACHINE no arquivo de certificados do Windows. Não há nenhum administrador global envolvidos neste procedimento, pelo que não existe nenhum token de acesso necessária em nome de administrador global.
    - A chave pública gerada no passo 2.
    - Um certificado de assinatura de pedido (CSR ou pedido de certificado). Este pedido aplica-se a um novo certificado de identidade digital, com o Azure AD como a autoridade de certificação.
4. Azure AD valida o certificado existente no pedido de renovação de certificado. Em seguida, verifica se o pedido veio de um agente de autenticação registada no seu inquilino.
5. Se o certificado existente ainda é válido, do Azure AD, em seguida, inicia um novo certificado de identidade digital e emite o novo certificado para o agente de autenticação. 
6. Se o certificado existente tiver expirado, do Azure AD elimina o agente de autenticação da lista do seu inquilino de registado de autenticação de agentes. Em seguida, um administrador global tem de instalar manualmente e registar um novo agente de autenticação.
    - Utilize a AC de raiz do Azure AD para assinar o certificado.
    - Defina o assunto do certificado (nome único ou DN) para o ID de inquilino, um GUID que identifica exclusivamente o seu inquilino. O DN âmbitos o certificado com o seu inquilino apenas.
6. Azure AD armazena a nova chave pública do agente de autenticação numa base de dados SQL do Azure que apenas tem acesso a. -Invalida também a chave pública antiga associada com o agente de autenticação.
7. O novo certificado (emitido no passo 5), em seguida, é armazenado no servidor no arquivo de certificados do Windows (especificamente no [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) localização).
    - Porque o procedimento de renovação de confiança acontece de forma não interativa (sem a presença de um administrador global), o agente de autenticação já não tem acesso para atualizar o certificado existente na localização CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Este procedimento não remover o certificado propriamente dito da localização CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. O novo certificado é utilizado para autenticação a partir deste ponto no. Cada subsequente renovação do certificado substitui o certificado na localização CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Atualização automática de agentes de autenticação

A aplicação do Atualizador é atualizada automaticamente o agente de autenticação quando for lançada uma nova versão. A aplicação não processa os pedidos de validação da palavra-passe para o seu inquilino. 

Azure AD aloja a nova versão do software como um assinado **pacote do Windows Installer (MSI)**. O MSI sejam assinado utilizando [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) com SHA256 como o algoritmo de texto implícita. 

![Atualização automática](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

A atualização de automática um agente de autenticação:

1. Os pings de aplicação do Atualizador do Azure AD para verificar se há uma nova versão do agente de autenticação disponível a cada hora. 
    - Esta verificação é feita através de um canal HTTPS mutuamente autenticado utilizando o mesmo certificado que foi emitido durante o registo. O agente de autenticação e do Atualizador partilham o certificado armazenado no servidor.
2. Se estiver disponível uma nova versão, do Azure AD devolve o MSI assinado novamente para o Atualizador.
3. O Atualizador verifica se o MSI está assinado pela Microsoft.
4. O Atualizador é executado o MSI. Esta ação envolve os seguintes passos:

 > [!NOTE]
 > O Atualizador executa com [Sistema Local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) privilégios.

    - Para o serviço de agente de autenticação
    - Instala a nova versão do agente de autenticação no servidor
    - Reinicia o serviço de agente de autenticação

>[!NOTE]
>Se tiver múltiplos agentes de autenticação registada no seu inquilino, o Azure AD não renovar os respetivos certificados ou atualizá-las ao mesmo tempo. Em vez disso, do Azure AD não gradualmente, por isso, certifique-se a disponibilidade elevada de pedidos de início de sessão.
>


## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](active-directory-aadconnect-pass-through-authentication-current-limitations.md): saber que cenários são suportados e aqueles que não são.
- [Início Rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md): começar a trabalhar na autenticação pass-through do Azure AD.
- [Bloqueio do smart](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): configurar a capacidade de bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [Como funciona](active-directory-aadconnect-pass-through-authentication-how-it-works.md): aprender as noções básicas como funciona a autenticação pass-through do Azure AD.
- [Perguntas mais frequentes](active-directory-aadconnect-pass-through-authentication-faq.md): encontre respostas a perguntas mais frequentes.
- [Resolver problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Saiba como resolver problemas comuns com a funcionalidade de autenticação pass-through.
- [Azure SSO totalmente integrada de AD](active-directory-aadconnect-sso.md): saber mais sobre esta funcionalidade complementares.
