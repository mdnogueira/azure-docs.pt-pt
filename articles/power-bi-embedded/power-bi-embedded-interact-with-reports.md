<properties
   pageTitle="Interagir com relatórios através da API de JavaScript | Microsoft Azure"
   description="Power BI Embedded, interagir com relatórios através da API de JavaScript"
   services="power-bi-embedded"
   documentationCenter=""
   authors="jocaplan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="08/26/2016"
   ms.author="jocaplan"/>

# Interagir com relatórios do Power BI através da API de JavaScript

A API de JavaScript do Power BI permite-lhe incorporar facilmente os relatórios do Power BI nas suas aplicações. Com a API, as suas aplicações podem interagir, programaticamente, com diferentes elementos dos relatórios, como páginas e filtros. Esta interatividade faz com que os relatórios do Power BI sejam uma parte mais integrante das aplicações.

Para incorporar um relatório do Power BI na sua aplicação, é utilizada uma iframe que é alojada como parte da aplicação. A iframe funciona como limite entre a aplicação e o relatório, como pode ver na imagem seguinte. 

![Iframe incorporada do Power BI sem API de JavaScript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

A iframe facilita bastante o processo de incorporação, mas a aplicação e o relatório não podem interagir um com o outro sem a API do JavaScript. Esta falta de interação pode fazer parecer que o relatório não faz, realmente, parte da aplicação. O relatório e a aplicação têm, efetivamente, de conseguir comunicar mutuamente, como ilustrado na imagem seguinte.

![Iframe incorporada do Power BI com API de JavaScript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

A API de JavaScript do Power BI permite-lhe escrever código que pode ser transmitido em segurança através do limite da iframe. Desta forma, a aplicação pode realizar, programaticamente, uma ação num relatório e pode escutar eventos de ações que os utilizadores fazem no relatório.

## O que pode fazer com a API de JavaScript do Power BI?
Com a API de JavaScript, pode gerir relatórios, navegar para páginas num relatório, filtrar um relatório e processar a incorporação de eventos. O diagrama seguinte mostra a estrutura da API.

![Diagrama da API de JavaScript do Power BI](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### Gerir relatórios
A API de JavaScript permite-lhe gerir o comportamento ao nível do relatório e página:

- Incorporar um Relatório do Power BI específico em segurança na aplicação - experimente a [aplicação de demonstração incorporada](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - Definir o token de acesso
- Configurar o relatório
  - Ativar e desativar o painel do filtro e o painel de navegação da página - experimente a [aplicação de demonstração de atualização de definições](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - Definir predefinições para páginas e filtros - experimente a [demonstração de definição de predefinições](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Entrar e sair do modo de ecrã inteiro

[Saiba mais sobre a incorporação de relatórios](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### Navegar para páginas num relatório
A API de JavaScript permite-lhe descobrir todas as páginas de um relatório e definir a página atual. Experimente a [aplicação de demonstração de navegação](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Saiba mais sobre a navegação nas páginas](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### Filtrar um relatório
A API de JavaScript proporciona capacidades básicas e avançadas de filtragem para relatórios incorporados e para páginas de relatórios. Experimente a [aplicação de demonstração de filtragem](http://azure-samples.github.io/powerbi-angular-client/#/scenario4) e veja alguns códigos introdutórios aqui.  


#### Filtros básicos
É colocado um filtro básico numa coluna ou ao nível da hierarquia, o qual contém uma lista de valores a incluir ou excluir.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### Filtros avançados
Os filtros avançados utilizam o operador lógico AND ou OR e aceitam uma ou duas condições, cada qual com o seu próprio operador e valor. As condições suportadas são:

- Nenhuma
- LessThan
- LessThanOrEqual
- GreaterThan
- GreaterThanOrEqual
- Contains
- DoesNotContain
- StartsWith
- DoesNotStartWith
- Is
- IsNot
- IsBlank
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Saiba mais sobre a filtragem.](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### Processar eventos
Para além de enviar informações para a iframe, a sua aplicação também pode obter informações sobre os eventos seguintes provenientes da iframe:

- Incorporar
  - carregado
  - erro
- Relatórios
  - pageChanged
  - dataSelected (brevemente)

[Saiba mais sobre o processamento de eventos](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## Passos seguintes
Para obter mais informações sobre a API de JavaScript do Power BI, veja as ligações seguintes:

- [JavaScript API Wiki (Wiki da API de JavaScript)](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [Referência de modelo de objeto](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- Amostras
  - [Angular](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [Demonstração em direto](https://microsoft.github.io/PowerBI-JavaScript/demo/)



<!--HONumber=ago16_HO5-->


