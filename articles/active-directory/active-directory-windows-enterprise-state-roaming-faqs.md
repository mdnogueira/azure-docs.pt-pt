---
title: "As definições e dados em roaming FAQ | Microsoft Docs"
description: "Fornece respostas a algumas questões, os administradores de TI podem ter sobre as definições e sincronização de dados de aplicação."
services: active-directory
keywords: "Enterprise Estado as definições de roaming, nuvem do windows, perguntas mais frequentes em roaming de estado empresarial"
documentationcenter: 
author: tanning
manager: swadhwa
editor: curtand
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 9968d9fa1ebbc92b5647a23c75e75fb819f5d5ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="settings-and-data-roaming-faq"></a>FAQ de definições e roaming de dados
Este tópico responde a algumas perguntas os administradores de TI podem ter sobre as definições e sincronização de dados de aplicação.

## <a name="what-data-roams"></a>Se os dados?
**Definições do Windows**: as definições do PC que são incorporadas no sistema operativo Windows. Geralmente, estas são as definições que personalizar o seu PC e incluem as seguintes categorias amplas:

* *Tema*, que inclui funcionalidades, tais como o ambiente de trabalho definições tema e na barra de tarefas.
* *Definições do Internet Explorer*, incluindo separadores recentemente abertas e os favoritos.
* *As definições do browser de contorno*, tais como favoritos e a lista de leitura.
* *As palavras-passe*, incluindo palavras-passe de Internet, perfis Wi-Fi e outras pessoas.
* *Preferências de idioma*, que inclui as definições de esquemas de teclado, idioma do sistema, data e hora e muito mais.
* *Facilidade de funcionalidades de acesso*, tais como o tema de alto contraste, Narrador e Lupa.
* *Outras definições do Windows*, tais como definições de linha de comandos e a lista de aplicações.

**Dados de aplicação**: aplicações Universal do Windows podem escrever dados de definições para uma pasta de roaming e serão automaticamente sincronizados todos os dados escritos nesta pasta. Está a funcionar para o Programador de aplicações individuais para conceber uma aplicação para tirar partido desta capacidade. Para obter mais detalhes sobre como desenvolver uma aplicação Universal do Windows que utiliza o roaming, consulte o [appdata armazenamento API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) e [appdata Windows 8 em roaming blogue para programadores](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Que conta é utilizada para sincronização de definições?
No Windows 8 e Windows 8.1, a sincronização de definições sempre utilizadas contas Microsoft de consumidor. Os utilizadores empresariais tinham a capacidade de ligar uma conta Microsoft à sua conta de domínio do Active Directory para obter acesso a sincronização de definições. No Windows 10, isto ligado conta Microsoft, a funcionalidade está a ser substituída com uma estrutura de conta primária/secundário.

A conta primária é definida como a conta utilizada para iniciar sessão no Windows. Isto pode ser uma conta Microsoft, uma conta do Azure Active Directory (Azure AD), uma conta do Active Directory no local ou uma conta local. Para além da conta principal, os utilizadores do Windows 10 podem adicionar uma ou mais contas de nuvem secundário para o respetivo dispositivo. Uma conta secundária é, geralmente, uma conta Microsoft, uma conta do Azure AD ou alguma outra conta, tais como o Gmail ou o Facebook. Estas contas secundárias fornecem acesso a serviços adicionais, tais como o início de sessão único e a loja Windows, mas não são capazes da ligar a sincronização de definições.

No Windows 10, apenas a conta primária para o dispositivo pode ser utilizada para sincronização de definições (consulte [como atualizar da sincronização de definições de conta Microsoft no Windows 8 para o Azure AD sync definições no Windows 10?](active-directory-windows-enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Dados nunca for misto entre as contas de utilizador diferente no dispositivo. Existem duas regras de sincronização de definições:

* Windows irá sempre sejam acedidas remotamente com a conta primária.
* Os dados da aplicação serão etiquetados com a conta utilizada para adquirir a aplicação. Irão sincronizar apenas aplicações marcadas com a conta do principal. Marcação de propriedade de aplicação é determinado quando uma aplicação side-loaded através da loja Windows ou a gestão de dispositivos móveis (MDM).

Se não seja possível identificar o proprietário de uma aplicação, será se movem com a conta primária. Se um dispositivo é atualizado a partir do Windows 8 ou Windows 8.1, Windows 10, todas as aplicações serão marcadas como adquirido pela conta Microsoft. Isto acontece porque a maioria dos utilizadores adquirir aplicações através da loja Windows e não ocorreu nenhum suporte para a loja Windows para contas do Azure AD antes do Windows 10. Se uma aplicação é instalada através de uma licença offline, a aplicação será marcada com a conta primária no dispositivo.

> [!NOTE]
> Dispositivos Windows 10 que são propriedade da empresa e que estão ligados ao Azure AD já não podem ligar as respetivas contas Microsoft para uma conta de domínio. A capacidade de ligar uma conta Microsoft para uma conta de domínio e ter a sincronização de dados do utilizador à conta Microsoft (ou seja, a conta Microsoft em roaming através de ligado conta Microsoft e a funcionalidade de Active Directory) é removida do Windows 10 dispositivos que estão associados a um ambiente do Active Directory ou do Azure AD ligado.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Como atualizar da sincronização de definições de conta Microsoft no Windows 8 para o Azure AD sync definições no Windows 10?
Se estão associados ao domínio do Active Directory com o Windows 8 ou Windows 8.1 com uma conta Microsoft ligada, irá sincronizar definições através da sua conta Microsoft. Depois de atualizar para o Windows 10, continuará a sincronizar as definições do utilizador através da conta Microsoft, desde que um utilizador associados a um domínio e o domínio do Active Directory não estabelecer ligação com o Azure AD.

Se o domínio do Active Directory no local a estabelecer ligação com o Azure AD, o dispositivo irá tentar sincronizar as definições utilizando o ligado conta do Azure AD. Se o administrador do Azure AD não permitir Roaming de estado empresarial, o ligado conta do Azure AD irá parar a sincronização de definições. Se for um utilizador do Windows 10 e iniciar sessão com uma identidade do Azure AD, irá iniciar a sincronização de definições do windows, assim que o administrador ativa a sincronização de definições através do Azure AD.

Se tiver armazenado quaisquer dados pessoais no seu dispositivo da empresa, deve ter conhecimento de que dados de aplicações e do sistema operativo Windows começará a sincronização com o Azure AD. Isto tem implicações seguintes:

* Definições da sua conta Microsoft pessoais terá que se desviam além as definições no seu trabalho ou escola contas do Azure AD. Isto acontece porque a conta Microsoft e definições do Azure AD sincronizar agora estão a utilizar contas separadas.
* Dados pessoais tais como palavras-passe de Wi-Fi, as credenciais da web e os favoritos do Internet Explorer que anteriormente foram sincronizados através de uma conta Microsoft ligada serão sincronizados através do Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Como fazer o trabalho de interoperabilidade de Roaming de estado do Azure AD Enterprise e conta Microsoft?
As versões de Novembro versão 2015 ou posterior do Windows 10, Roaming de estado empresarial é apenas suportado para uma única conta cada vez. Se iniciar sessão no Windows utilizando um trabalho ou escola conta do Azure AD, todos os dados serão sincronizados através do Azure AD. Se iniciar sessão Windows, utilizando uma conta Microsoft pessoal, serão sincronizados todos os dados através da conta Microsoft. Universal appdata será sejam acedidas remotamente utilizando apenas a início de sessão na conta principal no dispositivo e será se movem apenas se a licença da aplicação é propriedade de conta da principal. Universal appdata para as aplicações pertencentes a quaisquer contas secundárias não será sincronizado.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>As definições de sincronização para contas de anúncios do Azure a partir de múltiplos inquilinos?
Quando contas de várias do Azure AD de diferentes inquilinos do Azure AD estão no mesmo dispositivo, tem de atualizar o registo do dispositivo para comunicar com o Azure Rights Management (Azure RMS) para cada inquilino do Azure AD.  

1. Localize o GUID para cada inquilino do Azure AD. Abra o portal clássico do Azure e selecione um inquilino do Azure AD. É o GUID para o inquilino no URL na barra de endereço do seu browser. Por exemplo: `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Depois de ter o GUID, terá de adicionar a chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<GUID de ID de inquilino >**.
   Do **GUID de ID de inquilino** chave, crie um novo valor de cadeia múltipla (REG-várias-SZ) com o nome **AllowedRMSServerUrls**. Para os respetivos dados, especifique os URLs de ponto de distribuição licenciamento de outros inquilinos do Azure que acede ao dispositivo.
3. Pode encontrar os URLs de ponto de distribuição licenciamento executando o **Get-AadrmConfiguration** cmdlet. Se os valores para o **LicensingIntranetDistributionPointUrl** e **LicensingExtranetDistributionPointUrl** são diferentes, especifique ambos os valores. Se os valores forem iguais, especifique o valor apenas uma vez.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Quais são as opções de definições de roaming para aplicações de ambiente de trabalho Windows existentes?
Roaming só funciona para aplicações Universal do Windows. Existem duas opções disponíveis para ativar uma aplicação de ambiente de trabalho existente do Windows está em roaming:

* O [Bridge de ambiente de trabalho](http://aka.ms/desktopbridge) ajuda-o a colocar as aplicações de ambiente de trabalho do Windows existentes para a plataforma Universal do Windows. Aqui, alterações de código mínimas será necessárias para tirar partido de roaming de dados de aplicação do Azure AD. A Bridge de ambiente de trabalho fornece as suas aplicações com uma identidade de aplicação, o que é necessária para ativar o roaming para aplicações de ambiente de trabalho existentes de dados de aplicação.
* [Virtualização de experiência de utilizador (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) ajuda-o a criar um modelo de definições personalizadas para aplicações de ambiente de trabalho do Windows existentes e ativar roaming para aplicações de Win32. Esta opção não necessita que o programador da aplicação alterar o código da aplicação. UE-V está limitado a Itinerância de Active Directory no local para os clientes que compraram Microsoft Desktop Optimization Pack.

Os administradores podem configurar UE-V para se movem os dados da aplicação de ambiente de trabalho Windows alterando o roaming das definições do sistema operativo Windows e os dados de aplicação Universal através de [políticas de grupo de UE-V](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), incluindo:

* Transferir a política de grupo de definições do Windows
* Não sincronizar a política de grupo de aplicações do Windows
* Internet Explorer em roaming na secção de aplicações

No futuro, a Microsoft pode investigar formas para tornar profundamente integrado no Windows de UE-V e expandir UE-V transferir definições através da nuvem do Azure AD.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Posso armazenar definições sincronizadas e os dados no local?
Roaming de estado empresarial armazena todos os dados sincronizados na nuvem do Azure. Oferece de UE-V no local em roaming solução.

## <a name="who-owns-the-data-thats-being-roamed"></a>Quem tem os dados que estão a ser movidos?
As empresas próprias movidos dados através de Roaming de estado empresarial. Os dados são armazenados num datacenter do Azure. Todos os dados de utilizador são encriptados em trânsito e inativos na nuvem utilizando o Azure RMS. Esta é uma melhoria em comparação comparada a sincronização de definições com base na conta Microsoft, que encripta apenas determinados dados confidenciais, tais como as credenciais do utilizador antes de sai do dispositivo.

A Microsoft está empenhada em proteger as dados de cliente. Dados de definições de um utilizador enterprise automaticamente são encriptados pelo Azure RMS antes de sai um dispositivo Windows 10, pelo que não existem outros utilizadores podem ler estes dados. Se a sua organização tiver uma subscrição paga do Azure RMS, pode utilizar outras funcionalidades do Azure RMS, por exemplo, controlar e revogar documentos, automaticamente proteger e-mails que contêm informações confidenciais e gerir as suas próprias chaves (a solução "traga a sua própria chave", também conhecido como BYOK). Para obter mais informações sobre estas funcionalidades e como funciona o Azure RMS, consulte [que é o Azure Rights Management](https://technet.microsoft.com/jj585026.aspx).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Pode gerir a sincronização para uma aplicação específica ou a definição?
No Windows 10, não há nenhuma definição de MDM ou política de grupo para desativar o roaming para uma aplicação individual. Os administradores inquilinos podem desativar a sincronização de appdata para todas as aplicações em dispositivos geridos, mas não existe nenhum melhorar o controlo a um nível por aplicação ou na aplicação.

## <a name="how-can-i-enable-or-disable-roaming"></a>Como ativar ou desativar o roaming?
No **definições** aplicação, aceda a **contas** > **sincronize as suas definições**. Nesta página, pode ver a conta está a ser utilizada para transferir as definições e pode ativar ou desativar a grupos individuais de definições para ser movidos.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>O que é recomendação da Microsoft para ativar o roaming no Windows 10?
A Microsoft tem alguns diferentes definições de roaming soluções disponíveis, incluindo perfis de utilizador itinerantes, UE-V e Roaming de estado de empresa.  A Microsoft está empenhada para efetuar um investimento em Enterprise Estado Roaming nas futuras versões do Windows. Se a sua organização não está pronto ou confortável com mover dados para a nuvem, em seguida, recomendamos que utilize UE-V com a tecnologia de roaming primária. Se a sua organização necessita de suporte para aplicações de ambiente de trabalho do Windows existentes de roaming, mas é eager mover para a nuvem, recomendamos que utilize o Roaming de estado do Enterprise e UE-V. Apesar de UE-V e Roaming de estado empresarial são muito semelhantes tecnologias, não são mutuamente exclusivos. Complementa entre si para ajudar a garantir que a sua organização fornece os serviços de roaming que os utilizadores precisam.  

Ao utilizar o Roaming de estado empresarial e de UE-V, aplicam as seguintes regras:

* Roaming de estado empresarial é o agente de roaming primário no dispositivo. UE-V está a ser utilizado para completar o "intervalo de Win32".
* Roaming para as definições do Windows e os dados da aplicação UWP modernos de UE-V deve ser desativado quando utilizar o grupo de UE-V políticas. Estes já estão abrangidas pelos Roaming de estado empresarial.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Como o Roaming de estado de Enterprise suporta a infraestrutura de ambiente de trabalho virtual (VDI)?
Roaming de estado empresarial é suportada no Windows 10 cliente SKUs, mas não no servidor SKUs. Se um cliente VM está alojado numa máquina hipervisor e iniciam sessão remotamente em à máquina virtual, os dados serão utilizados em roaming. Se vários utilizadores partilham o mesmo sistema operativo e os utilizadores iniciam sessão remotamente em para um servidor para uma experiência de ambiente de trabalho completo, não poderá funcionar em roaming. O cenário de última baseados em sessão não é oficialmente suportado.

## <a name="what-happens-when-my-organization-purchases-azure-rms-after-using-roaming"></a>O que acontece quando a minha organização comprar o Azure RMS depois de utilizar o roaming?
Se a sua organização já está a utilizar em roaming no Windows 10 com a Azure RMS utilização limitada subscrição gratuita, compra de uma subscrição paga do Azure RMS não terá qualquer impacto na funcionalidade da funcionalidade de roaming e sem alterações de configuração será necessárias o administrador de TI.

## <a name="known-issues"></a>Problemas conhecidos
Consulte a documentação do [resolução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md) secção para obter uma lista dos problemas conhecidos. 

## <a name="related-topics"></a>Tópicos relacionados
* [Descrição geral de roaming de estado do Enterprise](active-directory-windows-enterprise-state-roaming-overview.md)
* [Ativar roaming no Azure Active Directory de estado empresarial](active-directory-windows-enterprise-state-roaming-enable.md)
* [Política de grupo e definições de MDM para sincronização de definições](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Referência de definições de roaming Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Resolução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
