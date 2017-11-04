---
title: "Segurança para o dispositivo StorSimple | Microsoft Docs"
description: "Descreve as convenções de segurança, diretrizes e as considerações e explica como instalar e operar o dispositivo StorSimple de forma segura."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: a178e8880bcbcada9d66eaacf5ccbdb7c55957cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Em segurança instalar e operar o dispositivo StorSimple
![Ícone de aviso](./media/storsimple-safety/IC740879.png)
![ícone de aviso de segurança de leitura](./media/storsimple-safety/IC740885.png) **leitura informações de estado de funcionamento e segurança**

Ler todas as informações de estado de funcionamento e segurança neste artigo que se aplica para o seu dispositivo do Microsoft Azure StorSimple. Mantenha os guias de impressos fornecidos com o dispositivo StorSimple para consulta futura. Falha ao seguir as instruções e corretamente configurado, utilizar e mais importantes para si para este produto pode aumentar o risco de injury grave ou death ou prejudicar para o dispositivo ou dispositivos. A [versão transferível deste guia](http://www.microsoft.com/download/details.aspx?id=44233) também está disponível.

## <a name="safety-icon-conventions"></a>Convenções de ícone de segurança
Seguem-se os ícones que encontrará quando rever as precauções de segurança para ser observados quando configurar e executar o seu dispositivo do Microsoft Azure StorSimple.

| Ícone | Descrição |
|:--- |:--- |
| ![Ícone de risco](./media/storsimple-safety/IC740879.png) **perigo!** |Indica uma situação hazardous que, se não evitadas, irá resultar no death ou injury grave. Esta palavra de sinal está a ser limitada para as situações mais extremos. |
| ![Ícone de aviso](./media/storsimple-safety/IC740879.png) **aviso!** |Indica uma situação hazardous que, se não evitadas, pode resultar em death ou injury grave. |
| ![Ícone de aviso](./media/storsimple-safety/IC740879.png) **cuidado!** |Indica uma situação hazardous que, se não evitadas, pode resultar em injury moderada ou secundária. |
| ![Tenha em atenção ícone](./media/storsimple-safety/IC740881.png) **aviso:** |Indica informações consideradas importantes, mas não relacionados com hazard. |
| ![Ícone de Shock elétrica](./media/storsimple-safety/IC740882.png) **Hazard Shock elétrica** |Tensão elevada |
| ![Ícone de ponderação pesada](./media/storsimple-safety/IC740883.png) **pesado peso** | |
| ![Nenhum utilizador uma partes ícone](./media/storsimple-safety/IC740879.png) **sem partes de uma de utilizador** |Não acedam a menos que preparada corretamente. |
| ![Ícone de aviso de segurança de leitura](./media/storsimple-safety/IC740885.png)**ler primeiro todas as instruções** | |
| ![Hazard ícone de sugestão](./media/storsimple-safety/IC740886.png) **sugestão Hazard** | |

## <a name="handling-precautions"></a>Precauções de processamento
![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![ponderação pesada ícone](./media/storsimple-safety/IC740883.png) **aviso!** 

Para reduzir o risco de injury:

* Um bastidor totalmente configurado pode pesar até 32 kg (70 lbs); Não tente comparação de precisão por si.
* Antes de mover o bastidor, certifique-se sempre de que duas pessoas estão disponíveis para processar a ponderação. Lembre-se de que uma pessoa tentar este peso de comparação de precisão pode suportar injuries.
* Não de comparação de precisão o bastidor por identificadores de energia e arrefecimento módulos (PCMs), localizados em rear da unidade. Estes não foram concebidos para efetuar a ponderação.

## <a name="connection-precautions"></a>Precauções de ligação
![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![Shock elétrica ícone](./media/storsimple-safety/IC740882.png) **aviso!**

Para reduzir a probabilidade de injury, shock elétrica ou death:

* Quando utiliza a tecnologia de várias origens de AC, desligue todas as de alimentação de energia para o isolamento de conclusão.
* Desligue permanentemente a unidade antes de mover, ou se considerar que tem de ficar danificado de qualquer forma.
* Forneça uma ligação de earth elétrica seguro para os cabos de alimentação de energia. Certifique-se de que grounding do bastidor cumpre os requisitos de national e locais antes de aplicar energia.
* Certifique-se de que a ligação de energia sempre é interrompida antes da remoção de um PCM da inclusão.
* Dado que o plug no cabo de alimentação de energia é o principal desligue o dispositivo, certifique-se de que as saídas de socket estão localizadas perto do equipamento e estejam facilmente acessível.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![Shock elétrica ícone](./media/storsimple-safety/IC740882.png) **aviso!**

Para reduzir a probabilidade de sobreaquecimento ou acionados das ligações elétrica:

* Forneça uma origem de energia adequado com a proteção de sobrecarga elétrica para cumprir os requisitos detalhados na especificação técnica.
* Não utilize cabos de energia bifurcated ("Y" clientes potenciais clientes potenciais).
* Para cumprir os requisitos thermal, emission e segurança aplicável, não abrange deve ser removida e todos os bays tem de ser preenchidos com módulos de plug-in ou unidade espaços em branco.
* Certifique-se de que o equipamento é utilizado na forma especificada pelo fabricante. Se esta equipamento for utilizado de forma não especificada pelo fabricante, a proteção fornecida o equipamento poderá ser afetada.

![Tenha em atenção ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Para o funcionamento correto do seu equipamento e para evitar danos de produto:

* As portas de RJ45 at the back of o dispositivo são para apenas uma ligação de Ethernet. Estes não devem estar ligados a uma rede do umts.
* Não se esqueça de instalar o dispositivo num bastidor que possam acomodá uma estrutura de arrefecimento de repetição de frente.
* Módulos de todos os plug-ins e em branco plates fazem parte da cobertura do sistema. Estes tem apenas removidos quando uma substituição pode ser adicionada imediatamente. O sistema não podem ser executado sem todos os módulos ou espaços em branco no local.

## <a name="rack-system-precautions"></a>Bastidor precauções de sistema
Os seguintes requisitos de segurança têm de ser considerados ao montar o dispositivo num bastidor Cab.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![Hazard ícone de sugestão](./media/storsimple-safety/IC740886.png) **aviso!**

Para reduzir a probabilidade de injury de uma sugestão através de:

* A estrutura de bastidor deve suportar a ponderação total das caixas de instalado e deve incorporar funcionalidades stabilizing adequadas impedir que o bastidor tipping ou que está a ser enviado através de durante a instalação ou utilização normal.
* Quando carregar um bastidor, cheias bastidor na parte inferior e vazio na parte superior para baixo.
* Não, faça deslize mais do que um bastidor fora bastidor um momento para evitar o risco de bastidor toppling ao longo.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![Shock elétrica ícone](./media/storsimple-safety/IC740882.png) **aviso!**

Para reduzir a probabilidade de injury, shock elétrica ou death:

* Bastidor deve ter um sistema de distribuição elétrica seguro. Tem de fornecer proteção superior atual para o bastidor e não deve ser sobrecarregado pelo número total de inclusões instalado. A classificação de consumo de energia elétrica apresentada no nameplate deve ser respeitada.
* O sistema de distribuição elétrica tem de fornecer um zero fiável para cada bastidor no bastidor.
* A estrutura do sistema de distribuição elétrica tem de ter em consideração a fuga de zero total atual de todas as fontes de alimentação em todas as inclusões. Tenha em atenção que cada fonte de alimentação em cada bastidor possui um atual de fuga de zero de 1,0 máximo de mA 60 Hz, 264 volts. Bastidor pode necessitar de etiquetagem com "elevada fugas atual. Ligação de zero (earth) é essencial antes de ligar uma alimentação."
* Bastidor, quando configurado com as inclusões, tem de cumprir os requisitos de segurança de UL 60950-1 e IEC 60950-1/EN 60950-1.

![Tenha em atenção ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Para o adequada de arrefecimento do seu sistema de bastidor:

* Certifique-se de que a estrutura de bastidor leva em consideração o bastidor máximo operativo ambiente temperatura de 35 graus Celsius (95 graus Fahrenheit).
* O sistema é utilizado com a instalação low-pressure, esgotar os rear (back-pressão criado por portas de bastidor e obstacles para não ter mais de 5 Pascal [0.5 mm máximo medidor]).

## <a name="power-cooling-module-pcm-precautions"></a>Precauções de módulo de arrefecimento (PCM) de energia
O dispositivo foi concebido para funcionar com dois PCMs. Cada um dos PCMs tem uma fonte de alimentação e uma ventoinha dual-eixo. Durante uma condição crítica, o sistema permite a uma falha de uma fonte de alimentação ao continuar as operações normais. Dois PCMs (e, por conseguinte, fontes de energia) sempre tem de estar instalado. Um único PCM não fornecer a energia redundante. Por conseguinte, a falha de um mesmo PCM pode resultar em tempo de inatividade ou possíveis perdas de dados.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![Shock elétrica ícone](./media/storsimple-safety/IC740882.png) **aviso!**

Para reduzir a probabilidade de injury, shock elétrica ou death:

* Não remova bastidores o PCM. Não há um risco de electric shock no interior. Devolver o PCM e obter uma substituição, [contacte a Microsoft Support](storsimple-contact-microsoft-support.md).

![Tenha em atenção ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Para o funcionamento correto do seu equipamento e para evitar danos de produto:

* Tem de substituir o PCM falhada dentro de 24 horas. Depois de remover um PCM para substituição, a substituição têm de ser concluída 10 minutos após a remoção.
* Não remova um PCM, exceto se uma substituição pode ser instalada imediatamente. A inclusão não tem de ser utilizada sem todos os módulos no local.

## <a name="electrostatic-discharge-esd-precautions"></a>Precauções discharge electrostatic (ESD)
![Tenha em atenção ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Observe as seguintes precauções relacionadas com ESD.

* Certifique-se de que instalou e selecionada uma strap wrist ou ankle antistatic adequado.
* Observe todas as precauções ESD convencionais quando o processamento de módulos e componentes.
* Evite contacto com os componentes de backplane e conectores do módulo.
* Danos ESD não é abrangido por garantia.

## <a name="battery-disposal-precautions"></a>Precauções disposal de bateria
Fonte de alimentação utiliza uma bateria especial para proteger o conteúdo da memória durante falhas de energia temporário, curta duração. A bateria é seated no PCM. Mantenha as seguintes informações em mente sobre a bateria.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) **aviso!**

Para reduzir o risco de shorts, fire, explosão, injury ou death:

* Eliminar baterias utilizadas em conformidade com regulamentos national/regionais.
* Não desassemblagem, crush, térmico acima 60 graus Celsius (140 graus Fahrenheit) ou incinerate. Substitua a bateria PCM apenas uma bateria fornecida. Utilização de outra bateria poderá representam um risco de fire ou explosão.
* Utilize caps de fim protective as baterias se estes são removidas da fonte de alimentação.

![Tenha em atenção ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Quando o envio ou caso contrário as baterias de transporte por ondas eletromagnéticas, siga o documento de orientação de bateria IATA Lithium disponível em [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Depois de rever estes avisos de segurança, os passos seguintes são descompactar, em bastidor e instalar o seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
* Para um dispositivo 8100, aceda a [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).
* Para um dispositivo 8600, aceda a [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

