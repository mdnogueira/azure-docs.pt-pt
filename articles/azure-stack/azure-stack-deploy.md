<properties
    pageTitle="Antes de implementar a Prova Conceito do Azure Stack | Microsoft Azure"
    description="Ver os requisitos de hardware e de ambiente da Prova de Conceito do Azure Stack (administrador de serviços)."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/29/2016"
    ms.author="erikje"/>


# Pré-requisitos da implementação do Azure Stack

Antes de implementar a POC ([Proof of Concept](azure-stack-poc.md) – Prova de Conceito) do Azure Stack, verifique se o computador cumpre os seguintes requisitos.
Estes requisitos aplicam-se apenas à Prova de Conceito do Azure Stack e podem ser alterados em versões futuras.

Poderá também considerar útil ver este vídeo tutorial sobre a implementação:

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-deployment-tutorial]

## Hardware

| Componente | Mínimo  | Recomendado |
|---|---|---|
| Unidades de disco: Sistema Operativo | 1 disco do SO com 200 GB disponíveis, no mínimo, para a partição do sistema (SSD ou HDD) | 1 disco do SO com 200 GB disponíveis, no mínimo, para a partição do sistema (SSD ou HDD) |
| Unidades de disco: Dados Gerais da Prova de Conceito do Azure Stack | 4 discos. Cada disco fornece um mínimo de 140 GB de capacidade (SSD ou HDD). Todos os discos disponíveis serão utilizados. | 4 discos. Cada disco fornece um mínimo de 250 GB de capacidade (SSD ou HDD). Todos os discos disponíveis serão utilizados.|
| Computação: CPU | Dual-Socket: 12 Núcleos Físicos (total)  | Dual-Socket: 16 Núcleos Físicos (total) |
| Computação: Memória | 96 GB de RAM  | 128 GB de RAM |
| Computação: BIOS | Hyper-V Ativado (com suporte SLAT)  | Hyper-V Ativado (com suporte SLAT) |
| Rede: NIC | Certificação Windows Server 2012 R2 necessária para a NIC; nenhuma funcionalidade específica necessária | Certificação Windows Server 2012 R2 necessária para a NIC; nenhuma funcionalidade específica necessária |
| Certificação de logótipo do HW | [Certificado para Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificado para Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

Pode utilizar o [Deployment Checker for Azure Stack Technical Preview 1 (Verificador de Implementação do Azure Stack Technical Preview 1)](https://gallery.technet.microsoft.com/Deployment-Checker-for-76d824e1) para confirmar os requisitos.

**Configuração da unidades de disco de dados:** todas as unidades de dados devem ser do mesmo tipo (todas SAS ou todas SATA) e com a mesma capacidade. Se forem utilizadas unidades de disco SAS, as unidades de disco devem ser ligadas através de um caminho único (sem MPIO; é fornecido suporte a caminhos múltiplos).

**Opções de configuração de HBA**
 
- (Preferencial) HBA Simples
- HBA RAID – O adaptador deve ser configurado no modo “Pass Through”
- HBA RAID – Os discos devem ser configurados como Disco Único, RAID-0

**Combinações suportadas de tipo de suporte de dados e de barramento**

-   HDD SATA

-   HDD SAS

-   HDD RAID

-   RAID SSD (se o tipo de suporte não for especificado/for desconhecido\*)

-   SSD SATA + HDD SATA

-   SSD SAS + HDD SAS

\* Os controladores RAID sem capacidade pass-through não conseguem reconhecer o tipo de suporte de dados. Esses controladores irão marcar ambas as unidades HDD e SSD como Não Especificadas. Nesse caso, a SSD será utilizada como armazenamento persistente, em vez de dispositivos de colocação em cache. Por conseguinte, pode implementar a Prova de Conceito do Microsoft Azure Stack nessas SSDs.

**Exemplo de HBAs**: LSI 9207-8i, LSI-9300-8i ou LSI-9265-8i no modo Pass Through

Estão disponíveis configurações do OEM de exemplo.




## Sistema operativo

| | **Requisitos**  |
|---|---|
| **Versão do SO** | Windows Server 2016 Datacenter Edition **Technical Preview 4** com as atualizações importantes mais recentes instaladas. Está incluído um WindowsServer2016Datacenter.vhdx no pacote de transferência. Pode arrancar neste VHDX e, em seguida, utilizá-lo como o sistema operativo base da sua implementação da Prova de Conceito do Azure Stack.|
| **Método de Instalação** | Instalação de raiz. Pode utilizar o WindowsServer2016Datacenter.vhdx incluído no pacote de implementação para instalar rapidamente o sistema operativo no computador da Prova de Conceito do Azure Stack. |
| **Domínio associado?** | Não. |


## Contas do Microsoft Azure Active Directory

1. Crie uma conta do Azure AD com privilégios de administrador de diretórios para, pelo menos, um Azure Active Directory. Se já tiver uma conta, pode utilizá-la. Caso contrário, pode criar uma gratuitamente em [http://azure.microsoft.com/pt-pt/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (na China, aceda antes a <http://go.microsoft.com/fwlink/?LinkID=717821>).

    Guarde estas credenciais para utilizá-las no passo 6 do tópico [Run the PowerShell deployment script (Executar o script de implementação do PowerShell)](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script). Esta conta de *administrador de serviços* permite configurar e gerir nuvens de recursos, contas de utilizador, planos de inquilino, quotas e preços. No portal, pode criar nuvens de sites, nuvens privadas de máquinas virtuais, criar planos e gerir subscrições do utilizador.

2. [Crie](azure-stack-add-new-user-aad.md), pelo menos, uma conta para poder iniciar sessão na Prova de Conceito do Azure Stack como inquilino.

  	| **Conta do Azure Active Directory**  | **Suportada?** |
  	|---|---| 
  	| ID da Organização com Subscrição Pública do Azure válida  | Sim |
  	| Conta da Microsoft com a Subscrição Pública do Azure válida  | Sim |
  	| ID da Organização com Subscrição do Azure válida na China  | Sim |
  	| ID da Organização com Subscrição do Azure válida na Administração Pública dos EUA  | Não |

>[AZURE.NOTE] A Prova de Conceito do Azure Stack apenas suporta a autenticação do Azure Active Directory.


## Rede

### Comutador

Uma porta disponível num comutador para o computador da Prova de Conceito.  

O computador da Prova de Conceito do Azure Stack suporte a ligação a uma porta de acesso por comutador ou porta de ramal. Não são necessárias quaisquer funcionalidades especializadas no comutador. Se estiver a utilizar uma porta de ramal ou se tiver de configurar um ID de VLAN, terá de fornecer o ID de VLAN como um parâmetro de implementação. Por exemplo:

    DeployAzureStack.ps1 –Verbose –PublicVLan 305

A especificação deste parâmetro apenas irá definir o ID de VLAN para o anfitrião e NATVM.

### Subrede

Não ligue o computador da Prova de Conceito às sub-redes 192.168.200.0/24, 192.168.100.0/24 ou 192.168.133.0/24. Estas estão reservadas para as redes internas do ambiente da Prova de Conceito do Microsoft Azure Stack.

### IPv4/IPv6

É apenas suportado o IPv4. Não pode criar redes IPv6.

### DHCP

Verifique se existe um servidor DHCP disponível na rede à qual liga o NIC. Se o DHCP não estiver disponível, terá de preparar uma rede IPv4 estática adicional para além daquela utilizada pelo anfitrião. Tem de fornecer esse endereço IP e gateway como um parâmetro de implementação. Por exemplo:

    DeployAzureStack.ps1 -Verbose -NATVMStaticIP 10.10.10.10/24 -NATVMStaticGateway 10.10.10.1

### Acesso à Internet

Verifique se o NIC consegue ligar à Internet. Tanto o IP do anfitrião como o novo IP atribuído ao NATVM (pelo DHCP ou IP estático) devem poder aceder à Internet. As portas 80 e 443 são utilizadas nos domínios graph.windows.net e login.windows.net.

### Proxy

Se for preciso um proxy no seu ambiente, especifique o endereço do servidor proxy e a porta como um parâmetro de implementação. Por exemplo:

    DeployAzureStack.ps1 -Verbose -ProxyServer 172.11.1.1:8080

A Prova de Conceito do Azure Stack não suporta a autenticação do proxy. 

### Telemetria

A porta 443 (HTTPS) tem de estar aberta para a sua rede. O ponto final do cliente é https://vortex-win.data.microsoft.com.


## Passos seguintes

[Transferir o pacote de implementação da Prova de Conceito do Azure Stack](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Implementar a Prova de Conceito do Azure Stack](azure-stack-run-powershell-script.md)



<!--HONumber=Sep16_HO3-->


