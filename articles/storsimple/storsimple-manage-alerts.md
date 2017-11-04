---
title: Ver e gerir alertas do StorSimple | Microsoft Docs
description: "Descreve as condições de alerta do StorSimple e gravidade, como configurar notificações de alertas e como utilizar o serviço StorSimple Manager para gerir alertas."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: bee49253-9ac7-4131-95f6-6bf0e72b8438
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/08/2017
ms.author: anbacker
ms.openlocfilehash: 5772334aade138101730fa7345f4515664d6d22f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-alerts"></a>Utilizar o serviço StorSimple Manager para ver e gerir alertas do StorSimple
## <a name="overview"></a>Descrição geral
O **alertas** separador no serviço StorSimple Manager fornece uma forma de rever e limpar alertas relacionados com o dispositivo do StorSimple numa base em tempo real. A partir deste separador, pode monitorizar centralmente os problemas de estado de funcionamento dos seus dispositivos StorSimple e a solução global de Microsoft Azure StorSimple.

Este tutorial descreve alerta condições comuns, níveis de gravidade do alerta e como configurar notificações de alerta. Além disso, inclui as tabelas de alerta de referência rápida, que permitem-lhe localizar um alerta específico e adequadamente responder rapidamente.

![Página de alertas](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## <a name="common-alert-conditions"></a>Condições de alerta comuns
O dispositivo StorSimple gera alertas em resposta a uma variedade de condições. Seguem-se os tipos de condições alertas mais comuns:

* **Problemas de hardware** – estes alertas dizer sobre o estado de funcionamento do seu hardware. Permitem-lhe saber se forem necessárias atualizações de firmware, se uma interface de rede tem problemas, ou se houver um problema com uma das suas unidades de dados.
* **Problemas de conectividade** – estes alertas ocorrem quando não existe qualquer dificuldade na transferência de dados. Podem ocorrer problemas de comunicação durante a transferência de dados para e da conta do storage do Azure ou devido a falta de conectividade entre os dispositivos e o serviço StorSimple Manager. Problemas de comunicação são algumas das hardest corrigir porque existem muitos pontos de falha. Deve sempre primeiro certifique-se de que o acesso à Internet e conectividade de rede estão disponíveis antes de continuar para a resolução de problemas mais avançadas. Para obter ajuda na resolução de problemas, aceda a [resolver problemas com o cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemas de desempenho** – estes alertas são causados quando o sistema não se encontra satisfatória, tal como quando está sob uma carga elevada.

Além disso, poderá ver alertas relacionados com segurança, as atualizações ou falhas de tarefas.

## <a name="alert-severity-levels"></a>Níveis de gravidade do alerta
Os alertas têm níveis de gravidade de diferente, consoante o impacto que terá da situação alerta e a necessidade de uma resposta para o alerta. Os níveis de gravidade são:

* **Crítico** – este alerta está em resposta a uma condição que está a afetar o desempenho do sistema efetuada com êxito. Ação é necessária para se certificar de que o StorSimple serviço não é interrompido.
* **Aviso** – esta condição pode ficar crítica, se não resolvido. Deve investigar a situação e efetuar qualquer ação necessária para limpar o problema.
* **Informações** – este alerta contém informações que podem ser úteis para controlar e gerir o sistema.

## <a name="configure-alert-settings"></a>Configurar definições de alerta
Pode escolher se pretende ser notificado por correio eletrónico de condições de alerta para cada um dos seus dispositivos StorSimple. Além disso, pode identificar outros destinatários de notificação de alertas, introduzindo os respetivos endereços de e-mail no **outros destinatários de e-mail** caixa, separada por ponto e vírgula.

> [!NOTE]
> Pode introduzir um máximo de 20 endereços de e-mail por dispositivo.
> 
> 

Depois de ativar a notificação por correio eletrónico para um dispositivo, os membros da lista de notificação irão receber uma mensagem de e-mail, ocorre um alerta crítico de cada vez. As mensagens serão enviadas  *storsimple-alerts-noreply@mail.windowsazure.com*  e descreve a condição do alerta. Podem clicar destinatários **Unsubscribe** para remover a próprios a lista de notificação de correio eletrónico.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para ativar o e-mail de notificação de alertas para um dispositivo
1. Aceda a **dispositivos** > **configurar** para o dispositivo.
2. Em **definições de alerta**, defina o seguinte:
   
   1. No **Enviar notificação de correio eletrónico** campo, selecione **Sim**.
   2. No **administradores de serviço de E-Mail** campo, selecione **Sim** se pretender ter o administrador de serviço e todos os coadministradores recebem as notificações de alerta.
   3. No **outros destinatários de e-mail** campo, introduza os endereços de e-mail de todos os destinatários que devem receber notificações de alerta. Introduza os nomes no formato  *someone@somewhere.com* . Utilize ponto e vírgula para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo. 
      
       ![Configuração de notificação de alertas](./media/storsimple-manage-alerts/AlertNotify.png)
3. Para enviar uma notificação de correio eletrónico de teste, clique no ícone de seta junto a **enviar e-mail de teste**. O serviço StorSimple Manager irá apresentar mensagens de estado, que reencaminha a notificação de teste. 
4. Quando aparecer a mensagem seguinte, clique em **OK**. 
   
    ![Alertas de e-mail de notificação enviado de teste](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)
   
   > [!NOTE]
   > Se não é possível enviar a mensagem de notificação de teste, o serviço StorSimple Manager apresentará uma mensagem adequada. Clique em **OK**, aguarde alguns minutos e, em seguida, tente enviar a mensagem de notificação de teste novamente. 
   > 
   > 

## <a name="view-and-track-alerts"></a>Ver e monitorizar alertas
O dashboard do serviço StorSimple Manager fornece uma leitura rápida no número de alertas nos seus dispositivos, dispostas por nível de gravidade.

![Dashboard de alertas](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Clicar o nível de gravidade abre o **alertas** separador. Os resultados incluem apenas os alertas que correspondem a esse nível de gravidade.

![Relatório de alertas no âmbito do tipo de alerta](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Ao clicar num alerta na lista fornece-lhe detalhes adicionais para o alerta, incluindo a última vez que o alerta foi comunicado, o número de ocorrências do alerta no dispositivo e as ações recomendadas para resolver o alerta. Se se tratar de um alerta de hardware, também identifiquem o componente de hardware.

![Exemplo de alerta de hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Pode copiar os detalhes do alerta para um ficheiro de texto, se tiver de enviar as informações ao Microsoft Support. Depois de ter seguido de recomendação e resolver a condição de alerta no local, deve limpar o alerta do dispositivo ao selecionar o alerta no **alertas** separador e clicando em **limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna, exceto o **alerta** coluna e, em seguida, clique em **limpar** depois de selecionar todos os alertas para ser limpa. Tenha em atenção que alguns alertas são automaticamente eliminadas quando o problema está resolvido ou quando o sistema atualiza o alerta com novas informações.

Ao clicar em **limpar**, terá a oportunidade para fornecer comentários sobre o alerta e os passos que demorou para resolver o problema. Alguns eventos serão limpo pelo sistema se outro evento é acionado com novas informações. Nesse caso, verá a mensagem seguinte.

![Mensagem de alerta limpar](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>A ordenação e rever alertas
Pode encontrar mais eficiente para executar relatórios de alertas para que pode rever e limpar estes grupos. Além disso, o **alertas** separador pode apresentar até 250 alertas. Se ter excedido esse número de alertas, nem todos os alertas serão apresentados na vista de predefinição. Pode combinar os campos seguintes para personalizar os alertas são apresentados:

* **Estado** – pode apresentar um **Active Directory** ou **Cleared** alertas. Alertas ativos ainda estão a ser acionados no seu sistema, enquanto a alertas desmarcadas foram ou eliminadas manualmente por um administrador ou de forma programática porque o sistema atualizado a condição de alerta com novas informações.
* **Gravidade** – pode apresentar os alertas de todos os níveis de gravidade (crítico, aviso, informações) ou apenas uma determinada gravidade, tais como alertas críticos apenas.
* **Origem** – pode apresentar os alertas de todas as origens ou limitar os alertas que são provenientes de serviço ou um ou todos os dispositivos.
* **Intervalo de tempo** – especificando o **de** e **para** as datas e carimbos de data / hora, pode ver alertas durante o período de tempo que está interessado em.

## <a name="alerts-quick-reference"></a>Referência rápida de alertas
As tabelas seguintes listam alguns dos alertas do Microsoft Azure StorSimple que poderá encontrar, bem como informações adicionais e as recomendações, quando disponíveis. Alertas de dispositivo do StorSimple enquadram-se uma das seguintes categorias:

* [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)
* [Alertas de cluster](#cluster-alerts)
* [Alertas de recuperação de desastre](#disaster-recovery-alerts)
* [Alertas de hardware](#hardware-alerts)
* [Alertas de falha da tarefa](#job-failure-alerts)
* [Alertas de volume afixado localmente](#locally-pinned-volume-alerts)
* [Alertas de redes](#networking-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)
* [Alertas de pacote de suporte](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de nuvem
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Conectividade <*nome da credencial de nuvem*> não pode ser estabelecida. |Não é possível ligar à conta de armazenamento. |Parece que é possível que exista um problema de conectividade com o seu dispositivo. Execute o `Test-HcsmConnection` cmdlet a partir da Interface do Windows PowerShell para StorSimple no seu dispositivo para identificar e corrigir o problema. Se as definições estão corretas, o problema poderá ser com as credenciais da conta do storage para o qual o alerta foi gerado. Neste caso, utilize o `Test-HcsStorageAccountCredential` cmdlet para determinar se existem problemas que possa resolver.<ul><li>Verifique as definições de rede.</li><li>Verifique as credenciais da conta de armazenamento.</li></ul> |
| Não recebemos um heartbeat do seu dispositivo durante a última <*número*> minutos. |Não é possível ligar ao dispositivo. |Parece que há um problema de conectividade com o seu dispositivo. Utilize o `Test-HcsmConnection` cmdlet a partir da Interface do Windows PowerShell para StorSimple no seu dispositivo para identificar e corrigir o problema ou contacte o administrador de rede. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamento do StorSimple quando ocorre uma falha de conectividade de nuvem
O que acontece se falhar de conectividade de nuvem para o meu dispositivo StorSimple em execução na produção?

Se a conectividade de nuvem falhar no dispositivo StorSimple produção, em seguida, consoante o estado do seu dispositivo, o seguinte pode ocorrer: 

* **Para os dados locais no seu dispositivo**: há algum tempo, existirá sem interrupção e leituras irão continuar a ser servido. No entanto, como o número de IOs pendentes aumenta e excede um limite, as operações de leitura foi começam a falhar. 
  
    Dependendo da quantidade de dados no seu dispositivo, as escritas também irão continuar a ocorrer pelas primeiro algumas horas após a interrupção na conectividade de nuvem. As escritas serão, em seguida, mais lentos e, eventualmente, começam a falhar se a conectividade de nuvem é interrompida durante várias horas. (Existe é armazenamento temporário no dispositivo para dados que está a ser enviado para a nuvem. Esta área é descarregada quando os dados são enviados. Se falhar a conectividade, dados nesta área de armazenamento não serão enviados para a nuvem, e/s falhará.)   
* **Para os dados na nuvem**: para a maioria dos erros de conectividade de nuvem, é devolvido um erro. Depois da conectividade ser restaurada, os IOs são retomados sem o utilizador ter de colocar o volume online. Em situações raras, intervenção do utilizador poderá ser necessária para recuperar o volume online do portal clássico do Azure. 
* **Instantâneos de nuvem em curso**: A operação é repetida algumas vezes no prazo de 4 a 5 horas e se a conectividade não for restaurada, os instantâneos de nuvem irão falhar.

### <a name="cluster-alerts"></a>Alertas de cluster
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Dispositivos a ativação pós-falha para <*nome de dispositivo*>. |Dispositivo estiver no modo de manutenção. |Dispositivos a ativação pós-falha devido a introduzir ou sair do modo de manutenção. Isto é normal e é necessária nenhuma ação. Depois de terem confirmados este alerta, desmarcá-la a partir da página de alertas. |
| Dispositivos a ativação pós-falha para <*nome de dispositivo*>. |Foi atualizou o software ou firmware do dispositivo. |Ocorreu uma ativação pós-falha do cluster devido a uma atualização. Isto é normal e é necessária nenhuma ação. Depois de terem confirmados este alerta, desmarcá-la a partir da página de alertas. |
| Dispositivos a ativação pós-falha para <*nome de dispositivo*>. |Controlador foi encerrado ou reiniciado. |Dispositivos a ativação pós-falha porque o controlador de Active Directory foi encerrado ou reiniciado por um administrador. É necessária nenhuma ação. Depois de terem confirmados este alerta, desmarcá-la a partir da página de alertas. |
| Dispositivos a ativação pós-falha para <*nome de dispositivo*>. |Ativação pós-falha planeada. |Certifique-se de que este foi uma ativação pós-falha planeada. Depois de tomar as medidas adequadas, desmarque este alerta a partir da página de alertas. |
| Dispositivos a ativação pós-falha para <*nome de dispositivo*>. |Ativação pós-falha não planeada. |StorSimple foi concebido para recuperar automaticamente as ativações pós-falha não planeadas. Se vir um grande número destes alertas, contacte Support da Microsoft. |
| Dispositivos a ativação pós-falha para <*nome de dispositivo*>. |Causa de outra/desconhecida. |Se vir um grande número destes alertas, contacte Support da Microsoft. Depois de resolvido o problema, limpe este alerta a partir da página de alertas. |
| Um serviço de dispositivo crítico relatórios de estado como falhado. |Falha do serviço de DataPath. |Contacte Support da Microsoft para obter assistência. |
| Endereço IP virtual para a interface de rede <*dados #*> relatórios de estado como falhado. |Causa de outra/desconhecida. |Condições temporárias, por vezes, podem fazer com que estes alertas. Se for este o caso, em seguida, caso este alerta será automaticamente eliminado após algum tempo. Se o problema persistir, contacte o Suporte da Microsoft. |
| Endereço IP virtual para a interface de rede <*dados #*> relatórios de estado como falhado. |Nome da interface: <*dados #*> endereço IP <IP address> não pode ser colocada online porque foi detetado um endereço IP duplicado na rede. |Certifique-se de que o endereço IP duplicado é removido da rede ou reconfigurar a interface com um endereço IP diferente. |

### <a name="disaster-recovery-alerts"></a>Alertas de recuperação de desastre
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Operações de recuperação não conseguiu restaurar todas as definições para este serviço. Dados de configuração de dispositivo estão num estado inconsistente para alguns dispositivos. |Dados detetada uma inconsistência após a recuperação de desastre. |Os dados encriptados no serviço não estão sincronizados com que o dispositivo. Autorizar o dispositivo <*nome de dispositivo*> do StorSimple Manager para iniciar o processo de sincronização. Utilize a Interface do Windows PowerShell para StorSimple para executar o `Restore-HcsmEncryptedServiceData` no dispositivo <*nome de dispositivo*> cmdlet, fornecer a palavra-passe antiga como uma entrada para este cmdlet para restaurar o perfil de segurança. Em seguida, execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet para atualizar a chave de encriptação de dados do serviço. Depois de tomar as medidas adequadas, desmarque este alerta a partir da página de alertas. |

### <a name="hardware-alerts"></a>Alertas de hardware
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Componente de hardware <*ID de componente*> relatórios de estado como <*estado*>. | |Condições temporárias, por vezes, podem fazer com que estes alertas. Se assim for, este alerta será automaticamente limpa após algum tempo. Se o problema persistir, contacte o Suporte da Microsoft. |
| Controlador passivo funcionar incorretamente ocorrer sistematicamente. |O controlador passivo (secundário) não está a funcionar. |O dispositivo está operacional, mas é funcionar um dos seus controladores incorretamente ocorrer sistematicamente. Tente reiniciar desse controlador. Se não for resolvido o problema, contacte Support da Microsoft. |
| Falha de unidade iminente detetada. | Falha de unidade iminente detetada. |Detetámos uma falha de unidade iminente para o componente de hardware ' unidade na ranhura <*espaço ID*>, bastidor <*bastidor ID*>'. Considere substituir a unidade. <br> Antes de começar a substituição de disco, reveja as informações seguintes.<br><br>Se o dispositivo tiver mais do que um disco com falhas, não remova mais do que um SSD ou HDD em qualquer altura. Se o fizer, pode resultar na perda de dados.<br><br>Certifique-se de que coloque um substituto SSD numa ranhura que continha anteriormente um SSD. O mesmo se aplica-se um HDD.<br><br>As ranhuras são numeradas da 0 para 11. Um disco com falhas na ranhura 2 mapeia para um disco com falhas na ranhura 3 do dispositivo (a partir da parte superior esquerda).<br><br>Para obter mais informações sobre a substituição do disco, aceda a https://go.microsoft.com/fwlink/?linkid=838653. Se o problema persistir, contacte o suporte da Microsoft através de https://go.microsoft.com/fwlink/?linkid=838654. |

### <a name="job-failure-alerts"></a>Alertas de falha da tarefa
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Cópia de segurança de <*ID de grupo do volume de origem*> falha. |Falha na tarefa de cópia de segurança. |Problemas de conectividade podem estar a impedir a operação de cópia de segurança a conclusão com êxito. Se não existem não existem problemas de conectividade, poderá ter atingido o número máximo de cópias de segurança. Elimine quaisquer cópias de segurança que já não são necessárias e repita a operação. Depois de tomar as medidas adequadas, desmarque este alerta a partir da página de alertas. |
| Clonar de <*IDs de elemento de cópia de segurança de origem*> a <*números de série do volume de destino*> falha. |Falha na tarefa de clone. |Atualize a lista de cópia de segurança para verificar que a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que os problemas de conectividade de nuvem estão a impedir a conclusão com êxito da operação clone. Se não existem não existem problemas de conectividade, poderá ter atingido o limite de armazenamento. Elimine quaisquer cópias de segurança que já não são necessárias e repita a operação. Depois de tomar as medidas adequadas para resolver o problema, limpe este alerta a partir da página de alertas. |
| Restaurar de <*IDs de elemento de cópia de segurança de origem*> falha. |Falha na tarefa de restauro. |Atualize a lista de cópia de segurança para verificar que a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que os problemas de conectividade de nuvem estão a impedir a conclusão com êxito a operação de restauro. Se não existem não existem problemas de conectividade, poderá ter atingido o limite de armazenamento. Elimine quaisquer cópias de segurança que já não são necessárias e repita a operação. Depois de tomar as medidas adequadas para resolver o problema, limpe este alerta a partir da página de alertas. |

### <a name="locally-pinned-volume-alerts"></a>Alertas de volume afixado localmente
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| A criação do local volume <*nome do volume*> falha. |A tarefa de criação do volume falhou. <*Mensagem de erro correspondente para o código de erro falhou*>. |Problemas de conectividade podem estar a impedir a operação de criação de espaço a conclusão com êxito. Volumes localmente afixados são fortemente aprovisionados e o processo de criação de espaço envolve transbordar volumes em camadas para a nuvem. Se não existem não existem problemas de conectividade, poderá ter esgotado o espaço local no dispositivo. Determine se o espaço existe no dispositivo antes de repetir esta operação. |
| Expansão de local volume <*nome do volume*> falha. |A tarefa de modificação de volume falhou devido a <*mensagem de erro correspondente para o código de erro falhou*>. |Problemas de conectividade podem estar a impedir a conclusão com êxito da operação volume expansão. Volumes localmente afixados são fortemente aprovisionados e o processo de alargar o espaço existente envolve transbordar volumes em camadas para a nuvem. Se não existem não existem problemas de conectividade, poderá ter esgotado o espaço local no dispositivo. Determine se o espaço existe no dispositivo antes de repetir esta operação. |
| A conversão do volume <*nome do volume*> falha. |A tarefa de conversão de volume para converter o tipo de volume localmente afixado em camadas com falhas. |Conversão do volume do tipo afixado localmente em camadas não foi possível concluir. Certifique-se de que existem não existem problemas de conectividade a impedir a conclusão com êxito da operação. Para resolução de problemas de conectividade problemas, aceda a [resolver problemas com o cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume localmente afixado original agora tiver sido marcado como um volume em camadas, uma vez que alguns dos dados do volume localmente afixado tem spilled para a nuvem durante a conversão. O volume em camadas resultante é ainda occupying espaço local no dispositivo que não pode ser recuperado para volumes locais futuras.<br>Resolva os eventuais problemas de conectividade, limpe o alerta e converter este volume para o tipo de volume localmente afixado original para garantir que todos os dados ficam disponíveis localmente novamente. |
| A conversão do volume <*nome do volume*> falha. |A tarefa de conversão de volume para converter o tipo de volume em camadas para afixado localmente falhou. |Conversão do volume do tipo em camadas para afixado localmente não foi possível concluir. Certifique-se de que existem não existem problemas de conectividade a impedir a conclusão com êxito da operação. Para resolução de problemas de conectividade problemas, aceda a [resolver problemas com o cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume em camadas original agora marcado como um volume localmente afixado, como parte do processo de conversão continua a ter dados que residem na nuvem, enquanto o espaço fortemente aprovisionado no dispositivo para este volume já não pode reclamada para volumes locais futuras.<br>Resolva os eventuais problemas de conectividade, limpe o alerta e converter este volume para o tipo de volume em camadas original para garantir espaço local fortemente aprovisionado no dispositivo pode ser recuperado. |
| Prestes a consumo de espaço local para instantâneos locais dos <*nome do grupo de volume*> |Instantâneos locais para a política de cópia de segurança em breve poderão ficar sem espaço e invalidados para evitar falhas de escrita do anfitrião. |Instantâneos locais frequentes juntamente com um fluxo de dados elevada em volumes associados a este grupo de política de cópia de segurança estão a causar espaço local no dispositivo a ser consumidos rapidamente. Elimine quaisquer instantâneos locais que já não são necessárias. Além disso, as agendas de instantâneo local para esta política de cópia de segurança criar instantâneos locais menos frequentes e certifique-se de que os instantâneos de nuvem são efetuados regularmente de atualização. Se não são tidas nestas ações, em breve poderá ser esgotado espaço local para estes instantâneos e o sistema será automaticamente eliminá-los para se certificar de que o anfitrião escritas continuam a ser processado com êxito. |
| Instantâneos locais para <*nome do grupo de volume*> foram invalidados. |Os instantâneos locais do <*nome do grupo de volume*> foram invalidados e, em seguida, eliminar porque foram a exceder o espaço local no dispositivo. |Para garantir que este não repetir no futuro, reveja as agendas de instantâneo local para esta política de cópia de segurança e elimine quaisquer instantâneos locais que já não são necessárias. Instantâneos locais frequentes juntamente com um fluxo de dados elevada em volumes associados a este grupo de política de cópia de segurança poderão causar espaço local no dispositivo a ser consumidos rapidamente. |
| Restaurar de <*IDs de elemento de cópia de segurança de origem*> falha. |A tarefa de restauro falhou. |Se tiver de ser afixado localmente ou uma combinação de volumes localmente afixados e em camadas nesta política de cópia de segurança, atualize a lista de cópia de segurança para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que os problemas de conectividade de nuvem estão a impedir a conclusão com êxito a operação de restauro. Os volumes localmente afixados restaurados como parte deste grupo de instantâneo não dispõe de todos os dados transferidos para o dispositivo e, se tiver uma mistura de volumes em camadas e localmente afixados neste grupo de instantâneo, eles não serão sincronizados entre si. Para concluir com êxito a operação de restauro, colocar os volumes neste grupo offline no anfitrião e repita a operação de restauro. Tenha em atenção que quaisquer alterações aos dados do volume que foram executadas durante o processo de restauro serão perdidas. |

### <a name="networking-alerts"></a>Alertas de redes
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Não foi possível iniciar o(s) serviço(s) do StorSimple. |Erro de DataPath |Se o problema persistir, contacte o Suporte da Microsoft. |
| Endereço IP duplicado detetado para 'Data0'. | |O sistema detetou um conflito de endereço IP '10.0.0.1'. O recurso de rede 'Data0' no dispositivo  *<device1>*  está offline. Certifique-se de que este endereço IP não está a ser utilizado por quaisquer outras entidades na rede. Para resolver problemas de rede, aceda a [resolver problemas com o cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Contacte o administrador de rede para ajudar a resolver este problema. Se o problema persistir, contacte o Suporte da Microsoft. |
| Endereço IPv4 (ou IPv6) para 'Data0' está offline. | |O recurso de rede 'Data0' com o endereço IP '10.0.0.1'. e comprimento '22' no dispositivo do prefixo  *<device1>*  está offline. Certifique-se de que as portas de comutador à qual está ligada esta interface estão operacionais. Para resolver problemas de rede, aceda a [resolver problemas com o cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |

### <a name="performance-alerts"></a>Alertas de desempenho
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| A carga de dispositivo excedeu <*limiar*>. |Mais lenta do que os tempos de resposta esperado. |A utilização de relatórios de dispositivo numa sobrecarga de entrada/saída. Isto pode fazer com que o dispositivo não está a funcionar, bem como devia. Reveja as cargas de trabalho que tenha ligado ao dispositivo e determinar se existe alguma que foi movida para outro dispositivo ou que já não são necessários.<br>Para compreender o estado atual, aceda a [utilizar o serviço StorSimple Manager para monitorizar o seu dispositivo](storsimple-monitor-device.md) |

### <a name="security-alerts"></a>Alertas de segurança
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Foi iniciada sessão Support da Microsoft. |Sessão de suporte de acedidos de terceiros. |Verifique se que está autorizado este acesso. Depois de tomar as medidas adequadas, desmarque este alerta a partir da página de alertas. |
| Palavra-passe para <*elemento*> irá expirar dentro de <*período de tempo*>. |Expiração da palavra-passe está a aproximar-se. |Altere a palavra-passe antes de expirar. |
| As informações de configuração de segurança em falta para <*ID de elemento*>. | |Os volumes associados a este contentor de volume não podem ser utilizados para replicar a configuração do StorSimple. Para garantir que os seus dados são armazenados em segurança, recomendamos que elimina o contentor de volume e todos os volumes associados com o contentor de volume. Depois de tomar as medidas adequadas, desmarque este alerta a partir da página de alertas. |
| <*número*> tentativas de início de sessão falhou para <*ID de elemento*>. |Tentativas de início de sessão falhado vários. |O dispositivo pode estar a sofrer um ataque ou um utilizador autorizado está a tentar estabelecer ligação com uma palavra-passe incorreta.<ul><li>Contacte os utilizadores autorizados e certifique-se de que estes tentativas foram de uma origem legítima. Se continuar a ver grande número de tentativas de início de sessão, considere desativar a gestão remota e contactar o administrador da rede. Depois de tomar as medidas adequadas, desmarque este alerta a partir da página de alertas.</li><li>Certifique-se de que as instâncias do Snapshot Manager estão configuradas com a palavra-passe correta. Depois de tomar as medidas adequadas, desmarque este alerta a partir da página de alertas.</li></ul>Para obter mais informações, aceda a [alterar uma palavra-passe do dispositivo expirada](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Uma ou mais falhas ocorreram ao alterar a chave de encriptação de dados do serviço. | |Ocorreram erros encontrados ao alterar a chave de encriptação de dados do serviço. Após ter resolvido as condições de erro, execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet a partir da Interface do Windows PowerShell para StorSimple no seu dispositivo para atualizar o serviço. Se este problema persistir, contacte o suporte da Microsoft. Depois de resolver o problema, limpe este alerta a partir da página de alertas. |

### <a name="support-package-alerts"></a>Alertas de pacote de suporte
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Falha na criação do pacote de suporte. |StorSimple não foi possível gerar o pacote. |Repita esta operação. Se o problema persistir, contacte o Suporte da Microsoft. Depois de resolver o problema, limpe este alerta a partir da página de alertas. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [StorSimple erros e resolução de problemas de um dispositivo operacional](storsimple-troubleshoot-operational-device.md).

