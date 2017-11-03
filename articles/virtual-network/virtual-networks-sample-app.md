---
title: "Aplicação de exemplo do Azure para utilização com DMZ | Microsoft Docs"
description: "Implementar esta aplicação web simples depois de criar uma rede de Perímetro para testar cenários de fluxo de tráfego"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 60340ab7-b82b-40e0-bd87-83e41fe4519c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 8506238e41c5d9dac8d76d729d4919b30a0528b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sample-application-for-use-with-dmzs"></a>Exemplo de aplicação para utilização com DMZ
[Regressar à página de segurança limites melhores práticas][HOME]

Estes scripts PowerShell podem ser executados localmente nos servidores de IIS01 e AppVM01 para instalar e configurar uma aplicação web simples que apresenta uma página html a partir do servidor de front-end IIS01 com conteúdo do servidor back-end AppVM01.

Esta aplicação fornece um ambiente de teste simples para muitos os exemplos de rede de Perímetro e como as alterações no pontos finais, NSGs, UDR e Firewall regras podem afetar os fluxos de tráfego.

## <a name="firewall-rule-to-allow-icmp"></a>Regra de firewall para permitir o protocolo ICMP
Esta declaração de PowerShell simple pode ser executada em qualquer VM do Windows para permitir tráfego ICMP (Ping). Esta atualização de firewall permite a mais fácil de testes e resolução de problemas, permitindo que o protocolo de ping para passar a firewall do windows (para a maioria das Linux distros que ICMP está ativada por predefinição).

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Se utilizar os scripts seguintes, esta adição da regra de firewall é a primeira instrução.

## <a name="iis01---web-application-installation-script"></a>IIS01 - o script de instalação de aplicações Web
Este script irão:

1. Abrir IMCPv4 (Ping) na firewall do windows local server para testar a mais fácil
2. Instalar o IIS e o .net Framework 4.5
3. Criar uma página web do ASP.NET e Web. config
4. Alterar o conjunto aplicacional predefinido para facilitar o acesso a ficheiros
5. Definir o utilizador anónimo para a conta de administrador e a palavra-passe

Este script do PowerShell deve ser executado localmente enquanto RDP tinha em IIS01.

```PowerShell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Clasic Pipeline to remote file access will work easier
    Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - o script de instalação do servidor de ficheiros
Este script configura o back-end para esta aplicação simple. Este script irão:

1. Abrir IMCPv4 (Ping) na firewall para fins de teste mais fácil
2. Criar um diretório para o web site
3. Criar um ficheiro de texto para ser remotamente acesso por página web
4. Defina as permissões do diretório e o ficheiro para anónimo para permitir o acesso
5. Desativar a segurança avançada do IE para permitir a navegação mais fácil do servidor 

> [!IMPORTANT]
> **Melhor prática**: nunca desativar a segurança avançada do IE num servidor de produção e é, geralmente, uma ideia incorreta navegue web a partir de um servidor de produção. Além disso, a abertura de partilhas de ficheiros para acesso anónimo é incorreto, mas já está aqui de simplicidade.
> 
> 

Este script do PowerShell deve ser executado localmente enquanto RDP tinha em AppVM01. PowerShell é necessário para executar como administrador para garantir uma execução bem sucedida.

```PowerShell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 - o script de instalação do servidor DNS
Não há nenhum script incluído nesta aplicação de exemplo para configurar o servidor DNS. Se testar regras de firewall, NSG ou UDR precisa de incluir o tráfego DNS, o servidor de DNS01 tem de ser configurado manualmente. O ficheiro xml de configuração de rede e o modelo do Resource Manager para ambos os exemplos incluem DNS01 como o servidor DNS primário e o servidor DNS público alojadas pelo nível 3 como servidor de cópia de segurança de DNS. O servidor de nível 3 DNS teria de ser o servidor DNS real utilizado para o tráfego não local e com DNS01 não o programa de configuração, não há redes locais ocorreriam DNS.

## <a name="next-steps"></a>Passos seguintes
* Execute o script de IIS01 num servidor IIS
* Executar script de servidor de ficheiros em AppVM01
* Navegue para o IP público na IIS01 para validar a sua compilação

<!--Link References-->
[HOME]: ../best-practices-network-security.md
