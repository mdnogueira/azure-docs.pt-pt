---
title: "Como utilizar o SDK de aplicações móveis do Azure para Android | Microsoft Docs"
description: "Como utilizar o SDK de aplicações móveis do Azure para Android"
services: app-service\mobile
documentationcenter: android
author: ggailey777
manager: syntaxc4
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: glenga
ms.openlocfilehash: ac5cbb51a5ed340a6cbf2eeefa41feb337d28fb9
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Como utilizar o SDK de aplicações móveis do Azure para Android

Este guia mostra como utilizar o cliente do Android SDK para Mobile Apps para implementar cenários comuns, tais como:

* Consultar dados (inserir, atualizar e eliminar).
* Autenticação.
* Processamento de erros.
* Personalizar o cliente.

Este guia centra-se sobre o SDK Android do lado do cliente.  Para saber mais sobre os SDKs do lado do servidor para Mobile Apps, consulte [trabalhar com o back-end de .NET SDK] [ 10] ou [como utilizar o back-end Node.js SDK][11].

## <a name="reference-documentation"></a>Documentação de referência

Pode encontrar o [referência da API de Javadocs] [ 12] para a biblioteca de cliente do Android no GitHub.

## <a name="supported-platforms"></a>Plataformas suportadas

O SDK de aplicações móveis do Azure para Android suporta níveis de API 19 através de 24 (KitKat através de Nougat) para o telefone e tablet fatores de formulários.  A autenticação, em particular, utiliza uma abordagem de framework web comum para recolher as credenciais.  Autenticação de servidor fluxo não funciona com dispositivos de fator de formulário pequeno como observa.

## <a name="setup-and-prerequisites"></a>A configuração e pré-requisitos

Concluir o [início rápido de aplicações móveis](app-service-mobile-android-get-started.md) tutorial.  Esta tarefa garante que todos os pré-requisitos para o desenvolvimento de aplicações móveis do Azure foram cumpridos.  O guia de introdução ajuda a configurar a sua conta e criar a sua primeira back-end da aplicação móvel.

Se optar por não concluir o tutorial de início rápido, conclua as seguintes tarefas:

* [criar um back-end de aplicação móvel] [ 13] para utilizar com a sua aplicação Android.
* No Android Studio, [atualização o Gradle compilar ficheiros](#gradle-build).
* [Ativar a permissão de internet](#enable-internet).

### <a name="gradle-build"></a>Atualizar o ficheiro de compilação do Gradle

Alterar ambas **gradle** ficheiros:

1. Adicione este código para o *projeto* nível **gradle** dentro do ficheiro a *buildscript* etiqueta:

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Adicione este código para o *módulo aplicação* nível **gradle** dentro do ficheiro a *dependências* etiqueta:

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    A versão mais recente está atualmente 3.4.0. As versões suportadas são listadas [na gaveta][14].

### <a name="enable-internet"></a>Ativar a permissão de internet

Para aceder ao Azure, a aplicação tem de ter a permissão de INTERNET ativada. Se ainda não estiver ativada, adicione a seguinte linha de código para sua **AndroidManifest.xml** ficheiro:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Criar uma ligação de cliente

Mobile Apps do Azure fornece quatro funções da aplicação móvel:

* Acesso a dados e a Sincronização Offline com um serviço de aplicações móveis do Azure.
* Chame APIs personalizadas escritas com o SDK de servidor de aplicações do Azure Mobile.
* Autenticação com o App Service do Azure autenticação e autorização.
* Registo da notificação com os Hubs de notificação push.

Cada uma destas funções primeiro exige que crie um `MobileServiceClient` objeto.  Apenas um `MobileServiceClient` objecto deve ser criado dentro do seu cliente móvel (ou seja, deve ser um padrão de Singleton).  Para criar um `MobileServiceClient` objeto:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

O `<MobileAppUrl>` é uma cadeia ou um objeto de URL que aponta para o back-end móvel.  Se estiver a utilizar o App Service do Azure para alojar o seu back-end móvel, em seguida, certifique-se de que utiliza a segura `https://` versão do URL.

O cliente também requer acesso à atividade ou contexto - o `this` parâmetro no exemplo.  A construção de MobileServiceClient deverá ocorrer dentro de `onCreate()` método da atividade referenciada no `AndroidManifest.xml` ficheiro.

Como melhor prática, deve de separar as comunicações de servidor na sua própria classe (padrão de singleton).  Neste caso, deverá passar a atividade dentro do construtor para configurar corretamente o serviço.  Por exemplo:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Agora pode chamar `AzureServiceAdapter.Initialize(this);` no `onCreate()` método da sua atividade principal.  Outros métodos de que necessita de acesso para a utilização de cliente `AzureServiceAdapter.getInstance();` para obter uma referência para o adaptador de serviço.

## <a name="data-operations"></a>Operações de dados

É o núcleo do SDK de aplicações móveis do Azure para fornecer acesso aos dados armazenados no SQL Azure no back-end da aplicação móvel.  Pode aceder a estes dados a utilizar classes com tipo seguro (preferidas) ou sem tipos consultas (não recomendadas).  O volume desta secção lida com a utilizar classes com tipo seguro.

### <a name="define-client-data-classes"></a>Definir as classes de dados de cliente

Para aceder aos dados das tabelas de SQL Azure, defina as classes de dados de cliente que correspondem a tabelas no back-end da aplicação móvel. Os exemplos neste tópico partem do princípio de uma tabela com o nome **MyDataTable**, que tem as seguintes colunas:

* ID
* Texto
* Concluir

O objeto do lado do cliente com tipo correspondente reside num ficheiro denominado **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Adicione métodos getter e setter para cada campo que adicionar.  Se a tabela de SQL Azure contiver mais colunas, teria de adicionar os campos correspondentes para esta classe.  Por exemplo, se o DTO (objeto de transferência de dados) tinha uma coluna de prioridade de número inteiro, em seguida, poderá adicionar este campo, juntamente com os seus métodos getter e setter:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Para saber como criar tabelas adicionais no seu back-end do Mobile Apps, consulte [como: definir um controlador de tabela] [ 15] (back-end do .NET) ou [definir tabelas utilizando um esquema dinâmica] [ 16] (Back-end do Node.js).

Uma tabela de back-end do Mobile Apps do Azure define cinco campos especiais, quatro que são disponibilizados aos clientes:

* `String id`: O ID exclusivo global para o registo.  Como melhor prática, certifique-o id a representação de cadeia de um [UUID] [ 17] objeto.
* `DateTimeOffset updatedAt`: A data/hora da última atualização.  O campo de updatedAt é definido pelo servidor e nunca deve ser definido pelo seu código de cliente.
* `DateTimeOffset createdAt`: A data/hora que o objeto foi criado.  O campo de createdAt é definido pelo servidor e nunca deve ser definido pelo seu código de cliente.
* `byte[] version`: Normalmente representado como uma cadeia, a versão seja também definida pelo servidor.
* `boolean deleted`: Indica que o registo tem sido eliminado, mas não removido ainda.  Não utilize `deleted` como uma propriedade de classe.

O `id` campo é obrigatório.  O `updatedAt` campo e `version` campo são utilizados para sincronização offline (para a resolução de sincronização e conflito incremental respetivamente).  O `createdAt` é um campo de referência de campo e não é utilizado pelo cliente.  Os nomes são "entre a transmissão" nomes das propriedades e não são ajustável.  No entanto, pode criar um mapeamento entre o objeto e os nomes de "entre a transmissão" utilizando o [gson] [ 3] biblioteca.  Por exemplo:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getUpdatedAt() { return mCreatedAt; }
    protected DateTimeOffset setUpdatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected DateTimeOffset setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Criar uma referência de tabela

Para aceder a uma tabela, primeiro crie um [MobileServiceTable] [ 8] objeto ao chamar o **getTable** método no [MobileServiceClient] [9].  Este método tem dois sobrecargas:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

No seguinte código, **mClient** é uma referência para o objeto de MobileServiceClient.  A sobrecarga primeiro é utilizada em que o nome de classe e o nome da tabela são os mesmos e é aquele utilizada no início rápido:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

A sobrecarga segundo é utilizada quando o nome da tabela é diferente do nome de classe: o primeiro parâmetro é o nome da tabela.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Consulta uma tabela de back-end

Em primeiro lugar, obtenha uma referência de tabela.  Em seguida, execute uma consulta na referência de tabela.  Uma consulta é qualquer combinação de:

* A `.where()` [cláusula de filtro](#filtering).
* Um `.orderBy()` [ordenação cláusula](#sorting).
* A `.select()` [cláusula de seleção de campo](#selection).
* A `.skip()` e `.top()` para [resultados do bloco paginado](#paging).

As cláusulas devem ser apresentadas na ordem anterior.

### <a name="filter"></a>Filtrar resultados

O formato geral de uma consulta é:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

O exemplo anterior devolve todos os resultados (até ao tamanho de página máximo definido pelo servidor).  O `.execute()` método executa a consulta no back-end.  A consulta é convertida para um [OData v3] [ 19] consulta antes da transmissão para o back-end do Mobile Apps.  Na receção, o back-end do Mobile Apps converte a consulta para uma instrução de SQL Server antes de executá-lo na instância do SQL Azure.  Uma vez que a atividade de rede demora algum tempo, o `.execute()` método devolve um [ `ListenableFuture<E>` ] [ 18].

### <a name="filtering"></a>Filtrar os dados devolvidos

A execução de consulta seguinte devolve todos os itens do **ToDoItem** tabela onde **concluída** é igual a **falso**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** é a referência para a tabela de serviço móvel que criámos anteriormente.

Definir um filtro, utilizando o **onde** método chamada para a referência de tabela. O **onde** método é seguido um **campo** método seguido de um método que especifica o predicado lógico. Métodos de predicado possíveis incluem **eq** (igual a), **ne** (não igual a), **gt** (superior), **ge** (maior ou igual a), **lt** (inferior), **le** (menor ou igual a). Estes métodos permitem-lhe comparar o número e a cadeia de campos para valores específicos.

Pode filtrar as datas. Os seguintes métodos permitem-lhe comparar o campo de data todo ou partes da data: **ano**, **mês**, **dia**, **hora**,  **minuto**, e **segundo**. O exemplo seguinte adiciona um filtro para itens cujo *data devida* é igual a 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Os seguintes métodos de suportam filtros complexos em campos de cadeia: **startsWith**, **endsWith**, **concat**, **subcadeia**, **indexOf**, **substituir**, **toLower**, **toUpper**, **compactar**, e **comprimento** . Os seguintes filtros de exemplo para a tabela de linhas onde o *texto* coluna começa com "PRI0."

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

São suportados os seguintes métodos de operador em campos número: **adicionar**, **sub**, **mul**, **div**, **mod**, **piso**, **limite**, e **arredondar**. Os seguintes filtros de exemplo para a tabela de linhas onde o **duração** é um número par.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Pode combinar os predicados com estes métodos lógicos: **e**, **ou** e **não**. O exemplo seguinte combina dois dos exemplos anteriores.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Agrupar e aninhar operadores lógicos:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Para obter mais debate e exemplos de filtragem, consulte [explorar richness do modelo de consulta de cliente do Android][20].

### <a name="sorting"></a>Ordenação devolvida dados

O seguinte código devolve todos os itens a partir de uma tabela de **ToDoItems** ordenados ascendente pelo *texto* campo. *mToDoTable* é a referência para a tabela de back-end que criou anteriormente:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

O primeiro parâmetro do **orderBy** método é uma cadeia igual ao nome do campo no qual pretende ordenar. O segundo parâmetro utiliza o **QueryOrder** enumeração para especificar se pretende ordenar ascendente ou descendente.  Se está a filtrar o utilizando o ***onde*** método, o ***onde*** método tem de ser invocado antes do ***orderBy*** método.

### <a name="selection"></a>Selecionar colunas específicas

O seguinte código ilustra como devolver todos os itens a partir de uma tabela de **ToDoItems**, mas apenas apresenta as **concluída** e **texto** campos. **mToDoTable** é a referência para a tabela de back-end que criámos anteriormente.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Os parâmetros para a função selecione são os nomes de cadeia de colunas da tabela que se pretender regressar.  O **selecione** método tem de seguir métodos, como **onde** e **orderBy**. Pode ser seguido de métodos de paginação, como **ignorar** e **superior**.

### <a name="paging"></a>Devolver dados nas páginas

Os dados são **sempre** devolvido nas páginas.  O número máximo de registos devolvidos é definido pelo servidor.  Se o cliente solicitar mais registos, o servidor devolve o número máximo de registos.  Por predefinição, o tamanho máximo de página no servidor é 50 registos.

O primeiro exemplo mostra como selecionar os itens de cinco superiores de uma tabela. A consulta devolve os itens de uma tabela com **ToDoItems**. **mToDoTable** é a referência para a tabela de back-end que criou anteriormente:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Segue-se uma consulta que ignora os itens de cinco primeiro e, em seguida, devolve os cinco seguintes:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Se pretender obter todos os registos numa tabela, implemente o código para iterar todas as páginas:

```java
List<MyDataModel> results = new List<MyDataModel>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Um pedido para todos os registos ao utilizar este método cria um mínimo de dois pedidos para o back-end do Mobile Apps.

> [!TIP]
> Escolher o tamanho de página direita é um equilíbrio entre a utilização da memória enquanto o pedido está a acontecer, a utilização de largura de banda e a atraso na completamente a receber os dados.  A predefinição (50 registos) é adequada para todos os dispositivos.  Se utilizar exclusivamente nos dispositivos de memória superiores, aumente até 500.  Ter encontrámos que aumento do tamanho de página para além de 500 registos resulta em atrasos inaceitável e problemas de memória grande.

### <a name="chaining"></a>Como: concatenar métodos de consulta

Os métodos utilizados no consultar tabelas de back-end podem concatenar. Encadeamento de métodos de consulta permite-lhe selecionar colunas específicas de filtrado linhas são ordenadas e bloco paginadas. Pode criar filtros lógicos complexos.  Cada método de consulta devolve um objeto da consulta. Para terminar a série de métodos e, na verdade, execute a consulta, chame o **executar** método. Por exemplo:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Os métodos em cadeia de consulta tem de ser ordenados como segue:

1. Filtragem (**onde**) métodos.
2. A ordenação (**orderBy**) métodos.
3. Seleção (**selecione**) métodos.
4. paginação (**ignorar** e **superior**) métodos.

## <a name="binding"></a>Vincular dados para a interface de utilizador

O enlace de dados envolve três componentes:

* A origem de dados
* O esquema do ecrã
* O adaptador vincula as duas em conjunto.

No nosso código de exemplo, iremos devolver os dados da tabela SQL Azure com aplicações móveis **ToDoItem** para uma matriz. Esta atividade é um padrão comum para aplicações de dados.  Consultas de base de dados, muitas vezes, devolvem uma coleção de linhas que o cliente obtém uma lista ou a matriz. Neste exemplo, a matriz é a origem de dados.  O código especifica um esquema de ecrã que define a vista dos dados que é apresentado no dispositivo.  As duas estão vinculadas juntamente com um adaptador, o qual este código é uma extensão do **ArrayAdapter&lt;ToDoItem&gt;**  classe.

#### <a name="layout"></a>Definir o esquema

O esquema é definido por alguns fragmentos de código XML. Tendo em conta um esquema existente, o seguinte código representa o **ListView** queremos preenchido com os nossos dados do servidor.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

No código anterior, o *listitem* atributo especifica o id do esquema de uma linha individuais na lista. Este código especifica uma caixa de verificação e do respetivo texto associado e obtém instanciado uma vez para cada item na lista. Este esquema não apresentar o **id** campo e um esquema mais complexo de especificar os campos adicionais no ecrã. Este código está no **row_list_to_do.xml** ficheiro.

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Definir o adaptador
Uma vez que a origem de dados da nossa vista é uma matriz de **ToDoItem**, iremos subclasse nosso adaptador de um **ArrayAdapter&lt;ToDoItem&gt;**  classe. Este subclasse produz uma vista para cada **ToDoItem** utilizando o **row_list_to_do** esquema.  No nosso código, iremos definir o que é uma extensão da seguinte classe o **ArrayAdapter&lt;i&gt;**  classe:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Substituir os adaptadores **getView** método. Por exemplo:

```
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Estamos a criar uma instância desta classe no nosso atividade da seguinte forma:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

O segundo parâmetro para o construtor de ToDoItemAdapter é uma referência para o esquema. Iremos agora pode instanciar o **ListView** e atribua o adaptador para o **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Utilizar a placa para enlace com a IU

Agora está pronto para utilizar o enlace de dados. O código seguinte mostra como obter itens na tabela e preenche o adaptador local com os itens devolvidos.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Chamar o adaptador sempre modificar o **ToDoItem** tabela. Uma vez que as modificações são efetuadas numa base de registo por registo, para processar uma única linha, em vez de uma coleção. Inserir um item, chamar o **adicionar** método no adaptador; a eliminar, chamar o **remover** método.

Pode encontrar um exemplo completo no [Android projeto de início rápido][21].

## <a name="inserting"></a>Inserir dados de back-end

Instanciar uma instância do *ToDoItem* classe e definir as respetivas propriedades.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Em seguida, utilize **INSERT ()** de inserção de um objeto:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Correspondências de entidade devolvidos os dados inseridos na tabela de back-end, incluído o ID e quaisquer outros valores (tais como o `createdAt`, `updatedAt`, e `version` campos) definida no back-end.

As tabelas de Mobile Apps requerem uma coluna de chave primária com o nome **id**. Esta coluna tem de ser uma cadeia. O valor predefinido da coluna ID é um GUID.  Pode fornecer outros valores exclusivos, como endereços de e-mail ou nomes de utilizador. Quando um valor de ID de cadeia não é fornecido para um registo inserido, o back-end gera um novo GUID.

Valores de ID de cadeia fornecem as seguintes vantagens:

* Os iDs podem ser gerados sem o tornar uma ida e volta para a base de dados.
* Os registos são mais fáceis de intercalação de tabelas diferentes ou bases de dados.
* Valores de ID melhor integram com a lógica de uma aplicação.

Os valores de ID de cadeia são **REQUIRED** para suporte de sincronização offline.  Não é possível alterar um Id assim que esta está armazenada na base de dados de back-end.

## <a name="updating"></a>Atualize dados numa aplicação móvel

Para atualizar os dados numa tabela, passar o objeto novo para o **Update ()** método.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Neste exemplo, *item* é uma referência a uma linha no *ToDoItem* tabela, o que tenha tido algumas das alterações efetuadas ao mesmo.  A linha com o mesmo **id** é atualizado.

## <a name="deleting"></a>Eliminar dados de uma aplicação móvel

O código seguinte mostra como eliminar dados de uma tabela, especificando o objeto de dados.

```java
mToDoTable
    .delete(item);
```

Também pode eliminar um item, especificando o **id** campo da linha para eliminar.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Procurar um item específico por Id

Procurar um item com específico **id** campo com o **lookUp()** método:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Como: trabalhar com dados sem tipo

O modelo de programação sem tipo dá-lhe controlo exato através de serialização JSON.  Existem alguns cenários comuns em que pode pretender utilizar um modelo de programação sem tipo. Por exemplo, se a tabela de back-end contém demasiadas colunas e só precisa de fazer referência a um subconjunto de colunas.  O modelo com tipo requer que defina todas as colunas definidas no back-end do Mobile Apps na sua classe de dados.  Na maioria das chamadas de API para aceder aos dados é semelhante às chamadas de programação com tipo. A principal diferença é que o modelo sem tipo invocar métodos no **MobileServiceJsonTable** objeto, em vez do **MobileServiceTable** objeto.

### <a name="json_instance"></a>Criar uma instância de uma tabela sem tipo

Semelhante ao modelo de tipo, comece por obter uma referência de tabela, mas neste caso, é um **MobileServicesJsonTable** objeto. Obter a referência ao chamar o **getTable** método numa instância do cliente:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Assim que tiver criado uma instância do **MobileServiceJsonTable**, virtualmente tem a API mesma disponível como com o modelo de programação com tipo. Em alguns casos, os métodos assumem um parâmetro sem tipo em vez de um parâmetro com tipo.

### <a name="json_insert"></a>Inserir uma tabela sem tipo
O código seguinte mostra como fazer uma inserção. O primeiro passo é criar um [JsonObject][1], que faz parte o [gson] [ 3] biblioteca.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Em seguida, utilize **INSERT ()** para inserir o objeto sem tipo na tabela.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Se precisar de obter o ID do objeto inserido, utilize o **getAsJsonPrimitive()** método.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Eliminar uma tabela sem tipo
O código seguinte mostra como eliminar uma instância, neste caso, a mesma instância de um **JsonObject** que foi criado no antes do *inserir* exemplo. O código é o mesmo, tal como acontece com as maiúsculas e minúsculas com tipo, mas o método tem uma assinatura diferente, porque referencia uma **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Também pode eliminar uma instância diretamente, utilizando o respetivo ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Devolver todas as linhas a partir de uma tabela sem tipo
O código seguinte mostra como obter uma tabela inteira. Uma vez que estiver a utilizar uma tabela em JSON, pode obter seletivamente apenas algumas das colunas da tabela.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

O mesmo conjunto de filtragem, filtragem e métodos que estão disponíveis para o modelo com tipo de paginação estão disponíveis para o modelo sem tipo.

## <a name="offline-sync"></a>Implementar a Sincronização Offline

O cliente SDK do Azure Mobile Apps também implementa sincronização offline dos dados através da utilização de uma base de dados do SQLite para arquivar uma cópia dos dados de servidor localmente.  Operações executadas numa tabela offline não necessitam de conectividade móvel para trabalhar.  Ajudas de sincronização offline no resiliência e desempenho em detrimento lógica mais complexas para resolução de conflitos.  O cliente SDK do Azure Mobile Apps implementa as seguintes funcionalidades:

* Sincronização incremental: Registos nova e atualizados apenas são transferidos, guardar o consumo de memória e largura de banda.
* Simultaneidade otimista: Operações são consideradas como concluída com êxito.  Resolução de conflitos é deferida até que as atualizações são executadas no servidor.
* Resolução de conflitos: O SDK Deteta quando uma alteração em conflito foram efetuada no servidor e fornece hooks para alertar o utilizador.
* Eliminação de forma recuperável: Registos eliminados são marcados eliminados, permitir que os outros dispositivos atualizar a respetiva cache offline.

### <a name="initialize-offline-sync"></a>Iniciar Sincronização Offline

Cada tabela offline tem de ser definida na cache de offline antes de utilização.  Normalmente, a definição da tabela é feita imediatamente após a criação do cliente:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Obter uma referência para a tabela de Cache Offline

Para uma tabela online, utilize `.getTable()`.  Para uma tabela offline, utilize `.getSyncTable()`:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Todos os métodos disponíveis para tabelas online (incluindo filtragem, ordenação, paginação, inserir dados, atualizar dados e a eliminação de dados) funcionam igualmente bem em tabelas online e offline.

### <a name="synchronize-the-local-offline-cache"></a>Sincronizar a Cache Local Offline

A sincronização é o controlo da sua aplicação.  Segue-se um método de sincronização de exemplo:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Se não for fornecido um nome de consulta para o `.pull(query, queryname)` método, em seguida, sincronização incremental é utilizado para devolver apenas registos que foi criados ou alterados com êxito desde o último concluída solicitação.

### <a name="handle-conflicts-during-offline-synchronization"></a>Identificador de conflitos durante a Sincronização Offline

Se acontecer um conflito durante um `.push()` operação, uma `MobileServiceConflictException` é emitida.   O item de servidor emitido está incorporado a exceção e podem ser obtido pelos `.getItem()` a exceção.  Ajuste o push chamando os seguintes itens no objeto MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Depois de todos os conflitos são marcados como quiser, chamar `.push()` novamente para resolver todos os conflitos.

## <a name="custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite-lhe definir os pontos finais personalizados que expõem funcionalidades do servidor que não mapeiam para uma insert, update, eliminar ou operação de leitura. Ao utilizar uma API personalizada, pode ter mais controlo sobre mensagens, incluindo o ler, definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem que não sejam JSON.

A partir de um cliente do Android, tem de chamar o **invokeApi** método a chamar o ponto final de API personalizado. O exemplo seguinte mostra como chamar um ponto final de API com o nome **completeAll**, que devolve uma classe de coleção com o nome **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

O **invokeApi** método é denominado no cliente, o que envia um pedido POST para a nova API personalizada. O resultado devolvido pela API personalizada é apresentado uma caixa de diálogo de mensagem, como a quaisquer erros. Outras versões **invokeApi** permitem-lhe enviar um objeto no corpo do pedido, opcionalmente, especifique o método HTTP e enviar parâmetros de consulta com o pedido. Sem tipos versões do **invokeApi** são fornecidos bem.

## <a name="authentication"></a>Adicionar autenticação à sua aplicação

Tutoriais descrevem já em detalhe como adicionar estas funcionalidades.

Serviço de aplicações suporta [autenticar utilizadores de aplicação](app-service-mobile-android-get-started-users.md) através de vários fornecedores de identidade externas: Facebook, Google, Account Microsoft, Twitter e o Azure Active Directory. Pode definir as permissões em tabelas para restringir o acesso de operações específicas para apenas os utilizadores autenticados. Também pode utilizar a identidade de utilizadores autenticados para implementar as regras de autorização no seu back-end.

São suportados dois fluxos de autenticação: um **servidor** fluxo e um **cliente** fluxo. O fluxo de servidor fornece a experiência de autenticação mais simples, como baseia-se na interface de web de fornecedores de identidade.  Não existem SDKs adicionais são necessários para implementar a autenticação de fluxo de servidor. Autenticação de fluxo de servidor não fornece uma integração profunda num dispositivo móvel e só é recomendada para uma prova de cenários de conceito.

O fluxo de cliente permite a integração mais profunda com as capacidades específicas do dispositivo, como o início de sessão único como depende SDKs fornecidas pelo fornecedor de identidade.  Por exemplo, pode integrar o SDK do Facebook a sua aplicação móvel.  O cliente móvel trocas para a aplicação do Facebook e confirma o início de sessão antes de troca de volta para a sua aplicação móvel.

São necessários quatro passos para ativar a autenticação na sua aplicação:

* Registe a aplicação para autenticação com um fornecedor de identidade.
* Configure o back-end do serviço de aplicações.
* Restringir permissões de tabela para utilizadores autenticados apenas no back-end do serviço de aplicações.
* Adicione o código de autenticação para a sua aplicação.

Pode definir as permissões em tabelas para restringir o acesso de operações específicas para apenas os utilizadores autenticados. Também pode utilizar o SID de um utilizador autenticado para modificar pedidos.  Para obter mais informações, consulte [introdução à autenticação] e a documentação do servidor SDK HOWTO.

### <a name="caching"></a>Autenticação: Fluxo de servidor

O código seguinte inicia um processo de início de sessão de fluxo de servidor utilizando o fornecedor do Google.  É necessária configuração adicional devido aos requisitos de segurança para o fornecedor de Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Além disso, adicione o seguinte método à classe de atividade principal:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

O `GOOGLE_LOGIN_REQUEST_CODE` definido no seu main atividade é utilizada para o `login()` método e dentro de `onActivityResult()` método.  Pode escolher qualquer número exclusivo, desde que o mesmo número é utilizado dentro de `login()` método e o `onActivityResult()` método.  Se separar o código de cliente para um adaptador de serviço (conforme mostrado anteriormente), deve chamar os métodos adequados no adaptador de serviço.

Terá também de configurar o projeto para customtabs.  Primeiro, especifique um URL de redirecionamento.  Adicione o seguinte fragmento para `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Adicionar o **redirectUriScheme** para o `build.gradle` ficheiro para a sua aplicação:

```text
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Por fim, adicione `com.android.support:customtabs:23.0.1` à lista de dependências no `build.gradle` ficheiro:

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Obter o ID de utilizador com sessão iniciada de um **MobileServiceUser** utilizando o **getUserId** método. Para obter um exemplo de como utilizar Futures para chamar o APIs de início de sessão assíncrono, consulte [introdução à autenticação].

> [!WARNING]
> O esquema de URL mencionado diferencia maiúsculas de minúsculas.  Certifique-se de que todas as ocorrências de `{url_scheme_of_you_app}` corresponder a maiúsculas e minúsculas.

### <a name="caching"></a>Tokens de autenticação de cache

Colocação em cache os tokens de autenticação requer a armazenar localmente o ID de utilizador e o token de autenticação no dispositivo. Da próxima vez que a aplicação for iniciada, verifique a cache, e se estes valores não estiverem presentes, pode ignorar o registo no procedimento rehydrate cliente com estes dados. No entanto estes dados são sensíveis e devem ser armazenado encriptados para segurança no caso do telefone obtém roubado.  Pode ver um exemplo completo de como para os tokens de autenticação de cache no [secção de tokens de autenticação da Cache][7].

Quando tenta utilizar um token expirado, receberá um *401 não autorizado* resposta. Pode processar erros de autenticação utilizando filtros.  Filtros interceptar pedidos para o back-end do serviço de aplicações. O código de filtro testa a resposta para um 401, aciona o processo de início de sessão e, em seguida, retoma o pedido que gerou o 401.

### <a name="refresh"></a>Utilize Tokens de atualização

O token devolvido pelo autorização e autenticação do serviço de aplicações do Azure tem um tempo de vida definido de uma hora.  Após este período, tem de voltar o utilizador.  Se estiver a utilizar um token de longa duração que receberam através da autenticação de fluxo de cliente, pode voltar com autenticação do serviço de aplicações do Azure e utilizar o mesmo token de autorização.  Outro token do App Service do Azure é gerado com uma duração de novo.

Também pode registar o fornecedor a utilizar Tokens de atualização.  Um Token de atualização não está sempre disponível.  É necessária configuração adicional:

* Para **do Azure Active Directory**, configurar um segredo do cliente para a aplicação do Active Directory do Azure.  Especifique o segredo do cliente no serviço de aplicações do Azure quando configurar a autenticação do Active Directory do Azure.  Quando chamar `.login()`, transmitir `response_type=code id_token` como um parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para **Google**, passar o `access_type=offline` como um parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para **Account Microsoft**, selecione o `wl.offline_access` âmbito.

Para atualizar um token, chame `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Como melhor prática, crie um filtro que Deteta uma resposta 401 do servidor e tenta atualizar o token de utilizador.

## <a name="log-in-with-client-flow-authentication"></a>Iniciar sessão com a autenticação de cliente-fluxo

O processo geral para iniciar sessão com a autenticação de fluxo de cliente é da seguinte forma:

* Configure a autorização e autenticação do serviço de aplicações do Azure tal como faria com autenticação de servidor fluxo.
* Integre o SDK para a autenticação para produzir um token de acesso do fornecedor de autenticação.
* Chamar o `.login()` método da seguinte forma:

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Substitua o `onSuccess()` método com independentemente código que pretende utilizar um início de sessão com êxito.  O `{provider}` cadeia é um fornecedor de válido: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount**, ou **twitter**.  Se tiver implementado a autenticação personalizada, em seguida, também pode utilizar a tag de fornecedor de autenticação personalizado.

### <a name="adal"></a>Autenticar os utilizadores com o Active Directory Authentication Library (ADAL)

Pode utilizar o Active Directory Authentication Library (ADAL) para a sessão dos utilizadores na sua aplicação com o Azure Active Directory. A utilização de um início de sessão do fluxo de cliente, muitas vezes, é preferível a utilizar o `loginAsync()` métodos que fornece uma funcionalidade do UX mais nativa e permite a personalização adicional.

1. Configurar o back-end da aplicação móvel para o início de sessão do AAD ao seguir o [como configurar o serviço de aplicações para início de sessão do Active Directory] [ 22] tutorial. Certifique-se concluir o passo opcional de registar uma aplicação cliente nativa.
2. Instale ADAL ao modificar o ficheiro de gradle para incluir as seguintes definições:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. Adicione o seguinte código à sua aplicação, tornando as substituições seguintes:

* Substitua **aqui de autoridade de inserção** com o nome do inquilino no qual que aprovisionou a sua aplicação. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Substitua **INSERT-RESOURCE-ID-aqui** com o ID de cliente para o back-end da aplicação móvel. Pode obter o ID de cliente do **avançadas** separador em **definições do Azure Active Directory** no portal.
* Substitua **INSERT-cliente ID aqui** com o ID de cliente que copiou da aplicação cliente nativa.
* Substitua **INSERT-REDIRECIONAMENTO-URI-aqui** com o seu site */.auth/login/done* ponto final, utilizando o esquema de HTTPS. Este valor deve ser semelhante *https://contoso.azurewebsites.net/.auth/login/done*.

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Ajuste as comunicações cliente-servidor

A ligação de cliente é normalmente uma ligação HTTP básica, utilizando a biblioteca HTTP subjacente fornecida com o SDK Android.  Existem vários motivos por que motivo seria aconselhável alterar que:

* Pretender utilizar uma biblioteca HTTP alternativa para ajustar os tempos limite.
* Pretende fornecer uma barra de progresso.
* Que pretende adicionar um cabeçalho personalizado para suportar a funcionalidade de gestão de API.
* Pretende interceptar uma resposta de falha para que pode implementar a reautenticação rápida.
* Pretende iniciar pedidos de back-end a um serviço de análise.

### <a name="using-an-alternate-http-library"></a>Utilizar uma biblioteca de HTTP alternativo

Chamar o `.setAndroidHttpClientFactory()` método imediatamente depois de criar uma referência de cliente.  Por exemplo, para definir o tempo limite da ligação a 60 segundos (em vez da predefinição 10 segundos):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementar um filtro de progresso

Pode implementar um intercept de cada pedido, implementando um `ServiceFilter`.  Por exemplo, as seguintes atualizações de uma barra de progresso previamente criada:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    pubic void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Pode ligar este filtro ao cliente da seguinte forma:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Personalizar a cabeçalhos de pedido

Utilize o seguinte `ServiceFilter` e anexe o filtro da mesma forma como o `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Configurar serialização automática

Pode especificar uma estratégia de conversão que se aplica a cada coluna utilizando o [gson] [ 3] API. A biblioteca de cliente do Android utiliza [gson] [ 3] bastidores para serializar objetos Java no dados JSON antes dos dados são enviados para o App Service do Azure.  O seguinte código utiliza o **setFieldNamingStrategy()** método para definir a estratégia de. Neste exemplo, irá eliminar o caráter inicial (um "m") e, em seguida, minúsculos o caráter seguinte, para cada nome de campo. Por exemplo, este seria ativar "mId" em "id".  Implementar uma estratégia de conversão para reduzir a necessidade de `SerializedName()` anotações na maioria dos campos.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStategy)
);
```

Este código tem de ser executado antes de criar uma referência de cliente para dispositivos móveis utilizando o **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[introdução à autenticação]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html
