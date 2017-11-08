---
title: "Indicadores de monitorização do StorSimple | Microsoft Docs"
description: Descreve os leve - emitting diodes (LEDs) e alarmes audible utilizados para monitorizar o estado do dispositivo StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 9ae0caec211dc1199f0abd2ce9bc0c7ad11c02ec
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Utilize StorSimple indicadores de monitorização para gerir o seu dispositivo


## <a name="overview"></a>Descrição geral
O dispositivo StorSimple inclui leve - emitting diodes (LEDs) e alarmes que pode utilizar para monitorizar o estado global do dispositivo StorSimple e módulos. Os indicadores de monitorização podem ser encontrados nos componentes de hardware de inclusão principal do dispositivo e a inclusão EBOD. Os indicadores de monitorização podem ser LEDs ou audible alarmes.

Existem três Estados de LED utilizados para indicar o estado de um módulo: verde, flashing verde red amber ou amber vermelho.  

* Verde LEDs representam um bom estado de funcionamento.  
* Flashing verde para representam de LEDs red amber a presença das condições não críticos que poderão necessitar de intervenção do utilizador.  
* Vermelho amber LEDs indicarem que existe uma falha crítica presente no módulo.  

O resto deste artigo descreve os vários LEDs de indicador de monitorização, as respetivas localizações no dispositivo StorSimple, o estado do dispositivo, consoante os Estados de LED e qualquer alarmes audible associados.

## <a name="front-panel-indicator-leds"></a>Indicador de painel frontal LEDs
Painel de início, também conhecido como o *painel operações* ou *painel ops*, apresenta o estado agregado de todos os módulos no sistema. O painel frontal é idêntico no StorSimple principal e a inclusão EBOD e é ilustrado abaixo.  

   ![Painel frontal do dispositivo][1]

O painel frontal contém indicadores dos seguintes:  

1. Botão Mute
2. Indicador de energia LED (vermelho/verde-amber)
3. Indicador de falhas de módulo GUIADO (no red-amber/DESATIVADA)
4. Indicador de falhas lógica GUIADO (no red-amber/OFF
5. Apresentação de ID de unidade  

A principal diferença entre os painel frontal LEDs para o dispositivo e as para o bastidor EBOD é o **o número de identificação de unidade de sistema** mostrado no ecrã do LED. A unidade de predefinição ID apresentado no dispositivo é **00**, enquanto que o ID de unidade predefinido apresentado na caixa EBOD é **01**. Isto permite-lhe diferenciar entre o dispositivo e a inclusão EBOD rapidamente quando o dispositivo está ligado. Se o dispositivo está desativado, utilize as informações fornecidas nos [ativar um novo dispositivo](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) para diferenciar o dispositivo da inclusão EBOD.  

## <a name="front-panel-led-status"></a>Painel frontal LED Estado
Utilize a tabela seguinte para identificar o Estado indicado pelo LEDs no painel frontal para o dispositivo ou a inclusão EBOD.  

| Energia do sistema | Falha do módulo | Índice de falhas lógica | Alarme | Estado |
| --- | --- | --- | --- | --- |
| Amber vermelho |OFF |OFF |N/D |Energia AC perdida, a utilizar na cópia de segurança energia, ou de energia de AC e o controlador de módulos foram removidos. |
| Verde |ON |ON |N/D |Estado de teste de ativação painel OPS (5s) |
| Verde |OFF |OFF |N/D |Ligar, todas as funções de boas |
| Verde |ON |N/D |Falhas PCM LEDs, falhas de ventoinha LEDs |Quaisquer falhas PCM, falhas de ventoinha, ativação pós-falha ou em temperatura |
| Verde |ON |N/D |Módulo de e/s LEDs |Quaisquer falhas de módulo de controlador |
| Verde |ON |N/D |N/D |Falhas de lógica de inclusão |
| Verde |Flash |N/D |Estado do módulo LED no módulo de controlador. Falhas PCM LEDs, falhas de ventoinha LEDs |Tipo de módulo de controlador desconhecido instalado, I2C bus falha, o erro de configuração do controlador módulo vital produto dados (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Indicador de módulo (PCM) arrefecimento LEDs de energia
Indicador de módulo (PCM) arrefecimento Power LEDs pode ser encontrado de cópia de segurança da inclusão principal ou bastidor EBOD em cada módulo PCM. Este tópico descreve como utilizar os LEDs seguintes para monitorizar o estado do dispositivo StorSimple.  

* PCM LEDs para o bastidor primário
* PCM LEDs para o bastidor EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM LEDs para o bastidor primário
O dispositivo StorSimple tem um módulo PCM 764W com um bateria adicional. A ilustração seguinte mostra o painel LED para o dispositivo.  

   ![PCM LEDs na caixa primária][2]

Legenda de LED:

1. Falha de energia de AC
2. Falha de ventoinha
3. Falhas de bateria
4. PCM OK
5. Falha de DC
6. Bateria boa  

O estado do PCM é indicado no painel LED. O painel de PCM LED de dispositivo tem seis LEDs. Quatro estes LEDs apresentar o estado da fonte de alimentação e a ventoinha. As restantes dois LEDs indicam o estado do módulo bateria cópia de segurança no PCM. Pode utilizar as tabelas seguintes para determinar o estado do PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Indicador PCM LEDs de fonte de alimentação e ventoinha
| Estado | PCM OK (verde) | Falha de AC (amber) | Ventoinha falhar (amber) | Falha de DC (amber) |
| --- | --- | --- | --- | --- |
| Não existem AC energia (bastidor) |OFF |OFF |OFF |OFF |
| Sem energia de AC (apenas para este PCM) |OFF |ON |OFF |ON |
| AC está presente no PCM - OK |ON |OFF |OFF |OFF |
| Falha PCM (ventoinha falhar) |OFF |OFF |ON |N/D |
| Falhas PCM (através de amp, através de tensão, através de atual) |OFF |ON |ON |ON |
| PCM (ventoinha fora tolerância) |ON |OFF |OFF |ON |
| Modo de reserva dinâmica |Flashing |OFF |OFF |OFF |
| Transferência de firmware PCM |OFF |Flashing |Flashing |Flashing |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Indicador PCM LEDs para a cópia de segurança bateria
| Estado | Bateria bom (verde) | Falhas de bateria (amber) |
| --- | --- | --- |
| Bateria não está presente |OFF |OFF |
| Bateria presente e cobrada |ON |OFF |
| Discharge charging ou a manutenção de bateria |Flashing |OFF |
| Falhas de "de forma recuperável" da bateria (recuperáveis) |OFF |Flashing |
| Falhas de "disco rígida" da bateria (não recuperável) |OFF |ON |
| Bateria disarmed |Flashing |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM LEDs para o bastidor EBOD
A inclusão EBOD tem um PCM 580W e nenhum bateria adicional. O painel PCM para o bastidor EBOD tem indicador LEDs apenas para as fontes de alimentação e a ventoinha. A ilustração seguinte mostra estes LEDs.

   ![PCM LEDs na caixa EBOD][3] 

Pode utilizar a tabela seguinte para determinar o estado do PCM.  

| Estado | PCM OK (verde) | Falha de AC (amber) | Ventoinha falhar (amber) | Falha de DC (amber) |
| --- | --- | --- | --- | --- |
| Não existem AC energia (bastidor) |OFF |OFF |OFF |OFF |
| Sem energia de AC (apenas para este PCM) |OFF |ON |OFF |ON |
| AC está presente no PCM – OK |ON |OFF |OFF |OFF |
| Falha PCM (ventoinha falhar) |OFF |OFF |ON |X |
| Falhas PCM (através de amp, através de tensão, através de atual |OFF |ON |ON |ON |
| PCM (ventoinha fora tolerância) |ON |OFF |OFF |ON |
| Modelo de reserva dinâmica |Flashing |OFF |OFF |OFF |
| Transferência de firmware PCM |OFF |Flashing |Flashing |Flashing |

## <a name="controller-module-indicator-leds"></a>Indicador de módulo de controlador LEDs
O dispositivo StorSimple contém LEDs para o controlador principal e os módulos de controlador EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitorização LEDs para o controlador principal
A ilustração seguinte ajuda-o a identificar os LEDs no controlador de primário. (Todos os componentes são listados para ajudar a orientação.)  

   ![LEDs - controlador principal de monitorização][4]

Utilize a tabela seguinte para determinar se o módulo de controlador está a funcionar corretamente.  

### <a name="controller-indicator-leds"></a>Indicador de controlador LEDs
| LED | Descrição |
| --- | --- |
| ID LED (azul) |Indica que o módulo está a ser identificado. Se o LED azul é blinking num controlador de execução, o controlador é o controlador de Active Directory e a outros é o controlador de reserva dinâmica. Para obter mais informações, consulte [identificar controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Falhas LED (amber) |Indica uma falha no controlador de. |
| LED OK (verde) |Gradual verde indica que o controlador está OK. Flashing verde indica um erro de configuração VPD de controlador. |
| Atividade SAS LEDs (verde) |Gradual verde indica uma ligação com nenhuma atividade atual. Flashing verde indica que a ligação tem actividade em curso. |
| Estado de Ethernet LEDs |Lado direito indicar atividade de ligação/rede: Active Directory, (flashing verde) de ligação (verde gradual) atividade de rede. Lado esquerdo indica a velocidade da rede: (amarelo) 1000 Mb/s, (OFF) e (verde) 100 Mb/s, 10 Mb/s. Consoante o modelo de componentes, este leve poderá blink, mesmo que a interface de rede não está ativada. |
| POST LEDs |Indica o progresso de arranque quando o controlador está ativado. Se o dispositivo StorSimple não conseguir efetuar o arranque, esta LED ajudará Support da Microsoft identificam o ponto no processo de arranque à qual a falha ocorreu. |

> [!IMPORTANT]
> Se o LED de falha é lit, há um problema com o módulo de controlador que poderá ser resolvido reiniciando o controlador. Contacte Support da Microsoft se reiniciar o controlador não resolver este problema.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitorização LEDs para o EBOD (bastidor EBOD)
Cada um dos controladores de 6 Gb/s SAS EBOD tem LEDs indicam o respetivo estado conforme mostrado na ilustração seguinte.  

  ![Monitorização LEDs - bastidor EBOD][5]

Utilize a tabela seguinte para determinar se o módulo de controlador EBOD está a funcionar normalmente.  

### <a name="ebod-controller-module-indicator-leds"></a>Indicador de módulo de controlador EBOD LEDs
| Estado | Módulo de e/s OK (verde) | Falhas de módulo de e/s (amber) | Atividade de porta de anfitrião (verde) |
| --- | --- | --- | --- |
| Módulo de controlador OK |ON |OFF |- |
| Falha do módulo de controlador |OFF |ON |- |
| Nenhuma ligação de porta de anfitrião externo |- |- |OFF |
| Ligação de porta de anfitrião externo – nenhuma atividade |- |- |ON |
| Ligação de porta de anfitrião externo - atividade |- |- |Flashing |
| Erro de metadados do módulo de controlador |Flashing |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Indicador de unidade de disco LEDs de inclusão principal e inclusão EBOD
O dispositivo StorSimple tem unidades de disco localizadas na inclusão principal e a inclusão EBOD. Cada unidade de disco contém monitorização indicador LEDs, conforme descrito nesta secção. 

Para as unidades de disco, o estado da unidade é indicado por uma verde LED e um LED red amber montados no início de cada módulo de carrier da unidade. A ilustração seguinte mostra estes LEDs.

  ![Unidade de disco LEDs][6]

Utilize a tabela seguinte para determinar o estado de cada unidade de disco, o que por sua vez afeta o painel global frontal LED de estado.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Indicador de unidade de disco LEDs para o bastidor EBOD
| Estado | Atividade LED de OK (verde) | Falhas LED (amber vermelho) | Painel de ops LED de associados |
| --- | --- | --- | --- |
| Nenhuma unidade instalado |OFF |OFF |Nenhuma |
| Unidade instalada e operacional |Flashing / com atividade |X |Nenhuma |
| Conjunto de identidade de dispositivo de serviços de bastidor SCSI (SES) |ON |Flashing 1 segundo em/1 segundo desativado |Nenhuma |
| Conjunto de bits de falhas de dispositivo SES |ON |ON |Lógica falha (vermelho) |
| Falha de circuito de controlo de energia |OFF |ON |Falha do módulo (vermelho) |

## <a name="audible-alarms"></a>Alarmes audible
Um dispositivo StorSimple contém alarmes audible associados a inclusão principal e a inclusão EBOD. Um alarme audible está localizado no painel frontal (também conhecido como o painel de ops) de ambas as caixas. O alarme audible indica quando está presente uma condição de falha. As seguintes condições irão ativar o alarme:  

* Falhas de ventoinha ou falha
* Tensão fora do intervalo
* Ativação pós-falha ou com a condição de temperatura
* Thermal overrun
* Falhas de sistema
* Índice de falhas lógica
* Falha de alimentação de energia
* Remoção de uma potência arrefecimento módulo (PCM)  

A tabela seguinte descreve os vários Estados de alarme.  

### <a name="alarm-states"></a>Estados de alarme
| Estado de alarme | Ação | Acção conjunta mute botão premido |
| --- | --- | --- |
| S0 |Modo normal: automática |Aviso sonoro duas vezes |
| S1 |Modo de falhas: 1 segundo em/1 segundo desativado |Transição para S2 ou S3 (consulte as notas de) |
| S2 |Modo de lembrar: beep intermitente |Nenhuma |
| S3 |Modo muted: automática |Nenhuma |
| S4 |Modo de falhas crítico: alarme contínua |Não está disponível: desativar som não Active Directory |

> [!NOTE]
> * Estado de alarme S1, se não premir mute em dois minutos, o estado passa automaticamente para S2 ou S3.  
> * Os Estados de alarme S1 para S4 volte a S0 depois da condição de falha está desmarcada.  
> * Estado de falha crítica S4 pode ser introduzido a partir de qualquer outro Estado.  


Pode mute o alarme audible, premindo o botão mute no painel ops. Muting automática irá ocorrer depois de dois minutos se o comutador mute não é operado manualmente. Quando o alarme é muted, continuará a som com beeps intermitentes curtos para indicar que ainda existe um problema. O alarme será automática quando todos os problemas são eliminados.

A tabela seguinte descreve as várias condições de alarme.

### <a name="alarm-conditions"></a>Condições de alarme
| Estado | Gravidade | Alarme | OPS painel LED |
| --- | --- | --- | --- |
| Alerta PCM – perda de energia de DC de um único PCM |Falhas – sem perda de redundância |S1 |Falha do módulo |
| Alerta PCM – perda de energia de DC de um único PCM |Falhas – perda de redundância |S1 |Falha do módulo |
| Falha de ventoinha PCM |Falhas – perda de redundância |S1 |Falha do módulo |
| O módulo SBB detetou falhas PCM |Índice de falhas |S1 |Falha do módulo |
| PCM removido |Erro de configuração |Nenhuma |Falha do módulo |
| Erro de configuração de inclusão |Índice de falhas – crítico |S1 |Falha do módulo |
| Alerta de aviso de temperatura baixa |Aviso |S1 |Falha do módulo |
| Alerta de aviso de temperatura elevada |Aviso |S1 |Falha do módulo |
| Ao longo de alarme de temperatura |Índice de falhas – crítico |S1 |Falha do módulo |
| Falha de barramento I2C |Falhas – perda de redundância |S1 |Falha do módulo |
| Erro de comunicação (I2C) de painel do OPS Manager |Índice de falhas – crítico |S1 |Falha do módulo |
| Erro de controlador |Índice de falhas – crítico |S1 |Falha do módulo |
| Falhas de módulo de interface SBB |Índice de falhas – crítico |S1 |Falha do módulo |
| Falha no módulo de interface SBB – não funcionar módulos restante |Índice de falhas – crítico |S4 |Falha do módulo |
| Módulo de interface SBB removido |Aviso |Nenhuma |Falha do módulo |
| Falha de controlo de energia de unidade |Aviso – sem perda de energia de unidade |S1 |Falha do módulo |
| Falha de controlo de energia de unidade |Índice de falhas – crítico; Falha de energia de unidade |S1 |Falha do módulo |
| Unidade removida |Aviso |Nenhuma |Falha do módulo |
| Energia suficiente disponível |Aviso |Nenhum |Falha do módulo |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [StorSimple componentes de hardware e estado](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


