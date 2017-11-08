---
title: "Notas de versão 1.2 do StorSimple 8000 série Update | Microsoft Docs"
description: "Descreve as novas funcionalidades, problemas e soluções para StorSimple 8000 série atualização 1.2."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2856cda1fde04ab61b4cf15ad0dcc3db2a9df68
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Atualizar as notas de versão 1.2 do seu dispositivo de série 8000 do StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Descrição geral
As notas de versão seguintes descrevem as novas funcionalidades e identificam os problemas críticos abertos para StorSimple 8000 série atualização 1.2. Também contêm uma lista de software do StorSimple, controlador e atualizações de firmware de disco incluídas nesta versão. 

Atualização 1.2 pode ser aplicada a todos os dispositivos StorSimple com o lançamento (GA), atualização 0.1, atualização 0,2 ou software da atualização 0.3. Atualização 1.2 não está disponível se o dispositivo estiver em execução Update 1 ou atualização 1.1. Se o dispositivo estiver em execução lançamento (GA), [contacte a Microsoft Support](storsimple-contact-microsoft-support.md) para o ajudar a instalar esta atualização.

A tabela seguinte lista as versões do software de dispositivo correspondente a atualizações, 1, 1.1 e 1.2.

| Se executar a atualização... | Esta é a versão do software de dispositivo. |
| --- | --- |
| Atualizar 1.2 |6.3.9600.17584 |
| Atualizar 1.1 |6.3.9600.17521 |
| Atualização 1.0 |6.3.9600.17491 |

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple. Para obter mais informações, consulte como [instalar 1.2 de atualização no dispositivo StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Demora cerca de 5-10 horas para instalar esta atualização (incluindo as atualizações do Windows). 
> * Atualização 1.2 tem o software, os controladores de LSI e atualizações de firmware do disco. Para instalar, siga as instruções em [instalar 1.2 de atualização no dispositivo StorSimple](storsimple-install-update-1.md).
> * Para os novos lançamentos, poderá não ver atualizações imediatamente porque fazemos de uma implementação faseada de atualizações. Análise de atualizações de alguns dias novamente como estes ficará disponível em breve.
> 
> 

## <a name="whats-new-in-update-12"></a>Novidades na atualização 1.2
Estas funcionalidades foram lançado pela primeira vez com atualização 1 que foi disponibilizado um conjunto limitado de utilizadores. Com a versão 1.2 de atualização, a maioria dos utilizadores StorSimple seria consulte as seguintes funcionalidades novas e melhoradas:

* **A migração de 5000 7000 séries para dispositivos de 8000 série** – nesta versão apresenta uma nova funcionalidade de migração, que permite a StorSimple 5000 7000 série aplicação aos utilizadores migrar os seus dados para um dispositivo físico de série 8000 do StorSimple ou virtual aplicação. A funcionalidade de migração tem dois propostas de valor da chave:                                                                  
  
  * **Continuidade do negócio**, Ativando a migração dos dados existentes no aparelhos de séries 5000 7000 para aplicações da 8000 série.
  * **Melhorado ofertas de funcionalidade de aparelhos da 8000 série**, tais como eficiente gestão centralizada de vários aparelhos através do serviço StorSimple Manager, uma melhor classe de hardware e atualizar o firmware, de aplicações virtuais, mobilidade de dados, as funcionalidades e o plano de futura.
    
    Consulte o [guia de migração](http://www.microsoft.com/download/details.aspx?id=47322) para obter detalhes sobre como migrar StorSimple série de 5000 7000 num dispositivo da 8000 série. 
* **Disponibilidade no Portal do Azure Government** – StorSimple está agora disponível no portal do Azure Government. Veja como [implementar um dispositivo StorSimple no Portal do Azure Government](storsimple-deployment-walkthrough-gov.md).
* **Suporte para outros fornecedores de serviços em nuvem** – as outros fornecedores de serviços cloud suportados são Amazon S3, Amazon S3 com RRS, HP e OpenStack (beta).
* **Atualização mais recente APIs de armazenamento** – com esta versão, StorSimple foi atualizado para o serviço de armazenamento do Azure mais recente APIs. Dispositivos de série 8000 do StorSimple que executam versões do software de pré-atualização 1 (versão, 0.1, 0,2 e 0,3) estiver a utilizar versões mais antigas do que 17 de Julho de 2009 as APIs de serviço do Storage do Azure. Conforme indicado na atualização [anúncio sobre a remoção das versões do serviço de armazenamento](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), por 1 de Agosto de 2016, vão ser preteridos estas APIs. É imperativo que aplique 8000 do StorSimple série Update 1 antes de 1 de Agosto de 2016. Se conseguir fazê-lo, dispositivos StorSimple irão parar de funcionar corretamente.
* **Suporte para a zona redundante armazenamento (ZRS)** – com a atualização para a versão mais recente das APIs de armazenamento, a série 8000 do StorSimple irá suportar zona redundante armazenamento (ZRS) para além das localmente redundante armazenamento (LRS) e armazenamento georredundante (GRS ). Consulte este [artigo sobre as opções de redundância do armazenamento do Azure](../storage/common/storage-redundancy.md) para detalhes ZRS.
* **Melhorado experiência de implementação e atualização inicial** – nesta versão, os processos de instalação e atualização foram melhorados. A instalação através do Assistente de configuração foi melhorada para fornecer comentários para o utilizador se a configuração de rede e as definições da firewall estão incorretas. Cmdlets de diagnóstico adicionais foram fornecidos para o ajudar a resolver problemas de rede do dispositivo. Consulte o [artigo sobre implementação de resolução de problemas](storsimple-troubleshoot-deployment.md) para obter mais informações sobre os novos cmdlets de diagnóstico utilizados para resolução de problemas.

## <a name="issues-fixed-in-update-12"></a>Problemas fixos em atualização 1.2
A tabela seguinte fornece um resumo dos problemas que foram corrigidos nas atualizações 1.2, 1.1 e 1.    

| Não. | Funcionalidade | Problema | Corrigido na atualização | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell para StorSimple |Quando um utilizador aceder remotamente o dispositivo StorSimple através do Windows PowerShell para StorSimple e, em seguida, iniciar o Assistente de configuração, uma falha ocorreu logo que dados 0 IP foi introduzido. Agora, é corrigido na atualização 1 Este erro. |Atualização 1 |Sim |Sim |
| 2 |Reposição de fábrica |Em alguns casos, quando efetuar uma reposição de fábrica do dispositivo StorSimple ficou presa e apresentada esta mensagem: **reposição de fábrica está em curso (fase 8)**. Isto aconteceu se premido CTRL + C enquanto o cmdlet estava em curso. Agora é corrigido este erro. |Atualização 1 |Sim |Não |
| 3 |Reposição de fábrica |Depois de a reposição de fábrica de um controlador dupla com falhas, foram permitido para continuar com o registo de dispositivos. Isto resultou uma configuração de sistema não suportado. Na atualização 1, é apresentada uma mensagem de erro e registo é bloqueado num dispositivo que tem uma falha na reposição de fábrica. |Atualização 1 |Sim |Não |
| 4 |Reposição de fábrica |Em alguns casos, foram geradas alertas de erro de correspondência falsos positivos. Já não serão gerados alertas de erro de correspondência incorreto em dispositivos com o Update 1. |Atualização 1 |Sim |Não |
| 5 |Reposição de fábrica |Se uma reposição de fábrica foi interrompida antes da conclusão, o dispositivo introduziu o modo de recuperação e não permitiu a aceder ao Windows PowerShell para StorSimple. Agora é corrigido este erro. |Atualização 1 |Sim |Não |
| 6 |Recuperação após desastre |Foi corrigido um erro de (DR) de recuperação de desastre wherein DR falharão durante a deteção de cópias de segurança do dispositivo de destino. |Atualização 1 |Sim |Sim |
| 7 |Monitorização LEDs |Em determinadas situações, monitorização LEDs at the back of dispositivo não indicar Estado correto. O LED azul foi desativada. DATA 0 e dados 1 LEDs foram flashing, mesmo quando estas interfaces não foram configuradas. Corrigir o problema e monitorização LEDs agora indicam o estado correto. |Atualização 1 |Sim |Não |
| 8 |Monitorização LEDs |Em determinadas situações, depois de aplicar a atualização 1, claro azul no controlador ativo desativada, deste modo, tornando difícil identificar controlador ativo. Este problema nesta versão do patch. |Atualizar 1.2 |Sim |Não |
| 9 |Interfaces de rede |Em versões anteriores, um dispositivo StorSimple configurado com um gateway não encaminháveis internos foi fique offline. Nesta versão, a métrica de encaminhamento para Data 0 tiver sido efetuada a mais baixa; Por conseguinte, mesmo que outras interfaces de rede estão preparados para nuvem, todo o tráfego de nuvem do dispositivo será encaminhado através de dados 0. |Atualização 1 |Sim |Sim |
| 10 |Cópias de segurança |Tem foi corrigido um erro na atualização 1 que provocou a cópias de segurança a falhar após 24 dias na versão patch 1.1 de atualização. |Atualizar 1.1 |Sim |Sim |
| 11 |Cópias de segurança |Um erro em versões anteriores resultou num fraco desempenho para instantâneos de nuvem com taxas de alteração baixa. Corrigir este erro nesta versão do patch. |Atualizar 1.2 |Sim |Sim |
| 12 |Atualizações |Tem foi corrigido um erro na atualização 1, que comunicou uma atualização falhada e causou os controladores entrar em modo de recuperação, nesta versão do patch. |Atualizar 1.2 |Sim |Sim |

## <a name="known-issues-in-update-12"></a>Problemas conhecidos na atualização 1.2
A tabela seguinte fornece um resumo dos problemas conhecidos desta versão.

| Não. | Funcionalidade | Problema | Comentários/solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum de disco |Em situações raras, se a maioria dos discos no bastidor EBOD de um dispositivo 8600 estiver desligada, resultando em quórum sem disco, em seguida, o agrupamento de armazenamento estarão offline. Irá permanecer offline, mesmo se os discos estão ligados de novo. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte a Microsoft Support para passos seguintes. |Sim |Não |
| 2 |ID de controlador incorreto |Quando é efetuada uma substituição de controlador, controlador 0 poderá aparecer como controlador 1. Durante a substituição de controlador, quando a imagem ser carregada a partir do nó de elemento de rede, o ID de controlador pode aparecer inicialmente como ID de. o controlador de ponto a ponto Em situações raras, este comportamento também pode ser visto após um reinício do sistema. |Não é necessária qualquer ação do utilizador. Esta situação irá resolver-se automaticamente depois de concluída a substituição de controlador. |Sim |Não |
| 3 |Contas de armazenamento |Com o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Esta funcionalidade irá originar uma situação em que não não possível obter dados do utilizador. |Sim |Sim | |
| 4 |Ativação pós-falha do dispositivo |Não é suportada a várias ativações pós-falha de um contentor de volume do mesmo dispositivo de origem para os dispositivos de destino diferente. Dispositivo ativação pós-falha de um único dispositivo Inativos para vários dispositivos faz com que os contentores de volume no primeiro dispositivo de ativação pós-falha perder a propriedade de dados. Após a ativação pós-falha, estes contentores de volume serão apresentados ou comportar-se de forma diferente quando vê-las no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante a placa de StorSimple para a instalação do SharePoint, tem de fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 6 |Web proxy |Se a configuração de proxy web tem HTTPS como o protocolo especificado, em seguida, a comunicação de serviço de dispositivo será afetada e o dispositivo será fique offline. Pacotes de suporte também serão gerados no processo de consumir recursos significativos no seu dispositivo. |Certifique-se de que o URL de proxy web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-configure-web-proxy.md). |Sim |Não |
| 7 |Web proxy |Se configurar e ativar o proxy da web num dispositivo registado, terá de reiniciar o controlador de Active Directory no seu dispositivo. | |Sim |Não |
| 8 |Latência de nuvem de alta e carga de trabalho de e/s elevada |Quando o dispositivo StorSimple encontra uma combinação de latências muito elevado de nuvem (ordem de segundos) e a elevada carga de trabalho de e/s, os volumes do dispositivo aceda num Estado degradado e a e/s pode falhar com um erro de "device não preparado". |Será necessário reiniciar os controladores de dispositivo ou execute uma ativação pós-falha de dispositivo para recuperar desta situação manualmente. |Sim |Não |
| 9 |Azure PowerShell |Quando utiliza o cmdlet do StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - primeiro 1 - espera** para selecionar o objeto primeiro para que possa criar uma nova **VolumeContainer** objeto, o cmdlet devolve todos os objetos. |Moldar o cmdlet parênteses da seguinte forma: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - primeiro 1 - espera** |Sim |Sim |
| 10 |Migração |Quando são transmitidos vários contentores de volume para a migração, ETA para cópia de segurança mais recente é preciso apenas para o contentor de volume primeiro. Além disso, migração de paralela será iniciado depois das primeiros 4 cópias de segurança no contentor de volume do primeiro são migradas. |Recomendamos que migrar de um contentor de volume cada vez. |Sim |Não |
| 11 |Migração |Após o restauro, os volumes de mensagens em fila não são adicionados à política de cópia de segurança ou o grupo de disco virtual. |Terá de adicionar estes volumes a uma política de cópia de segurança para criar cópias de segurança. |Sim |Sim |
| 12 |Migração |Depois de concluída a migração, o dispositivo de séries 5000/7000 não têm de aceder os contentores de dados migrados. |Recomendamos que elimina os contentores de dados migrados após a migração estar concluída e consolidada. |Sim |Não |
| 13 |Clone e DR |Um dispositivo StorSimple com o Update 1 não é possível clonar ou efetuar a recuperação de desastres num dispositivo com o software de pré-atualização 1. |Terá de atualizar o dispositivo de destino a atualizar 1 para permitir estas operações |Sim |Sim |
| 14 |Migração |Cópia de segurança de configuração para a migração poderá falhar num dispositivo séries 5000 7000 quando existem grupos de volume com nenhuma volumes associados. |Elimine todos os grupos de volume vazio com nenhuma volumes associados e, em seguida, repita a cópia de segurança de configuração. |Sim |Não |

## <a name="physical-device-updates-in-update-12"></a>Atualizações ao dispositivo físico na atualização 1.2
Se atualizar o patch 1.2 é aplicado a um dispositivo físico (com versões anteriores ao Update 1), a versão do software será alterado para 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Controlador e atualizações de firmware na atualização 1.2
Esta versão de atualizações de controladores e firmware do disco no seu dispositivo.

* Para obter mais informações sobre a atualização do controlador SAS, consulte [Update 1 para controladores de LSI SAS na aplicação do Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005). 
* Para obter mais informações sobre a atualização de firmware do disco, consulte [firmware de disco Update 1 para a aplicação do Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Atualizações ao dispositivo virtual na atualização 1.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais terão de ser criado. 

## <a name="next-steps"></a>Passos seguintes
* [Instalar a atualização 1.2 no seu dispositivo](storsimple-install-update-1.md).

