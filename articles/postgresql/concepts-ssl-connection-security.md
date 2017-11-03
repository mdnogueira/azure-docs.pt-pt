---
title: Configurar a conectividade SSL na base de dados do Azure para PostgreSQL | Microsoft Docs
description: "As instruções e informações para configurar a base de dados do Azure para PostgreSQL e aplicações associadas a corretamente utilizar ligações SSL."
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql
ms.custom: 
ms.topic: article
ms.date: 11/01/2017
ms.openlocfilehash: 3173964f0315559b0839fd7e659f8f3bd2c30b2a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Configurar a conectividade SSL na base de dados do Azure para PostgreSQL
Base de dados do Azure para PostgreSQL prefers ligar as aplicações de cliente para o serviço de PostgreSQL utilizando Secure Sockets Layer (SSL). Imposição de ligações de SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man no meio" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

Por predefinição, o serviço de base de dados PostgreSQL está configurado para exigir a ligação SSL. Opcionalmente, pode desativar exigir o SSL ligar ao seu serviço de base de dados se a aplicação de cliente não suporta a conectividade SSL. 

## <a name="enforcing-ssl-connections"></a>Impor ligações SSL
Para todas as do Azure da base de dados para servidores de PostgreSQL aprovisionados através do portal do Azure e CLI, a imposição de ligações de SSL está ativada por predefinição. 

Da mesma forma, as cadeias de ligação previamente definidas nas definições do "Cadeias de ligação" sob o servidor no portal do Azure incluem os parâmetros necessários para idiomas comuns ligar ao seu servidor de base de dados através de SSL. O parâmetro SSL varia consoante o conector, por exemplo "ssl = true" ou "sslmode = requerem" ou "sslmode = necessária" e outras variações.

## <a name="configure-enforcement-of-ssl"></a>Configurar a imposição de SSL
Opcionalmente, pode desativar a imposição conectividade SSL. Microsoft Azure recomenda a ativação da sempre **ligação SSL impor** a definição de segurança avançada.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
Visite a base de dados do Azure para o servidor de PostgreSQL e clique em **segurança de ligação**. Utilize o botão de alternar para ativar ou desativar o **ligação SSL impor** definição. Em seguida, clique em **guardar**. 

![Segurança de ligação - desativar impor SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Pode confirmar a definição visualizando o **descrição geral** página para ver o **SSL impor estado** indicador.

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Pode ativar ou desativar o **ssl imposição** utilizando o parâmetro `Enabled` ou `Disabled` valores respetivamente na CLI do Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mypgserver-20170401 --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Certifique-se a aplicação ou estrutura suporta ligações SSL
Muitas estruturas de aplicações comuns que utilizam PostgreSQL para os respetivos serviços de base de dados, como Drupal e Django, não ativam o SSL por predefinição durante a instalação. Ativar a conectividade SSL tem de ser efetuada após a instalação ou através de comandos da CLI específicos para a aplicação. Se o servidor de PostgreSQL é impor ligações SSL e na aplicação associada não está corretamente configurada, a aplicação pode não conseguir ligar ao seu servidor de base de dados. Consulte a documentação da sua aplicação para saber como ativar as ligações de SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicações que necessitam de verificação de certificado para a conectividade SSL
Em alguns casos, as aplicações requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado autoridade de certificação (CA) de fidedigno (. cer) para ligar em segurança. Consulte os seguintes passos para obter o ficheiro. cer, descodificar o certificado e vinculá-lo à sua aplicação.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Transfira o ficheiro de certificado da autoridade de certificação (CA) 
O certificado necessário para comunicar através de SSL com a sua base de dados do Azure para o servidor de PostgreSQL encontra-se [aqui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Transfira o ficheiro de certificado localmente.

### <a name="download-and-install-openssl-on-your-machine"></a>Transfira e instale OpenSSL no seu computador 
Descodificar o ficheiro de certificado necessário para a sua aplicação estabelecer ligação com segurança ao seu servidor de base de dados, terá de instalar OpenSSL no seu computador local.

#### <a name="for-linux-os-x-or-unix"></a>Para o Linux, OS X ou Unix
As bibliotecas de OpenSSL são fornecidas no código fonte diretamente a partir de [OpenSSL Software Foundation](http://www.openssl.org). As seguintes instruções descrevem os passos necessários para instalar OpenSSL no seu PC de Linux. Este artigo utiliza comandos conhecidos para funcionar no Ubuntu 12.04 e planos superiores.

Abra uma sessão de terminal e transferir OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Extraia os ficheiros do pacote transferido.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Introduza o diretório onde os ficheiros foram extraídos. Por predefinição, este deve ser o seguinte.

```bash
cd openssl-1.1.0e
```
Configure OpenSSL executando o seguinte comando. Se pretender que os ficheiros numa pasta diferente /usr/local/openssl, certifique-se alterar o seguinte, conforme apropriado.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Agora que OpenSSL está corretamente configurado, terá de compilá-la para converter o certificado. Para compilar, execute o seguinte comando:

```bash
make
```
Após a conclusão da compilação, está pronto para instalar OpenSSL como um executável, executando o seguinte comando:
```bash
make install
```
Para confirmar que instalou com êxito OpenSSL no seu sistema, execute o seguinte comando e verificação para se certificar de que obtém o mesmo resultado.

```bash
/usr/local/openssl/bin/openssl version
```
Se tiver êxito deverá ver a mensagem seguinte.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Para Windows
Instalar OpenSSL num Windows PC pode ser feito das seguintes formas:
1. **(Recomendado)**  Utilizando a funcionalidade de Bash para Windows incorporada no Windows 10 e superior, OpenSSL é instalado por predefinição. Pode encontrar instruções sobre como ativar a funcionalidade de Bash para Windows no Windows 10 [aqui](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).
2. Através de transferir uma aplicação Win32/64 fornecida pela Comunidade. Enquanto a base de Software de OpenSSL não fornece nem apoia quaisquer programas de instalação do Windows específicos, fornecem uma lista de programas de instalação disponíveis [aqui](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>Descodificar o ficheiro de certificado
O ficheiro transferido da AC de raiz está em formato encriptado. Utilize OpenSSL para descodificar o ficheiro de certificado. Para tal, execute este comando de OpenSSL:

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Ligar à base de dados do Azure para PostgreSQL com autenticação de certificado SSL
Agora que tem de descodificar com êxito o certificado, pode agora ligar ao seu servidor de base de dados em segurança através de SSL. Para permitir a verificação de certificado de servidor, o certificado tem de ser colocado numa ~/.postgresql/root.crt ficheiro no diretório raiz do utilizador. (No Microsoft Windows o ficheiro é denominado % APPDATA%\postgresql\root.crt.). O seguinte fornece instruções para ligar à base de dados do Azure para PostgreSQL.

> [!NOTE]
> Atualmente, não há um problema conhecido se utilizar "sslmode = completo verificar" na sua ligação ao serviço, a ligação irá falhar com o seguinte erro: _certificado de servidor para "&lt;região&gt;. Control.Database.Windows.NET"(e 7 outros nomes) não corresponde ao nome de anfitrião"&lt;servername&gt;. postgres.database.azure.com "._
> Se "sslmode = completo Certifique-se de" é necessário, utilize a Convenção de nomenclatura de servidor  **&lt;servername&gt;. database.windows.net** como o nome do anfitrião da cadeia de ligação. Planeamos remover esta limitação no futuro. Ligações utilizando outros [modos SSL](https://www.postgresql.org/docs/9.6/static/libpq-ssl.html#LIBPQ-SSL-SSLMODE-STATEMENTS) deve continuar a utilizar a Convenção de nomenclatura de anfitrião preferencial  **&lt;servername&gt;. postgres.database.azure.com**.

#### <a name="using-psql-command-line-utility"></a>Utilizar o utilitário de linha de comandos psql
O exemplo seguinte mostra como ligar com êxito ao servidor utilizando o utilitário de linha de comandos psql PostgreSQL. Utilize o `root.crt` ficheiro criado e a `sslmode=verify-ca` ou `sslmode=verify-full` opção.

Utilizar a interface de linha de comandos PostgreSQL, execute o seguinte comando:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mypgserver-20170401.postgres.database.azure.com dbname=postgres user=mylogin@mypgserver-20170401"
```
Se tiver êxito, receberá o seguinte resultado:
```bash
Password for user mylogin@mypgserver-20170401:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>Utilizando a ferramenta de pgAdmin GUI
Configurar pgAdmin 4 para ligar em segurança através de SSL requer que defina o `SSL mode = Verify-CA` ou `SSL mode = Verify-Full` da seguinte forma:

![Captura de ecrã do modo SSL pgAdmin - ligação - requerem](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Passos seguintes
Reveja as várias opções de conectividade de aplicação seguinte [bibliotecas de ligação para base de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
