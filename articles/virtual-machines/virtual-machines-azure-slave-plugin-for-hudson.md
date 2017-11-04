---
title: "Como utilizar o serviço do Azure secundário Plug-in com a integração contínua Hudson | Microsoft Docs"
description: "Descreve como utilizar o serviço do Azure secundário Plug-in com a integração contínua Hudson."
services: virtual-machines-linux
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: b2083d1c-4de8-4a19-a615-ccc9d9b6e1d9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: c11b59f8ea432075b147a391de4b7bd3331e639e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Como utilizar o serviço do Azure secundário Plug-in com a integração contínua Hudson
O serviço do Azure secundário Plug-in para Hudson permite-lhe aprovisionar nós secundários no Azure, quando executar distribuída baseia-se.

## <a name="install-the-azure-slave-plug-in"></a>Instalar o serviço Azure secundário Plug-in
1. No dashboard do Hudson, clique em **gerir Hudson**.
2. No **gerir Hudson** página, clique em **gerir plug-ins**.
3. Clique em de **disponível** separador.
4. Clique em **pesquisa** e tipo **Azure** para limitar a lista de plug-ins relevantes.
   
    Se optar por deslocar a lista de plug-ins disponíveis, irá encontrar o serviço do Azure secundário Plug-in no **distribuídas compilar e gestão de clusters** secção o **outros** separador.
5. Selecione a caixa de verificação **Plug-in do Azure multisservidor**.
6. Clique em **Instalar**.
7. Reinicie Hudson.

Agora que o plug-in estiver instalado, seria os passos seguintes para configurar o plug-in com o seu perfil de subscrição do Azure e criar um modelo que será utilizado na criação da VM para o nó subordinado.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurar o serviço Azure secundário Plug-in com o seu perfil de subscrição
Um perfil de subscrição, também referido como publicar as definições, é um ficheiro XML que contém as credenciais seguras e algumas informações adicionais, terá de trabalhar com o Azure no seu ambiente de desenvolvimento. Para configurar o serviço do Azure secundário Plug-in, tem de:

* O id de subscrição
* Um certificado de gestão para a sua subscrição

Pode encontrá-las no seu [perfil subscrição]. Segue-se um exemplo de um perfil de subscrição.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

          <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

              ServiceManagementUrl="https://management.core.windows.net"

              Id="<Subscription ID>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

Assim que tiver o seu perfil de subscrição, siga estes passos para configurar o serviço do Azure secundário Plug-in.

1. No dashboard do Hudson, clique em **gerir Hudson**.
2. Clique em **Configurar sistema**.
3. Desloque para baixo a página para localizar o **nuvem** secção.
4. Clique em **Adicionar nova nuvem > Microsoft Azure**.
   
    ![Adicionar nova na nuvem][add new cloud]
   
    Isto irá mostrar os campos em que tem de introduzir os detalhes da sua subscrição.
   
    ![configurar o perfil][configure profile]
5. Copie o certificado de gestão e o id de subscrição do seu perfil de subscrição e cole-os em campos apropriados.
   
    Quando copiar o certificado de gestão e o id de subscrição, **não** incluir as aspas que coloque os valores.
6. Clique em **verifique configuração**.
7. Quando a configuração é verificada com êxito, clique em **guardar**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurar um modelo de máquina virtual para o serviço Azure secundário Plug-in
Um modelo de máquina virtual define os parâmetros que o plug-in irá utilizar para criar um nó subordinado no Azure. Nos passos seguintes, irá criar modelo para uma VM com Ubuntu.

1. No dashboard do Hudson, clique em **gerir Hudson**.
2. Clique em **Configurar sistema**.
3. Desloque para baixo a página para localizar o **nuvem** secção.
4. Dentro do **nuvem** secção, localizar **adicionar modelo de Máquina Virtual do Azure** e clique no **adicionar** botão.
   
    ![Adicionar modelo de vm][add vm template]
5. Especifique um nome de serviço em nuvem no **nome** campo. Se o nome especificado se refere a um serviço em nuvem existente, a VM será aprovisionada em que o serviço. Caso contrário, o Azure irá criar um novo.
6. No **Descrição** campo, introduza o texto que descreve o modelo que está a criar. Estas informações são apenas para fins de documentary e não são utilizadas no aprovisionamento de uma VM.
7. No **etiquetas** campo, introduza **linux**. Esta etiqueta é utilizada para identificar o modelo que está a criar e, subsequentemente, é utilizada para referenciar o modelo quando criar uma tarefa de Hudson.
8. Selecione uma região onde será criada a VM.
9. Selecione o tamanho da VM adequado.
10. Especifique uma conta de armazenamento onde será criada a VM. Certifique-se de que está a ser a mesma região que o serviço em nuvem que irá utilizar. Se pretender que o armazenamento novo a ser criado, pode deixar este campo em branco.
11. Período de retenção Especifica o número de minutos antes de Hudson elimina um subordinado de inatividade. Deixe esta o valor predefinido de 60.
12. No **utilização**, selecione a condição apropriada quando este nó subordinado será utilizado. Por agora, selecione **utilizar este nó quanto possível**.
    
     Neste momento, o formulário seria semelhante um pouco a isto:
    
     ![configuração de modelo][template config]
13. No **família de imagem ou Id** tem de especificar que imagem do sistema será instalada na VM. Pode selecionar numa lista de famílias de imagem ou especificar uma imagem personalizada.
    
     Se pretender selecionar numa lista de famílias de imagem, introduza o primeiro caráter (maiúsculas e minúsculas) com o nome de família de imagem. Por exemplo, escrevendo **U** será apresentada uma lista de famílias de Ubuntu Server. Depois de selecionar a partir da lista, Jenkins utilizará a versão mais recente que da imagem de sistema de que família quando aprovisionar a VM.
    
     ![Lista de família do SO][OS family list]
    
     Se tiver uma imagem personalizada que pretende utilizar em vez disso, introduza o nome dessa imagem personalizada. Os nomes de imagens personalizadas não são apresentados numa lista, pelo que terá de garantir que o nome é introduzido corretamente.    
    
     Para este tutorial, escreva **U** para trazer uma lista de imagens Ubuntu e selecionar **Ubuntu Server 14.04 LTS**.
14. Para **iniciar método**, selecione **SSH**.
15. Copie o script abaixo e cole o **Init script** campo.
    
         # Install Java
    
         sudo apt-get -y update
    
         sudo apt-get install -y openjdk-7-jdk
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y openjdk-7-jdk
    
         # Install git
    
         sudo apt-get install -y git
    
         #Install ant
    
         sudo apt-get install -y ant
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y ant
    
     O **Init script** será executado depois da VM é criada. Neste exemplo, o script instala Java, o git e ant.
16. No **Username** e **palavra-passe** campos, introduza os seus valores preferidos para a conta de administrador que será criada na sua VM.
17. Clique em **verificar modelo** para verificar se os parâmetros que especificou são válidos.
18. Clique em **Guardar**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Criar uma tarefa de Hudson que é executada num nó subordinado no Azure
Nesta secção, irá criar uma tarefa de Hudson que será executada num nó subordinado no Azure.

1. No dashboard do Hudson, clique em **nova tarefa**.
2. Introduza um nome para a tarefa que está a criar.
3. Para o tipo de tarefa, selecione **criar uma tarefa de estilo livre software**.
4. Clique em **OK**.
5. Na página de configuração da tarefa, selecione **restringir onde é possível executar este projeto**.
6. Selecione **menu nó e a etiqueta** e selecione **linux** (especificamos esta etiqueta ao criar o modelo de máquina virtual na secção anterior).
7. No **criar** secção, clique em **Adicionar passo de compilação** e selecione **executar shell**.
8. Editar o seguinte script, substituindo **{github nome da sua conta}**, **{no nome do projeto}**, e **{diretório do seu projeto}** com adequado valores e cole o editado script na área de texto que aparece.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e {your project directory} ]; then
   
              cd {your project directory}
   
              git pull origin master
   
        else
   
              git clone https://github.com/{your github account name}/{your project name}.git
   
        fi
   
        # change directory to project
   
        cd $currentDir/{your project directory}
   
        #Execute build task
   
        ant
9. Clique em **Guardar**.
10. No dashboard do Hudson, localize a tarefa que acabou de criar e clique em de **agendar uma compilação** ícone.

Hudson, em seguida, irá criar um nó subordinado utilizando o modelo que criou na secção anterior e execute o script que especificou no passo de compilação para esta tarefa.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores do Java do Azure].

<!-- URL List -->

[Centro de Programadores do Java do Azure]: https://azure.microsoft.com/develop/java/
[perfil subscrição]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

