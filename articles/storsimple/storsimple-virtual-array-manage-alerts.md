---
title: Ver e gerir alertas do Microsoft Azure StorSimple Virtual matriz | Microsoft Docs
description: "Descreve as condições de alerta de matriz Virtual StorSimple e gravidade e como utilizar o serviço StorSimple Manager para gerir alertas."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a96e52bad725acb70a429c8eaafd6942b13d9b8
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Utilize o Gestor de dispositivo StorSimple para gerir alertas para a matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral

A funcionalidade de alertas no serviço do Gestor de dispositivos do StorSimple fornece uma forma de rever e limpar alertas relacionados com a matrizes Virtual StorSimple numa base em tempo real. Pode utilizar os alertas no **resumo serviço** painel para monitorizar centralmente os problemas de estado de funcionamento das suas matrizes de Virtual StorSimple e da solução global de Microsoft Azure StorSimple.

Este tutorial descreve como configurar notificações de alerta, as condições de alerta comuns, níveis de gravidade do alerta e como visualizar e controlar os alertas. Além disso, inclui as tabelas de alerta de referência rápida, que permitem-lhe localizar um alerta específico e adequadamente responder rapidamente.

![Página de alertas](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurar definições de alerta

Pode escolher se pretende ser notificado por e-mail as condições de alerta para cada uma das suas matrizes de Virtual StorSimple. Além disso, pode identificar outros destinatários de notificação de alertas, introduzindo os respetivos endereços de e-mail no **destinatários de e-mail adicionais** caixa, separada por ponto e vírgula.

> [!NOTE]
> Pode introduzir um máximo de 20 endereços de e-mail por matriz virtual.


Depois de ativar a notificação por e-mail para uma matriz de virtual, os membros da lista de notificação irão receber uma mensagem de e-mail, ocorre um alerta crítico de cada vez. As mensagens serão enviadas  *storsimple-alerts-noreply@mail.windowsazure.com*  e descreve a condição do alerta. Podem clicar destinatários **Unsubscribe** para remover a próprios a lista de notificação de correio eletrónico.

#### <a name="to-enable-email-notification-for-alerts"></a>Para ativar notificações por e-mail para alertas

1. Aceda ao seu serviço do Gestor de dispositivos do StorSimple e no **gestão** secção, selecione e clique em **dispositivos**. Na lista de dispositivos apresentada, selecione e clique em seu dispositivo.
   
    ![definições de alerta](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Isto abre-se a **definições** painel. No **definições do dispositivo** secção, selecione **geral**. Isto abre-se a **definições gerais** painel.
   
    ![Configuração de notificação de alertas](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. No **definições gerais** painel, aceda a **as definições de alertas** secção e defina o seguinte:
   
   1. No **ativar notificação de correio eletrónico** campo, selecione **Sim**.
   2. No **administradores de serviço de E-Mail** campo, selecione **Sim** se pretender ter o administrador de serviço e todos os coadministradores recebem as notificações de alerta.
   3. No **destinatários de e-mail adicionais** campo, introduza os endereços de e-mail de todos os destinatários que devem receber notificações de alerta. Introduza os nomes no formato  *someone@somewhere.com* . Utilize ponto e vírgula para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo virtual.
      
       ![Configuração de notificação de alertas](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Para enviar uma notificação de correio eletrónico de teste, clique em **enviar e-mail de teste**. O serviço StorSimple Manager de dispositivos irá apresentar mensagens de estado, que reencaminha a notificação de teste.
      
       ![Alertas de e-mail de notificação enviado de teste](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Se não é possível enviar a mensagem de notificação de teste, o serviço do Gestor de dispositivos do StorSimple apresentará uma mensagem adequada. Clique em **OK**, aguarde alguns minutos e, em seguida, tente enviar a mensagem de notificação de teste novamente.
      > 
      > 
   5. Na parte inferior da página, clique em **guardar** para guardar a configuração. Quando lhe for pedida a confirmação, clique em **Sim**.
      
      ![Alertas de e-mail de notificação enviado de teste](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Condições de alerta comuns

A matriz de Virtual StorSimple gera alertas em resposta a uma variedade de condições. Seguem-se os tipos de condições alertas mais comuns:

* **Problemas de conectividade** – estes alertas ocorrem quando não existe qualquer dificuldade na transferência de dados. Podem ocorrer problemas de comunicação durante a transferência de dados para e da conta do storage do Azure ou devido a falta de conectividade entre os dispositivos virtuais e o serviço do Gestor de dispositivos do StorSimple. Problemas de comunicação são algumas das hardest corrigir porque existem muitos pontos de falha. Deve sempre primeiro certifique-se de que o acesso à Internet e conectividade de rede estão disponíveis antes de continuar para a resolução de problemas mais avançadas. Para informações sobre as portas e as definições da firewall, aceda a [requisitos de sistema de matriz Virtual StorSimple](storsimple-ova-system-requirements.md). Para obter ajuda na resolução de problemas, aceda a [resolver problemas com o cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemas de desempenho** – estes alertas são causados quando o sistema não se encontra satisfatória, tal como quando está sob uma carga elevada.

Além disso, poderá ver alertas relacionados com segurança, as atualizações ou falhas de tarefas.

## <a name="alert-severity-levels"></a>Níveis de gravidade do alerta

Os alertas têm níveis de gravidade de diferente, consoante o impacto que terá da situação alerta e a necessidade de uma resposta para o alerta. Os níveis de gravidade são:

* **Crítico** – este alerta está em resposta a uma condição que está a afetar o desempenho do sistema efetuada com êxito. Ação é necessária para se certificar de que o StorSimple serviço não é interrompido.
* **Aviso** – esta condição pode ficar crítica, se não resolvido. Deve investigar a situação e efetuar qualquer ação necessária para limpar o problema.
* **Informações** – este alerta contém informações que podem ser úteis para controlar e gerir o sistema.

## <a name="view-and-track-alerts"></a>Ver e monitorizar alertas

O painel de resumo do Gestor de dispositivos do StorSimple serviço fornece-lhe uma leitura rápida no número de alertas nos seus dispositivos virtuais, dispostas por nível de gravidade.

![Dashboard de alertas](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Clique no nível de gravidade para abrir o **alertas** painel. Os resultados incluem apenas os alertas que correspondem a esse nível de gravidade.

![Relatório de alertas no âmbito do tipo de alerta](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Clique num alerta na lista para obter detalhes adicionais para o alerta, incluindo a última vez que o alerta foi comunicado, o número de ocorrências do alerta no dispositivo e as ações recomendadas para resolver o alerta.

![Lista de alertas e os detalhes](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Pode copiar os detalhes do alerta para um ficheiro de texto, se tiver de enviar as informações ao Microsoft Support. Depois de ter seguido de recomendação e resolver a condição de alerta no local, deve limpar o alerta da lista. Selecione o alerta na lista e, em seguida, clique em **limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna, exceto o **alerta** coluna e, em seguida, clique em **limpar** depois de selecionar todos os alertas para ser limpa.

Ao clicar em **limpar**, terá a oportunidade para fornecer comentários sobre o alerta e os passos que demorou para resolver o problema. 

![comentários do alerta](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Alguns eventos serão limpo pelo sistema se outro evento é acionado com novas informações. 

## <a name="sort-and-review-alerts"></a>A ordenação e rever alertas

O **alertas** painel pode apresentar até 250 alertas. Se ter excedido esse número de alertas, nem todos os alertas serão apresentados na vista de predefinição. Pode combinar os campos seguintes para personalizar os alertas são apresentados:

* **Estado** – pode apresentar um **Active Directory** ou **Cleared** alertas. Alertas ativos ainda estão a ser acionados no seu sistema, enquanto a alertas desmarcadas foram ou eliminadas manualmente por um administrador ou de forma programática porque o sistema atualizado a condição de alerta com novas informações.
* **Gravidade** – pode apresentar os alertas de todos os níveis de gravidade (crítico, aviso, informações) ou apenas uma determinada gravidade, tais como alertas críticos apenas.
* **Origem** – pode apresentar os alertas de todas as origens ou limitar os alertas que são provenientes de serviço ou um ou todos os dispositivos virtuais.
* **Intervalo de tempo** – especificando o **de** e **para** as datas e carimbos de data / hora, pode ver alertas durante o período de tempo que está interessado em.

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas seguintes listam alguns dos alertas StorSimple que poderá encontrar, bem como informações adicionais e as recomendações, quando disponíveis. Alertas de matriz Virtual StorSimple enquadram-se uma das seguintes categorias:

* [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)
* [Alertas de configuração](#configuration-alerts)
* [Alertas de falha da tarefa](#job-failure-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)
* [Atualizar alertas](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de nuvem

| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Dispositivo  *<device name>*  não está ligado à nuvem. |Não é possível ligar o dispositivo com o nome para a nuvem. |Não foi possível ligar à nuvem. Isto pode dever-se um dos seguintes:<ul><li>Pode existir um problema com as definições de rede no seu dispositivo.</li><li>Pode existir um problema com as credenciais da conta de armazenamento.</li></ul>Para obter mais informações sobre como resolver problemas de conectividade, vá para o [IU da web do local](storsimple-ova-web-ui-admin.md) do dispositivo. |

### <a name="configuration-alerts"></a>Alertas de configuração

| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Configuração do dispositivo virtual no local não suportada. |Desempenho lento. |A configuração atual poderão resultar numa degradação do desempenho. Certifique-se de que o servidor cumpre os requisitos mínimos de configuração. Para obter mais informações, aceda a [StorSimple Virtual matriz requisitos](storsimple-ova-system-requirements.md). |
| Estiver a ficar sem espaço em disco aprovisionado <*nome de dispositivo*>. |Aviso de espaço em disco. |Tiver pouco espaço em disco aprovisionado. Para libertar espaço, considere mover as cargas de trabalho para outro volume ou partilha ou eliminação de dados. |

### <a name="job-failure-alerts"></a>Alertas de falha da tarefa

| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Cópia de segurança de <*nome de dispositivo*> não foi possível concluir. |Falha da tarefa de cópia de segurança. |Não foi possível criar uma cópia de segurança. Considere um dos seguintes:<ul><li>Problemas de conectividade podem estar a impedir a operação de cópia de segurança a conclusão com êxito. Certifique-se de que existem não existem problemas de conectividade. Para obter mais informações sobre como resolver problemas de conectividade, vá para o [IU da web do local](storsimple-ova-web-ui-admin.md) para o dispositivo virtual.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere eliminar quaisquer cópias de segurança que já não são necessárias.</li></ul> Resolva os problemas, limpe o alerta e repita a operação. |
| Clonar de <*nome de dispositivo*> não foi possível concluir. |Falha da tarefa de clone. |Não foi possível criar um clone. Considere um dos seguintes:<ul><li>A lista de cópia de segurança pode não ser válida. Atualize a lista para verificar ainda é válida.</li><li>Problemas de conectividade podem estar a impedir a conclusão com êxito da operação clone. Certifique-se de que existem não existem problemas de conectividade.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere eliminar quaisquer cópias de segurança que já não são necessárias.</li></ul>Resolva os problemas, limpe o alerta e repita a operação. |

### <a name="networking-alerts"></a>Alertas de redes
| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Não foi possível ligar ao serviço de autenticação. |Erro de DataPath |O URL que é utilizado para autenticar não está acessível. Certifique-se de que as regras de firewall incluem os padrões de URL especificados para o dispositivo StorSimple. Para obter mais informações sobre os padrões de URL no portal do Azure, aceda a [matriz Virtual StorSimple requisitos de rede](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Sofram atrasos inesperados na transferência de dados. |Transferência de dados lenta. |Limitação erros ocorrem quando exceder os destinos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento efetua este procedimento para se certificar de que nenhum cliente único ou inquilino pode utilizar o serviço em detrimento dos outros. Para obter mais informações sobre como resolver problemas da sua conta do storage do Azure, aceda a [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Está a executar a baixa no local de reserva de espaço em disco no <*nome de dispositivo*>. |Tempo de resposta lento. |10% do tamanho total aprovisionado para <*nome de dispositivo*> está reservado no local e dispositivo são agora pouco no espaço reservado. A carga de trabalho no <*nome de dispositivo*> está a gerar uma maior taxa de volume de alterações ou pode ter migrada recentemente uma grande quantidade de dados. Isto pode resultar num desempenho reduzido. Considere uma das seguintes ações para resolver este problema:<ul><li>Aumente a largura de banda de nuvem para este dispositivo.</li><li>Reduza ou move cargas de trabalho para outro volume ou partilha.</li></ul> |

### <a name="security-alerts"></a>Alertas de segurança

| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Palavra-passe para <*nome de dispositivo*> irá expirar dentro de <*número*> dias. |Aviso de palavra-passe. |A palavra-passe expira em < número < dias. Considere alterar a palavra-passe. Para obter mais informações, aceda a [alterar a palavra-passe de administrador do dispositivo de matriz Virtual StorSimple](storsimple-virtual-array-change-device-admin-password.md). |

### <a name="update-alerts"></a>Atualizar alertas

| Texto de alerta | Evento | Obter mais informações / as ações recomendadas |
|:--- |:--- |:--- |
| Novas atualizações estão disponíveis para o seu dispositivo. |Estão disponíveis atualizações para a matriz de Virtual StorSimple. |Pode instalar novas atualizações a partir de **manutenção** página. |
| Não foi possível verificar novas atualizações em <*nome de dispositivo*>. |Falha de atualização. |Ocorreu um erro ao instalar novas atualizações. Pode instalar manualmente as atualizações. Se o problema persistir, contacte [Microsoft Support](storsimple-contact-microsoft-support.md). |

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a matriz de Virtual StorSimple](storsimple-ova-overview.md).

