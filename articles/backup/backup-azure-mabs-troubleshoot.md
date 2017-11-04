---
title: "Resolver problemas do servidor de cópia de segurança do Azure | Microsoft Docs"
description: "Resolver problemas de instalação, o registo do servidor de cópia de segurança do Azure e a cópia de segurança e restauro de cargas de trabalho da aplicação"
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: 7ef808e933d1c3ff88e18766cd3a29138959297a
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="troubleshoot-azure-backup-server"></a>Resolução de problemas do Azure Backup Server

Pode resolver erros encontrados ao utilizar o servidor de cópia de segurança do Azure com informações listados na seguinte tabela.

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Erro: Inválido fornecidas credenciais do cofre. O ficheiro está danificado ou não ter as credenciais mais recentes associadas ao serviço de recuperação 

Siga estes [passos de resolução de problemas] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) para resolver este problema.

## <a name="error-the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-server"></a>Erro: A operação do agente falhou devido a um erro de comunicação com o serviço de coordenador de agente do DPM no servidor 

Siga estes [passos de resolução de problemas] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) para resolver este problema.

## <a name="error-setup-could-not-update-registry-metadata"></a>Erro: A configuração não foi possível atualizar os metadados de registo

Siga estes [passos de resolução de problemas] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#installation-issues) para resolver este problema.


## <a name="installation-issues"></a>Problemas de instalação

| Operação | Detalhes do erro | Solução |
|-----------|---------------|------------|
|Instalação | A configuração não foi possível atualizar os metadados de registo. Esta falha de atualização poderia através da utilização de consumo de armazenamento. Para evitar este Atualize a entrada de registo ReFS Trimming. | Ajuste a chave de registo, SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim. Defina o valor Dword para 1. |
|Instalação | A configuração não foi possível atualizar os metadados de registo. Esta falha de atualização poderia através da utilização de consumo de armazenamento. Para evitar este Atualize a entrada de registo SnapOptimization de Volume. | Crie a chave de registo, SOFTWARE\Microsoft Manager\Configuration\VolSnapOptimization\WriteIds de proteção de dados, com um valor de cadeia vazia. |

## <a name="registration-and-agent-related-issues"></a>Registo e o agente problemas relacionados com o
| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Registar para um cofre | Credenciais do cofre inválidas fornecidas. O ficheiro está danificado ou não ter as credenciais mais recentes associadas ao serviço de recuperação | Para corrigir este erro: <ol><li> Transferir o ficheiro mais recente de credenciais do cofre e tente novamente <br>(OU)</li> <li> Se a ação acima não resultar, experimente transferir as credenciais para um diretório local diferente ou crie um novo cofre <br>(OU)</li> <li> Tente atualizar a data e hora definições conforme indicado na [este blogue](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/) <br>(OU)</li> <li> Verifique se c:\windows\temp tem mais do que 65000 ficheiros. Mover os ficheiros obsoletos para outra localização ou eliminar os itens na pasta Temp <br>(OU)</li> <li> Verificar o estado de certificados <br> a. Abrir "Gerir certificados de computador" (no painel de controlo) <br> b. Expanda o nó "Pessoal" e o nó de subordinados "Certificados" <br> c.  Remover o certificado "Ferramentas do Windows Azure" <br> d. Repita o registo no cliente do Backup do Azure <br> (OU) </li> <li> Verifique se qualquer política de grupo está no local </li></ol> |
| Enviar os agentes para servidores protegidos | A operação do agente falhou devido a um erro de comunicação com o serviço Coordenador de agente do DPM em \<ServerName > | **Se a ação recomendada mostrada no produto não funciona**, <ol><li> Se estiver a ligá um computador a partir do domínio não fidedigno, siga [estes](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx) passos <br> (OU) </li><li> Se estiver a ligá um computador de um domínio fidedigno, resolver problemas com os passos descritos em [este blogue](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/) <br>(OU)</li><li> Tente desativar antivírus como um passo de resolução de problemas. Se resolve o problema, modifique as definições de antivírus como sugerido [neste artigo] (https://technet.microsoft.com/library/hh757911.aspx)</li></ol> |
| Enviar os agentes para servidores protegidos | As credenciais especificadas para o servidor são inválidas | **Se a ação recomendada mostrada no produto não funciona**, <br> Tente instalar manualmente o agente de proteção no servidor de produção, conforme especificado no [neste artigo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)|
| Agente de cópia de segurança do Azure não conseguiu ligar ao serviço de cópia de segurança do Azure (ID: 100050) | O Azure Backup Agent não conseguiu ligar ao serviço de cópia de segurança do Azure. | **Se a ação recomendada mostrada no produto não funciona**, <br>1. Execute o seguinte comando na linha, elevada psexec -i -s "c:\Programas\Microsoft Files\Internet Explorer\iexplore.exe" irá abrir a janela do internet explorer. <br/> 2. Aceda a ferramentas -> Opções da Internet -> ligações -> definições de LAN. <br/> 3. Verifique as definições de proxy para a conta de sistema. Definir o IP de Proxy e a porta. <br/> 4. Feche o Internet Explorer.|
| Falha na instalação do agente do Backup do Azure | A instalação dos serviços de recuperação do Microsoft Azure falhou. Todas as alterações feitas pela instalação do serviços de recuperação do Microsoft Azure para o sistema foram revertidas. (ID: 4024) | Instale manualmente o agente do Azure


## <a name="configuring-protection-group"></a>Configurar o grupo de proteção
| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Configurar grupos de proteção | O DPM não conseguiu enumerar o componente da aplicação no computador protegido (computador protegido nome) | Clique em 'Atualizar' no ecrã de IU de grupo de proteção configurar ao nível da origem de dados/componentes relevante |
| Configurar grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido é um SQL server, verifique se as permissões de função sysadmin foram fornecidas para a conta do sistema (NTAuthority\System) no computador protegido conforme indicado na [neste artigo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)
| Configurar grupos de proteção | Não existe espaço livre suficiente no agrupamento de armazenamento para este grupo de proteção | Os discos que são adicionados ao agrupamento de armazenamento [não deve conter uma partição](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Elimine os volumes eventualmente existentes nos discos e, em seguida, adicioná-lo ao agrupamento de armazenamento|
| Alterações na política |Não foi possível modificar a política de cópia de segurança. Erro: A operação atual falhou devido a um erro interno do serviço [0x29834]. Repita a operação após algum tempo. Se o problema persistir, contacte o suporte da Microsoft. |**Causa:**<br/>Este erro é fornecido quando as definições de segurança estão ativadas, tente reduzir o período de retenção abaixo os valores mínimos especificados acima e estiver numa versão não suportada (abaixo versão MAB 2.0.9052 e atualização de servidor do Backup do Azure 1). <br/>**Ação recomendada:**<br/> Neste caso, deve definir o período de retenção acima a retenção mínimo período especificado (sete dias para diariamente, quatro semanas para semanal, três semanas para mensal ou um ano para anual) continuar com a política relacionadas com atualizações. Opcionalmente, a abordagem preferida seria possível atualizar o agente de cópia de segurança e o servidor de cópia de segurança do Azure para tirar partido de todas as atualizações de segurança. |

## <a name="backup"></a>Cópia de segurança
| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança | A réplica está inconsistente | Certifique-se que consitency automática seleciona a opção grupo de proteção assistente está ativado. Obter mais informações sobre as causas de inconsistência de réplica e sugestões relevantes [aqui](https://technet.microsoft.com/library/cc161593.aspx) <br> <ol><li> Em caso de cópia de segurança do Estado do sistema/BMR, verifique se a cópia de segurança do Windows Server está instalada ou não no servidor protegido </li><li> Verifique a existência de espaço relacionado com problemas no armazenamento do DPM no servidor do DPM/MABS do agrupamento e atribuir armazenamento conforme necessário </li><li> Verifique o estado do serviço de cópia sombra de volumes no servidor protegido. Se estiver no estado desativado defini-lo para iniciar manualmente e iniciar o serviço no servidor. Em seguida, volte atrás para a consola do DPM/MABS e iniciar a sincronização com a tarefa de verificação de consistência.</li></ol>|
| Cópia de segurança | Ocorreu um erro inesperado durante a execução da tarefa, o dispositivo não está pronto | **Se a ação recomendada mostrada no produto não funciona**, <br> <ol><li>Definir o espaço de armazenamento de cópia sombra de volumes para ilimitado sobre os itens do grupo de proteção e execute a verificação de consistência <br></li> (OU) <li>Tente eliminar o grupo de proteção existente e crie várias campanhas novas – um com cada item individuais no mesmo</li></ol> |
| Cópia de segurança | Se estiver a criar cópias de segurança só do Estado do sistema, verifique se existe espaço livre suficiente no computador protegido para armazenar a cópia de segurança do Estado do sistema | <ol><li>Certifique-se de que a WSB no computador protegido está instalado</li><li>Certifique-se de que existe espaço suficiente está presente no computador protegido para o estado do sistema: é a forma mais fácil de fazê-lo para ir para o computador protegido, abra o WSB e clicar as seleções e selecionar a BMR. A IU irá, em seguida, indicam quanto espaço é necessário para este. Abra WSB -> Local cópia de segurança -> cópia de segurança agenda -> selecionar configuração de cópia de segurança -> servidor completo (é apresentado o tamanho). Utilize este tamanho para verificação.</li></ol>
| Cópia de segurança | Falha na criação do ponto de recuperação online | Se a mensagem de erro indica "Windows Azure Backup Agent não conseguiu criar um instantâneo do volume selecionado", tente aumentar o espaço no volume de pontos de recuperação e de réplica.
| Cópia de segurança | Falha na criação do ponto de recuperação online | Se a mensagem de erro indica "O Windows Azure Backup Agent não é possível ligar o serviço OBEngine", certifique-se de que o OBEngine existe na lista de serviços em execução no computador. Se o serviço OBEngine não está em execução, utilize o comando "net start OBEngine" para iniciar o serviço OBEngine.
| Cópia de segurança | Falha na criação do ponto de recuperação online | Se a mensagem de erro indica "a frase de acesso de encriptação para este servidor não está definido. Configure uma frase de acesso de encriptação"tente configurar uma frase de acesso de encriptação. Se falhar, <br> <ol><li>Verifique se a localização scratch existe ou não. A localização mencionada no registo HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config com o nome "ScratchLocation" deve existir.</li><li> Se a localização scratch existe, tente voltar a registar utilizando o frase de acesso antigo. **Sempre que configure uma frase de acesso de encriptação, guarde-a numa localização segura**</li><ol>
| Cópia de segurança | Falha de cópia de segurança de BMR | Se o tamanho de BMR for grande, tente novamente depois de mover alguns ficheiros de aplicação para o disco do SO |
| Cópia de segurança | Voltar a proteger a VM de VMWare num novo servidor MABS não mostra como disponível para adicionar. | Um servidor MABS antigo, extinto são indicadas propriedades de VMWare. Para resolver este problema: no VCenter (equivalente do SC VMM), aceda ao separador 'Resumo' e 'Os atributos personalizados'.  Elimine o nome do servidor MABS antigo a partir do valor 'DPMServer'.  Volte para o novo servidor MABS e modificar o PG.  Depois de utilizar o botão 'Atualizar', a VM será apresentada uma caixa de verificação como disponível para adicionar a proteção. |
| Cópia de segurança | Erro ao aceder a pastas/ficheiros partilhados | Tente modificar as definições de antivírus como sugerido [aqui](https://technet.microsoft.com/library/hh757911.aspx)|
| Cópia de segurança | Falha de tarefas de criação de pontos de recuperação online para a VM de VMware. O DPM encontrou o erro do VMware ao tentar obter informações de ChangeTracking. Código de erro - FileFaultFault (ID 33621) |  1. Repor o ctk no VMWare, para as VMs afetadas <br/> Verifique se o disco independente não está a ser realizada no VMWare <br/> Pare a proteção para as VMs afetadas e voltar a proteger com o botão de atualização <br/> Executar um CC para as VMs afetadas|


## <a name="change-passphrase"></a>Alterar o frase de acesso
| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Alterar o frase de acesso |Segurança PIN introduzido está incorreta. Fornece o PIN de segurança correto para concluir esta operação. |**Causa:**<br/> Este erro é fornecido ao introduzir o PIN de segurança expirou ou era inválida ao efetuar a operação crítico (como alterar o frase de acesso). <br/>**Ação recomendada:**<br/> Para concluir a operação, tem de introduzir o PIN de segurança válido. Para obter o PIN, inicie sessão no portal do Azure e navegue para o Cofre dos serviços de recuperação > Definições > propriedades > gerar PIN de segurança. Utilize este PIN para alterar o frase de acesso. |
| Alterar o frase de acesso |Falha na operação. ID: 120002 |**Causa:**<br/>Este erro é fornecido quando as definições de segurança estão ativadas, tentar alterar o frase de acesso e se a versão não suportada.<br/>**Ação recomendada:**<br/> Para alterar o frase de acesso, primeiro tem de atualizar o agente de cópia de segurança para a versão mínima 2.0.9052 mínima e o servidor de cópia de segurança do Azure para atualização mínima 1, em seguida, introduza o PIN de segurança válido. Para obter o PIN, inicie sessão no portal do Azure e navegue para o Cofre dos serviços de recuperação > Definições > propriedades > gerar PIN de segurança. Utilize este PIN para alterar o frase de acesso. |


## <a name="configure-email-notifications"></a>Configurar notificações por e-mail
| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| A tentar configurar as notificações de e-mail utilizando a conta do Office 365. | obter ID de erro: 2013| **Causa:**<br/> A tentar utilizar a conta do Office 365 <br/> **Ação recomendada:**<br/> A primeira coisa para garantir que é que "Permitir anónimo reencaminhamento em a receber o conector" para o servidor do DPM está configurada no Exchange. Eis uma ligação na configuração: http://technet.microsoft.com/en-us/library/bb232021.aspx <br/> Se não é possível utilizar um reencaminhamento de SMTP interno e tem de definir através do servidor do Office 365, pode configurar o IIS para ser um reencaminhamento para este. <br/> Terá de configurar o servidor do DPM para conseguir o SMTP para o Office 365, utilizando o IIS https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx to setup IIS to relay to O365 de reencaminhamento <br/> Nota importante: no passo 3->-g -> ii, certifique-se utilizar user@domain.com formato e não domínio \ utilizador <br/> Ponto de DPM para utilizar o servername local como SMTP servidor, a porta 587 e, em seguida, o e-mail de utilizador que deverá ser proveniente de mensagens de correio eletrónico. <br/> O nome de utilizador e palavra-passe na página de configuração do DPM SMTP devem ser uma conta de domínio no domínio que DPM está ativada. <br/> Nota: Quando alterar o endereço do servidor SMTP, efetuar a alteração para as novas definições, feche a caixa de definições e, em seguida, volte a abrir o para garantir que este reflete o novo valor.  Basta alterar e testar não sempre demorará as novas definições, para que testar desta forma é a melhor prática. <br/> Em qualquer altura durante este processo, pode limpar estas definições ao fechar a consola do DPM e editar as seguintes chaves do registo:<br/> HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Elimine chaves SMTPPassword e SMTPUserName. <br/> Pode adicioná-los novamente na IU quando iniciar novamente.
