---
title: "Aplicação de Java de computação intensiva numa VM | Microsoft Docs"
description: "Saiba como criar uma máquina virtual do Azure que executa uma aplicação de Java de computação intensivas que pode ser monitorizada por outra aplicação de Java."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 8c51c0bb37e25ad61fe58a85dd641dabe0a1958c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Como executar uma tarefa de computação intensiva em Java numa máquina virtual
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Com o Azure, pode utilizar uma máquina virtual vai processar as tarefas de computação intensivas. Por exemplo, uma máquina virtual pode processar tarefas e fornecer os resultados para as máquinas cliente ou aplicações móveis. Depois de ler este artigo, terá uma compreensão sobre como criar uma máquina virtual que executa uma aplicação de Java de computação intensivas que pode ser monitorizada por outra aplicação de Java.

Este tutorial parte do princípio de saber como criar aplicações de consola Java, pode importar bibliotecas para a aplicação Java e pode gerar um arquivo de Java (JAR). Presume-se sem qualquer conhecimento do Microsoft Azure.

Aprenderá:

* Como criar uma máquina virtual com um Java Development Kit (JDK) já instalado.
* Como iniciar sessão remotamente no sua máquina virtual.
* Como criar um espaço de nomes de barramento de serviço.
* Como criar uma aplicação de Java que realiza uma tarefa de computação intensivas.
* Como criar uma aplicação de Java que monitoriza o progresso da tarefa de computação intensivas.
* Como executar as aplicações de Java.
* Como parar as aplicações de Java.

Este tutorial irá utilizar o problema Traveling Salesman para a tarefa de computação intensivas. Segue-se um exemplo de aplicação Java executando a tarefa de computação intensivas.

![Solver Traveling Salesman problema][solver_output]

Segue-se um exemplo de aplicação Java a tarefa de computação intensivas de monitorização.

![Cliente Traveling Salesman problema][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Clique em **novo**, clique em **computação**, clique em **Máquina Virtual**e, em seguida, clique em **da galeria**.
3. No **selecione de imagem de Máquina Virtual** caixa de diálogo, selecione **JDK 7 do Windows Server 2012**.
   Tenha em atenção que **JDK 6 Windows Server 2012** está disponível no caso de ter aplicações antigas que ainda não estão prontas ser executada no JDK 7.
4. Clique em **Seguinte**.
5. No **configuração da Máquina Virtual** caixa de diálogo:
   1. Especifique um nome para a máquina virtual.
   2. Especifica o tamanho a utilizar para a máquina virtual.
   3. Introduza um nome para o administrador no **nome de utilizador** campo. Lembre-se de que este nome e a palavra-passe que introduziu em seguida, irá utilizar quando iniciar sessão remotamente máquina virtual.
   4. Introduza uma palavra-passe no **nova palavra-passe** campo e volte a introduzir no **confirmar** campo. Esta é a palavra-passe da conta de administrador.
   5. Clique em **Seguinte**.
6. As próximas **configuração da Máquina Virtual** caixa de diálogo:
   1. Para **serviço em nuvem**, utilize a predefinição **criar um novo serviço de nuvem**.
   2. O valor para **nome DNS do serviço de nuvem** tem de ser exclusivo em cloudapp.net. Se for necessário, modifique este valor para que essa Azure indica que seja exclusivo.
   3. Especifique uma região, o grupo de afinidade ou a rede virtual. Para efeitos deste tutorial, especifique uma região, tal como **EUA oeste**.
   4. Para **conta de armazenamento**, selecione **utilizar uma conta de armazenamento gerado automaticamente**.
   5. Para **do conjunto de disponibilidade**, selecione **(nenhum)**.
   6. Clique em **Seguinte**.
7. No final **configuração da Máquina Virtual** caixa de diálogo:
   1. Aceite as entradas de ponto final predefinido.
   2. Clique em **Concluído**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Para iniciar sessão remotamente no sua máquina virtual
1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **máquinas virtuais**.
3. Clique no nome da máquina virtual que pretende iniciar sessão.
4. Clique em **Ligar**.
5. Responda aos avisos, conforme necessário para ligar à máquina virtual. Quando lhe for pedido para o nome de administrador e a palavra-passe, utilize os valores que forneceu quando criou a máquina virtual.

Tenha em atenção que a funcionalidade do Service Bus do Azure necessita do certificado de Root da CyberTrust Baltimore para ser instalado como parte da sua JRE **cacerts** armazenar. Este certificado é automaticamente incluído no Java Runtime Environment (JRE) utilizados por este tutorial. Se não tiver este certificado no seu JRE **cacerts** armazenar, consulte [a adição de um certificado para o arquivo de certificados de AC do Java] [ add_ca_cert] para obter informações sobre como adicioná-lo (bem como informações sobre a visualização de certificados no arquivo de cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Como criar um espaço de nomes de barramento de serviço
Para começar a utilizar as filas do Service Bus no Azure, deve começar por criar um espaço de nomes do serviço. Um espaço de nomes de serviço fornece um contentor de âmbito de endereçamento de recursos do Service Bus na sua aplicação.

Para criar um espaço de nomes de serviço:

1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel de navegação inferior esquerdo do portal clássico do Azure, clique em **Service Bus, controlo de acesso e colocação em cache**.
3. No painel de canto superior esquerdo do portal clássico do Azure, clique o **Service Bus** nó e, em seguida, clique o **novo** botão.  
   ![Captura de ecrã do nó de barramento de serviço][svc_bus_node]
4. No **criar um novo espaço de nomes de serviço** caixa de diálogo, introduza um **espaço de nomes**e, em seguida, para se certificar de que é exclusiva, clique em de **verificar disponibilidade** botão.  
   ![Criar uma captura de ecrã do novo espaço de nomes][create_namespace]
5. Após certificar-se de que o nome de espaço de nomes está disponível, escolha o país ou região na qual será alojado o espaço de nomes e, em seguida, clique em de **criar espaço de nomes** botão.  
   
   O espaço de nomes que criou, em seguida, será apresentada no portal clássico do Azure e demora alguns minutos a ativar. Aguarde enquanto o estado for **Active Directory** antes de continuar com o passo seguinte.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Obter as credenciais de gestão predefinido para o espaço de nomes
Para efetuar operações de gestão, tais como criar uma fila, no novo espaço de nomes, terá de obter as credenciais de gestão para o espaço de nomes.

1. No painel de navegação esquerdo, clique o **Service Bus** nó para apresentar a lista de espaços de nomes disponíveis.
   ![Captura de ecrã de espaços de nomes disponível][avail_namespaces]
2. Selecione o espaço de nomes que acabou de criar a lista apresentada.
   ![Captura de ecrã da lista de espaço de nomes][namespace_list]
3. O direito **propriedades** painel lista as propriedades de novo espaço de nomes.
   ![Captura de ecrã de painel de propriedades][properties_pane]
4. O **predefinido chave** está oculto. Clique em de **vista** botão para apresentar as credenciais de segurança.
   ![Captura de ecrã de chave de predefinido][default_key]
5. Anote o **predefinido emissor** e o **predefinido chave** porque irá utilizar estas informações abaixo para executar operações com o espaço de nomes.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Como criar uma aplicação de Java que realiza uma tarefa de computação intensiva
1. No computador de desenvolvimento (que não têm de ser a máquina virtual que criou), transfira o [Azure SDK para Java](https://azure.microsoft.com/develop/java/).
2. Crie uma aplicação de consola Java utilizando o código de exemplo no final desta secção. Neste tutorial, iremos utilizar **TSPSolver.java** como o nome de ficheiro de Java. Modificar o **o\_serviço\_barramento\_espaço de nomes**, **sua\_serviço\_barramento\_proprietário**, e **o\_serviço\_barramento\_chave** marcadores de posição para utilizar o barramento de serviço **espaço de nomes**, **emissor predefinido** e **predefinido chave** valores, respetivamente.
3. Após a codificação, exportar a aplicação para um arquivo de Java passíveis de execução (JAR) e as bibliotecas necessárias o pacote para o JAR gerado. Neste tutorial, iremos utilizar **TSPSolver.jar** como o nome de JAR gerado.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Como criar uma aplicação de Java que monitoriza o progresso da tarefa de computação intensiva
1. No computador de desenvolvimento, crie uma aplicação de consola Java utilizando o código de exemplo no final desta secção. Neste tutorial, iremos utilizar **TSPClient.java** como o nome de ficheiro de Java. Conforme mostrado anteriormente, modificar o **o\_serviço\_barramento\_espaço de nomes**, **sua\_serviço\_barramento\_proprietário**, e **o\_serviço\_barramento\_chave** marcadores de posição para utilizar o barramento de serviço **espaço de nomes**, **emissor predefinido** e **predefinido chave** valores, respetivamente.
2. Exportar a aplicação para um JAR passíveis de execução e as bibliotecas necessárias o pacote para o JAR gerado. Neste tutorial, iremos utilizar **TSPClient.jar** como o nome de JAR gerado.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Como executar as aplicações de Java
Execute a aplicação de computação intensiva, primeiro para criar a fila, em seguida, para resolver o problema de Saleseman estiverem em deslocação, irá adicionar a melhor rota atual para a fila de barramento de serviço. Enquanto a aplicação de computação intensiva está em execução (ou posteriormente), execute o cliente para ver os resultados da fila de barramento de serviço.

### <a name="to-run-the-compute-intensive-application"></a>Para executar a aplicação de computação intensiva
1. Inicie sessão na sua máquina virtual.
2. Crie uma pasta onde irá executar a sua aplicação. Por exemplo, **c:\TSP**.
3. Cópia **TSPSolver.jar** para **c:\TSP**,
4. Crie um ficheiro denominado **c:\TSP\cities.txt** com o seguinte conteúdo.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. Numa linha de comandos, altere os diretórios para c:\TSP.
6. Certifique-se de pasta de reciclagem o JRE na variável de ambiente PATH.
7. Terá de criar a fila de barramento de serviço antes de executar as combinações de solver TSP. Execute o seguinte comando para criar a fila de barramento de serviço.
   
        java -jar TSPSolver.jar createqueue
8. Agora que a fila é criada, pode executar as combinações de solver TSP. Por exemplo, execute o seguinte comando para executar o solver para 8 cidades.
   
        java -jar TSPSolver.jar 8
   
   Se não especificar um número, será executada para 10 cidades. Como o solver localiza atuais rotas mais curta, irá adicioná-los para a fila.

> [!NOTE]
> Quanto maior for o número que especificar, mais tempo a solver será executado. Por exemplo, executar para 14 cidades pode demorar vários minutos e, em execução para 15 cidades pode demorar várias horas. Aumentar a 16 ou mais cidades pode resultar em dias do tempo de execução (eventualmente semanas, meses e anos). Isto acontece porque o aumento rápido do número de permutações avaliadas pelo solver como o número de aumentos de cidades.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>Como executar a aplicação de monitorização de cliente
1. Inicie sessão no seu computador onde irá executar a aplicação de cliente. Isto não tem de ser o mesmo computador que executa o **TSPSolver** aplicação, apesar de poder ser.
2. Crie uma pasta onde irá executar a sua aplicação. Por exemplo, **c:\TSP**.
3. Cópia **TSPClient.jar** para **c:\TSP**,
4. Certifique-se de pasta de reciclagem o JRE na variável de ambiente PATH.
5. Numa linha de comandos, altere os diretórios para c:\TSP.
6. Execute o seguinte comando.
   
        java -jar TSPClient.jar
   
    Opcionalmente, especifique o número de minutos para suspender entre a verificação da fila, mediante a transmissão num argumento da linha de comandos. O período de suspensão predefinido para verificar a fila é de 3 minutos, o que é utilizada se nenhum argumento da linha de comandos é passado para **TSPClient**. Se pretender utilizar um valor diferente para o intervalo de suspensão, por exemplo, um minuto, execute o seguinte comando.
   
        java -jar TSPClient.jar 1
   
    O cliente será executado até que vê uma mensagem de fila de "Concluir". Tenha em atenção que se executa as múltiplas ocorrências a solver sem executar o cliente, poderá ter de executar o cliente várias vezes para completamente esvaziar a fila. Em alternativa, pode eliminar a fila e, em seguida, crie-a novamente. Para eliminar a fila, execute o seguinte **TSPSolver** (não **TSPClient**) comando.
   
        java -jar TSPSolver.jar deletequeue
   
    O solver será executado até concluir todas as rotas a examinar.

## <a name="how-to-stop-the-java-applications"></a>Como parar as aplicações de Java
Para o solver e aplicações de cliente, pode premir **Ctrl + C** para sair se pretende terminar antes da conclusão normal.

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
