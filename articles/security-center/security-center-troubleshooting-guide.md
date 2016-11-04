---
title: Guia de Resolução de Problemas do Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda a resolver problemas no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: yurid

---
# Guia de Resolução de Problemas do Centro de Segurança do Azure
Este guia destina-se a profissionais de tecnologias da informação (TI), analistas de segurança de informações e administradores de nuvem cujas organizações utilizam o Centro de Segurança do Azure e que precisam de resolver problemas relacionados com o Centro de Segurança.

## Guia de resolução de problemas
Este guia explica como resolver problemas relacionados com o Centro de Segurança. A maior parte da resolução de problemas feita no Centro de Segurança será realizada ao consultar primeiro os [Registos de Auditoria](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) quanto ao componente que falhou. Os registos de auditoria permitem-lhe determinar:

* As operações que foram executadas
* Quem iniciou a operação
* Quando ocorreu a operação
* O estado da operação
* Os valores de outras propriedades que possam ajudá-lo a procurar a operação

O registo de auditoria contém todas as operações de escrita (PUT, POST, DELETE) efetuadas nos seus recursos, no entanto, não inclui as operações de leitura (GET).

## Resolução de problemas da instalação do agente de monitorização no Windows
O agente de monitorização do Centro de Segurança é utilizado para efetuar a recolha de dados. Assim que a recolha de dados estiver ativa e o agente estiver corretamente instalado no computador de destino, os processos em execução devem ser:

* ASMAgentLauncher.exe - Agente de Monitorização do Azure 
* ASMMonitoringAgent.exe - extensão de Monitorização da Segurança do Azure
* ASMSoftwareScanner.exe – Gestor de Análise do Azure

A extensão de Monitorização de Segurança do Azure verifica a existência de várias configurações relevantes de segurança e recolhe registos de segurança da máquina virtual. O gestor de análise será utilizado como um detetor de patches.

Se a instalação for efetuada com êxito, deverá ver uma entrada semelhante à apresentada abaixo nos Registos de Auditoria da VM de destino:

![Registos de Auditoria](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

Também pode obter mais informações sobre o processo de instalação ao ler os registos do agente, localizados em *%systemdrive%\windowsazure\logs* (exemplo: C:\WindowsAzure\Logs).

> [!NOTE]
> Se o Agente do Centro de Segurança do Azure funcionar incorretamente, terá de reiniciar a VM de destino, uma vez que não existe qualquer comando para parar e inicializar o agente.
> 
> 

## Resolução de problemas da instalação do agente de monitorização no Linux
Quando resolver problemas de instalação do Agente da VM num sistema Linux, confirme que a extensão foi transferida para /var/lib/waagent/. Pode executar o comando abaixo para verificar se foi instalada:

`cat /var/log/waagent.log` 

Os outros ficheiros de registo que poderá consultar para efeitos de resolução de problemas são: 

* /var/log/mdsd.err
* /var/log/azure/

Num sistema funcional, deverá ver uma ligação ao processo mdsd na TCP 29130. Este é o syslog que comunica com o processo mdsd. Pode validar este comportamento ao executar o comando abaixo:

`netstat -plantu | grep 29130`

## Contactar o Suporte da Microsoft
Alguns problemas podem ser identificados através das diretrizes fornecidas neste artigo, ao passo que outros estão também documentados no [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) público do Centro de Segurança. Contudo, se tiver de resolver mais problemas, pode abrir um novo pedido de suporte através do Portal do Azure, conforme mostrado abaixo: 

![Suporte da Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## Consultar também
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) – Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure

<!--HONumber=Sep16_HO3-->


