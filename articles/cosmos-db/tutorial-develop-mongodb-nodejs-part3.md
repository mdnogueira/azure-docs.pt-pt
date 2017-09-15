---
title: Tutorial de MongoDB, Angular e Node para o Azure - Parte 3 | Microsoft Docs
description: "Parte 3 da série do tutorial sobre como criar uma aplicação MongoDB com Angular e Node no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: 1fb8d7b9f1014f37f0f3afa20605fce10c45a967
ms.contentlocale: pt-pt
ms.lasthandoff: 09/06/2017

---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-3-build-the-ui-with-angular"></a>Criar uma aplicação MongoDB com o Angular e o Azure Cosmos DB - Parte 3: criar a IU com o Angular

Este tutorial com várias parte demonstra como criar uma nova aplicação [API de MongoDB](mongodb-introduction.md) escrita em Node.js com o Express e Angular e ligá-la à sua base de dados do Azure Cosmos DB.

A Parte 3 do tutorial é a continuação da [Parte 2](tutorial-develop-mongodb-nodejs-part2.md) e abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar a IU do Angular
> * Utilizar CSS para definir o aspeto e a funcionalidade
> * Testar a aplicação localmente

## <a name="video-walkthrough"></a>Instruções de vídeo

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, certifique-se de que concluiu os passos na [Parte 2](tutorial-develop-mongodb-nodejs-part2.md) do tutorial.

> [!TIP]
> Este tutorial orienta-o ao longo dos passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="build-the-ui"></a>Criar a IU

1. No Visual Studio Code, clique no botão Parar ![Botão Parar no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png) para parar a aplicação Node.

2. Na Janela da Linha de Comandos do Windows ou na janela de Terminal do Mac, introduza o comando seguinte para gerar um componente heroes. Neste código, g=gerar, c=componente, heroes=nome do componente e utiliza uma estrutura de ficheiros simples (--flat), para que seja criada uma subpasta para o mesmo.

    ```
    ng g c heroes --flat 
    ```

    A janela de terminal apresenta a confirmação dos componentes novos.

    ```bash
    installing component
      create src\client\app\heroes.component.ts
      update src\client\app\app.module.ts 
    ```

    Vamos ver os ficheiros que foram criados e atualizados. 

3. No Visual Studio Code, no painel **Explorer**, navegue para a pasta **src\client\app** nova e abra o ficheiro **heroes.component.ts** novo, criado no passo 2. Este ficheiro de componente TypeScript foi criado pelo comando anterior.

    > [!TIP]
    > Se não vir a pasta da aplicação no Visual Studio Code, introduza CMD + SHIFT P em Mac ou Ctrl + Shift + P no Windows para abrir a Paleta de Comandos e introduza *Reload Window*, para obter a alteração do sistema.

    ![Abra o ficheiro heroes.component.ts](./media/tutorial-develop-mongodb-nodejs-part3/open-folder.png)

4. Na mesma pasta, abra o ficheiro **app.module.ts** e repare que este adicionou `HeroesComponent` à declaração na linha 5 e que também o importou na linha 10.

    ![Abra o ficheiro app-module.ts](./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png)

    Agora que tem o seu componente Heros, crie um novo ficheiro para o HTML do componente heroes. Uma vez que criámos uma aplicação muito simples, o HTML devia ser incluído no mesmo ficheiro que o ficheiro TypeScript, mas vamos dividi-lo e criar um ficheiro separado.

5. No painel **Explorer**, clique com o botão direito do rato na pasta **app**, clique em **Novo Ficheiro** e dê o nome *heroes.component.html* ao mesmo.

6. No ficheiro **heroes.component.ts**, elimine as linhas 5 a 9 

    ```ts
    template: `
        <p>
          heroes Works!
        </p>
      `,
      ```
      e substitua-as com
  
    ```ts
    templateUrl: './heroes.component.html',
    ```

    para referenciar o ficheiro HTML novo.
 
    > [!TIP]
    > Pode utilizar as extensões Angular Essentials e os fragmentos de John Papa para o Visual Studio Code, de modo a acelerar o desenvolvimento. 
    > 1. Clique no botão **Extensions** (Extensões)![Visual Studio Code Extensions button](./media/tutorial-develop-mongodb-nodejs-part3/extensions-button.png).
    > 2. Escreva *angular essentials* na caixa de pesquisa.
    > 3. Clique em **Instalar**. 
    > 4. Clique no botão **Reload** (Recarregar) para utilizar as extensões novas.
    > Em alternativa, transfira a partir de [http://jpapa.me/angularessentials](http://jpapa.me/angularessentials). 
    > ![Extensão Angular Essentials](./media/tutorial-develop-mongodb-nodejs-part3/angular-essentials-extension.png)

7. Regresse ao ficheiro **heroes.component.html** e copie este código. O `<div>` é o contentor para a página inteira. Dentro do contentor, há uma lista de heroes que temos de criar para que, quando clicar num, pode selecioná-lo e editá-lo ou eliminá-lo na IU. Em seguida, no HTML, temos alguns estilos, para que saiba qual deles está selecionado. Há também uma área de edição, que lhe permite adicionar um hero novo ou editar um já existente. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

8. Agora que o HTML já está a postos, temos de adicioná-lo ao ficheiro **heroes.component.ts**, de modo a podermos interagir com o modelo. O código novo adicionado a **heroes.component.ts**, abaixo, adiciona o modelo ao ficheiro de componentes. Foi adicionado um construtor que obtém alguns heroes e inicializa o componente do serviço hero para adquirir todos os dados. Este código também adiciona todos os métodos necessários para processar eventos na IU. Pode copiar o código seguinte por cima do que já existe em **heroes.component.ts**. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html'
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

9. No **Explorer**, abra o ficheiro **app/app.module.ts** e atualize as linhas 13 (adicione uma vírgula) e 14 para adicionar uma importação para um `FormsModule`. A secção de importação deve ter agora o seguinte aspeto:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

10. Adicione uma importação para o módulo FormsModule novo, na linha 3. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>Utilizar CSS para definir o aspeto e a funcionalidade

1. No painel do Explorer, abra o ficheiro **src/client/styles.scss**.

2. Copie o código seguinte para o ficheiro **styles.scss**, substituindo o conteúdo existente do mesmo.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Guarde o ficheiro. 

## <a name="display-the-component"></a>Apresentar o componente

Agora que temos o componente, como podemos proceder para que apareça no ecrã? Vamos modificar os componentes predefinidos em **app.component.ts**.

1. No painel do Explorer, abra **client/app/app.component.ts**.

2. Nas linhas 6 a 8, altere o título para Heroes e ponha o nome do componente que criámos em **heroes.components.ts** (app-heroes) para referenciar esse componente novo A secção de modelo deve ter agora o seguinte aspeto: 

    ```ts
    template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `,
    ```

3. Vamos referenciar outros componentes em **heroes.components.ts**, como o componente heroes, pelo que também temos de o criar. Na linha de comandos da IU do Angular, utilize o seguinte comando para criar um modelo de hero e um ficheiro denominado **hero.ts**, em que g = gerar cl = classe e hero =nome da classe.

    ```bash
    ng g cl hero
    ```

    A janela de terminal apresenta a confirmação da classe nova.

    ```bash
    installing class
    create src\client\app\hero.ts
    ```

4. No painel do Explorer, abra **src\client\app\hero.ts**.

5. Em **hero.ts**, substitua o conteúdo do ficheiro pelo código seguinte, que acrescenta uma classe Hero com um ID, um nome e uma indicação. 

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

6. Regresse a **heroes.components.ts** e repare que, na linha `selectedHero: Hero;` (linha 10), `Hero` tem uma linha vermelha por baixo. 

7. Clique com o botão esquerdo do rato no termo `Hero` e o Visual Studio mostra um ícone de lâmpada no lado esquerdo do bloco de código. 

    ![Lâmpada no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png)

8. Clique na lâmpada e clique em **Import Hero from "client/app/hero"** (Importar Hero de "client/app/hero.") ou em **Import Hero from "./hero"** (Importar Hero de "./hero"). (A mensagem é diferente consoante a sua configuração).

    É apresentada uma nova linha de código na linha 2. Se a linha 2 referenciar cliente/aplicação/hero, modifique-a de modo a que referencie o ficheiro hero da pasta local (./hero). A linha 2 deve ter o seguinte aspeto:

   ```
   import { Hero } from "./hero";
   ``` 

    O modelo fica assim terminado, mas ainda temos de criar o serviço.

## <a name="create-the-service"></a>Criar o Serviço

1. Na linha de comandos da IU do Angular, introduza o comando seguinte para criar um serviço hero em **app.module.ts**, em que g=gerar, s=serviço, hero=nome do serviço, -m=colocar em app.module.

    ```bash
    ng g s hero -m app.module
    ```

    O resultado indica que **hero.service.ts** foi criado e que **app.module.ts** foi atualizado.
  
    ```bash
    installing service
      create src\client\app\hero.service.ts
      update src\client\app\app.module.ts
    ```
    
    Em app.module.ts, foram adicionadas as seguintes linhas de código (linhas 6 e 17):
    
    ```typescript
    import { HeroService } from './hero.service';
    ...
        providers: [HeroService],
    ```

2. No Visual Studio Code, abra **hero.service.ts** e copie o código seguinte, substituindo o conteúdo do ficheiro.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Este código utiliza a versão mais recente do HttpClient que o Angular oferece, que é um módulo que tem de ser fornecido, o que vamos fazer de seguida.

3. No Visual Studio Code, abra **app.module.ts** e importe HttpClientModule mediante a atualização da secção de importação, para que inclua HttpClientModule.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

4. Em **app.module.ts**, adicione a declaração de importação HttpClientModule à lista de importações.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

5. No Visual Studio Code, regresse a **heroes.components.ts**. Repare que, na linha `constructor(private heroService: HeroService) {}` (linha 13), `HeroService` tem uma linha vermelha por baixo. Clique em `HeroService` e obterá a lâmpada no lado esquerdo do bloco de código. Clique na lâmpada e, em seguida, clique em **Import HeroService from "./hero.service ".** (Importar HeroService de "./hero.service ".) ou em **Import HeroService from "client/app/hero.service ".** (Importar HeroService de "client/app/hero.service ".).

    Clicar na lâmpada insere uma linha de código nova na linha 2. Se a linha 2 referenciar a pasta client/app/hero.service, modifique-a de modo a que referencie o ficheiro hero da pasta local (./hero.serivce). A linha 2 deve ter o seguinte aspeto:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

6. Guarde todos os ficheiros no Visual Studio Code.

## <a name="build-the-app"></a>Criar a aplicação

1. Na linha de comandos, introduza o comando seguinte para criar a aplicação Angular. 

    ```bash
    ng b
    ``` 

    Em caso de problemas, a janela do terminal apresenta informações sobre os ficheiros que têm de ser corrigidos. Quando tiver concluído a compilação, os ficheiros novos são adicionados na pasta **dist**. Se quiser, pode rever os ficheiros novos na pasta **dist**.

    Agora, vamos executar a aplicação.

2. No Visual Studio Code, clique em no botão **Depurar** ![ícone de Depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png), no lado esquerdo, clique no botão **Iniciar Depuração** ![Ícone de depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png).

3. Agora, abra um browser e navegue para **localhost:3000** e veja a aplicação a ser executada localmente.

     ![Aplicação Hero em execução local](./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png)

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, fez o seguinte:

> [!div class="checklist"]
> * Criou a IU do Angular
> * Testou a aplicação localmente

Pode avançar para a parte seguinte do tutorial para criar uma conta do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Create an Azure Cosmos DB account using the Azure CLI](tutorial-develop-mongodb-nodejs-part4.md) (Criar uma conta do Azure Cosmos DB com a CLI do Azure)

