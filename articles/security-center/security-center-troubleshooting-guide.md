---
title: "Guia de Resolução de Problemas do Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda a resolver problemas no Centro de Segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: b9f4a8b185f9fb06f8991b6da35a5d8c94689367
ms.openlocfilehash: dbbec729c14d0d9dc5781e7a88a1db3f66f7df97


---
# <a name="azure-security-center-troubleshooting-guide"></a>Guia de Resolução de Problemas do Centro de Segurança do Azure
Este guia destina-se a profissionais de tecnologias da informação (TI), analistas de segurança de informações e administradores da cloud cujas organizações utilizam o Centro de Segurança do Azure e que precisam de resolver problemas relacionados com o Centro de Segurança.

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Este guia explica como resolver problemas relacionados com o Centro de Segurança. A maior parte da resolução de problemas feita no Centro de Segurança será realizada ao consultar primeiro os [Registos de Auditoria](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) quanto ao componente que falhou. Os registos de auditoria permitem-lhe determinar:

* As operações que foram executadas
* Quem iniciou a operação
* Quando ocorreu a operação
* O estado da operação
* Os valores de outras propriedades que possam ajudá-lo a procurar a operação

O registo de auditoria contém todas as operações de escrita (PUT, POST, DELETE) efetuadas nos seus recursos, no entanto, não inclui as operações de leitura (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Resolução de problemas da instalação do agente de monitorização no Windows
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


Se está ainda está a ter problemas com a recolha de dados, pode desinstalar o agente ao seguir os passos abaixo:

1. No **Portal do Azure**, selecione a máquina virtual que está a experienciar os problemas da recolha de dados e clique em **Extensões**.
2. Clique com o botão direito do rato em **Microsoft.Azure.Security.Monitoring** e clique em **Desinstalar**.

![Remover agente](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig4.png)

A extensão de Monitorização de Segurança do Azure deve ser reinstalada automaticamente dentro de alguns minutos.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Resolução de problemas da instalação do agente de monitorização no Linux
Quando resolver problemas de instalação do Agente da VM num sistema Linux, confirme que a extensão foi transferida para /var/lib/waagent/. Pode executar o comando abaixo para verificar se foi instalada:

`cat /var/log/waagent.log` 

Os outros ficheiros de registo que poderá consultar para efeitos de resolução de problemas são: 

* /var/log/mdsd.err
* /var/log/azure/

Num sistema funcional, deverá ver uma ligação ao processo mdsd na TCP 29130. Este é o syslog que comunica com o processo mdsd. Pode validar este comportamento ao executar o comando abaixo:

`netstat -plantu | grep 29130`

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>A resolução de problemas do endpoint protection não está a funcionar corretamente

O agente convidado é o processo principal de tudo o que a extensão do [Microsoft Antimalware](../security/azure-security-antimalware.md) faz. Quando o processo do agente convidado falha, o Microsoft Antimalware que é executado como um processo subordinado do agente convidado também poderá falhar.  Em cenários como os que são recomendados para verificar as seguintes opções:

- Se a VM de destino for uma imagem personalizada e o criador da VM nunca tiver instalado o agente convidado.
- Se o destino for uma VM do Linux em vez de uma VM do Windows, a instalação da versão do Windows da extensão de antimalware numa VM do Linux irá falhar. O agente convidado do Linux tem requisitos específicos em termos da versão do SO e pacotes necessários, e se esses requisitos não forem cumpridos, o agente da VM também não irá funcionar. 
- Se a VM foi criada com uma versão antiga do agente convidado. Caso tenha sido, deve ter em atenção que alguns agentes antigos não conseguirão atualizar de forma automática para a versão mais recente e isto pode levar a este problema. Utilize sempre a versão mais recente do agente convidado se criar as suas próprias imagens.
- Algum software de administração de terceiros pode desativar o agente convidado ou bloquear o acesso a determinadas localizações de ficheiros. Se tiver instalado software de terceiros na VM, certifique-se de que o agente está na lista de exclusão.
- Determinadas definições da firewall ou do Grupo de Segurança de Rede (NSG) podem bloquear o tráfego de rede para e a partir do agente convidado.
- Determinada Lista de Controlo de Acesso (ACL) poderá impedir o acesso ao disco.
- A falta de espaço em disco pode impedir o agente convidado de funcionar corretamente. 

A Interface de Utilizador de Antimalware da Microsoft está desativada por predefinição, leia [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment (Ativar a Interface de Utilizador de Antimalware da Microsoft nas VMs do Azure Resource Manager Depois da Implementação)](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) para obter mais informações sobre como ativá-lo se for necessário.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Resolução de problemas ao carregar o dashboard

Se ocorrerem problemas ao carregar o dashboard do Centro de Segurança, certifique-se de que o utilizador que regista a subscrição no Centro de Segurança (ou seja, o primeiro utilizador que abrir o Centro de Segurança com a subscrição) e o utilizador que gostaria de ativar a recolha de dados são o *Proprietário* ou o *Contribuinte* na subscrição. A partir desse momento os utilizadores com *Leitor* na subscrição também podem ver o dashboard/alertas/recomendação/política.

## <a name="contacting-microsoft-support"></a>Contactar o Suporte da Microsoft
Alguns problemas podem ser identificados através das diretrizes fornecidas neste artigo, ao passo que outros estão também documentados no [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) público do Centro de Segurança. Contudo, se tiver de resolver mais problemas, pode abrir um novo pedido de suporte através do **Portal do Azure**, conforme mostrado abaixo: 

![Suporte da Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) – Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure




<!--HONumber=Feb17_HO3-->


