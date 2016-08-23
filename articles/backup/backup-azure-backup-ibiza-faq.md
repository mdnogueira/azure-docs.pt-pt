<properties
   pageTitle="FAQ sobre o lançamento da pré-visualização pública do Backupdo Azure | Microsoft Azure"
   description="Esta versão de FAQ suporta o lançamento da Pré-visualização Pública do serviço do Backup do Azure. Respostas a perguntas mais frequentes sobre o agente da cópia de segurança, a cópia de segurança e a retenção, recuperação, segurança e outras perguntas comuns sobre a solução do Backup do Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="solução de cópia de segurança; serviço de cópia de segurança"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="07/01/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# FAQ sobre o lançamento da Pré-visualização Pública do serviço do Backup do Azure

> [AZURE.SELECTOR]
- [FAQ sobre a Cópia de segurança para o modo Clássico](backup-azure-backup-faq.md)
- [FAQ sobre a Cópia de segurança para o modo ARM](backup-azure-backup-ibiza-faq.md)

Este artigo fornece informações específicas para o lançamento da Pré-visualização Pública do serviço do Backup do Azure. Este artigo é atualizado quando chegam novas perguntas mais frequentes e complementa as [FAQ sobre o Backup do Azure](backup-azure-backup-faq). As FAQ sobre o Backup do Azure fornecem o conjunto completo de perguntas e respostas sobre o serviço do Backup do Azure.  

Pode colocar perguntas sobre o Backup do Azure na secção Disqus deste artigo ou um artigo relacionado. Também pode publicar perguntas sobre o serviço de Backup do Azure no [fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## O que é o lançamento da Pré-visualização Pública?
O lançamento da Pré-visualização pública apresenta o cofre dos Serviços de Recuperação e o suporte ARM ao proteger as VMs do Azure. O cofre dos Serviços de Recuperação é a próxima geração de cofres. É o cofre utilizado pelo Backup do Azure e os serviços do Azure Site Recovery (ASR). Considere-o como o cofre v.2.

## Cofres dos Serviços de Recuperação e Cópia de Segurança

**P1. Os cofres dos Serviços de Recuperação são v.2, os cofres da Cópia de Segurança (v.1) ainda são suportados?** <br/>
R1. Sim, os cofres da Cópia de Segurança ainda são suportados. Crie cofres da Cópia de Segurança no portal Clássico. Crie cofres dos Serviços de Recuperação no portal do Azure.

**P2. Posso migrar um cofre da Cópia de Segurança para um cofre dos Serviços de Recuperação?** <br/>
R2. Infelizmente não, neste momento não é possível migrar os conteúdos de um cofre da Cópia de Segurança para um cofre dos Serviços de Recuperação. Estamos a tentar adicionar esta funcionalidade, mas não está disponível como parte da Pré-visualização Pública.

**P3. Os cofres dos Serviços de Recuperação suportam as VMs v.1 ou v.2?** <br/>
 R3. Os cofres dos Serviços de Recuperação suportam as VMs v.1 e v.2. Pode efetuar uma cópia de segurança de uma VM criada no portal Clássico (que é V.1) ou uma VM criada no portal do Azure (que é V.2) para um cofre dos Serviços de Recuperação.


## Suporte ARM para VMs do Azure

**P1. Existem algumas limitações no suporte ARM para as VMs do Azure?** <br/>
R1. Os cmdlets do PowerShell para ARM não estão atualmente disponíveis. Tem de utilizar a IU do portal do Azure para adicionar recursos a um grupo de recursos.



<!--HONumber=Aug16_HO1-->


