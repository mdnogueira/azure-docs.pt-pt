---
title: "Notas de versão do Azure ML Workbench para sprint 1 de Novembro de 2017"
description: "Este documento fornece detalhes sobre as atualizações para a versão de sprint 1 do Azure ML"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: a4945c77be5763ffeda328184149f712572937c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="sprint-1---november-2017"></a>Sprint 1 - Novembro de 2017 

**Número de versão: 0.1.1710.31013**

Bem-vindo à actualização segundo do Workbench do Azure Machine Learning. Vamos continuar a efetuar melhoramentos em torno da segurança, estabilidade e maintainability na aplicação workbench, a CLI e a camada de serviços de back-end. Obrigado muito para enviar-nos smiles e frowns. Muitas do abaixo as atualizações que são efetuados como diretos resultados dos seus comentários. . Mantê-las provenha!

## <a name="notable-new-features"></a>Funcionalidades novas importantes
- Azure ML está agora disponível em duas regiões do Azure novo: **Europa Ocidental** e **Sudeste asiático**. Que se associam as regiões anteriores do **EUA Leste 2**, **Central EUA oeste**, e **leste da Austrália**, colocar o número total de implementado regiões cinco.
- Iremos ativar a sintaxe de código Python realce na aplicação do Workbench para tornar mais fácil ler e editar o código de origem do Python. 
- Agora pode iniciar a sua IDE favorito diretamente a partir de um ficheiro, em vez do projeto todo.  A abertura de um ficheiro no Workbench e, em seguida, clicando em "Editar" inicia o IDE (atualmente VS Code e PyCharm são suportados) para o ficheiro atual e o projeto.  Também pode clicar na seta junto ao botão Editar para editar o ficheiro no editor de texto do Workbench.  Os ficheiros são só de leitura até que clique em Editar, impedir alterações acidentais.
- A biblioteca de desenho popular `matplotlib` versão 2.1.0 agora vem incluído com a aplicação do Workbench.
- A versão do .NET Core foi atualizado para 2.0 para o motor de preparação de dados. Isto remover o requisito de instalação brew openssl durante a instalação da aplicação no macOS. É também paves a forma de dados mais e extraordinária funcionalidades preparação fique num futuro próximo. 
- Iremos tiver ativado uma home page de aplicação específico da versão, para obter mais relevantes notas de versão e pedidos com base na sua versão atual da aplicação de atualização.
- Se o seu nome de utilizador local tem um espaço no mesmo, a aplicação pode agora ser instalada com êxito. 

## <a name="detailed-updates"></a>Atualizações de detalhado
Segue-se uma lista das atualizações de detalhado cada área de componente do Azure Machine Learning neste sprint.

### <a name="installer"></a>Instalador
- O instalador de aplicação limpa agora o diretório de instalação criado por uma versão anterior da aplicação.
- Corrigido um erro que reconduz a acumular-a 100% no macOS Sierra elevada do instalador.
- Agora é uma ligação direta para o diretório do installer do utilizador rever os registos do instalador no caso de falha de instalação.
- Instale agora funciona para os utilizadores que tenham espaço no respetivo nome de utilizador.

### <a name="workbench-authentication"></a>Autenticação do Workbench
- Suporte para a autenticação no Gestor de Proxy.
- O registo é bem sucedida se o utilizador é protegido por uma firewall. 
- Se o utilizador tiver contas de experimentação em várias regiões do Azure e se uma região acontecer fique indisponível, já não bloqueia a aplicação.
- Quando a autenticação não foi concluída e a caixa de diálogo de autenticação está ainda visível, aplicação tenta já não carregar área de trabalho a partir da local cache.

### <a name="workbench-app"></a>Aplicação do Workbench
- Realce da sintaxe de código de Python está ativada no editor de texto.
- O botão Editar no editor de texto permite-lhe editar o ficheiro tem um IDE (VS Code PyCharm são suportados e) ou no editor de texto incorporado.
- Editor de texto está no modo só de leitura, por predefinição. 
- Guarde as alterações de agora do botão visual estado desativado depois do ficheiro atual é guardado e, por conseguinte, já não está modificado.
- Guarda o Workbench _todos os_ guardadas ficheiros quando iniciar uma execução.
- Workbench memorizou que o último utilizado a área de trabalho no computador local, pelo que é aberta automaticamente.
- Apenas uma única instância do Workbench agora é permitida a execução. Anteriormente podem ser executadas várias instâncias que provocou a problemas quando a funcionar no mesmo projeto.
- Menu ficheiro cujo nome foi alterado "Abrir projeto..." para "Adicionar existente pasta como projeto de..." 
- Mudar de separador está muito mais rápida.
- Ligações de ajuda são adicionadas à caixa de diálogo Configurar IDE.
- O formulário de comentários memorizou agora o endereço de e-mail que introduziu a última vez.
- Área de texto de formulário smiles e frowns agora é maior, pelo que pode enviar-nos comentários mais! 
- O `--owner` mudar texto de ajuda na `az ml workspace create` seja corrigido.
- Adicionámos uma caixa de diálogo "Sobre" para ajudar o utilizador facilmente ver e copiar o número de versão da aplicação.
- Um item de menu "Sugerir uma funcionalidade" é adicionado ao menu de ajuda.
- Nome da conta experimentação agora é visível no título da aplicação de barra, precedente o nome da aplicação "Do Azure Machine Learning Workbench".
- Uma home page específico da versão de aplicação é apresentado baseia-se agora na versão da aplicação detetada.

### <a name="data-preparation"></a>Preparação de dados 
- Já não podem ser carregadas externo do web site do mapa Inspector para impedir potenciais problemas de segurança.
- Inspetores histograma e contagem de valores tem agora a opção para apresentar o gráfico na escala logarítmica.
- Quando um cálculo está em curso, barra de qualidade de dados mostra agora uma cor diferentes para assinalar o estado "calcular".
- Métricas de coluna agora mostram as estatísticas de colunas de valor categórico.
- O último caráter no nome da origem de dados já não é truncado.
- Pacote de preparação de dados agora permanece aberta quando mudar separadores, resultando ganhos de desempenho percetível.
- Na origem de dados, quando alternar entre a vista de dados e a vista de métrica, a ordem das colunas agora já é alterado.
- Abrir um inválido `.dprep` ou `.dsource` ficheiro já não faz com que Workbench para falhas.
- Pacote de preparação de dados pode agora utiliza o caminho relativo para o resultado no _escrever CSV_ transformação.
- _Manter a coluna_ transformação agora permite ao utilizador adicionar colunas adicionais quando editado.
- _Substitua_ menu agora, na verdade, inicia _substitua o valor_ caixa de diálogo.
- _Substitua o valor_ transformar agora funções conforme esperado em vez de gerar o erro.
- Pacote de preparação de dados utiliza agora o caminho absoluto quando referenciar os ficheiros de dados fora da pasta do projeto, tornando a possível executar o pacote no contexto local com o caminho absoluto para o ficheiro de dados.
- _Ficheiro completo_ como uma estratégia de amostragem é agora suportada ao utilizar o Azure blob como origem de dados.
- Gerar código Python (a partir do pacote de preparação de dados) acarreta agora CR e LF, tornando amigável no Windows.
- _Escolha as métricas_ pendente oculta agora propriedade ao mudar para vista de dados.
- Workbench pode agora processo parquet ficheiros, mesmo quando está a utilizar runtime do Python. Anteriormente Spark só pode ser utilizado quando o processamento de ficheiros de parquet. 
- Filtrar valores numa coluna com _data_ tipo de dados já não provoca o motor de preparação de dados para falhas.
- Vista de métrica agora respeita a atualizações de estratégia de amostragem.
- Remoto amostragem tarefas agora funciona corretamente.

### <a name="job-execution"></a>Execução da tarefa
- O argumento está agora incluído no registo de histórico de execução.
- Tarefas arrancou CLI agora aparece no painel de tarefas de histórico de execução automaticamente.
- Painel de tarefas mostra agora as tarefas criadas por utilizadores convidados adicionados ao inquilino do AAD.
- Cancelar do painel de tarefas e ações de eliminação são mais estáveis.
- Ao clicar no botão de execução, a mensagem de erro é acionada agora se os ficheiros de configuração estão no formato incorreto.
- Aplicação terminação já não interfere com tarefas arrancou na CLI.
- Tarefas arrancou CLI agora continua a spit saída standard out, mesmo após uma hora de execução.
- Melhor mensagens de erro são apresentadas quando ocorre uma falha do pacote de preparação de dados executar no Python/PySpark.
- `az ml experiment clean`Limpa agora imagens de Docker na VM remoto bem.
- `az ml experiment clean`agora funciona corretamente para o destino local no macOS.
- Mensagens de erro quando executa filtragem Docker local ou remoto são limpos cópias de segurança e mais fáceis de ler.
- Melhor mensagem de erro é apresentada quando o nome de nó principal do cluster de HDInsight não está formatado corretamente quando ligado como um destino de execução.
- Melhor mensagem de erro é apresentada quando o segredo não foi encontrado no serviço de credencial. 
- Biblioteca de MMLSpark é atualizada para suportar o Apache Spark 2.2.
- MMLSpark incluem agora o requerente codificação transformação (Mesh codificação) para documentos médicas.
- `matplotlib`versão 2.1.0 está agora incluída fora os caixa com Workbench.

### <a name="jupyter-notebook"></a>Bloco de Notas do Jupyter
- Pesquisa de nomes do bloco de notas agora funciona corretamente na vista de blocos de notas.
- Agora pode eliminar um bloco de notas na vista de blocos de notas.
- Magic novo `%upload_artifact` é adicionada para carregar ficheiros produzidos no ambiente de execução do bloco de notas para arquivo de dados do histórico de execução.
- Erros de kernel agora estão anexados no estado de tarefa do bloco de notas para depuração mais fácil.
- Servidor do Jupyter agora corretamente encerrado quando o utilizador inicia sessão fora da aplicação.

### <a name="azure-portal"></a>Portal do Azure
- Conta de experimentação e conta de gestão de modelo agora podem ser criadas em duas regiões do Azure novo: Europa Ocidental e Sudeste asiático.
- Esquema de DevTest da conta de gestão de modelo agora só está disponível quando este é o primeiro a ser criado na subscrição. 
- Ligação de ajuda no portal do Azure é atualizada para apontar para a página de documentação correto.
- Campo de descrição é removido da página de detalhes do Docker imagem, uma vez que não é aplicável.
- Foram adicionados detalhes, incluindo as definições de AppInsights e dimensionamento automático para a página de detalhes do serviço web.
- Página de gestão de modelo compõe agora mesmo cookies de terceiros estão desativados no browser. 

### <a name="operationalization"></a>Operationalization
- Serviço Web com "pontuação" no nome já não irá falhar.
- Utilizador pode agora criar um ambiente de implementação com apenas o acesso de contribuinte a um grupo de recursos do Azure ou a subscrição. Já não é necessário acesso de proprietário para a subscrição completa.
- Operationalization CLI agora gostar separador-conclusão automática no Linux.
- Serviço de construção de imagem suporta agora criar imagens para serviços/dispositivos do IoT do Azure.

### <a name="sample-projects"></a>Projetos de exemplo
- [_Classificar Iris_ ](./tutorial-classifying-iris-part-1.md) projeto de exemplo:
    - `iris_pyspark.py`é mudado para `iris_spark.py`.
    - `iris_score.py`é mudado para `score_iris.py`.
    - `iris.dprep`e `iris.dsource` são atualizadas para refletir as atualizações de motor de preparação de dados mais recentes.
    - `iris.ipynb`Bloco de notas é corrigido para funcionar no cluster do HDInsight.
    - Histórico de execução estiver ativado no `iris.ipynb` célula de bloco de notas.
- [_Avançadas preparação de dados utilizando os dados de partilha de bicicleta_ ](./tutorial-bikeshare-dataprep.md) projeto de exemplo passo "A lidar com o erro Value" fixo.
- [_MMLSpark para adultos Census dados_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) projeto de exemplo `docker.runconfig` formato atualizado de JSON para YAML.
- [_Distribuída Hyperparameter otimização_ ](./scenario-distributed-tuning-of-hyperparameters.md) projeto de exemplo`docker.runconfig` formato atualizado de JSON para YAML.
- Novo projeto de exemplo [ _classificação imagem utilizando CNTK_](./scenario-image-classification-using-cntk.md).
