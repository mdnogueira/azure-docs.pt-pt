---
title: "Notas de versão de atualizações de matriz Virtual StorSimple | Microsoft Docs"
description: "Descreve problemas abertos críticos e resoluções para a matriz de Virtual StorSimple, executar a atualização 0.3."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: fe9d4f6b232e9abcf1fe9fc5657044b6c72fedb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>Notas de versão 0,3 de atualização de matriz Virtual StorSimple
## <a name="overview"></a>Descrição geral
As seguintes notas de versão identificam os problemas abertos críticos e os problemas resolvidos para atualizações de matriz Virtual do Microsoft Azure StorSimple.

As notas de versão são continuamente atualizadas e, à medida que são descobertos problemas críticos que requerem uma solução, estes são adicionados. Antes de implementar a matriz de Virtual StorSimple, reveja com atenção as informações contidas nas notas de versão.

Atualização 0.3 corresponde à versão de software **10.0.10288.0**.

> [!NOTE]
> As atualizações são acontece e reinicie o seu dispositivo. Se a e/s estão em curso, o dispositivo incorreu período de indisponibilidade.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Novidades na atualização 0.3
Atualização 0.3 é principalmente uma compilação de correção de erro. Nesta versão, tem sido resolvidos vários erros, resultando em falhas de cópia de segurança na versão anterior.

## <a name="issues-fixed-in-the-update-03"></a>Problemas fixos na atualização 0.3
A tabela seguinte fornece um resumo dos problemas fixo nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Cópias de segurança |Um problema foi visto na versão anterior em que as cópias de segurança falharão para concluir para uma partilha de ficheiros. Se o problema ocorreu, a tarefa de cópia de segurança falharão e foi gerado um alerta crítico no serviço StorSimple Manager para notificar o utilizador. Este problema não afetou os dados nas partilhas ou acesso aos dados. A causa raiz foi identificada e resolvida nesta versão. <br></br> A correção não se aplica retroactively partilhas que já está a ver este problema. Os clientes que estão a ver este problema devem primeiro aplicar atualização 0.3, em seguida, contacte a Microsoft Support para efetuar uma cópia de segurança completa do sistema para corrigir o problema. Em vez de contactar a Microsoft Support, os clientes também podem restaurar para uma nova partilha de uma cópia de segurança de bom estado de funcionamento para as partilhas afetadas. |
| 2 |iSCSI |Um problema foi visto na versão anterior em que os volumes seriam desaparecem quando copiar dados para um volume na matriz Virtual StorSimple. Este problema foi corrigido nesta versão. <br></br> As correções entram em vigor apenas no recém-criado volumes. As correções não se aplicam retroactively para volumes que já estão a ver este problema. Os clientes aconselha-se para colocar os volumes afetados online através do portal clássico do Azure, execute uma cópia de segurança para estes volumes e, em seguida, restaurar estes volumes a novos volumes. |

## <a name="known-issues-in-the-update-03"></a>Problemas conhecidos na atualização 0.3
A tabela seguinte fornece um resumo dos problemas conhecidos para a matriz de Virtual StorSimple e inclui os problemas que anotou lançamento de versões anteriores. 

| Não. | Funcionalidade | Problema | Solução/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Não não possível atualizar os dispositivos virtuais criados na versão de pré-visualização para uma versão suportada de disponibilidade geral. |Estes dispositivos virtuais tem a ativação pós-falha para a versão de disponibilidade geral, utilizando um fluxo de trabalho do após desastre (DR) de recuperação. |
| **2.** |Disco de dados de aprovisionamento |Depois de ter aprovisionado um disco de dados de um determinado tamanho especificado e criar dispositivo virtual StorSimple correspondente, tem não expandir ou reduzir o disco de dados. A tentar fazer resultados uma perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo está associado a um domínio, aplicar uma política de grupo que pode afetar negativamente a operação de dispositivo. |Certifique-se de que a matriz de virtual destina-se no seu próprio unidade organizacional (UO) do Active Directory e não existem objetos de política de grupo (GPO) são aplicados ao mesmo. |
| **4.** |IU da web do local |Se as funcionalidades de segurança avançada estiverem ativadas no Internet Explorer (IE ESC), algumas páginas de IU da web de locais como resolução de problemas ou a manutenção poderão não funcionar corretamente. Botões nestas páginas também poderão não funcionar. |Desative funcionalidades de segurança avançada do Internet Explorer. |
| **5.** |IU da web do local |Numa máquina virtual de Hyper-V, as interfaces de rede na web IU são apresentadas como 10 Gbps das interfaces. |Este comportamento é um reflexão do Hyper-V. Hyper-V Mostra sempre a 10 Gbps para adaptadores de rede virtual. |
| **6.** |Volumes em camadas ou partilhas |Intervalo de byte bloqueio para aplicações que funcionam com StorSimple volumes em camadas não é suportada. Se o bloqueio de intervalo de byte estiver ativado, criação de camadas do StorSimple não funciona. |Medidas recomendadas incluem: <br></br>Desative o intervalo de byte bloquear na lógica da sua aplicação.<br></br>Optar por colocar os dados para esta aplicação em volumes localmente afixados, por oposição a volumes em camadas.<br></br>*Advertência*: quando utilizar localmente afixado volumes e o bloqueio de intervalo de byte está ativado, o volume localmente afixado pode ser online mesmo antes do restauro foi concluído. Nessas instâncias, se for um restauro em curso, em seguida, tem de aguardar o conclusão do restauro. |
| **7.** |Partilhas em camadas |Trabalhar com ficheiros grandes pode resultar em camada lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o ficheiro maior é inferior a 3 de % do tamanho da partilha. |
| **8.** |Utilizar a capacidade de partilhas |Poderá ver partilhar consumo quando não existe nenhum dado na partilha. Isto acontece porque a capacidade utilizada para partilhas inclui metadados. | |
| **9.** |Recuperação após desastre |Só pode efetuar a recuperação após desastre de um servidor de ficheiros ao mesmo domínio que o dispositivo de origem. Recuperação de desastres para um dispositivo de destino no outro domínio não é suportada nesta versão. |Está implementado numa versão posterior. |
| **10.** |Azure PowerShell |Dispositivos virtuais StorSimple não podem ser geridos através do PowerShell do Azure nesta versão. |Todas as a gestão de dispositivos virtuais deve ser feita através do portal clássico do Azure e a IU da web do local. |
| **11.** |Alteração de palavra-passe |A consola de dispositivos de matriz virtual apenas aceita entrada no formato de teclado en-US. | |
| **12.** |CHAP |Não não possível remover as credenciais CHAP depois de criado. Além disso, se modificar as credenciais do CHAP, tem de colocar os volumes offline e, em seguida, colocá-los online para a alteração produza efeito. |Este problema é resolvido numa versão posterior. |
| **13.** |servidor de iSCSI |O 'utilizado armazenamento' apresentadas para um volume de iSCSI pode ser diferente no serviço StorSimple Manager e o anfitrião de iSCSI. |O anfitrião iSCSI tem a vista de sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume foi no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tem uma alternativa dados fluxo (anúncios) associados à mesma, dos anúncios não é uma cópia de segurança ou restaurados através de recuperação após desastre, clone e recuperação ao nível do Item. | |

## <a name="next-step"></a>Passo seguinte
[Instalar a atualização 0.3](storsimple-ova-install-update-01.md) na sua matriz de Virtual StorSimple.

## <a name="references"></a>Referências
Está à procura de uma nota de versão mais antiga? Vá para: 

* [Notas de versão de atualização de matriz Virtual StorSimple 0.1 e 0,2](storsimple-ova-update-01-release-notes.md)
* [Notas de lançamento de disponibilidade geral de matriz Virtual StorSimple](storsimple-ova-pp-release-notes.md)

