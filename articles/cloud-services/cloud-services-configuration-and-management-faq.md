---
title: "Problemas de configuração e gestão de FAQ de serviços do Microsoft Azure nuvem | Microsoft Docs"
description: "Este artigo apresenta uma lista de perguntas mais frequentes sobre a configuração e gestão de serviços de nuvem do Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: genli
ms.openlocfilehash: 2a20ee1df23df683c49444e8fb3ffdb2085b174f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de configuração e gestão do Cloud Services do Azure: Perguntas mais frequentes sobre (FAQ)

Este artigo inclui perguntas mais frequentes sobre problemas de configuração e gestão de [dos serviços de nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Também pode consultar o [página de tamanho de VM de serviços em nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>Como adicionar "nosniff" a minha Web site?
Para impedir que os clientes os tipos de MIME de interceção, adicione uma definição no seu *Web. config* ficheiro.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Também pode adicionar esta como uma definição no IIS. Utilize o seguinte comando com o [tarefas comuns de arranque](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artigo.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

## <a name="how-do-i-customize-iis-for-a-web-role"></a>Como personalizar o IIS para uma função web?
Utilize o script de arranque do IIS do [tarefas comuns de arranque](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artigo.

## <a name="i-cannot-scale-beyond-x-instances"></a>Posso não é possível escalar para além disso X instâncias
Subscrição do Azure tem um limite no número de núcleos que pode utilizar. Dimensionamento não funcionará se tiver utilizado a todos os núcleos disponíveis. Por exemplo, se tiver um limite de 100 núcleos, isto significa que pode ter 100 instâncias de máquina virtual de tamanho de A1 do serviço em nuvem ou um tamanho 50 A2 instâncias de máquina virtual.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Como posso implementar o acesso baseado em funções para serviços em nuvem?
Serviços em nuvem não suporta o modelo de controlo de acesso baseado em funções (RBAC), dado que não é um serviço do Azure Resource Manager com base.

Consulte [RBAC do Azure vs. os administradores da subscrição clássica](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Como se definir o tempo limite de inatividade para o Balanceador de carga do Azure?
Pode especificar o tempo limite no ficheiro de definição (. csdef) do serviço como esta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Consulte [novo: tempo limite de inatividade configuráveis, para o Balanceador de carga do Azure](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) para obter mais informações.

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Pode engenheiros de internos da Microsoft RDP para instâncias do serviço em nuvem sem permissão?
Microsoft segue um processo strict que não permitirá engenheiros internos para o RDP no seu serviço em nuvem sem a permissão de escrita (e-mail ou outras comunicações escrita) do proprietário ou as respetivas designee.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Por que motivo está incompleta a cadeia de certificados do meu certificado SSL do serviço de nuvem?
Recomendamos que os clientes instale a cadeia de certificados completo (certificado de folha, certificados intermédios e certificados de raiz) em vez de apenas o certificado de folha. Quando instalar apenas o certificado de folha, confiar no Windows para criar a cadeia de certificados orientando CTL. Se a problemas DNS de rede intermitente ou ocorrerem no Azure ou o Windows Update quando o Windows está a tentar validar o certificado, o certificado pode ser considerado inválido. Ao instalar a cadeia de certificados completa, pode ser evitado este problema. O blogue em [como instalar um certificado SSL em cadeia](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) mostra como efetuar esta ação.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Como associar a um endereço IP estático para o serviço em nuvem
Para configurar um endereço IP estático, tem de criar um IP reservado. Este IP reservado pode ser associado a um novo serviço de nuvem ou para uma implementação existente. Consulte os seguintes documentos para obter mais detalhes:
* [Como criar um endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [O endereço IP de um serviço em nuvem existente de reserva](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associar um IP reservado para um novo serviço em nuvem](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associar um IP reservado para uma implementação em execução](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associar um IP reservado para um serviço em nuvem, utilizando um ficheiro de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>O que é o limite de quota para o serviço em nuvem?
Consulte [específicos do serviço limita](../azure-subscription-service-limits.md#subscription-limits).

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Por que motivo a unidade de na minha VM de serviço de nuvem mostra pouco espaço livre em disco?
Este comportamento está previsto, e não deve fazer com que qualquer problema à sua aplicação. Registo em diário está ativado para o % uproot unidade % PaaS as VMs do Azure, que consome essencialmente duplo a quantidade de espaço que ficheiros normalmente demorar algum tempo. No entanto, existem vários aspetos a ter em atenção que, essencialmente, ative esta opção para um problema não.

O tamanho de unidade do % approot % é calculado como < tamanho de. cspkg + tamanho do diário máximo > + a margem de espaço livre, ou 1,5 GB, optando-se maior. O tamanho da VM não tem efeito neste cálculo. (O tamanho da VM afeta apenas o tamanho da unidade c: temporário.) 

-Não é suportado para escrever na unidade de % % approot. Se estiver a escrever à VM do Azure, deve fazê-num recurso LocalStorage temporário (ou outra opção, tais como o armazenamento de BLOBs, ficheiros do Azure, etc.). Por isso, a quantidade de espaço livre na pasta % approot % não é significativa. Se não tiver a certeza se a aplicação está a escrever para a unidade de % % approot, pode sempre permitir que o serviço executar alguns dias e, em seguida, compare o "antes" e "depois" tamanhos. 

Azure não irá escrever nada para a unidade de % % approot. Assim que o VHD é criado a partir do seu. cspkg e montado na VM do Azure, a única coisa que poderá escrever a esta unidade é a aplicação. 

As definições do diário de alterações são não configuráveis, pelo que não pode desativá-la.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quais são as funcionalidades e capacidades que básico do Azure IPS IDS e DDOS fornece?
O Azure tem IPS/IDS em servidores físicos para se Defender contra ameaças do Centro de dados. Além disso, os clientes podem implementar soluções de segurança de terceiros, tais como firewalls de aplicação web, firewalls de rede, antimalware, deteção de intrusões, sistemas de prevenção (IDS/IPS) e muito mais. Para obter mais informações, consulte [proteger os seus dados e recursos e está em conformidade com as normas de segurança global](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft monitoriza continuamente os servidores, redes e aplicações para detetar ameaças. Deteção de intrusão do Azure multipronged ameaça-gestão abordagem utiliza, distribuída denial of service (DDoS distribuídos) prevenção de ataque, análise de teste, comportamental penetração, deteção de anomalias e machine learning para constantemente reforçar a respetiva defesa e reduzir riscos. Antimalware da Microsoft para o Azure protege máquinas virtuais e serviços em nuvem do Azure. Tem a opção para implementar soluções de segurança de terceiros Além disso, tais como planos laterais fire da aplicação web, as firewalls de rede, antimalware, intrusões deteção e prevenção sistemas (IDS/PIS) e muito mais.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Por que motivo é que o IIS parar escrita para o diretório de registo?
Ter esgotado a quota de armazenamento local para escrever para o diretório de registo. Para corrigir isto, pode efetuar um dos três coisas:
* Ative os diagnósticos para o IIS e o diagnóstico periodicamente movido para o armazenamento de Blobs.
* Remova manualmente os ficheiros de registo do diretório de registo.
* Aumente o limite de quota de recursos locais.

Para obter mais informações, consulte os seguintes documentos:
* [Armazenar e ver dados de diagnósticos no Armazenamento do Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Registos de IIS para escrever num serviço em nuvem](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>O que é o objetivo de "Windows Azure ferramentas de encriptação para as extensões do certificado"?
Estes certificados são criados automaticamente sempre que uma extensão é adicionada ao serviço em nuvem. Normalmente, trata-se a extensão WAD ou a extensão RDP, mas poderá ser outros recursos, tais como a extensão de Antimalware ou Recoletor de registos. Estes certificados são utilizados apenas para a encriptação e desencriptação da configuração privada da extensão. A data de expiração nunca é verificada, pelo que é irrelevante se o certificado expirou. 

Pode ignorar estes certificados. Se pretende limpar os certificados, pode tentar eliminar todos eles. Azure irá gerar um erro se tentar eliminar um certificado que está a ser utilizado.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Como gerar um certificado de assinatura do pedido (CSR) sem "RDP utilize" para a instância?
Consulte o documento de orientação seguinte:

>[Obter um certificado para utilizar com Web Sites do Windows Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Tenha em atenção que um CSR é apenas um ficheiro de texto. Não tem a ser criado a partir do computador onde o certificado, fundamentalmente, será utilizado. Embora este documento foi escrito para um serviço de aplicações, a criação de CSR é genérica e aplica-se também para serviços em nuvem.

## <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Como adicionar uma extensão de Antimalware para os meus serviços em nuvem de uma forma automática?

Pode ativar a extensão de Antimalware utilizando o script do PowerShell na tarefa de arranque. Siga os passos descritos nestes artigos para implementá-las: 
 
- [Criar uma tarefa de arranque do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Conjunto AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Para obter mais informações sobre cenários de implementação de Antimalware e como ativá-la a partir do portal, consulte [cenários de implementação de Antimalware](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

## <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Como ativar a indicação de nome de servidor (SNI) para serviços em nuvem?

Pode ativar SNI nos serviços em nuvem, utilizando um dos seguintes métodos:

### <a name="method-1-use-powershell"></a>Método 1: Utilizar o PowerShell

O enlace de SNI pode ser configurado utilizando o cmdlet PowerShell **New-WebBinding** numa tarefa de arranque para uma instância de função de serviço em nuvem como abaixo:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Conforme descrito [aqui](https://technet.microsoft.com/library/ee790567.aspx), o $sslFlags pode ser um dos valores da seguinte forma:

|Valor|Significado|
------|------
|0|Não existem SNI|
|1|SNI ativado |
|2 |Não SNI enlace que utiliza o arquivo de certificados Central|
|3|Armazenar o enlace de SNI que utiliza certificados Central |
 
### <a name="method-2-use-code"></a>Método 2: Código de utilização

O enlace de SNI também foi configurado através de código no arranque de função, tal como descrito neste [blogue](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Utilizar uma das abordagens acima, os respetivos certificados (*.pfx) para os nomes de anfitriões específicos tem de ser instalada pela primeira vez as instâncias de função através de uma tarefa de arranque ou através de código para que o enlace de SNI ser eficaz.

## <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Como adicionar etiquetas ao meu serviço de nuvem do Azure? 

Serviço em nuvem é um recurso de clássico. Apenas os recursos criados através de etiquetas de suporte do Azure Resource Manager. Não é possível aplicar etiquetas a recursos de clássico, como o serviço em nuvem. 

## <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Quais são as capacidades futuras do serviço em nuvem no Portal do Azure que pode ajudar a gerir e monitorizar as aplicações?

* Capacidade de gerar um novo certificado para o protocolo de ambiente de trabalho remoto (RDP) está disponível em breve. Em alternativa, pode executar este script:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
* Capacidade de escolher o blob ou local para o seu csdef e cscfg carregar localização estará disponível brevemente. Utilizar [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-4.0.0), pode definir cada valor de localização.
* Capacidade de monitorizar as métricas ao nível da instância. Capacidades de monitorização adicionais estão disponíveis no [como os serviços de nuvem de Monitor](cloud-services-how-to-monitor.md).


## <a name="how-to-enable-http2-on-cloud-services-vm"></a>Como ativar o HTTP/2 na VM de serviços na nuvem?

Windows 10 e Windows Server 2016 vêm com suporte para HTTP/2 no lado do cliente e servidor. Se o cliente (browser) está a ligar para o servidor IIS sobre TLS que negoceia HTTP/2 através de extensões TLS, então, não terá de fazer qualquer alteração no lado do servidor. Isto acontece porque, ao longo do TLS, o cabeçalho de h2 14 especificando a utilização de HTTP/2 é enviado por predefinição. Se outro lado do cliente está a enviar um cabeçalho de atualização para atualizar para HTTP/2, em seguida, terá de efetuar a alteração abaixo no lado do servidor para garantir que funciona a atualização e acaba por ficar com uma ligação de HTTP/2. 

1. Execute regedit.exe.
2. Navegue até à chave de registo: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Criar um novo valor DWORD denominado **DuoEnabled**.
4. Defina o respetivo valor como 1.
5. Reinicie o servidor.
6. Aceda ao seu **Web Site predefinido** e, em **enlaces**, crie um novo enlace de TLS com o certificado autoassinado que acabou de criar. 

Para obter mais informações, consulte:

- [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2)
- [Vídeo: HTTP/2 no Windows 10: Browser, aplicações e o servidor Web](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Tenha em atenção que os passos acima podem ser automatizados através de uma tarefa de arranque para que sempre que é criada uma nova instância de PaaS, pode fazer alterações acima no registo do sistema. Para obter mais informações, consulte [como configurar e executar tarefas de arranque para um serviço em nuvem](cloud-services-startup-tasks.md).

 
Assim que tiver sido realizada, pode verificar se foi ativado HTTP/2 ou não utilizando um dos seguintes métodos:

- Ativar a versão de protocolo nos registos do IIS e procure para os registos IIS. Esta operação irá mostrar HTTP/2 nos registos. 
- Ativar a ferramenta do programador F12 no Internet Explorer/Edge e mude para o separador de rede para verificar o protocolo. 

Para obter mais informações, consulte [HTTP/2 no IIS](https://blogs.iis.net/davidso/http2).
