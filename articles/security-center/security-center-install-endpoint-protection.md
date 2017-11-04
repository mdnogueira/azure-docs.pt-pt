---
title: "Gerir problemas de proteção de ponto final com o Centro de segurança do Azure | Microsoft Docs"
description: "Saiba como gerir problemas de proteção de ponto final no Centro de segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2017
ms.author: terrylan
ms.openlocfilehash: abbcb0a8e0206d78ca94520dfa81ab92506c47af
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Gerir problemas de proteção de ponto final com o Centro de segurança do Azure
Centro de segurança do Azure monitoriza o estado de proteção antimalware e os relatórios tal o painel de problemas do Endpoint protection. Centro de segurança realça problemas, tais como a ameaças detetadas e proteção insuficiente, que pode tornar as máquinas de virtuais (VMs) e os computadores vulneráveis a ameaças de antimalware. Ao utilizar as informações em **problemas de proteção de ponto final**, pode identificar um plano para resolver quaisquer problemas identificados.

Centro de segurança relatórios os seguintes problemas de proteção de ponto final:

- Endpoint protection não é instalado em VMs do Azure – uma solução antimalware suportado não está instalado nestas VMs do Azure.
- Endpoint protection não é instalado em computadores não do Azure – um antimalware suportado não está instalada nestes computadores não do Azure.
- Estado de funcionamento do Endpoint protection:

   - Assinatura desatualizada – uma solução antimalware é instalada nessas VMs e computadores, mas a solução não têm as assinaturas de antimalware mais recentes.
   - Sem proteção em tempo real – uma solução antimalware é instalada nessas VMs e computadores, mas não está configurado para proteção em tempo real.   O serviço poderá estar desativado ou centro de segurança poderá não ser possível obter o estado, porque a solução não é suportada. Consulte [parceiro integração](security-center-partner-integration.md) para obter uma lista das soluções suportadas.
   - Não Reporting Services – uma solução antimalware é instalada, mas não os dados de relatórios.
   - Desconhecido – uma solução antimalware é instalado, mas o respetivo estado é desconhecido ou reportar um erro desconhecido.

   > [!NOTE]
   > Consulte [integrar soluções de segurança](security-center-partner-integration.md#integrated-azure-security-solutions) para obter uma lista de soluções de segurança de proteção de ponto final integrado com o Centro de segurança.
   >
   >

## <a name="implement-the-recommendation"></a>Implementar a recomendação
Problemas de proteção de ponto final é apresentada como uma recomendação no Centro de segurança.  Se o seu ambiente é vulnerável a ameaças de antimalware, esta recomendação será apresentada em **recomendações** e, em **computação**. Para ver o **dashboard de problemas do Endpoint protection**, tem de seguir o fluxo de trabalho de computação.

Neste exemplo, utilizaremos **computação**.  Veremos como instalar antimalware em VMs do Azure e em computadores não do Azure.

## <a name="install-antimalware-on-azure-vms"></a>Instalar antimalware em VMs do Azure

1. Selecione **computação** sob o menu principal do Centro de segurança ou **descrição geral**.

   ![Selecione de computação][1]

2. Em **computação**, selecione **problemas de proteção de ponto final**. O **problemas de proteção de ponto final** dashboard abre.

   ![Selecione os problemas de proteção de ponto final][2]

   Fornece a parte superior do dashboard:

   - Instalar fornecedores de proteção de ponto final - apresenta uma lista de fornecedores diferentes identificadas pelo centro de segurança.
   - Estado de funcionamento da proteção de ponto final instalada - mostra o estado de funcionamento de VMs e os computadores que têm uma solução de proteção de ponto final instalada. O gráfico mostra o número de VMs e os computadores que estão em bom Estados e o número com proteção insuficiente.
   - Software maligno detetado – mostra o número de VMs e computadores em que Centro de segurança é detetado software maligno.
   - Computadores atacados – mostra o número de VMs e computadores em que Centro de segurança está a comunicar ataques por software maligno.

   Na parte inferior do dashboard não há uma lista de ponto final de problemas de proteção que inclui as seguintes informações:  

   - **TOTAL** -o número de VMs e os computadores afetados pelo problema.
   - A barra agregar o número de VMs e os computadores afetados pelo problema. As cores na barra de identificam prioridade:

      - Vermelho - alta prioridade e deve ser resolvido imediatamente
      - Cor de laranja - prioridade média e deve ser resolvido logo que possível

3. Selecione **Endpoint protection não é instalado em VMs do Azure**.

   ![Selecionar a proteção de ponto final não instalada em VMs do Azure][3]

4. Em **Endpoint protection não é instalado em VMs do Azure** é uma lista de VMs do Azure que não tenham antimalware instalado.  Pode optar por instalar antimalware em todas as VMs na lista ou selecione VMs individuais para instalar o antimalware clicando na VM específica.
5. Em **selecione Endpoint protection**, selecione a solução de proteção de ponto final que pretende utilizar. Neste exemplo, selecione **Antimalware da Microsoft**.
6. São apresentadas informações adicionais sobre a solução de proteção de ponto final. Selecione **Criar**.

## <a name="install-antimalware-on-non-azure-computers"></a>Instalar antimalware em computadores não do Azure

1. Volte ao **problemas de proteção de ponto final** e selecione **Endpoint protection não é instalado em computadores não Azure**.

   ![Selecionar a proteção de ponto final não instalada em computadores não do Azure][4]

2. Em **Endpoint protection não é instalado em computadores não Azure**, selecione uma área de trabalho. Uma consulta de pesquisa de análise de registos filtrada à área de trabalho abre e apresenta uma lista de computadores em falta antimalware. Selecione um computador a partir da lista para obter mais informações.

   ![Análise de registos de pesquisa][5]

Resultado da pesquisa outro abre-se com informações filtradas apenas para esse computador.

  ![Análise de registos de pesquisa][6]

> [!NOTE]
> Recomendamos que do endpoint protection seja aprovisionada para todas as VMs e computadores ajudar a identificar e remover vírus, spyware e outro software malicioso.
>
>

## <a name="next-steps"></a>Passos seguintes
Este artigo mostrou como implementar a recomendação de centro de segurança "Instalar o Endpoint Protection." Para obter mais informações sobre como ativar o Antimalware da Microsoft no Azure, consulte o documento seguinte:

* [Antimalware da Microsoft para máquinas virtuais e serviços em nuvem](../security/azure-security-antimalware.md) – Saiba como implementar Antimalware da Microsoft.

Para saber mais acerca do Centro de segurança, consulte os seguintes documentos:

* [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e conformidade.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
