---
title: Serializar dados no Azure Hadoop - Microsoft Avro Library - | Microsoft Docs
description: "Saiba como serializar e anular a serialização de dados no Hadoop no HDInsight com o Microsoft Avro Library para manter a memória, uma base de dados ou ficheiro."
keywords: avro, avro de hadoop
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c78dc20d-5d8d-4366-94ac-abbe89aaac58
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jgao
ms.custom: hdiseo17may2017
ms.openlocfilehash: 04f76d15f12b52d7b47011010a5ce20cc45174ee
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serializar dados no Hadoop com o Microsoft Avro Library

>[!NOTE]
>O SDK Avro já não é suportado pela Microsoft. A biblioteca é Comunidade de código aberto suportada. As origens para a biblioteca estão localizadas em [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

Este tópico mostra como utilizar o [Microsoft Avro Library](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) para serializar objetos e outras estruturas de dados em fluxos para as manter para memória, uma base de dados ou um ficheiro. Também mostra como anular a serialização-los para recuperar os objetos originais.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
O <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> implementa o sistema de serialização de dados do Apache Avro para o ambiente do Microsoft.NET. Apache Avro fornece um formato de intercâmbio de dados binários compact para serialização. Utiliza <a href="http://www.json.org" target="_blank">JSON</a> para definir um esquema de linguagem desconhecida que assegura a interoperabilidade da linguagem. Os dados serializados numa linguagem podem ser lidos noutra. Atualmente são suportadas C, C++, c#, Java, PHP, Python e Ruby. Podem encontrar informações detalhadas sobre o formato no <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">especificação do Apache Avro</a>. 

>[!NOTE]
>A Microsoft Avro Library não suporta a parte de (RPCs) as chamadas de procedimento remoto desta especificação.
>

A representação serializada de um objeto no sistema Avro é composto por duas partes: esquema e o valor real. O esquema Avro descreve o modelo de dados de independente de idioma dos dados serializados com JSON. É apresentada side by side with uma representação binária de dados. Ter o esquema separado da representação binária permite cada objeto a ser escritos com sem sobrecargas por valor, efetuar pequenas serialização rápida e a representação.

## <a name="the-hadoop-scenario"></a>O cenário de Hadoop
O formato de serialização do Apache Avro é amplamente utilizado no Azure HDInsight e noutros ambientes do Apache Hadoop. Avro fornece uma maneira conveniente para representar estruturas de dados complexas dentro de uma tarefa de MapReduce do Hadoop. O formato de ficheiros do Avro (ficheiro de contentor de objeto Avro) tem sido concebido para suportar o modelo de programação de MapReduce distribuído. A funcionalidade-chave que permite a distribuição é que os ficheiros estão "divisíveis", na medida em que um pode procurar qualquer ponto num ficheiro e iniciar a leitura a partir de um determinado bloco.

## <a name="serialization-in-avro-library"></a>Serialização na biblioteca de Avro
A biblioteca .NET para Avro suporta duas formas de serialização de objetos:

* **reflexão** -esquema o JSON para os tipos é automaticamente criada a partir de dados contrato atributos dos tipos de .NET para ser serializado.
* **registo genérico** -esquema A JSON é ou não explicitamente especificado num registo representado pelo [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) classe quando não existem tipos .NET estão presentes para descrever o esquema para os dados a ser serializado.

Quando o esquema de dados é conhecido para o escritor e o leitor da transmissão em fluxo, os dados podem ser enviados sem a esquema. Em casos quando um ficheiro de contentor de objeto Avro é utilizado, o esquema é armazenado no ficheiro. Outros parâmetros, tais como o codec utilizado para compressão de dados, podem ser especificados. Estes cenários são descritos mais detalhadamente e ilustrados nos exemplos de código seguinte:

## <a name="install-avro-library"></a>Instalar Avro biblioteca
É necessário o seguinte antes de instalar a biblioteca:

* <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">O Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 ou posterior)

Tenha em atenção que a dependência de Newtonsoft.Json.dll é transferida automaticamente com a instalação do Microsoft Avro Library. O procedimento é fornecido na secção seguinte:

A Microsoft Avro Library é distribuído como um pacote NuGet que pode ser instalado a partir do Visual Studio através do seguinte procedimento:

1. Selecione o **projeto** separador -> **gerir pacotes NuGet...**
2. Pesquisar por "Microsoft.Hadoop.Avro" no **Pesquisa Online** caixa.
3. Clique em de **instalar** junto a **biblioteca do Microsoft Azure HDInsight Avro**.

Tenha em atenção que o Newtonsoft.Json.dll (> = 6.0.4) dependência também é transferida automaticamente com o Microsoft Avro Library.

O código de origem do Microsoft Avro Library está disponível em [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

## <a name="compile-schemas-using-avro-library"></a>Compilar os esquemas Avro biblioteca a utilizar
A Microsoft Avro Library contém um utilitário de geração de código que lhe permite criar c# tipos automaticamente com base no esquema JSON definido anteriormente. O utilitário de geração de código não é distribuído como um executável binário, mas pode ser facilmente criado através do seguinte procedimento:

1. Transfira o ficheiro. zip com a versão mais recente do código de origem do SDK do HDInsight da <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK para o Hadoop</a>. (Clique o **transferir** ícone, não o **transfere** separador.)
2. Extraia o SDK do HDInsight para um diretório no computador com o .NET Framework 4 instalado e ligado à Internet para transferir pacotes de NuGet dependência necessária. A seguir, partimos do princípio que o código de origem é extraído para C:\SDK.
3. Ir para a pasta C:\SDK\src\Microsoft.Hadoop.Avro.Tools e execute build.bat. (O ficheiro chamadas MSBuild da distribuição de 32 bits do .NET Framework. Se gostaria de utilizar a versão de 64 bits, editar build.bat, seguindo os comentários dentro do ficheiro.) Certifique-se de que a compilação for concluída com êxito. (Em alguns sistemas MSBuild pode produzir avisos. Estes avisos não afetam o utilitário enquanto existirem erros de compilação.)
4. O utilitário compilado está localizado em C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.

Para se familiarizar com a sintaxe da linha de comandos, execute o seguinte comando a partir da pasta onde está localizado o utilitário de geração de código:`Microsoft.Hadoop.Avro.Tools help /c:codegen`

Para testar o utilitário, pode gerar c# classes do ficheiro de esquema do JSON de exemplo fornecido com o código de origem. Execute o seguinte comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Isto deveria para produzir ficheiros dois c# no diretório atual: SensorData.cs e Location.cs.

Para compreender a lógica que está a utilizar o utilitário de geração de código ao converter o esquema JSON para tipos de c#, consulte o ficheiro que generationverification.Feature localizado na C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Espaços de nomes são extraídos de esquema JSON, utilizando a lógica descrita no ficheiro mencionado no parágrafo anterior. Espaços de nomes extraídos do esquema têm precedência sobre o que é fornecido com o parâmetro /n na linha de comandos de utilitário. Se quiser substituir os espaços de nomes contidos o esquema, utilize o parâmetro /nf. Por exemplo, para alterar todos os espaços de nomes de SampleJSONSchema.avsc para my.own.nspace, execute o seguinte comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>Sobre as amostras
Exemplos de seis fornecidos neste tópico mostram diferentes cenários suportados, o Microsoft Avro Library. A Microsoft Avro Library foi concebido para trabalhar com qualquer fluxo. Nestes exemplos, estão manipular dados através de fluxos de memória em vez de fluxos de ficheiros ou bases de dados para obter simplicidade e consistência. A abordagem adotada num ambiente de produção depende dos requisitos de cenário exato, origem de dados e volume, as restrições de desempenho e outros fatores.

Os primeiros dois exemplos mostram como serializar e anular a serialização de dados para memórias intermédias de fluxo de memória utilizando reflexão e registos genéricos. Pressupõe-se que o esquema nestes casos dois ser partilhados entre os leitores e escritores fora de banda.

Os terceiros e quarto exemplos mostram como serializar e anular a serialização de dados, utilizando os ficheiros do Avro objeto contentor. Quando os dados são armazenados num ficheiro Avro contentor, a esquema é sempre armazenada com o mesmo porque o esquema tem de ser partilhado para anulação de serialização.

O exemplo que contém os exemplos de quatro primeiras pode ser transferido a partir de <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">exemplos de código do Azure</a> site.

O exemplo de quinto mostra como utilizar um codec de compressão personalizadas para os ficheiros do Avro objeto contentor. Um exemplo que contém o código para este exemplo pode ser transferido a partir de <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">exemplos de código do Azure</a> site.

O sexto exemplo mostra como utilizar serialização Avro para carregar dados para o Blob storage do Azure e, em seguida, analise-o utilizando o Hive com um cluster do HDInsight (Hadoop). Pode ser transferido a partir de <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">exemplos de código do Azure</a> site.

Seguem-se ligações para os exemplos de seis explicados no tópico:

* <a href="#Scenario1">**Serialização com reflexão** </a> -esquema o JSON para tipos de forma a ser serializado é automaticamente criada a partir os dados de atributos de contrato.
* <a href="#Scenario2">**Serialização com registo genérico** </a> -esquema o JSON é ou não explicitamente especificado num registo quando nenhum tipo .NET está disponível para reflexão.
* <a href="#Scenario3">**Serialização com ficheiros de contentor de objeto reflexão** </a> -esquema o JSON é automaticamente criada e partilhado juntamente com os dados serializados através de um ficheiro de contentor de objeto Avro.
* <a href="#Scenario4">**Serialização utilizando ficheiros de contentor de objeto com registo genérico** </a> -esquema JSON o explicitamente especificado antes da serialização e partilhado, juntamente com os dados através de um ficheiro de contentor de objeto Avro.
* <a href="#Scenario5">**Serialização utilizando ficheiros de contentor de objeto com um codec de compressão personalizado** </a> -o exemplo mostra como criar um ficheiro de contentor de objeto Avro com uma implementação .NET personalizada o codec de compressão de dados Deflate.
* <a href="#Scenario6">**Utilizar Avro para carregar dados para o serviço do Microsoft Azure HDInsight** </a> -exemplo ilustra como serialização Avro interage com o serviço de HDInsight. Uma subscrição do Azure Active Directory e o acesso a um cluster do Azure HDInsight são necessários para executar este exemplo.

## <a name="Scenario1"></a>Exemplo 1: Serialização com reflexão
O esquema JSON para os tipos de pode ser automaticamente criado, a Microsoft Avro Library através de reflexão a partir dos dados de atributos de contrato de objetos c# para ser serializado. A Microsoft Avro Library cria um [ **IAvroSeralizer<T>**  ](http://msdn.microsoft.com/library/dn627341.aspx) para identificar os campos para ser serializado.

Neste exemplo, os objetos (um **SensorData** classe com um membro **localização** struct) são serializadas para um fluxo de memória, e este fluxo é por sua vez anular a serialização. O resultado é comparado com a instância inicial para confirmar que o **SensorData** objeto recuperado é idêntico ao original.

O esquema neste exemplo é assumido ser partilhado entre os leitores e escritores, para que o formato de contentor de objeto Avro não é necessário. Para obter um exemplo de como serializar e anular a serialização de dados para memórias intermédias de memória utilizando reflexão com o formato de contentor de objeto, quando o esquema tem de ser partilhado com os dados, consulte <a href="#Scenario3">serialização com ficheiros de contentor de objeto reflexão</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-2-serialization-with-a-generic-record"></a>Exemplo 2: Serialização com um registo genérico
Um esquema JSON pode ser especificado explicitamente num registo genérico quando reflexão não pode ser utilizado porque os dados não podem ser representados através de classes .NET com um contrato de dados. Este método é mais lento do que utilizando reflexão. Nestes casos, o esquema para os dados também pode ser dinâmico, ou seja, não é conhecido no momento da compilação. Dados representados como ficheiros de valores separados por vírgulas (CSV), cuja esquema é desconhecida até que está a ser transformado para o formato Avro em tempo de execução se um exemplo deste tipo de cenário dinâmico.

Este exemplo mostra como criar e utilizar um [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) para especificar explicitamente um esquema JSON, como preenchê-lo com os dados e, em seguida, como serializar e anular a serialização. O resultado é comparado inicial de instâncias para confirmar que o registo de recuperação é idêntico à original.

O esquema neste exemplo é assumido ser partilhado entre os leitores e escritores, para que o formato de contentor de objeto Avro não é necessário. Para obter um exemplo de como serializar e anular a serialização de dados para memórias intermédias de memória utilizando um registo genérico com o formato de contentor de objeto, quando o esquema tem de ser incluído com os dados serializados, consulte o <a href="#Scenario4">serialização utilizando ficheiros de contentor de objeto com registo genérico</a> exemplo.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn is then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Exemplo 3: Serialização com ficheiros de contentor de objeto e serialização reflexão
Neste exemplo é semelhante do cenário no <a href="#Scenario1"> primeiro exemplo</a>, onde o esquema implicitamente for especificado com reflexão. A diferença é que aqui, o esquema não é assumido como conhecido para o leitor deserializes-lo. O **SensorData** objetos a ser serializado e o respetiva esquema implicitamente especificado são armazenados num ficheiro de contentor de objeto Avro representado pelo [ **AvroContainer** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) classe.

Os dados está a ser serializados neste exemplo com [ **SequentialWriter<SensorData>**  ](http://msdn.microsoft.com/library/dn627340.aspx) e serialização anulada com [ **SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). O resultado é comparado com as instâncias iniciais para garantir que a identidade.

Os dados no ficheiro de contentor de objeto são comprimidos através de predefinição [ **Deflate** ] [ deflate-100] codec de compressão do .NET Framework 4. Consulte o <a href="#Scenario5"> exemplo quinto</a> neste tópico para saber como utilizar uma versão mais recente e superior a [ **Deflate** ] [ deflate-110] codec de compressão disponível no .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Exemplo 4: Serialização utilizar os ficheiros de contentor de objeto e serialização com o registo genérico
Neste exemplo é semelhante do cenário no <a href="#Scenario2"> segundo exemplo</a>, onde o esquema é ou não explicitamente especificado com JSON. A diferença é que aqui, o esquema não é assumido como conhecido para o leitor deserializes-lo.

O conjunto de dados de teste é recolhido para uma lista de [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) objetos através de um esquema JSON explicitamente definido e, em seguida, armazenadas num ficheiro de contentor do objeto representado pelo [ **AvroContainer** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) classe. Este ficheiro de contentor cria um escritor que é utilizado para serializar os dados, descomprimidos, como um fluxo de memória que, em seguida, é guardado para um ficheiro. O [ **Codec.Null** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) utilizado para criar o leitor de parâmetro que não serão comprimidos estes dados.

Os dados, em seguida, é lida do ficheiro e anular a serialização para uma coleção de objetos. Esta coleção é comparado com a lista inicial de registos de Avro para confirmar se são idênticos.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is not compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Exemplo 5: Serialização utilizando ficheiros de contentor de objeto com um codec de compressão personalizado
O exemplo de quinto mostra como utilizar um codec de compressão personalizadas para os ficheiros do Avro objeto contentor. Um exemplo que contém o código para este exemplo pode ser transferido a partir de [exemplos de código do Azure](http://code.msdn.microsoft.com/Serialize-data-with-the-67159111) site.

O [Avro especificação](http://avro.apache.org/docs/current/spec.html#Required+Codecs) permite a utilização de um codec de compressão opcional (para além **nulo** e **Deflate** predefinições). Este exemplo não está a implementar um novo codec como Snappy (mencionada como um codec opcional suportado no [Avro especificação](http://avro.apache.org/docs/current/spec.html#snappy)). Mostra como utilizar a implementação do .NET Framework 4.5 do [ **Deflate** ] [ deflate-110] codec, que fornece um algoritmo de compressão melhor com base no [zlib](http://zlib.net/) biblioteca de compressão que a versão do .NET Framework 4 predefinido.

    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It catches the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it relies on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Exemplo 6: Utilizar Avro para carregar dados para o serviço do Microsoft Azure HDInsight
O sexto exemplo ilustra alguns técnicas de programação relacionadas com a interagir com o serviço do Azure HDInsight. Um exemplo que contém o código para este exemplo pode ser transferido a partir de [exemplos de código do Azure](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) site.

O exemplo efetua as seguintes tarefas:

* Estabelece ligação a um cluster de serviço do HDInsight existente.
* Serializa vários ficheiros CSV e carrega o resultado para o armazenamento de Blobs do Azure. (Os ficheiros CSV são distribuídos juntamente com o exemplo e representam uma extração de dados históricos de gráfico de cotações AMEX distribuídas [Infochimps](http://www.infochimps.com/) para o período de 1970 2010. O exemplo lê dados de ficheiro CSV, converte os registos de instâncias do **Stock** classe e, em seguida, serializa-los utilizando reflexão. A definição de tipo as cotações é criada a partir de um esquema JSON através do utilitário de geração de código Microsoft Avro Library.
* Cria uma nova tabela externa chamada **ações** no ramo de registo e ligações para os dados carregado no passo anterior.
* Executa uma consulta, utilizando o Hive através de **ações** tabela.

Além disso, o exemplo efetua um procedimento de limpeza antes e depois de efetuar as principais operações. Durante a limpeza, todos os dados de Blobs do Azure relacionados e pastas são removidos e a tabela de Hive é removida. Pode também invocar o procedimento de limpeza a partir da linha de comandos de exemplo.

O exemplo tem os seguintes pré-requisitos:

* Uma subscrição do Microsoft Azure Active Directory e o ID da subscrição.
* Um certificado de gestão para a subscrição com a chave privada correspondente. O certificado deve ser instalado no atual utilizador privada armazenamento na máquina utilizada para executar o exemplo.
* Um cluster de HDInsight Active Directory.
* Uma conta de armazenamento do Azure ligado ao cluster do HDInsight de pré-requisitos anteriores, juntamente com a chave de acesso primária ou secundária correspondente.

Todas as informações de pré-requisitos devem ser introduzidas no ficheiro de configuração de exemplo antes da amostra é executada. Existem duas formas possíveis de fazê-lo:

* Edite o ficheiro App. config no diretório de raiz de exemplo e, em seguida, criar a amostra
* Primeiro criar a amostra e, em seguida, edite AvroHDISample.exe.config no diretório de compilação

Em ambos os casos, todas as edições devem ser feitas  **<appSettings>**  secção de definições. Siga os comentários no ficheiro.
O exemplo é executado a partir da linha de comandos, executando o seguinte comando (caso contrário, onde o ficheiro. zip com o exemplo foi pressupõe-se a ser extraído para C:\AvroHDISample; se utilizar o caminho do ficheiro relevantes):

    AvroHDISample run C:\AvroHDISample\Data

Para limpar o cluster, execute o seguinte comando:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
