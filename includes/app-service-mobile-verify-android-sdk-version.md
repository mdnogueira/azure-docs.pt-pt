Devido ao desenvolvimento em curso, a versão do Android SDK instalada no Android Studio, não poderá corresponder à versão no código. O SDK Android referenciado neste tutorial está versão 23, a versão mais recente no momento da escrita. O número de versão pode aumentar à medida que novas versões do SDK aparecem, recomendamos que utilize a versão mais recente disponível.

Dois sintomas de incompatibilidade de versão são:

- Quando criar ou reconstruir o projeto, poderá receber mensagens de erro do Gradle como "**não foi possível encontrar o destino Google Inc.:Google APIs:n**".
- Objetos Android padrão no código que deve resolver com base no `import` instruções poderão estar a gerar mensagens de erro.

Se qualquer um destes for apresentada, a versão do SDK Android instalado no Android Studio, poderá não corresponder ao destino SDK do projeto transferido. Para verificar a versão, efetue as seguintes alterações:

1. No Android Studio, clique em **ferramentas** > **Android** > **SDK Manager**. Se não tiver instalado a versão mais recente da plataforma do SDK, em seguida, clique para instalá-lo. Anote o número de versão.
2. No **Explorador de projeto** separador em **Gradle Scripts**, abra o ficheiro **gradle (modeule: aplicação)**. Certifique-se de que o **compileSdkVersion** e **buildToolsVersion** estão definidos para a versão do SDK mais recente instalada. As etiquetas podem ter o seguinte aspeto:

             compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
3. No Explorador de projeto de Android Studio, clique no nó do projeto, escolha **propriedades**e na coluna esquerda, escolha **Android**. Certifique-se de que o **compilação do projeto de destino** está definido para a mesma versão do SDK como o **targetSdkVersion**.

No Android Studio, o ficheiro de manifesto já não é utilizado para especificar o destino SDK e a versão mínima do SDK, ao contrário as maiúsculas e minúsculas com o Eclipse.
