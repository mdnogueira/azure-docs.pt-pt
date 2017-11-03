---
title: "Criar uma aplicação web Java e o MySQL no Azure"
description: "Saiba como obter uma aplicação Java que liga ao serviço de base de dados do Azure MySQL a funcionar no serviço de aplicações do Azure."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 31af39eef3889bebd18baa2be297c433cc733ed3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Criar uma aplicação web Java e o MySQL no Azure

Este tutorial mostra como criar uma aplicação web Java no Azure e ligue-o a uma base de dados MySQL. Quando tiver terminado, terá um [mola arranque](https://projects.spring.io/spring-boot/) aplicação armazenar dados na [base de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/overview) em execução no [Web Apps do Azure App Service](app-service-web-overview.md).

![Aplicação de Java em execução no serviço de aplicações do Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados MySQL no Azure
> * Ligar uma aplicação de exemplo para a base de dados
> * Implementar a aplicação no Azure
> * Atualizar e reimplementar a aplicação
> * Registos de diagnóstico de fluxo a partir do Azure
> * Monitorizar a aplicação no portal do Azure


## <a name="prerequisites"></a>Pré-requisitos

1. [Transferir e instalar o Git](https://git-scm.com/)
1. [Transfira e instale o JDK do Java 7 ou posterior](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [Transferir, instalar e iniciar o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Preparar o local MySQL 

Neste passo, vai criar uma base de dados no servidor local MySQL para utilização em testar a aplicação localmente no seu computador.

### <a name="connect-to-mysql-server"></a>Ligar ao servidor de MySQL

Numa janela de terminal, ligue ao servidor local MySQL. Pode utilizar esta janela de terminal para executar todos os comandos neste tutorial.

```bash
mysql -u root -p
```

Se lhe for pedida uma palavra-passe, introduza a palavra-passe para o `root` conta. Se não se lembra da sua palavra-passe da conta de raiz, consulte o artigo [MySQL: como repor a palavra-passe de raiz](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se o comando for executado com êxito, em seguida, o servidor de MySQL já está em execução. Se não, certifique-se de que o servidor de MySQL local é iniciado, seguindo o [passos de pós-instalação MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Criar uma base de dados 

No `mysql` solicitar, crie uma base de dados e uma tabela para os itens de tarefas.

```sql
CREATE DATABASE tododb;
```

Sair da sua ligação ao servidor introduzindo `quit`.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Criar e executar a aplicação de exemplo 

Neste passo, clone a aplicação de arranque de mola de exemplo, configure-a para utilizar a base de dados MySQL local e executá-lo no seu computador. 

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela de terminal, navegue para um diretório de trabalho e clone o repositório de exemplo. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Configurar a aplicação para utilizar a base de dados MySQL

Atualização do `spring.datasource.password` e valor no *spring-boot-mysql-todo/src/main/resources/application.properties* com a mesma palavra-passe raiz utilizado para abrir a linha de comandos da MySQL:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Compilar e executar o exemplo

Compilar e executar o exemplo utilizando o wrapper de Maven incluído no repositório:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Abra o browser para `http://localhost:8080` para ver o exemplo na ação. Como adicionar tarefas à lista, utilize os seguintes comandos do SQL Server na linha de MySQL para ver os dados armazenados no MySQL.

```SQL
use testdb;
select * from todo_item;
```

Pare a aplicação por atingir `Ctrl` + `C` no terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Criar uma base de dados MySQL do Azure

Neste passo, cria um [base de dados do Azure para MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) instância utilizando o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Configurar a aplicação de exemplo para utilizar esta base de dados mais tarde no tutorial.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o [criar grupo az](/cli/azure/group#create) comando. Um grupo de recursos do Azure é um contentor lógico onde os recursos relacionados, como as web apps, bases de dados e as contas de armazenamento são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos na região Europa do Norte:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

Para ver os valores possíveis pode utilizar para `--location`, utilize o [az appservice lista-localizações](/cli/azure/appservice#list-locations) comando.

### <a name="create-a-mysql-server"></a>Criar um servidor de MySQL

A Shell de nuvem, criar um servidor na base de dados do Azure para MySQL (pré-visualização) com o [az mysql servidor criar](/cli/azure/mysql/server#create) comando.    
Substitua o seu próprio nome de servidor exclusivo MySQL onde vir o `<mysql_server_name>` marcador de posição. Este nome é parte do nome de anfitrião do seu servidor MySQL, `<mysql_server_name>.mysql.database.azure.com`, por isso, tem de ser globalmente exclusivo. Também substituir `<admin_user>` e `<admin_password>` com os seus próprios valores.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user <admin_user> --admin-password <admin_password>
```

Quando o servidor de MySQL é criado, a CLI do Azure mostra as informações semelhante ao seguinte exemplo:

```json
{
  "administratorLogin": "admin_user",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mysql_server_name.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysql_server_name",
  "location": "northeurope",
  "name": "mysql_server_name",
  "resourceGroup": "mysqlJavaResourceGroup",
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Configurar a firewall do servidor

A Shell de nuvem, criar uma regra de firewall para o servidor de MySQL permitir ligações de cliente utilizando o [az mysql servidor-regra de firewall criar](/cli/azure/mysql/server/firewall-rule#create) comando. 

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Base de dados do Azure para MySQL (pré-visualização) não ativar ligações a partir de serviços do Azure atualmente automaticamente. Como dinamicamente forem atribuídos a endereços IP no Azure, é melhor ativar todos os endereços IP por agora. Como o serviço continua a pré-visualização, melhor métodos para proteger a sua base de dados serão ativados.

## <a name="configure-the-azure-mysql-database"></a>Configurar a base de dados MySQL do Azure

Na janela de terminal do local, ligar ao servidor MySQL no Azure. Utilize o valor que especificou anteriormente para `<admin_user>` e `<mysql_server_name>`.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Criar uma base de dados 

No `mysql` solicitar, crie uma base de dados e uma tabela para os itens de tarefas.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Criar um utilizador com permissões

Criar um utilizador de base de dados e conceda-lhe todos os privilégios `tododb` base de dados. Substitua os marcadores de posição `<Javaapp_user>` e `<Javaapp_password>` com o seu próprio nome de aplicação único.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Sair da sua ligação ao servidor introduzindo `quit`.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Implementar o exemplo para o App Service do Azure

Criar um plano do App Service do Azure com o **livres** utilizando o escalão de preço de [criar plano de serviço aplicacional az](/cli/azure/appservice/plan#create) comando da CLI. O plano de serviço aplicacional define os recursos físicos utilizados para alojar as suas aplicações. Todas as aplicações atribuídas a um plano de serviço aplicacional partilham destes recursos, permitindo-lhe guardar custo quando várias aplicações de alojamento. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando estiver pronto o plano, a CLI do Azure mostra um resultado semelhante ao seguinte exemplo:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Criar uma aplicação Web do Azure

 Na Shell de nuvem, utilize o [az webapp criar](/cli/azure/appservice/web#create) comando da CLI para criar uma definição de aplicação web no `myAppServicePlan` plano do App Service. A definição da aplicação web fornece um URL para aceder à sua aplicação com e configura várias opções para implementar o código para o Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Substitui o `<app_name>` marcador de posição com o seu próprio nome de aplicação único. Este nome exclusivo faz parte do nome de domínio predefinido para a aplicação web, para que o nome tem de ser exclusivo em todas as aplicações no Azure. Pode mapear uma entrada de nome de domínio personalizado para a aplicação web antes de expô-la aos seus utilizadores.

Quando a definição da aplicação web estiver pronta, a CLI do Azure mostra as informações semelhante ao seguinte exemplo: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configurar o Java 

Na Shell de nuvem, definir a configuração de tempo de execução do Java que precisa da aplicação com o [atualização az da configuração de web de serviço aplicacional](/cli/azure/appservice/web/config#update) comando.

O seguinte comando configura a aplicação web com um 8 JDK recentes do Java e [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Configurar a aplicação para utilizar a base de dados SQL do Azure

Antes de executar a aplicação de exemplo, defina as definições da aplicação na aplicação web para utilizar a base de dados do Azure MySQL que criou no Azure. Estas propriedades são expostas à aplicação web como variáveis de ambiente e substituem os valores definidos no application.properties dentro da aplicação web em pacote. 

Na Shell de nuvem, configurar as definições de aplicação utilizando [az webapp configuração appsettings](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) na CLI do:

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>Obter as credenciais de implementação de FTP 
Pode implementar a aplicação do serviço de aplicações do Azure de várias formas, incluindo FTP, Git local, GitHub, Visual Studio Team Services e BitBucket. Neste exemplo, de FTP para implementar o. Ficheiro WAR criado anteriormente no seu computador local para o App Service do Azure.

Para determinar quais as credenciais para passa ao longo de um comando de ftp para a aplicação Web, utilize [az appservice web implementação lista publicação-perfis](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) comando na Shell do Cloud: 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>Carregar a aplicação a utilizar FTP

Utilize a ferramenta FTP favorita para implementar o. Ficheiro WAR para o */site/wwwroot/webapps* o endereço do servidor obtida a partir da pasta a `URL` campo no comando anterior. Remova o diretório de aplicação predefinido (ROOT) existente e substitua o ROOT.war existente com o. Ficheiro WAR incorporado anteriores no tutorial.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>Testar a aplicação web

Navegue até à `http://<app_name>.azurewebsites.net/` e adicionar algumas tarefas à lista. 

![Aplicação de Java em execução no serviço de aplicações do Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Parabéns!** Está a executar uma aplicação de Java condicionada por dados no App Service do Azure.

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

Atualize a aplicação para incluir uma coluna adicional na todo list para que dia o item foi criado. Arranque mola processa a atualizar o esquema de base de dados para si como as alterações de modelo de dados sem alterar os registos da base de dados existente.

1. No sistema local, abrir *src/main/java/com/example/fabrikam/TodoItem.java* e adicione as seguintes importações para a classe:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Adicionar um `String` propriedade `timeCreated` para *src/main/java/com/example/fabrikam/TodoItem.java*, inicializar-o com um carimbo na criação do objeto. Adicionar getters/setters para o novo `timeCreated` propriedade enquanto estiver a editar este ficheiro.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. Atualização *src/main/java/com/example/fabrikam/TodoDemoController.java* com uma linha no `updateTodo` método para definir o timestamp:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Adicione suporte para o novo campo no modelo Thymeleaf. Atualização *src/main/resources/templates/index.html* com um novo cabeçalho de tabela para o carimbo e um novo campo para apresentar o valor da timestamp em cada linha de dados de tabela.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. Reconstrua a aplicação:

    ```bash
    mvnw clean package 
    ```

6. A atualização de FTP. WAR como anteriormente, a remoção existente */wwwroot/webapps/raiz do site* diretório e *ROOT.war*, em seguida, carregar a atualização. Ficheiro WAR como ROOT.war. 

Quando atualiza a aplicação, um **tempo criado** coluna agora está visível. Quando adiciona uma nova tarefa, a aplicação será preenchido o timestamp automaticamente. As suas tarefas existentes permanecem inalteradas e trabalhar com a aplicação, apesar do modelo de dados subjacente foi alterado. 

![Aplicação de Java atualizada com uma nova coluna](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Registos de diagnóstico de fluxo 

Enquanto executa a aplicação Java no App Service do Azure, pode obter os registos de consola direcionados diretamente para o seu terminal. Dessa forma, pode obter as mesmas mensagens de diagnóstico para ajudar a depurar erros de aplicações.

Para iniciar o registo de transmissão em fluxo, utilize o [seguimento de registo de webapp az](/cli/azure/appservice/web/log#tail) comando na Shell de nuvem.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Gerir a sua aplicação web do Azure

Aceda ao portal do Azure para ver a aplicação web que criou.

Para tal, inicie sessão em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviço de Aplicações** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/app-service-web-tutorial-java-mysql/access-portal.png)

Por predefinição, o painel da sua aplicação Web mostra a página **Descrição Geral**. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode efetuar tarefas de gestão, como parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo do painel mostram as várias páginas de configuração que pode abrir.

![Painel Serviço de Aplicações no portal do Azure](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

Estes separadores no painel mostram as muitas funcionalidades excelentes que pode adicionar à sua aplicação Web. A lista seguinte dá-lhe apenas algumas das possibilidades:
* Mapear um nome DNS personalizado
* Vincular um certificado SSL personalizado
* Configurar a implementação contínua
* Aumentar e reduzir verticalmente
* Adicionar a autenticação do utilizador

## <a name="clean-up-resources"></a>Limpar recursos

Se não tiver estes recursos para outro tutorial (consulte [passos](#next)), podem eliminá-los, executando o seguinte comando na Shell do Cloud: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Passos seguintes

> [!div class="checklist"]
> * Criar uma base de dados MySQL no Azure
> * Ligar uma aplicação de Java de exemplo para o MySQL
> * Implementar a aplicação no Azure
> * Atualizar e reimplementar a aplicação
> * Registos de diagnóstico de fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

Avançar para o próximo tutorial para saber como mapear um nome DNS personalizado para a aplicação.

> [!div class="nextstepaction"] 
> [Mapear um nome DNS personalizado existente para as Aplicações Web do Azure](app-service-web-tutorial-custom-domain.md)