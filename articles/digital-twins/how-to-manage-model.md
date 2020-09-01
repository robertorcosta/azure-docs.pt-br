---
title: Gerenciar modelos personalizados
titleSuffix: Azure Digital Twins
description: Veja como criar, editar e excluir um modelo no Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3a2b3bfa8553e7c350c08fa7e1a7376ca08d9644
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079769"
---
# <a name="manage-azure-digital-twins-models"></a>Gerenciar modelos de gêmeos digitais do Azure

Você pode gerenciar os [modelos](concepts-models.md) que sua instância do gêmeos digital do Azure conhece usando as [**APIs do DIGITALTWINSMODELS**](how-to-use-apis-sdks.md), o SDK do [.net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou a [CLI do Azure digital gêmeos](how-to-use-cli.md). 

As operações de gerenciamento incluem carregamento, validação, recuperação e exclusão de modelos. 

## <a name="create-models"></a>Criar modelos

Os modelos para o gêmeos digital do Azure são escritos em DTDL e salvos como arquivos *. JSON* . Também há uma [extensão DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) disponível para [Visual Studio Code](https://code.visualstudio.com/), que fornece validação de sintaxe e outros recursos para facilitar a gravação de documentos DTDL.

Considere um exemplo em que um hospital deseja representar digitalmente suas salas. Cada sala contém um dispensador de SOAP inteligente para monitoramento de mão washing e sensores para monitorar o tráfego por meio da sala.

A primeira etapa em direção à solução é criar modelos para representar aspectos do hospital. Uma sala de pacientes nesse cenário pode ser descrita da seguinte maneira:

```json
{
  "@id": "dtmi:com:contoso:PatientRoom;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "displayName": "Patient Room",
  "contents": [
    {
      "@type": "Property",
      "name": "visitorCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashPercentage",
      "schema": "double"
    },
    {
      "@type": "Relationship",
      "name": "hasDevices"
    }
  ]
}
```

> [!NOTE]
> Este é um corpo de exemplo para um arquivo. JSON no qual um modelo é definido e salvo, para ser carregado como parte de um projeto de cliente. Por outro lado, a chamada à API REST usa uma matriz de definições de modelo como a acima (que é mapeada para um `IEnumerable<string>` no SDK do .net). Portanto, para usar esse modelo na API REST diretamente, coloque-o entre colchetes.

Esse modelo define um nome e uma ID exclusiva para a sala do paciente e propriedades para representar a contagem de visitantes e o status de mão-desbotado (esses contadores serão atualizados dos sensores de movimento e dos dispensadores do Smart SOAP e serão usados em conjunto para calcular uma propriedade de *porcentagem de handwash* ). O modelo também define uma relação *hasDevices*, que será usada para conectar qualquer [gêmeos digital](concepts-twins-graph.md) com base nesse modelo de *sala* aos dispositivos reais.

Seguindo esse método, você pode ir para definir modelos para as regiões, zonas ou o próprio hospital do hospital.

### <a name="validate-syntax"></a>Validar sintaxe

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="manage-models-with-apis"></a>Gerenciar modelos com APIs

As seções a seguir mostram como concluir diferentes operações de gerenciamento de modelos usando os [SDKs e APIs do gêmeos digital do Azure](how-to-use-apis-sdks.md).

> [!NOTE]
> Os exemplos a seguir não incluem o tratamento de erros para fins de brevidade. No entanto, é altamente recomendável em seus projetos encapsular chamadas de serviço em blocos try/catch.

> [!TIP] 
> Lembre-se de que todos os métodos do SDK vêm em versões síncronas e assíncronas. Para chamadas de paginação, os métodos assíncronos retornam `AsyncPageable<T>` enquanto as versões síncronas retornam `Pageable<T>` .

### <a name="upload-models"></a>Carregar modelos

Depois que os modelos são criados, você pode carregá-los na instância do gêmeos digital do Azure.

> [!TIP]
> É recomendável validar seus modelos offline antes de carregá-los na instância do gêmeos digital do Azure. Você pode usar a [biblioteca do analisador do lado do cliente DTDL](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) e a [amostra do validador DTDL](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) descritas em [*instruções: analisar e validar modelos*](how-to-parse-models.md) para verificar seus modelos antes de carregá-los para o serviço.

Quando estiver pronto para carregar um modelo, você poderá usar o seguinte trecho de código:

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

Observe que o `CreateModels` método aceita vários arquivos em uma única transação. Aqui está um exemplo para ilustrar:

```csharp
var dtdlFiles = Directory.EnumerateFiles(sourceDirectory, "*.json");

List<string> dtdlStrings = new List<string>();
foreach (string fileName in dtdlFiles)
{
    // Read model file into string (not part of SDK)
    StreamReader r = new StreamReader(fileName);
    string dtdl = r.ReadToEnd(); r.Close();
    dtdlStrings.Add(dtdl);
}
client.CreateModels(dtdlStrings);
```

Os arquivos de modelo podem conter mais de um único modelo. Nesse caso, os modelos precisam ser colocados em uma matriz JSON. Por exemplo:

```json
[
  {
    "@id": "dtmi:com:contoso:Planet",
    "@type": "Interface",
    //...
  },
  {
    "@id": "dtmi:com:contoso:Moon",
    "@type": "Interface",
    //...
  }
]
```
 
No carregamento, os arquivos de modelo são validados pelo serviço.

### <a name="retrieve-models"></a>Recuperar modelos

Você pode listar e recuperar modelos armazenados em sua instância do gêmeos digital do Azure. 

Aqui estão as opções para isso:
* Recuperar todos os modelos
* Recuperar um único modelo
* Recuperar um modelo único com dependências
* Recuperar metadados para modelos

Aqui estão algumas chamadas de exemplo:

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
ModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<ModelData> pmd2 = client.GetModels();

// Get a list of metadata and full model definitions
Pageable<ModelData> pmd3 = client.GetModels(null, true);

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<ModelData> pmd4 = client.GetModels(new string[] { modelId }, true);
```

As chamadas à API para recuperar modelos todos os objetos de retorno `ModelData` . `ModelData` contém metadados sobre o modelo armazenado na instância do gêmeos digital do Azure, como nome, DTMI e data de criação do modelo. O `ModelData` objeto também inclui, opcionalmente, o próprio modelo. Dependendo dos parâmetros, você pode, portanto, usar as chamadas de recuperação para recuperar apenas os metadados (o que é útil em cenários em que você deseja exibir uma lista de interfaces do usuário de ferramentas disponíveis, por exemplo) ou todo o modelo.

A `RetrieveModelWithDependencies` chamada retorna não apenas o modelo solicitado, mas também todos os modelos dos quais o modelo solicitado depende.

Os modelos não são necessariamente retornados exatamente no formulário de documento em que foram carregados. O gêmeos digital do Azure só garante que o formulário de retorno será semanticamente equivalente. 

### <a name="update-models"></a>Atualizar modelos

Depois que um modelo é carregado em sua instância, a interface de modelo inteira é imutável. Isso significa que não há "edição" tradicional de modelos.

Em vez disso, se você quiser fazer alterações em um modelo no Azure digital gêmeos, a maneira de fazer isso é carregar uma **versão mais recente** do mesmo modelo. Durante a visualização, o progresso de uma versão de modelo só permitirá que você remova os campos, e não adicione novos (para adicionar novos campos, basta [criar um modelo totalmente novo](#create-models)).

Para criar uma nova versão de um modelo existente, comece com o DTDL do modelo original. Atualize os campos que você deseja alterar.

Em seguida, marque-a como uma versão mais recente do modelo atualizando o `id` campo do modelo. A última seção da ID do modelo, após o `;` , representa o número do modelo. Para indicar que essa é agora uma versão mais atualizada desse modelo, aumente o número no final do `id` valor para qualquer número maior que o número de versão atual.

Por exemplo, se a ID do modelo anterior tiver a seguinte aparência:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

a versão 2 desse modelo pode ter a seguinte aparência:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Em seguida, carregue a nova versão do modelo em sua instância. Ele assumirá o lugar da versão antiga e as novas gêmeos que você criar usando esse modelo usarão a versão atualizada.

### <a name="remove-models"></a>Remover modelos

Os modelos também podem ser removidos do serviço, de uma das duas maneiras:
* **Encerramento** : depois que um modelo é encerrado, você não pode mais usá-lo para criar um novo gêmeos digital. As gêmeos digitais existentes que já usam esse modelo não são afetadas, portanto, você ainda pode atualizá-las com itens como alterações de propriedade e adicionar ou excluir relações.
* **Exclusão** : isso removerá completamente o modelo da solução. Qualquer gêmeos que estivesse usando esse modelo não estará mais associado a nenhum modelo válido, portanto, eles serão tratados como se não tivessem um modelo. Você ainda pode ler essas gêmeos, mas não poderá fazer nenhuma atualização nelas até que elas sejam reatribuídas a um modelo diferente.

Esses são recursos separados e não afetam uns aos outros, embora possam ser usados juntos para remover um modelo gradualmente. 

#### <a name="decommissioning"></a>Encerramento

Este é o código para desativar um modelo:

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

O status de descomissionamento de um modelo é incluído nos `ModelData` registros retornados pelas APIs de recuperação de modelo.

#### <a name="deletion"></a>Exclusão

Você pode excluir todos os modelos em sua instância de uma só vez ou pode fazê-lo em uma base individual.

Para obter um exemplo de como excluir todos os modelos, baixe o aplicativo de exemplo usado no [*tutorial: explorar os conceitos básicos com um aplicativo cliente de exemplo*](tutorial-command-line-app.md). O arquivo *CommandLoop.cs* faz isso em uma `CommandDeleteAllModels` função.

O restante desta seção divide a exclusão do modelo em detalhes mais detalhadamente e mostra como fazê-lo para um modelo individual.

##### <a name="before-deletion-deletion-requirements"></a>Antes da exclusão: requisitos de exclusão

Em geral, os modelos podem ser excluídos a qualquer momento.

A exceção são modelos dos quais outros modelos dependem, com uma `extends` relação ou como um componente. Por exemplo, se um modelo de *conferenceroom* estende um modelo de *sala* e tem um modelo de *ACUnit* como componente, você não pode excluir *sala* ou *ACUnit* até que *conferenceroom* remova essas respectivas referências. 

Você pode fazer isso atualizando o modelo dependente para remover as dependências ou excluir completamente o modelo dependente.

##### <a name="during-deletion-deletion-process"></a>Durante a exclusão: processo de exclusão

Mesmo que um modelo atenda aos requisitos para excluí-lo imediatamente, você pode querer percorrer algumas etapas primeiro para evitar consequências indesejadas para o gêmeos deixados para trás. Aqui estão algumas etapas que podem ajudá-lo a gerenciar o processo:
1. Primeiro, descomissionar o modelo
2. Aguarde alguns minutos para verificar se o serviço processou todas as solicitações de criação de alto minutos enviadas antes da descomissionação
3. Consultar gêmeos por modelo para ver todos os gêmeos que estão usando o modelo agora descomissionado
4. Exclua o gêmeos se você não precisar mais deles ou corrija-os para um novo modelo, se necessário. Você também pode optar por deixá-los sozinhos; nesse caso, eles se tornarão gêmeos sem modelos depois que o modelo for excluído. Consulte a próxima seção para as implicações desse Estado.
5. Aguarde alguns minutos para certificar-se de que as alterações tenham percolated por meio de
6. Excluir o modelo 

Para excluir um modelo, use esta chamada:
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>Após a exclusão: gêmeos sem modelos

Depois que um modelo é excluído, todas as gêmeos digitais que estavam usando o modelo agora são consideradas sem um modelo. Observe que não há nenhuma consulta que possa fornecer a você uma lista de todos os gêmeos nesse estado — embora você ainda *possa* consultar o gêmeos pelo modelo excluído para saber quais gêmeos são afetados.

Aqui está uma visão geral do que você pode e não pode fazer com gêmeos que não têm um modelo.

Coisas que você **pode** fazer:
* Consultar o entrelaçamento
* Ler propriedades
* Ler relações de saída
* Adicionar e excluir relações de entrada (como em, outros gêmeos ainda podem formar relações *com* este "or")
  - O `target` na definição de relação ainda pode refletir o DTMI do modelo excluído. Uma relação sem um destino definido também pode funcionar aqui.
* Excluir relações
* Excluir o

Coisas que você **não pode** fazer:
* Editar relações *de* saída (como em, relações desta gêmeos para outras)
* Editar propriedades

##### <a name="after-deletion-re-uploading-a-model"></a>Após a exclusão: recarregar um modelo

Depois que um modelo tiver sido excluído, você poderá decidir posteriormente carregar um novo modelo com a mesma ID que aquele que você excluiu. Aqui está o que acontece nesse caso.
* Da perspectiva do repositório de soluções, isso é o mesmo que carregar um modelo completamente novo. O serviço não se lembra de que o antigo já foi carregado.   
* Se houver qualquer gêmeos restante no grafo que referencia o modelo excluído, eles não serão mais órfãos; Essa ID de modelo é válida novamente com a nova definição. No entanto, se a nova definição para o modelo for diferente da definição de modelo que foi excluída, essas gêmeos podem ter propriedades e relações que correspondam à definição excluída e não são válidas com a nova.

O Azure digital gêmeos não impede esse Estado, portanto, tenha cuidado para aplicar o patch gêmeos adequadamente para garantir que eles permaneçam válidos por meio da opção de definição de modelo.

## <a name="manage-models-with-cli"></a>Gerenciar modelos com a CLI

Os modelos também podem ser gerenciados usando a CLI do Azure digital gêmeos. Os comandos podem ser encontrados em [*How-to: Use the Azure digital gêmeos CLI*](how-to-use-cli.md).

## <a name="next-steps"></a>Próximas etapas

Veja como criar e gerenciar gêmeos digitais com base em seus modelos:
* [*Como: gerenciar gêmeos digitais*](how-to-manage-twin.md)