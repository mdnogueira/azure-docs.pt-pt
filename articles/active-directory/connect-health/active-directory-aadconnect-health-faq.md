---
title: "Azure Active Directory Connect Health FAQ – Azure | Microsoft Docs"
description: "Estas perguntas mais frequentes respondem a dúvidas sobre o Azure AD Connect Health. Estas perguntas mais frequentes incluem perguntas sobre como utilizar o serviço, incluindo o modelo de faturação, capacidades, limitações e suporte."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 0c32ac27187a88dd13bb747f541968d2e81c5064
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Perguntas mais frequentes do Azure AD Connect Health
Este artigo inclui as respostas a perguntas mais frequentes (FAQ) sobre o Azure Active Directory (Azure AD) Connect Health. Estas perguntas mais frequentes incluem perguntas sobre como utilizar o serviço, que inclui a faturação modelo, capacidades, limitações e suporte.

## <a name="general-questions"></a>Perguntas gerais
**P: Posso gerir vários diretórios do Azure AD. Como mudar para um que tenha o Azure Active Directory Premium**

Alternar entre diferentes inquilinos do Azure AD, selecione o atualmente com sessão iniciada **nome de utilizador** no canto superior-direito canto e, em seguida, escolha a conta apropriada. Se a conta não está listada, selecione **terminar sessão**e, em seguida, utilizar as credenciais de administrador global do diretório que tem o Azure Active Directory Premium ativado para iniciar sessão.

**P: qual a versão das funções de identidade são suportadas pelo Azure AD Connect Health?**

A tabela seguinte lista as funções e versões de sistema operativo.

|Função| Sistema operativo / versão|
|--|--|
|Serviços de Federação do Active Directory (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Versão 1.0.9125 ou superior|
|Serviços de domínio do Active Directory (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Tenha em atenção que as funcionalidades fornecidas pelo serviço podem diferir consoante a função e o sistema operativo. Por outras palavras, todas as funcionalidades poderão não estar disponíveis em todas as versões de sistema operativo. Consulte as descrições de funcionalidade para obter mais detalhes.

**P: o número de licenças é necessário monitorizar a minha infraestrutura?**

* O agente de estado de funcionamento ligar primeiro requer pelo menos uma licença do Azure AD Premium.
* Cada agente registado adicional requer 25 licenças adicionais do Azure AD Premium.
* Contagem de agentes é equivalente ao número total de agentes que estão registados em todas as funções monitorizadas (AD FS, o Azure AD Connect, e/ou do AD DS).

Informações de licenciamento também é possível localizar no [página de preços do Azure AD](https://aka.ms/aadpricing).

Exemplo:

| Agentes registados | Licenças necessárias | Configuração de monitorização de exemplo |
| ------ | --------------- | --- |
| 1 | 1 | 1 servidor do azure AD Connect |
| 2 | 26| 1 do azure AD Connect servidor e o controlador de 1 domínio |
| 3 | 51 | 1 servidor de serviços de Federação do Active Directory (AD FS), o proxy de 1 AD FS e o controlador de 1 domínio |
| 4 | 76 | Servidor de 1 AD FS, proxy 1 AD FS e 2 controladores de domínio |
| 5 | 101 | 1 servidor do azure AD Connect, servidor 1 AD FS, proxy 1 AD FS e 2 controladores de domínio |

**P: É o suporte do Azure AD Connect Health nuvem de Datacenters do Azure?**

O Azure AD Connect Health tem um [instalação](active-directory-aadconnect-health-agent-install.md) para Datacenters do Azure. Todos os dados para os clientes da nuvem alemão é mantida na nuvem de Datacenters do Azure.


## <a name="installation-questions"></a>Perguntas de instalação

**P: qual é o impacto de instalar o agente do Azure AD Connect Health em servidores individuais?**

O impacto de instalar o agente do Microsoft Azure AD Connect Health AD FS, servidores de proxy de aplicações web, servidores do Azure AD Connect (sincronização), controladores de domínio é mínimo relativamente a CPU, consumo de memória, largura de banda de rede e armazenamento.

Os seguintes números são uma aproximação do seu:

* O consumo da CPU: ~ 1 a 5% aumento.
* Consumo de memória: até 10% da total da memória do sistema.

> [!NOTE]
> Se o agente não consegue comunicar com o Azure, o agente armazena os dados localmente para um limite máximo definido. O agente substitui os dados de forma "menos recentemente servidos" "em cache".
>
>

* Armazenamento local da memória intermédia para agentes de estado de funcionamento ligar do Azure AD: ~ 20 MB.
* Para servidores do AD FS, recomendamos que aprovisionar um espaço de disco de 1.024 MB (1 GB) para o canal de auditoria do AD FS do Azure AD os agentes Connect Health processar todos os dados de auditoria é substituído.

**P: Posso terá de reiniciar os meus servidores durante a instalação de agentes do AD Connect Health do Azure?**

Não. A instalação dos agentes não requerem a reiniciar o servidor. No entanto, a instalação de alguns passos dos pré-requisitos poderá exigir um reinício do servidor.

Por exemplo, no Windows Server 2008 R2, a instalação do .NET 4.5 Framework requer um reinício do servidor.

**P: É o trabalho do Azure AD Connect Health através de um proxy HTTP pass-through?**

Sim. Para operações em curso, pode configurar o agente de estado de funcionamento para utilizar um proxy HTTP para reencaminhar pedidos de HTTP de saída.
Leia mais sobre [configurar o Proxy de HTTP para os agentes de estado de funcionamento](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Se precisar de configurar um proxy durante o registo do agente, poderá ter de modificar as definições de Proxy do Internet Explorer previamente.

1. Abra o Internet Explorer > **definições** > **opções da Internet** > **ligações** > **definições de LAN** .
2. Selecione **utilizar um servidor Proxy para a sua LAN**.
3. Selecione **avançadas** se tiver portas proxy diferente para HTTP e HTTPS/seguro.

**P: funciona a autenticação básica de suporte do Azure AD Connect Health ao estabelecer ligação com HTTP proxies?**

Não. Um mecanismo para especificar um nome de utilizador arbitrária e a palavra-passe para a autenticação básica não é atualmente suportado.

**P: as portas de firewall é necessário abrir para o agente do Azure AD Connect estado de funcionamento funcionar?**

Consulte o [secção requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para a lista de portas de firewall e outros requisitos de conectividade.

**P: por que razão vejo dois servidores com o mesmo nome no portal do Azure AD Connect Health?**

Quando remove um agente de um servidor, o servidor não é removido automaticamente do portal do Azure AD Connect Health. Se remover um agente de um servidor manualmente ou remova o próprio servidor, terá de eliminar manualmente a entrada de servidor a partir do portal do Azure AD Connect Health.

Pode recriar um servidor ou criar um novo servidor com os detalhes do mesmos (por exemplo, o nome do computador). Se não removeu o servidor já registado a partir do portal do Azure AD Connect Health e instalar o agente no novo servidor, poderá ver duas entradas com o mesmo nome.

Neste caso, elimine manualmente a entrada que pertence o servidor antigo. Os dados para este servidor devem estar desatualizados.

## <a name="health-agent-registration-and-data-freshness"></a>Actualização de dados e de registo do agente de estado de funcionamento

**P: quais são razões comuns para as falhas de registo do agente de estado de funcionamento e como resolver problemas?**

O agente de estado de funcionamento pode falhar registar devido a pelas seguintes razões possíveis:

* O agente não consegue comunicar com os pontos finais necessários porque existe uma firewall a bloquear tráfego. Isto é particularmente comum em servidores de proxy de aplicações web. Certifique-se de que têm permitido comunicações de saída para as portas e os pontos finais necessários. Consulte o [secção requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para obter mais detalhes.
* Comunicação de saída é sujeitos a inspeção de SSL pela camada de rede. Isto faz com que o certificado que o agente utiliza para ser substituído pela inspeção servidor/entidade e efetuar os passos para concluir o registo do agente.
* O utilizador não tem acesso para executar o registo do agente. Administradores globais têm acesso por predefinição. Pode utilizar [controlo de acesso baseado em funções](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) para delegar o acesso a outros utilizadores.

**P: Posso estou obter alertado que "dados do serviço de estado de funcionamento não estão atualizados." Como posso resolver o problema?**

O Azure AD Connect Health gera o alerta quando este não recebe todos os pontos de dados do servidor nas últimas duas horas. Podem existir várias razões para este alerta.

* O agente não consegue comunicar com os pontos finais necessários porque existe uma firewall a bloquear tráfego. Isto é particularmente comum em servidores de proxy de aplicações web. Certifique-se de que têm permitido comunicações de saída para as portas e os pontos finais necessários. Consulte o [secção requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para obter mais detalhes.
* Comunicação de saída é sujeitos a inspeção de SSL pela camada de rede. Isto faz com que o certificado que o agente utiliza para ser substituído pela inspeção servidor/entidade e o processo não consegue carregar dados para o serviço do Azure AD Connect Health.
* Pode utilizar o comando de conectividade incorporado no agente. [Leia mais](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
* Os agentes também suportam a conectividade de saída através de um Proxy de HTTP não autenticados. [Leia mais](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).

## <a name="operations-questions"></a>Perguntas de operações
**P: é necessário ativar a auditoria nos servidores de proxy de aplicações web?**

Não, auditoria não precisa de ser ativada em servidores de proxy de aplicações web.

**P: como obter resolver alertas do Azure AD Connect Health?**

Alertas do Azure AD Connect Health obterem resolvidas numa condição de sucesso. Agentes de estado de funcionamento ligar do Azure AD detetar e comunicar as condições de êxito ao serviço periodicamente. Para alguns alertas, a supressão é baseados no tempo. Por outras palavras, se a condição de erro mesmo não é observada dentro de 72 horas da geração de alertas, o alerta é resolvido automaticamente.

**P: Posso estou obter alertado que "o pedido de autenticação de teste (transação sintética) não conseguiu obter um token." Como posso resolver o problema?**

Azure AD Connect Health para AD FS gera este alerta quando o agente de estado de funcionamento instalado num servidor do AD FS não consegue obter um token como parte de uma transação sintética iniciada pelo agente de estado de funcionamento. O agente de estado de funcionamento utiliza o contexto de sistema local e tenta obter um token para um self entidade confiadora. Este é um teste de catch-all para se certificar de que o AD FS está num Estado de emissão de tokens.

Frequentemente este teste falha porque o agente de estado de funcionamento se não é possível resolver o nome do farm do AD FS. Isto pode acontecer se os servidores AD FS atrás de um balanceadores de carga de rede e o pedido obtém iniciado a partir de um nó que está atrás do Balanceador de carga (por oposição a um cliente regular que é à frente do Balanceador de carga). Isto pode ser corrigido atualizando o ficheiro de "anfitriões" localizado em "C:\Windows\System32\drivers\etc" para incluir o endereço IP do servidor do AD FS ou um endereço IP de loopback (127.0.0.1) para o nome do farm do AD FS (por exemplo, sts.contoso.com). Adicionar o ficheiro do anfitrião serão short-circuit a chamada de rede, permitindo assim que o agente de estado de funcionamento para obter o token.

**P: Posso recebeu uma mensagem de e-mail a indicar que meus computadores não estão corrigidos para ataques de ransomeware recentes. Por que motivo recebeu este e-mail?**

Serviço do Azure AD Connect Health analisados todas as máquinas que monitoriza para garantir que os patches necessários foram instaladas. Mensagem de correio eletrónico foi enviada para os administradores de inquilinos se pelo menos uma máquina não tinha os patches críticos. A seguinte lógica foi utilizada para efetuar este determinação.
1. Localize todas as correções instaladas no computador.
2. Verifique se pelo menos uma das correções da lista está presente.
3. Se Sim, a máquina está protegida. Caso contrário, a máquina está em risco de ataque.

Pode utilizar o seguinte script do PowerShell para executar esta verificação manualmente. Implementa a lógica acima.

```
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```



## <a name="related-links"></a>Ligações relacionadas
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações do Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Histórico de versões do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
