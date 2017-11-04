---
title: "Integração de registos do Azure FAQ | Microsoft Docs"
description: "Este artigo responde a questões sobre a integração de registo do Azure."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 08/07/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: bfdc7154160bb6bb7dc9c46eb2352ce74310c4de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-log-integration-faq"></a>Integração de registos do Azure FAQ
Este artigo responde a perguntas mais frequentes (FAQ) sobre a integração de registo do Azure. 

Integração de registo do Azure é um serviço do sistema operativo Windows que pode utilizar para integrar os registos não processados a partir dos seus recursos do Azure no seu sistemas no local segurança informações e eventos management (SIEM). Esta integração proporciona um dashboard unificado para todos os seus recursos, no local ou na nuvem. Pode, em seguida, Agregar, correlacionar, analisar e alerta para eventos de segurança associados com as suas aplicações.

## <a name="is-the-azure-log-integration-software-free"></a>O software de integração de registo do Azure é gratuito?
Sim. Não há sem encargos durante o software de integração de registo do Azure.

## <a name="where-is-azure-log-integration-available"></a>Em que a integração de registo do Azure está disponível?

Está atualmente disponível no Azure comerciais e Azure Government e não está disponível na China ou na Alemanha.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Como posso ver as contas de armazenamento a partir da qual a integração de registo do Azure é extrair os registos de VM do Azure?
Execute o comando **lista de origem azlog**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Como posso saber a subscrição que pertencem os registos de integração de registo do Azure?

No caso de registos de auditoria que são colocados no **AzureResourcemanagerJson** diretórios, a subscrição do ID está a ser o nome de ficheiro de registo. Isto também se aplica para os registos no **AzureSecurityCenterJson** pasta. Por exemplo:

20170407T070805_2768037.0000000023. **1111e5ee-1111-111b-a11e-1e111e1111dc**. JSON

Os registos de auditoria do Azure Active Directory incluem o ID de inquilino como parte do nome.

Os registos de diagnóstico que são lidos a partir de um hub de eventos não inclui o ID de subscrição como parte do nome. Em vez disso, incluem o nome amigável especificado como parte da criação da origem de hub de eventos. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Como posso atualizar a configuração do proxy?
Se a definição de proxy não permite o acesso de armazenamento do Azure diretamente, abra o **AZLOG. EXE. CONFIGURAÇÃO** ficheiros **c:\Program Files\Microsoft Azure registo integração**. Atualizar o ficheiro para incluir o **defaultProxy** secção com o endereço do proxy da sua organização. Depois de terminar a atualização, parar e iniciar o serviço, utilizando os comandos **net stop azlog** e **net iniciar azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Como posso ver as informações de subscrição de eventos do Windows?
Acrescente o ID de subscrição para o nome amigável ao adicionar a origem:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
O evento de XML tem os metadados seguintes, incluindo o ID de subscrição:

![Evento XML][1]

## <a name="error-messages"></a>Mensagens de erro
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Quando executar o comando **azlog createazureid**, por que motivo obterá o erro seguinte?
Erro:

  *Falha ao criar a aplicação AAD - inquilino 72f988bf-86f1-41af-91ab-2d7cd011db37-razão = 'Proibido' - mensagem = 'Privilégios suficientes para concluir a operação'.*

O **azlog createazureid** tenta criar um principal de serviço em todos os inquilinos do Azure AD para as subscrições que o início de sessão do Azure tem acesso ao comando. Se o início de sessão do Azure é apenas um utilizador convidado nesse inquilino do Azure AD, o comando falha com "Privilégios suficientes para concluir a operação." Peça ao administrador do inquilino para adicionar a sua conta como um utilizador no inquilino.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Quando executar o comando **azlog autorizar**, por que motivo obterá o erro seguinte?
Erro:

  *Criar a atribuição de função - AuthorizationFailed de aviso: O cliente janedo@microsoft.com' com o objeto o id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no âmbito ' subscrições/70 d 95299-d689-4C 97-b971-0d8ff0000000'.*

O **azlog autorizar** comando atribui a função de leitor para o principal de serviço do Azure AD (criado com **azlog createazureid**) para as subscrições fornecidas. Se o início de sessão do Azure não é um coadministrador ou um proprietário da subscrição, falhará com uma mensagem de erro "Falha de autorização". Azure baseada em funções controlo de acesso (RBAC) de coadministrador ou proprietário é necessário para concluir esta ação.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Onde posso encontrar a definição das propriedades no registo de auditoria?
Consulte:

* [Auditar operações com o Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Lista os eventos de gestão numa subscrição na API de REST de Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Onde posso encontrar os detalhes de alertas do Centro de segurança do Azure?
Consulte [gerir e responder a alertas de segurança no Centro de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Como posso modificar o que é recolhido com diagnósticos da VM?
Para obter detalhes sobre como obter, modificar e definir a configuração de diagnósticos do Azure, consulte [utilize o PowerShell para ativar o diagnóstico do Azure numa máquina virtual com o Windows](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

O exemplo seguinte obtém a configuração de diagnósticos do Azure:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

O exemplo seguinte modifica a configuração de diagnósticos do Azure. Nesta configuração, apenas o evento ID 4624 e o evento ID 4625 são recolhidos a partir do registo de eventos de segurança. Antimalware da Microsoft para os eventos do Azure são recolhidos a partir do registo de eventos do sistema. Para obter detalhes sobre a utilização de expressões XPath, consulte [consumir eventos](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

O exemplo seguinte define a configuração de diagnósticos do Azure:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Depois de efetuar alterações, certifique-se a conta de armazenamento para garantir que são recolhidos os eventos corretos.

Se tiver quaisquer problemas durante a instalação e configuração, abra uma [pedido de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Selecione **integração de registo** como o serviço para o qual está a pedir suporte.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Pode utilizar a integração de registo do Azure para integrar os registos de observador de rede no meu SIEM?

Observador de rede do Azure gera grandes quantidades de informações de registo. Estes registos não se destinam a ser enviadas para um SIEM. O destino suportado apenas para os registos de observador de rede é uma conta de armazenamento. Integração de registo do Azure não suporta leitura estes registos e disponibilizando-as para um SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
