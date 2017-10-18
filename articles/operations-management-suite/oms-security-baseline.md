---
title: "Linha de Base de Solução de Segurança e Auditoria do Operations Management Suite | Microsoft Docs"
description: "Este documento explica como utilizar a Segurança OMS e a solução de Auditoria para efetuar uma avaliação de linha de base de todos os computadores monitorizados para o objetivo de segurança e de conformidade."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 6f4cfda6f367cb8a68d038fa0a3390442be034c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Avaliação da Linha de Base em Solução de Segurança e Auditoria do Operations Management Suite
Este documento ajuda-o a utilizar as capacidades de avaliação da linha de base da [Solução de Segurança e Auditoria do Operations Management Suite (OMS)](operations-management-suite-overview.md) para aceder ao estado seguro dos seus recursos monitorizados.

## <a name="what-is-baseline-assessment"></a>O que é a Avaliação da Linha de Base?
A Microsoft, juntamente com o setor e as organizações governamentais em todo o mundo, define uma configuração do Windows que representa implementações de servidor altamente seguras. Esta configuração é um conjunto de chaves de registo, definições de política de auditoria e definições de política de segurança juntamente com os valores recomendados da Microsoft para estas definições. Este conjunto de regras é conhecido como linha de base de Segurança. A Segurança OMS e a capacidade de avaliação da linha de base de Auditoria podem analisar perfeitamente todos os computadores para verificar compatibilidade. 

Existem três tipos de regras:

* **Regras de registo**: verifique se as chaves de registo estão definidas corretamente.
* **Regras de política de auditoria**: regras sobre a sua política de auditoria.
* **Regras de política de segurança**: regras sobre as permissões do utilizador na máquina.

> [!NOTE]
> Leia o artigo [Utilize a Segurança OMS para aceder à Linha de Base de Configuração de Segurança](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/) para uma breve descrição geral desta funcionalidade.
> 
> 

## <a name="security-baseline-assessment"></a>Avaliação da Linha de Base de Segurança
Pode rever a avaliação de linha de base de segurança atual para todos os computadores que são monitorizados pela Segurança OMS e Auditoria utilizando o dashboard. Execute os passos seguintes para aceder ao dashboard de avaliação da linha de base de segurança:

1. No dashboard principal do **Microsoft Operations Management Suite**, clique em mosaico de **Segurança e Auditoria**.
2. No dashboard de **Segurança e Auditoria** dashboard, clique em **Avaliação da Linha de base** em **Domínios de Segurança**. O dashboard de **Avaliação da Linha de Base de Segurança** é apresentado na imagem seguinte:
   
    ![Segurança OMS e Avaliação da Linha de Base de Auditoria](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Este dashboard está dividido em três áreas principais:

* **Computadores comparados à linha de base**: esta secção fornece um resumo do número de computadores que foram acedidos e a percentagem de computadores que passaram a avaliação. Também apresenta os 10 melhores computadores e o resultado de percentagem para a avaliação.
* **Estado das Regras Obrigatórias**: esta secção tem a intenção de alertar para as regras com falhas por gravidade e para as regras com falhas por tipo. Ao consultar o primeiro gráfico pode identificar rapidamente se a maioria das regras com falhas são críticas, ou não. Também apresenta a lista das 10 principais regras com falhas e a respetiva gravidade. O segundo gráfico mostra o tipo de regra que falhou durante a avaliação. 
* **Computadores com avaliação da linha de base em falta**: esta secção lista os computadores que não foram acedidos devido à incompatibilidade do sistema operativo ou falhas. 

### <a name="accessing-computers-compared-to-baseline"></a>Aceder a computadores em comparação com a linha de base
Idealmente todos os seus computadores devem satisfazer a avaliação da linha de base de segurança. Contudo, é esperado que em algumas circunstâncias isto não aconteça. Como parte do processo de gestão de segurança, é importante incluir a revisão dos computadores que falharam em todos os testes de avaliação de segurança. Uma forma rápida para visualizar é selecionando a opção **Computadores acedidos** localizado na secção **Computadores comparados com a linha de base**. Deverá ver o resultado da pesquisa de registo que apresenta a lista de computadores, como mostra no ecrã seguinte:

![Resultados do computador acedido](./media/oms-security-baseline/oms-security-baseline-fig2.png)

O resultado da pesquisa é apresentado em formato de tabela, onde a primeira coluna tem o nome do computador e a segunda cor tem o número de regras que falharam. Para obter as informações sobre o tipo de regra que falhou, clique no número de regras com falhas salvo o nome do computador. Deverá ver um resultado semelhante ao apresentado na imagem seguinte:

![Detalhes dos resultados do computador acedido](./media/oms-security-baseline/oms-security-baseline-fig3.png)

Neste resultado da pesquisa, tem o total de regras de acesso, o número de regras críticas que falharam, as regras de aviso e as regras de informações com falhas.

### <a name="accessing-required-rules-status"></a>Aceder ao estado de regras obrigatórias
Depois de obter as informações sobre o número de percentagem de computadores que passaram a avaliação, poderá pretender obter mais informações sobre quais as regras que estão a falhar, de acordo com o nível crítico. Esta visualização ajuda-o a atribuir prioridade aos computadores que devem ser resolvidos primeiro, para se certificar de que serão compatíveis na avaliação seguinte. Passe o rato sobre a parte Crítica do gráfico localizado no mosaico **Regras com falhas por gravidade** no **Estado de regras obrigatórias** e clique. Deverá ver um resultado semelhante ao ecrã seguinte:

![Regras com falhas por detalhes de gravidade](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

Neste resultado de registo pode ver o tipo de regra de linha de base que falhou, a descrição desta regra e o ID de Enumeração de Configuração Comum (CCE) desta regra de segurança. Estes atributos deveriam ser suficientes para executar uma ação de correção para corrigir este problema no computador de destino.

> [!NOTE]
> Para mais informações sobre CCE, aceder a [Base de Dados de Vulnerabilidade Nacional](https://nvd.nist.gov/cce/index.cfm).
> 
> 

### <a name="accessing-computers-missing-baseline-assessment"></a>Aceder a computadores com avaliação da linha de base em falta
O OMS suporta o perfil da linha da base do Controlador de Domínio e do membro de domínio no Windows Server 2008 R2 até ao Windows Server 2012 R2. A linha de base do Windows Server 2016 ainda não é final e será adicionada assim que for publicada. Todos os outros sistemas operativos analisados através da avaliação da linha de base de Segurança e Auditoria do OMS aparecem na secção **Computadores com avaliação da linha de base em falta**.

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu sobre a avaliação da linha de base de Segurança e Auditoria do OMS. Para saber mais sobre a Segurança do OMS, veja os artigos seguintes:

* [Descrição geral do Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Monitorização e Resposta aos Alertas de Segurança na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)
* [Recursos de Monitorização na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)

