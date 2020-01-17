---
title: Guia de programação do SCP.NET para Storm no HDInsight do Azure
description: Saiba como usar SCP.NET para criar topologias do Storm baseadas em .NET para usar com o a execução do Storm no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: f462fd88acf04fc8dced3db739a555c371c184ab
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154475"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Guia de programação do SCP para Apache Storm no Azure HDInsight

O SCP é uma plataforma para a criação de aplicativos de processamento de dados em tempo real, confiáveis, consistentes e de alto desempenho. Ele é criado com base no [Apache Storm](https://storm.incubator.apache.org/) – um sistema de processamento de fluxo projetado pelas comunidades de OSS. O Storm foi projetado por Nathan Marz e foi aberto pelo Twitter. Ele aproveita o [Apache ZooKeeper](https://zookeeper.apache.org/), outro projeto da Apache, para habilitar uma coordenação distribuída e gerenciamento de estado altamente confiáveis.

O projeto SCP não apenas compatibilizou o Storm no Windows como também incluiu extensões e personalização para o ecossistema do Windows. As extensões incluem a experiência dos desenvolvedores e as bibliotecas do .NET; a personalização inclui a implantação baseada em Windows.

A extensão e a personalização são feitas de forma que não precisamos bifurcar os projetos de OSS e poderíamos aproveitar os ecossistemas derivados criados com base no Storm.

## <a name="processing-model"></a>Modelo de processamento

Os dados no SCP são modelados como fluxos contínuos de tuplas. Geralmente, as tuplas são transmitidas para alguma fila primeiro, depois, são apanhadas e transformadas pela lógica de negócios hospedada em uma topologia do Storm; por fim, o resultado pode ser conectado como tuplas a outro sistema do SCP ou ser confirmado para armazenamentos, como o sistema de arquivos distribuído, ou bancos de dados, como o SQL Server.

![Um diagrama de uma fila que fornece dados para processamento, alimentando um armazenamento de dados](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

No Storm, a topologia de um aplicativo define um grafo de computação. Cada nó em uma topologia contém a lógica de processamento, e os links entre os nós indicam o fluxo de dados. Os nós que injetam dados de entrada na topologia são denominados _spouts_, que podem ser usados para sequenciar os dados. Os dados de entrada podem residir em logs de arquivo, banco de dados transacional, contador de desempenho do sistema e assim por diante. Os nós com fluxos de dados de entrada e de saída são denominados _bolts_, que realizam a filtragem, seleções e agregação reais dos dados.

O SCP dá suporte a melhores esforços, pelo menos uma vez e pelo processamento de dados. Em um aplicativo de processamento de streaming distribuído, vários erros podem ocorrer durante o processamento de dados, como interrupção da rede, falha do computador ou erro de código do usuário e assim por diante. O processamento ao menos uma vez assegura que todos os dados serão processados pelo menos uma vez, reproduzindo automaticamente os mesmos dados quando o erro ocorre. O processamento pelo menos uma vez é simples e confiável e atende a muitos aplicativos. No entanto, quando o aplicativo exige uma contagem exata, por exemplo, o processamento ao menos uma vez é insuficiente, já que os mesmos dados podem ser reproduzidos na topologia do aplicativo. Nesse caso, o processamento exatamente após é projetado para garantir que o resultado esteja correto mesmo quando os dados podem ser reproduzidos e processados várias vezes.

O SCP permite que os desenvolvedores do .NET desenvolvam aplicativos de processo de dados em tempo real, aproveitando a JVM (Máquina Virtual Java) com o Storm nos bastidores. O .NET e a JVM se comunicam por meio do soquete local do TCP. Basicamente, cada Spout/parafuso é um par de processos .NET/Java, onde a lógica do usuário é executada no processo do .NET como um plug-in.

Para criar um aplicativo de processamento de dados baseado no SCP, várias etapas são necessárias:

* Projete e implemente os Spouts para extrair dados da fila.
* Projete e implemente os Bolts para processar os dados de entrada e salvar os dados em armazenamentos externos, como um banco de dados.
* Projete a topologia e depois envie-a e execute-a. A topologia define os vértices e os fluxos de dados entre os vértices. O SCP assume a especificação da topologia e a implanta em um cluster do Storm, em que cada vértice é executado em um nó lógico. O failover e o dimensionamento serão resolvidos pelo Agendador de tarefas do Storm.

Este documento usa alguns exemplos simples para explicar como criar aplicativos de processamento de dados com o SCP.

## <a name="scp-plugin-interface"></a>Interface do plug-in do SCP

Plugins (ou aplicativos) do SCP são EXEs independentes que podem ser executados dentro do Visual Studio durante a fase de desenvolvimento e podem ser conectados ao pipeline do Storm após a implantação na produção. Escrever o plugin do SCP é igual escrever qualquer outro aplicativo do console do Windows padrão. A plataforma do SCP.NET declara alguma interface para spout/bolt, e o código de plugin do usuário deve implementar essas interfaces. O principal propósito desse design é que o usuário possa se concentrar em sua própria lógica de negócios, e deixar que as outras coisas sejam realizadas pela plataforma do SCP.NET.

O código do plug-in do usuário deve implementar uma das seguintes interfaces, dependendo se a topologia é transacional ou não transacional e se o componente é um spout ou bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin

ISCPPlugin é a interface comum para todos os tipos de plugins. Atualmente, é uma interface fictícia.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout

ISCPSpout é a interface para spout não transacional.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Quando `NextTuple()` é chamado, o código do usuário C# pode emitir uma ou mais tuplas. Se não houver nada a emitir, esse método deverá retornar sem emitir nada. Deve-se observar que `NextTuple()`, `Ack()`e `Fail()` são todos chamados em um loop rígido em um único thread em C# processo. Quando não há tuplas a serem emitidas, é educado ter o NextTuple Sleep por um curto período de tempo (como 10 milissegundos) para não desperdiçar muita CPU.

`Ack()` e `Fail()` são chamados apenas quando o mecanismo de confirmação é habilitado no arquivo de especificações. O `seqId` é usado para identificar a tupla que é confirmada ou falhou. Portanto, se o reconhecimento for habilitado em uma topologia não transacional, a seguinte função de emissão deverá ser usada no Spout:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Se a ACK não tiver suporte na topologia não transacional, o `Ack()` e `Fail()` poderão ser deixados como uma função vazia.

O `parms` parâmetro de entrada nessas funções é um dicionário vazio, ele é reservado para uso futuro.

### <a name="iscpbolt"></a>ISCPBolt

ISCPBolt é a interface para bolt não transacional.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Quando a nova tupla estiver disponível, função `Execute()` será chamada para processá-la.

### <a name="iscptxspout"></a>ISCPTxSpout

ISCPTxSpout é a interface para spout transacional.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Assim como sua parte do contador não transacional, `NextTx()`, `Ack()`e `Fail()` são todas chamadas em um loop rígido em um único thread em C# processo. Quando não há dados a serem emitidos, é educado ter `NextTx` suspensão por um curto período de tempo (10 milissegundos) para não perder muita CPU.

O `NextTx()` é chamado para iniciar uma nova transação, e o parâmetro de saída `seqId` é usado para identificar a transação, que também é usado em `Ack()` e `Fail()`. No `NextTx()`, o usuário pode emitir dados para o lado do Java. Os dados são armazenadas no ZooKeeper para dar suporte à reprodução. Como a capacidade do ZooKeeper é limitada, o usuário deve emitir somente metadados e não dados em massa no spout transacional.

O Storm repetirá uma transação automaticamente se falhar, portanto `Fail()` não deve ser chamado em caso normal. Mas se o SCP puder verificar os metadados emitidos pelo spout transacional, ele poderá chamar `Fail()` quando os metadados forem inválidos.

O `parms` parâmetro de entrada nessas funções é um dicionário vazio, ele é reservado para uso futuro.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

ISCPBatchBolt é a interface para bolt transacional.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` é chamado quando há uma nova tupla chegando ao parafuso. `FinishBatch()` é chamado quando essa transação é encerrada. O parâmetro de entrada `parms` é reservado para uso futuro.

Para topologia transacional, há um conceito importante – `StormTxAttempt`. Ele tem dois campos, `TxId` e `AttemptId`. `TxId` é usado para identificar uma transação específica e para uma determinada transação, poderá haver várias tentativas se a transação falhar e for reproduzida. O SCP.NET cria um novo objeto ISCPBatchBolt para processar cada `StormTxAttempt`, assim como o Storm faz no Java. O propósito desse design é oferecer suporte ao processamento de transações paralelas. O usuário deve considerar que, se uma tentativa de transação for concluída, o objeto ISCPBatchBolt correspondente será destruído e o lixo será coletado.

## <a name="object-model"></a>Modelo de Objeto

O SCP.NET também oferece um conjunto simples de objetos chave para que os desenvolvedores realizem a programação. Eles são **Context**, **StateStore**e **SCPRuntime**. Eles são discutidos na parte restante desta seção.

### <a name="context"></a>Contexto

O Contexto oferece um ambiente de execução ao aplicativo. Cada instância ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) possui uma instância de Contexto correspondente. A funcionalidade fornecida pelo contexto pode ser dividida em duas partes: (1) a parte estática, que está disponível em todo C# o processo, (2) a parte dinâmica, que está disponível somente para a instância de contexto específica.

### <a name="static-part"></a>Parte Estática

    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` é fornecido para fins de obtenção de log.

`pluginType` é usado para indicar o tipo de plug-in do processo do C#. Se o processo do C# for executado no modo de teste local (sem Java), o tipo de plug-in será `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` é fornecido para obter os parâmetros de configuração do lado do Java. Os parâmetros são transmitidos do lado do Java quando o plugin do C# é inicializado. O parâmetros `Config` são divididos em duas partes: `stormConf` e `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` são parâmetros definidos pelo Storm e `pluginConf` são os parâmetros definidos pelo SCP. Por exemplo:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` é fornecido para obter o contexto de topologia, ele é mais útil para componentes com vários paralelismo. Veja um exemplo:

    //demo how to get TopologyContext info
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)                      
    {
        Context.Logger.Info("TopologyContext info:");
        TopologyContext topologyContext = Context.TopologyContext;                    
        Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());          
        taskIndex = topologyContext.GetThisTaskIndex();
        Context.Logger.Info("taskIndex: {0}", taskIndex);
        string componentId = topologyContext.GetThisComponentId();                    
        Context.Logger.Info("componentId: {0}", componentId);
        List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
        Context.Logger.Info("taskNum: {0}", componentTasks.Count);                    
    }

### <a name="dynamic-part"></a>Parte Dinâmica

As seguintes interfaces são pertinentes a uma determinada instância do Contexto. A instância do Contexto é criada pela plataforma do SCP.NET e transmitida ao código do usuário:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Para o reconhecimento do suporte a spout não transacional, o seguinte método é fornecido:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Para ack de suporte a bolt não transacional, ele deve exibir explicitamente `Ack()` ou `Fail()` para a tupla recebida. E, ao emitir uma nova tupla, também deve especificar as âncoras da nova tupla. Os seguintes métodos são fornecidos.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore

`StateStore` oferece serviços de metadados, geração de sequência monotônica e coordenação sem espera. Abstrações de simultaneidade distribuídas de níveis mais altos podem ser baseadas no `StateStore`, incluindo bloqueios distribuídos, filas distribuídas, barreiras e serviços transacionais.

Os aplicativos do SCP podem usar o objeto `State` para persistir algumas informações no [Apache ZooKeeper](https://zookeeper.apache.org/), especialmente para a topologia transacional. Ao fazer isso, se o spout transacional travar e for reiniciado, ele poderá recuperar as informações necessárias do ZooKeeper e reiniciar o pipeline.

O objeto `StateStore` tem principalmente os seguintes métodos:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

O objeto `State` tem principalmente os seguintes métodos:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

Para o método `Commit()`, quando simpleMode for definido como true, ele excluirá o ZNode correspondente no ZooKeeper. Caso contrário, ele excluirá o ZNode atual e adicionará um novo nó no COMMITTED\_PATH.

### <a name="scpruntime"></a>SCPRuntime

O SCPRuntime oferece os dois métodos a seguir:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` é usado para inicializar o ambiente de runtime do SCP. Nesse método, o C# processo se conecta ao lado do Java e obtém os parâmetros de configuração e o contexto da topologia.

`LaunchPlugin()` é usado para iniciar o loop de processamento de mensagem. Nesse loop, o C# plug-in recebe mensagens do lado do Java (incluindo tuplas e sinais de controle) e, em seguida, processa as mensagens, talvez chamando o método de interface fornecido pelo código do usuário. O parâmetro de entrada para o método `LaunchPlugin()` é um delegado que pode retornar um objeto que implementa a interface ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Para ISCPBatchBolt, podemos obter `StormTxAttempt` de `parms`e usá-lo para avaliar se é uma tentativa reproduzida. A verificação de uma tentativa de reprodução geralmente é feita no parafuso de confirmação e é demonstrada no exemplo de `HelloWorldTx`.

Em termos gerais, os plugins do SCP podem ser executados em dois modos aqui:

1. Modo de Teste Local: nesse modo, os plug-ins do SCP (o código de usuário C#) é executado dentro do Visual Studio durante a fase de desenvolvimento. `LocalContext` pode ser usado nesse modo, oferecendo um método para serializar as tuplas emitidas para os arquivos locais e lê-los de volta na memória.

        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }

2. Modo Regular: nesse modo, os plug-ins do SCP são lançados pelo processo de Java do Storm.

    Aqui está um exemplo da inicialização do plugin do SCP:

        namespace Scp.App.HelloWorld
        {
        public class Generator : ISCPSpout
        {
            … …
            public static Generator Get(Context ctx, Dictionary<string, Object> parms)
            {
            return new Generator(ctx);
            }
        }
   
        class HelloWorld
        {
            static void Main(string[] args)
            {
            /* Setting the environment variable here can change the log file name */
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");
   
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
            }
        }
        }

## <a name="topology-specification-language"></a>Linguagem de Especificação da Topologia

A Especificação da Topologia do SCP é uma linguagem específica de domínio para descrever e configurar topologias do SCP. Ele é baseado na Clojure DSL do Storm (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) e é estendido pelo SCP.

As especificações de topologia podem ser enviadas diretamente ao cluster do Storm para execução por meio do comando ***runspec***.

O SCP.NET adicionou as seguintes funções para definir as Topologias Transacionais:

| Novas funções | Parâmetros | Description |
| --- | --- | --- |
| TX-topolopy |topology-name<br />spout-map<br />bolt-map |Defina uma topologia transacional com o nome da topologia, o &nbsp;mapa de definição de spouts e o mapa de definição de bolts |
| SCP-TX-Spout |exec-name<br />args<br />fields |Defina um spout transacional. Ele executa o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***fields*** são os Campos de Saída do spout |
| SCP-TX-lote-raio |exec-name<br />args<br />fields |Defina um bolt em lote transacional. Ele executa o aplicativo com ***exec-name*** usando ***args.***<br /><br />Os Fields são os Campos de Saída do bolt. |
| SCP-TX-Commit-parafuso |exec-name<br />args<br />fields |Defina um bolt de confirmação transacional. Ele executa o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***fields*** são os Campos de Saída do bolt |
| nontx-topolopy |topology-name<br />spout-map<br />bolt-map |Defina uma topologia não transacional com o nome da topologia, o &nbsp;mapa de definição de spouts e o mapa de definição de bolts |
| SCP-Spout |exec-name<br />args<br />fields<br />parâmetros |Defina um spout não transacional. Ele executa o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***fields*** são os Campos de Saída do spout<br /><br />Os ***parâmetros*** são opcionais, usados para especificar alguns parâmetros como "nontransactional.ack.enabled". |
| SCP |exec-name<br />args<br />fields<br />parâmetros |Defina um bolt não transacional. Ele executa o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***fields*** são os Campos de Saída do bolt<br /><br />Os ***parâmetros*** são opcionais, usados para especificar alguns parâmetros como "nontransactional.ack.enabled". |

O SCP.NET tem as seguintes palavras-chave definidas:

| Palavras-chave | Description |
| --- | --- |
| : nome |Defina o nome da topologia |
| : topologia |Defina a Topologia usando as funções acima e as internas. |
| :p |Defina a dica de paralelismo para cada spout ou bolt. |
| : config |Defina o parâmetro de configuração ou atualize os existentes |
| : esquema |Defina o esquema do fluxo. |

E os parâmetros usados com frequência:

| Parâmetro | Description |
| --- | --- |
| "plugin.name" |nome do arquivo exe do plugin do C# |
| "plugin. Args" |args do plugin |
| "output. Schema" |Esquema de saída |
| "transacional. ACK. Enabled" |Se o reconhecimento está ativado para a topologia não transacional |

O comando runspec é implantado juntamente com os bits, o uso é semelhante a:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

O parâmetro ***Resource-dir*** é opcional, você precisa especificá-lo quando desejar conectar um C# aplicativo, e esse diretório conterá o aplicativo, as dependências e as configurações.

O parâmetro ***classpath*** também é opcional. Ele é usado para especificar o classpath do Java se o arquivo de especificação contiver Spout ou parafuso do Java.

## <a name="miscellaneous-features"></a>Recursos diversos

### <a name="input-and-output-schema-declaration"></a>Declaração de esquema de entrada e saída

Os usuários podem emitir tuplas C# em processos, a plataforma precisa serializar a tupla em byte [], transferir para o lado do Java e o Storm transferirá essa tupla para os destinos. Enquanto isso, nos componentes C# downstream, os processos receberão tuplas de volta do lado do Java e os converterão para os tipos originais por plataforma, todas essas operações serão ocultadas pela plataforma.

Para oferecer suporte à serialização e desserialização, o código do usuário precisa declarar o esquema das entradas e saídas.

O esquema do fluxo de entrada/saída é definido como um dicionário. A chave é o StreamId. O valor são os Tipos das colunas. O componente pode ter múltiplos fluxos declarados.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }


No objeto de Contexto, temos a seguinte API adicionada:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Os desenvolvedores devem assegurar-se de que as tuplas emitidas obedeçam ao esquema definido para esse fluxo, caso contrário, o sistema lançará uma exceção de runtime.

### <a name="multi-stream-support"></a>Suportes a múltiplos fluxos

O SCP oferece suporte ao código do usuário para emitir ou receber de vários fluxos diferentes ao mesmo tempo. O suporte é refletido no objeto de Contexto conforme o método de Emissão assume um parâmetro de ID de fluxo opcional.

Dois métodos no objeto de Contexto do SCP.NET foram adicionados. Eles são usados para emitir tupla ou tuplas para especificar Streamid. O StreamId é uma cadeia de caracteres e precisa ser consistente no C# e na Especificação de Definição da Topologia.

    /* Emit tuple to the specific stream. */
    public abstract void Emit(string streamId, List<object> values);

    /* for non-transactional Spout only */
    public abstract void Emit(string streamId, List<object> values, long seqId);

A emissão para um fluxo não existente causará exceções de runtime.

### <a name="fields-grouping"></a>Agrupamento de Campos

O agrupamento de campos internos no Storm não está funcionando corretamente no SCP.NET. No lado do Proxy Java, todos os tipos de dados de campo são na verdade byte[] e o agrupamento de campos usa o código hash do objeto byte[] para realizar o agrupamento. O código hash do objeto byte[] é o endereço desse objeto na memória. Portanto, o agrupamento ficará incorreto para objetos de dois bytes que compartilham o mesmo conteúdo, mas não o mesmo endereço.

O SCP.NET adiciona um método de agrupamento personalizado e utiliza o conteúdo de byte[] para realizar o agrupamento. No arquivo **SPEC** , a sintaxe é parecida com:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )

Aqui,

1. “scp-field-group” significa “Agrupamento de campo personalizado implementado pelo SCP”.
2. “:tx” ou “:non-tx” significa que se trata de uma topologia transacional. Precisamos dessa informação, uma vez que o índice de início é diferente em topologias tx em relação às não tx.
3. [0,1] significa um conjunto de hash dos IDs do campo, começando do 0.

### <a name="hybrid-topology"></a>Topologia híbrida

O Storm nativo é escrito em Java. E o SCP.NET o aprimorou para permitir C# que os desenvolvedores C# escrevam código para lidar com a lógica de negócios. Mas ele também dá suporte a topologias híbridas, C# que contêm não apenas esgotamentos/parafusos, mas também Spout/parafusos Java.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Especificar o Spout/Bol do Java no arquivo de especificações

No arquivo de especificações, "scp-spout" e "scp-bolt" também podem ser usados para especificar Spouts e Bolts do Java, aqui está um exemplo:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Aqui `microsoft.scp.example.HybridTopology.Generator` é o nome da classe Spout do Java.

### <a name="specify-java-classpath-in-runspec-command"></a>Especificar o caminho de classe do Java no comando runSpec

Se quiser enviar uma topologia contendo Spouts ou Bolts do Java, será necessário primeiro compilar os Spouts ou Bolts do Java e obter os arquivos Jar. Depois, você deve especificar o caminho de classe do Java que contém os arquivos Jar ao enviar a topologia. Veja um exemplo:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Aqui, **examples\\HybridTopology\\java\\target\\** é a pasta que contém o arquivo Jar do Spout/Bolt.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialização e desserialização entre Java eC#

O componente SCP inclui o lado C# do Java e o lado. Para interagir com Spouts/Bolts Java nativos, a Serialização/Desserialização deve ser realizada entre o lado do Java e C#, conforme ilustrado no grafo a seguir.

![diagrama do componente java enviando ao componente SCP enviando ao componente Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. Serialização no lado do Java e desserialização C# no lado

   Primeiro, fornecemos a implementação padrão para serialização no lado do Java e a desserialização no lado do C#. O método de serialização no lado do Java pode ser especificado no arquivo SPEC:

       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })

   O método de desserialização no lado do C# deve ser especificado no código do usuário do C#:

       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            

   Essa implementação padrão deve lidar com a maioria dos casos, desde que o tipo de dados não seja muito complexo. Para determinados casos, porque o tipo de dados do usuário é muito complexo ou porque o desempenho de nossa implementação padrão não atende ao requisito do usuário, os usuários podem conectar sua própria implementação.

   A interface de serialização no lado do Java é definida como:

       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }

   A interface de desserialização no lado do C# é definida como:

   interface pública ICustomizedInteropCSharpDeserializer

       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. Serialização no C# lado e desserialização no lado do Java

   O método de serialização no lado do C# deve ser especificado no código do usuário do C#:

       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 

   O método de Desserialização no lado do Java deve ser especificado no arquivo SPEC:

    ```
    (scp-spout
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       }
    )
    ```

   Aqui "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" é o nome do Desserializador e "microsoft.scp.example.HybridTopology.Person" é a classe de destino para a qual os dados são desserializados.

   O usuário também pode conectar sua própria implementação do C# serializador e do desserializador Java. Esse código é a interface para C# o serializador:

       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }

   Esse código é a interface para o Desserializador Java:

       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Modo de host do SCP

Nesse modo, o usuário pode compilar seus códigos como DLL e usar o SCPHost.exe fornecido pelo SCP para enviar a topologia. O arquivo de especificações parece este código:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Aqui, `plugin.name` é especificado como `SCPHost.exe` fornecido pelo SDK do SCP. O SCPHost.exe aceita três parâmetros:

1. O primeiro é o nome DLL, que é `"HelloWorld.dll"` neste exemplo.
2. O segundo é o nome da Classe, que é `"Scp.App.HelloWorld.Generator"` neste exemplo.
3. O terceiro é o nome de um método estático público, que pode ser invocado para obter uma instância do ISCPPlugin.

No modo do host, o código do usuário é compilado como DLL e é invocado pela plataforma SCP. Por isso, a plataforma do SCP pode ter controle total de toda a lógica de processamento. Portanto, recomendamos que nossos clientes enviem a topologia no modo do host do SCP, uma vez que isso pode simplificar a experiência do desenvolvimento e oferecer mais flexibilidade e uma maior compatibilidade com versões anteriores também para versões posteriores.

## <a name="scp-programming-examples"></a>Exemplos de programação do SCP

### <a name="helloworld"></a>HelloWorld

**HelloWorld** é um exemplo simples para mostrar um gosto de SCP.net. Ele usa uma topologia não transacional com um spout chamado **generator** e dois bolts chamados **splitter** e **counter**. O spout **generator** gera frases aleatoriamente e as envia para o **splitter**. O separador de raio * * divide as frases em palavras e emite essas palavras ao **balcão** . O bolt “counter” usa um dicionário para registrar o número de ocorrências de cada palavra.

Há dois arquivos de especificações, **HelloWorld.spec** e **HelloWorld\_EnableAck.spec** para este exemplo. No código do C#, ele pode descobrir se o reconhecimento está habilitado obtendo o pluginConf no lado do Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

No Spout, se ACK estiver habilitado, um dicionário será usado para armazenar em cache as tuplas que não foram confirmadas. Se Fail() for chamado, a tupla com falha será reproduzida:

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        Context.Logger.Info("Fail, seqId: {0}", seqId);
        if (cachedTuples.ContainsKey(seqId))
        {
            /* get the cached tuple */
            string sentence = cachedTuples[seqId];

            /* replay the failed tuple */
            Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
        }
        else
        {
            Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
        }
    }

### <a name="helloworldtx"></a>HelloWorldTx

O exemplo **HelloWorldTx** demonstra como implementar a topologia transacional. Ele tem um spout chamado **generator**, um bolt em lote chamado **partial-count** e um bolt de confirmação chamado **count-sum**. Também há três arquivos txt pré-criados: **DataSource0.txt**, **DataSource1.txt** e **DataSource2.txt**.

Em cada transação, o spout **generator** seleciona aleatoriamente dois arquivos dos três arquivos pré-criados e emite os dois nomes de arquivo para o bolt **partial-count**. O bolt **partial-count** obtém o nome do arquivo da tupla recebida, em seguida, abre o arquivo e conta o número de palavras nesse arquivo e, por fim, emite o número de palavras para o bolt **count-sum**. O bolt **count-sum** resume a contagem total.

Para atingir a semântica **exatamente uma vez** , a **soma da contagem** de confirmações precisa avaliar se é uma transação reproduzida. Neste exemplo, ele possui uma variável do membro estático:

    public static long lastCommittedTxId = -1; 

Quando uma instância ISCPBatchBolt é criada, ela obtém `txAttempt` dos parâmetros de entrada:

    public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
    {
        /* for transactional topology, we can get txAttempt from the input parms */
        if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
        {
            StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
            return new CountSum(ctx, txAttempt);
        }
        else
        {
            throw new Exception("null txAttempt");
        }
    }

Quando `FinishBatch()` for chamado, o `lastCommittedTxId` será atualizado se não for uma transação reproduzida.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }

### <a name="hybridtopology"></a>HybridTopology

Essa topologia contém um Spout do Java e um Bolt do C#. Ela usa a implementação de serialização e desserialização padrão fornecida pela plataforma do SCP. Consulte **HybridTopology.spec** na pasta **examples\\HybridTopology** para obter os detalhes do arquivo de especificações e **SubmitTopology.bat** para saber como especificar o caminho de classe de Java.

### <a name="scphostdemo"></a>SCPHostDemo

Este exemplo é igual ao HelloWorld em essência. A única diferença é que o código do usuário é compilado como DLL e a topologia é enviada usando SCPHost.exe. Consulte a seção "Modo de host do SCP" para obter mais explicações detalhadas.

## <a name="next-steps"></a>Próximas etapas

Para ver exemplos de topologias Apache Storm criadas usando o SCP, consulte os documentos a seguir:

* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Processar dados de sensor de veículo nos Hubs de Eventos usando o Apache Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [ETL (Extração, Transformação e Carregamento) de Hubs de Eventos do Azure para Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
