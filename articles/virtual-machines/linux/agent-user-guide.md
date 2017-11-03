---
title: "Descrição geral do agente de VM Linux do Azure | Microsoft Docs"
description: "Saiba como instalar e configurar o agente Linux (waagent) para gerir a interação da sua máquina virtual com o controlador de recursos de infraestrutura do Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: szark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 486ad6bb148583a957fb82b7954ff94f853b12cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Compreender e utilizar o agente Linux do Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introdução
O agente de Linux do Microsoft Azure (waagent) gere Linux e FreeBSD aprovisionamento e interação de VM com o controlador de recursos de infraestrutura do Azure. Fornece as seguintes funcionalidades para Linux e FreeBSD IaaS implementações:

> [!NOTE]
> Consulte o agente Linux do Azure [Leia-me](https://github.com/Azure/WALinuxAgent/blob/master/README.md) para obter detalhes adicionais.
> 
> 

* **Imagem de aprovisionamento**
  
  * Criação de uma conta de utilizador
  * Configurar tipos de autenticação SSH
  * Implementação de chaves públicas SSH e pares de chaves
  * A definição do nome de anfitrião
  * O nome de anfitrião de publicação para a plataforma de DNS
  * Relatórios por impressão digital de chave de anfitrião do SSH para a plataforma
  * Gestão de discos de recursos
  * Formatação e montar o disco de recursos
  * Configurar o espaço de comutação
* **Redes**
  
  * Gere as rotas para melhorar a compatibilidade com servidores DHCP de plataforma
  * Garante a estabilidade do nome de interface de rede
* **Kernel**
  
  * Configura o virtual NUMA (desativar kernel < 2.6.37)
  * Consome entropia de Hyper-V para /dev/random
  * Configura os tempos limite de SCSI para o dispositivo de raiz (o que pode ser remoto)
* **Diagnóstico**
  
  * Redirecionamento de consola para a porta série
* **Implementações do SCVMM**
  
  * Deteta e bootstraps o agente do VMM para Linux quando executado num ambiente do System Center Virtual Machine Manager 2012 R2
* **Extensão da VM**
  
  * Inserir componentes criados pela Microsoft e parceiros, numa VM com Linux (IaaS) para ativar o software e automatização de configuração
  * Implementação de referência de extensão da VM no [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Comunicação
O fluxo de informações da plataforma para o agente ocorre através de dois canais:

* Um momento do arranque ligados DVD para implementações IaaS. DVD inclui um ficheiro de configuração compatível com OVF, que inclui todas as informações de aprovisionamento que não seja o keypairs SSH real.
* Um ponto final TCP expor uma API REST utilizado para obter a implementação e configuração da topologia.

## <a name="requirements"></a>Requisitos
Os seguintes sistemas foram testados e são conhecidos para trabalhar com o agente Linux do Azure:

> [!NOTE]
> Esta lista pode diferir da lista oficial de sistemas suportados na plataforma Microsoft Azure, conforme descrito aqui: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Outros sistemas suportados:

* FreeBSD 10 + (agente Linux do Azure v2.0.10 +)

O agente Linux depende de alguns pacotes de sistema para funcionar corretamente:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Utilitários de sistema de ficheiros: sfdisk fdisk, mkfs, parted
* Ferramentas de palavra-passe: chpasswd, sudo
* Ferramentas de processamento de texto: PO, grep
* Ferramentas de rede: rota ip
* Suporta kernel para montar a sistemas de ficheiros instalados UDF.

## <a name="installation"></a>Instalação
Instalação a utilizar um RPM ou um pacote de DEB no repositório de pacotes a distribuição é o método preferencial de instalar e atualizar o agente Linux do Azure. Todos os o [aprovadas fornecedores de distribuição](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) integrar o pacote do agente Linux do Azure, as imagens e repositórios.

Consulte a documentação do [repo do agente Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent) para opções de instalação avançadas, como a instalação de origem ou para localizações personalizadas ou prefixos.

## <a name="command-line-options"></a>Opções de linha de comandos
### <a name="flags"></a>Sinalizadores
* verboso: aumentar a verbosidade do comando especificado
* forçar: Ignorar confirmação interativa para alguns comandos

### <a name="commands"></a>Comandos
* ajudar a: lista os comandos suportados e os sinalizadores.
* deprovision: tentativa de limpar o sistema e torná-lo adequado para o aprovisionamento de novamente. Esta operação eliminar o seguinte:
  
  * Todas as chaves de anfitrião SSH (se Provisioning.RegenerateSshHostKeyPair 'y' no ficheiro de configuração)
  * Configuração de NameServer no /etc/resolv.conf
  * Palavra-passe de raiz do /etc/shadow (se Provisioning.DeleteRootPassword 'y' no ficheiro de configuração)
  * Concessões de cliente DHCP em cache
  * Repõe o nome do anfitrião para localhost.localdomain

> [!WARNING]
> Desaprovisionamento não garante que a imagem é limpa de todas as informações confidenciais e adequado para a redistribuição.
> 
> 

* deprovision + utilizador: efetua tudo em - deprovision (acima) e também elimina a última conta de utilizador aprovisionado (obtida /var/lib/waagent) e dados associados. Este parâmetro é quando uma imagem que foi anteriormente aprovisionamento no Azure, pelo que podem ser capturada e novamente utilizado de aprovisionamento automatizados.
* versão: apresenta a versão de waagent
* serialconsole: Configura GRUB para marcar ttyS0 (a porta série primeiro) como a consola de arranque. Isto garante que os registos do kernel lido são enviados para a porta série e disponibilizados para depuração.
* o daemon: executar waagent como um daemon para gerir a interação com a plataforma. Este argumento é especificado para waagent no waagent init script.
* iniciar: executar waagent como um processo em segundo plano

## <a name="configuration"></a>Configuração
Um ficheiro de configuração (/ etc/waagent.conf) controla as ações de waagent. Um ficheiro de configuração de exemplo é mostrado abaixo:

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None

As várias opções de configuração são descritas detalhadamente abaixo. Opções de configuração são dos três tipos; Valor booleano, cadeia ou número inteiro. As opções de configuração booleano podem ser especificadas como "y" ou "n". A especial palavra-chave "None" podem ser utilizadas para alguns cadeia tipo entradas de configuração conforme especificado abaixo.

**Provisioning.Enabled:**  
Tipo: booleano  
Predefinição: y

Isto permite ao utilizador ativar ou desativar a funcionalidade de aprovisionamento no agente. Os valores válidos são "y" ou "n". Se o aprovisionamento estiver desativado, chaves de anfitrião e o utilizador SSH na imagem são preservadas e qualquer configuração especificada no Azure API de aprovisionamento é ignorada.

> [!NOTE]
> O `Provisioning.Enabled` por predefinição, parâmetro "n" no Ubuntu nuvem imagens que utilizam a nuvem init para o aprovisionamento.
> 
> 

**Provisioning.DeleteRootPassword:**  
Tipo: booleano  
Predefinição: n

Se o conjunto, a palavra-passe de raiz no ficheiro de sombra etc/é apagado durante o processo de aprovisionamento.

**Provisioning.RegenerateSshHostKeyPair:**  
Tipo: booleano  
Predefinição: y

Se o conjunto de todos os SSH anfitrião pares de chaves (ecdsa, dsa e rsa) é eliminado durante o processo de aprovisionamento do /etc/hosts ssh /. E é gerado um par de chaves de raiz único.

O tipo de encriptação para o par de chaves de raiz é configurável pela entrada Provisioning.SshHostKeyPairType. Tenha em atenção que algumas distribuições recriará pares de chaves SSH para todos os tipos de encriptação em falta quando o daemon de SSH é reiniciado (por exemplo, após um reinício).

**Provisioning.SshHostKeyPairType:**  
Tipo: Cadeia  
Predefinição: rsa

Isto pode ser definido como um tipo de algoritmos de encriptação é suportado pelo daemon de SSH na máquina virtual. Os valores normalmente suportados são "rsa", "dsa" e "ecdsa". Tenha em atenção que "putty.exe" no Windows não suporta "ecdsa". Por isso, se pretender utilizar putty.exe no Windows para ligar a uma implementação de Linux, utilize "rsa" ou "dsa".

**Provisioning.MonitorHostName:**  
Tipo: booleano  
Predefinição: y

Se definido, waagent irá monitorizar a máquina virtual do Linux para que as alterações de nome de anfitrião (tal como devolvido pelo comando "hostname") e atualizar automaticamente a configuração de rede na imagem para refletir a alteração. Para enviar a alteração de nome para os servidores DNS, rede será reiniciada na máquina virtual. Este procedimento resultará na brief perda de conectividade à Internet.

**Provisioning.DecodeCustomData**  
Tipo: booleano  
Predefinição: n

Se definido, waagent será descodificar CustomData a partir de Base64.

**Provisioning.ExecuteCustomData**  
Tipo: booleano  
Predefinição: n

Se definido, waagent executará CustomData após o aprovisionamento.

**Provisioning.PasswordCryptId**  
Tipo: cadeia  
Predefinição: 6

Algoritmo utilizado pela crypt ao gerar o hash de palavra-passe.  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
Tipo: cadeia  
Predefinição: 10

Comprimento de salt aleatório utilizado ao gerar o hash de palavra-passe.

**ResourceDisk.Format:**  
Tipo: booleano  
Predefinição: y

Se definido, o disco de recursos fornecido pela plataforma do será formatado e montado pelas waagent se o tipo de sistema de ficheiros pedido pelo utilizador no "ResourceDisk.Filesystem" é diferente de "ntfs". Uma única partição do tipo Linux (83) será disponibilizada no disco. Tenha em atenção que esta partição não será formatada se podem ser montados com êxito.

**ResourceDisk.Filesystem:**  
Tipo: Cadeia  
Predefinição: ext4

Esta ação Especifica o tipo de sistema de ficheiros para o disco de recursos. Os valores suportados variam consoante a distribuição de Linux. Se a cadeia é X, em seguida, mkfs. X deve estar presente na imagem de Linux. SLES 11 imagens, normalmente, devem utilizar 'ext3'. Imagens de FreeBSD devem utilizar aqui 'ufs2'.

**ResourceDisk.MountPoint:**  
Tipo: Cadeia  
Predefinição: recursos/mnt / 

Esta ação Especifica o caminho no qual o disco de recursos está montado. Tenha em atenção que o disco de recursos é um *temporário* disco e poderá ser esvaziada quando a VM é desaprovisionada.

**ResourceDisk.MountOptions**  
Tipo: Cadeia  
Predefinição: nenhuma

Especifica opções de montagem do disco a ser transmitido para o comando -o de montagem. Esta é uma lista separada por vírgulas de valores, por ex. 'nodev, nosuid'. Consulte mount(8) para obter mais detalhes.

**ResourceDisk.EnableSwap:**  
Tipo: booleano  
Predefinição: n

Se definir um ficheiro de comutação (/ swapfile) é criado no disco recursos e adicionado para o espaço de comutação do sistema.

**ResourceDisk.SwapSizeMB:**  
Tipo: número inteiro  
Predefinição: 0

O tamanho do ficheiro de comutação em megabytes.

**Logs.Verbose:**  
Tipo: booleano  
Predefinição: n

Se o conjunto de verbosidade do registo é elevado. Waagent regista /var/log/waagent.log e tira partido da funcionalidade de logrotate sistema para rodar os registos.

**SO. EnableRDMA**  
Tipo: booleano  
Predefinição: n

Se definido, o agente irá tentar instalar e, em seguida, carregar um controlador de kernel RDMA que corresponde à versão de firmware no hardware subjacente.

**SO. RootDeviceScsiTimeout:**  
Tipo: número inteiro  
Predefinição: 300

Esta ação configura o limite de tempo de SCSI em segundos em unidades de dados e de disco do SO. Se não for definido, o sistema são utilizadas as predefinições.

**SO. OpensslPath:**  
Tipo: Cadeia  
Predefinição: nenhuma

Isto pode ser utilizado para especificar um caminho alternativo para openssl binário a utilizar para operações de criptografia.

**HttpProxy.Host, HttpProxy.Port**  
Tipo: Cadeia  
Predefinição: nenhuma

Se definido, o agente irá utilizar este servidor proxy para aceder à internet. 

## <a name="ubuntu-cloud-images"></a>Ubuntu nuvem imagens
Tenha em atenção que utilizar imagens de nuvem Ubuntu [nuvem init](https://launchpad.net/ubuntu/+source/cloud-init) para efetuar várias tarefas de configuração que caso contrário, deverá ser geridas pelo agente Linux do Azure.  Tenha em atenção as seguintes diferenças:

* **Provisioning.Enabled** por predefinição, "n" no Ubuntu nuvem imagens que utilizam a nuvem init para efetuar tarefas de aprovisionamento.
* Os seguintes parâmetros de configuração não tem qualquer efeito nas imagens de nuvem de Ubuntu utilizar init da nuvem para gerir o disco de recursos e espaço de comutação:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**
* Consulte os seguintes recursos para configurar o ponto de montagem do disco de recursos e espaço no Ubuntu nuvem imagens de comutação durante o aprovisionamento:
  
  * [Ubuntu Wiki: Configurar partições de comutação](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Dados personalizados inserirem-se para uma Máquina Virtual do Azure](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

