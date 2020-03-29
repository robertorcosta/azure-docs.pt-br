---
title: ingerir dados do Logstash para o Azure Data Explorer
description: Neste artigo, você aprende como ingerir dados (carregar) no Azure Data Explorer do Logstash
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 86f6732cbf2409d3c79a3d7709100e8af24988a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66494537"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>ingerir dados do Logstash para o Azure Data Explorer

O [Logstash](https://www.elastic.co/products/logstash) é um software livre de pipeline de processamento de dados no lado do servidor que ingere dados de muitas origens simultaneamente, transforma os dados e, depois, envia os dados ao seu "stash" favorito. Neste artigo, você enviará esses dados para o Azure Data Explorer, que é um serviço de exploração de dados rápido e altamente escalável para dados de registro e telemetria. Inicialmente, você criará um mapeamento de tabela e dados em um cluster de teste e, depois, instruirá o Logstash a enviar dados para a tabela e validar os resultados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/), caso ainda não tenha uma, antes de começar.
* Um [cluster e banco de dados de teste](create-cluster-database-portal.md) do Azure Data Explorer
* [Instruções de instalação](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html) do Logstash versão 6+

## <a name="create-a-table"></a>Criar uma tabela

Depois que você tiver um cluster e um banco de dados, é hora de criar uma tabela.

1. Execute o comando a seguir na janela de consulta do banco de dados para criar uma tabela:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Execute o comando a seguir para confirmar que a nova tabela `logs` foi criada e está vazia:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Criar um mapeamento

O mapeamento é usado pelo Azure Data Explorer para transformar os dados recebidos no esquema da tabela de destino. O comando a seguir cria um mapeamento denominado `basicmsg` que extrai as propriedades do json de entrada, conforme indicado pelo `path`, e gera como saída para o `column`.

Execute o comando a seguir na janela de consulta:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Instalar o plug-in de saída do Logstash

O plug-in de saída do Logstash se comunica com o Azure Data Explorer e envia os dados para o serviço.
Execute o comando a seguir no diretório raiz do Logstash para instalar o plug-in:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Configurar o Logstash para gerar um conjunto de dados de exemplo

O Logstash consegue gerar eventos de exemplo que podem ser usados para testar um pipeline de ponta a ponta.
Se você já estiver usando o Logstash e tem acesso ao seu próprio fluxo de eventos, pule para a próxima seção. 

> [!NOTE]
> Se estiver usando seus próprios dados, altere os objetos de mapeamento e de tabela definidos nas etapas anteriores.

1. Edite um novo arquivo de texto que conterá as configurações de pipeline necessárias (usando o vi):

    ```sh
    vi test.conf
    ```

1. Cole as configurações a seguir, que instruirão o Logstash a gerar 1000 eventos de teste:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Essa configuração também inclui o `stdin` plug-in de entrada que permite que você escreva mais mensagens por conta própria (não se esqueça de usar *Enter* para enviá-las no pipeline).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Configurar o Logstash para enviar dados para o Azure Data Explorer

Cole as configurações a seguir no mesmo arquivo de configuração usado na etapa anterior. Substitua todos os espaços reservados pelos valores relevantes para sua configuração. Para obter mais informações, consulte [Criação de um aplicativo do AAD](/azure/kusto/management/access-control/how-to-provision-aad-app). 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Nome do Parâmetro | Descrição |
| --- | --- |
| **path** | O plug-in do Logstash grava eventos em arquivos temporários antes de enviá-los para o Azure Data Explorer. Esse parâmetro inclui um caminho no qual os arquivos devem ser gravados e uma expressão de tempo para a rotação de arquivos a fim de disparar um upload para o serviço do Azure Data Explorer.|
| **ingest_url** | O ponto de extremidade do Kusto para comunicação relacionada à ingestão.|
| **app_id**, **app_key** e **app_tenant**| Credenciais necessárias para se conectar ao Azure Data Explorer. Certifique-se de usar um aplicativo com privilégios de ingestão. |
| **Database**| Nome do banco de dados para colocar os eventos. |
| **Tabela** | Nome da tabela de destino para colocar os eventos. |
| **Mapeamento** | O mapeamento é usado para mapear uma cadeia de caracteres json de evento recebida no formato correto de linha (define qual propriedade entra em qual coluna). |

## <a name="run-logstash"></a>Executar o Logstash

Agora, estamos prontos para executar o Logstash e testar nossas configurações.

1. No diretório raiz do Logstash, execute o comando a seguir:

    ```sh
    bin/logstash -f test.conf
    ```

    Você deve ver informações impressas na tela e, em seguida, as 1000 mensagens geradas por nosso exemplo de configuração. Neste ponto, também pode inserir mais mensagens manualmente.

1. Depois de alguns minutos, execute a consulta do Data Explorer a seguir para ver as mensagens na tabela que definiu:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Selecione Ctrl+C para sair do Logstash

## <a name="clean-up-resources"></a>Limpar recursos

Execute o comando a seguir no seu banco de dados para limpar a tabela `logs`:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Próximas etapas

* [Gravar consultas](write-queries.md)