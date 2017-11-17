---
title: "Problemas e o guia de resolução de problemas conhecidos | Microsoft Docs"
description: Lista dos problemas conhecidos e um guia para ajudar a resolver problemas
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: a03fb4f202bddb6454f703c998e95abf13d14fff
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - problemas conhecidos e guia de resolução de problemas 
Este artigo ajuda-o a encontrar e corrija erros ou falhas encontradas como parte da utilização da aplicação do Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Determinar o número de compilação do Workbench
Ao comunicar com a equipa de suporte, é importante incluir o número de compilação da aplicação Workbench. No Windows, pode descobrir o número de compilação, clicando no **ajudar** menu e escolha **sobre o Azure ML Workbench**. No macOS, pode clicar no **do Azure ML Workbench** menu e escolha **sobre o Azure ML Workbench**.

## <a name="machine-learning-msdn-forum"></a>Fórum MSDN do Machine Learning
Temos um fórum MSDN que pode publicar perguntas. A equipa de produto monitoriza ativamente o Fórum. Fórum do URL é [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Recolher informações de diagnóstico
Por vezes, pode ser útil se pode fornecer informações de diagnóstico quando pedir ajuda. Aqui é onde os ficheiros de registo em direto:

### <a name="installer"></a>Instalador
Caso se depare com problema durante a instalação, os ficheiros de registo do instalador são aqui:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Pode zip se os conteúdos de diretórios e envia-as à-nos para obter um diagnóstico.

### <a name="workbench-desktop-app"></a>Aplicação de ambiente de trabalho do Workbench
Se tiver problemas em iniciar sessão, ou se o ambiente de trabalho do Workbench falhar, pode encontrar os ficheiros de registo aqui:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Pode zip se os conteúdos de diretórios e envia-as à-nos para obter um diagnóstico.

### <a name="experiment-execution"></a>Execução da experimentação
Se um determinado script falhar durante a submissão da aplicação de ambiente de trabalho, tente reenviá-lo através da utilização da CLI `az ml experiment submit` comando. Isto deverá dar-lhe mensagem de erro completa no formato JSON e mais importante ainda, pois contém um **ID da operação** valor. Envie-no ficheiro JSON, incluindo o **ID da operação** e podemos ajudar a diagnosticar. 

Se um determinado script for bem sucedida na submissão mas ocorrer uma falha de execução, deve imprimir o **executar ID** para identificar que executar específico. O pacote dos ficheiros de registo relevantes utilizando o seguinte comando:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

O `az ml experiment diagnostics` comandos gera um `diagnostics.zip` ficheiro na pasta de raiz do projeto. O pacote ZIP contém a pasta do projeto todo no Estado momento que foi executada, juntamente com informações de registo. Não se esqueça de remover quaisquer informações confidenciais que não pretende incluir antes de enviar-no ficheiro de diagnóstico.

## <a name="send-us-a-frown-or-a-smile"></a>Envie-num negativos (ou um smile)

Quando estiver a trabalhar no Azure ML Workbench, também pode enviar-num negativos (ou um smile) ao clicar no ícone de rostos em smiley no canto inferior esquerdo da shell de aplicação. Opcionalmente, pode optar por incluir o seu endereço de correio eletrónico (modo, pode regressar ao), e/ou uma captura de ecrã do estado atual. 

## <a name="known-service-limits"></a>Limites de serviço conhecido
- O tamanho de pasta do projeto máximo permitido: 25 MB.
    >[!NOTE]
    >Este limite não se aplica a `.git`, `docs` e `outputs` pastas. Estes nomes de pastas diferenciam maiúsculas de minúsculas. Se estiver a trabalhar com ficheiros grandes, consulte [a persistência de alterações e grau com ficheiros grandes](how-to-read-write-files.md).

- Máx. permitido tempo de execução da experimentação: sete dias

- Tamanho máx. de ficheiros registados no `outputs` pasta após a execução: 512 MB
  - Isto significa que o se o script produz um ficheiro com mais de 512 MB na pasta saídas, não está a recolher não existe. Se estiver a trabalhar com ficheiros grandes, consulte [a persistência de alterações e grau com ficheiros grandes](how-to-read-write-files.md).

- Não são suportadas chaves SSH ao ligar a um computador remoto ou um cluster do Spark através de SSH. Modo de nome de utilizador/palavra-passe apenas é suportado atualmente.

- Ao utilizar o cluster do HDInsight como destino de computação, tem de utilizar Azure blob como armazenamento primário. Não é suportada a utilização de armazenamento do Azure Data Lake.

- Texto transformações de clusters não são suportadas em Mac.

- Biblioteca de RevoScalePy só é suportada no Windows e Linux (em contentores de Docker). Não é suportada no macOS.

## <a name="cant-update-workbench"></a>Não é possível atualizar o Workbench
Quando estiver disponível uma nova atualização, o Workbench aplicação Home page apresenta uma mensagem a informar sobre a atualização de novo. Deverá ver um destaque de atualização, volte a aparecer no canto inferior esquerdo da aplicação no ícone de sino. Clique no destaque e siga o Assistente do instalador para instalar a atualização. 

![Atualizar imagem](./media/known-issues-and-troubleshooting-guide/update.png)

Se não vir a notificação, tente reiniciar a aplicação. Se ainda não vir a notificação de atualização após o reinício, poderão existir algumas causas.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Iniciar o Workbench de um atalho afixado na barra de tarefas
Pode já instalou a atualização. Mas o atalho afixado ainda está a apontar para o bits antigo no disco. Pode verificar isto procurando o `%localappdata%/AmlWorkbench` pasta e verifique se tem a versão mais recente instalada existe e examine o atalho afixado para ver onde está a apontar para a propriedade. Se verificar, basta remover o atalho antigo, inicie o Workbench a partir do menu Iniciar e, opcionalmente, criar um novo atalho afixado na barra de tarefas.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Instalou o Workbench através da ligação "instalar o Workbench do Azure ML" num DSVM do Windows
Infelizmente, não há nenhuma garantia fácil neste. Terá de efetuar os seguintes passos para remover o bits instalado e transferir o instalador mais recente para o Workbench termos instalar: 
   - remover a pasta`C:\Users\<Username>\AppData\Local\amlworkbench`
   - Remova o script`C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - remover o atalho de ambiente de trabalho que inicia o script acima
   - Transferir o instalador https://aka.ms/azureml-wb-msi e volte a instalar.

## <a name="cant-delete-experimentation-account"></a>Não é possível eliminar a conta de experimentação
Pode utilizar a CLI para eliminar uma conta de experimentação, mas tem de eliminar as áreas de trabalho subordinados e os projetos de subordinados dentro desses áreas de trabalho subordinados primeiro. Caso contrário, verá um erro.

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Também pode eliminar os projetos e áreas de trabalho de dentro da aplicação do Workbench.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Não é possível abrir o ficheiro de projeto esteja no OneDrive
Se tiver de atualização de criadores de reversão do Windows 10 e o projeto ser criado numa pasta local mapeada para o OneDrive, pode considerar que não é possível abrir qualquer ficheiro no Workbench. Este é resultam de erros que foi introduzido pela atualização criadores enquadram-se que faz com que o código node.js falhar numa pasta OneDrive. Os erros será corrigido em breve pelo Windows update, mas até lá, não crie projetos numa pasta OneDrive.

## <a name="file-name-too-long-on-windows"></a>Nome do ficheiro demasiado tempo no Windows
Se utilizar o Workbench no Windows, poderá executar num limite de comprimento do nome a predefinição máximo de 260 carateres de ficheiro, a qual foi superfície como um erro "o sistema não é possível localizar o caminho especificado". Pode modificar uma definição de chave de registo para permitir muito mais nome de caminho de ficheiro. Reveja [neste artigo](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) para obter mais detalhes sobre como definir o _MAX_PATH_ chave de registo.

## <a name="docker-error-read-connection-refused"></a>Erro de docker "ler: ligação foi recusada"
Quando executar em relação a um contentor de Docker local, por vezes poderá ver o seguinte erro: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Pode corrigi-lo alterando o servidor de DNS de Docker de `automatic` para um valor fixo de `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Remova o erro de execução de VM "presente nenhum tty"
Quando executar em relação a um contentor de Docker num computador remoto, Linux, poderá encontrar a seguinte mensagem de erro:
```
sudo: no tty present and no askpass program specified.
``` 
Isto pode acontecer se utilizar o portal do Azure para alterar a palavra-passe de raiz de uma VM do Ubuntu Linux. 

Workbench de aprendizagem máquina do Azure necessita de acesso de sudoers sem palavra-passe para ser executada em anfitriões remotos. A forma mais simples para o fazer consiste em utilizar _visudo_ para editar o ficheiro seguinte (pode criar o ficheiro se não existir):

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>É importante editar o ficheiro com _visudo_ e não outro comando. _visudo_ verifica automaticamente, a sintaxe de todos os ficheiros de configuração de sudo e falhas para produzir um ficheiro de sudoers correta, podem impedi-lo fora de sudo.

Insira a seguinte linha no final do ficheiro:

```
username ALL=(ALL) NOPASSWD:ALL
```

Onde _username_ é o nome do Azure Machine Learning Workbench irá utilizar para iniciar sessão no seu anfitrião remoto.

A linha tem de ser colocada após #includedir "/ etc/sudoers.d", caso contrário, poderá ser substituída por outra regra.

Se tiver uma configuração de sudo mais complicada, pode querer consulte a documentação de sudo para Ubuntu disponível aqui: https://help.ubuntu.com/community/Sudoers

O erro acima também pode acontecer se não estiver a utilizar uma VM para baseado no Ubuntu Linux no Azure como um destino de execução. Suportamos apenas Ubuntu-VM com base em Linux para execução remota. 

## <a name="vm-disk-is-full"></a>VM disco está cheio
Por predefinição quando cria uma nova VM com Linux no Azure, que obtém um disco de 30 GB para o sistema operativo. Motor de docker por predefinição utiliza o mesmo disco para a solicitação para baixo de imagens e execução contentores. Isto pode preencher o disco do SO e vir um erro de "VM disco está completo" quando ocorre.

Uma correção rápida consiste em remover todas as imagens de Docker que deixar de utilizar. O seguinte comando do Docker efetua mesmo. (Obviamente tem SSH para a VM para executar o comando de Docker da shell de deteção.)

```
$ docker system prune -a
```

Também pode adicionar um disco de dados e configurar o motor de Docker para utilizar o disco de dados para armazenar imagens. Eis [como adicionar um disco de dados](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk). Pode, em seguida, [alteração onde Docker armazena imagens](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Ou, pode expandir o disco do SO e não tem de touch de configuração do motor de Docker. Eis [como pode expandir o disco de SO](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk).

## <a name="sharing-c-drive-on-windows"></a>Partilha de unidade C no Windows
Se estiver a executar num contentor Docker local no Windows, a definição `sharedVolumes` para `true` no `docker.compute` de ficheiros em `aml_config` pode melhorar o desempenho de execução. No entanto, isto requer a partilha de unidade C a _Docker para Windows ferramenta_. Se não é possível partilhar unidade C, experimente as sugestões seguintes:

* Verificar a partilha na unidade C utilizando o Explorador de ficheiros
* Abra as definições da placa de rede e desinstalar/reinstalar "Partilha de ficheiros e impressoras em redes Microsoft" para vEthernet
* Abra as definições de docker e partilhar a unidade C a partir de definições de docker
* As alterações para a palavra-passe do Windows afetam a partilha. Abra o Explorador de ficheiros, partilhar novamente a unidade C e introduza a palavra-passe nova.
* Também poderá encontrar problema de firewall ao tentar partilhar a sua unidade C com o Docker. Isto [Stack Overflow post](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) podem ser úteis.
* Quando partilha unidade C utilizando as credenciais do domínio, a partilha poderá parar de funcionar em redes onde o controlador de domínio não está acessível (por exemplo, rede doméstica, pública Wi-Fi etc.). Para obter mais informações, consulte [este post](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Também pode evitar o problema de partilha, a um custo, através da definição de desempenho pequeno `sharedVolumne` para `false` no `docker.compute` ficheiro.

## <a name="some-useful-docker-commands"></a>Alguns comandos de Docker útil

Eis alguns comandos de Docker úteis:

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
