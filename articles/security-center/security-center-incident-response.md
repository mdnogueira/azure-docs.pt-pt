---
title: Tirar Partido do Centro de Segurança do Azure para Resposta a Incidentes | Microsoft Docs
description: Este documento explica como tirar partido do Centro de Segurança do Azure para obter um cenário de Resposta a Incidentes.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2016
ms.author: yurid

---
# Tirar Partido do Centro de Segurança do Azure para Resposta a Incidentes
Muitas organizações só aprendem a responder a incidentes de segurança depois de sofrerem um ataque. Para reduzir os custos e os danos, é importante ter um plano de resposta a incidentes implementado antes que ocorra um ataque. O Centro de segurança do Azure pode ser otimizado em várias fases de uma resposta a incidentes.

## Resposta a incidentes
Um plano eficaz depende de três funcionalidades principais: as capacidades para proteger, detetar e responder a ameaças. A proteção está relacionada com a prevenção de incidentes, a deteção com a identificação precoce de ameaças, a resposta diz respeito à expulsão de um atacante e a recuperação de sistemas para minimizar as consequências decorrentes de uma violação. 

Este artigo irá utilizar as fases de resposta a incidentes de segurança com base no artigo [Resposta de Segurança do Microsoft Azure na Nuvem](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678), conforme mostrado no diagrama seguinte:

![Ciclo de vida de resposta a incidentes](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Pode ser tirar-se partido do Centro de Segurança durante as fases de Deteção, Avaliação e Diagnóstico. Para saber mais sobre cada uma destas fases. Eis um exemplo de como o Centro de Segurança pode ser útil durante as três fases iniciais da resposta a incidentes:

* **Detetar**: primeira indicação uma investigação de eventos
  * Exemplo: verificação inicial a indicar que foi gerado um alerta de segurança de alta prioridade no dashboard do Centro de Segurança.
* **Avaliar**: efetue a avaliação inicial para obter mais informações sobre a atividade suspeita.
  * Exemplo: obter mais informações sobre o alerta de segurança.
* **Diagnosticar**: realizar uma investigação técnica, identificar as estratégias de contenção, atenuação e solução
  * Exemplo: siga os passos de remediação descritos pelo Centro de Segurança nesse alerta de segurança específico.

O cenário que se segue mostra como tirar partido do Centro de Segurança durante as fases de deteção, avaliação e diagnóstico/resposta de um incidente de segurança.  No Centro de Segurança, um [incidente de segurança](security-center-incident.md) é uma agregação de todos os alertas de um recurso que são alinhados com padrões de [cadeia de eliminação](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Os incidentes surgem no mosaico e no painel [Alertas de Segurança](security-center-managing-and-responding-alerts.md). Um Incidente apresenta a lista de alertas relacionados, o que lhe permitir obter mais informações sobre cada ocorrência. O Centro de Segurança apresenta também alertas de segurança autónomos que também podem ser utilizados para identificar atividades suspeitas.

## Cenário
O Contoso migrou recentemente alguns dos recursos no local para o Azure, incluindo algumas cargas de trabalho de linha de negócio baseadas em máquinas virtuais e bases de dados do SQL. De momento, a CSIRT (Equipa de Resposta a Incidentes de Segurança Informática) da Contoso tem dificuldade em investigar problemas de segurança, devido à falta de informações de segurança integradas com as respetivas ferramentas atuais de resposta a incidentes. Esta falta de integração diagnosticar fases e apresenta um problema durante a deteção (demasiados falsos positivos) e durante as fases de avaliação e diagnóstico. Como parte desta migração, eles decidem se pretendem que o Centro de Segurança os ajude a resolver este problema. 

A primeira fase desta migração foi concluída após a integração de todos os recursos e a aceitação de todas as recomendações de segurança do Centro de Segurança. O Contoso CSIRT é o ponto central para lidar com incidentes de segurança informática. A equipa consiste num grupo de pessoas com responsabilidades para lidar com qualquer incidente de segurança. Os membros da equipa têm deveres claramente definidos para se certificar de que nenhuma área de resposta é deixada sem cobertura. 

Para efeitos deste cenário, vamos concentrar-nos nas funções das seguintes pessoas que fazem parte do Contoso CSIRT:

![Ciclo de vida de resposta a incidentes](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Constança trabalha em operações de segurança e as respetivas responsabilidades incluem:

* Monitorização e capacidade de resposta contínuas a ameaças de segurança
* Escalonamento para o proprietário de cargas de trabalho na nuvem ou analista de segurança, conforme necessário

Samuel é um analista de segurança e as suas responsabilidades incluem:

* Investigar ataques
* Resolver alertas 
* Trabalhar com proprietários de cargas de trabalho para determinar e aplicar atenuações

Como pode ver, Constança e Samuel têm responsabilidades diferentes e devem trabalhar em conjunto, partilhando as informações obtidas a partir do Centro de Segurança. 

## Solução recomendada
Uma vez que Constança e Samuel têm funções diferentes, estes vão utilizar diferentes áreas do Centro de Segurança para obter informações relevantes para as respetivas atividades diárias. Constança vai utilizar Alertas de Segurança como parte da respetiva monitorização diária. 

![Alerta de Segurança](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Constança vai utilizar Alertas de Segurança durante as fases de Deteção e Avaliação. Depois de Constança terminar a avaliação inicial, ela poderá escalar o problema para Samuel se for necessária investigação adicional. Neste momento Samuel vai ter de utilizar as informações fornecidas pelo Centro de Segurança, por vezes em conjunto com outras origens de dados, para avançar para a fase de Diagnóstico.

## Como implementar esta solução
Para saber como utilizaria o Centro de Segurança do Azure num cenário de resposta a incidentes, vamos seguir os passos de Constança nas fases de Deteção e Avaliação e, em seguida, ver o que faz Samuel para diagnosticar o problema. 

### Diagnosticar e avaliar fases de resposta a incidentes
Constança iniciou sessão no portal do Azure e está na consola do Centro de Segurança. Como parte das respetivas atividades de monitorização diária, ela iniciou a revisão dos alertas de segurança de alta prioridade, efetuando os seguintes passos:

1. Clique no mosaico **Alertas de Segurança** e aceda ao painel **Alertas de Segurança**.
    ![Painel de Alerta de Segurança](./media/security-center-incident-response/security-center-incident-response-fig4.png)
   
   > [!NOTE]
   > Para efeitos deste cenário, Constança vai efetuar uma avaliação sobre o alerta de atividade SQL maliciosa, conforme indicado na imagem acima. 
   > 
   > 
2. Clique em **Atividade SQL maliciosa** e reveja os recursos atacados no painel **Atividade SQL maliciosa**:  ![Detalhes do incidente](./media/security-center-incident-response/security-center-incident-response-fig5.png)
   
    Neste painel, Constança pode tomar notas sobre os recursos atacados, o número de ataques e quando foram detetados.
3. Clique no **recurso atacado** para obter mais informações sobre este ataque. 

Depois de ler a descrição, Constança tem a certeza de que não se trata de um falso positivo e que deve escalar este caso para Samuel.

### Diagnosticar a fase de resposta a incidentes
Samuel recebe o caso de Constança e começa a rever os passos de remediação sugeridos pelo Centro de Segurança.

![Ciclo de vida de resposta a incidentes](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### Recursos adicionais
A equipa de resposta a incidentes pode também aproveitar a capacidade do [Power Bi do Centro de Segurança](security-center-powerbi.md) para ver diferentes tipos de relatórios que podem ajudá-lo durante uma investigação posterior para visualizar, analisar e filtrar recomendações e alertas de segurança. As empresas que utilizam a respetiva solução de SIEM (Gestão de Segurança da Informação e Eventos) durante o processo de investigação também podem [integrar o Centro de Segurança com a respetiva solução](security-center-integrating-alerts-with-log-integration.md). Os registos de auditoria do Azure e os eventos de segurança VM podem também ser integrados com a [ferramenta de integração de registo do Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Esta informação pode ser utilizada em conjunto com as informações fornecidas pelo Centro de Segurança para investigar um ataque.

## Conclusão
A criação de uma equipa antes de ocorrer um incidente é muito importante para a sua organização e irá influenciar positivamente como os incidentes são processados. Ter as ferramentas certas para monitorizar recursos pode ajudar a esta equipa a tomar medidas precisas para resolver um incidente de segurança. As [capacidades de deteção](security-center-detection-capabilities.md) do Centro de Segurança vão ajudar as TI a responder rapidamente a incidentes de segurança e a resolver problemas de segurança.

<!--HONumber=Sep16_HO3-->


