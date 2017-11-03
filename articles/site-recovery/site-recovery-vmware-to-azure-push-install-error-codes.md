---
title: "Azure Site Recovery resolução de problemas do VMware para Azure | Microsoft Docs"
description: "Resolver erros ao replicar máquinas virtuais do Azure"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/28/2017
ms.author: asgang
ms.openlocfilehash: b7b03442ba815c86e5defa1018b66f56c0b379df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Resolver problemas de instalação push do serviço de mobilidade

Este artigo descreve problemas comuns que poderá enfrentam quando tentar instalar o serviço de mobilidade do Azure Site Recovery no servidor de origem para ativar a proteção.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Erro 78007 - não foi possível concluir a operação pedida
Este erro pode ser emitido pelo serviço por vários motivos. Escolha o erro do fornecedor correspondente profundidade.

* [Erro 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [Erro 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [Erro 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [Erro 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [Erro 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [Erro 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [Erro 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [Erro 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>Erro 95105 - não foi possível proteção ativada (EP0856)

**Código de erro** | **Causas possíveis** | **Recomendações de erro específico**
--- | --- | ---
95105 </br>**Mensagem:** a instalação Push do serviço de mobilidade na máquina de origem falhou com o código de erro **EP0856**. <br> O **impressora partilha de ficheiros e** não é permitida na origem de máquinas ou há são rede problemas de conectividade entre o servidor de processos e a máquina de origem.| **Impressora partilha de ficheiros e** não está ativada. | Permitir **impressora partilha de ficheiros e** na máquina de origem na Firewall do Windows. Na máquina de origem, em **Firewall do Windows** > **permitir que uma aplicação ou funcionalidade através da Firewall**, selecione **impressora partilha para todos os perfis de ficheiros e**. </br> Além disso, verifique os pré-requisitos seguintes para concluir com êxito a instalação de push.<br> Leia mais sobre [isssues WMI de resolução de problemas](#troubleshoot-wmi-issues)


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Erro 95107 - não foi possível proteção ativada (EP0858)

**Código de erro** | **Causas possíveis** | **Recomendações de erro específico**
--- | --- | ---
95107 </br>**Mensagem:** a instalação Push do serviço de mobilidade na máquina de origem falhou com o código de erro **EP0858**. <br> As credenciais fornecidas para instalar o serviço de mobilidade estão incorretas ou a conta de utilizador não tem privilégios suficientes. | Credenciais de utilizador fornecidas para instalar o serviço de mobilidade na máquina de origem estão incorretas. | Certifique-se de que as credenciais de utilizador fornecidas para a máquina de origem no servidor de configuração estão corretas. <br> Para adicionar/editar credenciais de utilizador, vá para o servidor de configuração e selecionar **Cspsconfigtool** > **gerir conta**. </br> Além disso, verifique o seguinte [pré-requisitos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) para concluir com êxito a instalação de push.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Erro 95117 - não foi possível proteção ativada (EP0865)

**Código de erro** | **Causas possíveis** | **Recomendações de erro específico**
--- | --- | ---
95117 </br>**Mensagem:** a instalação Push do serviço de mobilidade na máquina de origem falhou com o código de erro **EP0865**. <br> A máquina de origem não está em execução ou existem problemas de conectividade de rede entre o servidor de processos e a máquina de origem. | Problemas de conectividade de rede entre o servidor de processos e o servidor de origem. | Verifique a conectividade entre o servidor de processos e o servidor de origem. </br> Além disso, verifique o seguinte [pré-requisitos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) para concluir com êxito a instalação de push.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Erro 95103 - não foi possível proteção ativada (EP0854)

**Código de erro** | **Causas possíveis** | **Recomendações de erro específico**
--- | --- | ---
95103 </br>**Mensagem:** a instalação Push do serviço de mobilidade na máquina de origem falhou com o código de erro **EP0854**. <br> O Windows Management Instrumentation (WMI) não é permitida na máquina de origem ou existem problemas de conectividade de rede entre o servidor de processos e a máquina de origem.| WMI está bloqueado na Firewall do Windows. | Permitir WMI na Firewall do Windows. Em **Firewall do Windows** > **permitir que uma aplicação ou funcionalidade através da Firewall**, selecione **WMI em todos os perfis**. </br> Além disso, verifique o seguinte [pré-requisitos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) para concluir com êxito a instalação de push.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Erro 95213 - não foi possível proteção ativada (EP0874)

**Código de erro** | **Causas possíveis** | **Recomendações de erro específico**
--- | --- | ---
95213 </br>**Mensagem:** instalação do serviço de mobilidade falhou na máquina de origem % SourceIP; falhou com o código de erro **EP0874**. <br> | Versão do sistema operativo na máquina de origem não é suportada. <br>| Certifique-se de que a máquina de origem é suportada a versão do SO. Leia o [matriz de suporte](https://aka.ms/asr-os-support). </br> Além disso, verifique o seguinte [pré-requisitos](https://aka.ms/pushinstallerror) para concluir com êxito a instalação de push.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Erro 95108 - não foi possível proteção ativada (EP0859)

**Código de erro** | **Causas possíveis** | **Recomendações de erro específico**
--- | --- | ---
95108 </br>**Mensagem:** a instalação Push do serviço de mobilidade na máquina de origem falhou com o código de erro **EP0859**. <br>| As credenciais fornecidas para instalar o serviço de mobilidade estão incorretas ou a conta de utilizador tem privilégios insuficientes <br>| Certifique-se de que as credenciais fornecidas são a **raiz** credenciais da conta. Para [adicionar/editar credenciais de utilizador](site-recovery-vmware-to-azure-manage-configuration-server.md#modify-user-accounts-and-passwords), vá para o servidor de configuração e clique no ícone de atalho "Cspsconfigtool" no ambiente de trabalho. Clique em "Gerir conta" para adicionar/editar credenciais.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Erro 95265 - não foi possível proteção ativada (EP0902)

**Código de erro** | **Causas possíveis** | **Recomendações de erro específico**
--- | --- | ---
95265 </br>**Mensagem:** a instalação Push do serviço de mobilidade na máquina de origem foi concluída com êxito mas a máquina de origem requer um reinício para algumas alterações de sistema entre em vigor. <br>| Já foi instalada uma versão anterior do serviço de mobilidade no servidor.| Replicação da máquina virtual continua de forma totalmente integrada.<br> Reinicie o servidor durante a próxima janela de manutenção para obter os benefícios dos novos melhoramentos no serviço de mobilidade.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Erro 95224 - não foi possível proteção ativada (EP0883)

**Código de erro** | **Causas possíveis** | **Recomendações de erro específico**
--- | --- | ---
95224 </br>**Mensagem:** instalação Push do serviço de mobilidade da máquina de origem % SourceIP; falhou com o código de erro EP0883. Um sistema de reinício de uma instalação anterior / atualização está pendente.| O sistema não foi reiniciado a desinstalar uma versão mais antiga/incompatível do serviço de mobilidade.| Certifique-se de que não existe nenhuma versão do serviço de mobilidade no servidor. <br> Reinicie o servidor e execute novamente a tarefa de proteção de ativação|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Recursos para resolver problemas de instalação de push

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Resolver problemas de impressão partilha de ficheiros e
*  [Ativar ou desativar a partilha de ficheiros com a política de grupo](https://technet.microsoft.com/en-us/library/cc754359(v=ws.10).aspx)
* [Como ativar o ficheiro e imprimir partilha através da Firewall do Windows](https://technet.microsoft.com/en-us/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Resolver problemas WMI
* [Teste de WMI básico](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Resolução de problemas do WMI](https://msdn.microsoft.com/en-us/library/aa394603(v=vs.85).aspx)
* [Resolução de problemas com scripts WMI e serviços de WMI](https://technet.microsoft.com/en-us/library/ff406382.aspx#H22)

## <a name="next-steps"></a>Passos seguintes
- [Ativar a replicação para máquinas virtuais VMware](vmware-walkthrough-enable-replication.md)
