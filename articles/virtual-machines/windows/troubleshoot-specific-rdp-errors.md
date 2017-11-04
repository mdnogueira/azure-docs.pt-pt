---
title: "As mensagens de erro RDP específicas para as VMs do Azure | Microsoft Docs"
description: "Compreender as mensagens de erro específicas que poderá receber quando tenta utilizam a ligação de ambiente de trabalho remoto para uma máquina virtual do Windows no Azure"
keywords: "Erro de ambiente de trabalho remoto, erro de ligação de ambiente de trabalho remoto, não é possível ligar à VM, resolução de ambiente de trabalho remoto"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 8eb4420978c0a66012f3f46d4a5f7ed0da56a3b7
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Resolução de problemas específicas mensagens de erro RDP a uma VM do Windows no Azure
Poderá receber uma mensagem de erro específico ao utilizar a ligação de ambiente de trabalho remoto para uma máquina virtual (VM) do Windows no Azure. Este artigo fornece detalhes sobre algumas das mensagens de erro mais comuns encontradas, juntamente com os passos para resolvê-los de resolução de problemas. Se estiver a ter problemas em ligar à VM através de RDP mas efetue não encontrar uma mensagem de erro específica, consulte o [guia para o ambiente de trabalho remoto de resolução de problemas](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter informações sobre mensagens de erro específicas, consulte o seguinte:

* [A sessão remota foi desligada porque existem não existem servidores de licenciamento de ambiente de trabalho remoto disponíveis para fornecer uma licença](#rdplicense).
* [Ambiente de trabalho remoto não é possível localizar o computador "name"](#rdpname).
* [Ocorreu um erro de autenticação. Não é possível contactar a autoridade de segurança Local](#rdpauth).
* [Erro de segurança do Windows: as suas credenciais não foi possível funcionar](#wincred).
* [Este computador não é possível ligar ao computador remoto](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A sessão remota foi desligada porque existem não existem servidores de licenciamento de ambiente de trabalho remoto disponíveis para fornecer uma licença.
Causa: O período de tolerância licenciamento 120 dias para a função de servidor de ambiente de trabalho remoto expirou e tem de instalar licenças.

Como solução, guardar uma cópia local do ficheiro RDP a partir do portal e execute este comando numa linha de comandos do PowerShell para estabelecer a ligação. Este passo desativa o licenciamento de apenas essa ligação:

        mstsc <File name>.RDP /admin

Se, na verdade, não precisa de mais de duas ligações de ambiente de trabalho remoto em simultâneo para a VM, pode utilizar o Gestor de servidor para remover a função de servidor de ambiente de trabalho remoto.

Para obter mais informações, consulte a mensagem de blogue [VM do Azure falha com "Nenhum ambiente de trabalho licenças servidores remotos disponíveis"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Ambiente de trabalho remoto não é possível localizar o computador "name".
Causa: O cliente de ambiente de trabalho remoto no seu computador não é possível resolver o nome do computador em que as definições do ficheiro RDP.

Possíveis soluções:

* Se estiver na intranet da organização, certifique-se de que o computador tem acesso ao servidor proxy e pode enviar o tráfego HTTPS ao mesmo.
* Se estiver a utilizar um ficheiro RDP armazenado localmente, tente utilizar o que é gerado pelo portal. Este passo garante que tem o nome DNS correto para a máquina virtual, ou o serviço em nuvem e a porta do ponto final da VM. Segue-se um ficheiro RDP de exemplo gerado pelo portal:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

A parte do endereço deste ficheiro RDP tem:

* O nome de domínio completamente qualificado do serviço em nuvem que contém a VM ("tailspin-azdatatier.cloudapp.net" neste exemplo).
* A porta TCP externa do ponto final para o tráfego de ambiente de trabalho remoto (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ocorreu um erro de autenticação. Não é possível contactar a autoridade de segurança Local.
Causa: O destino da VM não é possível localizar a autoridade de segurança na parte do nome de utilizador das credenciais.

Quando o seu nome de utilizador está no formato *SecurityAuthority*\\*UserName* (exemplo: CORP\User1), o *SecurityAuthority* parte é o nome do computador da VM (para a autoridade de segurança local) ou um nome de domínio do Active Directory.

Possíveis soluções:

* Se a conta for local para a VM, certifique-se de que o nome da VM está escrito corretamente.
* Se a conta estiver num domínio do Active Directory, verifique a ortografia do nome de domínio.
* Se for uma conta de domínio do Active Directory e o nome de domínio está escrito corretamente, certifique-se de que está disponível um controlador de domínio nesse domínio. É um problema comum verificado em redes virtuais do Azure que contém os controladores de domínio que um controlador de domínio está indisponível porque não foi iniciado. Como solução, pode utilizar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Erro de segurança do Windows: não foi possível funcionar as suas credenciais.
Causa: O destino da VM não é possível validar o nome de conta e palavra-passe.

Um computador baseado em Windows pode validar as credenciais de uma conta local ou uma conta de domínio.

* Para contas locais, utilize o *ComputerName*\\*UserName* sintaxe (exemplo: SQL1\Admin4798).
* Para contas de domínio, utilize o *DomainName*\\*UserName* sintaxe (exemplo: CONTOSO\peterodman).

Se tem de ter promovido a VM para um controlador de domínio numa floresta nova do Active Directory, a conta de administrador local com que iniciou sessão é convertida para uma conta equivalente com a mesma palavra-passe na nova floresta e no domínio. A conta local, em seguida, é eliminada.

Por exemplo, se tiver iniciado sessão com a conta local DC1\DCAdmin e, em seguida, promovido a máquina virtual como controlador de domínio numa floresta nova do domínio corp.contoso.com, a conta local DC1\DCAdmin é eliminada e é criada uma nova conta de domínio (CORP\DCAdmin) com a mesma palavra-passe.

Certifique-se de que o nome da conta é um nome que a máquina virtual pode verificar como uma conta válida e de que a palavra-passe está correta.

Se precisar de alterar a palavra-passe da conta de administrador local, consulte [como repor uma palavra-passe ou o serviço de ambiente de trabalho remoto para máquinas virtuais Windows](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Este computador não é possível ligar ao computador remoto.
Causa: A conta que é utilizada para ligar não tem direitos de início de sessão de ambiente de trabalho remoto.

Cada computador com o Windows tem um grupo de local da utilizadores ambiente de trabalho remoto, que contém as contas e grupos que podem iniciar sessão no-lo remotamente. Os membros do grupo de administradores locais têm também acesso, mesmo que essas contas não estão listadas no grupo local de utilizadores do ambiente de trabalho remoto. Para máquinas associados a um domínio, o grupo de administradores locais também contém os administradores de domínio para o domínio.

Certifique-se de que a conta que está a utilizar para estabelecer ligação com tem direitos de início de sessão de ambiente de trabalho remoto. Como solução, utilize um domínio ou conta de administrador local para ligar através de ambiente de trabalho remoto. Para adicionar a conta pretendida para o grupo local de utilizadores do ambiente de trabalho remoto, utilize o snap-in Consola de gestão da Microsoft (**ferramentas do sistema > utilizadores e grupos locais > grupos > Remote Desktop Users**).

## <a name="next-steps"></a>Passos seguintes
Se nenhum destes erros ocorreu e tiver um desconhecido emitir com ligar através de RDP, consulte o [guia para o ambiente de trabalho remoto de resolução de problemas](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para aceder a aplicações em execução numa VM passos de resolução de problemas, consulte [resolver problemas de acesso a uma aplicação em execução numa VM do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se estiver a ter problemas utilizando Secure Shell (SSH) para ligar a uma VM com Linux no Azure, consulte [resolver problemas de SSH ligações a uma VM com Linux no Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

