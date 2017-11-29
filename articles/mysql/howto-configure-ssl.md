---
title: "Configurar a conectividade SSL liguem de forma segura à base de dados do Azure para MySQL | Microsoft Docs"
description: "Instruções sobre como configurar corretamente a base de dados do Azure para o MySQL e aplicações associadas a corretamente utilizar ligações SSL"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 289d1c4c0ffd2667c49c5625e72780d54a71ceb5
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configurar a conectividade SSL na sua aplicação para ligar de forma segura à base de dados do Azure para MySQL
Base de dados do Azure para MySQL suporta a ligação a base de dados do Azure para o servidor de MySQL para aplicações de cliente, utilizando Secure Sockets Layer (SSL). Imposição de ligações de SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man no meio" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="step-1-obtain-ssl-certificate"></a>Passo 1: Obter o certificado SSL
Transfira o certificado necessário para comunicar através de SSL com a sua base de dados do Azure para o servidor de MySQL de [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) e guarde o ficheiro de certificado no disco local (isto tutorial utiliza c:\ssl por exemplo).
**Para o Microsoft Internet Explorer e Microsoft Edge:** depois de concluída a transferência, mude o nome do certificado para BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Passo 2: Enlace SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Ligar ao servidor utilizando o MySQL Workbench através de SSL
Configure o MySQL Workbench para ligar em segurança através de SSL. O diálogo de ligação de novo programa de configuração, navegue para o **SSL** separador. No **SSL AC ficheiro:** campo, introduza a localização do ficheiro do **BaltimoreCyberTrustRoot.crt.pem**. 
![Guardar personalizado mosaico](./media/howto-configure-ssl/mysql-workbench-ssl.png) para as ligações existentes, pode vincular SSL clicando no ícone de ligação e selecione editar. Em seguida, navegue para o **SSL** separador e vincule o ficheiro de certificado.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Ligar ao servidor utilizando a CLI de MySQL através de SSL
Outra forma de vincular o certificado SSL é utilizar a interface de linha de comandos do MySQL, executando o seguinte comando:
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Passo 3: Impor ligações de SSL no Azure 
### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
No portal do Azure, visite a base de dados do Azure para o servidor de MySQL e, em seguida, clique em **segurança de ligação**. Utilize o botão de alternar para ativar ou desativar o **ligação SSL impor** definição e, em seguida, clique em **guardar**. A Microsoft recomenda a ativar sempre a **ligação SSL impor** a definição de segurança avançada.
![permitir ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Pode ativar ou desativar o **ssl imposição** parâmetro utilizando valores ativado ou desativado respetivamente na CLI do Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Passo 4: Verificar a ligação SSL
Executar o mysql **estado** comando para verificar que tiver estabelecido ligação ao servidor MySQL utilizar SSL:
```dos
mysql> status
```
Confirme a ligação é encriptada, revendo o resultado, o que deve mostrar: **SSL: cifras em utilização é AES256 SHA** 

## <a name="sample-code"></a>Código de exemplo
Para estabelecer uma ligação segura à base de dados do Azure para MySQL através de SSL da sua aplicação, consulte os seguintes exemplos de código:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@myserver4demo', 
        password='yourpassword', 
        database='quickstartdb', 
        host='myserver4demo.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@myserver4demo', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'myserver4demo.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'myserver4demo.mysql.database.azure.com', 
        :username => 'myadmin@myserver4demo',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool, InsecureSkipVerify: true})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@myserver4demo' , 'yourpassword', 'myserver4demo.mysql.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>Java(MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

## <a name="next-steps"></a>Passos seguintes
Reveja as várias opções de conectividade de aplicação seguinte [bibliotecas de ligação para base de dados do Azure para MySQL](concepts-connection-libraries.md)
