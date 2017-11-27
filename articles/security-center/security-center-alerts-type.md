---
title: "Alertas de segurança por tipo no Centro de Segurança do Azure | Microsoft Docs"
description: "Este artigo explica os vários tipos de alertas de segurança disponíveis no Centro de Segurança do Azure."
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
ms.date: 11/22/2017
ms.author: yurid
ms.openlocfilehash: 829657664cf1e37b22d57c62614300a205b5e91c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Compreender os alertas de segurança no Centro de Segurança do Azure
Este artigo ajuda-o a compreender os vários tipos de alertas de segurança e informações relacionadas que estão disponíveis no Centro de Segurança do Azure. Para obter mais informações sobre como gerir alertas e incidentes, veja [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md).

Para configurar as deteções avançadas, atualize para o Centro de Segurança do Azure Standard. Está disponível uma avaliação gratuita de 60 dias. Para atualizar, selecione **Escalão de Preço** na [política de segurança](security-center-policies.md). Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> O Centro de Segurança lançou uma pré-visualização limitada de um novo conjunto de deteções que tiram partido dos registos de auditoria, um framework de auditoria comum, para detetar comportamentos maliciosos em computadores Linux. Envie um e-mail para a [Microsoft](mailto:ASC_linuxdetections@microsoft.com) com os seus IDs de subscrição para aderir à pré-visualização.

## <a name="what-type-of-alerts-are-available"></a>Que tipo de alertas estão disponíveis?
O Centro de Segurança do Azure utiliza uma variedade de [capacidades de deteção](security-center-detection-capabilities.md) para alertar os clientes para possíveis ataques direcionados aos respetivos ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. As informações incluídas num alerta variam de acordo com o tipo de análise usado para detetar a ameaça. Os incidentes também podem conter informações contextuais adicionais que podem ser úteis ao investigar uma ameaça.  Este artigo fornece informações sobre os seguintes tipos de alerta:

* Análise Comportamental de Máquinas Virtuais (VMBA)
* Análise de Rede
* Análise de Recursos
* Informações Contextuais

## <a name="virtual-machine-behavioral-analysis"></a>Análise comportamental de máquinas virtuais
O Centro de Segurança do Azure pode utilizar a análise comportamental para identificar recursos comprometidos com base na análise de registos de eventos de máquinas virtuais. Por exemplo, Eventos de Criação de Processos e Eventos de Início de Sessão. Além disso, existe uma correlação com outros sinais para procurar provas de uma campanha ampla.

> [!NOTE]
> Para obter mais informações sobre como funcionam as capacidades de deteção do Centro de Segurança, veja [Capacidades de deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md).
>

### <a name="crash-analysis"></a>Análise de falhas
A análise de memória de informação de falha de memória é um método utilizado para detetar software maligno sofisticado que é capaz de se esconder de soluções de segurança tradicionais. As várias formas de software maligno tentam reduzir a possibilidade de ser detetado por produtos de software antivírus, ao nunca escrever no disco ou ao encriptar componentes de software escritos em disco. Esta técnica faz com que o software maligno seja difícil de detetar com soluções contra software maligno tradicionais. No entanto, este tipo de software maligno pode ser detetado através da análise de memória, uma vez que o software maligno tem de deixar rastreios na memória para funcionar.

Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha. A falha pode ser provocada por software maligno, aplicações gerais ou problemas de sistema. Ao analisar a memória na informação de falha de sistema, o Centro de Segurança pode detetar técnicas utilizadas para explorar vulnerabilidades no software, aceder a dados confidenciais e persistir, de maneira sub-reptícia, numa máquina afetada. Isto é conseguido com um impacto mínimo no desempenho nos anfitriões, porque a análise é realizada pelo back-end do Centro de Segurança.

Os campos seguintes são comuns aos exemplos de alertas de informação de falha de sistema apresentados mais à frente neste artigo:

* DUMPFILE: nome do ficheiro de informação de falha de sistema.
* PROCESSNAME: nome do processo de falha inesperada.
* PROCESSVERSION: versão do processo de falha inesperada.

### <a name="code-injection-discovered"></a>Injeção de Código Detetada
A injeção de código é a inserção de módulos executáveis em processos ou threads em execução.  Esta técnica é utilizada por software maligno para aceder a dados, ocultar ou impedir a sua remoção (por exemplo, persistência). Este alerta indica que está presente nas informações de falha de sistema um módulo injetado. Os programadores de software legítimo efetuam ocasionalmente a injeção de código por motivos não maliciosos, por exemplo, para modificar ou expandir uma aplicação ou componente do sistema operativo existente.  Para ajudar a diferenciar entre módulos maliciosos e não maliciosos injetados, o Centro de Segurança verifica se o módulo inserido está ou não em conformidade com um perfil de comportamento suspeito. O resultado desta verificação é indicado pelo campo "SIGNATURE" do alerta e é refletido na gravidade do alerta, na descrição do alerta e nos passos de remediação do alerta. 

Este alerta fornece os campos adicionais seguintes:

- ADDRESS: a localização na memória do shellcode injetado
- IMAGENAME: o nome do módulo injetado. Tenha em atenção que isto pode estar em branco se o nome da imagem não for fornecido na imagem.
- SIGNATURE: indica se o módulo injetado está em conformidade com um perfil de comportamento suspeito. 

A tabela abaixo mostra exemplos de resultados e a respetiva descrição:

| Valor de assinatura                      | Descrição                                                                                                       |
|--------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Exploração de carregador reflexivo suspeito | Este comportamento suspeito está frequentemente relacionado com o carregamento de código injetado, independentemente do carregador do sistema operativo |
| Exploração injetada suspeita          | Significa malícia frequentemente relacionada com a injeção de código na memória                                       |
| Exploração de injeção suspeita         | Significa malícia frequentemente relacionada com a utilização de código injetado na memória                                   |
| Exploração de depuração injetada suspeita | Significa malícia frequentemente relacionada com a deteção ou a neutralização de um depurador                         |
| Exploração remota injetada suspeita   | Significa malícia frequentemente relacionada com cenários de comando e controlo (C2)                                 |

Segue um exemplo deste tipo de alerta:

![Alerta de injeção de código](./media/security-center-alerts-type/security-center-alerts-type-fig21.png)

### <a name="suspicious-code-segment"></a>Segmento de código suspeito
O segmento de código suspeito indica que foi alocado um segmento de código através de métodos não padrão, como injeção reflexiva e “hollowing” de processos.  Além disso, este alerta processa características adicionais do segmento de código, para dar contexto relativamente às capacidades e comportamentos do segmento reportado.

Este alerta fornece os campos adicionais seguintes:

- ADDRESS: a localização na memória do shellcode injetado
- SIZE: o tamanho do segmento de código suspeito
- STRINGSIGNATURES: este campo lista as capacidades das APIs cujos nomes de funções estão contidos dentro do segmento de código. Os exemplos de capacidades podem incluir:
    - Descritores de Secção de Imagem, Execução de Código Dinâmica para x64, Capacidade de alocação de memória e carregador, Capacidade de injeção de código remota, Capacidade de controlo de Hijack, Ler variáveis de ambiente, Ler memória de processos arbitrários, Consultar ou modificar tokens de privilégios, Comunicação de rede HTTP/HTTPS e Comunicação de socket de Rede.
- IMAGEDETECTED: este campo indica se foi injetada uma imagem de PE no processo em que foi detetado o segmento de código suspeito e em que endereço é que o módulo injetado começa.
- SHELLCODE: este código indica a presença de comportamentos normalmente utilizados pelos payloads maliciosos para adquirir acesso a funções de segurança sensíveis de sistema operativo adicionais. 

Segue um exemplo deste tipo de alerta:

![Alerta de segmento de código suspeito](./media/security-center-alerts-type/security-center-alerts-type-fig22.png)

### <a name="shellcode-discovered"></a>Shellcode detetado
Shellcode é o payload executado depois de o software maligno explorar uma vulnerabilidade de software. Este alerta indica que a análise da informação de falha de sistema detetou código executável que apresenta um comportamento que é normalmente tido por payloads maliciosos. Apesar de o software não malicioso poder ter este comportamento, não é característico das práticas normais de desenvolvimento de software.

O exemplo de alerta Shellcode fornece o seguinte campo adicional:

* ADDRESS: a localização na memória do shellcode.

Segue um exemplo deste tipo de alerta:

![Alerta de Shellcode](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Módulo de hijacking detetado
O Windows utiliza DLLs (dynamic-link libraries) para permitir que o software utilize a funcionalidade do sistema Windows comum. O Hijacking de DLL ocorre quando software maligno altera a ordem de carregamento da DLL para carregar vários payloads maliciosos na memória, podendo ser executado código arbitrário. Este alerta indica que a análise de informação de falha de sistema detetou um módulo com o mesmo nome carregado a partir de dois caminhos diferentes. Um dos caminhos carregados é proveniente de uma localização binária comum do sistema do Windows.

Por vezes, os programadores de software legítimo alteram a ordem de carregamento do DLL por motivos não maliciosos, por exemplo, para instrumentação, para expansão do SO Windows ou para expansão de uma aplicação do Windows. Para ajudar a fazer a distinção entre alterações maliciosas e potencialmente benignas na ordem de carregamento do DLL, o Centro de segurança do Azure verifica se um módulo carregado está em conformidade com um perfil suspeito. O resultado desta verificação é indicado pelo campo "SIGNATURE" do alerta e é refletido na gravidade do alerta, na descrição do alerta e nos passos de remediação do alerta. Para investigar se o módulo é legítimo ou malicioso, analise a cópia em disco do módulo de hijacking. Por exemplo, pode verificar a assinatura digital do ficheiro ou executar uma análise de software antivírus.

Além dos campos comuns descritos na secção “Shellcode detetado” anterior, este alerta fornece os seguintes campos:

* SIGNATURE: indica se o módulo de hijacking está em conformidade com um perfil de comportamento suspeito.
* HIJACKEDMODULE: o nome do módulo do sistema Windows hijacked.
* HIJACKEDMODULEPATH: o caminho do módulo do sistema Windows hijacked.
* HIJACKINGMODULEPATH: o caminho do módulo de hijacking.

Segue um exemplo deste tipo de alerta:

![Alerta de hijacking do módulo](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Módulo do Windows de disfarce detetado
O software maligno pode utilizar nomes comuns dos binários do sistema Windows (por exemplo, SVCHOST.EXE) ou módulos (por exemplo, NTDLL.DLL) para se *infiltrar* e ocultar dos administradores de sistema a natureza do software malicioso. Este alerta indica que a análise de informação de falha de sistema detetou que o ficheiro de informação de falha de sistema contém módulos que utilizam nomes de módulos do sistema Windows, mas que não satisfazem outros critérios característicos dos módulos do Windows. Analisar a cópia em disco do módulo de disfarce pode fornecer mais informações sobre a natureza legítima ou maliciosa deste módulo. A análise pode incluir:

* Confirmação de que o ficheiro em causa é enviado como parte de um pacote de software legítimo.
* Verificação da assinatura digital do ficheiro.
* Execução de uma análise de software antivírus no ficheiro.

Além dos campos comuns descritos na secção “Shellcode detetado” anterior, este alerta fornece os seguintes campos adicionais:

* DETAILS: descreve se os metadados do módulo são válidos e se o módulo foi carregado a partir de um caminho de sistema.
* NAME: o nome do módulo do Windows de disfarce.
* PATH: o caminho para o módulo do Windows de disfarce.

Este alerta também extrai e apresenta determinados campos do cabeçalho de PE do módulo, como "CHECKSUM" e "TIMESTAMP". Estes campos são apresentados apenas se os campos estiverem presentes no módulo. Veja o artigo [Microsoft PE e Especificação de COFF](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) para obter detalhes sobre estes campos.

Segue um exemplo deste tipo de alerta:

![Alerta de Windows de disfarce](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Binário de sistema modificado detetado
O software maligno pode modificar os binários do sistema principal para aceder a dados de maneira dissimulada ou persistir sub-reticiamente num sistema comprometido. Este alerta indica que a análise da informação de falha de sistema detetou que foram modificados binários principais do SO Windows na memória ou no disco.

Os programadores de software legítimo modificam ocasionalmente módulos do sistema na memória por razões não maliciosas, por exemplo, para desvios ou para compatibilidade de aplicações. Para ajudar a fazer a distinção entre módulos maliciosos e potencialmente legítimos, o Centro de Segurança do Azure verifica se o módulo modificado está em conformidade com um perfil suspeito. O resultado desta verificação é refletido na gravidade do alerta, na descrição do alerta e nos passos de remediação do alerta.

Além dos campos comuns descritos na secção “Shellcode detetado” anterior, este alerta fornece os seguintes campos adicionais:

* MODULENAME: nome do binário de sistema modificado.
* MODULEVERSION: versão do binário de sistema modificado.

Segue um exemplo deste tipo de alerta:

![Alerta de binário do sistema](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Processos suspeitos executados
O Centro de Segurança identifica um processo suspeito em execução na máquina virtual de destino e, em seguida, aciona um alerta. A deteção não procura o nome específico, mas sim o parâmetro do ficheiro executável. Por conseguinte, mesmo que o atacante mude o nome do executável, o Centro de Segurança continua a conseguir detetar o processo suspeito.

Segue um exemplo deste tipo de alerta:

![Alerta de processos suspeitos](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domains-accounts-queried"></a>Várias contas de domínios consultadas
O Centro de Segurança pode detetar várias tentativas para consultar contas de domínio do Active Directory, o que é normalmente realizado pelos atacantes durante o reconhecimento de redes. Os atacantes podem aproveitar esta técnica para consultar o domínio e identificar os utilizadores, identificar as contas de administrador de domínio, identificar os computadores que são controladores de domínio e também identificar uma potencial relação de confiança do domínio com outros domínios.

Segue um exemplo deste tipo de alerta:

![Alerta de conta de vários domínios](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

### <a name="local-administrators-group-members-were-enumerated"></a>Foram enumerados membros do grupo de Administradores Locais

O Centro de Segurança vai acionar um alerta quando o evento de segurança 4798, no Windows Server 2016 e Windows 10, está acionado. Isto acontece quando são enumerados grupos de administradores locais, que é algo normalmente efetuado pelos atacantes durante o reconhecimento de rede. Os atacantes podem tirar partido desta técnica para consultar a identidade dos utilizadores com privilégios administrativos.

Segue um exemplo deste tipo de alerta:

![Administrador local](./media/security-center-alerts-type/security-center-alerts-type-fig14-new.png)

### <a name="anomalous-mix-of-upper-and-lower-case-characters"></a>Mistura anómala de carateres minúsculos e maiúsculos

O Centro de Segurança irá acionar um alerta quando detetar a utilização de uma combinação de carateres minúsculos e maiúsculos na linha de comandos. Alguns atacantes poderão utilizar esta técnica para ocultar a regra de maiúsculas e minúsculas ou a máquina baseada em hash.

Segue um exemplo deste tipo de alerta:

![Mistura anómala](./media/security-center-alerts-type/security-center-alerts-type-fig15-new.png)

### <a name="suspected-kerberos-golden-ticket-attack"></a>Ataque de Pedido Dourado Kerberos suspeito

Uma chave [krbtgt](https://technet.microsoft.com/library/dn745899.aspx) comprometida pode ser utilizada por um atacante para criar o "Pedido Dourado" Kerberos, permitindo que o atacante represente qualquer utilizador que desejar. O Centro de Segurança vai acionar um alerta quando detetar este tipo de atividade.

> [!NOTE] 
> Para mais informações sobre o Pedido Dourado Kerberos, leia [Windows 10 credential theft mitigation guide (Guia de atenuação de roubo de credenciais do Windows 10)](http://download.microsoft.com/download/C/1/4/C14579CA-E564-4743-8B51-61C0882662AC/Windows%2010%20credential%20theft%20mitigation%20guide.docx).

Segue um exemplo deste tipo de alerta:

![Pedido dourado](./media/security-center-alerts-type/security-center-alerts-type-fig16-new.png)

### <a name="suspicious-account-created"></a>Conta suspeita criada

O Centro de Segurança irá acionar um alerta quando for criada uma conta semelhante a uma conta de privilégios administrativos incorporada existente. Esta técnica pode ser utilizada por atacantes para criar uma conta de adesão, para evitar serem detetados por verificação humana.
 
Segue um exemplo deste tipo de alerta:

![Conta suspeita](./media/security-center-alerts-type/security-center-alerts-type-fig17-new.png)

### <a name="suspicious-firewall-rule-created"></a>Regra de Firewall suspeita criada

Os atacantes poderão tentar contornar a segurança do anfitrião ao criar regras personalizadas da firewall para permitir que aplicações maliciosas comuniquem com o comando e o controlo, ou para lançar ataques através da rede pelo anfitrião comprometido. O Centro de Segurança irá acionar um alerta quando detetar que foi criada uma nova regra de firewall de um ficheiro executável numa localização suspeita.
 
Segue um exemplo deste tipo de alerta:

![Regra de firewall](./media/security-center-alerts-type/security-center-alerts-type-fig18-new.png)

### <a name="suspicious-combination-of-hta-and-powershell"></a>Combinação suspeita de HTA e PowerShell

O Centro de Segurança irá acionar um alerta quando detetar que um Anfitrião de Aplicação HTML (HTA) da Microsoft está a iniciar comandos do PowerShell. Esta é uma técnica utilizada pelos atacantes para iniciar scripts maliciosos do PowerShell.
 
Segue um exemplo deste tipo de alerta:

![HTA e PS](./media/security-center-alerts-type/security-center-alerts-type-fig19-new.png)


## <a name="network-analysis"></a>Análise de rede
A deteção de ameaças de rede do Centro de Segurança funciona através da recolha automática de informações de segurança a partir do tráfego do Azure IPFIX (Internet Protocol Flow Information Export). Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças.

### <a name="suspicious-outgoing-traffic-detected"></a>Tráfego de saída suspeito detetado
Os dispositivos de rede podem ser detetados e pode ser criado um perfil dos mesmos de uma forma muito semelhante aos outros tipos de sistemas. Normalmente, os atacantes começam por uma análise de portas ou varrimento de portas. No exemplo seguinte, existe tráfego SSH (Secure Shell) suspeito a partir de uma VM. Neste cenário, é possível um ataque de força bruta SSH ou de varrimento de portas contra um recurso externo.

![Alerta de tráfego de saída suspeito](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Este alerta dá informações que lhe permitem identificar o recurso que foi utilizado para iniciar este ataque. Este alerta fornece também informações para identificar a máquina comprometida, a hora da deteção, para além do protocolo e da porta que foi utilizada. Esta página também apresenta uma lista dos passos de remediação que podem ser seguidos para atenuar este problema.

### <a name="network-communication-with-a-malicious-machine"></a>Comunicação de rede com uma máquina maliciosa
Ao tirar partido dos feeds de informações sobre ameaças da Microsoft, o Centro de Segurança do Azure pode detetar máquinas comprometidas que comunicam com endereços IP maliciosos. Em muitos casos, o endereço malicioso é um centro de comando e controlo. Neste caso, o Centro de Segurança detetou que a comunicação foi realizada através de software maligno Pony Loader (também conhecido como [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![alerta de comunicação de rede](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

Este alerta dá informações que lhe permitem identificar o recurso que foi utilizado para iniciar este ataque, o recurso atacado, o IP da vítima, o IP do atacante e a hora da deteção.

> [!NOTE]
> Os endereços IP em direto foram removidos desta captura de ecrã por motivos de privacidade.
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>Possível ataque denial-of-service de saída detetado
O tráfego de rede anómalo proveniente de uma máquina virtual pode fazer com que o Centro de Segurança acione um potencial tipo de ataque denial-of-service.

Segue um exemplo deste tipo de alerta:

![DOS de saída](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Análise de recursos
A análise de recursos do Centro de Segurança concentra-se em serviços de PaaS (plataforma como serviço), como a integração com a funcionalidade [Deteção de ameaças da Base de Dados SQL do Azure](../sql-database/sql-database-threat-detection.md). Com base nos resultados da análise destas áreas, o Centro de Segurança aciona um alerta relacionado com um recurso.

### <a name="potential-sql-injection"></a>Potencial injeção de SQL
A injeção de SQL é um ataque no qual é inserido código malicioso nas cadeias que são transmitidas posteriormente para uma instância do SQL Server para análise e execução. Uma vez que o SQL Server executa todas as consultas sintaticamente válidas que recebe, qualquer procedimento que crie instruções SQL deve ser revisto em termos de vulnerabilidades de injeção. A Deteção de Ameaças SQL utiliza machine learning, análise comportamental e deteção de anomalias para determinar eventos suspeitos que possam estar a ocorrer nas suas bases de dados SQL do Azure. Por exemplo:

* Tentativa de acesso à base de dados por um antigo funcionário
* Ataques de injeção de SQL
* Acesso invulgar a uma base de dados de produção de um utilizador doméstico

![Potencial alerta de injeção de SQL](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

As informações neste alerta podem ser utilizadas para identificar o recurso atacado, a hora da deteção e o estado do ataque. Fornece também uma hiperligação para passos de investigação adicionais.

### <a name="vulnerability-to-sql-injection"></a>Vulnerabilidade a injeção de SQL
Este alerta é acionado quando é detetado um erro de aplicação numa base de dados. Este alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL.

![Potencial alerta de injeção de SQL](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Acesso invulgar a partir de uma localização desconhecida
Este alerta é acionado quando é detetado um evento de acesso a partir de um endereço IP desconhecido no servidor, que não foi visualizado no último período.

![Alerta de acesso invulgar](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="contextual-information"></a>Informações contextuais
Durante uma investigação, os analistas precisam de contexto extra para chegar a um veredito sobre a natureza da ameaça e como resolvê-la.  Por exemplo, foi detetada uma anomalia na rede, mas sem compreender o que está a acontecer na rede ou ao recurso afetado, é muito difícil entender as ações que devem ser tomadas. Para ajudar nisso, um Incidente de Segurança pode incluir artefactos, eventos relacionados e informações que podem ajudar o investigador. A disponibilidade de informações adicionais varia com base no tipo de ameaça detetada e a configuração do seu ambiente, e não está disponível para todos os Incidentes de Segurança.

Se estiverem disponíveis informações adicionais, estas serão mostradas no Incidente de Segurança abaixo da lista de alertas. Podem estar contidas informações como:

- Eventos de limpeza de registos
- Dispositivo PNP ligado a partir de um dispositivo desconhecido
- Alertas não acionáveis 

![Alerta de acesso invulgar](./media/security-center-alerts-type/security-center-alerts-type-fig20.png) 


## <a name="see-also"></a>Consultar também
Neste artigo, obteve informações sobre os vários tipos de alertas de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Lidar com incidentes de segurança no Centro de Segurança do Azure](security-center-incident.md)
* [Capacidades de deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md): encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre mensagens do blogue acerca da segurança e conformidade do Azure.
