---
title: "Azure Site Recovery de resolução de problemas para problemas de replicação do Azure para o Azure e erros | Microsoft Docs"
description: "Erros e problemas de resolução de problemas quando replicar máquinas virtuais do Azure para recuperação após desastre"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: sujayt
ms.openlocfilehash: 5e4de47de554f36e7797b7994faee4e90c3a8186
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Resolver problemas de replicação de VM do Azure para o Azure

Este artigo descreve os problemas comuns no quando replicar e recuperar máquinas virtuais do Azure a partir de uma região noutra região do Azure Site Recovery e explica como resolvê-los. Para obter mais informações sobre as configurações suportadas, consulte o [matriz de suporte para replicar as VMs do Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de quota de recursos do Azure (código de erro 150097)
A subscrição deve ser ativada para criar as VMs do Azure na região de destino que pretende utilizar como a região de recuperação após desastre. Além disso, a sua subscrição deve ter quota suficiente ativada para criar as VMs do tamanho específico. Por predefinição, a recuperação de sites escolhe o mesmo tamanho da VM de destino como a VM de origem. Se o tamanho correspondente não estiver disponível, o tamanho de possíveis mais próximo é selecionado automaticamente. Se não houver nenhum tamanho correspondente que suporte a configuração de VM de origem, é apresentada esta mensagem de erro:

**Código de erro** | **Causas possíveis** | **Recomendação**
--- | --- | ---
150097<br></br>**Mensagem**: não foi possível ativar a replicação da máquina virtual VmName. | -O ID de subscrição não pode estar ativado para criar quaisquer VMs na localização de região de destino.</br></br>-O ID de subscrição pode não estar ativado ou não tem quota suficiente para criar específicos tamanhos de VM na localização de região de destino.</br></br>-Um destino adequado de tamanho da VM que corresponde à origem de contagem de VM NIC (2) não for encontrado para o ID de subscrição na localização de região de destino.| Contacte [ao apoio de faturação do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para permitir a criação de VM para os tamanhos VM necessários na localização de destino para a sua subscrição. Depois de estar ativado, repita a operação falhada.

### <a name="fix-the-problem"></a>Corrija o problema
Pode contactar [ao apoio de faturação do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para ativar a sua subscrição criar as VMs de tamanhos necessários na localização de destino.

Se a localização de destino tiver uma restrição de capacidade, desative a replicação e ative-o para outra localização em que a sua subscrição tem quota suficiente para criar VMs os tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificados de raiz fidedigna (código de erro 151066)

Se todos os certificados de raiz fidedigna mais recentes não estão presentes na VM, poderá falhar a tarefa "ativar a replicação". Sem os certificados, a autenticação e autorização de chamadas de serviço de recuperação de sites da VM falharem. É apresentada a mensagem de erro para a tarefa de recuperação de Site "ativar a replicação de" falhada:

**Código de erro** | **Uma causa possível** | **Recommendations (Recomendações)**
--- | --- | ---
151066<br></br>**Mensagem**: Falha na configuração de recuperação de sites. | Necessários fidedignos certificados de raiz utilizado para autorização e autenticação não estão presentes na máquina. | -Para uma VM com o sistema operativo do Windows, certifique-se de que os certificados de raiz fidedigna estão presentes na máquina. Para informações, consulte [configurar raízes confiáveis e certificados não permitidos](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-Para uma VM com o sistema operativo Linux, siga as orientações para certificados de raiz fidedigna publicados pelo distribuidor de versão do sistema operativo Linux.

### <a name="fix-the-problem"></a>Corrija o problema
**Windows**

Instale todas as atualizações mais recentes do Windows na VM para que todos os certificados de raiz fidedigna estão presentes na máquina. Se estiver num ambiente desligado, siga o processo de atualização do Windows padrão na sua organização para obter os certificados. Se os certificados necessários não estão presentes na VM, as chamadas para o serviço de recuperação de sites falharem por motivos de segurança.

Siga o processo normal do Windows update management ou certificado atualização gestão na sua organização para obter todos os certificados de raiz mais recentes e a lista de revogação do certificado atualizado nas VMs.

Para verificar se o problema está resolvido, visite login.microsoftonline.com a partir de um browser no VM.

**Linux**

Siga as orientações fornecidas pelo sua distribuidor Linux para obter os certificados de raiz fidedigna mais recentes e a lista de revogação de certificados mais recente na VM.

Uma vez SuSE Linux utiliza symlinks para manter uma lista de certificados, siga estes passos:

1.  Inicie sessão como utilizador raiz.

2.  Execute este comando:

      ``# cd /etc/ssl/certs``

3.  Para ver se o certificado de AC de raiz da Symantec está presente ou não, execute este comando:

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4.  Se o ficheiro não for encontrado, execute estes comandos:

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

      ``# c_rehash``

5.  Para criar um symlink com b204d74a.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem, execute este comando:

      ``# ln -s  VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

6.  Verifique se este comando tem o seguinte resultado. Caso contrário, terá de criar um symlink:

      ``# ls -l | grep Baltimore
      -rw-r--r-- 1 root root   1303 Apr  7  2016 Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 04:47 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 05:01 653b494a.0 -> Baltimore_CyberTrust_Root.pem``

7. Se symlink 653b494a.0 não estiver presente, utilize este comando para criar um symlink:

      ``# ln -s Baltimore_CyberTrust_Root.pem 653b494a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de URLs de recuperação de Site ou IP (código de erro 151037 ou 151072)

Para replicação de recuperação de Site para o trabalho, a conectividade de saída para URLs específico ou de IP, intervalos é necessário da VM. Se a VM estiver protegido por uma firewall ou utiliza regras de grupo (NSG) de segurança de rede para controlar a conectividade de saída, poderá ver um destas mensagens de erro:

**Códigos de erro** | **Causas possíveis** | **Recommendations (Recomendações)**
--- | --- | ---
151037<br></br>**Mensagem**: Falha ao registar a máquina virtual do Azure Site Recovery. | -Utilizar NSG para controlar o acesso de saída na VM e o IP necessário intervalos não estão na lista de permissões de acesso de saída.</br></br>-Utilizar ferramentas de firewall de terceiros e necessários intervalos IP/os URLs não estão na lista de permissões.</br>| -Se estiver a utilizar o proxy de firewall para controlar a conectividade de rede de saída na VM, certifique-se de que os URLs de pré-requisitos ou a intervalos IP do datacenter estão na lista de permissões. Para informações, consulte [orientações de proxy de firewall](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-Se estiver a utilizar as regras do NSG para controlar a conectividade de rede de saída na VM, certifique-se de que os intervalos IP do datacenter pré-requisitos estão na lista de permissões. Para informações, consulte [orientações do grupo de segurança de rede](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Mensagem**: Falha na configuração de recuperação de sites. | Não é possível estabelecer ligação a pontos finais do serviço de recuperação de sites. | -Se estiver a utilizar o proxy de firewall para controlar a conectividade de rede de saída na VM, certifique-se de que os URLs de pré-requisitos ou a intervalos IP do datacenter estão na lista de permissões. Para informações, consulte [orientações de proxy de firewall](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-Se estiver a utilizar as regras do NSG para controlar a conectividade de rede de saída na VM, certifique-se de que os intervalos IP do datacenter pré-requisitos estão na lista de permissões. Para informações, consulte [orientações do grupo de segurança de rede](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>Corrija o problema
Lista branca [os URLs necessários](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-urls) ou o [necessário intervalos IP](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-ip-ranges), siga os passos a [rede documento de orientação](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco não localizado na máquina (código de erro 150039)

Tem de ser inicializado num disco novo anexado à VM.

**Código de erro** | **Causas possíveis** | **Recommendations (Recomendações)**
--- | --- | ---
150039<br></br>**Mensagem**: o disco de dados do Azure (DiskName) (DiskURI) com o número de unidade lógica (LUN) (LUNValue) não foi mapeado para um disco correspondente a ser reportado de dentro da VM que tenha o mesmo valor LUN. | -Um novo disco de dados foi ligado à VM, mas não foi inicializado.</br></br>-O disco de dados dentro da VM não está corretamente a comunicar o valor do LUN em que o disco foi ligado à VM.| Certifique-se de que os discos de dados são inicializados e, em seguida, repita a operação.</br></br>Para o Windows: [anexar e inicializar um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk).</br></br>Para Linux: [inicializar um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk#initialize-a-new-data-disk-in-linux).

### <a name="fix-the-problem"></a>Corrija o problema
Certifique-se de que os discos de dados tem sido inicializados e, em seguida, repita a operação:

- Para o Windows: [anexar e inicializar um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk).
- Para Linux: [inicializar um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk#initialize-a-new-data-disk-in-linux).

Se o problema persistir, contacte o suporte.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Não é possível ver a VM do Azure para a seleção "ativar a replicação"

Não poderá ver a VM do Azure para seleção nos [ativar a replicação: passo 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines). Este problema foi devido a configuração de recuperação de Site obsoleta ficar na VM do Azure. A configuração obsoleta pode ser deixada na VM do Azure nos seguintes casos:

- Ativar a replicação para a VM do Azure utilizando a recuperação de Site e, em seguida, eliminar o Cofre de recuperação de sites sem explicitamente a desativação da replicação da VM.
- Ativar a replicação para a VM do Azure utilizando a recuperação de Site e, em seguida, eliminar o grupo de recursos que contém o Cofre de recuperação de sites sem explicitamente a desativação da replicação da VM.

### <a name="fix-the-problem"></a>Corrija o problema

Pode utilizar [remover o script de configuração de ASR obsoleto](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) e remover a configuração da recuperação de Site obsoleta na VM do Azure. Deverá ver a VM no [ativar a replicação: passo 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines) depois de remover a configuração obsoleta.


## <a name="next-steps"></a>Passos seguintes
[Replicar máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
