---
title: Instalar MongoDB no Windows VM no Azure | Microsoft Docs
description: "Saiba como instalar MongoDB numa VM do Azure a executar o Windows Server 2012 R2 criado com o modelo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: db1a550b9273925b304fe4280f2a1b0e115f856d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalar e configurar o MongoDB uma VM do Windows no Azure
[MongoDB](http://www.mongodb.org) é um popular open source e de elevado desempenho base de dados NoSQL. Este artigo irá orientá-lo a instalar e configurar o MongoDB numa máquina virtual (VM) do Windows Server 2012 R2 no Azure. Também pode [instalar MongoDB numa VM com Linux no Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de instalar e configurar o MongoDB, terá de criar uma VM e, adicionar Idealmente, um disco de dados ao mesmo. Consulte os artigos seguintes para criar uma VM e adicionar um disco de dados:

* Criar uma VM do Windows Server utilizando [portal do Azure](quick-create-portal.md) ou [Azure PowerShell](quick-create-powershell.md).
* Anexar um disco de dados para uma VM do Windows Server utilizando [portal do Azure](attach-managed-disk-portal.md) ou [Azure PowerShell](attach-disk-ps.md).

Para começar a instalar e configurar MongoDB, [inicie sessão no Windows Server VM](connect-logon.md) utilizando o ambiente de trabalho remoto.

## <a name="install-mongodb"></a>Instalar MongoDB
> [!IMPORTANT]
> Funcionalidades de segurança do MongoDB, como a autenticação e o enlace de endereço IP, não estão ativadas por predefinição. Funcionalidades de segurança devem ser ativadas antes de implementar o MongoDB para um ambiente de produção. Para obter mais informações, consulte [autenticação e de segurança do MongoDB](http://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Depois de se ligar à VM utilizando o ambiente de trabalho remoto, abra o Internet Explorer a partir de **iniciar** menu na VM.
2. Selecione **utilizar definições de segurança, privacidade e compatibilidade recomendadas** quando o Internet Explorer primeiro abre e clique em **OK**.
3. Configuração de segurança avançada do Internet Explorer está ativada por predefinição. Adicione o Web site do MongoDB à lista de sites autorizados:
   
   * Selecione o **ferramentas** ícone no canto superior direito.
   * No **opções da Internet**, selecione o **segurança** separador e, em seguida, selecione o **Sites fidedignos** ícone.
   * Clique em de **Sites** botão. Adicionar *https://\*. mongodb.org* à lista de sites fidedignos e, em seguida, feche a caixa de diálogo.
     
     ![Configurar definições de segurança do Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Navegue para o [MongoDB - transfere](http://www.mongodb.org/downloads) página (http://www.mongodb.org/downloads).
5. Se necessário, selecione o **Comunidade servidor** edition e, em seguida, selecione o estável atual mais recente versão para o Windows Server 2008 R2 de 64 bits e posterior. Para transferir o instalador, clique em **transferências (msi)**.
   
    ![Transferir o instalador do MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Execute o instalador depois de concluída a transferência.
6. Ler e aceitar o contrato de licença. Quando lhe for pedido, selecione **concluída** instalar.
7. No ecrã final, clique em **instalar**.

## <a name="configure-the-vm-and-mongodb"></a>Configurar a VM e MongoDB
1. As variáveis de caminho não são atualizadas pelo programa de instalação do MongoDB. Sem o MongoDB `bin` localização na sua variável de caminho, tem de especificar o caminho completo sempre que utilizar um executável do MongoDB. Para adicionar a localização para sua variável de caminho:
   
   * Clique com botão direito do **iniciar** menu e selecione **sistema**.
   * Clique em **definições de sistema avançadas**e, em seguida, clique em **variáveis de ambiente**.
   * Em **variáveis do sistema**, selecione **caminho**e, em seguida, clique em **editar**.
     
     ![Configurar variáveis de caminho](./media/install-mongodb/configure-path-variables.png)
     
     Adicione o caminho para o MongoDB `bin` pasta. MongoDB é normalmente instalado na *c:\Programas\Microsoft Files\MongoDB*. Verifique o caminho de instalação na VM. O exemplo seguinte adiciona o predefinido a uma localização para instalar o MongoDB a `PATH` variável:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.2\bin
     ```
     
     > [!NOTE]
     > Certifique-se de adicionar o ponto e vírgula à esquerda (`;`) para indicar que está a adicionar uma localização para sua `PATH` variável.

2. Crie diretórios de dados e de registo do MongoDB no seu disco de dados. Do **iniciar** menu, selecione **linha de comandos**. Os exemplos seguintes criar os diretórios no disco f:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Iniciar uma instância do MongoDB com o comando seguinte, ajuste o caminho para os dados e diretórios de registo em conformidade:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Pode demorar alguns minutos para o MongoDB alocar os ficheiros do diário de alterações e começar a escutar ligações. Todas as mensagens de registo são direcionadas para o *F:\MongoLogs\mongolog.log* ficheiro como `mongod.exe` servidor é iniciado e atribui ficheiros do diário.
   
   > [!NOTE]
   > A linha de comandos permanece direcionada para esta tarefa enquanto a instância do MongoDB está em execução. Deixe a janela de linha de comandos aberta para continuar a executar o MongoDB. Em alternativa, instale o MongoDB como serviço, conforme detalhado no próximo passo.

4. Para uma experiência mais robusta do MongoDB, instale o `mongod.exe` como um serviço. Criação de um serviço significa que não precisa de deixe uma linha de comandos executar sempre que pretender utilizar o MongoDB. Crie o serviço de forma, ajuste o caminho para os seus diretórios de dados e de registo em conformidade:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```
   
    O comando anterior cria um serviço com o nome MongoDB, com uma descrição do "BD de Mongo". Os parâmetros seguintes também são especificados:
   
   * O `--dbpath` opção especifica a localização do diretório de dados.
   * O `--logpath` opção tem de ser utilizada para especificar um ficheiro de registo, porque o serviço em execução não possui uma janela de comando para apresentar os resultados.
   * O `--logappend` opção especifica que um reinício do serviço de faz com que a saída para acrescentar ao ficheiro de registo existente.
   
   Para iniciar o serviço do MongoDB, execute o seguinte comando:
   
    ```
    net start MongoDB
    ```
   
    Para obter mais informações sobre como criar o serviço do MongoDB, consulte [configurar um serviço do Windows para o MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testar a instância do MongoDB
Com o MongoDB em execução como uma única instância ou instalado como um serviço, pode agora começar a criar e utilizar as bases de dados. Para iniciar a shell de administrativa do MongoDB, abrir outra janela de linha de comandos do **iniciar** menu e introduza o seguinte comando:

```
mongo  
```

Pode listar as bases de dados com o `db` comando. Insira alguns dados da seguinte forma:

```
db.foo.insert( { a : 1 } )
```

Procure dados da seguinte forma:

```
db.foo.find()
```

O resultado é semelhante ao seguinte exemplo:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Saída de `mongo` consola da seguinte forma:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurar regras de grupo de segurança de rede e de firewall
Agora que MongoDB está instalado e em execução, abra uma porta na Firewall do Windows para que pode ligar remotamente ao MongoDB. Para criar uma nova regra de entrada para permitir que a porta TCP 27017, abra uma linha de comandos administrativa do PowerShell e introduza o seguinte comando:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Também pode criar a regra, utilizando o **Firewall do Windows com segurança avançada** ferramenta de gestão gráficas. Crie uma nova regra de entrada para permitir que a porta TCP 27017.

Se necessário, crie uma regra de grupo de segurança de rede para permitir o acesso ao MongoDB de fora da sub-rede de rede virtual do Azure existente. Pode criar as regras do grupo de segurança de rede utilizando o [portal do Azure](nsg-quickstart-portal.md) ou [Azure PowerShell](nsg-quickstart-powershell.md). Tal como acontece com as regras de Firewall do Windows, permitir que a porta TCP 27017 para a interface de rede virtual da sua VM do MongoDB.

> [!NOTE]
> A porta TCP 27017 é a porta predefinida utilizada por MongoDB. Pode alterar esta porta, utilizando o `--port` parâmetro ao iniciar `mongod.exe` manualmente ou a partir de um serviço. Se alterar a porta, certifique-se de que atualiza as regras de Firewall do Windows e o grupo de segurança de rede nos passos anteriores.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a instalar e configurar o MongoDB na sua VM do Windows. Agora pode aceder MongoDB na sua VM do Windows, seguindo os tópicos avançados a [documentação do MongoDB](https://docs.mongodb.com/manual/).

