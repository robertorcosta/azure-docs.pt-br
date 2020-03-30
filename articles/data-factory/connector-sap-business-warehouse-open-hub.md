---
title: Copiar dados do SAP Business Warehouse via Open Hub
description: Saiba como copiar dados do SAP BW (Business Warehouse) via Open Hub para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: ad7d171cb115729e174090c1c80915abbde5999f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238741"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse via Open Hub com o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados SAP BW (Business Warehouse) via Open Hub. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para obter o suporte geral da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP usando o whitepaper da Fábrica de Dados Do Azure](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este Armazém de Negócios SAP via conector Open Hub é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados do SAP Business Warehouse via Open Hub para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP Business Warehouse Open Hub dá suporte a:

- SAP Business Warehouse **versão 7.01 ou superior (em um recente SAP Support Package Stack lançado após o ano de 2015)**.
- Cópia de dados por meio da tabela de local de destino do Open Hub que pode ser, por baixo, DSO, InfoCube, MultiProvider, DataSource etc.
- À cópia de dados usando a autenticação Básica.
- Conexão ao Servidor de Aplicativos.

## <a name="sap-bw-open-hub-integration"></a>Integração do SAP BW Open Hub 

O [Serviço do SAP BW Open Hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) é uma maneira eficiente de extrair dados do SAP BW. O diagrama a seguir mostra um dos fluxos típicos que os clientes têm em seu sistema SAP, caso em que os dados fluem do SAP ECC -> PSA -> DSO -> Cubo.

O SAP BW OHD (Destino de Open Hub) define o destino ao qual os dados do SAP são retransmitidos. Qualquer objeto suportado pelo SAP Data Transfer Process (DTP) pode ser usado como fontes de dados de hub aberto, por exemplo, DSO, InfoCube, DataSource, etc. O tipo Open Hub Destination - onde os dados retransmitidos são armazenados - pode ser tabelas de banco de dados (locais ou remotos) e arquivos planos. Esse conector do SAP BW Open Hub é compatível com copiar dados da tabela local do OHD no BW. Caso você esteja usando outros tipos, poderá conectar-se diretamente ao banco de dados ou sistema de arquivos usando outros conectores.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Fluxo de extração delta

O ADF SAP BW Open Hub `excludeLastRequest` Connector oferece duas propriedades opcionais: e `baseRequestId` que podem ser usadas para lidar com a carga delta do Open Hub. 

- **exclulastRequestId**: Se excluir os registros da última solicitação. O valor padrão é true. 
- **baseRequestId**: O ID de solicitação de carregamento delta. Depois que ele for definido, somente os dados com requestId maior do que o valor dessa propriedade serão recuperados. 

No geral, a extração de SAP InfoProviders para Azure Data Factory (ADF) consiste em 2 etapas: 

1. **SAP BW Data Transfer Process (DTP)** Esta etapa copia os dados de um SAP BW InfoProvider para uma tabela SAP BW Open Hub 

1. **Cópia de dados do ADF** Nesta etapa, a tabela Open Hub é lida pelo Conector ADF 

![Fluxo de extração delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

Na primeira etapa, um DTP é executado. Cada execução cria um novo ID de solicitação SAP. O ID de solicitação é armazenado na tabela Open Hub e, em seguida, é usado pelo conector ADF para identificar o delta. As duas etapas são executadas de forma assíncrona: o DTP é acionado pelo SAP, e a cópia de dados Do ADF é acionada através do ADF. 

Por padrão, o ADF não está lendo o delta mais recente da tabela Open Hub (a opção "excluir última solicitação" é verdadeira). Com isso, os dados no ADF não estão 100% atualizados com os dados da tabela Open Hub (o último delta está faltando). Em troca, este procedimento garante que nenhuma linha se perca causada pela extração assíncrona. Funciona bem mesmo quando o ADF está lendo a tabela Open Hub enquanto o DTP ainda está escrevendo na mesma tabela. 

Você normalmente armazena o ID de solicitação copiado max na última execução pelo ADF em um armazenamento de dados de preparação (como Azure Blob no diagrama acima). Portanto, o mesmo pedido não é lido uma segunda vez pela ADF na execução subsequente. Enquanto isso, observe que os dados não são excluídos automaticamente da tabela Open Hub.

Para o manuseio delta adequado, não é permitido solicitar IDs de Diferentes DTPs na mesma tabela Open Hub. Portanto, você não deve criar mais de um DTP para cada Destino de Hub Aberto (OHD). Ao precisar da extração Full e Delta do mesmo InfoProvider, você deve criar dois OHDs para o mesmo Provedor de Informações. 

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector do SAP Business Warehouse Open Hub, você precisa:

- Configurar um Integration Runtime auto-hospedado com versão 3.13 ou superior. Consulte [o artigo 'Executtime de integração'.](create-self-hosted-integration-runtime.md)

- Baixar a versão de **64 bits do [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** do site da SAP e instalá-la no computador de IR auto-hospedado. Ao instalar, na janela de etapas de instalação opcionais, verifique se você selecionou a opção **Instalar Assemblies no GAC** conforme mostra a imagem a seguir. 

    ![Instalar o SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O usuário do SAP que está sendo usado no conector do Data Factory BW precisa ter as seguintes permissões: 

    - Autorização para RFC e SAP BW. 
    - Permissões para a Atividade de "Executar" do Objeto de Autorização "S_SDSAUTH".

- Criar tipo de Destino do SAP Open Hub como **Tabela de Banco de Dados** com opção "Chave Técnica" marcada.  Também é recomendável deixar a opção Excluir Dados da Tabela desmarcada, embora não seja necessário. Aproveite o DTP (execute ou integre diretamente na cadeia de processo existente) para descarregar dados do objeto de origem (como um cubo) que você escolheu para a tabela de destino do Open Hub.

## <a name="getting-started"></a>Introdução

> [!TIP]
>
> Para obter um passo a passo de usar o conector SAP BW Open Hub, consulte [Os dados de carga do SAP Business Warehouse (BW) usando o Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir apresentam detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas do conector do SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do SAP BW Open Hub (Business Warehouse):

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida como: **SapOpenHub** | Sim |
| Servidor | Nome do servidor no qual reside a instância do SAP BW. | Sim |
| systemNumber | Número de sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim |
| clientId | ID de Cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| Linguagem | Idioma que o sistema SAP usa. | Não (o valor padrão é **EN**)|
| userName | Nome do usuário que tem acesso ao servidor SAP. | Sim |
| password | Senha do usuário. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados SAP BW Open Hub.

Para copiar dados de e para o SAP BW Open Hub, defina a propriedade type do conjunto de dados como **SapOpenHubTable**. Há suporte para as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **SapOpenHubTable**.  | Sim |
| openHubDestinationName | O nome de Destino do Open Hub do qual copiar dados. | Sim |

Se você `excludeLastRequest` estava `baseRequestId` configurando e no conjunto de dados, ele ainda é suportado como está, enquanto você é sugerido a usar o novo modelo na fonte de atividade daqui para frente.

**Exemplo:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub como origem

Para copiar dados do SAP BW Open Hub, as seguintes propriedades são suportadas na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** da fonte de atividade de cópia deve ser definida como **SapOpenHubSource**. | Sim |
| excludeLastRequest | Se você deseja excluir os registros da última solicitação. | Não (padrão é **verdadeiro)** |
| baseRequestId | A ID da solicitação do carregamento delta. Depois que ele for definido, somente os dados com requestId **maior do que** o valor dessa propriedade serão recuperados.  | Não |

>[!TIP]
>Se a tabela do Open Hub contém apenas os dados gerados por ID de solicitação única, por exemplo, você sempre realiza carga completa e substitui os dados existentes na tabela ou somente executa o DTP uma vez para teste; lembre-se de desmarcar a opção "excludeLastRequest" para copiar os dados.

Para acelerar o carregamento de [`parallelCopies`](copy-activity-performance.md#parallel-copy) dados, você pode definir a atividade de cópia para carregar dados do SAP BW Open Hub em paralelo. Por exemplo, se `parallelCopies` você definir para quatro, a Data Factory executa simultaneamente quatro chamadas RFC, e cada chamada RFC recupera uma parte dos dados da sua tabela SAP BW Open Hub particionada pelo ID de solicitação DTP e iD do pacote. Isso se aplica quando o número de ID de solicitação de DTP exclusivo + iD do pacote é maior do que o valor de `parallelCopies`. Ao copiar dados no armazenamento de dados baseado em arquivos, ele também é recomandado para gravar em uma pasta como vários arquivos (apenas especificar nome da pasta), nesse caso, o desempenho é melhor do que escrever em um único arquivo.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapeamento de tipo de dados para SAP BW Open Hub

Ao copiar dados do SAP BW Open Hub, os seguintes mapeamentos são usados de tipos de dados do SAP BW para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo SAP ABAP | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| C (Cadeia de caracteres) | String |
| I (inteiro) | Int32 |
| F (Flutuante) | Double |
| D (Data) | String |
| T (Hora) | String |
| P (BCD Empacotado, Moeda, Decimal, Qtd) | Decimal |
| N (Numc) | String |
| X (Binário e Bruto) | String |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

**Sintomas:** Se você estiver executando o SAP BW no HANA e observar que apenas o subconjunto de dados é copiado usando a atividade de cópia do ADF (1 milhão de linhas), a causa possível é que você habilite a opção "Execução SAP HANA" em seu DTP, nesse caso, o ADF só pode recuperar o primeiro lote de dados.

**Resolução:** Desabilite a opção "Execução SAP HANA" no DTP, reprocesse os dados e tente executar a atividade de cópia novamente.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
