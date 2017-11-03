---
title: "Compreender a utilização detalhada do Azure | Microsoft Docs"
description: "Saiba como leia e compreenda as secções da sua utilização detalhada CSV para a sua subscrição do Azure"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: a99e0dfea16f9fe9776d24fc94eac9ee18c7b2b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Compreender os termos de licenciamento em custos de utilização detalhada do Azure da Microsoft 
O ficheiro CSV de encargos de utilização detalhada contém diária e medição custos de utilização de nível para o período de faturação atual. 

Para obter o ficheiro de utilização detalhada, consulte [como obter o Azure faturação dados de utilização de uma fatura e diariamente](billing-download-azure-invoice-daily-usage-date.md).
Está disponível no formato de ficheiro de valores separados por vírgulas (. csv) que pode abrir uma aplicação de folha de cálculo. Se vir duas versões disponíveis, transfira a versão 2. Este é o formato de ficheiro mais recente.

Custos de utilização são o total **mensal** encargos em subscrições. Os encargos de utilização não tem em conta quaisquer créditos nem descontos.


<div style="padding-top: 56.25%; position: relative; width: 100%;">
<iframe style="position: absolute;top: 0;left: 0;right: 0;bottom: 0;" width="100%" height="100%" src="https://www.youtube.com/embed/p13S350M2Vk" frameborder="0" allowfullscreen></iframe>
</div>

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Termos de detalhado e descrições do seu ficheiro de utilização detalhada
As secções seguintes descrevem os termos importantes mostrados na versão 2 do ficheiro de utilização detalhada.

### <a name="statement"></a>Instrução
A secção superior do ficheiro CSV de utilização detalhada mostra os serviços que utilizou durante o período do mês de faturação. A tabela seguinte lista os termos e as descrições apresentadas nesta secção.

| Termo | Descrição |
| --- | --- |
|Período de Faturação |O período de faturação quando os medidores foram utilizados |
|Categoria do Medidor |Identifica o serviço de nível superior de utilização |
|Subcategoria do Medidor |Define o tipo de serviço do Azure que pode afetar a velocidade |
|Nome do Medidor |Identifica a unidade de medida para a medição consumida |
|Região do Medidor |Identifica a localização do Centro de dados para determinados serviços que têm um preço com base na localização do Centro de dados |
|SKU |Identifica o identificador de sistema exclusivo para cada Medidor do Azure |
|Unidade |Identifica a Unidade em que o serviço é cobrado. Por exemplo, GB, horas, 10 000 s. |
|Quantidade Consumida |A quantidade de medidor utilizado durante o período de faturação |
|Quantidade Incluída |A quantidade de medição que está incluída, sem encargos no seu período de faturação atual |
|Quantidade de Utilização Excedente |Mostra a diferença entre a quantidade consumido e a quantidade incluída. Será cobrado durante este período. Para ofertas de pay as you go com nenhuma quantidade incluída com a oferta, este total é o mesmo que a quantidade consumido. |
|No Limite da Alocação |Mostra os encargos de medição são subtraídos a quantidade de compromisso associada a sua oferta 6 ou de 12 meses. Custos de medição são subtraídos por ordem cronológica. |
|Moeda |A moeda utilizada no seu período de faturação atual |
|Utilização Excedente |Mostra os encargos de medidor excedem a quantidade de compromisso associada a sua oferta 6 ou de 12 meses |
|Tarifa de Alocação |Mostra a taxa de compromisso com base na quantidade total de compromisso associada a sua oferta 6 ou de 12 meses |
|Tarifa |A velocidade a que se estiver a ser cobrados por unidade facturável |
|Valor |Mostra o resultado da multiplicação a coluna de excedido quantidade pela coluna de taxa. Se a quantidade consumido não exceder a quantidade incluída, há sem qualquer encargo nesta coluna. |

### <a name="daily-usage"></a>Utilização diária

A secção diária de utilização do ficheiro CSV mostra detalhes de utilização que afetam as taxas de faturação. A tabela seguinte lista os termos e as descrições apresentadas nesta secção.

| Termo | Descrição |
| --- | --- |
|Data de Utilização |A data em que foi utilizado o medidor |
|Categoria do Medidor |Identifica o serviço de nível superior para que esta utilização pertence |
|ID do Medidor |O identificador de medidor cobrados que é utilizado para a utilização de faturação de preços |
|Subcategoria do Medidor |Define o tipo de serviço do Azure que pode afetar a velocidade |
|Nome do Medidor |Identifica a unidade de medida para a medição consumida |
|Região do Medidor |Identifica a localização do Centro de dados para determinados serviços que têm um preço com base na localização do Centro de dados |
|Unidade |Identifica a unidade que o medidor é cobrada. Por exemplo, GB, horas, 10 000 s. |
|Quantidade Consumida |A quantidade de medição que consumiu para esse dia |
|Localização do Recurso |Identifica o datacenter onde o medidor está em execução |
|Serviço Consumido |O serviço de plataforma Azure que utilizou |
|Grupo de Recursos |O grupo de recursos no qual o medidor implementado está em execução no. <br/><br/>Para obter mais informações, veja [Descrição geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|ID da Instância | O identificador para o medidor. <br/><br/> O identificador contém o nome que especificar para a medição quando foi criado. É o nome do recurso ou o ID de recurso completamente qualificado. Para obter mais informações, consulte [API do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|Etiquetas | Tag que atribuir para a medição. Utilize etiquetas para registos de faturação do grupo.<br/><br/>Por exemplo, pode utilizar etiquetas para distribuir os custos pelo departamento de que utiliza a medição. Services que suporta etiquetas de emissão são máquinas virtuais, armazenamento e serviços de rede aprovisionados através da utilização de [API do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). Para obter mais informações, consulte [organizar os recursos do Azure com etiquetas](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Informações Adicionais |Metadados específicos do serviço. Por exemplo, um tipo de imagem de uma máquina virtual. |
|Informações de Serviço 1 |O nome do projeto que o serviço pertence a sua subscrição |
|Informações de Serviço 2 |Campo de legado que capture metadados específicos do serviço opcionais |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Como torno se de que os encargos na minha ficheiros de utilização detalhada corretos?
Se não houver cobrada uma taxa no seu ficheiro de utilização detalhada que pretende obter mais detalhes, consulte [compreender a fatura do Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>O que sobre externos encargos de serviço?
Os serviços externos (também conhecido como as ordens de Marketplace) são fornecidos pelos fornecedores do serviço independentes e são cobrados separadamente. Os encargos não apareçam a fatura do Azure. Para obter mais informações, consulte [compreender a Azure cobra serviço externa](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?) para obter o seu problema resolvido rapidamente.
