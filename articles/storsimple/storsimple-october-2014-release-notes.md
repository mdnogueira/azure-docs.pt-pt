---
title: "Notas de versão 0.1 de atualização de 8000 do StorSimple | Microsoft Docs"
description: "Descreve as novas funcionalidades e correções, problemas abertos e soluções disponíveis de Outubro de 2014 versão do Microsoft Azure StorSimple (atualização 0.1)."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: fd35e3c3-4770-460c-999d-f72ab7053a20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 4dfd3973593a94adfc15a6e15d69c697e13998af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>Notas de versão 0.1 de atualização de série de 8000 do StorSimple – Outubro de 2014
## <a name="overview"></a>Descrição geral
As seguintes notas de versão identificam os problemas abertos críticos para a atualização de série 8000 do StorSimple 0.1 lançada em Outubro de 2014. Também contêm uma lista das atualizações de software e firmware StorSimple incluído nesta versão. Esta é a primeira versão depois da versão de lançamento de série 8000 do StorSimple foi disponibilizada geralmente em Julho de 2014 e corresponde à versão do software 6.3.9600.17312.  

Recomendamos que procurar e aplicar as atualizações disponíveis imediatamente depois de instalar o dispositivo. Também pode ativar as atualizações automáticas para transferir e instalar atualizações de alta prioridade da Microsoft, assim são lançadas. Para obter mais informações, consulte como [atualizar o dispositivo StorSimple](storsimple-update-device.md).  

Reveja as informações contidas nas notas de versão antes de implementar as atualizações na sua solução StorSimple.  

> [!IMPORTANT]
> * Utilize o serviço StorSimple Manager e não do Windows PowerShell para StorSimple para instalar as atualizações de Outubro.  
> * As atualizações demoram, normalmente, cerca de 3 horas para concluir.  
> * A versão de Outubro do StorSimple não contém quaisquer atualizações para o dispositivo virtual StorSimple. Pode continuar a aplicar as atualizações disponíveis do Windows, incluindo correções de segurança recente, mas não irá ver uma alteração de versão para o dispositivo virtual.  
> 
> 

Certifique-se de que os seguintes pré-requisitos são cumpridos antes de atualizar o seu dispositivo StorSimple.  

* Certifique-se de que ambos os controladores de dispositivo estão em execução antes de procurar atualizações. Se o controlador não está em execução, a análise irá falhar. Para verificar que os controladores estão em bom estado, navegue até à **estado do Hardware** sob o **manutenção** página. Se existirem componentes que **necessitem de atenção**, contacte a Microsoft Support antes de prosseguir.  
* Certifique-se de que os IPs fixos para o controlador 0 e o controlador 1 são encaminháveis e podem estabelecer ligação à Internet estes campos são utilizados para manutenção de atualizações para o dispositivo. Pode utilizar o [cmdlet Test-Connection](https://technet.microsoft.com/library/hh849808.aspx) um ping a um endereço conhecido fora da rede, tais como o outlook.com, para verificar se o controlador tem conetividade à rede externa.  
* Certifique-se de que as portas de saída necessárias estão disponíveis no dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte o [requisitos de rede para o dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
* Se a versão do software de dispositivos é anterior ao 6.3.9600.17312 (atualização de Outubro de 2014), desative as portas de dados 2 e dados 3, se estiver ativada, antes de iniciar a atualização. Se deixar a dados 2 ou 3 dados portas ativadas ao aplicar a atualização, pode fazer com que o controlador de dispositivo entrar em modo de recuperação. Tenha em atenção que ao desativar as interfaces de rede, todos os volumes associados serão colocados offline e a e/s serão interrompidos durante a duração da atualização.  

## <a name="whats-new-in-the-october-release"></a>Novidades na versão de Outubro
Esta atualização inclui as seguintes melhorias:

* Agora, pode utilizar o IU do serviço StorSimple Manager para gerir os controladores de dispositivo. Gestão ações incluem reiniciar, encerramento, ou ativar um controlador. Para obter mais informações, aceda a [controladores de dispositivo do StorSimple gerir](storsimple-manage-device-controller.md).  
* Pode agendar a alocação de largura de banda WAN, de acordo com as combinações de dia da semana e hora do dia. Isto permite-lhe efetuar uma melhor utilização de largura de banda WAN durante as horas de ponta. Modelos de largura de banda diferente são permitidos para contentores de volume diferente. Para obter mais informações, aceda a [gestão de modelos de largura de banda do StorSimple](storsimple-manage-bandwidth-templates.md).  
* Pode configurar notificações por correio eletrónico a notificar proativamente a administradores e outros problemas existentes ou futuros possivelmente. Para obter mais informações, aceda a [configurar definições de alerta](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Corrigido na versão de Outubro de problemas
A tabela seguinte fornece um resumo dos problemas que foram corrigidos esta atualização.  

| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Interfaces de rede |Na versão anterior, as interfaces de rede dados 2 e dados 3 foram alternados no software. Esta correção esta atualização. Volte a limpar as definições e desativar estas interfaces de rede antes de instalar a atualização. Depois de instalar a atualização, terá de reconfigurar estas interfaces. |Sim |Não |
| 2 |Pacote de suporte |Na versão anterior, se tiver executado o Windows PowerShell **exportação HcsSupportPackage** registos de cmdlet para obter o controlador de gestão de placas base (BMC), a operação falhou com o seguinte aviso: "a operação foi concluída com êxito em Este controlador, mas não conseguiu no controlador de peer devido aos seguintes erros. Verifique se o elemento de rede é bom estado de funcionamento e se o nó atual pode ligar para o elemento de rede." Este problema ser corrigido agora. |Sim |Não |
| 3 |Ativação pós-falha do dispositivo |Na versão anterior, Ocorreu uma possibilidade de inconsistência de dados se um **detetar a cópia de segurança** tarefa falhou durante uma ativação pós-falha de dispositivo. Este problema ser corrigido agora. |Sim |Não |
| 4 |Ativação pós-falha do dispositivo |Na versão anterior, após uma ativação pós-falha do dispositivo, as cópias de segurança foram visível mas o contentor de volume associado não estava presente no dispositivo de destino. Este problema ser corrigido agora. |Sim |Não |
| 5 |Ativação pós-falha do dispositivo |Na versão anterior, Ocorreu um erro na enumeração de cópias de segurança de nuvem durante a operação de restauro do registo que pode levar a inconsistência de dados se ocorreram problemas de conectividade de nuvem. |Sim |Não |
| 6 |Atualização de firmware |Na versão anterior, a tarefa de atualização de firmware do dispositivo falhou e apresentado um erro que indicado que os cmdlets não reconhecível e a que a atualização falhou como consequência. O controlador, em seguida, Ocorreu um erro no modo de recuperação. Este problema ser corrigido agora. |Sim |Não |
| 7 |Instalação |Tem agora corrigir erros causados pelo dispositivo não a ser instalado corretamente durante a instalação. |Sim |Não |
| 8 |Reposição de fábrica |Opcionalmente, agora pode ignorar a verificação de firmware de reposição de fábrica. Esta é uma alteração a partir da versão anterior. |Sim |Não |
| 9 |Reposição de fábrica |Na versão anterior, quando um cmdlet de reposição de fábrica foi executado, foram efetuadas as verificações de versão de firmware apenas para alguns componentes de hardware. Foram efetuadas as verificações de firmware adicionais após o primeiro reinício no processo, o que pode provocar a reposição de falhar. Esta correção assegura que todas as verificações de firmware são efetuadas quando o reposição de fábrica cmdlet é executado antes do sistema primeiro reinício. |Sim |Não |
| 10 |Rotação de chave de conta de armazenamento |O **Invoke-HcsmServiceDataEncryptionKeyChange** cmdlet utilizado para rodar as chaves de conta de armazenamento agora pede ao utilizador para introduzir a chave de encriptação de dados do serviço. Esta é uma alteração da versão anterior em que a chave de encriptação de dados do serviço foi transmitida como um parâmetro de inline. |Sim |Não |
| 11 |Reativação pós-falha dentro de 24 horas |Durante a recuperação de desastre, a limpeza do dispositivo de origem não foi possível acontecer reativação pós-falha corretamente, que provocou a falha. Esta correção nesta versão. |Sim |Não |

## <a name="known-issues-in-the-october-release"></a>Problemas conhecidos na versão de Outubro
A tabela seguinte fornece um resumo dos problemas conhecidos desta versão.

| Não. | Funcionalidade | Problema | Comentários/solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Reposição de fábrica |Em alguns casos, quando efetua uma reposição de fábrica do dispositivo StorSimple pode estar bloqueada e apresentar esta mensagem: **reposição de fábrica está em curso (fase 8)**. Isto acontece se premir CTRL + C enquanto o cmdlet está em curso. |Não é prima CTRL + C depois de iniciar uma reposição de fábrica. Se já estiver a trabalhar neste estado, contacte a Microsoft Support para passos. |Sim |Não |
| 2 |Reposição de fábrica |Efetue não reposição de fábrica num dispositivo StorSimple que é atualizado de GA para Outubro de 2014 de versão. |Esta operação só funcionará se uma correção está instalada. Contacte Support da Microsoft para obter esta correção necessária. |Sim |Não |
| 3 |Quórum de disco |Em situações raras, se a maioria dos discos no bastidor EBOD de um dispositivo 8600 estiver desligada, resultando em quórum sem disco, em seguida, o agrupamento de armazenamento estarão offline. Irá permanecer offline, mesmo se os discos estão ligados de novo. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte a Microsoft Support para passos seguintes. |Sim |Não |
| 4 |Falhas de instantâneos de nuvem |Em situações raras, um instantâneo na nuvem pode falhar com o erro **foi atingido o limite cópia de segurança máximo**. Isto ocorre se exceder 255 clones online no mesmo dispositivo, do volume original mesmo que tenha sido eliminado. | |Sim |Sim |
| 5 |ID de controlador incorreto |Quando é efetuada uma substituição de controlador, controlador 0 poderá aparecer como controlador 1. Durante a substituição de controlador, quando a imagem ser carregada a partir do nó de elemento de rede, o ID de controlador pode aparecer inicialmente como ID de. o controlador de ponto a ponto Em situações raras, este comportamento também pode ser visto após um reinício do sistema. |Não é necessária qualquer ação do utilizador. Esta situação irá resolver-se automaticamente depois de concluída a substituição de controlador. |Sim |Não |
| 6 |Gráficos de monitorização de dispositivos |O serviço StorSimple Manager, os gráficos de monitorização de dispositivos não funcionam quando básica ou autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. |Modificar a configuração de proxy web para o dispositivo registado no serviço StorSimple Manager, para que a autenticação está definida como NONE. Para tal, execute o Windows PowerShell para o cmdlet Set-HcsWebProxy do StorSimple. |Sim |Sim |
| 7 |Contas de armazenamento |Com o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Esta funcionalidade irá originar uma situação em que não não possível obter dados do utilizador. | |Sim |Sim |
| 8 |Ativação pós-falha do dispositivo |Não é suportada a várias ativações pós-falha de um contentor de volume do mesmo dispositivo de origem para os dispositivos de destino diferente. |Ativação pós-falha de um único dispositivo Inativos para vários dispositivos faz com que os contentores de volume no primeiro dispositivo de ativação pós-falha perder a propriedade de dados. Após a ativação pós-falha, estes contentores de volume serão apresentados ou comportar-se de forma diferente quando vê-las no portal clássico do Azure. |Sim |Não |
| 9 |Instalação |Durante a placa de StorSimple para a instalação do SharePoint, tem de fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 10 |Web proxy |Se a configuração de proxy web tem HTTPS como o protocolo especificado, em seguida, a comunicação de serviço de dispositivo será afetada e o dispositivo será fique offline. Pacotes de suporte também serão gerados no processo de consumir recursos significativos no seu dispositivo. |Certifique-se de que o URL de proxy web tem HTTP como o protocolo especificado. Obter mais informações sobre como [configurar o proxy web para o seu dispositivo](storsimple-configure-web-proxy.md). |Sim |Não |
| 11 |Web proxy |Se configurar e ativar o proxy da web num dispositivo registado, terá de reiniciar o controlador de Active Directory no seu dispositivo. | |Sim |Não |
| 12 |Latência de nuvem de alta e carga de trabalho de e/s elevada |Quando o dispositivo StorSimple encontra uma combinação de latências muito elevado de nuvem (ordem de segundos) e a elevada carga de trabalho de e/s, os volumes do dispositivo aceda num Estado degradado e a e/s pode falhar com um erro de "device não preparado". |Será necessário reiniciar os controladores de dispositivo ou execute uma ativação pós-falha de dispositivo para recuperar desta situação manualmente. |Sim |Não |

## <a name="physical-device-updates-in-the-october-release"></a>Atualizações ao dispositivo físico de Outubro de versão
Quando estas atualizações são aplicadas a um dispositivo físico, a versão do software será alterado para 6.3.9600.17312. Salvo especificação em contrário, estas notas de versão aplicam-se a todos os modelos do dispositivo StorSimple. Para obter mais informações sobre estas atualizações, consulte [atualização de software do dispositivo físico de Outubro de 2014 para aplicação do Microsoft Azure StorSimple](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Controlador de SCSI (SAS) ligados à série e atualizações de firmware de Outubro de versão
Esta versão de atualizações de controladores e firmware no controlador de SAS do seu dispositivo físico. Para obter mais informações sobre a atualização do controlador SAS, consulte [atualização de Outubro de 2014 para controladores de LSI SAS na aplicação do Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987020).   

Esta versão aplica-se uma atualização de firmware cumulativa que aborda problemas de fiabilidade com os componentes de hardware do dispositivo. Para obter mais informações sobre a atualização de firmware, consulte [atualização de firmware de Outubro de 2014 para a aplicação do Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Atualizações ao dispositivo virtual de Outubro de versão
Esta versão não contém quaisquer atualizações para o dispositivo virtual. Aplicar esta atualização não irá alterar a versão do software de um dispositivo virtual.

