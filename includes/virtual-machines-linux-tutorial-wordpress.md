## <a name="install-wordpress"></a>Instalar o WordPress

Se pretender a pilha, instale uma aplicação de exemplo. Por exemplo, os seguintes passos instalar de open source [WordPress](https://wordpress.org/) plataforma para criar os Web sites e blogues. Incluem outras cargas de trabalho para experimentar [Drupal](http://www.drupal.org) e [Moodle](https://moodle.org/). 

Este programa de configuração do WordPress destina-se a prova de conceito. Para obter mais informações e as definições de instalação de produção, consulte o [WordPress documentação](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Instalar o pacote do WordPress

Execute o seguinte comando:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Configurar WordPress

Configure WordPress para utilizar o MySQL e PHP. Execute o seguinte comando para abrir o editor de texto à sua escolha e criar o ficheiro `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Copie as seguintes linhas ao ficheiro, substituindo a palavra-passe de base de dados para *yourPassword* (mantenha outros valores inalterados). Em seguida, guarde o ficheiro.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```

No diretório de trabalho, crie um ficheiro de texto `wordpress.sql` para configurar a base de dados do WordPress: 

```bash
sudo sensible-editor wordpress.sql
```

Adicionar os comandos seguintes, substituindo a palavra-passe de base de dados para *yourPassword* (mantenha outros valores inalterados). Em seguida, guarde o ficheiro.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```


Execute o seguinte comando para criar a base de dados:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Depois do comando for concluído, elimine o ficheiro `wordpress.sql`.

Mova a instalação do WordPress na raiz de documento de servidor web:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Agora pode concluir a configuração do WordPress e publicar na plataforma. Abra um browser e aceda a `http://yourPublicIPAddress/wordpress`. Substitua o endereço IP público da sua VM. Deve ser semelhante a esta imagem.

![Página de instalação do WordPress](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)