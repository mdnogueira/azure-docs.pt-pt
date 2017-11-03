---
title: "O Azure AD Connect: Ativar a repetição de escrita do dispositivo | Microsoft Docs"
description: "Este documento fornece detalhes sobre como ativar a repetição de escrita do dispositivo com o Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.openlocfilehash: 7af8fadca15e07e178f12db27fec2467f43c5d36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>O Azure AD Connect: Ativar a repetição de escrita do dispositivo
> [!NOTE]
> Uma subscrição do Azure AD Premium é necessária para a repetição de escrita do dispositivo.
> 
> 

A seguinte documentação fornece informações sobre como ativar a funcionalidade de repetição de escrita do dispositivo no Azure AD Connect. Repetição de escrita do dispositivo é utilizada nos seguintes cenários:

* Ativar o acesso condicional com base nos dispositivos para AD FS (2012 R2 ou superior) aplicações (confianças de entidades confiadoras) protegidas.

Esta opção fornece segurança adicional e garantia que é concedido acesso a aplicações apenas a dispositivos fidedignos. Para obter mais informações sobre o acesso condicional, consulte [gerir o risco com o acesso condicional](../active-directory-conditional-access.md) e [configurar o acesso condicional no local utilizando o registo de dispositivos do Azure Active Directory](../active-directory-conditional-access-automatic-device-registration-setup.md).

> [!IMPORTANT]
> <li>Dispositivos devem estar localizados na mesma floresta que os utilizadores. Uma vez que os dispositivos têm de ser escritos novamente para uma floresta única, esta funcionalidade não suporta atualmente uma implementação com várias florestas de utilizador.</li>
> <li>Objeto de configuração de registo de dispositivos apenas pode ser adicionado à floresta do Active Directory no local. Esta funcionalidade não é compatível com uma topologia em que o Active Directory no local é sincronizado a fim de vários diretórios do Azure AD.</li>> 

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Instalar o Azure AD Connect
1. Instale o Azure AD Connect utilizando personalizado ou definições rápidas. A Microsoft recomenda começar com todos os utilizadores e grupos sincronizados com êxito antes de ativar a repetição de escrita do dispositivo.

## <a name="part-2-prepare-active-directory"></a>Parte 2: Preparar do Active Directory
Utilize os seguintes passos para preparar para utilizar a repetição de escrita do dispositivo.

1. Da máquina onde está instalado o Azure AD Connect, inicie o PowerShell no modo elevado.
2. Se o módulo PowerShell do Active Directory não estiver instalado, instale as ferramentas de administração remota do servidor que contém o módulo PowerShell do AD e dsacls.exe que é necessário para executar o script.  Execute o seguinte comando:
  
   ``` powershell
   Add-WindowsFeature RSAT-AD-Tools
   ```

3. Se o módulo do Azure Active Directory PowerShell não está instalado, em seguida, transfira e instale-à partir [módulo Azure Active Directory para Windows PowerShell (versão de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297). Este componente não tem uma dependência no assistente início de sessão, que é instalado com o Azure AD Connect.  
4. Com credenciais de administrador de empresa, execute os seguintes comandos e, em seguida, saia do PowerShell.
   
   ``` powershell
   Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'
   ```

   ``` powershell
   Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}
   ```

São necessárias credenciais de administrador de empresa, uma vez que forem necessárias alterações para o espaço de nomes de configuração. Um administrador de domínio será tem permissões suficientes.

![PowerShell para ativar a repetição de escrita do dispositivo](./media/active-directory-aadconnect-feature-device-writeback/powershell.png) d


Descrição:

* Se já não existir, cria e configura novos contentores e objetos em CN = Device Registration Configuration, CN = Services, CN = Configuration, [floresta dn].
* Se já não existir, cria e configura novos contentores e objetos em CN = RegisteredDevices, [dn do domínio]. Os objetos de dispositivo serão criados neste contentor.
* Define as permissões necessárias na conta do conector do Azure AD, para gerir dispositivos no seu Active Directory.
* Só tem de executar na floresta, mesmo que o Azure AD Connect está a ser instalado em várias florestas.

Parâmetros:

* DomainName: Domínio do Active Directory onde serão criados a objetos de dispositivo. Nota: Todos os dispositivos para uma floresta do Active Directory fornecido serão criados num único domínio.
* AdConnectorAccount: Conta do Active Directory que será utilizada pelo Azure AD Connect para gerir objetos no diretório. Esta é a conta utilizada por uma sincronização do Azure AD Connect para ligar ao AD. Se tiver instalado utilizando as definições rápidas, é a conta de prefixo MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Parte 3: Repetição de escrita ativar dispositivo no Azure AD Connect
Utilize o procedimento seguinte para ativar a repetição de escrita do dispositivo no Azure AD Connect.

1. Execute novamente o Assistente de instalação. Selecione **personalizar as opções de sincronização** de tarefas adicionais de página e clique em **seguinte**.
   ![Instalação personalizada personalizar as opções de sincronização](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2. Na página de funcionalidades opcionais, repetição de escrita do dispositivo será já não é possível a cinzento. . Tenha em atenção que se o Azure AD Connect passos de preparação não são repetição de escrita do dispositivo concluída irá ser desativada horizontalmente na página de funcionalidades opcionais. Marque a caixa de repetição de escrita do dispositivo e clique em **seguinte**. Se a caixa de verificação ainda estiver desativada, consulte o [secção de resolução de problemas](#the-writeback-checkbox-is-still-disabled).
   ![Personalizada instalar funcionalidades opcionais de repetição de escrita do dispositivo](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3. Na página de repetição de escrita, verá o domínio fornecido como a floresta de repetição de escrita do dispositivo predefinido.
   ![Personalizado floresta de destino de repetição de escrita a dispositivos de instalação](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4. Conclua a instalação do assistente sem alterações de configuração adicionais. Se necessário, consulte [instalação personalizada do Azure AD Connect.](active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Ativar o acesso condicional
Instruções detalhadas para ativar este cenário estão disponíveis no [configurar o acesso condicional no local utilizando o registo de dispositivos do Azure Active Directory](../active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Certifique-se de que são sincronizados dispositivos ao Active Directory
Agora a repetição de escrita do dispositivo deve ser funcionar corretamente. Lembre-se de que pode demorar até 3 horas para os objetos de dispositivo ser a repetição de escrita para o AD.  Para verificar que os seus dispositivos estão a ser sincronizados corretamente, efetue o seguinte depois de concluir as regras de sincronização:

1. Inicie o Centro de administração do Active Directory.
2. Expanda RegisteredDevices, dentro do domínio que está a ser federado.
   ![Centro de administração do Active Directory registar dispositivos](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3. Dispositivos registados atuais serão listados não existe.
   ![Centro de administração do Active Directory registado a lista de dispositivos](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="the-writeback-checkbox-is-still-disabled"></a>A caixa de verificação de repetição de escrita ainda está desativada
Se a caixa de verificação para repetição de escrita do dispositivo não estiver ativada, apesar de ter seguido os passos acima, os passos seguintes descrevem o que o Assistente de instalação está a verificar antes da caixa está ativada.

Primeiros aspetos primeiro:

* Certifique-se de que tem, pelo menos, uma floresta do Windows Server 2012R2. O tipo de objeto de dispositivo tem de estar presente.
* Se o Assistente de instalação já está em execução, em seguida, quaisquer alterações não serão detetadas. Neste caso, conclua o Assistente de instalação e execute-o novamente.
* Certifique-se a conta que fornece o script de inicialização, na verdade, o utilizador correto utilizado pelo conector do Active Directory. Para verificar isto, siga estes passos:
  * A partir do menu Iniciar, abra **serviço de sincronização**.
  * Abra o **conectores** separador.
  * Localize o conector com o tipo de serviços de domínio do Active Directory e selecione-o.
  * Em **ações**, selecione **propriedades**.
  * Aceda a **ligar à floresta do Active Directory**. Certifique-se de que o nome de utilizador e domínio especificado nesta correspondência de ecrã, a conta fornecida para o script.
    ![Conta do conector no Gestor de serviço de sincronização](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Verifique a configuração no Active Directory:

* Certifique-se de que o serviço de registo do dispositivo se encontra localizado na localização abaixo (CN = DeviceRegistrationService, CN = Serviços no registo de dispositivos, CN = Device Registration Configuration, CN = Services, CN = Configuration) no contexto de nomenclatura de configuração.

![Resolver problemas, DeviceRegistrationService no espaço de nomes de configuração](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* Certifique-se existe apenas um objeto de configuração ao pesquisar o espaço de nomes de configuração. Se existir mais do que um, elimine o duplicado.

![Resolver problemas, procure os objetos duplicados](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* Do objeto de serviço de registo de dispositivos, certifique-se o atributo msDS-DeviceLocation está presente e tem um valor. Pesquisa esta localização e certifique-se estiver presente com o msDS-DeviceContainer objectType.

![Resolver problemas, msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Resolver problemas, RegisteredDevices classe do objeto](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* Certifique-se de que a conta utilizada pelo conector do Active Directory tem as permissões necessárias no contentor de dispositivos registados encontrado pelo passo anterior. Este é as permissões esperadas neste contentor:

![Resolver problemas, verifique as permissões no contentor](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* Certifique-se de que a conta do Active Directory tem permissões no CN = Device Registration Configuration, CN = Services, CN = o objeto de configuração.

![Resolver problemas, verifique as permissões na configuração de registo do dispositivo](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informações adicionais
* [Gerir o risco com o acesso condicional](../active-directory-conditional-access.md)
* [Configurar o acesso condicional no local utilizando o registo de dispositivos do Azure Active Directory](../active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

