---
title: "Notas de versão 0,6 de atualização de matriz Virtual StorSimple | Microsoft Docs"
description: "Descreve problemas abertos críticos e resoluções para a matriz de Virtual StorSimple, executar a atualização 0,6."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: af202cf652300ee7897eb2dede33f38058fc2837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>Notas de versão 0,6 de atualização de matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

As seguintes notas de versão identificam os problemas abertos críticos e os problemas resolvidos para atualizações de matriz Virtual do Microsoft Azure StorSimple.

As notas de versão são continuamente atualizadas e, à medida que são descobertos problemas críticos que requerem uma solução, estes são adicionados. Antes de implementar a matriz de Virtual StorSimple, reveja com atenção as informações contidas nas notas de versão.

Atualização 0,6 corresponde à versão de software **10.0.10293.0**.

> [!IMPORTANT]
> - As atualizações são acontece e reinicie o seu dispositivo. Se a e/s estão em curso, o dispositivo incorreu período de indisponibilidade. Para obter instruções detalhadas sobre como aplicar a atualização, visite [instalação atualização 0,6](storsimple-virtual-array-install-update-06.md).
>
> - Recomendamos vivamente que instale a atualização 0,6 imediatamente porque esta contém correções de segurança críticas.


## <a name="whats-new-in-the-update-06"></a>Novidades na atualização 0,6
Atualização 0,6 é uma atualização crítica e deve ser implementada imediatamente. Esta atualização contém as seguintes correções: 

- **Correções de segurança do Windows** -com esta versão **correções de segurança críticas do Windows**. Reveja as seguintes atualizações de segurança para obter mais informações sobre os problemas de segurança e as correções associadas:
    - [Dezembro de 2016 qualidade apenas de segurança de atualização para Windows 8.1 e Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Março de 2017 qualidade apenas de segurança de atualização para Windows 8.1 e Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 de Maio de 2017 — KB4019213 (atualização só de segurança)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Restaurar a correção** -nas versões anteriores, Ocorreu um erro que possam impedir o restauro de conclusão. Corrigir este erro nesta versão.


## <a name="issues-fixed-in-the-update-06"></a>Problemas fixos na atualização 0,6

A tabela seguinte fornece um resumo dos problemas fixo nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Segurança| Esta versão contém atualizações de segurança do Windows críticas. Sugerimos que instalar esta atualização imediatamente.|
| 2 |Restauro| Durante um restauro, Ocorreu uma condição provável de antecipação que seria impeçam a conclusão da tarefa de restauro. Esta condição provável de antecipação de endereços a correção de erros.|


## <a name="known-issues-in-the-update-06"></a>Problemas conhecidos na atualização 0,6

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
| **8.** |Utilizar a capacidade de partilhas |Poderá ver partilhar consumo quando não existe nenhum dado na partilha. Este consumo é porque a capacidade utilizada para partilhas inclui metadados. | |
| **9.** |Recuperação após desastre |Só pode efetuar a recuperação após desastre de um servidor de ficheiros ao mesmo domínio que o dispositivo de origem. Recuperação de desastres para um dispositivo de destino no outro domínio não é suportada nesta versão. |Está implementado numa versão posterior. Para obter mais informações, aceda a [ativação pós-falha e recuperação após desastre para a matriz de Virtual StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Dispositivos virtuais StorSimple não podem ser geridos através do PowerShell do Azure nesta versão. |Todas as a gestão de dispositivos virtuais deve ser efetuada através do portal do Azure e a IU da web do local. |
| **11.** |Alteração de palavra-passe |A consola de dispositivos de matriz virtual apenas aceita entrada no en-us formato de teclado. | |
| **12.** |CHAP |Não não possível remover as credenciais CHAP depois de criado. Além disso, se modificar as credenciais do CHAP, tem de colocar os volumes offline e, em seguida, colocá-los online para a alteração produza efeito. |Este problema é resolvido numa versão posterior. |
| **13.** |servidor de iSCSI |O 'utilizado armazenamento' apresentadas para um volume de iSCSI pode ser diferente no serviço StorSimple Manager de dispositivo e o anfitrião de iSCSI. |O anfitrião iSCSI tem a vista de sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume foi no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tem uma alternativa dados fluxo (anúncios) associados à mesma, dos anúncios não é uma cópia de segurança ou restaurados através de recuperação após desastre, clone e recuperação ao nível do Item. | |
| **15.** |Servidor de ficheiros |As ligações simbólicas não são suportadas. | |
| **16.** |Servidor de ficheiros |Os ficheiros protegidos pelo Windows sistema EFS (Encrypting File) quando copiadas ou armazenados no resultado de servidor de ficheiros de matriz Virtual StorSimple uma configuração suportada.  | |
| **17.** |Atualizações |Se vir o erro código: 2359302 (hex 0x240006) ao tentar instalar uma correção através da IU local, em seguida, isto implica que a correção já está instalada no seu dispositivo.   | |

## <a name="next-step"></a>Passo seguinte
[Instalar a atualização 0,6](storsimple-virtual-array-install-update-06.md) na sua matriz de Virtual StorSimple.

## <a name="references"></a>Referências
Está à procura de uma nota de versão mais antiga? Vá para:

* [Notas de versão 0.5 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Notas de versão 0.4 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de versão 0,3 de atualização de matriz Virtual StorSimple](storsimple-ova-update-03-release-notes.md)
* [Notas de versão de atualização de matriz Virtual StorSimple 0.1 e 0,2](storsimple-ova-update-01-release-notes.md)
* [Notas de lançamento de disponibilidade geral de matriz Virtual StorSimple](storsimple-ova-pp-release-notes.md)

