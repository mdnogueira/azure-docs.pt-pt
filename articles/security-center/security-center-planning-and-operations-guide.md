<properties
   pageTitle="Guia de Operações e Planeamento do Centro de Segurança | Microsoft Azure"
   description="Este documento ajuda-o a planear antes de adotar o Centro de Segurança do Azure e as considerações relativas à operações diárias."
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
   ms.date="07/21/2016"
   ms.author="yurid"/>

# Guia de operações e planeamento do Centro de Segurança do Azure
Este guia destina-se a profissionais de tecnologias da informação (TI), arquitetos de TI, analistas de segurança de informações e administradores de nuvem cujas organizações estejam a planear utilizar o Centro de Segurança do Azure.

## Guia de planeamento
Este guia aborda um conjunto de passos e tarefas que pode seguir para otimizar a utilização do Centro de Segurança do Azure com base nos requisitos de segurança e o modelo de gestão de nuvem da sua organização. Para tirar o máximo partido do Centro de Segurança, é importante compreender de que forma diferentes pessoas ou equipas na sua organização vão utilizar o serviço para dar resposta às necessidades de desenvolvimento e operações seguras, monitorização, governação e resposta a incidentes. As principais áreas a considerar quando planear utilizar o Centro de Segurança são:

- Funções de Segurança e Controlos de Acesso
- Recomendações e Políticas de Segurança
- Armazenamento e Recolha de Dados
- Monitorização de Segurança em Curso
- Resposta a Incidentes

Na secção seguinte irá aprender a planear cada uma dessas áreas e aplicar as recomendações com base nos seus requisitos.

> [AZURE.NOTE] Leia as [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure](security-center-faq.md) para obter uma lista de perguntas comuns que também podem ser úteis durante a fase de conceção e planeamento.


## Funções de segurança e controlos de acesso
Dependendo do tamanho e da estrutura da sua organização, várias pessoas e equipas podem utilizar o Centro de Segurança para executar diversas tarefas relacionadas com a segurança. Abaixo segue-se um exemplo de pessoas fictícias e as respetivas funções e responsabilidades de segurança:

![Funções](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01.png)

O Centro de Segurança permite que estes indivíduos cumpram estas variadas responsabilidades. Por exemplo:

**Jorge (Proprietário da Carga de Trabalho de Nuvem)**

- Visualiza e Completa Recomendações do Centro de Segurança no Portal do Azure
- Também Pode Utilizar um Sistema de Emissão de Permissões para Registar Alterações (preenche recomendações através da [API](https://msdn.microsoft.com/library/mt704034.aspx))

**André (CISO/CIO)**

- Visualiza Relatórios do Centro de Segurança do Power BI ou do Excel

**Diogo (Segurança de TI)**

- Define a Política de Segurança e Visualiza o Estado de Funcionamento de Segurança no Portal do Azure
- Analisa Dados e Gera Relatórios no Power BI

**Samuel (Operações de Segurança)**

- Visualiza e Faz a Triagem dos Alertas do Centro de Segurança no Portal do Azure
- Pode Utilizar um Dashboard Existente (preenche alertas através da [API](https://msdn.microsoft.com/library/mt704034.aspx))

**Francisco (Analista de Segurança)**

- Visualiza Alertas do Centro de Segurança no Portal do Azure
- Pode Utilizar um Dashboard Existente (preenche alertas através da [API](https://msdn.microsoft.com/library/mt704034.aspx))
- Analisa as Tendências dos Alertas no Power BI
- Revê Registos de Eventos do Storage

O Centro de Segurança utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../active-directory/role-based-access-control-configure.md), que fornece [funções incorporadas](../active-directory/role-based-access-built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure. Quando um utilizador abre o Centro de Segurança, apenas verá informações relacionadas com os recursos aos quais tenha acesso, o que significa que o utilizador tem atribuída a função de Proprietário, Contribuidor ou Leitor para a subscrição ou o grupo de recursos a que um recurso pertence. Ao utilizar as pessoas fictícias acima, seria necessário o seguinte RBAC:

**Jorge (Proprietário da Carga de Trabalho de Nuvem)**

- Proprietário/Colaborador de Grupo de Recursos

**Diogo (Segurança de TI)**

- Proprietário/Colaborador de Subscrição

**Samuel (Operações de Segurança)**

- Leitor de Subscrição para Visualizar Alertas
- Proprietário/Colaborador de Subscrição Necessário para Dispensar Alertas

**Francisco (Analista de Segurança)**

- Leitor de Subscrição para Visualizar Alertas
- Proprietário/Colaborador de Subscrição Necessário para Remediar ou Dispensar Alertas
- O Acesso ao Storage Pode Ser Necessário

Mais algumas informações importantes a considerar:

- Apenas os Proprietários e Contribuintes de subscrição podem editar uma política de segurança
- Apenas os Proprietários e os Contribuintes do grupo de recursos e de subscrição podem aplicar recomendações de segurança a um recurso

Quando planear o controlo de acesso através do RBAC para o Centro de Segurança, certifique-se de que compreende quem é que na sua organização vai utilizar o Centro de Segurança e quais são os tipos de tarefas que irão realizar. Em seguida, configure o RBAC em conformidade.

> [AZURE.NOTE] Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, os utilizadores que apenas necessitam de ver informações acerca do estado de segurança dos recursos, mas não têm de tomar medidas, como aplicar as recomendações ou editar políticas, também devem ter atribuída a função de Leitor.

## Recomendações e políticas de segurança
Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada ou do grupo de recursos. No Centro de Segurança, é possível definir políticas de acordo com os requisitos de segurança da sua empresa e com o tipo de aplicações ou sensibilidade dos dados.

As políticas que são ativadas ao nível da subscrição serão automaticamente propagadas a todos os grupos de recursos dentro da subscrição, conforme é mostrado no diagrama abaixo:

![Políticas de Segurança](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2-ga.png)

Como é mostrado na imagem acima, as políticas de segurança para grupos de recursos podem ser herdadas do nível de subscrição.

Em certos cenários onde é possível ter recursos num grupo de recursos que necessitam de um conjunto diferente de políticas, pode desativar a herança e aplicar políticas personalizadas a um Grupo de Recursos específico.

Se necessitar de políticas personalizadas em grupos de recursos específicos, deve desativar a herança no grupo de recursos e alterar as políticas de segurança. Por exemplo, se tiver algumas cargas de trabalho que não exigem a política de Encriptação de Dados Transparente (TDE) SQL, desative a política ao nível da subscrição e ative-a apenas nos grupos de recursos onde a TDE SQL é necessária.

Quando começar a criar políticas personalizadas para grupos de recursos diferentes, deve planear a implementação de políticas tendo em conta que em caso de um conflito de políticas (subscrição vs. grupo de recursos), a política de grupo de recursos prevalece.

> [AZURE.NOTE] Se precisar de rever as políticas que foram alteradas, pode utilizar os [Registos de Auditoria do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/). As alterações de políticas são sempre registadas nos Registos de Auditoria do Azure.

### Recomendações de segurança

Antes de configurar as políticas de segurança, deve rever cada uma das [recomendações de segurança](security-center-recommendations.md) e determinar se estas são adequadas às suas várias subscrições e grupos de recursos. Também é importante compreender que ação será executada para dar resposta às Recomendações de Segurança.

**Endpoint Protection**: se uma máquina virtual não tiver uma solução de proteção de ponto final ativada, o Centro de Segurança recomendará que instale uma. Se tiver uma solução de proteção de ponto final preferencial que já tenha adotado no local, terá de decidir se irá utilizar o mesmo antimalware para as suas VMs do Azure. O Centro de Segurança do Azure fornece-lhe várias opções de proteção de ponto final.  Pode utilizar o Microsoft Antimalware gratuito ou escolher entre uma lista de soluções de proteção de ponto final de parceiros integrados. Para obter mais informações sobre como implementar antimalware através do Centro de Segurança do Azure, leia [Instalar Proteção de Ponto Final no Centro de Segurança do Azure](security-center-install-endpoint-protection.md).

**Atualizações do Sistema**: o Centro de Segurança do Azure identificará as máquinas virtuais nas quais estejam em falta atualizações do sistema operativo críticas ou de segurança para o IaaS e os Serviços Cloud (PaaS). Considere quem será o responsável pela aplicação de atualizações quando necessário e como serão aplicadas. Muitas organizações utilizam os WSUS, o Windows Update ou outra ferramenta.

**Configurações de Linha de Base**: se as configurações do sistema operativo da máquina virtual não corresponderem às linhas de base recomendadas, será apresentada uma recomendação. Deve rever o conjunto de linhas de base [aqui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) e considerar de que forma as configurações do sistema operativo serão aplicadas.

**Encriptação de Disco**: se tiver discos da máquina virtual que não estão encriptados, o Centro de Segurança do Azure recomendará que aplique a Azure Disk Encryption, que tira partido do BitLocker para Windows e do DM-Crypt para Linux para fornecer a encriptação em volume para o SO e discos de dados. Esta recomendação irá redirecioná-lo para um [guia passo-a-passo](security-center-disk-encryption.md) que possui as instruções acerca de como efetuar esta encriptação.

Tenha em atenção que existem vários cenários de encriptação que precisa de abordar. Terá de planear os requisitos exclusivos para cada um destes cenários:

- Encriptação de novas Virtual Machines do Azure a partir de VHDs que encriptou com as suas próprias chaves de encriptação
- Encriptação de novas Virtual Machines do Azure que foram criadas a partir da Galeria do Azure
- Encriptação de Virtual Machines do Azure que já estão em execução no Azure

O planeamento de requisitos será diferente para cada um destes cenários. Consulte o [documento técnico da Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) para obter detalhes sobre cada um destes cenários.

**Firewall de Aplicação Web**: o Centro de Segurança do Azure irá identificar as máquinas virtuais que estão a executar Web Apps e recomenda-lhe que instale uma Firewall de Aplicação Web (WAF). Avalie as soluções de parceiros disponíveis para determinar qual é a melhor opção para a sua organização e determine como a solução irá licenciada (os parceiros podem suportar modelos do tipo Traga a sua própria licença e/ou Pay as you go). Para obter mais informações sobre como implementar uma firewall de aplicação Web nas suas VMs do Azure através do Centro de Segurança do Azure, leia [Adicionar uma firewall de aplicação Web no Centro de Segurança do Azure](security-center-add-web-application-firewall.md).

**Firewall da Próxima Geração**: permite-lhe aprovisionar uma aplicação virtual a partir dos principais fornecedores, incluindo o Check Point e, logo depois, a Cisco e a Fortinet. Isto expande as proteções de rede para além dos Grupos de Segurança de Rede, que estão incorporados no Azure. O Centro de Segurança irá detetar as implementações para as quais se recomenda uma Firewall da Próxima Geração e permite-lhe aprovisionar uma aplicação virtual.

**Redes Virtuais**: o Centro de Segurança do Azure irá avaliar a infraestrutura e a configuração da sua [Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) para verificar se o [Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md) é aplicado e configurado adequadamente com regras de tráfego de entrada. Deve considerar quais as regras de tráfego que devem ser definidas e comunicá-lo aos indivíduos que irão aplicar as recomendações de segurança relacionadas.

O Centro de Segurança irá recomendar que forneça os detalhes de contacto de segurança para a sua subscrição do Azure. A Microsoft irá utilizar estas informações para o contactar se o Microsoft Security Response Center (MSRC) detetar que os seus dados de cliente foram acedidos de forma ilícita ou não autorizada. Leia [Provide security contact details in Azure Security Center (Fornecer detalhes de contacto de segurança no Centro de Segurança do Azure)](security-center-provide-security-contact-details.md) para obter mais informações sobre como ativar esta recomendação.

## Armazenamento e recolha de dados

Recomendamos vivamente que ative a recolha de dados para cada uma das suas subscrições, já que tal irá garantir que a monitorização de segurança está disponível para todas as suas VMs. A recolha de dados é ativada através do Agente de Monitorização do Azure (ASMAgentLauncher.exe) e da extensão de Monitorização de Segurança do Azure (ASMMonitoringAgent.exe).

A extensão de Monitorização de Segurança do Azure verifica a existência de várias configurações relevantes de segurança e recolhe registos de segurança da máquina virtual. Estes dados são enviados para uma conta do Storage que especificar. O gestor de análise (ASMSoftwareScanner.exe) também será instalado na máquina virtual e será utilizado como um detetor de patches.

Depois de a recolha de dados estar ativada na política de segurança, o agente de monitorização e as extensões são instalados automaticamente em todas as máquinas virtuais existentes e em quaisquer VMs novas suportadas que sejam aprovisionadas no Azure.  O processo do agente não é invasivo e não afeta o desempenho da VM.

> [AZURE.NOTE] Para resolver problemas relacionados com o Agente de Monitorização de Segurança do Azure, leia o [Guia de Resolução de Problemas do Centro de Segurança do Azure](security-center-troubleshooting-guide.md).

Se a determinada altura pretender desativar a Recolha de Dados, pode desativá-la na política de segurança. Para eliminar os agentes de monitorização anteriormente implementados, selecione a opção de menu Eliminar Agentes.

> [AZURE.NOTE] para encontrar uma lista de VMs suportadas, leia as [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure ](security-center-faq.md).

Para cada região na qual tenha máquinas virtuais em execução, selecione a conta do Storage onde os dados recolhidos dessas máquinas virtuais estão armazenados. Se não escolher uma conta do Storage para cada região, será criada uma por si. Pode escolher a localização de armazenamento por região ou armazenar todas as informações numa localização central. Enquanto as políticas de segurança podem ser definidas ao nível de subscrição do Azure e ao nível de grupo de recursos, a região para a sua conta do Storage apenas pode ser selecionada ao nível da subscrição.

Se estiver a utilizar uma conta do Storage partilhada entre diferentes recursos do Azure, certifique-se de que lê o artigo [Metas de Desempenho e Escalabilidade do Storage do Azure](../storage/storage-scalability-targets.md) para obter mais informações sobre limites e restrições de tamanho. A sua subscrição também tem limites de contas de armazenamento. Reveja [Azure subscription and service limits, quotas, and constraints (Limites, quotas e restrições de subscrição e serviço do Azure)](../azure-subscription-service-limits.md) para compreender melhor estes limites.

> [AZURE.NOTE] Os custos associados a este armazenamento não estão incluídos no preço do serviço do Centro de Segurança do Azure e serão cobrados em separado às [taxas do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/) normais.

As considerações sobre o desempenho e a escalabilidade também deve ser planeadas de acordo com o tamanho do seu ambiente do Azure e os recursos que estão a consumir a sua conta do Storage. Reveja a [Lista de Verificação de Desempenho e Escalabilidade do Storage do Microsoft Azure](../storage/storage-performance-checklist.md) para obter mais informações.

## Monitorização de segurança em curso

Após a configuração inicial e a aplicação das recomendações do Centro de Segurança, o passo seguinte é considerar os processos operacionais do Centro de Segurança.

Para aceder ao Centro de Segurança a partir do Portal do Azure, pode clicar em **Procurar** e escrever **Centro de Segurança** no campo **Filtro**. As vistas que o utilizador obtém estão de acordo com estes filtros aplicados.

O Centro de Segurança do Azure não irá interferir com os seus procedimentos operacionais normais, irá monitorizar passivamente as implementações e facultar recomendações com base nas políticas de segurança que ativou.

O dashboard do Centro de Segurança do Azure está dividido em duas partes principais:

- Prevenção
- Deteção

Quando ativa primeiro a recolha de dados no Centro de Segurança do Azure para o seu ambiente do Azure atual, certifique-se de que revê todas as recomendações, que podem realizar no painel **Recomendações** ou por recurso (**Máquina Virtual**, **Redes**, **SQL** e **Aplicação**).

Assim que abordar todas as recomendações, a secção **Prevenção** deve estar verde em todos os recursos que foram abordados. A monitorização contínua neste momento torna-se mais fácil, uma vez que apenas realizará ações com base em alterações nos mosaicos de estado de funcionamento de segurança de recursos e de recomendações.

A secção **Deteção** é mais reativa, pois trata-se de alertas relativos a problemas que estão a ocorrer ou que ocorreram anteriormente e foram detetados pelos controlos do Centro de Segurança do Azure e por sistemas de terceiros. O mosaico Alertas de Segurança mostrará gráficos de barras que representam o número de alertas de deteção de ameaças encontradas em cada dia e a respetiva distribuição entre as diferentes categorias de gravidade (baixa, média, alta). Para obter mais informações acerca dos Alertas de Segurança, leia [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md).

> [AZURE.NOTE] Também pode tirar partido do Microsoft Power BI para visualizar os seus dados do Centro de Segurança do Azure. Leia [Obter informações a partir de dados do Centro de Segurança do Azure com Power BI](security-center-powerbi.md).

### Monitorização de recursos novos ou alterados

A maior parte dos ambientes do Azure são dinâmicos, com recursos novos que são regularmente acelerados ou desacelerados, configurações ou alterações, etc. O Centro de Segurança do Azure ajuda-o a garantir que tem visibilidade sobre o estado de segurança destes recursos novos.

Quando adicionar novos recursos (VMs, BDs SQL) ao seu Ambiente do Azure, o Centro de Segurança irá detetar automaticamente estes recursos e começar a monitorizar a segurança dos mesmos. Isto também inclui funções da Web e funções de trabalho PaaS. Se a Recolha de Dados estiver ativada na [Política de Segurança](security-center-policies.md), serão ativadas automaticamente capacidades de monitorização adicionais para as suas máquinas virtuais.

![Áreas principais](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-ga.png)

1.  Para as máquinas virtuais, aceda ao mosaico **Estado de funcionamento de segurança de recursos** e clique em **Virtual Machines**. Quaisquer problemas com a ativação da recolha de dados ou recomendações relacionadas aparecerão na secção **Recomendações de Monitorização**.
2.  Visualize as **Recomendações** para ver que riscos de segurança foram identificados para o novo recurso, se for o caso.
3.  É muito comum que, quando novas VMs são adicionadas ao seu ambiente, apenas o sistema operativo é inicialmente instalado. O proprietário do recurso pode necessitar de algum tempo para implementar outras aplicações que serão utilizadas por estas VMs.  Idealmente, deve ter conhecimento da intenção final desta carga de trabalho. Vai ser um Servidor de Aplicações? Com base no que esta nova carga de trabalho vai ser, pode ativar a **Política de Segurança** adequada, que é o terceiro passo deste fluxo de trabalho.
4.  À medida que são adicionados novos recursos ao seu ambiente do Azure, é possível que apareçam novos alertas no mosaico **Alertas de Segurança**. Verifique sempre se existem novos alertas neste mosaico e tome medidas de acordo com as recomendações do Centro de Segurança.

Também é aconselhável que monitorize regularmente o estado dos recursos existentes para identificar as alterações de configuração que criaram riscos de segurança, que se desviam das linhas de base recomendadas e dos alertas de segurança. Comece no dashboard do Centro de Segurança. A partir daí tem três áreas principais para rever de forma consistente.

![Operações](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4.png)

1.  O painel **Estado de funcionamento de segurança de recursos** fornece-lhe acesso rápido aos seus recursos principais. Utilize esta opção para monitorizar as suas Virtual Machines, Redes, SQL e Aplicações.
2.  O painel **Recomendações** permite-lhe rever as recomendações do Centro de Segurança. Durante a monitorização contínua, pode constatar que não tem recomendações diariamente. Tal é normal, uma vez que abordou todas as recomendações na configuração inicial do Centro de Segurança. Por este motivo, é possível que não tenha novas informações nesta secção todos os dias e que apenas tenha de aceder à mesma conforme seja necessário.
3.  O painel **Deteção** pode ser alterado de forma bastante frequente ou pouco frequente. Reveja sempre os alertas de segurança e tome medidas com base nas recomendações do Centro de Segurança.

## Resposta a incidentes

O Centro de Segurança deteta e alerta-o relativamente a ameaças à medida que ocorrem. As organizações devem monitorizar a existência de novos alertas de segurança e tomar medidas conforme seja necessário para uma investigação mais aprofundada ou para remediar o ataque. Para obter mais informações sobre como funciona a deteção de ameaças do Centro de Segurança, leia [Azure Security Center detection capabilities (Capacidades de deteção do Centro de Segurança do Azure)](security-center-detection-capabilities.md).

Embora este artigo não pretenda ajudá-lo a criar o seu próprio plano de Resposta a Incidentes, iremos utilizar o Microsoft Azure Security Response no ciclo de vida da Nuvem como a base para as fases de resposta a incidentes. As fases são apresentadas no diagrama seguinte:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [AZURE.NOTE] Pode utilizar o [Computer Security Incident Handling Guide](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guia de Processamento de Incidentes de Segurança Informática) do National Institute of Standards and Technology (NIST) (Instituto Nacional de Normas e Tecnologia dos Estados Unidos) como referência para o ajudar a criar o seu próprio.

Pode utilizar Alertas do Centro de Segurança durante as seguintes fases:

- **Detetar**: identifique uma atividade suspeita num ou mais recursos. 
- **Avaliar**: efetue a avaliação inicial para obter mais informações sobre a atividade suspeita.
- **Diagnosticar**: utilize os passos de remediação para realizar o procedimento técnico para resolver o problema.

Cada Alerta de Segurança faculta informações que podem ser utilizadas para compreender melhor a natureza do ataque e sugerir possíveis mitigações. Alguns alertas também fornecem ligações para mais informações ou para outras fontes de informação no Azure. Pode utilizar as informações fornecidas para uma investigação mais aprofundada e para iniciar a mitigação.

O exemplo abaixo mostra uma atividade suspeita de RDP a ocorrer:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Como pode ver, este painel mostra informações detalhadas acerca da hora em que ocorreu o ataque, o nome do anfitrião de origem, a VM visada e também lhe disponibiliza passos de recomendação. Em determinadas circunstâncias, as informações da origem do ataque podem estar vazias. Leia [Informações da Origem em Falta nos Alertas do Centro de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) para obter mais informações acerca deste tipo de comportamento.


## Consultar também
Neste documento, aprendeu a planear a adoção do Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

- [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
- [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
- [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.



<!--HONumber=Aug16_HO1-->


