### <a name="install-via-composer"></a>Instalar através do compositor
1. Crie um ficheiro denominado **Composer** na raiz do projeto e adicione o seguinte código:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Transferir  **[composer.phar] [ composer-phar]**  na raiz do projeto.
3. Abra uma linha de comandos e execute o seguinte comando na raiz do projeto
   
    ```
    php composer.phar install
    ```

Em alternativa, vá para o [biblioteca de clientes de PHP de armazenamento do Azure] [ php-sdk-github] no GitHub para clonar o código de origem.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
