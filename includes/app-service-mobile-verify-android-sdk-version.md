Devido ao desenvolvimento em curso, a versão do Android SDK instalada no Android Studio, não poderá corresponder à versão no código. O SDK Android referenciado neste tutorial está versão 26, a versão mais recente no momento da escrita. O número de versão pode aumentar à medida que novas versões do SDK aparecem, recomendamos que utilize a versão mais recente disponível.

Dois sintomas de incompatibilidade de versão são:

- Quando criar ou reconstruir o projeto, poderá receber mensagens de erro do Gradle como `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Objetos Android padrão no código que deve resolver com base no `import` instruções poderão estar a gerar mensagens de erro.

Se qualquer um destes for apresentada, a versão do SDK Android instalado no Android Studio, poderá não corresponder ao destino SDK do projeto transferido. Para verificar a versão, efetue as seguintes alterações:

1. No Android Studio, clique em **ferramentas** > **Android** > **SDK Manager**. Se não tiver instalado a versão mais recente da plataforma do SDK, em seguida, clique para instalá-lo. Anote o número de versão.

2. No **Explorador de projeto** separador em **Gradle Scripts**, abra o ficheiro **gradle (módulo: aplicação)**. Certifique-se de que o **compileSdkVersion** e **targetSdkVersion** estão definidos para a versão do SDK mais recente instalada. O `build.gradle` poderá ter este aspeto:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
