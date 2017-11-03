---
title: "Ligar aplicações Azure base de dados MySQL | Microsoft Docs"
description: "Este documento apresenta as cadeias de ligação suportado atualmente para aplicações ligar à base de dados do Azure para MySQL, incluindo, ADO.NET (c#), JDBC, Node.js, ODBC, PHP, Python e Ruby."
services: mysql
author: mswutao
ms.author: wuta
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: c9fe0a892bd5e81d2b33987b6ca55ec753550a01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Como ligar aplicações Azure base de dados MySQL
Este tópico lista os tipos de cadeia de ligação que são suportados pela base de dados do Azure para MySQL, juntamente com os modelos e exemplos. Poderá ter diferentes parâmetros e definições na sua cadeia de ligação.

- Para obter o certificado, consulte [como configurar o SSL](./howto-configure-ssl.md).
- {your_host} = <servername>. mysql.database.azure.com
- {your_user}@{servername} = corretamente o formato de ID de utilizador para autenticação.  Se utilizar apenas o ID de utilizador, a autenticação irá falhar.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

Neste exemplo, o nome do servidor é `myserver4demo`, o nome de base de dados é `wpdb`, o nome de utilizador é `WPAdmin`, e a palavra-passe é `mypassword!2`. Como resultado, a cadeia de ligação deve ser:

```ado.net
Server= "myserver4demo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@myserver4demo"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obter detalhes de cadeia de ligação do portal do Azure
No [portal do Azure](https://portal.azure.com), vá para a base de dados do Azure para o servidor de MySQL e, em seguida, clique em **cadeias de ligação** para obter a lista de cadeias para a sua instância: ![painel de cadeias de ligação a do Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

A cadeia fornece detalhes, tais como o controlador, o servidor e base de dados de outro parâmetros de ligação. Modificar estes exemplos para utilizar os seus parâmetros, tais como o nome de base de dados, palavra-passe e assim sucessivamente. Em seguida, pode utilizar esta cadeia para ligar ao servidor do seu código e aplicações.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre bibliotecas de ligação, consulte [conceitos - bibliotecas de ligação](./concepts-connection-libraries.md).
