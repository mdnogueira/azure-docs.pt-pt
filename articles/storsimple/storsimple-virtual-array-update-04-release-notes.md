---
title: "Notas de versão 0.4 de atualização de matriz Virtual StorSimple | Microsoft Docs"
description: "Descreve problemas abertos críticos e resoluções para a matriz de Virtual StorSimple, executar a atualização 0.4."
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
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: cc2b025b7f3e28954c7f95409ffab03e5cbcf13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Notas de versão 0.4 de atualização de matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

As seguintes notas de versão identificam os problemas abertos críticos e os problemas resolvidos para atualizações de matriz Virtual do Microsoft Azure StorSimple.

As notas de versão são continuamente atualizadas e, à medida que são descobertos problemas críticos que requerem uma solução, estes são adicionados. Antes de implementar a matriz de Virtual StorSimple, reveja com atenção as informações contidas nas notas de versão.

Atualização 0.4 corresponde à versão de software **10.0.10289.0**.

> [!NOTE]
> As atualizações são acontece e reinicie o seu dispositivo. Se a e/s estão em curso, o dispositivo incorreu período de indisponibilidade.


## <a name="whats-new-in-the-update-04"></a>Novidades na atualização 0.4
Atualização 0.4 é principalmente uma compilação de correção de erro conjugada com alguns melhoramentos. Nesta versão, tem sido resolvidos vários erros, resultando em falhas de cópia de segurança na versão anterior. Os principais melhoramentos e correções de erros são os seguintes:

- **Melhoramentos de desempenho de cópia de segurança** -nesta versão efetuou vários melhoramentos de chaves para melhorar o desempenho de cópia de segurança. Como resultado, as cópias de segurança que envolvem um grande número de ficheiros Consulte uma redução significativa no tempo a concluir, para cópias de segurança completas e incrementais.

- **Avançada de desempenho de restauro** -esta versão contém melhoramentos melhorar significativamente o desempenho de restauro ao utilizar o elevado número de ficheiros. Se utilizar 2-4 milhões de ficheiros, é recomendável que Aprovisiona uma matriz de virtual com 16 GB de RAM para ver os melhoramentos. Ao utilizar menos de 2 milhões de ficheiros, o requisito mínimo para a máquina virtual continua a ser 8 GB de RAM.

- **Melhoramentos ao pacote de suporte** -os melhoramentos incluem registo nas estatísticas de disco, da CPU, memória, rede e na nuvem para o pacote de suporte, deste modo, melhorando o processo de diagnosticar/depurar problemas do dispositivo.

- **Limite localmente afixado iSCSI volumes de 200 GB** -para volumes afixados localmente, recomendamos que limite a um volume de iSCSI de 200 GB na sua matriz de Virtual StorSimple. A reserva do local para volumes em camadas continua a ser 10% do tamanho do volume aprovisionado, mas está limitada a 200 GB. 

- **Correções de erros relacionados com a cópia de segurança** - em versões anteriores do software, ocorreram problemas relacionados com as cópias de segurança que possam causar falhas de cópia de segurança. Estes erros têm sido resolvidos nesta versão.


## <a name="issues-fixed-in-the-update-04"></a>Problemas fixos na atualização 0.4

A tabela seguinte fornece um resumo dos problemas fixo nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Desempenho de cópia de segurança|Nas versões anteriores, as cópias de segurança que envolve um grande número de ficheiros iriam demorar muito tempo a concluir (por ordem de dias). Nesta versão, as cópias de segurança completas e incrementais Consulte uma redução significativa do tempo de conclusão. |
| 2 |Pacote de suporte|Disco, da CPU, memória, rede e estatísticas de nuvem são agora sessão iniciadas para os registos de suporte efetuar os pacotes de suporte muito eficaz na resolução de problemas do dispositivo.|
| 3 |Cópia de segurança |Nas versões anteriores, as cópias de segurança de execução longa pode resultar num crunch de espaço no dispositivo, resultando em falhas de cópia de segurança. Este erro é abordado nesta versão, permitindo não mais de 5 cópias de segurança para a fila de uma só vez.|
| 4 |iSCSI | Nas versões anteriores, a reserva do local para os volumes em camadas ou localmente afixados foi 10% do tamanho do volume aprovisionado. Nesta versão, a reserva do local em todos os volumes do iSCSI (localmente afixado ou em camadas) está limitada a 10% com um máximo de até 200 GB (para os volumes em camadas maiores do que 2 TB) deste modo, libertar mais espaço no disco local. Recomendamos que os volumes localmente afixados nesta versão ser limitada para 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Problemas conhecidos na atualização 0.4

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
| **15.** |Servidor de ficheiros |As ligações simbólicas não são suportadas. | |
| **16.** |Servidor de ficheiros |Os ficheiros protegidos pelo Windows sistema EFS (Encrypting File) quando copiadas ou armazenados no resultado de servidor de ficheiros de matriz Virtual StorSimple uma configuração suportada.  | |

## <a name="next-step"></a>Passo seguinte
[Instalar a atualização 0.4](storsimple-virtual-array-install-update-04.md) na sua matriz de Virtual StorSimple.

## <a name="references"></a>Referências
Está à procura de uma nota de versão mais antiga? Vá para: 

* [Notas de versão 0,3 de atualização de matriz Virtual StorSimple](storsimple-ova-update-03-release-notes.md)
* [Notas de versão de atualização de matriz Virtual StorSimple 0.1 e 0,2](storsimple-ova-update-01-release-notes.md)
* [Notas de lançamento de disponibilidade geral de matriz Virtual StorSimple](storsimple-ova-pp-release-notes.md)

