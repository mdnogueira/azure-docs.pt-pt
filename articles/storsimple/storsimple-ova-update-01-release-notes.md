---
title: "Notas de versão de atualizações de matriz Virtual StorSimple | Microsoft Docs"
description: "Descreve problemas abertos críticos e resoluções para a matriz de Virtual StorSimple, executar a atualização 0,2 e 0.1."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: c4ccde9635b3874864baa9d4d262ff5ddcf2a425
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Notas de versão de atualização de matriz Virtual 0,2 e 0.1 do StorSimple
## <a name="overview"></a>Descrição geral
As seguintes notas de versão identificam os problemas abertos críticos e os problemas resolvidos para atualizações de matriz Virtual do Microsoft Azure StorSimple. (Matriz Virtual do Microsoft Azure StorSimple também é conhecido como dispositivo virtual StorSimple no local ou o dispositivo virtual StorSimple.) 

As notas de versão são continuamente atualizadas e, à medida que são descobertos problemas críticos que requerem uma solução, estes são adicionados. Antes de implementar o dispositivo virtual StorSimple, reveja com atenção as informações contidas nas notas de versão.

Atualização 0,2 corresponde à versão de software **10.0.10280.0**; Atualização 0.1 é versão **10.0.10279.0**. As secções abaixo listam as alterações para cada atualização. 

> [!NOTE]
> As atualizações são acontece e irão reiniciar o seu dispositivo. Se a e/s estão em curso, o dispositivo será cobrado o período de indisponibilidade.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemas fixos na atualização 0,2
Atualização 0,2 inclui todas as alterações da atualização 0.1 além a correção descrita na tabela seguinte:

| Funcionalidade | Problema |
| --- | --- |
| Atualizações |A última versão, as atualizações não foram detetadas automaticamente no portal clássico do Azure, para precisava de utilizar a IU da Web local para instalar atualizações. Este problema ser corrigido nesta versão. Depois de instalar a atualização 0,2, pode instalar as atualizações futuras através do portal clássico do Azure. |

## <a name="whats-new-in-the-update-01"></a>Novidades na atualização 0.1
Atualização 0.1 contém as seguintes correções de erros e melhoramentos. 

* **Resiliência melhorada para falhas de nuvem**: esta versão tem várias correções de erros em torno da recuperação após desastre, cópia de segurança, restaurar e criação de camadas na eventualidade de ocorrer uma interrupção de conectividade de nuvem. 
* **Restauro desempenho melhorado**: esta versão foi correções de erros que tem significativamente a diminuir o tempo de conclusão das tarefas de restauro.
* **Automatizada otimização de recuperação de espaço**: quando os dados são eliminados em volumes de aprovisionamento dinâmico, os blocos de armazenamento não utilizado tem de ser recuperados. Esta versão foi melhorado o processo de recuperação de espaço da nuvem resultando num espaço não utilizado se tornarem disponíveis mais rapidamente em comparação com as versões anteriores.
* **Novas imagens de disco virtual**: VMDK, novo VHD e VHDX estão agora disponíveis através do portal clássico do Azure. Pode transferir estas imagens para aprovisionar novos dispositivos de atualização 0.1.
* **Melhorar a precisão do Estado de tarefas no portal do**: na versão anterior do software, o estado da tarefa de criação de relatórios no portal do não foi granular. Este problema é resolvido nesta versão.
* **Experiência de associação de domínio**: correções de erros relacionados com a associação ao domínio e mudar o nome do dispositivo.

## <a name="issues-fixed-in-the-update-01"></a>Problemas fixos na atualização 0.1
A tabela seguinte fornece um resumo dos problemas fixo nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |VMDK |Em algumas versões do VMware, o disco do SO foi visto como dispersa fazendo com que alertas e perturbar as operações normais. Isto foi corrigido nesta versão. |
| 2 |servidor de iSCSI |A última versão, foi pedido ao utilizador para especificar um gateway para cada interface de rede ativada do dispositivo virtual StorSimple. Este comportamento é alterado nesta versão, para que o utilizador tem de configurar pelo menos um gateway para todas as interfaces de rede ativada. |
| 3 |Pacote de suporte |Na versão anterior do software, a coleção de pacote de suporte falhou quando os tamanhos de pacote foram superiores a 1 GB. Este problema ser corrigido nesta versão. |
| 4 |Acesso à nuvem |A última versão, se a matriz de Virtual StorSimple não tinha a conectividade de rede e foi reiniciada, a IU do local teria problemas de conectividade. Este problema esteja corrigido nesta versão. |
| 5 |Gráficos de monitorização |A versão anterior, após uma ativação pós-falha do dispositivo, os gráficos de utilização da capacidade de nuvem apresentados valores incorretos no portal clássico do Azure. Isto é corrigido na versão atual. |

## <a name="known-issues-in-the-update-01"></a>Problemas conhecidos na atualização 0.1
A tabela seguinte fornece um resumo dos problemas conhecidos para a matriz de Virtual StorSimple e inclui os problemas que anotou lançamento de versões anteriores. **A versão de problemas de salientar nesta versão estão marcados com um asterisco. Quase todos os problemas nesta lista tem transportadas partir da versão GA da matriz Virtual StorSimple.**

| Não. | Funcionalidade | Problema | Solução/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Não não possível atualizar os dispositivos virtuais criados na versão de pré-visualização para uma versão suportada de disponibilidade geral. |Estes dispositivos virtuais tem a ativação pós-falha para a versão de disponibilidade geral, utilizando um fluxo de trabalho do após desastre (DR) de recuperação. |
| **2.** |Disco de dados de aprovisionamento |Depois de ter aprovisionado um disco de dados de um determinado tamanho especificado e criar dispositivo virtual StorSimple correspondente, tem não expandir ou reduzir o disco de dados. Tentar fazê-lo resultará na perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo está associado a um domínio, aplicar uma política de grupo que pode afetar negativamente a operação de dispositivo. |Certifique-se de que a matriz de virtual destina-se no seu próprio unidade organizacional (UO) do Active Directory e não existem objetos de política de grupo (GPO) são aplicados ao mesmo. |
| **4.** |IU da web do local |Se as funcionalidades de segurança avançada estiverem ativadas no Internet Explorer (IE ESC), algumas páginas de IU da web de locais como resolução de problemas ou a manutenção poderão não funcionar corretamente. Botões nestas páginas também poderão não funcionar. |Desative funcionalidades de segurança avançada do Internet Explorer. |
| **5.** |IU da web do local |Numa máquina virtual de Hyper-V, as interfaces de rede na web IU são apresentadas como 10 Gbps das interfaces. |Este comportamento é um reflexão do Hyper-V. Hyper-V Mostra sempre a 10 Gbps para adaptadores de rede virtual. |
| **6.** |Volumes em camadas ou partilhas |Intervalo de byte bloqueio para aplicações que funcionam com StorSimple volumes em camadas não é suportada. Se o bloqueio de intervalo de byte estiver ativado, a criação de camadas do StorSimple não irá funcionar. |Medidas recomendadas incluem: <br></br>Desative o intervalo de byte bloquear na lógica da sua aplicação.<br></br>Optar por colocar os dados para esta aplicação em volumes localmente afixados, por oposição a volumes em camadas.<br></br>*Advertência*: Se utilizar localmente afixado volumes e o bloqueio de intervalo de byte está ativado, lembre-se de que o volume localmente afixado pode ficar online, mesmo antes do restauro foi concluído. Nessas instâncias, se for um restauro em curso, em seguida, tem de aguardar o conclusão do restauro. |
| **7.** |Partilhas em camadas |Trabalhar com ficheiros grandes pode resultar em camada lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o ficheiro maior é inferior a 3 de % do tamanho da partilha. |
| **8.** |Utilizar a capacidade de partilhas |Poderá ver partilhar consumo na ausência de quaisquer dados na partilha. Isto acontece porque a capacidade utilizada para partilhas inclui metadados. | |
| **9.** |Recuperação após desastre |Só pode efetuar a recuperação após desastre de um servidor de ficheiros ao mesmo domínio que o dispositivo de origem. Recuperação de desastres para um dispositivo de destino no outro domínio não é suportada nesta versão. |Isto irá ser implementado uma versão posterior. |
| **10.** |Azure PowerShell |Dispositivos virtuais StorSimple não podem ser geridos através do PowerShell do Azure nesta versão. |Todas as a gestão de dispositivos virtuais deve ser feita através do portal clássico do Azure e a IU da web do local. |
| **11.** |Alteração de palavra-passe |A consola de dispositivos de matriz virtual apenas aceita entrada no formato de teclado en-US. | |
| **12.** |CHAP |Não não possível remover as credenciais CHAP depois de criado. Além disso, se modificar as credenciais do CHAP, terá de colocar os volumes offline e, em seguida, colocá-los online para a alteração produza efeito. |Estes serão resolvidos numa versão posterior. |
| **13.** |servidor de iSCSI |O 'utilizado armazenamento' apresentadas para um volume de iSCSI pode ser diferente no serviço StorSimple Manager e o anfitrião de iSCSI. |O anfitrião iSCSI tem a vista de sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume foi no tamanho máximo. |
| **14.** |Servidor de ficheiros * |Se um ficheiro numa pasta tem uma alternativa dados fluxo (anúncios) associados à mesma, dos anúncios não é uma cópia de segurança ou restaurados através de recuperação após desastre, clone e recuperação ao nível do Item. | |

## <a name="next-step"></a>Passo seguinte
[Instalar atualizações](storsimple-ova-install-update-01.md) na sua matriz de Virtual StorSimple.

