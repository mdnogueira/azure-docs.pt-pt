---
title: "Notas de versão 1.0 do StorSimple Virtual matriz Update | Microsoft Docs"
description: "Descreve problemas abertos críticos e resoluções para a matriz de Virtual StorSimple com atualização 1.0."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 777718c4893f1edab3613be5dc941e2716d4c972
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Notas de versão do StorSimple Virtual matriz atualização 1.0

## <a name="overview"></a>Descrição geral

As seguintes notas de versão identificam os problemas abertos críticos e os problemas resolvidos para atualizações de matriz Virtual do Microsoft Azure StorSimple.

As notas de versão são continuamente atualizadas e, à medida que são descobertos problemas críticos que requerem uma solução, estes são adicionados. Antes de implementar a matriz de Virtual StorSimple, reveja com atenção as informações contidas nas notas de versão.

Atualização 1.0 corresponde à versão de software **10.0.10296.0**.

> [!IMPORTANT]
> - As atualizações são acontece e reinicie o seu dispositivo. Se a e/s estão em curso, o dispositivo incorreu período de indisponibilidade. Para obter instruções detalhadas sobre como aplicar a atualização, visite [instalar 1.0 atualizar](storsimple-virtual-array-install-update-1.md).
>
> - A atualização 1 apenas está disponível para si através do portal do Azure se o dispositivo estiver em execução de atualização 0,6.

## <a name="whats-new-in-update-10"></a>Novidades na atualização 1.0

**Atualização 1.0 contém alterações relacionadas com autenticação do serviço do Gestor de dispositivos do StorSimple e devem ser implementada na sua mais antigo.** Esta atualização contém as seguintes melhorias e correções de erros:

 - **Utilização do Azure Active Directory (AAD) para autenticar com o serviço do Gestor de dispositivos do StorSimple** – da atualização 1.0 e superior, do Azure Active Directory é utilizado para autenticar com o serviço do Gestor de dispositivos do StorSimple. O mecanismo de autenticação antigo será preterido Dezembro de 2017. Todos os utilizadores têm de incluir novos URLs de autenticação nas regras de firewall. Para obter mais informações, aceda a URLs de autenticação listados no [requisitos de rede para a matriz de Virtual StorSimple](storsimple-ova-system-requirements.md).
 
    Se o URL de autenticação não está incluído nas regras da firewall, os utilizadores verão um alerta crítico que o dispositivo StorSimple não foi possível autenticar com o serviço. Se os utilizadores veem este alerta, tem de incluir o novo URL de autenticação. Para obter mais informações, aceda a [StorSimple redes alertas](storsimple-virtual-array-manage-alerts.md).

 - **Melhorias de desempenho** -foram efetuadas várias correções de erros para melhorar as velocidades de leituras de nuvem, a camada-ins e a camada outs. Como resultado, o desempenho de cópia de segurança e restauro foi melhorado para dispositivos de servidor de ficheiros e iSCSI.

 - **Melhoramento de recolha de lixo** -nesta versão tem correções de erros que melhoram o desempenho do ciclo de recolha de lixo quando a conta de armazenamento e de dispositivo são em duas regiões distantes.

 - **Melhoramento do registo** -esta versão contém melhoramentos ao registo relacionadas com a recolha de lixo e o caminho de e/s.


## <a name="issues-fixed-in-update-10"></a>Problemas fixos em atualização 1.0

A tabela seguinte fornece um resumo dos problemas fixo nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Autenticação baseada no AAD| Esta versão contém alterações que permite o AAD autenticar com o Gestor de dispositivos do StorSimple.|
| 2 |Recolha de lixo| Este problema foi comunicado um site do cliente em que as contas de armazenamento e de dispositivo estão em diferentes regiões e o cliente comunicou erros de rede intermitente, deste modo, afetar a faturação. Nesta versão, este problema foi corrigido. |
| 3 |Desempenho| Esta versão contém alterações que resultam no restauro/nuvem leituras/camada / camada saída melhorias de desempenho.|
| 4 |Atualizar| Ocorreu um problema com a atualização na versão anterior que resultaram em falhas de cópia de segurança num site do cliente. Este problema ser corrigido nesta versão.|

## <a name="known-issues-in-update-10"></a>Problemas conhecidos na atualização 1.0

A tabela seguinte fornece um resumo dos problemas conhecidos para a matriz de Virtual StorSimple e inclui os problemas que anotou lançamento de versões anteriores.

| Não. | Funcionalidade | Problema | Solução/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |As matrizes virtuais criadas na versão de pré-visualização não podem ser atualizadas para uma versão suportada de disponibilidade geral. |Estes matrizes virtuais têm a ativação pós-falha para a versão de disponibilidade geral, utilizando um fluxo de trabalho do após desastre (DR) de recuperação. |
| **2.** |Disco de dados de aprovisionamento |Uma vez aprovisionou um disco de dados de um determinado tamanho especificado e criar a matriz de Virtual StorSimple correspondente, tem não expandir ou reduzir o disco de dados. A tentar fazer resultados uma perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo está associado a um domínio, aplicar uma política de grupo que pode afetar negativamente a operação de dispositivo. |Certifique-se de que a matriz de virtual destina-se no seu próprio unidade organizacional (UO) do Active Directory e não existem objetos de política de grupo (GPO) são aplicados ao mesmo. |
| **4.** |IU da web do local |Se as funcionalidades de segurança avançada estiverem ativadas no Internet Explorer (IE ESC), algumas páginas de IU da web de locais como resolução de problemas ou a manutenção poderão não funcionar corretamente. Botões nestas páginas também poderão não funcionar. |Desative funcionalidades de segurança avançada do Internet Explorer. |
| **5.** |IU da web do local |Numa máquina virtual de Hyper-V, as interfaces de rede na web IU são apresentadas como 10 Gbps das interfaces. |Este comportamento é um reflexão do Hyper-V. Hyper-V Mostra sempre a 10 Gbps para adaptadores de rede virtual. |
| **6.** |Volumes em camadas ou partilhas |Intervalo de byte bloqueio para aplicações que funcionam com StorSimple volumes em camadas não é suportada. Se o bloqueio de intervalo de byte estiver ativado, criação de camadas do StorSimple não funciona. |Medidas recomendadas incluem: <br></br>Desative o intervalo de byte bloquear na lógica da sua aplicação.<br></br>Optar por colocar os dados para esta aplicação em volumes localmente afixados, por oposição a volumes em camadas.<br></br>*Advertência*: quando utilizar localmente afixado volumes e o bloqueio de intervalo de byte está ativado, o volume localmente afixado pode ser online mesmo antes do restauro foi concluído. Nessas instâncias, se for um restauro em curso, em seguida, tem de aguardar o conclusão do restauro. |
| **7.** |Partilhas em camadas |Trabalhar com ficheiros grandes pode resultar em camada lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o ficheiro maior é inferior a 3 de % do tamanho da partilha. |
| **8.** |Utilizar a capacidade de partilhas |Poderá ver partilhar consumo quando não existe nenhum dado na partilha. Este consumo é porque a capacidade utilizada para partilhas inclui metadados. | |
| **9.** |Recuperação após desastre |Só pode efetuar a recuperação após desastre de um servidor de ficheiros ao mesmo domínio que o dispositivo de origem. Recuperação de desastres para um dispositivo de destino no outro domínio não é suportada nesta versão. |Está implementado numa versão posterior. Para obter mais informações, aceda a [ativação pós-falha e recuperação após desastre para a matriz de Virtual StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |As matrizes de Virtual StorSimple não podem ser geridas através do PowerShell do Azure nesta versão. |Todas as a gestão de dispositivos virtuais deve ser efetuada através do portal do Azure e a IU da web do local. |
| **11.** |Alteração de palavra-passe |A consola de dispositivos de matriz virtual apenas aceita entrada no en-us formato de teclado. | |
| **12.** |CHAP |Não não possível remover as credenciais CHAP depois de criado. Além disso, se modificar as credenciais do CHAP, tem de colocar os volumes offline e, em seguida, colocá-los online para a alteração produza efeito. |Este problema é resolvido numa versão posterior. |
| **13.** |servidor de iSCSI |O 'utilizado armazenamento' apresentadas para um volume de iSCSI pode ser diferente no serviço StorSimple Manager de dispositivo e o anfitrião de iSCSI. |O anfitrião iSCSI tem a vista de sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume foi no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tem uma alternativa dados fluxo (anúncios) associados à mesma, dos anúncios não é uma cópia de segurança ou restaurados através de recuperação após desastre, clone e recuperação ao nível do Item. | |
| **15.** |Servidor de ficheiros |As ligações simbólicas não são suportadas. | |
| **16.** |Servidor de ficheiros |Os ficheiros protegidos pelo Windows sistema EFS (Encrypting File) quando copiadas ou armazenados no resultado de servidor de ficheiros de matriz Virtual StorSimple uma configuração suportada.  | |
| **17.** |Atualizações |Se vir o erro código: 2359302 (hex 0x240006) ao tentar instalar uma correção através da IU local, em seguida, isto implica que a correção já está instalada no seu dispositivo.   | |
| **18.** |Atualizações |Se utilizar a IU da web local para instalar a atualização 1 na sua matriz virtual, certifique-se de que está a executar a atualização 0,6. Se estiver a executar uma versão inferior ao atualizar a 0,6, tem de instalar primeiro a atualização 0,6 e, em seguida, aplicar Update 1. Se instalou a atualização 1.0 diretamente de uma versão de 0,6 anterior à atualização, em seguida, perderá algumas atualizações e os gráficos de monitorização não irão funcionar.   | |


## <a name="next-steps"></a>Passos seguintes
[Instalar a atualização 1.0](storsimple-virtual-array-install-update-1.md) na sua matriz de Virtual StorSimple.

## <a name="references"></a>Referências
Está à procura de uma nota de versão mais antiga? Vá para:
*  [Notas de versão 0,6 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-06-release-notes.md)
* [Notas de versão 0.5 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Notas de versão 0.4 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de versão 0,3 de atualização de matriz Virtual StorSimple](storsimple-ova-update-03-release-notes.md)
* [Notas de versão de atualização de matriz Virtual StorSimple 0.1 e 0,2](storsimple-ova-update-01-release-notes.md)
* [Notas de lançamento de disponibilidade geral de matriz Virtual StorSimple](storsimple-ova-pp-release-notes.md)
