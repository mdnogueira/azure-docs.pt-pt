---
title: "Resolução de problemas de definições de Roaming de estado empresarial no Azure Active Directory | Microsoft Docs"
description: "Fornece respostas a algumas questões, os administradores de TI podem ter sobre as definições e sincronização de dados de aplicação."
services: active-directory
keywords: "Enterprise Estado as definições de roaming, nuvem do windows, perguntas mais frequentes em roaming de estado empresarial"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: ed25e6b922321fd4d8852860ad8817dc318d89ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Resolução de problemas de definições de Roaming de estado empresarial no Azure Active Directory

Este tópico fornece informações sobre como diagnosticar e resolver problemas com o Roaming de estado empresarial e fornece uma lista dos problemas conhecidos.

## <a name="preliminary-steps-for-troubleshooting"></a>Preliminares passos de resolução de problemas 
Antes de iniciar a resolução de problemas, certifique-se de que o utilizador e dispositivo foram configuradas corretamente e que todos os requisitos de Roaming de estado empresarial são cumpridos, o dispositivo e o utilizador. 

1. Windows 10, com as atualizações mais recentes e um 1511 versão mínima (compilação do SO 10586 ou posterior) está instalado no dispositivo. 
2. O dispositivo é o Azure AD associada ou associada ao Azure AD híbrido. Para obter mais informações, consulte [a obtenção de um dispositivo sob o controlo do Azure AD](device-management-introduction.md).
3. Certifique-se de que **Roaming de estado empresarial** está ativada para o inquilino no Azure AD, tal como descrito no [para ativar o Roaming de estado empresarial](active-directory-windows-enterprise-state-roaming-enable.md). Pode ativar roaming para todos os utilizadores ou para um grupo selecionado de utilizadores.
4. O utilizador já deve ser atribuído uma licença do Azure Active Directory Premium.  
25. O dispositivo tem de ser reiniciado e o utilizador deve iniciar sessão novamente para aceder às funcionalidades de Roaming de estado empresarial.

## <a name="information-to-include-when-you-need-help"></a>Informações a incluir quando precisar de ajuda
Se não conseguir resolver o problema com a documentação de orientação abaixo, pode contactar a nossa engenheiros de suporte. Quando contactá-los, inclua as seguintes informações:

* **Descrição geral do erro**: existem mensagens de erro vistas pelo utilizador? Se não ocorreu nenhuma mensagem de erro, descrevem o comportamento inesperado reparado em detalhe. Quais as funcionalidades estão ativadas para sincronização e o que é o utilizador estava à espera de sincronização? São várias funcionalidades não sincronizar ou está isolada para um?
* **Utilizadores afetados** – é sincronização trabalho/falhar para um utilizador ou vários utilizadores? Quantos dispositivos estão envolvidos por utilizador? Todos eles não sincronizar ou algumas da sincronização e algumas não a sincronização?
* **Informações sobre o utilizador** – que a identidade é o utilizador a utilizar para iniciar sessão para o dispositivo? Como é o utilizador iniciar sessão no dispositivo? São parte de um grupo de segurança selecionados autorizado a sincronizar? 
* **Informações sobre o dispositivo** – este dispositivo do Azure AD-associados a um ou associados a um domínio? Compilação do que o dispositivo está no? Quais são as atualizações mais recentes?
- **Data / hora / fuso horário** – qual era a precisa data e hora vimos o erro (incluem o fuso horário)?

Incluindo estas informações ajuda-na resolver o problema mais rapidamente possível.

## <a name="troubleshooting-and-diagnosing-issues"></a>Resolução de problemas e diagnosticar problemas
Esta secção fornece sugestões sobre como resolver problemas e diagnosticar problemas relacionados com o Roaming de estado empresarial.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Certifique-se a sincronização e a página de definições "Sincronize as suas definições" 

1. Depois de associar o seu PC do Windows 10 a um domínio que está configurado para permitir Roaming de estado empresarial, início de sessão com a sua conta profissional. Aceda a **definições** > **contas** > **definições da sincronização** e confirme que a sincronização e as definições individuais são e que na parte superior do página de definições indica que estão a sincronizar com a sua conta profissional. Confirme a mesma conta também é utilizada como a sua conta de início de sessão no **definições** > **contas** > **informações do seu**. 
2. Certifique-se de que a sincronização funciona em várias máquinas ao fazer algumas alterações no computador original, tais como mover a barra de tarefas no lado direito ou superior do ecrã. Ver a alteração propagadas a segunda máquina dentro de cinco minutos. 
  * Bloquear e desbloquear que o ecrã (Win + L) pode ajudar a acionar uma sincronização.
  * Tem de ser iniciar sessão com a mesma conta em ambos os PCs para sincronização funcione – como o Roaming de estado empresarial está associado para a conta de utilizador e não a conta da máquina.

**Potencial problema**: se os controlos de **definições** página não estão disponíveis e ver a mensagem "algumas funcionalidades do Windows só estão disponíveis se estiver a utilizar uma conta Microsoft ou uma conta profissional." Este problema poderá surgir para dispositivos que estão definidos até ser associado a um domínio e registado com o Azure AD, mas o dispositivo ainda não ainda com êxito foi autenticado para o Azure AD. Uma causa possível é que tem de ser aplicada a política de dispositivo, mas esta aplicação acontece de forma assíncrona e foi atrasada por algumas horas. 

### <a name="verify-the-device-registration-status"></a>Verifique o estado de registo do dispositivo
Roaming de estado empresarial requer que o dispositivo ser registado com o Azure AD. Apesar de não específicas para o Roaming de estado empresarial, seguindo as instruções abaixo pode ajudar a confirmar que o cliente do Windows 10 está registado e confirmar o estado do thumbprint, o URL de definições do Azure AD, NGC e outras informações.

1.  Abra a linha de comandos o. Para fazê-lo no Windows, abra o iniciador de execução (Win + R) e escreva "cmd" para abrir.
2.  Depois de abrir a linha de comandos, escreva "*dsregcmd.exe /status*".
3.  Para o resultado esperado, o **AzureAdJoined** o valor de campo deve ser "YES", **WamDefaultSet** o valor de campo deve ser "YES" e o **WamDefaultGUID** o valor de campo deve ser um GUID com "(AzureAd)" no final.

**Potencial problema**: **WamDefaultSet** e **AzureAdJoined** tem o valor do campo "Não", o dispositivo foi associado a um domínio e registado com o Azure AD, tanto o dispositivo não sincronizar. Se está visível isto, o dispositivo poderá ter de aguardar por política sejam aplicadas ou a autenticação do dispositivo falhou ao estabelecer ligação com o Azure AD. O utilizador poderá ter de aguardar algumas horas para a política sejam aplicadas. Outros passos de resolução de problemas podem incluir repetir o registo automático ao terminar a sessão e back no ou iniciar a tarefa no programador de tarefas. Em alguns casos, em execução "*dsregcmd.exe /leave*" numa janela de linha de comandos elevada, podem ajudar a ser reiniciado e tentar novamente o registo com este problema.


**Potencial problema**: O campo de **AzureAdSettingsUrl** está vazio e o dispositivo não sincronizar. O utilizador pode ter pela última vez registado para o dispositivo antes do Roaming de estado empresarial foi ativada no Portal do Azure Active Directory. Reiniciar o dispositivo e tem o início de sessão do utilizador. Opcionalmente, no portal, tente ter o administrador de TI, desativar e reativar os utilizadores poderão sincronizar definições e dados de aplicações da empresa. Uma vez reativada, reiniciar o dispositivo e tem o início de sessão do utilizador. Se isto não resolver o problema, **AzureAdSettingsUrl** pode estar vazio no caso de um certificado de dispositivo inválido. Neste caso, em execução "*dsregcmd.exe /leave*" numa janela de linha de comandos elevada, podem ajudar a ser reiniciado e tentar novamente o registo com este problema.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming de estado empresarial e a autenticação Multifator 
Em determinadas condições, o Roaming de estado empresarial pode falhar sincronizar os dados se Azure multi-factor Authentication está configurado. Para obter detalhes adicionais sobre estes sintomas, consulte o documento de suporte [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potencial problema**: se o dispositivo estiver configurado para exigir a multi-factor Authentication no portal do Azure Active Directory, poderá falhar para definições de sincronização ao iniciar sessão num dispositivo Windows 10 através de uma palavra-passe. Este tipo de configuração de multi-factor Authentication destina-se para proteger uma conta de administrador do Azure. Os utilizadores administradores ainda poderá sincronizar ao iniciar sessão nos seus dispositivos Windows 10 com o respetivo Microsoft Passport para trabalho PIN ou concluindo o multi-factor Authentication ao aceder a outros serviços do Azure como o Office 365.

**Potencial problema**: sincronização pode falhar se o administrador configura a política de acesso condicional de Active Directory Federação serviços multi-factor Authentication e o token de acesso do dispositivo expira. Certifique-se de que a iniciar sessão e terminar sessão utilizando o Microsoft Passport para Work PIN ou concluir o multi-factor Authentication ao aceder a outros serviços do Azure como o Office 365.

###<a name="event-viewer"></a>Visualizador de eventos
Para resolução de problemas avançada, o Visualizador de eventos pode ser utilizado para localizar os erros específicos. Estes estão documentados na tabela abaixo. Os eventos podem ser encontrados no Visualizador de eventos > registos de serviços e aplicações > **Microsoft** > **Windows** > **SettingSync** e para problemas relacionados com a identidade com a sincronização **Microsoft** > **Windows** > **do Azure AD**.


## <a name="known-issues"></a>Problemas conhecidos

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Sincronização não funcionam em dispositivos que têm aplicações de sideload utilizando MDM software

Afeta os dispositivos que executam o Windows 10 aniversário da Update (versão 1607). No Visualizador de eventos em registos SettingSync Azure, o 6013 de ID de evento com o erro 80070259 é frequentemente utilizado.

**Ação recomendada**  
Certifique-se de que o cliente do Windows 10 v1607 tem de 23 de Agosto de 2016 a atualização cumulativa ([KB3176934](https://support.microsoft.com/kb/3176934) 14393.82 de compilação do SO). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Não sincronizar a Internet Explorer Favoritos

Afeta os dispositivos que executam a atualização de Novembro de 10 do Windows (versão 1511).

**Ação recomendada**  
Certifique-se de que o cliente do Windows 10 v1511 tem de Julho de 2016 a atualização cumulativa ([KB3172985](https://support.microsoft.com/kb/3172985) 10586.494 de compilação do SO).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Tema não estiver a sincronizar, bem como os dados protegidos com o Windows Information Protection 

Para evitar a fuga de dados, os dados que estão protegidos com [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) não serão sincronizados através de Roaming de estado empresarial para dispositivos com Windows 10 aniversário da atualização.



**Ação recomendada**  
nenhum. As futuras atualizações para o Windows poderão ajudar a resolver este problema.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Definições de data, hora e região não sincronizar num dispositivo associado a um domínio 
  
Dispositivos que estão associados a um domínio não irão ocorrer sincronização para a definição de data, hora e região: tempo automático. Utilizar hora automática pode substituir as outras definições de data, hora e região e fazer com que essas definições não a sincronização. 

**Ação recomendada**  
nenhum. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Pede-lhe UAC quando a sincronização de palavras-passe

Afeta os dispositivos que executam a atualização de Novembro de 10 do Windows (versão 1511) com um NIC sem fios que está configurado para sincronizar as palavras-passe.

**Ação recomendada**  
Certifique-se de que o cliente do Windows 10 v1511 tem a atualização cumulativa ([KB3140743](https://support.microsoft.com/kb/3140743) 10586.494 de compilação do SO).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Sincronização não funciona em dispositivos que utilizem smart cards para início de sessão
Se tentar iniciar sessão no seu dispositivo Windows com um smart card ou virtual smart card, a sincronização de definições irá parar de funcionar.     

**Ação recomendada**  
nenhum. As futuras atualizações para o Windows poderão ajudar a resolver este problema.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Dispositivos associados a um domínio não estiver a sincronizar após mantendo a rede empresarial     
Dispositivos associados a um domínio registados com o Azure AD podem ocorrer a falha de sincronização se o dispositivo fica fora das instalações por períodos prolongados de tempo e não é possível concluir a autenticação de domínio.

**Ação recomendada**  
Ligue o dispositivo a uma rede empresarial para que possa retomar a sincronização.

---

 ### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Não está a sincronizar o dispositivo do Azure AD associados e o utilizador tem um nome de Principal de utilizador caso misto.
 Se o utilizador tem um misto incidente UPN (por exemplo, nome de utilizador em vez do nome de utilizador) e o utilizador é um dispositivo de associados do Azure AD que tenha atualizado do 10586 de compilação do Windows 10 para 14393, o dispositivo do utilizador poderão falhar a sincronização. 

**Ação recomendada**  
O utilizador terá de anulação da associação ao e passem o dispositivo para a nuvem. Para fazê-lo, inicie sessão como o utilizador de administrador Local e anulação da associação ao dispositivo, acedendo a **definições** > **sistema** > **sobre** e selecione "Gerir a ou desligar de empresa ou escola ". Limpar os ficheiros abaixo e, em seguida, associação do Azure AD o dispositivo novamente na **definições** > **sistema** > **sobre** e selecionando "ligar ao trabalho ou Profissional". Continue a associação do dispositivo ao Azure Active Directory e concluir o fluxo.

O passo de limpeza, limpeza os seguinte ficheiros:
- Settings.dat no`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Todos os ficheiros na pasta`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID de evento 6065:80070533 que este utilizador não pode iniciar sessão porque esta conta está atualmente desativada  
No Visualizador de eventos em registos SettingSync/depuração, este erro pode ser visto quando as credenciais do utilizador tem expirado. Além disso, pode ocorrer quando o inquilino não tinha automaticamente AzureRMS aprovisionado. 

**Ação recomendada**  
No primeiro caso, tem de atualizar as credenciais e início de sessão para o dispositivo com as novas credenciais de utilizador. Para resolver o problema AzureRMS, continuar com os passos apresentados na [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>ID de evento 1098: Erro: Falha na operação do Mediador de Token de 0xCAA5001C  
No Visualizador de eventos em registos AAD/Operational, este erro pode ser visto com eventos 1104: chamada de plug-in do AAD da Ásia-Pacífico de nuvem Get token devolveu o erro: 0xC000005F. Este problema ocorre se existe estão em falta permissões ou de atributos de propriedade.  

**Ação recomendada**  
Continuar com os passos apresentados [KB3196528](https://support.microsoft.com/kb/3196528).  



## <a name="next-steps"></a>Passos seguintes

- Utilize o [fórum de voz do utilizador](https://feedback.azure.com/forums/169401-azure-active-directory/category/158658-enterprise-state-roaming) para fornecer comentários e sugestões sobre como melhorar o Roaming de estado empresarial.

- Para obter mais informações, consulte o [descrição geral do Roaming de estado empresarial](active-directory-windows-enterprise-state-roaming-overview.md). 

## <a name="related-topics"></a>Tópicos relacionados
* [Descrição geral de roaming de estado do Enterprise](active-directory-windows-enterprise-state-roaming-overview.md)
* [Ativar roaming no Azure Active Directory de estado empresarial](active-directory-windows-enterprise-state-roaming-enable.md)
* [As definições e roaming FAQ de dados](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Política de grupo e definições de MDM para sincronização de definições](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Referência de definições de roaming Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
