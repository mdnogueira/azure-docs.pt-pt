<properties
   pageTitle="Gerir e responder a alertas de segurança no Centro de Segurança do Azure | Microsoft Azure"
   description="Este documento ajuda-o a utilizar as capacidades de Centro de Segurança do Azure para gerir e responder a alertas de segurança."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/07/2016"
   ms.author="yurid"/>

# Gerir e responder a alertas de segurança no Centro de Segurança do Azure
Este documento ajuda-o a utilizar o Centro de Segurança do Azure para gerir e responder a alertas de segurança.

## O que são alertas de segurança?
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque. O Centro de segurança do Azure também agrega alertas alinhados com padrões de cadeia de ataque em [Incidentes](security-center-incident.md). 

> [AZURE.NOTE] Para obter mais informações sobre como funcionam as capacidades de deteção do Centro de Segurança, leia [Capacidades de Deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md).


## Gerir alertas de segurança

Pode rever os alertas atuais ao observar o mosaico **Alertas de segurança**. Abra o Portal do Azure e siga os passos abaixo para ver mais detalhes sobre cada alerta:

1. No dashboard do Centro de Segurança, verá o mosaico **Alertas de segurança**.

    ![Mosaico Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2.  Clique no mosaico para abrir o painel **Alertas de segurança** que contém mais detalhes sobre os alertas como é mostrado abaixo.

    ![O painel Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Na parte inferior deste painel encontram-se os detalhes de cada alerta. Para ordenar, clique na coluna pela qual pretende ordenar. A definição para cada coluna é indicada abaixo:

- **Alerta**: uma explicação breve do alerta.
- **Contagem**: uma lista de todos os alertas deste tipo específico que foram detetados num dia específico.
- **Detetado por**: o serviço que foi responsável por ter acionado o alerta.
- **Data**: a data em que o evento ocorreu.
- **Estado**: o estado atual para esse alerta. Existem dois tipos de estados:
    - **Ativo**: o alerta de segurança foi detetado.
    - **Dispensado**: o alerta de segurança foi dispensado pelo utilizador. Este estado é normalmente utilizado para os alertas que tenham sido investigados, mas que foram mitigados ou considerados como não sendo um ataque real.

- **Gravidade**: o nível de gravidade, que pode ser alta, média ou baixa.

### Filtragem de alertas

Pode filtrar os alertas com base na data, no estado e na gravidade. A filtragem de alertas pode ser útil para cenários onde necessita de limitar o âmbito dos alertas de segurança mostrados. Por exemplo, pode pretender resolver alertas de segurança que ocorreram nas últimas 24 horas, porque está a investigar uma potencial violação no sistema.

1. Clique em **Filtrar** no painel **Alertas de Segurança**. O painel **Filtro** abre-se e o utilizador seleciona os valores de data, de estado e de gravidade que pretende ver.

    ![Filtragem de alertas no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-ga.png)

2.  Depois de investigar um alerta de segurança, pode aperceber-se de que se trata de um falso positivo para o seu ambiente ou que indica um comportamento esperado para um recurso específico. Seja como for, se determinar que um alerta de segurança não é aplicável, pode dispensá-lo e, em seguida, filtrá-lo da sua vista. Existem duas formas de dispensar um alerta de segurança. Clique com o botão direito do rato num alerta e selecione **Dispensar** ou coloque o cursor sobre um item, clique nas reticências que aparecem à direita e selecione **Dispensar**. Pode ver os alertas de segurança dispensados ao clicar no **Filtro** e selecionar **Dispensados**.

    ![Dispensar alertas no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4-ga.png)

### Responder a alertas de segurança

Selecione um alerta de segurança para obter mais informações sobre o(s) evento(s) que acionaram o alerta e quais os passos (se existirem) necessários para remediar um ataque. Os alertas de segurança estão agrupados por tipo e data. Ao clicar num alerta de segurança abre-se um painel que contém uma lista dos alertas agrupados.

![Responder a alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Neste caso, os alertas que foram acionados referem-se a atividade suspeita do protocolo RDP (Remote Desktop Protocol). A primeira coluna mostra quais os recursos que foram atacados, a segunda mostra o número de vezes que o recurso foi atacado, a terceira mostra a hora do ataque, a quarta mostra o estado do alerta e a quinta mostra a gravidade do ataque. Depois de rever estas informações, clique no recurso que foi atacado e será apresentado um novo painel.

![Sugestões para o que fazer sobre alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

No campo **Descrição** deste painel irá encontrar mais detalhes sobre este evento. Estes detalhes adicionais facultam informações aprofundadas sobre o que acionou o alerta de segurança, o recurso de destino, quando aplicável, o endereço IP de origem e as recomendações sobre como remediar.  Em certos casos, o endereço IP de origem estará vazio (não disponível) porque nem todos os registos de eventos de segurança do Windows incluem o endereço IP.

> [AZURE.NOTE] A remediação sugerida pelo Centro de Segurança irá variar de acordo com o alerta de segurança. Em certos casos, poderá ter de utilizar outras capacidades do Azure para implementar a remediação recomendada. Por exemplo, a remediação para este ataque é colocar na lista de proibições o endereço IP que está a gerar este ataque ao utilizar um [ACL de rede](../virtual-network/virtual-networks-acl.md) ou uma regra de [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md).

## Alertas de segurança por tipo
Os mesmos passos utilizados para aceder ao alerta de atividade RDP suspeita podem ser utilizados para aceder a outro tipo de alertas. Aqui estão outros exemplos de alertas que pode ver nos alertas do Centro de Segurança:

### Potencial Injeção de SQL
A injeção de SQL é um ataque no qual é inserido código malicioso nas cadeias que são transmitidas posteriormente para uma instância do SQL Server para análise e execução. Qualquer procedimento que constrói instruções SQL deve ser revisto quanto às vulnerabilidades de injeção, uma vez que o SQL Server irá executar todas as consultas sintaticamente válidas que receber. 

![Alerta de injeção de SQL](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9.png) 

Este alerta disponibiliza informações que lhe permitem identificar o recurso atacado, a hora da deteção e o estado do ataque, além de lhe fornecer uma ligação para passos de investigação adicionais.

### Tráfego de saída suspeito detetado

Os dispositivos de rede podem ser detetados e pode ser criado um perfil dos mesmos de uma forma muito semelhante aos outros tipos de sistemas. Normalmente, os atacantes começam por uma análise de portas/varrimento de portas. No exemplo abaixo, existe tráfego SSH suspeito a partir de uma VM que pode estar a realizar um ataque de força bruta SSH ou de varrimento da portas contra um recurso externo. 

![Alerta de tráfego de saída suspeito](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig-10.png)

Este alerta dá-informações que lhe permitem identificar o recurso que foi utilizado para iniciar este ataque, a máquina comprometida, a hora da deteção, o protocolo e a porta que foi utilizada. Este painel também fornece uma lista dos passos de remediação que podem ser seguidos para atenuar este problema.

### Comunicação de rede com uma máquina maliciosa
 
Ao tirar partido dos feeds de informações sobre ameaças da Microsoft, o Centro de Segurança do Azure pode detetar máquinas comprometidas que estão a comunicar com endereços IP maliciosos, em muitos casos, um centro de comando e controlo. Neste caso, o Centro de Segurança do Azure detetou que a comunicação foi efetuada com software maligno Pony Loader (também conhecido como [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![Comunicação de rede com um alerta de máquina maliciosa](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9-ga.png)

Este alerta dá-informações que lhe permitem identificar o recurso que foi utilizado para iniciar este ataque, o recurso atacado, o IP da vítima, o IP do atacante e a hora da deteção.

> [AZURE.NOTE] Os endereços IP em direto foram removidos desta captura de ecrã por motivos de privacidade.

### Shellcode Detetado 

Shellcode é o payload executado depois de o software maligno ter explorado uma vulnerabilidade de software.  Este alerta indica que a análise da informação de falha de sistema detetou código executável que apresenta um comportamento normalmente tido por payloads maliciosos.  Apesar de o software não malicioso poder ter este comportamento, não é característico das práticas normais de desenvolvimento de software. 

Os campos seguintes são comuns a todos os alertas de informação de falha de sistema:

- DUMPFILE: nome do ficheiro de informação de falha de sistema 
- PROCESSNAME: nome do processo de falha inesperada 
- PROCESSVERSION: versão do processo de falha inesperada 

Este alerta fornece o seguinte campo adicional:

- ADDRESS: a localização na memória do shellcode

Este é um exemplo deste tipo de alerta:

![Alerta de Shellcode](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig10-ga.png)

### Injeção de Código Detetada

A injeção de código é a inserção de módulos executáveis em processos ou threads em execução.  Esta técnica é utilizada por software maligno para aceder a dados, ocultar ou impedir a sua remoção (por exemplo, persistência).  Este alerta indica que a análise de informação de falha de sistema detetou um módulo injetado com a informação de falha de sistema.
 
Os programadores de software legítimo efetuam ocasionalmente a injeção de código por motivos não maliciosos, por exemplo, para modificar ou expandir uma aplicação ou componente do sistema operativo existente.  Para ajudar a diferenciar entre módulos maliciosos e não maliciosos injetados, o Centro de segurança do Azure verifica se o módulo inserido está ou não em conformidade com um perfil de comportamento suspeito. O resultado desta verificação é indicado pelo campo "SIGNATURE" do alerta e é refletido na gravidade do alerta, na descrição do alerta e nos passos de remediação do alerta.  

Além dos campos comuns descritos na secção “Shellcode Detetados” acima, este alerta fornece os seguintes campos adicionais:

- ADDRESS: a localização na memória do shellcode injetado
- IMAGENAME: o nome do módulo injetado. Tenha em atenção que isto pode estar em branco se o nome da imagem não for fornecido na imagem.
- SIGNATURE: indica se o módulo injetado está em conformidade com um perfil de comportamento suspeito. A tabela seguinte mostra exemplos de resultados e a respetiva descrição:

| **Valor de assinatura**                  | **Descrição**                                                                                                   |
|--------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Exploração de carregador reflexivo suspeito | Este comportamento suspeito está frequentemente relacionado com o carregamento de código injetado, independentemente do carregador do sistema operativo |
| Exploração injetada suspeita          | Significa malícia frequentemente relacionada com a injeção de código na memória                                       |
| Exploração de injeção suspeita         | Significa malícia frequentemente relacionada com a utilização de código injetado na memória                                   |
| Exploração de depuração injetada suspeita | Significa malícia frequentemente relacionada com a deteção ou a neutralização de um depurador                         |
| Exploração remota injetada suspeita   | Significa malícia frequentemente relacionada com cenários de comando e controlo (C2)                                 |

Este é um exemplo deste tipo de alerta:

![Injeção de Código Detetada](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig11-ga.png)

### Módulo de Hijacking Detetado

O Windows baseia-se em Dynamic Link Libraries (DLLs) para permitir que o software utilize a funcionalidade do sistema Windows comum.  O Hijacking de DLL ocorre quando software maligno altera a ordem de carregamento da DLL para carregar vários payloads maliciosos na memória, podendo ser executado código arbitrário. Este alerta indica que a análise de informação de falha de sistema detetou um módulo com o mesmo nome carregado a partir de dois caminhos diferentes, sendo um dos caminhos carregados proveniente de uma localização binária comum do sistema do Windows.

Por vezes, os programadores de software legítimo alteram a ordem de carregamento da DLL por motivos não maliciosos, por exemplo, para instrumentação, para expansão do SO Windows ou para aplicações do Windows.  Para ajudar a diferenciar entre alterações maliciosas e potencialmente benignas na ordem de carregamento de DLL, o Centro de segurança do Azure verifica se um módulo carregado está ou não em conformidade com um perfil suspeito.   O resultado desta verificação é indicado pelo campo "SIGNATURE" do alerta e é refletido na gravidade do alerta, na descrição do alerta e nos passos de remediação do alerta.  Analisar a cópia em disco do módulo de hijacking, por exemplo, verificando a assinatura digital dos ficheiros ou executando uma análise de software antivírus, pode fornecer mais informações sobre a natureza legítima ou maliciosa do módulo de hijacking.

Além dos campos comuns descritos na secção “Shellcode Detetado” acima, este alerta fornece os seguintes campos:

- SIGNATURE: indica se o módulo de hijacking está em conformidade com um perfil de comportamento suspeito
- HIJACKEDMODULE: o nome do módulo do sistema Windows hijacked
- HIJACKEDMODULEPATH: o caminho do módulo do sistema Windows hijacked
- HIJACKINGMODULEPATH: o caminho do módulo de hijacking 

Este é um exemplo deste tipo de alerta:

![Alerta de Hijack de DLL](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig12-ga.png)

### Módulo do Windows de Disfarce Detetado

O software maligno pode utilizar nomes comuns dos binários do sistema Windows (por exemplo, SVCHOST.EXE) ou módulos (por exemplo, NTDLL.DLL) para se infiltrar e ocultar dos administradores de sistema a natureza do software malicioso.  Este alerta indica que a análise de informação de falha de sistema detetou que o ficheiro de informação de falha de sistema contém módulos que utilizam nomes de módulos do sistema Windows, mas que não satisfaz outros critérios característicos dos módulos do Windows. Analisar a cópia de disco do módulo de disfarce pode fornecer mais informações sobre a natureza legítima ou maliciosa deste módulo. A análise pode incluir:

- Confirmar se o ficheiro em causa é enviado como parte de um pacote de software legítimo
- Verificar a assinatura digital do ficheiro 
- Executar uma análise de software antivírus no ficheiro

Além dos campos comuns descritos na secção “Shellcode Detetados” acima, este alerta fornece os seguintes campos adicionais:

- DETAILS: descreve se os metadados de módulos são válidos e se o módulo foi carregado a partir de um caminho de sistema.
- NAME: o nome do módulo do Windows de disfarce
- PATH: o caminho para o módulo do Windows de disfarce.

Este alerta também extrai e apresenta determinados campos, do cabeçalho de PE do módulo, como "CHECKSUM" e "TIMESTAMP".  Estes campos são apresentados apenas se os campos estiverem presentes no módulo. Veja o artigo [Microsoft PE e Especificação de COFF](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) para obter detalhes sobre estes campos.

Este é um exemplo deste tipo de alerta:

![Alerta de disfarce](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig13-ga.png)

### Binário de Sistema Modificado Detetado 

O software maligno pode modificar os binários do sistema principal para aceder a dados de maneira dissimulada ou persistir sub-reticiamente num sistema comprometido.  Este alerta indica que a análise da informação de falha de sistema detetou que foram modificados binários principais do SO Windows na memória ou no disco. 

Os programadores de software legítimo modificam ocasionalmente módulos do sistema na memória por razões não maliciosas, por exemplo, para desvios ou para compatibilidade de aplicações. Para ajudar a diferenciar entre módulos maliciosos e potencialmente legítimos, o Centro de Segurança do Azure verifica se o módulo modificado está ou não em conformidade com um perfil suspeito.   O resultado desta verificação é refletido na gravidade do alerta, na descrição do alerta e nos passos de remediação do alerta. 

Além dos campos comuns descritos na secção “Shellcode Detetados” acima, este alerta fornece os seguintes campos adicionais:

- MODULENAME: nome do binário de sistema modificado 
- MODULEVERSION: versão do binário de sistema modificado

Este é um exemplo deste tipo de alerta:

![Alerta de binário modificado](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig14-ga.png)


## Consultar também

Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

- [Lidar com Incidentes de Segurança no Centro de Segurança do Azure](security-center-incident.md)
- [Capacidades de deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md)
- [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
- [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
- [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.



<!--HONumber=ago16_HO4-->


