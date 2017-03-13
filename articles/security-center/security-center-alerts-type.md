---
title: "Alertas de segurança por tipo no Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda-o a compreender o tipo de alertas de segurança disponíveis no Centro de Segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: b66128a2bb0c3237541ae991a98daa09deeff5d5
ms.lasthandoff: 03/07/2017


---
# <a name="security-alerts-by-type-in-azure-security-center"></a>Alertas de Segurança no Centro de Segurança do Azure
Este documento ajuda-o a compreender os vários tipos de alertas de segurança disponíveis no Centro de Segurança do Azure. Leia [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) para obter mais informações sobre como gerir alertas.

> [!NOTE]
> Para ativar as deteções avançadas, atualize para o Centro de Segurança do Azure Standard. Está disponível uma avaliação gratuita de 60 dias. Para atualizar, selecione Escalão de Preço na [Política de Segurança](security-center-policies.md). Veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) para saber mais.
>
>

## <a name="what-type-of-alerts-are-available"></a>Que tipo de alertas estão disponíveis?
O Centro de Segurança do Azure fornece vários tipos de alertas que são alinhados com as fases da cadeia de eliminação cibernética. A figura abaixo fornece alguns exemplos de vários alertas, porque estão relacionados com algumas destas fases.

![Cadeia de eliminação](./media/security-center-alerts-type/security-center-alerts-type-fig1.png)

**Alvo e Ataque**

* Ataques de RDP/SSH de entrada
* Ataques de DDoS e aplicações (parceiros WAF)
* Deteção de intrusões (parceiros NG Firewall)

**Instalar e Explorar**

* Assinaturas de software maligno conhecidas (parceiros AM)
* Tentativas de exploração e software na memória
* Execução de processos suspeitos
* Manobras evasivas para evitar a deteção
* Movimento lateral
* Reconhecimento interno
* Atividade suspeita do PowerShell

**Violação de Publicação**  

* Comunicação com um IP malicioso conhecido (filtragem, comando e controlo de dados)
* Utilização de recursos comprometidos para montar ataques adicionais (ataques de força bruta de RDP/SSH de análise da porta de saída e spam)

Os diferentes tipos de ataques estão associados a cada fase e destinam-se a subsistemas diferentes. Para lidar com ataques durante estas fases, o Centro de Segurança tem três categorias de alertas:

* Análise Comportamental de Máquinas Virtuais (VMBA)
* Análise de Rede
* Análise de Recursos

## <a name="virtual-machine-behavioral-analysis"></a>Análise comportamental de máquinas virtuais
O Centro de Segurança do Azure pode utilizar a análise comportamental para identificar recursos comprometidos com base na análise de registos de eventos de máquinas virtuais, por exemplo: Eventos de Criação de Processos, Eventos de Início de Sessão, etc.). Além disso, existe uma correlação com outros sinais para procurar provas de uma campanha ampla.

> [!NOTE]
> Para obter mais informações sobre como funcionam as capacidades de deteção do Centro de Segurança, leia [Capacidades de Deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md).
>
>

### <a name="crash-analysis"></a>Análise de falhas
A análise de memória de informação de falha de memória é um método utilizado para detetar software maligno sofisticado que é capaz de se esconder de soluções de segurança tradicionais. As várias formas de software maligno tentam reduzir a possibilidade de se detetado por produtos de software antivírus, ao nunca escrever no disco ou ao encriptar componentes de software escritas em disco. Isto faz com que o software maligno seja difícil de detetar com soluções contra software maligno tradicionais. No entanto, este software maligno pode ser detetado através da análise de memória, uma vez que este software tem de deixar rastro na memória para funcionar.

Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha. A falha pode ser provocada por software maligno, aplicações gerais ou problemas de sistema. Ao analisar a memória na informação de falha de sistema, o Centro de Segurança pode detetar técnicas utilizadas para explorar vulnerabilidades no software, aceder a dados confidenciais e persistir, de maneira sub-reptícia, numa máquina afetada. Isto é conseguido com um impacto mínimo no desempenho para anfitriões, porque a análise é efetuada pelo back-end do Centro de Segurança.

Os campos seguintes são comuns a todos os alertas de análise de informação de falha de sistema listados abaixo:

* DUMPFILE: nome do ficheiro de informação de falha de sistema
* PROCESSNAME: nome do processo de falha inesperada
* PROCESSVERSION: versão do processo de falha inesperada

### <a name="shellcode-discovered"></a>Shellcode detetado
Shellcode é o payload executado depois de o software maligno ter explorado uma vulnerabilidade de software. Este alerta indica que a análise da informação de falha de sistema detetou código executável que apresenta um comportamento normalmente tido por payloads maliciosos. Apesar de o software não malicioso poder ter este comportamento, não é característico das práticas normais de desenvolvimento de software.

Este alerta fornece o seguinte campo adicional:

* ADDRESS: a localização na memória do shellcode

Este é um exemplo deste tipo de alerta:

![Alerta de Shellcode](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Módulo de hijacking detetado
O Windows baseia-se em Dynamic Link Libraries (DLLs) para permitir que o software utilize a funcionalidade do sistema Windows comum. O Hijacking de DLL ocorre quando software maligno altera a ordem de carregamento da DLL para carregar vários payloads maliciosos na memória, podendo ser executado código arbitrário. Este alerta indica que a análise de informação de falha de sistema detetou um módulo com o mesmo nome carregado a partir de dois caminhos diferentes, sendo um dos caminhos carregados proveniente de uma localização binária comum do sistema do Windows.

Por vezes, os programadores de software legítimo alteram a ordem de carregamento da DLL por motivos não maliciosos, por exemplo, para instrumentação, para expansão do SO Windows ou para aplicações do Windows. Para ajudar a diferenciar entre alterações maliciosas e potencialmente benignas na ordem de carregamento de DLL, o Centro de segurança do Azure verifica se um módulo carregado está ou não em conformidade com um perfil suspeito. O resultado desta verificação é indicado pelo campo "SIGNATURE" do alerta e é refletido na gravidade do alerta, na descrição do alerta e nos passos de remediação do alerta. Analisar a cópia em disco do módulo de hijacking, por exemplo, verificando a assinatura digital dos ficheiros ou executando uma análise de software antivírus, pode fornecer mais informações sobre a natureza legítima ou maliciosa do módulo de hijacking.

Além dos campos comuns descritos na secção “Shellcode Detetado” acima, este alerta fornece os seguintes campos:

* SIGNATURE: indica se o módulo de hijacking está em conformidade com um perfil de comportamento suspeito
* HIJACKEDMODULE: o nome do módulo do sistema Windows hijacked
* HIJACKEDMODULEPATH: o caminho do módulo do sistema Windows hijacked
* HIJACKINGMODULEPATH: o caminho do módulo de hijacking

Este é um exemplo deste tipo de alerta:

![Alerta de hijacking do módulo](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Módulo do Windows de disfarce detetado
O software maligno pode utilizar nomes comuns dos binários do sistema Windows (por exemplo, SVCHOST.EXE) ou módulos (por exemplo, NTDLL.DLL) para se infiltrar e ocultar dos administradores de sistema a natureza do software malicioso. Este alerta indica que a análise de informação de falha de sistema detetou que o ficheiro de informação de falha de sistema contém módulos que utilizam nomes de módulos do sistema Windows, mas que não satisfaz outros critérios característicos dos módulos do Windows. Analisar a cópia de disco do módulo de disfarce pode fornecer mais informações sobre a natureza legítima ou maliciosa deste módulo. A análise pode incluir:

* Confirmar se o ficheiro em causa é enviado como parte de um pacote de software legítimo
* Verificar a assinatura digital do ficheiro
* Executar uma análise de software antivírus no ficheiro

Além dos campos comuns descritos na secção “Shellcode Detetados” acima, este alerta fornece os seguintes campos adicionais:

* DETAILS: descreve se os metadados de módulos são válidos e se o módulo foi carregado a partir de um caminho de sistema.
* NAME: o nome do módulo do Windows de disfarce
* PATH: o caminho para o módulo do Windows de disfarce.

Este alerta também extrai e apresenta determinados campos, do cabeçalho de PE do módulo, como "CHECKSUM" e "TIMESTAMP". Estes campos são apresentados apenas se os campos estiverem presentes no módulo. Veja o artigo [Microsoft PE e Especificação de COFF](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) para obter detalhes sobre estes campos.

Este é um exemplo deste tipo de alerta:

![Alerta de Windows de disfarce](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Binário de sistema modificado detetado
O software maligno pode modificar os binários do sistema principal para aceder a dados de maneira dissimulada ou persistir sub-reticiamente num sistema comprometido. Este alerta indica que a análise da informação de falha de sistema detetou que foram modificados binários principais do SO Windows na memória ou no disco.
Os programadores de software legítimo modificam ocasionalmente módulos do sistema na memória por razões não maliciosas, por exemplo, para desvios ou para compatibilidade de aplicações. Para ajudar a diferenciar entre módulos maliciosos e potencialmente legítimos, o Centro de Segurança do Azure verifica se o módulo modificado está ou não em conformidade com um perfil suspeito. O resultado desta verificação é refletido na gravidade do alerta, na descrição do alerta e nos passos de remediação do alerta.

Além dos campos comuns descritos na secção “Shellcode Detetados” acima, este alerta fornece os seguintes campos adicionais:

* MODULENAME: nome do binário de sistema modificado
* MODULEVERSION: versão do binário de sistema modificado

Este é um exemplo deste tipo de alerta:

![Alerta de binário do sistema](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Processos suspeitos executados
O Centro de Segurança identifica um suspeito processo em execução na máquina virtual de destino e aciona um alerta. A deteção não procura o nome específico, mas sim o respetivo parâmetro. Por conseguinte, mesmo que o atacante altere o executável, o Centro de Segurança continua a conseguir detetá-lo.

Este é um exemplo deste tipo de alerta:

![Alerta de processos suspeitos](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domain-accounts-queried"></a>Várias contas de domínio consultadas
O Centro de Segurança pode detetar várias tentativas para consultar contas de domínio, o que é normalmente efetuado pelos atacantes durante o reconhecimento de redes. Os atacantes podem aproveitar esta técnica para consultar o domínio e identificar quem são os utilizadores, quais são as contas de administrador de domínio, que computadores são Controladores de Domínio e também uma potencial relação de confiança de domínio com outros domínios.

Este é um exemplo deste tipo de alerta:

![Alerta de conta de vários domínios](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

## <a name="network-analysis"></a>Análise de rede
A deteção de ameaças de rede do Centro de Segurança funciona através da recolha automática de informações de segurança a partir do tráfego do Azure IPFIX (Internet Protocol Flow Information Export). Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças.

### <a name="suspicious-outgoing-traffic-detected"></a>Tráfego de saída suspeito detetado
Os dispositivos de rede podem ser detetados e pode ser criado um perfil dos mesmos de uma forma muito semelhante aos outros tipos de sistemas. Normalmente, os atacantes começam por uma análise de portas/varrimento de portas. No exemplo abaixo, existe tráfego SSH suspeito a partir de uma VM que pode estar a realizar um ataque de força bruta SSH ou de varrimento da portas contra um recurso externo.

![Alerta de tráfego de saída suspeito](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Este alerta dá-informações que lhe permitem identificar o recurso que foi utilizado para iniciar este ataque, a máquina comprometida, a hora da deteção, o protocolo e a porta que foi utilizada. Este painel também fornece uma lista dos passos de remediação que podem ser seguidos para atenuar este problema.

### <a name="network-communication-with-a-malicious-machine"></a>Comunicação de rede com uma máquina maliciosa
Ao tirar partido dos feeds de informações sobre ameaças da Microsoft, o Centro de Segurança do Azure pode detetar máquinas comprometidas que estão a comunicar com endereços IP maliciosos, em muitos casos, um centro de comando e controlo. Neste caso, o Centro de Segurança do Azure detetou que a comunicação foi efetuada com software maligno Pony Loader (também conhecido como [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![alerta de comunicação de rede](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

Este alerta dá-informações que lhe permitem identificar o recurso que foi utilizado para iniciar este ataque, o recurso atacado, o IP da vítima, o IP do atacante e a hora da deteção.

> [!NOTE]
> Os endereços IP em direto foram removidos desta captura de ecrã por motivos de privacidade.
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>Possível ataque denial-of-service de saída detetado
O tráfego de rede anómalo proveniente de uma máquina virtual pode fazer com que o Centro de Segurança acione um potencial tipo de ataque denial-of-service.

Este é um exemplo deste tipo de alerta:

![DOS de saída](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Análise de recursos
A análise de recursos do Centro de Segurança concentra-se em serviços de PaaS, como a integração com a funcionalidade [Deteção de Ameaças da Base de Dados SQL do Azure](../sql-database/sql-database-threat-detection.md). Com base nos resultados da análise destas áreas, o Centro de Segurança aciona um alerta relacionados com um recurso.

### <a name="potential-sql-injection"></a>Potencial injeção de SQL
A injeção de SQL é um ataque no qual é inserido código malicioso nas cadeias que são transmitidas posteriormente para uma instância do SQL Server para análise e execução. Qualquer procedimento que crie instruções SQL deve ser revisto em termos de vulnerabilidades de injeção, uma vez que o SQL Server irá executar todas as consultas sintaticamente válidas que receber. A Deteção de ameaça do SQL Server utiliza machine learning, análise comportamental e deteção de anomalias para determinar eventos suspeitos que poderão estar a ocorrer nas suas Bases de Dados SQL do Azure. Por exemplo:

* Tentativa de acesso à base de dados por um antigo funcionário
* Ataques de injeção de SQL
* Acesso invulgar à base de dados de produção de um utilizador doméstico

![Potencial alerta de injeção de SQL](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

Este alerta disponibiliza informações que lhe permitem identificar o recurso atacado, a hora da deteção e o estado do ataque, além de lhe fornecer uma ligação para passos de investigação adicionais.

### <a name="vulnerability-to-sql-injection"></a>Vulnerabilidade a injeção de SQL
Este alerta é acionado quando é detetado um erro de aplicação numa base de dados que pode indicar uma possível vulnerabilidade a ataques de injeção de SQL.

![Potencial alerta de injeção de SQL](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Acesso invulgar a partir de uma localização desconhecida
Este alerta é acionado quando é detetado um acesso a partir de um endereço IP desconhecido no servidor, que não foi visualizado no último período.

![Alerta de acesso invulgar](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="see-also"></a>Consultar também
Neste documento, tive conhecimento dos vários tipos de alertas de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Lidar com Incidentes de Segurança no Centro de Segurança do Azure](security-center-incident.md)
* [Capacidades de Deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

