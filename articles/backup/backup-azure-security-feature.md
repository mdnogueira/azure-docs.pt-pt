---
title: "Funcionalidades de segurança para ajudar a proteger as cópias de segurança de híbridas que utilizam a cópia de segurança do Azure | Microsoft Docs"
description: "Saiba como utilizar funcionalidades de segurança na cópia de segurança do Azure para efetuar cópias de segurança mais seguro"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: pajosh
ms.openlocfilehash: 8ef9ddc345fb553b93815022dc3e6a796cae8b3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funcionalidades de segurança para ajudar a proteger as cópias de segurança de híbridas que utilizam a cópia de segurança do Azure
Questões sobre problemas de segurança, como software maligno, ransomware e intrusões, estão aumentar. Estes problemas de segurança podem ser dispendiosos, em termos de dados e dinheiro. Para proteger contra estes ataques, a cópia de segurança do Azure agora fornece funcionalidades de segurança para ajudar a proteger as cópias de segurança híbrida. Este artigo abrange como ativar e utilizar estas funcionalidades, utilizando um agente de serviços de recuperação do Azure e o servidor de cópia de segurança do Azure. Estas funcionalidades incluem:

- **Prevenção**. É adicionada uma camada adicional de autenticação sempre que uma operação crítica como uma frase de acesso a alteração é executada. Esta validação é certificar-se de que estas operações podem ser efetuadas apenas por utilizadores que tenham credenciais do Azure válidas.
- **Alertas**. Uma notificação por e-mail é enviada para o administrador da subscrição, sempre que uma operação crítica como eliminar dados de cópia de segurança é executada. Este e-mail assegura que o utilizador é notificado rapidamente sobre essas ações.
- **Recuperação**. Dados de cópia de segurança eliminados são mantidos adicional para 14 dias a contar da data em que a eliminação. Isto garante a capacidade de recuperação dos dados dentro de um determinado período de tempo, pelo que não existe nenhuma perda de dados, mesmo se um ataque acontece. Além disso, um maior número de pontos de recuperação mínimo é mantido para proteger contra dados danificados.

> [!NOTE]
> Funcionalidades de segurança não devem ser ativadas se estiver a utilizar a infraestrutura de cópia de segurança de VM de serviço (IaaS). Estas funcionalidades ainda não estão disponíveis para cópia de segurança de VM do IaaS, para que ativá-las não terá qualquer impacto. Funcionalidades de segurança devem ser ativadas apenas se estiver a utilizar: <br/>
>  * **Agente de cópia de segurança do Azure**. Versão do agente mínimo 2.0.9052. Após ativar estas funcionalidades, deverá atualizar para esta versão do agente para executar operações críticas. <br/>
>  * **Servidor do Backup do Azure**. Versão mínima do Azure Backup agent 2.0.9052 com o servidor de cópia de segurança do Azure a atualização 1. <br/>
>  * **O System Center Data Protection Manager**. Versão mínima do Backup do Azure agente 2.0.9052 com o Data Protection Manager 2012 R2 UR12 ou UR2 do Data Protection Manager 2016. <br/> 


> [!NOTE]
> Estas funcionalidades estão disponíveis apenas para o Cofre de serviços de recuperação. Todos os cofres dos serviços de recuperação criados recentemente têm estas funcionalidades ativadas por predefinição. Para cofres de serviços de recuperação existentes, os utilizadores ativar estas funcionalidades, utilizando os passos mencionados na secção seguinte. Depois das funcionalidades estão ativadas, estas são aplicadas a todos os serviços de recuperação agente computadores, instâncias de servidor de cópia de segurança do Azure, e do Data Protection Manager servidores registados no cofre. A ativação desta definição é uma ação única e não é possível desativar estas funcionalidades após ativá-las.
>

## <a name="enable-security-features"></a>Ativar funcionalidades de segurança
Se estiver a criar um cofre dos serviços de recuperação, pode utilizar todas as funcionalidades de segurança. Se estiver a trabalhar com um cofre existente, ative funcionalidades de segurança, seguindo estes passos:

1. Inicie sessão no portal do Azure com as suas credenciais do Azure.
2. Selecione **procurar**e escreva **dos serviços de recuperação**.

    ![Opção de procurar portal de captura de ecrã do Azure](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    É apresentada a lista dos cofres dos serviços de recuperação. Nesta lista, selecione um cofre. O dashboard do cofre selecionado é aberto.
3. Na lista de itens que é apresentado no cofre, em **definições**, clique em **propriedades**.

    ![Opções do Cofre de serviços de captura de ecrã de recuperação](./media/backup-azure-security-feature/vault-list-properties.png)
4. Em **definições de segurança**, clique em **atualização**.

    ![Propriedades do Cofre de serviços de captura de ecrã de recuperação](./media/backup-azure-security-feature/security-settings-update.png)

    A hiperligação de atualização abre o **definições de segurança** painel, que fornece um resumo das funcionalidades e permite-lhe ativá-los.
5. Na lista pendente **configurou Azure multi-factor Authentication?**, selecione um valor para confirmar se tiver ativado o [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Se estiver ativada, é-lhe pedido para autenticar a partir de outro dispositivo (por exemplo, um telemóvel) ao iniciar sessão no portal do Azure.

   Quando efetua operações críticas na cópia de segurança, terá de introduzir um PIN, disponível no portal do Azure de segurança. Ativar o Azure multi-factor Authentication adiciona uma camada de segurança. Apenas que os utilizadores com credenciais do Azure válidos autorizados e autenticados a partir de um segundo dispositivo, pode aceder ao portal do Azure.
6. Para guardar as definições de segurança, selecione **ativar** e clique em **guardar**. Pode selecionar **ativar** apenas depois de selecionar um valor entre o **configurou Azure multi-factor Authentication?** lista no passo anterior.

    ![Captura de ecrã de definições de segurança](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Recuperar eliminados os dados da cópia de segurança
Cópia de segurança mantém os dados de cópia de segurança foi eliminados um adicionais nos últimos 14 dias e não elimine-o imediatamente se o **parar a cópia de segurança com dados de cópia de segurança** é efetuar a operação. Para restaurar estes dados no período de 14 dias, siga os passos seguintes, dependendo de que está a utilizar:

Para **agente dos serviços de recuperação do Azure** utilizadores:

1. Se o computador em que as cópias de segurança foram acontecer ainda está disponível, utilize [recuperar dados para a mesma máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) nos serviços de recuperação do Azure, para recuperar a partir de todos os pontos de recuperação antigos.
2. Se este computador não estiver disponível, utilize [recuperar para uma máquina alternativa](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) utilizar outro computador dos serviços de recuperação do Azure para obter estes dados.

Para **servidor de cópia de segurança do Azure** utilizadores:

1. Se o servidor em que as cópias de segurança foram acontecer ainda está disponível, voltar a proteger as origens de dados eliminadas e utilizar o **recuperar dados** funcionalidade para recuperar a partir de todos os pontos de recuperação antigos.
2. Se este servidor não estiver disponível, utilize [recuperar dados a partir de outro servidor de cópia de segurança do Azure](backup-azure-alternate-dpm-server.md) utilizar outra instância do servidor de cópia de segurança do Azure para obter estes dados.

Para **Data Protection Manager** utilizadores:

1. Se o servidor em que as cópias de segurança foram acontecer ainda está disponível, voltar a proteger as origens de dados eliminadas e utilizar o **recuperar dados** funcionalidade para recuperar a partir de todos os pontos de recuperação antigos.
2. Se este servidor não estiver disponível, utilize [adicionar DPM externo](backup-azure-alternate-dpm-server.md) utilizar outro servidor do Data Protection Manager para obter estes dados.

## <a name="prevent-attacks"></a>Impedir ataques
Foram adicionadas verificações para se certificar de que apenas os utilizadores válidos podem desempenhar várias operações. Estes incluem-se ao adicionar uma camada adicional de autenticação e a manutenção de um período de retenção mínimo para fins de recuperação.

### <a name="authentication-to-perform-critical-operations"></a>Autenticação para efetuar operações críticas
Como parte da adição de uma camada adicional de autenticação para operações críticas, lhe for pedido para introduzir um PIN de segurança quando efetuar **parar proteção com eliminação de dados** e **frase de acesso de alteração** operações.

Para receber este PIN:

1. Inicie sessão no Portal do Azure.
2. Navegue até à **cofre dos serviços de recuperação** > **definições** > **propriedades**.
3. Em **segurança PIN**, clique em **gerar**. Esta ação abre um painel que contém o PIN introduzidos na interface de utilizador do agente dos serviços de recuperação do Azure.
    Este PIN é válido para apenas cinco minutos e obtém gerado automaticamente após esse período.

### <a name="maintain-a-minimum-retention-range"></a>Manter um período de retenção mínima
Para se certificar de que existem sempre um número válido de pontos de recuperação disponíveis, foram adicionadas as seguintes verificações:

- Para uma retenção diária, um mínimo de **sete** dias da retenção devem ser feitos.
- Para uma retenção semanal, no mínimo **quatro** semanas de retenção devem ser feitas.
- Para uma retenção mensal, um mínimo de **três** meses de retenção devem ser feitas.
- Para uma retenção anual, um mínimo de **um** ano de retenção deve ser feito.

## <a name="notifications-for-critical-operations"></a>Notificações para operações críticas
Normalmente, quando é efetuada uma operação crítico, o administrador da subscrição é enviado uma notificação por e-mail com detalhes sobre a operação. Pode configurar os destinatários de e-mail adicionais para estas notificações utilizando o portal do Azure.

As funcionalidades de segurança mencionadas neste artigo fornecem mecanismos de defesa contra ataques direcionados. Mais importante ainda, se um ataque acontecer, estas funcionalidades dão-lhe a capacidade de recuperar os seus dados.

## <a name="troubleshooting-errors"></a>Resolução de problemas de erros
| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
| Alterações na política |Não foi possível modificar a política de cópia de segurança. Erro: A operação atual falhou devido a um erro interno do serviço [0x29834]. Repita a operação após algum tempo. Se o problema persistir, contacte o suporte da Microsoft. |**Causa:**<br/>Este erro é fornecido quando as definições de segurança estão ativadas, tente reduzir o período de retenção abaixo os valores mínimos especificados acima e estiver numa versão não suportada (as versões suportadas são especificadas na primeira nota deste artigo). <br/>**Ação recomendada:**<br/> Neste caso, deve definir o período de retenção acima a retenção mínimo período especificado (sete dias para diariamente, quatro semanas para semanal, três semanas para mensal ou um ano para anual) continuar com a política relacionadas com atualizações. Opcionalmente, a abordagem preferida seria possível atualizar o agente de cópia de segurança, servidor de cópia de segurança do Azure e/ou DPM UR para tirar partido de todas as atualizações de segurança. |
| Alterar o frase de acesso |Segurança PIN introduzido está incorreta. (ID: 100130) Fornece o PIN de segurança correto para concluir esta operação. |**Causa:**<br/> Este erro é fornecido ao introduzir o PIN de segurança expirou ou era inválida ao efetuar a operação crítico (como alterar o frase de acesso). <br/>**Ação recomendada:**<br/> Para concluir a operação, tem de introduzir o PIN de segurança válido. Para obter o PIN, inicie sessão no portal do Azure e navegue para o Cofre dos serviços de recuperação > Definições > propriedades > gerar PIN de segurança. Utilize este PIN para alterar o frase de acesso. |
| Alterar o frase de acesso |Falha na operação. ID: 120002 |**Causa:**<br/>Este erro é fornecido quando as definições de segurança estão ativadas, tentar alterar o frase de acesso e se a versão não suportada (versões válidas especificadas no primeiro nota deste artigo).<br/>**Ação recomendada:**<br/> Para alterar o frase de acesso, tem de atualizar primeiro o agente de cópia de segurança para a versão mínima 2.0.9052 mínimo, o servidor de cópia de segurança do Azure para atualização mínima 1, e/ou DPM UR12 de R2 de 2012 DPM mínimo ou DPM 2016 UR2 (transferência as ligações abaixo), em seguida, introduza o PIN de segurança válido. Para obter o PIN, inicie sessão no portal do Azure e navegue para o Cofre dos serviços de recuperação > Definições > propriedades > gerar PIN de segurança. Utilize este PIN para alterar o frase de acesso. |

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Cofre de serviços de recuperação do Azure](backup-azure-vms-first-look-arm.md) para ativar estas funcionalidades.
* [Transferir o agente dos serviços de recuperação do Azure mais recente](http://aka.ms/azurebackup_agent) para ajudar a proteger computadores com o Windows e proteger os dados de cópia de segurança contra ataques.
* [Transferir o servidor de cópia de segurança do Azure mais recente](https://aka.ms/latest_azurebackupserver) para ajudar a proteger cargas de trabalho e proteger os dados de cópia de segurança contra ataques.
* [Transferir UR12 para o System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) ou [transferir UR2 para o System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) para ajudar a proteger cargas de trabalho e proteger os dados de cópia de segurança contra ataques.
