---
title: Configurar o acesso condicional no local utilizando o registo de dispositivos do Azure Active Directory | Microsoft Docs
description: "Um guia passo a passo para ativar o acesso condicional para aplicações no local através da utilização de serviços de Federação do Active Directory (AD FS) no Windows Server 2012 R2."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 6d53d08156d0e5d1d070361db5b74d201f18a224
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Configurar o acesso condicional no local utilizando o registo de dispositivos do Azure Active Directory
Quando necessitar que os utilizadores a associação à área de trabalho os seus dispositivos pessoais para o serviço de registo de dispositivos do Azure Active Directory (Azure AD), os respetivos dispositivos podem ser marcados como conhecida para a sua organização. Segue-se um guia passo a passo para ativar o acesso condicional para aplicações no local através da utilização de serviços de Federação do Active Directory (AD FS) no Windows Server 2012 R2.

> [!NOTE]
> Uma licença do Office 365 ou uma licença do Azure AD Premium é necessária quando utilizar dispositivos que estão registados nas políticas de acesso condicional do serviço de registo do Azure Active Directory dispositivo. Estes incluem políticas são impostas pelo AD FS no recursos no local.
> 
> Para obter mais informações sobre os cenários de acesso condicional para recursos no local, consulte [associar à área de trabalho a partir de qualquer dispositivo para SSO e autenticação de segundo fator totalmente integrada nas aplicações da empresa](https://technet.microsoft.com/library/dn280945.aspx).

Estas capacidades estão disponíveis para os clientes que adquirir uma licença do Azure Active Directory Premium.

## <a name="supported-devices"></a>Dispositivos suportados
* Dispositivos associados a domínios do Windows 7
* Dispositivos de pessoais e associados a um domínio do Windows 8.1
* iOS 6 e posterior para o browser Safari
* Android 4.0 ou posterior, Samsung GS3 ou telemóveis posteriores, Samsung Galaxy nota 2 ou posteriores tablets

## <a name="scenario-prerequisites"></a>Pré-requisitos do cenário
* Subscrição do Office 365 ou do Azure Active Directory Premium
* Um inquilino do Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 ou posterior)
* Esquema atualizada no Windows Server 2012 R2
* Licença do Azure Active Directory Premium
* Windows Server 2012 R2 os serviços de Federação, configurado para SSO ao Azure AD
* Proxy de aplicações Web do Windows Server 2012 R2 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(transferir o Azure AD Connect)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Domínio verificado

## <a name="known-issues-in-this-release"></a>Problemas conhecidos desta versão
* Políticas de acesso condicional baseado no dispositivo necessitam de repetição de escrita de objeto de dispositivo no Active Directory do Azure Active Directory. Pode demorar até três horas para os objetos de dispositivo repetição de escrita para o Active Directory.
* dispositivos iOS 7 sempre apresentado um aviso ao utilizador selecionar um certificado durante a autenticação de certificado de cliente.
* Algumas versões do iOS 8 antes de iOS 8.3 não funcionam.

## <a name="scenario-assumptions"></a>Pressupostos do cenário
Este cenário pressupõe que tem um ambiente híbrido, constituída por um inquilino do Azure AD e um Active Directory no local. Estes inquilinos devem estar ligados com o Azure AD Connect, com um domínio verificado e com o AD FS para SSO. Utilize a lista de verificação seguinte para ajudar a configurar o seu ambiente, de acordo com os requisitos.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Lista de verificação: Pré-requisitos para o cenário de acesso condicional
Ligar o seu inquilino do Azure AD com a instância do Active Directory no local.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurar o serviço de registo de dispositivos do Azure Active Directory
Utilize este guia para implementar e configurar o serviço de registo de dispositivos do Azure Active Directory para a sua organização.

Este guia pressupõe que tiver configurado o Windows Server Active Directory e tiver subscrito ao Microsoft Azure Active Directory. Consulte os pré-requisitos descritos anteriormente.

Para implementar o serviço de registo de dispositivos do Azure Active Directory com o inquilino do Azure Active Directory, conclua as tarefas da lista de verificação seguinte na ordem. Se uma ligação de referência o levar para um tópico conceptual, volte a esta lista de verificação posteriormente, para que possa prosseguir com as tarefas restantes. Algumas tarefas incluem um passo de validação de cenário que pode ajudar a confirmar se o passo foi concluído com êxito.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Parte 1: Registo de dispositivos ativar Azure Active Directory
Siga os passos da lista de verificação para ativar e configurar o serviço de registo de dispositivos do Azure Active Directory.

| Tarefa | Referência | 
| --- | --- |
| Ative o registo de dispositivos no seu inquilino do Azure Active Directory para permitir que os dispositivos associar área de trabalho. Por predefinição, o multi-factor Authentication do Azure não está ativada para o serviço. No entanto, recomendamos que utilize o multi-factor Authentication quando registar um dispositivo. Antes de ativar a multi-factor Authentication no serviço de registo do Active Directory, certifique-se de que o AD FS está configurado para um fornecedor de multi-factor Authentication. |[Ativar o registo de dispositivos do Azure Active Directory](active-directory-device-registration-get-started.md)| 
|Dispositivos detetar o serviço de registo de dispositivos do Azure Active Directory procurando registos DNS conhecidos. Configure o DNS da sua empresa para que os dispositivos podem detetar o serviço de registo de dispositivos do Azure Active Directory. |[Configurar a deteção de registo de dispositivos do Azure Active Directory](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Parte 2: Implementar e configurar o Windows Server 2012 R2 Active Directory Federation Services e configurar uma relação de federação com o Azure AD

| Tarefa | Referência |
| --- | --- |
| Implemente serviços de domínio do Active Directory com as extensões de esquema do Windows Server 2012 R2. Não é necessário atualizar qualquer um dos seus controladores de domínio para o Windows Server 2012 R2. A atualização de esquema é o único requisito. |[Atualizar o esquema de serviços de domínio do Active Directory](#upgrade-your-active-directory-domain-services-schema) |
| Dispositivos detetar o serviço de registo de dispositivos do Azure Active Directory procurando registos DNS conhecidos. Configure o DNS da sua empresa para que os dispositivos podem detetar o serviço de registo de dispositivos do Azure Active Directory. |[Preparar os dispositivos de suporte do Active Directory](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>Parte 3: Repetição de escrita ativar dispositivo no Azure AD
| Tarefa | Referência |
| --- | --- |
| Concluir dois parte "Ativar dispositivos repetição de escrita no Azure AD Connect." Depois de terminar, regresse a este guia. |[Ativar a repetição de escrita do dispositivo no Azure AD Connect](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Opcional] Parte 4: Ativar multi-factor Authentication
Recomendamos vivamente que configure uma das várias opções para o multi-factor Authentication. Se pretende exigir a autenticação Multifator, consulte o artigo [escolher a solução de segurança da multi-factor Authentication para](../multi-factor-authentication/multi-factor-authentication-get-started.md). Inclui uma descrição de cada solução e ligações para o ajudar a configurar a solução à sua escolha.

## <a name="part-5-verification"></a>Parte 5: verificação
A implementação está agora concluída e, pode experimentar o alguns cenários. Utilize as seguintes ligações para experimentar o serviço e se familiarize com as respetivas funcionalidades.

| Tarefa | Referência |
| --- | --- |
| Associe alguns dispositivos à área de trabalho utilizando o serviço de registo de dispositivos do Azure Active Directory. Pode associar os dispositivos Android, iOS e Windows. |[Associar dispositivos à área de trabalho com o serviço de registo de dispositivos do Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Ver e ativar ou desativar dispositivos registados através do portal de administrador. Nesta tarefa, ver alguns dispositivos registados através do portal de administrador. |[Azure Active Directory registo serviço descrição geral de dispositivos](active-directory-device-registration-get-started.md) |
| Certifique-se de que os objetos de dispositivo são repetidos do Azure Active Directory para Windows Server Active Directory. |[Certifique-se de que os dispositivos registados são escritos de volta para o Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Agora que os utilizadores podem registar os respetivos dispositivos, pode criar a aplicação de políticas de acesso no AD FS que permitem apenas a dispositivos registados. Nesta tarefa, vai criar uma regra de acesso de aplicação e uma mensagem de acesso negado personalizada. |[Criar uma política de acesso de aplicação e mensagem personalizada de acesso negado](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrar o Active Directory no local do Azure Active Directory
Este passo ajuda a integrar o seu inquilino do Azure AD com o Active Directory no local utilizando o Azure AD Connect. Apesar dos passos estão disponíveis no portal clássico do Azure, anote quaisquer instruções especiais que são indicados nesta secção.

1. Inicie sessão no portal clássico do Azure, utilizando uma conta que seja um administrador global no Azure AD.
2. No painel esquerdo, selecione **Active Directory**.
3. No separador **Diretório** selecione o diretório.
4. Selecione o **integração de diretórios** separador.
5. Sob o **implementar e gerir** secção, siga os passos 1 a 3 para integrar o Azure Active Directory com o seu diretório no local.
   
   1. Adicione domínios.
   2. Instalar e executar o Azure AD Connect utilizando as instruções apresentadas em [instalação personalizada do Azure AD Connect](connect/active-directory-aadconnect-get-started-custom.md).
   3. Certifique-se e gerir a sincronização de diretórios. As instruções de início de sessão único estão disponíveis dentro deste passo.
   
   Além disso, configurar a Federação com o AD FS conforme descrito na [instalação personalizada do Azure AD Connect](connect/active-directory-aadconnect-get-started-custom.md).

## <a name="upgrade-your-active-directory-domain-services-schema"></a>Atualizar o esquema de serviços de domínio do Active Directory
> [!NOTE]
> Depois de atualizar o esquema do Active Directory, o processo não pode ser anulado. Recomendamos que efetue primeiro a atualização num ambiente de teste.
> 

1. Inicie sessão no seu controlador de domínio com uma conta que tenha o administrador de empresa e os direitos de administrador de esquema.
2. Copiar o **[media] \support\adprep** diretório e subdiretórios para um dos seus controladores de domínio do Active Directory (onde **[suporte de dados]** é o caminho para o suporte de dados de instalação do Windows Server 2012 R2).
4. Numa linha de comandos, vá para o **adprep** diretório e execute **adprep.exe /forestprep**. Siga as instruções onscreen para concluir a atualização de esquema.

## <a name="prepare-your-active-directory-to-support-devices"></a>Preparar o Active Directory para suportar dispositivos
> [!NOTE]
> Esta é uma operação única que é necessário executar para preparar a floresta do Active Directory para suportar dispositivos. Para concluir este procedimento, tem de ser assinado com permissões de administrador de empresa e a floresta do Active Directory tem de ter o esquema do Windows Server 2012 R2.
> 


### <a name="prepare-your-active-directory-forest"></a>Preparar a floresta do Active Directory
1. No servidor de Federação, abra uma janela de comando do Windows PowerShell e, em seguida, escreva **inicializar ADDeviceRegistration**. 
2. Quando lhe for pedido para **ServiceAccountName**, introduza o nome da conta de serviço selecionado como a conta de serviço para o AD FS. Se for uma conta gMSA, introduza a conta no **domain\accountname$** formato. Para uma conta de domínio, utilize o formato **domain\accountname**.

### <a name="enable-device-authentication-in-ad-fs"></a>Ativar autenticação do dispositivo no AD FS
1. No servidor de Federação, abra a consola de gestão do AD FS e aceda à **do AD FS** > **políticas de autenticação**.
2. No **ações** painel, selecione **editar autenticação principal Global**.
3. Verifique **ativar autenticação do dispositivo**e, em seguida, selecione **OK**.
4. Por predefinição, o AD FS remove periodicamente os dispositivos do Active Directory. Desative esta tarefa quando estiver a utilizar o serviço de registo de dispositivos do Azure Active Directory para que os dispositivos podem ser geridos no Azure.

### <a name="disable-unused-device-cleanup"></a>Desativar a limpeza do dispositivo não utilizadas
No servidor de Federação, abra uma janela de comando do Windows PowerShell e, em seguida, escreva **Set AdfsDeviceRegistration - MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Preparar o Azure AD Connect para a repetição de escrita do dispositivo
Concluir a parte 1: preparar o Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Associar dispositivos à área de trabalho utilizando o serviço de registo de dispositivos do Azure Active Directory

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Associar um dispositivo iOS utilizando o registo de dispositivos do Azure Active Directory
Registo de dispositivos do Azure Active Directory utiliza o processo de inscrição de perfil por ondas eletromagnéticas para dispositivos iOS. Este processo começa quando o utilizador liga para o URL do perfil de inscrição com Safari. O formato de URL é o seguinte:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Neste caso, `yourdomainname` é o nome de domínio que configurou no Azure Active Directory. Por exemplo, se o nome de domínio for contoso.com, o URL é o seguinte:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Existem muitas formas diferentes de comunicar este URL aos seus utilizadores. Por exemplo, um método, que recomendamos que está a publicar este URL numa mensagem de acesso negado de aplicação personalizada no AD FS. Esta informação é abrangida na próxima secção [criar uma política de acesso de aplicação e mensagem personalizada de acesso negado](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Associar um dispositivo Windows 8.1 com o registo de dispositivos do Azure Active Directory
1. No seu dispositivo Windows 8.1, selecione **definições do PC** > **rede** > **à área de trabalho**.
2. Introduza o nome de utilizador no formato UPN; Por exemplo,  **dan@contoso.com** .
3. Selecione **associar**.
4. Quando lhe for solicitado, inicie sessão com as suas credenciais. O dispositivo está agora associado.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Associar um dispositivo Windows 7, utilize o registo de dispositivos do Azure Active Directory
Para registar dispositivos associados a domínios do Windows 7, tem de implementar o pacote de software de registo do dispositivo. O pacote de software é chamado associação do local de trabalho para o Windows 7 e está disponível para transferência no [Web site Microsoft Connect](https://connect.microsoft.com/site1164). 

Instruções sobre como utilizar o pacote estão disponíveis no [como configurar o registo automático de dispositivos associados a um domínio do Windows com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Certifique-se de que os dispositivos registados são escritos de volta para o Active Directory
Pode ver e certifique-se de que os objetos de dispositivo escritos novamente para o Active Directory com LDP.exe ou Editor de ADSI. Ambos estão disponíveis com as ferramentas de administração do Active Directory.

Por predefinição, os objetos de dispositivo são repetidos do Azure Active Directory são colocados no mesmo domínio que o farm do AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Criar uma política de acesso de aplicação e mensagem personalizada de acesso negado
Considere o seguinte cenário: criar uma aplicação de confiança da entidade Confiadora no AD FS e configurar uma regra de autorização de emissão que permite apenas a dispositivos registados. Agora apenas os dispositivos que estão registados estão autorizados a aceder à aplicação. 

Para tornar mais fácil para os seus utilizadores obter acesso à aplicação, configure uma mensagem de acesso negado personalizada que inclui instruções sobre como associar os seus dispositivos. Agora, os utilizadores têm de forma totalmente integrada para registar os respetivos dispositivos para que podem aceder uma aplicação.

Os passos seguintes mostram como implementar este cenário.

> [!NOTE]
> Esta secção assume que já configurou uma confiança da entidade Confiadora para a sua aplicação no AD FS.
> 

1. Abra a ferramenta de MMC do AD FS e, em seguida, selecione **do AD FS** > **relações de confiança** > **confianças da entidade Confiadora**.
2. Localize a aplicação aos quais se aplica esta regra de acesso de novo. A aplicação com o botão direito e, em seguida, selecione **editar regras de afirmação**.
3. Selecione o **regras de autorização de emissão** separador e, em seguida, selecione **Adicionar regra**.
4. Do **regra de afirmação** modelo na lista pendente, selecione **permitir ou negar utilizadores com base numa afirmação de entrada**. Em seguida, selecione **seguinte**.
5. No **nome da regra de afirmação** , digite **permitir o acesso a partir de dispositivos registados**.
6. Do **tipo de afirmação de entrada** na lista pendente, selecione **é utilizador registado**.
7. No **valor de afirmação de entrada** , digite **verdadeiro**.
8. Selecione o **permitir o acesso a utilizadores com esta afirmação de entrada** botão de opção.
9. Selecione **concluir**e, em seguida, selecione **aplicar**.
10. Remova quaisquer regras que são mais permissiva do que a regra que criou. Por exemplo, remover a regra predefinida **permitir acesso a todos os utilizadores**.

A aplicação está agora configurada para permitir o acesso apenas quando o utilizador é proveniente de um dispositivo que registado e associado à área de trabalho. Para políticas de acesso mais avançado, consulte [gerir o risco com autenticação Multifator adicional para aplicações confidenciais](https://technet.microsoft.com/library/dn280949.aspx).

Em seguida, configure uma mensagem de erro personalizada para a sua aplicação. A mensagem de erro informa os utilizadores de que estes tem de associar o dispositivo à área de trabalho antes de poderem aceder a aplicação. Pode criar uma mensagem de acesso negado de aplicação personalizada utilizando personalizado HTML e PowerShell.

No servidor de Federação, abra uma janela de comando do PowerShell e, em seguida, escreva o seguinte comando. Substitua os itens específicos ao seu sistema partes do comando:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Tem de registar o seu dispositivo para poder aceder a esta aplicação.

**Se estiver a utilizar um dispositivo iOS, selecione esta hiperligação para associar o seu dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Associe este dispositivo iOS para a sua área de trabalho.

Se estiver a utilizar um dispositivo Windows 8.1, pode associar o seu dispositivo ao selecionar **definições do PC**> **rede** > **à área de trabalho**.

Nos comandos anteriores, **nome de confiança da entidade confiadora terceiros** é o nome do objeto de confiança de entidade Confiadora da aplicação no AD FS.
E **<subdomain>.oseudomínio.com** é o nome de domínio que configurou no Azure Active Directory (por exemplo, contoso.com).
Não se esqueça de remover quaisquer quebras de linha (se aplicável) o conteúdo HTML que passar para o **conjunto AdfsRelyingPartyWebContent** cmdlet.

Agora quando os utilizadores acedem a aplicação de um dispositivo que não está registado com o serviço de registo de dispositivos do Azure Active Directory, verá um erro.

