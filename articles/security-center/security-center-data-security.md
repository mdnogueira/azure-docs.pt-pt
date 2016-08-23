<properties
   pageTitle="Segurança de Dados do Centro de Segurança do Azure | Microsoft Azure"
   description="Este documento explica como os dados são geridos e salvaguardados no Centro de Segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="yurid"/>

# Segurança de Dados do Centro de Segurança do Azure
Para ajudar os clientes a evitar, detetar e responder a ameaças, o Centro de Segurança do Azure recolhe e processa dados sobre os seus recursos do Azure, incluindo informações de configuração, metadados, registos de eventos, ficheiros de informação de falha de sistema e muito mais. Estamos extremamente empenhados em proteger a privacidade e segurança destes dados. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço. 

Este artigo explica como os dados são geridos e salvaguardados no Centro de Segurança do Azure.

> [AZURE.NOTE] As informações neste documento aplicam-se ao pré-lançamento do Centro de Segurança do Azure.

## Origens de Dados
O Centro de Segurança do Azure analisa os dados das seguintes origens:

- Serviços do Azure: lê informações sobre a configuração dos serviços do Azure que implementou através da comunicação com o fornecedor de recursos desses serviços.
- Tráfego de Rede: lê metadados de tráfego de rede de amostragem da infraestrutura da Microsoft, tais como porta/IP de origem/destino, tamanho do pacote e protocolo de rede.
- Soluções de Parceiros: recolhe alertas de segurança das soluções de parceiros integradas, tais como firewalls e soluções antimalware. Estes dados são armazenados no armazenamento do Centro de Segurança do Azure, atualmente localizado nos Estados Unidos.
- As suas Máquinas Virtuais: o Centro de Segurança do Azure pode recolher informações de configuração e informações sobre eventos de segurança, tais como eventos do Windows e registos de auditoria, registos do IIS, mensagens do syslog e ficheiros de informação de falha de sistema das suas máquinas virtuais com agentes de recolha de dados. Veja a secção “Gestão da Recolha de Dados” abaixo para obter detalhes adicionais.  

Além disso, as informações sobre alertas de segurança, recomendações e estado de funcionamento de segurança são armazenadas no armazenamento do Centro de Segurança do Azure, atualmente localizado nos Estados Unidos. Estas informações podem incluir informações de configuração relacionadas e eventos de segurança recolhidos das suas máquinas virtuais, conforme necessário para lhe fornecer o alerta de segurança, recomendação ou estado de funcionamento de segurança.

## Proteção de Dados
**Segregação de dados**: os dados são mantidos separados de forma lógica em cada componente em todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço. Além disso, os dados recolhidos das suas máquinas virtuais são armazenados nas suas contas de armazenamento.

**Acesso a dados**: para fornecer recomendações de segurança e investigar potenciais ameaças de segurança, o pessoal técnico da Microsoft pode aceder a informações recolhidas ou analisadas pelos serviços do Azure, incluindo ficheiros de informação de falha de sistema. Os ficheiros de informação de falha de sistema e os eventos de criação do processo podem incluir, involuntariamente, Dados do Cliente ou dados pessoais das suas máquinas virtuais. Respeitamos os [Termos de Serviço Online da Microsoft](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e a [Declaração de Privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), os quais estipulam que a Microsoft não utilizará Dados do Cliente ou derivará informações dos mesmos para qualquer fim de publicidade ou comercial semelhante. Apenas utilizamos os Dados do Cliente conforme necessário para lhe fornecer os serviços do Azure, incluindo fins compatíveis com o fornecimento desses serviços. O utilizador retém todos os direitos sobre os Dados do Cliente.

**Utilização de dados**: a Microsoft utiliza os padrões e as informações sobre ameaças presentes em vários inquilinos para melhorar as nossas capacidades de prevenção e deteção. Fazemo-lo em conformidade com os compromissos de privacidade descritos na nossa [Declaração de Privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

**Localização de dados**: é especificada uma conta de armazenamento para cada região onde as máquinas virtuais são executadas. Isto permite-lhe armazenar dados na mesma região que a máquina virtual a partir da qual os dados são recolhidos. Estes dados, incluindo ficheiros de informação de falha de sistema, serão armazenados de forma permanente na sua conta de armazenamento. Os serviços também armazenam informações sobre alertas de segurança, incluindo alertas de soluções de parceiros integradas, recomendações e o estado de funcionamento de segurança no armazenamento do Centro de Segurança do Azure, atualmente localizado nos Estados Unidos.

## Gestão da Recolha de Dados provenientes de Máquinas Virtuais

Quando opta por ativar o Centro de Segurança do Azure, a recolha de dados é ativada para cada uma das suas subscrições. Pode desativar a recolha de dados na secção “Política de Segurança” do seu Dashboard do Centro de Segurança do Azure. Quando a Recolha de Dados é ativada, o Centro de Segurança do Azure aprovisiona o Agente de Monitorização do Azure em todas as máquinas virtuais existentes suportadas e quaisquer máquinas novas criadas. A extensão Monitorização de Segurança do Azure analisa vários eventos e configurações relacionados com a segurança em rastreios de [Rastreio de Eventos para o Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Além disso, o sistema operativo irá gerar eventos do registo de eventos durante a execução da máquina. Os exemplos destes dados incluem: tipo e versão do sistema operativo, registos de sistema operativo (registos de eventos do Windows), processos em execução, nome da máquina, endereços IP, utilizador com sessão iniciada e ID do inquilino. O Agente de Monitorização do Azure lê entradas de registo de eventos e rastreios ETW e copia-as para a sua conta de armazenamento para análise. 

É especificada uma conta de armazenamento para cada região na qual tenha máquinas virtuais em execução, onde os dados recolhidos das máquinas virtuais nessa mesma região estão armazenados. Isto faz com que seja mais fácil manter os dados na mesma área geográfica para fins de privacidade e soberania dos dados. Pode configurar contas de armazenamento para cada região na secção “Política de Segurança” do seu Dashboard do Centro de Segurança do Azure.

O Agente de Monitorização do Azure também copia os ficheiros de informação de falha de sistema para a sua conta de armazenamento.  O Centro de Segurança do Azure recolhe cópias efémeras dos ficheiros de informação de falha de sistema e analisa-as para encontrar sinais de tentativas de exploração e casos em que a segurança tenha sido comprometida.  O Centro de Segurança do Azure efetua esta análise na mesma região geográfica que a conta de armazenamento e elimina as cópias efémeras quando a análise estiver concluída.

Pode desativar a recolha de dados provenientes de máquinas virtuais a qualquer momento. Por sua vez, tal irá remover quaisquer Agentes de Monitorização anteriormente instalados pelo Centro de Segurança do Azure.


## Passos seguintes

Através deste documento aprendeu como os dados são geridos e salvaguardados no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, veja:

- [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) – Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure
- [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
- [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
- [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure



<!--HONumber=Aug16_HO1-->


