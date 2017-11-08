---
title: Resolver problemas de um dispositivo StorSimple implementado | Microsoft Docs
description: "Descreve como diagnosticar e corrigir os erros que ocorrem num dispositivo StorSimple que está atualmente implementado e operacional."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 4639e749cfaa6b38fa6b91ddebfc17924c1d5bd6
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Resolver problemas de um dispositivo StorSimple operacional
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
Este artigo fornece orientação de resolução de problemas úteis para resolver problemas de configuração que podem ocorrer depois do dispositivo StorSimple é implementado e operacional. Descreve problemas comuns, as causas possíveis e passos recomendados para ajudar a resolver problemas que podem ocorrer ao executar o Microsoft Azure StorSimple. Estas informações aplicam-se ao dispositivo físico StorSimple no local e o dispositivo virtual StorSimple.

No final deste artigo que pode encontrar uma lista de códigos de erro que poderá encontrar durante a operação do Microsoft Azure StorSimple, bem como passos que pode tomar para resolver os erros. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processo de Assistente de configuração para dispositivos operacionais
Utilize o Assistente de configuração ([Invoke-HcsSetupWizard][1]) para verificar a configuração de dispositivo e tome as medidas corretivas, se necessário.

Quando executar o Assistente de configuração num dispositivo previamente configurado e operacional, o fluxo de processo é diferente. Pode alterar as seguintes entradas:

* Endereço IP, máscara de sub-rede e gateway
* Servidor DNS primário
* Servidor NTP primário
* Configuração do proxy web opcional

O Assistente de configuração não efetua as operações relacionadas com o registo de coleção e de dispositivos de palavra-passe.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erros ocorridos durante execuções subsequentes do Assistente de configuração
A tabela seguinte descreve os erros que poderão surgir quando executar o Assistente de configuração num dispositivo operacional, as causas possíveis para os erros e recomendado ações para resolvê-los. 

| Não. | Mensagem de erro ou condição | Causas possíveis | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1 |Erro 350032: Este dispositivo já foi desativado. |Irá ver este erro, se executar o Assistente de configuração num dispositivo que está desativado. |[Contacte o Support Microsoft](storsimple-contact-microsoft-support.md) para passos seguintes. Um dispositivo desativado não é possível colocar no serviço. Uma reposição de fábrica de mensagens em fila pode ser necessária antes do dispositivo pode ser ativado novamente. |
| 2 |Invocar HcsSetupWizard: ERROR_INVALID_FUNCTION (excepção de HRESULT: 0x80070001) |A atualização do servidor DNS está a falhar. As definições de DNS são definições globais e são aplicadas em todas as interfaces de rede ativada. |Ative a interface e aplique as definições de DNS novamente. Isto poderá interromper a rede para outras interfaces ativadas porque estas definições são globais. |
| 3 |É apresentado o dispositivo estar online no portal do serviço StorSimple Manager, mas quando tenta concluir a configuração mínima e guardar a configuração, a operação falhar. |Durante a configuração inicial, o proxy web não foi configurado, mesmo que foi um servidor proxy real no local. |Utilize o [cmdlet Test-HcsmConnection] [ 2] para localizar o erro. [Contacte o Support Microsoft](storsimple-contact-microsoft-support.md) se não for possível corrigir o problema. |
| 4 |Invocar HcsSetupWizard: O valor não pertence ao intervalo esperado. |Uma máscara de sub-rede incorreto produz este erro. As causas possíveis são: <ul><li> A máscara de sub-rede está em falta ou vazia.</li><li>O formato de prefixo de Ipv6 está incorreto.</li><li>A interface está ativado para a nuvem, mas o gateway está em falta ou incorretos.</li></ul>Tenha em atenção que a DATA 0 é automaticamente ativado para a nuvem se configurado através do Assistente de configuração. |Para determinar o problema, utilize a sub-rede 0.0.0.0 ou 256.256.256.256 e, em seguida, observar os resultados. Introduza os valores corretos para a máscara de sub-rede, o gateway e o prefixo de Ipv6, conforme necessário. |

## <a name="error-codes"></a>Códigos de erro
Erros são apresentados por ordem numérica.

| Número de erro | Texto de erro ou descrição | Ação do utilizador recomendada |
|:--- |:--- |:--- |
| 10502 |Foi encontrado um erro ao aceder à sua conta do storage. |Aguarde alguns minutos e, em seguida, tente novamente. Se o erro persistir, volte a contactar o suporte da Microsoft para os passos seguintes. |
| 40017 |A operação de cópia de segurança falhou como um volume especificado na política de cópia de segurança não foi encontrado no dispositivo. |Repita a cópia de segurança operação, se o erro persistir, contacte Support da Microsoft. para os passos seguintes. |
| 40018 |A operação de cópia de segurança falhou porque foi encontrado nenhum dos volumes especificados na política de cópia de segurança no dispositivo. |Repita a cópia de segurança operação, se o erro persistir, contacte Support da Microsoft. para os passos seguintes. |
| 390061 |O sistema está ocupado ou não está disponível. |Aguarde alguns minutos e, em seguida, tente novamente. Se o erro persistir, volte a contactar o suporte da Microsoft para os passos seguintes. |
| 390143 |Ocorreu um erro com o código de erro 390143. (Erro desconhecido.) |Se o erro persistir, contacte a Microsoft Support para passos seguintes. |

## <a name="next-steps"></a>Passos seguintes
Se não for possível resolver o problema, [contacte a Microsoft Support](storsimple-contact-microsoft-support.md) para obter assistência. 

[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
