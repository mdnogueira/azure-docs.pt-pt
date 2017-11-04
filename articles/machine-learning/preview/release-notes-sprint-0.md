---
title: "Notas de versão do ML Workbench do Azure para sprint 0 Outubro de 2017"
description: "Este documento fornece detalhes sobre as atualizações para a versão de sprint 0 do Azure ML"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="sprint-0---october-2017"></a>Sprint 0 - Outubro de 2017 

**Número de versão: 0.1.1710.04013**

Bem-vindo à primeira atualização do Azure Machine Learning Workbench seguir a nossa pré-visualização pública inicial da Conferência Microsoft Ignite de 2017. As principais atualizações nesta versão estão fiabilidade e estabilização de correções.  Alguns dos problemas críticos que iremos abordados incluem:

## <a name="new-features"></a>Novas funcionalidades
- é agora suportado macOS Sierra elevada

## <a name="bug-fixes"></a>Correções de erros
### <a name="workbench-experience"></a>Experiência de Workbench
- Arrastar e largar um ficheiro para o Workbench faz com que o Workbench para falhas.
- A janela de terminal no VS Code configurado como um IDE para o Workbench não reconhece _az ml_ comandos.

### <a name="workbench-authentication"></a>Autenticação do Workbench
Iremos tomado uma série de atualizações para melhorar a vários problemas de início de sessão e autenticação relatados.
- Janela de autenticação mantém popping cópia de segurança, especialmente quando a ligação à Internet não está estável.
- Problemas de Fiabilidade melhorada em torno da expiração do token de autenticação.
- Em alguns casos, a janela de autenticação é apresentado duas vezes.
- Janela principal do Workbench continua a apresentar "autenticar" mensagem quando terminar, na verdade, o processo de autenticação e a caixa de diálogo de pop-up já dispensada.
- Se não houver nenhuma ligação à Internet, a caixa de diálogo de autenticação, aparece com um ecrã branco.

### <a name="data-preparation"></a>Preparação de dados 
- Quando um valor específico é filtrado, erros e os valores em falta são também filtrados.
- Alterar uma estratégia de amostragem remove subsequentes operações de associação existente.
- Substituir o valor em falta a transformação não tem NaN em consideração.
- A inferência de tipo data emite exceção quando encontrado valor nulo.

### <a name="job-execution"></a>Execução da tarefa
- Não há nenhuma mensagem de erro encriptado quando não consegue carregar a pasta do projeto porque excedia o limite de tamanho de execução da tarefa.
- Se o script de Python do utilizador altera o diretório de trabalho, os ficheiros escritos para pastas de saídas não são registados. 
- Se for diferente daquela projeto atual pertence à subscrição do Azure Active Directory, a submissão da tarefa resulta um erro 403.
- Quando Docker não estiver presente, nenhuma mensagem de erro encriptado é devolvida se o utilizador tenta utilizar o Docker como um destino de execução.
- ficheiro .runconfig não é guardado automaticamente quando o utilizador clica na _executar_ botão.

### <a name="jupyter-notebook"></a>Bloco de Notas do Jupyter
- Não é possível iniciar o servidor de bloco de notas se o utilizador utiliza com certos tipos de início de sessão.
- Mensagens de erro de servidor do bloco de notas não superfície nos registos visíveis ao utilizador.

### <a name="azure-portal"></a>Portal do Azure
- Selecionar o tema escuro do portal do Azure faz com que o painel de gestão de modelo a apresentar como uma caixa negra.

### <a name="operationalization"></a>Operationalization
- Reutilizar um manifesto para atualizar um serviço web faz com que uma nova imagem de Docker criada com um nome aleatório.
- Não não possível obter os registos de serviço Web do Kubernetes cluster.
- Enganosa mensagem de erro está impresso quando o utilizador tentou criar uma conta de gestão de modelo ou de uma conta de computação de ML e detetar problemas de permissões.
