---
title: Configurar PostgreSQL numa VM com Linux | Microsoft Docs
description: "Saiba como instalar e configurar PostgreSQL numa máquina virtual com Linux no Azure"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: mingzhan
ms.openlocfilehash: 0bccdc1cfdbda06b57da8cd662373ef137768672
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalar e configurar o PostgreSQL no Azure
PostgreSQL é uma avançadas open source base de dados semelhante a Oracle e DB2. Inclui funcionalidades de preparada para empresa, tais como a conformidade completa ACID, processamento transacional fiável e o controlo de simultaneidade de versão multi. Também suporta as normas, como ANSI SQL e SQL/MED (incluindo wrappers de dados externa para Oracle, MySQL, MongoDB e muitas outras). É altamente extensível com suporte para 12 mais idiomas procedimental, índices GIN e GiST, suporte de dados geográficos e várias funcionalidades como o NoSQL para JSON ou chave-valor baseados em aplicações.

Neste artigo, ficará a saber como instalar e configurar PostgreSQL numa máquina virtual do Azure com o Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Instalar PostgreSQL
> [!NOTE]
> Já tem de ter uma máquina virtual do Azure com o Linux para concluir este tutorial. Para criar e configurar uma VM com Linux antes de continuar, consulte o [tutorial da VM do Linux do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Neste caso, utilize a porta 1999 como a porta PostgreSQL.  

Estabelecer ligação com o Linux VM criada através do PuTTY. Se esta for a primeira vez que está a utilizar uma VM com Linux do Azure, consulte [como utilizar o SSH com o Linux no Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para aprender a utilizar o PuTTY para ligar a uma VM com Linux.

1. Execute o seguinte comando para mudar para a raiz (administrador):
   
        # sudo su -
2. Algumas distribuições têm dependências que tem de instalar antes de instalar PostgreSQL. Para sua distro nesta lista de verificação e execute o comando adequado:
   
   * Red Hat base com Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian base Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Transfira PostgreSQL para o diretório de raiz e, em seguida, deszipe o pacote:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    O procedimento acima se um exemplo. Pode encontrar o endereço de transferência mais detalhado no [índice da/pub/origem/](https://ftp.postgresql.org/pub/source/).
4. Para iniciar a compilação, execute estes comandos:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Se pretender criar tudo o que pode ser incorporado, incluindo a documentação (páginas HTML e man) e módulos adicionais (contrib), execute o seguinte comando em vez disso:
   
        # gmake install-world
   
    Deverá receber a mensagem de confirmação seguinte:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurar PostgreSQL
1. (Opcional) Crie uma ligação simbólica para bastante a referência de PostgreSQL por não incluir o número de versão:
   
        # ln -s /opt/pgsql9.3.5 /opt/pgsql
2. Crie um diretório para a base de dados:
   
        # mkdir -p /opt/pgsql_data
3. Criar um utilizador não raiz e modificar o perfil do utilizador. Em seguida, mude para este novo utilizador (chamado *postgres* no nosso exemplo):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Por motivos de segurança, PostgreSQL utiliza um utilizador não raiz ao inicializar, iniciar ou encerrar a base de dados.
   > 
   > 
4. Editar o *bash_profile* ficheiro, introduzindo os comandos abaixo. Estas linhas serão adicionadas ao fim do *bash_profile* ficheiro:
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. Executar o *bash_profile* ficheiro:
   
        $ source .bash_profile
6. Valide a instalação utilizando o seguinte comando:
   
        $ which psql
   
    Se a instalação for bem sucedida, verá a seguinte resposta:
   
        /opt/pgsql/bin/psql
7. Também pode verificar a versão de PostgreSQL:
   
        $ psql -V
8. Inicializar a base de dados:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Deverá receber a seguinte saída:

![Imagem](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurar PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Execute os seguintes comandos:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modifique as duas variáveis no ficheiro /etc/init.d/postgresql. O prefixo está definido para o caminho de instalação de PostgreSQL: **optar ativamente por participar/pgsql**. PGDATA está definido para o caminho de armazenamento de dados de PostgreSQL: **optar ativamente por participar/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![Imagem](./media/postgresql-install/no2.png)

Altere o ficheiro para torná-lo executável:

    # chmod +x /etc/init.d/postgresql

Inicie PostgreSQL:

    # /etc/init.d/postgresql start

Verifique se o ponto final do PostgreSQL no:

    # netstat -tunlp|grep 1999

Deverá ver o seguinte resultado:

![Imagem](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Ligar à base de dados Postgres
Mude para o utilizador postgres novamente:

    # su - postgres

Crie uma base de dados Postgres:

    $ createdb events

Ligar à base de dados de eventos que acabou de criar:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Criar e eliminar uma tabela de Postgres
Agora que tiver estabelecido ligação à base de dados, pode criar tabelas no mesmo.

Por exemplo, crie uma nova tabela de Postgres de exemplo usando o seguinte comando:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Configurou uma tabela de quatro colunas com os seguintes nomes de colunas e as restrições:

1. A coluna "nome" foi limitou pelo comando VARCHAR ser em 20 carateres.
2. A coluna "prato" indica o item de prato cada pessoa será apresentada. VARCHAR limita este texto para ser 30 carateres.
3. A coluna "confirmada" regista se a pessoa tenha RSVP'd para o potluck. Os valores aceitáveis são "Y" e "N".
4. O "data" coluna mostra quando se inscreveu para o evento. Postgres requer que as datas ser escritas como aaaa-mm-dd.

Deverá ver o seguinte se a tabela foi criada com êxito:

![Imagem](./media/postgresql-install/no4.png)

Também pode verificar a estrutura da tabela, utilizando o seguinte comando:

![Imagem](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adicionar dados a uma tabela
Em primeiro lugar, inserir informações de uma linha:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Deverá ver este resultado:

![Imagem](./media/postgresql-install/no6.png)

Pode adicionar alguns mais pessoas, bem como à tabela. Seguem-se algumas opções, ou pode criar os seus próprios:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Mostrar tabelas
Utilize o seguinte comando para mostrar uma tabela:

    select * from potluck;

O resultado é:

![Imagem](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Eliminar dados numa tabela
Utilize o seguinte comando para eliminar dados numa tabela:

    delete from potluck where name=’John’;

Isto elimina todas as informações na linha "João". O resultado é:

![Imagem](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Atualize dados em tabela
Utilize o seguinte comando para atualizar os dados numa tabela. Para este Sandy tiver confirmado que ela é attending, pelo que iremos irá alterar o respetivo RSVP de "N" para "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Obter mais informações sobre PostgreSQL
Agora que concluiu a instalação do PostgreSQL no VM Linux do Azure, possam desfrutar de utilizá-la no Azure. Para mais informações sobre PostgreSQL, visite o [PostgreSQL site](http://www.postgresql.org/).

