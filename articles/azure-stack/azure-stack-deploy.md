---
title: "Pré-requisitos da implementação do Kit de desenvolvimento de pilha do Azure | Microsoft Docs"
description: Ver os requisitos de hardware e de ambiente para o Kit de desenvolvimento de pilha do Azure (operador da nuvem).
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/11/2017
ms.author: erikje
ms.openlocfilehash: 73e7efb7d789fe12846d68066c0927bb123831a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-deployment-prerequisites"></a>Pré-requisitos da implementação do Azure Stack

*Aplica-se a: Azure da pilha Kit de desenvolvimento*

Antes de implementar [Kit de desenvolvimento de pilha do Azure](azure-stack-poc.md), certifique-se de que o computador cumpre os seguintes requisitos:


## <a name="hardware"></a>Hardware
| Componente | Mínimo | Recomendado |
| --- | --- | --- |
| Unidades de disco: Sistema Operativo |1 disco do SO com 200 GB disponíveis, no mínimo, para a partição do sistema (SSD ou HDD) |1 disco do SO com 200 GB disponíveis, no mínimo, para a partição do sistema (SSD ou HDD) |
| Unidades de disco: geral development kit dados * |4 discos. Cada disco fornece um mínimo de 140 GB de capacidade (SSD ou HDD). Todos os discos disponíveis serão utilizados. |4 discos. Cada disco fornece um mínimo de 250 GB de capacidade (SSD ou HDD). Todos os discos disponíveis serão utilizados. |
| Computação: CPU |Dual-Socket: 12 Núcleos Físicos (total) |Dual-Socket: 16 Núcleos Físicos (total) |
| Computação: Memória |96 GB de RAM |128 GB de RAM (este é o mínimo para suportar fornecedores de recursos de PaaS).|
| Computação: BIOS |Hyper-V Ativado (com suporte SLAT) |Hyper-V Ativado (com suporte SLAT) |
| Rede: NIC |Certificação Windows Server 2012 R2 necessária para a NIC; nenhuma funcionalidade específica necessária |Certificação Windows Server 2012 R2 necessária para a NIC; nenhuma funcionalidade específica necessária |
| Certificação de logótipo do HW |[Certificado para o Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificado para o Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\*Precisa de mais do que esta capacidade opção recomendada se planear adicionar muitas do [itens do marketplace](azure-stack-download-azure-marketplace-item.md) a partir do Azure.

**Configuração da unidades de disco de dados:** todas as unidades de dados devem ser do mesmo tipo (todas SAS ou todas SATA) e com a mesma capacidade. Se forem utilizadas unidades de disco SAS, as unidades de disco devem ser ligadas através de um caminho único (sem MPIO; é fornecido suporte a caminhos múltiplos).

**Opções de configuração de HBA**

* (Preferencial) HBA Simples
* HBA RAID – O adaptador deve ser configurado no modo “Pass Through”
* HBA RAID – Os discos devem ser configurados como Disco Único, RAID-0

**Combinações de tipo de barramento suportado e suportes de dados**

* HDD SATA
* HDD SAS
* HDD RAID
* RAID SSD (se o tipo de suporte não for especificado/for desconhecido\*)
* SSD SATA + HDD SATA
* SSD SAS + HDD SAS

\*Controladores RAID sem capacidade de pass-through não consegue reconhecer o tipo de suporte. Esses controladores irão marcar ambas as unidades HDD e SSD como Não Especificadas. Nesse caso, a SSD será utilizada como armazenamento persistente, em vez de dispositivos de colocação em cache. Por conseguinte, pode implementar o kit de desenvolvimento nesses SSDs.

**Exemplo de HBAs**: LSI 9207-8i, LSI-9300-8i ou LSI-9265-8i no modo Pass Through

Estão disponíveis configurações do OEM de exemplo.

## <a name="operating-system"></a>Sistema operativo
|  | **Requisitos** |
| --- | --- |
| **Versão do SO** |Windows Server 2012 R2 ou posterior. A versão do sistema operativo não é crítica antes do início da implementação, conforme irá arrancar o computador anfitrião para o VHD que está incluído na instalação de pilha do Azure. Sistema operativo e todos os patches necessários já estão integrados com a imagem. Não utilize a quaisquer chaves para ativar a quaisquer instâncias de Windows Server utilizadas no kit de desenvolvimento. |

## <a name="deployment-requirements-check-tool"></a>Ferramenta de verificação de requisitos de implementação
Depois de instalar o sistema operativo, pode utilizar o [Verificador de implementação para a pilha de Azure](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar que o seu hardware cumpre todos os requisitos.

## <a name="account-requirements"></a>Requisitos de conta
Normalmente, implementa o kit de desenvolvimento com ligação à internet, onde pode ligar ao Microsoft Azure. Neste caso, tem de configurar uma conta do Azure Active Directory (Azure AD) para implementar o kit de desenvolvimento.

Se o ambiente não está ligado à internet ou se não pretender utilizar o Azure AD, pode implementar a pilha do Azure, utilizando os serviços de Federação do Active Directory (AD FS). O kit de desenvolvimento inclui as suas próprias instâncias do AD FS e serviços de domínio do Active Directory. Se implementar utilizando esta opção, não tem de configurar as contas de antecedência.

>[!NOTE]
Se implementar utilizando a opção do AD FS, tem de voltar a implementar o Azure pilha para mudar para o Azure AD.

### <a name="azure-active-directory-accounts"></a>Contas do Active Directory do Azure
Para implementar a pilha do Azure, utilizando uma conta do Azure AD, tem de preparar uma conta do Azure AD antes de executar a script do PowerShell de implementação. Esta conta torna-se o Administrador Global para o inquilino do Azure AD. É utilizado para aprovisionar e delegar principais de serviço para todos os serviços de pilha do Azure que interagem com o Azure Active Directory e do Graph API e aplicações. Também é utilizado como o proprietário da subscrição de fornecedor predefinido (que é possível alterar mais tarde). Pode iniciar sessão no portal do administrador do seu sistema de pilha do Azure utilizando esta conta.

1. Crie uma conta do Azure AD, que é o administrador do diretório do Azure AD, pelo menos, um. Se já tiver uma conta, pode utilizá-la. Caso contrário, pode criar uma gratuitamente em [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (na China, visite <http://go.microsoft.com/fwlink/?LinkID=717821> em vez disso,). Se pretender mais tarde [registar pilha do Azure com o Azure](azure-stack-register.md), também tem de ter uma subscrição deste conta recentemente criada.
   
    Guardar estas credenciais para utilização no passo 6 do [implementar o kit de desenvolvimento](azure-stack-run-powershell-script.md#deploy-the-development-kit). Esta conta de *administrador de serviços* permite configurar e gerir nuvens de recursos, contas de utilizador, planos de inquilino, quotas e preços. No portal, pode criar nuvens de sites, nuvens privadas de máquinas virtuais, criar planos e gerir subscrições do utilizador.
2. [Criar](azure-stack-add-new-user-aad.md) pelo menos uma conta para que pode iniciar sessão para o kit de desenvolvimento de inquilino.
   
   | **Conta do Active Directory do Azure** | **Suportado?** |
   | --- | --- |
   | Conta escolar ou profissional, com uma subscrição do Azure público válida |Sim |
   | Conta da Microsoft com a Subscrição Pública do Azure válida |Sim |
   | Conta escolar ou profissional, com uma subscrição válida do Azure China |Sim |
   | Conta profissional ou escolar com válido US Government subscrição do Azure |Sim |

## <a name="network"></a>Rede
### <a name="switch"></a>Comutador
Uma porta disponível num comutador para a máquina do kit de desenvolvimento.  

A máquina do kit de desenvolvimento suporta a ligação uma porta de comutador de acesso ou a porta de ramal. Não são necessárias quaisquer funcionalidades especializadas no comutador. Se estiver a utilizar uma porta de ramal ou se tiver de configurar um ID de VLAN, terá de fornecer o ID de VLAN como um parâmetro de implementação. Pode ver exemplos de [lista dos parâmetros de implementação](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Subrede
Não ligar a máquina do kit de desenvolvimento para as sub-redes que se seguem:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Estas sub-redes estão reservados para as redes internas no ambiente do kit de desenvolvimento.

### <a name="ipv4ipv6"></a>IPv4/IPv6
É apenas suportado o IPv4. Não pode criar redes IPv6.

### <a name="dhcp"></a>DHCP
Verifique se existe um servidor DHCP disponível na rede à qual liga o NIC. Se o DHCP não estiver disponível, terá de preparar uma rede IPv4 estática adicional para além daquela utilizada pelo anfitrião. Tem de fornecer esse endereço IP e gateway como um parâmetro de implementação. Pode ver exemplos de [lista dos parâmetros de implementação](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Acesso à Internet
Pilha do Azure requer acesso à Internet, diretamente ou através de um proxy transparente. Pilha do Azure não suporta a configuração de um proxy web para ativar o acesso à Internet. O IP do anfitrião e as novas IP atribuída à MAS-BGPNAT01 (pelo DHCP ou o IP estático) tem de ser capazes de aceder à Internet. As portas 80 e 443 são utilizadas em domínios graph.windows.net e login.microsoftonline.com.

## <a name="telemetry"></a>Telemetria

Telemetria ajuda-na formam versões futuras do Azure pilha. Permite-nos responda rapidamente aos comentários, fornecer novas funcionalidades e melhorar a qualidade. Pilha do Microsoft Azure inclui o Windows Server 2016 e o SQL Server 2014. Nenhum destes produtos são alterado das predefinições e ambos são descritas pela declaração de privacidade do Microsoft Enterprise. Pilha do Azure também contém software open source para que não tenha sido modificado para enviar telemetria para a Microsoft. Seguem-se alguns exemplos de dados de telemetria de pilha do Azure:

- informações de registo de implementação
- Quando um alerta é aberto e fechado
- o número de recursos de rede

Para suportar o fluxo de dados de telemetria, a porta 443 (HTTPS) tem de ser aberta na sua rede. O ponto final do cliente é https://vortex-win.data.microsoft.com.

Se não quiser fornecer a telemetria de pilha do Azure, pode desativá-la no anfitrião do kit de desenvolvimento e as máquinas de virtuais de infraestrutura, conforme explicado abaixo.

### <a name="turn-off-telemetry-on-the-development-kit-host-optional"></a>Desativar a telemetria no anfitrião do kit de desenvolvimento (opcional)

>[!NOTE]
Se pretender desativar a telemetria para o anfitrião do kit de desenvolvimento, deve fazê-lo antes de executar o script de implementação.

Antes de [executar o script de asdk installer.ps1]() para implementar o anfitrião do kit de desenvolvimento, arrancar o CloudBuilder.vhdx e execute o seguinte script numa janela elevada do PowerShell:
```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0'  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Definição **AllowTelemetry** para 0 activa a telemetria para a implementação do Windows e pilha do Azure. Apenas os eventos de segurança críticas do sistema operativo são enviados. A definição controla a telemetria do Windows em todos os anfitriões e VMs de infraestrutura e é reaplicada a novas nós/VMs quando ocorrerem operações de escalamento horizontal.


### <a name="turn-off-telemetry-on-the-infrastructure-virtual-machines-optional"></a>Desativar a telemetria sobre as máquinas virtuais de infraestrutura (opcional)

Após a implementação for bem sucedida, execute o seguinte script numa janela elevada do PowerShell (como o utilizador AzureStack\AzureStackAdmin) no anfitrião do kit de desenvolvimento:

```powershell
$AzSVMs= get-vm |  where {$_.Name -like "AzS-*"}
### Show current AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
### Set & Get updated AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {Set-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name "AllowTelemetry" -Value '0'}
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
```

Para configurar a telemetria do SQL Server, consulte [como configurar o SQL Server 2016](https://support.microsoft.com/en-us/help/3153756/how-to-configure-sql-server-2016-to-send-feedback-to-microsoft).

### <a name="usage-reporting"></a>Relatórios de utilização

Através do registo, a pilha do Azure também está configurada para informações de utilização direta para o Azure. Relatórios de utilização é controlada independentemente de telemetria. Pode desativar a utilização de relatórios quando [registar](azure-stack-register.md) utilizando o script no Github. Basta definir o **$reportUsage** parâmetro **$false**.

Os dados de utilização é a formatados como detalhadas no [dados de utilização de pilha do relatório do Azure para o Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-usage-reporting). Utilizadores do Kit de desenvolvimento de pilha do Azure, na verdade, não serem cobrados. Esta funcionalidade está incluída no kit de desenvolvimento, de modo a que possa testar para ver como funciona a relatórios de utilização. 


## <a name="next-steps"></a>Passos seguintes
[Transferir o pacote de implementação de kit de desenvolvimento de pilha do Azure](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Implementar o kit de desenvolvimento de pilha do Azure](azure-stack-run-powershell-script.md)

