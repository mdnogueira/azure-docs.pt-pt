---
title: "Configurar o Apache Tomcat numa máquina virtual com Linux | Microsoft Docs"
description: "Saiba como configurar o Apache Tomcat7 através da utilização de máquinas virtuais do Azure com o Linux."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: b76f6f7f53bc649fbc740a79e182f81f3fd983c7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Configurar Tomcat7 numa máquina virtual Linux com o Azure
Apache Tomcat (ou simplesmente Tomcat, também anteriormente denominadas Jakarta Tomcat) é um servidor de web de código aberto e contentor de servlet desenvolvidas por Foundation de Software do Apache (ASF). Tomcat implementa a Servlet Java e as especificações de páginas JavaServer (JSP) de Sun Microsystems. Tomcat disponibiliza um ambiente de servidor de web de Java HTTP puro na qual executar o código de Java. A configuração mais simples, Tomcat é executado num processo de sistema operativo única. Este processo é executada uma máquina virtual de Java (JVM). Todos os pedidos HTTP a partir de um browser para Tomcat é processado como um thread separado no processo de Tomcat.  

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange como utilizar o modelo de implementação clássica. Recomendamos que as implementações mais novas utilizem o modelo do Resource Manager. Para utilizar um modelo do Resource Manager para implementar uma VM com Ubuntu com JDK aberta e Tomcat, consulte [neste artigo](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Neste artigo, irá instalar Tomcat7 numa imagem de Linux e implementá-la no Azure.  

Aprenderá:  

* Como criar uma máquina virtual no Azure.
* Como preparar a máquina virtual para Tomcat7.
* Como instalar Tomcat7.

Pressupõe que já tem uma subscrição do Azure.  Se não, pode inscrever-se numa avaliação gratuita em [Web site do Azure](https://azure.microsoft.com/). Se tiver uma subscrição do MSDN, consulte [preços especial do Microsoft Azure: MSDN, MPN e os benefícios de BizSpark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para saber mais sobre o Azure, consulte [que é o Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Este artigo pressupõe que tem um conhecimento básico de trabalho do Tomcat e Linux.  

## <a name="phase-1-create-an-image"></a>Fase 1: Criar uma imagem
Nesta fase, irá criar uma máquina virtual utilizando uma imagem do Linux no Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Passo 1: Gerar uma chave de autenticação SSH
SSH é uma ferramenta importante para administradores de sistema. No entanto, a configurar a segurança de acesso com base numa palavra-passe determinado por humanos não é recomendada. Utilizadores mal intencionados podem causar uma falha no seu sistema com base num nome de utilizador e uma palavra-passe fraca.

Boas notícias é que haja uma forma de deixar o acesso remoto abertas e não se preocupe sobre palavras-passe. Este método é constituído por autenticação com a criptografia assimétrica. Chave privada do utilizador é o que concede a autenticação. Ainda pode bloquear a conta de utilizador para não permitir a autenticação de palavra-passe.

Outra vantagem deste método é que não precisa de palavras-passe diferentes para iniciar sessão em servidores diferentes. Pode autenticar utilizando a chave privada pessoal em todos os servidores, que impede a terem de se lembrar de várias palavras-passe.



Siga estes passos para gerar a chave de autenticação SSH.

1. Transfira e instale o PuTTYgen da seguinte localização: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Execute Puttygen.exe.
3. Clique em **gerar** para gerar as chaves. No processo, pode aumentar randomness ao mover o rato sobre a área na janela em branco.  
   ![Captura de ecrã gerador de chave puTTY que mostra a gerar novas chaves botão][1]
4. Depois do processo de gerar Puttygen.exe mostrará a nova chave pública.  
   ![Captura de ecrã gerador de chave puTTY que apresenta a nova chave pública e a guardar no botão de chave privado][2]
5. Selecione e copie a chave pública e guarde-o num ficheiro denominado publicKey.pem. Não clique **Guardar chave pública**, porque o formato de ficheiro da chave pública guardado é diferente da chave pública queremos.
6. Clique em **Guardar chave privada**e guarde-o num ficheiro denominado privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Passo 2: Criar a imagem no portal do Azure
1. No [portal](https://portal.azure.com/), clique em **novo** na barra de tarefas para criar uma imagem. Em seguida, selecione a imagem do Linux com base nas suas necessidades. O exemplo seguinte utiliza a imagem de Ubuntu 14.04.
![Captura de ecrã do portal que mostra o botão novo][3]

2. Para **nome de anfitrião**, especifique o nome para o URL que clientes de Internet e irão utilizar para aceder a esta máquina virtual. Defina a última parte do nome de DNS, por exemplo, tomcatdemo. Azure irá então gerar o URL como tomcatdemo.cloudapp.net.  

3. Para **chave de autenticação SSH**, copie o valor da chave do ficheiro publicKey.pem, que contém a chave pública gerada pelo PuTTYgen.  
![Caixa de chave de autenticação SSH no portal][4]

4. Configure outras definições conforme necessário e, em seguida, clique em **criar**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fase 2: Preparar a máquina virtual para Tomcat7
Nesta fase, irá configurar um ponto final para o tráfego do Tomcat e, em seguida, ligar à máquina virtual nova.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Passo 1: Abra a porta HTTP para permitir o acesso web
Pontos finais no Azure é composto por um protocolo TCP ou UDP, juntamente com uma porta pública e privada. A porta privada é a porta que o serviço está a escutar na máquina virtual. A porta pública é a porta que o serviço em nuvem do Azure escuta externamente para o tráfego de entrada, baseado na Internet.  

A porta TCP 8080 é o número de porta predefinidos que Tomcat utiliza para escutar. Se esta porta é aberta com um ponto final do Azure, outros clientes de Internet e podem aceder ao páginas Tomcat.  

1. No portal, clique em **procurar** > **máquinas virtuais**e, em seguida, clique em máquina virtual que criou.  
   ![Captura de ecrã do diretório de máquinas virtuais][5]
2. Para adicionar um ponto final à máquina virtual, clique o **pontos finais** caixa.
   ![Captura de ecrã que mostra a caixa de pontos finais][6]
3. Clique em **Adicionar**.  

   1. Para o ponto final, introduza um nome para o ponto final no **Endpoint**e, em seguida, introduza 80 no **Porta pública**.  

      Se definir para 80, não precisa de incluir o número da porta no URL que é utilizado para aceder Tomcat. Por exemplo, http://tomcatdemo.cloudapp.net.    

      Se pode defini-la para outro valor, tal como 81, terá de adicionar o número de porta para o URL para aceder à Tomcat. Por exemplo, http://tomcatdemo.cloudapp.net:81 /.
   2. Introduza 8080 no **porta privada**. Por predefinição, Tomcat escuta TCP na porta 8080. Se alterar a predefinição escutar a porta do Tomcat, deve atualizar **porta privada** para ser o mesmo que o Tomcat escutar a porta.  
      ![Captura de ecrã de IU que mostra o comando adicionar, Porta pública e privada da porta][7]
4. Clique em **OK** para adicionar o ponto final à máquina virtual.

### <a name="step-2-connect-to-the-image-you-created"></a>Passo 2: Ligar a imagem que criou
Pode escolher qualquer ferramenta SSH para ligar à máquina virtual. Neste exemplo, vamos utilizar o PuTTY.  

1. Obter o nome DNS da sua máquina virtual a partir do portal.
    1. Clique em **procurar** > **máquinas virtuais**.
    2. Selecione o nome da sua máquina virtual e, em seguida, clique em **propriedades**.
    3. No **propriedades** mosaico, consulte o **nome de domínio** caixa para obter o nome DNS.  

2. Obter o número de porta para ligações SSH do **SSH** caixa.  
![Captura de ecrã que mostra o número de porta de ligação de SSH][8]

3. Transferir [PuTTY](http://www.putty.org/).  

4. Depois de transferir, clique no ficheiro executável Putty.exe. Na configuração do PuTTY, configure as opções básicas com o nome de anfitrião e porta número que é obtido a partir das propriedades da máquina virtual.   
![As opções de porta e nome de anfitrião de captura de ecrã que mostra a configuração do PuTTY][9]

5. No painel esquerdo, clique em **ligação** > **SSH** > **Auth**e, em seguida, clique em **procurar** para especificar a localização do ficheiro privateKey.ppk. O ficheiro de privateKey.ppk contém a chave privada que é gerada pelo PuTTYgen anteriormente no "fase 1: criar uma imagem" secção deste artigo.  
![Captura de ecrã que mostra a hierarquia de diretório de ligação e o botão de procura][10]

6. Clique em **abra**. O utilizador poderá ser alertado por uma caixa de mensagem. Se tiver configurado o nome DNS e o número de porta corretamente, clique em **Sim**.
![Captura de ecrã que mostra a notificação][11]

7. Lhe for pedido que introduza o seu nome de utilizador.  
![Captura de ecrã que mostra onde introduzir o nome de utilizador][12]

8. Introduza o nome de utilizador que utilizou para criar a máquina virtual no "fase 1: criar uma imagem" secção anteriormente neste artigo. Verá algo como o seguinte:  
![Captura de ecrã que mostra a confirmação de autenticação][13]

## <a name="phase-3-install-software"></a>Passo 3: Instalar software
Nesta fase, instalar o ambiente de tempo de execução Java, Tomcat7 e outros componentes de Tomcat7.  

### <a name="java-runtime-environment"></a>Ambiente de tempo de execução Java
Tomcat é escrito em Java. Existem dois tipos de Kits de desenvolvimento Java (JDKs), OpenJDK e Oracle JDK. Pode escolher aquele que pretende.  

> [!NOTE]
> Ambos os JDKs têm quase o mesmo código para as classes na Java API, mas o código para a máquina virtual é diferente. OpenJDK tende a utilizar bibliotecas de abrir, enquanto Oracle JDK tende a utilizar aqueles fechado. Oracle JDK tem mais classes e alguns erros de fixo, não sendo Oracle JDK mais estável que OpenJDK.

#### <a name="install-openjdk"></a>Instalar OpenJDK  

Utilize o seguinte comando para transferir OpenJDK.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* Para criar um diretório que contém os ficheiros JDK:  

        sudo mkdir /usr/lib/jvm  
* Para extrair os ficheiros JDK para o diretório/usr/lib/jvm /:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Instalar Oracle JDK


Utilize o seguinte comando para transferir o Oracle JDK do Web site do Oracle.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* Para criar um diretório que contém os ficheiros JDK:  

        sudo mkdir /usr/lib/jvm  
* Para extrair os ficheiros JDK para o diretório/usr/lib/jvm /:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Para definir o Oracle JDK que a máquina de virtual de Java predefinido:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Confirme que a instalação de Java é efetuada com êxito
Pode utilizar um comando semelhante ao seguinte para testar se o ambiente de tempo de execução Java está corretamente instalado:  

    java -version  

Se instalou OpenJDK, deverá ver uma mensagem semelhante ao seguinte: ![mensagem de instalação OpenJDK com êxito][14]

Se tiver instalado o Oracle JDK, deverá ver uma mensagem semelhante ao seguinte: ![mensagem de instalação com êxito Oracle JDK][15]

### <a name="install-tomcat7"></a>Instalar Tomcat7
Utilize o seguinte comando para instalar Tomcat7.  

    sudo apt-get install tomcat7  

Se não estiver a utilizar Tomcat7, utilize a variação adequada deste comando.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Confirme se Tomcat7 instalação foi bem-sucedida
Para verificar se Tomcat7 é instalado com êxito, navegue para o nome DNS do seu servidor Tomcat. Neste artigo, o URL de exemplo é http://tomcatexample.cloudapp.net/. Se vir uma mensagem semelhante ao seguinte, Tomcat7 está corretamente instalado.
![Mensagem de instalação Tomcat7 com êxito][16]

### <a name="install-other-tomcat7-components"></a>Instalar outros componentes de Tomcat7
Existem outros componentes de Tomcat opcionais que pode instalar.  

Utilize o **sudo apt cache pesquisa tomcat7** comando para ver todos os componentes disponíveis. Utilize os seguintes comandos para instalar alguns componentes útil.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Fase 4: Configurar Tomcat7
Nesta fase, administrar Tomcat.

### <a name="start-and-stop-tomcat7"></a>Iniciar e parar Tomcat7
O servidor de Tomcat7 inicia automaticamente quando o instalar. Também pode iniciá-lo com o seguinte comando:   

    sudo /etc/init.d/tomcat7 start

Para parar Tomcat7:

    sudo /etc/init.d/tomcat7 stop

Para ver o estado de Tomcat7:

    sudo /etc/init.d/tomcat7 status

Para reiniciar os serviços de Tomcat: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Administração de Tomcat7
Pode editar o ficheiro de configuração de utilizador do Tomcat para configurar as credenciais de administrador. Utilize o seguinte comando:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Segue-se um exemplo:  
![Captura de ecrã que mostra a saída do comando vi sudo][17]  

> [!NOTE]
> Crie uma palavra-passe segura para o nome de utilizador de admin.  

Após editar este ficheiro, deve reiniciar Tomcat7 serviços com o seguinte comando para se certificar de que as alterações surtam efeito:  

    sudo /etc/init.d/tomcat7 restart  

Abra o browser e introduza **http://<your tomcat server DNS name>/manager/html** como o URL. Por exemplo neste artigo, o URL é http://tomcatexample.cloudapp.net/manager/html.  

Depois de ligar, deverá ver algo semelhante ao seguinte:  
![Captura de ecrã do Gestor de aplicação de Web de Tomcat][18]

## <a name="common-issues"></a>Problemas comuns
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Não é possível aceder a máquina virtual com Tomcat e Moodle a partir da Internet
#### <a name="symptom"></a>Sintoma  
  Tomcat está em execução, mas não pode vir a página predefinida de Tomcat com o seu browser.
#### <a name="possible-root-cause"></a>Causa possível   

  * A porta de escuta de Tomcat não é o mesmo que a porta privada do ponto final da sua máquina virtual para o tráfego de Tomcat.  

     Verifique as definições de ponto final de uma porta privada e a porta pública e certifique-se que a porta privada é que o mesmo que o Tomcat escutar a porta. Consulte "fase 1: criar uma imagem" secção deste artigo para obter instruções sobre como configurar pontos finais para a máquina virtual.  

     Para determinar a porta de escuta do Tomcat, abra /etc/httpd/conf/httpd.conf (versão do Red Hat) ou /etc/tomcat7/server.xml (versão Debian). Por predefinição, a porta de escuta de Tomcat é 8080. Segue-se um exemplo:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Se estiver a utilizar uma máquina virtual, tal como Debian ou Ubuntu e pretender alterar a predefinição porta do Tomcat escutar (por exemplo 8081), deverá também abrir a porta para o sistema operativo. Em primeiro lugar, abra o perfil:  

        sudo vi /etc/default/tomcat7  

     Em seguida, anule os comentários da última linha e altere "não" para "Sim".  

        AUTHBIND=yes
  2. A firewall desativou a porta de escuta do Tomcat.

     Só pode ver a página predefinida de Tomcat do anfitrião local. O problema é mais provável que a porta que é listened por Tomcat, está bloqueada pela firewall. Pode utilizar a ferramenta de w3m para procurar a página Web. Os seguintes comandos instalar w3m e navegue para a página predefinida de Tomcat:  


        sudo yum instalação w3m w3m img


        w3m http://localhost:8080  
#### <a name="solution"></a>Solução

  * Se o Tomcat escutar a porta não é o mesmo que a porta privada do ponto final para o tráfego para a máquina virtual, tem de alterar a porta privada para ser que o mesmo que o Tomcat escutar a porta.   
  2. Se o problema é causado por firewall/iptables, adicione as seguintes linhas ao /etc/sysconfig/iptables. A segunda linha só é necessário para tráfego https:  

      -A -p tcp -m tcp – dport 80 -j aceitar de entrada

      -A -p tcp -m tcp – dport 443 -j aceitar de entrada  

     > [!IMPORTANT]
     > Certifique-se de que as linhas anteriores são posicionadas acima quaisquer linhas que global seriam restringir o acesso, como os seguintes: - A -j REJEITAR – rejeitar-com proibido de anfitrião de icmp de entrada



Recarregar o iptables, execute o seguinte comando:

    service iptables restart

Isto foi testado em CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Permissão negada quando carregar ficheiros de projeto para /var/lib/tomcat7/webapps /
#### <a name="symptom"></a>Sintoma
  Quando utiliza um cliente SFTP (por exemplo, FileZilla) para ligar à máquina virtual e navegue para /var/lib/tomcat7/webapps/para publicar o seu site, receberá uma mensagem de erro semelhante ao seguinte:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Causa possível
  Não tem nenhum permissões para aceder à pasta /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Solução  
  Terá de obter a permissão da conta raiz. Pode alterar a propriedade de pasta de raiz para o nome de utilizador que utilizou quando aprovisionou a máquina. Eis um exemplo com o nome de conta azureuser:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Utilize a opção -R para aplicar as permissões para todos os ficheiros dentro de um diretório demasiado.  

  Este comando também funciona para diretórios. A opção -R altera as permissões para todos os ficheiros e diretórios dentro do diretório. Segue-se um exemplo:  

     sudo chown -R username:group directory  

  Este comando altera a propriedade (utilizadores e de grupo) para todos os ficheiros e diretórios que estão dentro do diretório.  

  O comando seguinte altera apenas a permissão do diretório da pasta. Os ficheiros e pastas dentro do diretório não são alteradas.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
