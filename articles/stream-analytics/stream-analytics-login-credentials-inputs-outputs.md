---
title: "Análise de fluxo: Rodar credenciais de início de sessão de entradas e saídas | Microsoft Docs"
description: "Saiba como atualizar as credenciais de Stream Analytics entradas e saídas."
keywords: "credenciais de início de sessão"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 42ae83e1-cd33-49bb-a455-a39a7c151ea4
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: a1a927fa9c34b38e54fdb22782e80fd13bf430c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Roda credenciais de início de sessão de entradas e saídas nas tarefas do Stream Analytics
## <a name="abstract"></a>Abstrato
O Azure Stream Analytics atualmente não permite substituir as credenciais de uma entrada/saída enquanto a tarefa está em execução.

Enquanto o Azure Stream Analytics suporta retomar uma tarefa a partir do último saída, queremos partilhar todo o processo para minimizar o desfasamento entre a parar e iniciar a tarefa e rodar as credenciais de início de sessão.

## <a name="part-1---prepare-the-new-set-of-credentials"></a>Parte 1 – preparar o novo conjunto de credenciais:
Esta peça é aplicável para as entradas/saídas seguintes:

* Blob Storage
* Event Hubs
* Base de Dados SQL
* Armazenamento de Tabelas

Para outras entradas/saídas, prossiga com a parte 2.

### <a name="blob-storagetable-storage"></a>Armazenamento de/tabela de armazenamento de BLOBs
1. Ir para a extensão de armazenamento no portal de gestão do Azure:  
   ![graphic1][graphic1]
2. Localize o armazenamento utilizado pela sua tarefa e vá para a mesma:  
   ![graphic2][graphic2]
3. Clique no comando de gerir chaves de acesso:  
   ![graphic3][graphic3]
4. Entre a chave de acesso primária e a chave de acesso secundária, **escolha não utilizada pela sua tarefa**.
5. Voltar a gerar acessos:  
   ![graphic4][graphic4]
6. Copie a chave recentemente criada:  
   ![graphic5][graphic5]
7. Continue a parte 2.

### <a name="event-hubs"></a>Event hubs
1. Ir para a extensão do Service Bus no portal de gestão do Azure:  
   ![graphic6][graphic6]
2. Localize o espaço de nomes de barramento de serviço utilizada pelo seu trabalho e vá para a mesma:  
   ![graphic7][graphic7]
3. Se a sua tarefa utilizar uma política de acesso partilhado no espaço de nomes de barramento de serviço, avance para o passo 6  
4. Aceda ao separador Hubs de eventos:  
   ![graphic8][graphic8]
5. Localize o Hub de eventos utilizado pelo seu trabalho e vá para a mesma:  
   ![graphic9][graphic9]
6. Aceda ao separador Configurar:  
   ![graphic10][graphic10]
7. No nome da política lista pendente, localize a política de acesso partilhado utilizada pela sua tarefa:  
   ![graphic11][graphic11]
8. Entre a chave primária e a chave secundária, **escolha não utilizada pela sua tarefa**.  
9. Voltar a gerar acessos:  
   ![graphic12][graphic12]
10. Copie a chave recentemente criada:  
   ![graphic13][graphic13]
11. Continue a parte 2.  

### <a name="sql-database"></a>Base de Dados SQL
> [!NOTE]
> Nota: terá de ligar ao serviço de base de dados do SQL Server. Vamos mostrar como fazê-lo utilizando a experiência de gestão no portal de gestão do Azure, mas pode optar por utilizar algumas ferramenta do lado do cliente como o SQL Server Management Studio bem.
>
> 

1. Ir para a extensão de bases de dados SQL no portal de gestão do Azure:  
   ![graphic14][graphic14]
2. Localizar a base de dados do SQL Server utilizada pelo seu trabalho e **clique no servidor** ligação na mesma linha:  
   ![graphic15][graphic15]
3. Clique no comando de gerir:  
   ![graphic16][graphic16]
4. Tipo de base de dados mestra:  
   ![graphic17][graphic17]
5. Digite o nome de utilizador, palavra-passe e clique em registo em:  
   ![graphic18][graphic18]
6. Clique em nova consulta:  
   ![graphic19][graphic19]
7. Escreva a consulta seguinte, substituindo < login_name > com o nome de utilizador e de substituir <enterStrongPasswordHere> com a sua nova palavra-passe:  
   `CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8. Clique em executar:  
   ![graphic20][graphic20]
9. Volte ao passo 2 e este tempo clique na base de dados:  
   ![graphic21][graphic21]
10. Clique no comando de gerir:  
   ![graphic22][graphic22]
11. Digite o nome de utilizador, palavra-passe e clique em início de sessão:  
   ![graphic23][graphic23]
12. Clique em nova consulta:  
   ![graphic24][graphic24]
13. Introduza a consulta seguinte, substituindo < user_name > com um nome pelo qual pretende identificar este início de sessão no contexto desta base de dados (pode fornecer o mesmo valor que forneceu para < login_name >, por exemplo) e de substituir < login_name > com o novo nome de utilizador:  
   `CREATE USER <user_name> FROM LOGIN <login_name>`
14. Clique em executar:  
   ![graphic25][graphic25]
15. Agora deve fornecer o seu novo utilizador com as mesmas funções e privilégios tinha o utilizador original.
16. Continue a parte 2.

## <a name="part-2-stopping-the-stream-analytics-job"></a>Parte 2: A parar a tarefa do Stream Analytics
1. Ir para a extensão do Stream Analytics no portal de gestão do Azure:  
   ![graphic26][graphic26]
2. Localize a tarefa e vá para a mesma:  
   ![graphic27][graphic27]
3. Aceda ao separador de entradas ou no separador de saídas com base em se estão rodar as credenciais de entrada ou um resultado.  
   ![graphic28][graphic28]
4. Clique no comando de paragem e confirme que a tarefa foi parada:  
   ![graphic29][graphic29] aguardar pela tarefa parar.
5. Localize a entrada/saída para rodar as credenciais no e vá para a mesma:  
   ![graphic30][graphic30]
6. Avance para a parte 3.

## <a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Parte 3: Editando as credenciais na tarefa do Stream Analytics
### <a name="blob-storagetable-storage"></a>Armazenamento de/tabela de armazenamento de BLOBs
1. Localizar o campo de chave de conta de armazenamento e cole a chave recentemente criada para a mesma:  
   ![graphic31][graphic31]
2. Clique no comando de guardar e confirmar as alterações a guardar:  
   ![graphic32][graphic32]
3. Um teste de ligação irá iniciar automaticamente quando guardar as alterações, certifique-se de que é passou com êxito.
4. Avance para a parte 4.

### <a name="event-hubs"></a>Event hubs
1. Localizar o campo de chave de política de Hub de eventos e cole a chave recentemente criada para a mesma:  
   ![graphic33][graphic33]
2. Clique no comando de guardar e confirmar as alterações a guardar:  
   ![graphic34][graphic34]
3. Um teste de ligação irá iniciar automaticamente quando guardar as alterações, certifique-se de que tem passou com êxito.
4. Avance para a parte 4.

### <a name="power-bi"></a>Power BI
1. Clique na autorização de renovação:  

   ![graphic35][graphic35]
2. Irá receber a confirmação do seguinte:  

   ![graphic36][graphic36]
3. Clique no comando de guardar e confirmar as alterações a guardar:  
   ![graphic37][graphic37]
4. Um teste de ligação irá iniciar automaticamente quando guardar as alterações, certifique-se de passou com êxito.
5. Avance para a parte 4.

### <a name="sql-database"></a>Base de Dados SQL
1. Localizar os campos de nome de utilizador e palavra-passe e cole-os do conjunto de credenciais criado recentemente:  
   ![graphic38][graphic38]
2. Clique no comando de guardar e confirmar as alterações a guardar:  
   ![graphic39][graphic39]
3. Um teste de ligação irá iniciar automaticamente quando guardar as alterações, certifique-se de que tem passou com êxito.  
4. Avance para a parte 4.

## <a name="part-4-starting-your-job-from-last-stopped-time"></a>Parte 4: A iniciar a tarefa de hora da última paragem
1. Navega para fora de entrada/saída:  
   ![graphic40][graphic40]
2. Clique no comando de início:  
   ![graphic41][graphic41]
3. Escolha a hora da última paragem e clique em OK:  
   ![graphic42][graphic42]
4. Avance para a parte 5.  

## <a name="part-5-removing-the-old-set-of-credentials"></a>Parte 5: Remover o antigo conjunto de credenciais
Esta peça é aplicável para as entradas/saídas seguintes:

* Blob Storage
* Event Hubs
* Base de Dados SQL
* Armazenamento de Tabelas

### <a name="blob-storagetable-storage"></a>Armazenamento de/tabela de armazenamento de BLOBs
Repita parte 1 para a chave de acesso que foi utilizado anteriormente pelo seu trabalho para renovar a chave de acesso agora não utilizados.

### <a name="event-hubs"></a>Event hubs
Repita parte 1 para a chave que foi utilizada anteriormente pelo seu trabalho para renovar a chave agora não utilizada.

### <a name="sql-database"></a>Base de Dados SQL
1. Volte para a janela de consulta da parte 1 passo 7 e escreva a consulta seguinte, substituindo < previous_login_name > com o nome de utilizador que foi utilizado anteriormente pelo seu trabalho:  
   `DROP LOGIN <previous_login_name>`  
2. Clique em executar:  
   ![graphic43][graphic43]  

Pode ser obtido a confirmação do seguinte: 

    Command(s) completed successfully.

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png

