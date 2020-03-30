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
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186873"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Guia de programação SCP para Apache Storm no Azure HDInsight

O SCP é uma plataforma para criar aplicativos de processamento de dados em tempo real, confiáveis, consistentes e de alto desempenho. Ele é construído em cima do [Apache Storm,](https://storm.incubator.apache.org/)que é um sistema de processamento de fluxo projetado por comunidades de software de código aberto. Nathan Marz criou a Tempestade. Foi publicado como código aberto pelo Twitter. Storm usa [o Apache ZooKeeper](https://zookeeper.apache.org/), que é outro projeto Apache que permite coordenação distribuída altamente confiável e gerenciamento do estado.

O projeto SCP portou não apenas o Storm no Windows, mas também extensões adicionadas de projeto e personalização para o ambiente Windows. As extensões incluem a experiência de desenvolvedor .NET e bibliotecas .NET. A personalização inclui a implantação baseada no Windows.

Com as extensões e a personalização, você não precisa bifurcar os projetos de software de código aberto. Você pode usar ambientes derivados que são construídos em cima da Tempestade.

## <a name="processing-model"></a>Modelo de processamento

Os dados no SCP são modelados como fluxos contínuos de tuplas. Normalmente, as tuplas:

1. Flua para uma fila.
1. São captados e transformados pela lógica de negócios hospedada dentro de uma topologia da Tempestade.
1. Ou têm sua saída canalizada como tuplas para outro sistema SCP ou estão comprometidas com lojas como sistemas de arquivos distribuídos e bancos de dados como o SQL Server.

![Um diagrama de uma fila alimentando dados para processamento, que por sua vez alimenta um armazenamento de dados](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Em Storm, uma topologia de aplicação define um gráfico de computação. Cada nó em uma topologia contém lógica de processamento. Links entre nós indicam fluxo de dados.

Nós que injetam dados de entrada na topologia são chamados _de bicos_. Você pode usá-los para sequenciar os dados. Os dados de entrada podem vir de uma fonte como logs de arquivos, um banco de dados transacional ou um contador de desempenho do sistema.

Os nós que têm fluxos de dados de entrada e saída são chamados _de parafusos_. Eles fazem a filtragem de dados, seleções e agregação de dados reais.

O SCP suporta os melhores esforços, pelo menos uma vez e exatamente uma vez o processamento de dados.

Em um aplicativo de processamento de fluxo distribuído, erros podem acontecer durante o processamento de dados. Tais erros incluem uma falha de rede, uma falha na máquina ou um erro em seu código. Pelo menos uma vez o processamento garante que todos os dados sejam processados pelo menos uma vez, reproduzindo automaticamente os mesmos dados quando um erro acontece.

Pelo menos uma vez o processamento é simples e confiável, e serve para muitas aplicações. Mas quando um aplicativo requer contagem exata, pelo menos uma vez o processamento é insuficiente porque os mesmos dados podem ser reproduzidos na topologia do aplicativo. Nesse caso, exatamente uma vez que o processamento garante que o resultado esteja correto mesmo quando os dados são reproduzidos e processados várias vezes.

O SCP permite que os desenvolvedores do .NET criem aplicativos de processamento de dados em tempo real enquanto usam uma Máquina Virtual Java (JVM) com o Storm. Uma comunicação JVM e .NET através de soquetes locais TCP. Cada bico/parafuso é um par de processos .NET/Java, onde a lógica do usuário é executada em um processo .NET como um plug-in.

Para construir um aplicativo de processamento de dados em cima do SCP, siga estas etapas:

1. Projete e implemente bicos para extrair dados de filas.
1. Projete e implemente parafusos que processam os dados de entrada e os salvem em lojas externas como um banco de dados.
1. Projete a topologia, depois envie e execute-a.

A topologia define vértices e os dados que fluem entre eles. O SCP pega uma especificação de topologia e implanta-a em um cluster Tempestade, onde cada vértice é executado em um nó lógico. O agendador de tarefas Storm cuida do failover e do dimensionamento.

Este artigo usa alguns exemplos simples para percorrer como construir aplicativos de processamento de dados com SCP.

## <a name="scp-plug-in-interface"></a>Interface plug-in SCP

Os plug-ins SCP são aplicativos autônomos. Eles podem ser executados dentro do Visual Studio durante o desenvolvimento e serem conectados ao gasoduto Storm após a implantação da produção.

Escrever um plug-in SCP é o mesmo que escrever qualquer outro aplicativo do console Windows. A plataforma SCP.NET declara algumas interfaces para bico/parafuso. Seu código plug-in implementa essas interfaces. O principal objetivo deste design é permitir que você se concentre na lógica do seu negócio, deixando a plataforma SCP.NET lidar com outras coisas.

Seu código plug-in implementa uma das seguintes interfaces. Qual interface depende se a topologia é transacional ou não transacional e se o componente é um bico ou um parafuso.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** é a interface comum para muitos plug-ins. Atualmente, é uma interface.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** é a interface para um bico não transacional.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Quando **nextTuple** é chamado, seu código C# pode emite uma ou mais tuplas. Se não há nada para emitir, este método deve retornar sem emitir nada.

Os métodos **NextTuple**, **Ack**e **Fail** são todos chamados em um loop apertado em um único segmento de um processo C#. Quando não houver tuplas para emitir, faça **o NextTuple** dormir por um curto período de tempo como 10 milissegundos. Esse sono ajuda a evitar a perda de disponibilidade da CPU.

Os métodos **Ack** e **Fail** são chamados apenas quando um arquivo de especificação habilita o mecanismo de reconhecimento. O *parâmetro seqId* identifica a tuple que é reconhecida ou falhou. Se o reconhecimento estiver ativado em uma topologia não transacional, a seguinte função **Emit** deve ser usada em um bico:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Se uma topologia não transacional não suportar o reconhecimento, **Ack** e **Fail** podem ser deixados como funções vazias.

O parâmetro de entrada *de parms* nessas funções especifica um dicionário vazio e é reservado para uso futuro.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** é a interface para um parafuso não transacional.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Quando um novo tuplo está disponível, a função **Execute** é chamada para processá-la.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** é a interface para um bico transacional.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Assim como suas contrapartes não transacionais, **NextTx**, **Ack**e **Fail** são todos chamados em um loop apertado em um único segmento de um processo C#. Quando não houver tuplas para emitir, faça **o NextTx** dormir por um curto período de tempo como 10 milissegundos. Esse sono ajuda a evitar a perda de disponibilidade da CPU.

Quando **o NextTx** é chamado para iniciar uma nova transação, o parâmetro *seqId* output identifica a transação. A transação também é usada em **Ack** e **Fail**. O método **NextTx** pode emitir dados para o lado Java. Os dados são armazenadas no ZooKeeper para dar suporte à reprodução. Como o ZooKeeper tem capacidade limitada, seu código deve emitir apenas metadados e não dados em massa em um bico transacional.

Como o Storm reproduz automaticamente uma transação com falha, **o Fail** geralmente não será chamado. Mas se o SCP puder verificar os metadados emitidos por um bico transacional, ele pode chamar **Fail** quando os metadados são inválidos.

O parâmetro de entrada *de parms* nessas funções especifica um dicionário vazio e é reservado para uso futuro.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** é a interface de um parafuso transacional.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

O método **Execute** é chamado quando uma nova tupla chega ao parafuso. O método **FinishBatch** é chamado quando essa transação termina. O parâmetro de entrada *de parms* é reservado para uso futuro.

Para uma topologia transacional, **StormTxTry** é uma classe importante. Tem dois membros: **TxId** e **AttemptId**. O membro **TxId** identifica uma transação específica. Uma transação pode ser tentada várias vezes se falhar e for repetida.

SCP.NET cria um novo objeto **ISCPBatchBolt** para processar cada objeto **StormTxTry,** assim como o que o Storm faz em Java. O objetivo deste projeto é apoiar o processamento de transações paralelas. Após a conclusão de uma tentativa de transação, o objeto **ISCPBatchBolt** correspondente é destruído e o lixo coletado.

## <a name="object-model"></a>Modelo de objeto

O SCP.NET também oferece um conjunto simples de objetos chave para que os desenvolvedores realizem a programação. Os objetos são **Context,** **StateStore**e **SCPRuntime**. Eles são discutidos nesta seção.

### <a name="context"></a>Contexto

O objeto **Contexto** fornece um ambiente em execução para um aplicativo. Cada instância **ISCPPlugin** do **ISCPSpout,** **ISCPBolt,** **ISCPTxSpout**ou **ISCPBatchBolt** tem uma instância **de contexto** correspondente. A funcionalidade fornecida pelo **Context** é dividida nessas duas partes:

* A parte estática, que está disponível em todo o processo C#
* A parte dinâmica, que está disponível apenas para a instância específica do **Contexto**

### <a name="static-part"></a>Parte estática

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

O objeto **Logger** é fornecido para fins de registro.

O objeto **pluginType** indica o tipo de plug-in do processo C#. Se o processo for executado no modo de teste local sem Java, o tipo de plug-in será **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

A propriedade **Config** obtém parâmetros de configuração do lado Java, que os passa quando um plug-in C# é inicializado. Os parâmetros **config** são divididos em duas partes: **stormConf** e **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

A parte **stormConf** é parâmetros definidos por Storm, e a parte **pluginConf** é parâmetros definidos por SCP. Aqui está um exemplo:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

O tipo **TopologyContext** obtém o contexto de topologia. É mais útil para múltiplos componentes paralelos. Aqui está um exemplo:

```csharp
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
```

### <a name="dynamic-part"></a>Parte dinâmica

As seguintes interfaces são pertinentes a uma determinada instância **de contexto,** que é criada pela plataforma SCP.NET e passada para o seu código:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Para um bico não transacional que suporte o reconhecimento, o seguinte método é fornecido:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Um parafuso não transacional que suporta reconhecimento deve ligar explicitamente para **Ack** ou **Fail** com a tupla recebida. Ao emitir uma nova tuple, o parafuso também deve especificar as âncoras do tuple. Os seguintes métodos são fornecidos:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

O objeto **StateStore** fornece serviços de metadados, geração de seqüências monótonicas e coordenação sem espera. Você pode construir abstrações de concorrência distribuída de nível superior no **StateStore**. Essas abstrações incluem bloqueios distribuídos, filas distribuídas, barreiras e serviços de transação.

Os aplicativos SCP podem usar o objeto **Estado** para serializar informações no [Apache ZooKeeper](https://zookeeper.apache.org/). Essa habilidade é especialmente valiosa para uma topologia transacional. Se um bico transacional parar de responder e reiniciar, o **Estado** poderá recuperar as informações necessárias do ZooKeeper e reiniciar o pipeline.

O objeto **StateStore** tem esses métodos principais:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

O objeto **Estado** tem esses principais métodos:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Quando **simpleMode** é definido como **true,** o método **Commit** exclui o ZNode correspondente no ZooKeeper. Caso contrário, o método exclui o ZNode atual e\_adiciona um novo nó no CAMINHO COMPROMETIDO.

### <a name="scpruntime"></a>SCPRuntime

A classe **SCPRuntime** fornece os dois métodos a seguir:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

O método **Initialize** inicializa o ambiente de tempo de execução SCP. Neste método, o processo C# se conecta ao lado Java para obter parâmetros de configuração e contexto de topologia.

O método **LaunchPlugin** inicia o loop de processamento de mensagens. Neste loop, o plug-in C# recebe mensagens do lado Java. Essas mensagens incluem tuplas e sinais de controle. O plug-in então processa as mensagens, talvez chamando o método de interface fornecido pelo seu código.

O parâmetro de entrada **para LaunchPlugin** é um delegado. O método pode retornar um objeto que implementa a interface **ISCPSpout,** **ISCPBolt,** **ISCPTxSpout**ou **ISCPBatchBolt.**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Para **ISCPBatchBolt,** você pode obter um objeto **StormTxTry** do parâmetro *parms* e usá-lo para julgar se a tentativa é uma tentativa repetida. A verificação para uma tentativa de repetição é muitas vezes feita no parafuso de confirmação. O exemplo do HelloWorldTx mais tarde neste artigo demonstra esta verificação.

Os plug-ins SCP geralmente podem ser executados em dois modos: modo de teste local e modo regular.

#### <a name="local-test-mode"></a>Modo de teste local

Neste modo, os plug-ins SCP em seu código C# são executados dentro do Visual Studio durante a fase de desenvolvimento. Você pode usar a interface **ILocalContext** neste modo. A interface fornece métodos para serializar as tuplas emitidas para arquivos locais e lê-las de volta na RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Modo regular

Neste modo, o processo Storm Java executa os plug-ins SCP. Aqui está um exemplo:

```csharp
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
```

## <a name="topology-specification-language"></a>Linguagem de Especificação de Topologia

SCP Topology Specification é uma linguagem específica de domínio (DSL) para descrever e configurar topologias SCP. É baseado no [Clojure DSL da Storm](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) e é estendido pelo SCP.

Você pode enviar especificações de topologia diretamente para um cluster Tempestade para execução através do comando **runSpec.**

SCP.NET adicionou as seguintes funções para definir topologias transacionais:

| Nova função | Parâmetros | Descrição |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |Define uma topologia transacional com o nome topologia, o mapa de definição de bicos e o mapa de definição de parafusos. |
| **scp-tx-spout** |*exec-name*<br />*Args*<br />*campos* |Define um bico transacional. A função executa o aplicativo especificado pelo *exec-name* e usa *args*.<br /><br />O parâmetro *campos* especifica os campos de saída para o bico. |
| **scp-tx-batch-bolt** |*exec-name*<br />*Args*<br />*campos* |Define um parafuso de lote transacional. A função executa o aplicativo especificado pelo *exec-name* e usa *args.*<br /><br />O parâmetro *campos* especifica os campos de saída para o parafuso. |
| **scp-tx-commit-bolt** |*exec-name*<br />*Args*<br />*campos* |Define um parafuso de confirmação transacional. A função executa o aplicativo especificado pelo *exec-name* e usa *args*.<br /><br />O parâmetro *campos* especifica os campos de saída para o parafuso. |
| **nontx-topologia** |*topology-name*<br />*spout-map*<br />*bolt-map* |Define uma topologia não transacional com o nome de topologia, mapa de definição de bicos e mapa de definição de parafusos. |
| **scp-spout** |*exec-name*<br />*Args*<br />*campos*<br />*Parâmetros* |Define um bico não transacional. A função executa o aplicativo especificado pelo *exec-name* e usa *args*.<br /><br />O parâmetro *campos* especifica os campos de saída para o bico.<br /><br />O parâmetro *dos parâmetros* é opcional. Use-o para especificar parâmetros como "nontransactional.ack.enabled". |
| **scp-bolt** |*exec-name*<br />*Args*<br />*campos*<br />*Parâmetros* |Define um parafuso não transacional. A função executa o aplicativo especificado pelo *exec-name* e usa *args*.<br /><br />O parâmetro *campos* especifica os campos de saída para o parafuso<br /><br />O parâmetro *dos parâmetros* é opcional. Use-o para especificar parâmetros como "nontransactional.ack.enabled". |

SCP.NET define as seguintes palavras-chave:

| Palavra-chave | Descrição |
| --- | --- |
| **:nome** |O nome da topologia |
| **:topologia** |A topologia usando as funções na tabela anterior e funções incorporadas |
| **:p** |A dica de paralelismo para cada bico ou parafuso |
| **:config** |Seja para configurar parâmetros ou atualizar os já existentes |
| **:esquema** |O esquema do córrego |

SCP.NET também define esses parâmetros freqüentemente utilizados:

| Parâmetro | Descrição |
| --- | --- |
| "plugin.name" |O nome do arquivo .exe do plug-in C# |
| "plugin.args" |Os argumentos de plug-in |
| "output.schema" |O esquema de saída |
| "nontransactional.ack.enabled" |Se o reconhecimento está habilitado para uma topologia não transacional |

O comando **runSpec** é implantado juntamente com os bits. Aqui está o uso do comando:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

O *parâmetro resource-dir* é opcional. Especifique-o quando quiser conectar um aplicativo C#. O diretório especificado contém o aplicativo, as dependências e as configurações.

O parâmetro *classpath* também é opcional. Ele especifica o java classpath se o arquivo de especificação contiver um bico java ou um parafuso.

## <a name="miscellaneous-features"></a>Características diversas

### <a name="input-and-output-schema-declarations"></a>Declarações de esquema de entrada e saída

Seus processos C# podem emite tuplas. Para isso, a plataforma serializa tuplas em objetos **byte[]** e transfere os objetos para o lado Java. A tempestade então transfere essas tuplas para os alvos.

Nos componentes a jusante, os processos C# recebem tuplas de volta do lado Java e convertem-nas para os tipos originais da plataforma. Todas essas operações estão escondidas pela plataforma.

Para suportar serialização e desserialização, seu código precisa declarar o esquema da entrada e saída. O esquema é definido como um dicionário. O ID do fluxo é a chave do dicionário. O valor-chave são os tipos das colunas. Um componente pode declarar vários fluxos.

```csharp
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
```

A seguinte função é adicionada a um objeto **Contexto:**

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Os desenvolvedores devem garantir que as tuplas emitidas obedeçam ao esquema definido para um fluxo. Caso contrário, o sistema lançará uma exceção de tempo de execução.

### <a name="multistream-support"></a>Suporte multistream

O SCP permite que seu código emita ou receba de vários fluxos distintos ao mesmo tempo. O objeto **Contexto** reflete esse suporte como parâmetro opcional de ID de fluxo do método **Emit.**

Dois métodos no objeto SCP.NET **Context** foram adicionados. Eles emitem uma ou mais tuplas para fluxos específicos. O parâmetro *streamId* é uma string. Seu valor deve ser o mesmo tanto no código C# quanto na especificação de definição de topologia.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

A emissão de um fluxo inexistente causa exceções de tempo de execução.

### <a name="fields-grouping"></a>Agrupamento de campos

O agrupamento de campos embutidos em Storm não funciona corretamente em SCP.NET. No lado proxy Java, o tipo de dados de todos os campos é realmente **byte[]**. O agrupamento de campos usa o código hash do objeto **byte[]** para fazer o agrupamento. O código hash é o endereço deste objeto na RAM. Assim, o agrupamento será errado para objetos multibytes que compartilham o mesmo conteúdo, mas não o mesmo endereço.

SCP.NET adiciona um método de agrupamento personalizado e usa o conteúdo do objeto **byte[]** para fazer o agrupamento. Em um arquivo de especificação, a sintaxe se parece com este exemplo:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

No arquivo de especificação anterior:

* `scp-field-group`especifica que o agrupamento é um agrupamento de campo personalizado implementado pelo SCP.
* `:tx`ou `:non-tx` especifica se a topologia é transacional. Você precisa dessas informações porque o índice inicial é diferente entre topologias transacionais e não transacionais.
* `[0,1]`especifica um conjunto de hash de IDs de campo que começam com zero.

### <a name="hybrid-topology"></a>Topologia híbrida

O código de tempestade nativa é escrito em Java. SCP.NET melhorou o Storm para permitir que você escreva o código C# para lidar com a lógica de negócios. Mas SCP.NET também suporta topoologias híbridas, que contêm não apenas bicos/parafusos C#, mas também bicos/parafusos Java.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Especificar bico/parafuso java em um arquivo de especificação

Você pode usar **scp-spout** e **scp-bolt** em um arquivo de especificação para especificar bicos e parafusos Java. Aqui está um exemplo:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Aqui `microsoft.scp.example.HybridTopology.Generator` está o nome da classe java bico.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Espece o caminho de classe Java em um comando runSpec

Se você quiser enviar topologia que contenha bicos java ou parafusos, primeiro compile-os para produzir arquivos JAR. Em seguida, especifique o java classpath que contém os arquivos JAR quando você enviar topologia. Aqui está um exemplo:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Aqui, `examples\HybridTopology\java\target\` está a pasta contendo o arquivo Java spout/bolt JAR.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialização e desserialização entre Java e C #

Um componente SCP inclui o lado Java e o lado C#. Para interagir com os bicos/parafusos java nativos, a serialização e a desserialização devem ocorrer entre o lado Java e o lado C#, conforme ilustrado no gráfico a seguir:

![Diagrama de componente Java enviando para o componente SCP, que então envia para um componente Java diferente](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serialização no lado Java e desserialização no lado C#

Primeiro forneça a implementação padrão para serialização no lado Java e desserialização no lado C#.

Especifique o método de serialização do lado Java em um arquivo de especificação.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Especifique o método de desserialização do lado C# em seu código C#.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Se o tipo de dados não for muito complexo, essa implementação padrão deve lidar com a maioria dos casos. Aqui estão os casos em que você pode conectar sua própria implementação:

* Seu tipo de dados é muito complexo para a implementação padrão.
* O desempenho da sua implementação padrão não atende aos seus requisitos.

A interface de serialização no lado Java é definida como:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

A interface de desserialização no lado C# é definida como:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serialização no lado C# e desserialização no lado Java

Especifique o método de serialização do lado C# em seu código C#.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Especifique o método de desserialização do lado Java em um arquivo de especificação.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Aqui, `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` é o nome do desserializador, e `"microsoft.scp.example.HybridTopology.Person"` é a classe de destino para a classe em que os dados são desserializados.

Você também pode conectar sua própria implementação de um serializador C# e um desserializador Java.

Este código é a interface do serializador C#:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Este código é a interface para o desserializador Java:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Modo host SCP

Neste modo, você pode compilar seu código como dll e usar SCPHost.exe conforme fornecido pelo SCP para enviar uma topologia. Um arquivo de especificação se parece com este código:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Aqui, `"plugin.name"` é especificado como `"SCPHost.exe"`, que é fornecido pelo SCP SDK. SCPHost.exe aceita três parâmetros na seguinte ordem:

1. O nome DLL, `"HelloWorld.dll"` que está neste exemplo.
1. O nome da `"Scp.App.HelloWorld.Generator"` classe, que está neste exemplo.
1. O nome de um método estático público, que pode ser invocado para obter uma instância de **ISCPPlugin**.

No modo host, compile seu código como dll para invocação pela plataforma SCP. Como a plataforma pode então obter controle total de toda a lógica de processamento, recomendamos que você envie topologia no modo host SCP. Fazer isso simplifica a experiência de desenvolvimento. Ele também traz mais flexibilidade e melhor compatibilidade para trás para versões posteriores.

## <a name="scp-programming-examples"></a>Exemplos de programação SCP

### <a name="helloworld"></a>HelloWorld

O exemplo simples do HelloWorld mostra um gostinho de SCP.NET. Ele usa uma topologia não transacional com um bico chamado **gerador** e dois parafusos chamados **divisor** e **contador**. O **bico do gerador** gera aleatoriamente frases e emite essas frases para **divisor**. O parafuso **divisor** divide as frases em palavras e emite estas palavras para o **parafuso do contador.** O **parafuso do contador** usa um dicionário para registrar a ocorrência de cada palavra.

Este exemplo tem dois arquivos de especificação:\_HelloWorld.spec e HelloWorld EnableAck.spec. O código C# pode descobrir se o reconhecimento `pluginConf` está ativado ao obter o objeto do lado Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Se o reconhecimento for ativado no bico, um dicionário armazena as tuplas que não foram reconhecidas. Se `Fail` for chamado, a tuple falhada é repetida.

```csharp
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
```

### <a name="helloworldtx"></a>HelloWorldTx

O exemplo a seguir do HelloWorldTx demonstra como implementar topologia transacional. O exemplo tem um bico chamado **gerador,** um parafuso de lote chamado **contagem parcial,** e um parafuso de confirmação chamado **count-sum**. O exemplo também tem três arquivos de texto existentes: DataSource0.txt, DataSource1.txt e DataSource2.txt.

Em cada transação, o bico do **gerador** seleciona aleatoriamente dois arquivos dos três arquivos existentes e emite os dois nomes de arquivo para o parafuso **de contagem parcial.** O parafuso **de contagem parcial:**

1. Obtém um nome de arquivo da tuplo recebida.
1. Abre o arquivo correspondente.
1. Conta o número de palavras no arquivo.
1. Emite a palavra contagem para o parafuso **de contagem.**

O bolt **count-sum** resume a contagem total.

Para alcançar exatamente uma vez semântica, o parafuso **de confirmação de contagem** precisa julgar se é uma transação repetida. Neste exemplo, ele tem a seguinte variável membro estático:

```csharp
public static long lastCommittedTxId = -1; 
```

Quando uma ocorrência **ISCPBatchBolt** é criada, ela `txAttempt` obtém o valor do objeto a partir de parâmetros de entrada.

```csharp
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
```

Quando `FinishBatch` é `lastCommittedTxId` chamado, é atualizado se não for uma transação repetida.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Esta topologia contém um bico Java e um parafuso C#. Ele usa a implementação padrão de serialização e desserialização fornecida pela plataforma SCP. Consulte o arquivo HybridTopology.spec\\na pasta de exemplos HybridTopology para obter os detalhes do arquivo de especificação. Consulte Também SubmitTopology.bat para saber como especificar o java classpath.

### <a name="scphostdemo"></a>SCPHostDemo

Este exemplo é, em essência, o mesmo que HelloWorld. A única diferença é que seu código é compilado como um DLL e a topologia é enviada usando SCPHost.exe. Consulte a seção de modo host SCP para obter uma explicação mais detalhada.

## <a name="next-steps"></a>Próximas etapas

Para exemplos de topologias apache storm criadas usando SCP, consulte os seguintes artigos:

* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Processe os dados do sensor do veículo dos Hubs de Eventos usando o Apache Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrair, transformar e carregar (ETL) dos Hubs de Eventos Do Azure para o Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
