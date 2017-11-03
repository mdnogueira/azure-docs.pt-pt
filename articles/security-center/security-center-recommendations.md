---
title: "Gerir recomendações de segurança no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento explica como como recomendações no Centro de segurança do Azure ajudam a proteger os recursos do Azure e a manter em conformidade com as políticas de segurança."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: terrylan
ms.openlocfilehash: 32735a7dfaafd8bbfb983dd809b2204fb1b2fa10
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Gerir recomendações de segurança no Centro de segurança do Azure
Este documento explica como utilizar as recomendações no Centro de segurança do Azure para o ajudar a proteger os seus recursos do Azure.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Este documento não é um guia passo a passo.
>
>

## <a name="what-are-security-recommendations"></a>Quais são recomendações de segurança?
O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure. Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, cria recomendações. As recomendações orientam-no no processo de configuração de controlos necessários.

## <a name="implementing-security-recommendations"></a>Implementar recomendações de segurança
### <a name="set-recommendations"></a>Recomendações de conjunto
No [definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md), saiba como:

* Configure políticas de segurança.
* Ative a recolha de dados.
* Escolha as recomendações para ver como parte da sua política de segurança.

System center de recomendações de política em torno de atualizações do sistema, as regras de linha de base, programas antimalware, [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) em sub-redes e interfaces de rede, a auditoria de base de dados SQL, encriptação de dados transparente de base de dados do SQL Server, e firewalls de aplicação web.  [Definir políticas de segurança](security-center-policies.md) fornece uma descrição de cada opção de recomendação.

### <a name="monitor-recommendations"></a>Recomendações de monitor
Após definir uma política de segurança, o Centro de Segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. O **recomendações** mosaico em **descrição geral** permite-lhe determinar o número total de recomendações identificadas pelo centro de segurança.

![Recomendações de mosaico][1]

Para ver os detalhes de cada recomendação, selecione o **recomendações mosaico** em **descrição geral**. **Recomendações** abre.

![Recomendações de filtro][2]

As recomendações são apresentadas num formato de tabela em que cada linha representa uma recomendação específica. As colunas desta tabela são:

* **Descrição**: explica a recomendação e que tem de ser feito para resolver.
* **RECURSO**: apresenta uma lista de recursos aos quais se aplica esta recomendação.
* **ESTADO**: descreve o estado atual da Recomendação:
  * **Abra**: A recomendação ainda não foi tratada ainda.
  * **Em curso**: A recomendação está atualmente a ser aplicada aos recursos e é necessária nenhuma ação por si.
  * **Resolvido**: A recomendação já foi concluída (neste caso, a linha está a cinzento).
* **GRAVIDADE**: descreve a gravidade dessa recomendação específica:
  * **Elevada**: uma vulnerabilidade existe com um recurso significativo (por exemplo, uma aplicação, uma VM ou um grupo de segurança de rede) e necessita de atenção.
  * **Média**: uma vulnerabilidade existe e passos não críticos ou adicionais necessários para eliminar este ou para concluir um processo.
  * **Baixa**: uma vulnerabilidade existe que deve ser tratada, mas não necessita de atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender vê-las).

Utilize a tabela abaixo como referência para ajudar a compreender as recomendações disponíveis e que cada um faz se aplicá-lo.

> [!NOTE]
> Deve compreender o [clássica e modelos de implementação do Resource Manager](../azure-classic-rm.md) para recursos do Azure.
>
>

| Recomendação | Descrição |
| --- | --- |
| [Ativar a recolha de dados para subscrições](security-center-enable-data-collection.md) |Recomenda-se que ative a recolha de dados na política de segurança para cada uma das suas subscrições e todas as máquinas de virtuais (VMs) do Azure e computadores não do Azure. |
| [Remediar vulnerabilidades do SO](security-center-remediate-os-vulnerabilities.md) |Recomenda que alinhar as configurações de SO com as regras de configuração recomendada, por exemplo, não permitir palavras-passe sejam guardados. |
| [Aplicar atualizações do sistema](security-center-apply-system-updates.md) |Recomenda-se de que implemente atualizações críticas e de segurança do sistema em falta para o Windows e VMs com Linux e computadores. |
| [Aplicar um Just-In-Time controlo de acesso de rede](security-center-just-in-time.md) | Recomenda-se de que se aplicam apenas no acesso VM de tempo. O apenas na hora funcionalidade em pré-visualização e está disponível na camada padrão do Centro de segurança. Consulte [preços](security-center-pricing.md) para saber mais acerca do Centro de segurança do escalões de preço. |
| [Reiniciar após atualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates) |Recomenda-se que reinicie uma VM para concluir o processo de aplicar atualizações do sistema. |
| [Adicionar uma firewall de aplicação Web](security-center-add-web-application-firewall.md) |Recomenda-se de que irá implementar uma firewall de aplicação web (WAF) para pontos finais do web. É apresentada uma recomendação WAF para qualquer destinado ao IP público (IP de nível de instância ou IP com balanceamento de carga) que tenha um grupo de segurança de rede associada com portas web de entrada aberta (80,443). </br>Centro de segurança recomenda que Aprovisiona uma WAF para ajudar a proteger contra ataques direcionada para as aplicações web em máquinas virtuais e no ambiente de serviço de aplicações. Aplicação serviço de ambiente (ASE) é um [Premium](https://azure.microsoft.com/pricing/details/app-service/) service opção plano do App Service do Azure fornece um ambiente completamente isolado e dedicado para execução segura de aplicações do App Service do Azure. Para saber mais sobre ASE, consulte o [a documentação de ambiente de serviço de aplicação](../app-service/environment/intro.md).</br>Pode proteger várias aplicações web no Centro de segurança ao adicionar estas aplicações para as implementações de WAF existentes. |
| [Finalizar a proteção das aplicações](security-center-add-web-application-firewall.md#finalize-application-protection) |Para concluir a configuração de uma WAF, tráfego deve ser reencaminhado para a aplicação de WAF. Seguir esta recomendação conclui as alterações de configuração necessários. |
| [Adicionar uma Firewall da Próxima Geração](security-center-add-next-generation-firewall.md) |Recomenda-se de que adicionar uma Firewall seguinte de geração (NGFW) a partir de um parceiro da Microsoft para aumentar as proteções de segurança. |
| [Encaminhar o tráfego apenas através da NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Recomenda-se de que configura regras de grupo (NSG) de segurança de rede forçar o tráfego de entrada à VM através do seu NGFW. |
| [Instalar o Endpoint Protection](security-center-install-endpoint-protection.md) |Recomenda-se que aprovisione programas de antimalware em VMs (apenas VMs do Windows). |
| [Ativar grupos de segurança de rede em sub-redes ou máquinas virtuais](security-center-enable-network-security-groups.md) |Recomenda-se que ative NSGs em sub-redes ou VMs. |
| [Restringir o acesso através da Internet com o ponto final](security-center-restrict-access-through-internet-facing-endpoints.md) |Recomenda-se de que configura as regras de tráfego de entrada para os NSGs. |
| [Ativar a auditoria e a deteção de ameaças em servidores SQL](security-center-enable-auditing-on-sql-servers.md) |Recomenda-se que ative a auditoria e deteção de ameaças para servidores SQL do Azure. (Apenas para serviço do SQL do azure. Não inclui o SQL Server em execução em máquinas virtuais.) |
| [Ativar a auditoria e a deteção de ameaças nas bases de dados SQL](security-center-enable-auditing-on-sql-databases.md) |Recomenda-se que ative a auditoria e deteção de ameaças para bases de dados SQL do Azure. (Apenas para serviço do SQL do azure. Não inclui o SQL Server em execução em máquinas virtuais.) |
| [Ativar a encriptação transparente de dados nas bases de dados do SQL Server](security-center-enable-transparent-data-encryption.md) |Recomenda-se de que permitem a encriptação de bases de dados do SQL Server. (Apenas serviço SQL do azure.) |
| [Ativar o Agente de VM](security-center-enable-vm-agent.md) |Permite-lhe ver as VMs que necessitam do Agente de VM. O agente da VM deve ser instalado em VMs patch aprovisionar análise, a linha de base de análise e programas antimalware. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. O artigo [VM Agent and Extensions – Part 2 (Agente de VM e Extensões – Parte 2)](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM. |
| [Aplicar encriptação de discos](security-center-apply-disk-encryption.md) |Recomenda-se que encripte os discos da VM com o Azure Disk Encryption (VMs Windows e Linux). A encriptação é recomendada para o SO e os volumes de dados na sua VM. |
| [Disponibilizar detalhes de contacto de segurança](security-center-provide-security-contact-details.md) |Recomenda que fornece segurança de informações de contacto para cada uma das suas subscrições. Informações de contacto são um número de telefone e endereço de e-mail. As informações são utilizadas para contactá-lo se a nossa equipa de segurança localiza a que os recursos sejam comprometidos. |
| [Atualizar a versão do SO](security-center-update-os-version.md) |Recomenda-se de que atualiza a versão do sistema operativo (SO) do serviço em nuvem para a versão mais recente disponível para a sua família de SO.  Para saber mais sobre os serviços de nuvem, consulte o [descrição geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação de vulnerabilidades não instalada](security-center-vulnerability-assessment-recommendations.md) |Recomenda-se de que instala uma solução de avaliação de vulnerabilidades na sua VM. |
| [Remediar vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Permite-lhe ver as vulnerabilidades de sistema e de aplicações detetadas pela solução de avaliação de vulnerabilidade instalada na sua VM. |
| [Ativar a encriptação da conta de armazenamento do Azure](security-center-enable-encryption-for-storage-account.md) | Recomenda-se de ativar a encriptação do serviço de armazenamento do Azure para dados inativos. Encriptação de serviço de armazenamento (SSE) funciona ao encriptar os dados quando são escritos no armazenamento do Azure e desencripta antes de obtenção. SSE está atualmente disponível apenas para o serviço Blob do Azure e pode ser utilizado para blobs de blocos, blobs de páginas e blobs de acréscimo. Para obter mais informações, consulte [encriptação do serviço de armazenamento para dados Inativos](../storage/common/storage-service-encryption.md).</br>SSE só é suportado em contas de armazenamento do Resource Manager. |

Pode filtrar e dispensar recomendações.

1. Selecione **filtro** no **recomendações** painel. O **filtro** painel abre-se e selecione os valores de estado e gravidade que pretende ver.

2. Se determinar que uma recomendação não é aplicável, pode ignorar a recomendação e, em seguida, filtrá-lo da vista. Existem duas formas de dispensar uma recomendação. É uma forma para o botão direito do rato clique num item e, em seguida, selecione **dispensar**. O outro é paira o rato sobre um item, clique nas reticências que aparecem à direita e, em seguida, selecione **dispensar**. Pode ver as recomendações dispensadas ao clicar **filtro**e, em seguida, selecionar **dispensados**.

    ![Ignorar a recomendação][3]

### <a name="apply-recommendations"></a>Aplicar recomendações
Depois de rever todas as recomendações, decida que um deve ser aplicada primeiro. Recomendamos que utilize a classificação de gravidade, como o parâmetro principal para avaliar as recomendações deve ser aplicado pela primeira vez.

Na tabela de recomendações acima, selecione uma recomendação e guiá-lo como um exemplo de como aplicar a recomendação.

## <a name="next-steps"></a>Passos seguintes
Neste documento, foram introduzidas para recomendações de segurança no Centro de segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) — Saiba como configurar políticas de segurança para as subscrições do Azure e os grupos de recursos.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
