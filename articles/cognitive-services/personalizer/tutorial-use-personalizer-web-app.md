---
title: Usar aplicativo Web-personalizador
description: Personalize um aplicativo Web C# .NET com um loop personalizado para fornecer o conteúdo correto a um usuário com base em ações (com recursos) e recursos de contexto.
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.author: diberry
ms.openlocfilehash: 9def69a1540e81b99723c16ad34ba522d1737c7f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84713912"
---
# <a name="add-personalizer-to-a-net-web-app"></a>Adicionar personalizador a um aplicativo Web .NET

Personalize um aplicativo Web C# .NET com um loop personalizado para fornecer o conteúdo correto a um usuário com base em ações (com recursos) e recursos de contexto.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Configurar a chave e o ponto de extremidade do personalizador
> * Coletar recursos
> * Chamar as APIs de classificação e recompensa
> * Exibir a ação superior, designada como _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>Selecionar o melhor conteúdo para um aplicativo Web

Um aplicativo Web deve usar o personalizador quando há uma lista de _ações_ (algum tipo de conteúdo) na página da Web que precisa ser personalizada para um único item superior (rewardActionId) a ser exibido. Exemplos de listas de ações incluem artigos de notícias, locais de posicionamento de botão e opções de palavras para nomes de produtos.

Você envia a lista de ações, juntamente com os recursos de contexto, para o loop personalizador. O personalizador seleciona a melhor ação e, em seguida, seu aplicativo Web exibe essa ação.

Neste tutorial, as ações são tipos de alimentos:

* parafuso
* sorvete
* suco
* salada
* pipoca
* café
* sopa

Para ajudar o personalizado a aprender sobre suas ações, envie as duas_ações com recursos_ e _recursos de contexto_ com cada solicitação de API de classificação.

Um **recurso** do modelo é uma informação sobre a ação ou o contexto que pode ser agregado (agrupado) entre membros da sua base de usuários do aplicativo Web. Um recurso _não é_ específico individualmente (como uma ID de usuário) ou altamente específico (como uma hora exata do dia).

### <a name="actions-with-features"></a>Ações com recursos

Cada ação (item de conteúdo) tem recursos para ajudar a distinguir o item de alimentos.

Os recursos não são configurados como parte da configuração de loop no portal do Azure. Em vez disso, eles são enviados como um objeto JSON com cada chamada à API de classificação. Isso permite flexibilidade para as ações e seus recursos para aumentar, alterar e reduzir ao longo do tempo, o que permite que o personalizado acompanhe as tendências.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Recursos de contexto

Recursos de contexto ajudam o personalizador a entender o contexto das ações. O contexto para este aplicativo de exemplo inclui:

* hora do dia-manhã, tarde, noite, noite
* preferência do usuário para gosto-Salty, doce, tive, Camin ou savory
* contexto do navegador-agente do usuário, localização geográfica, referenciador

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Como o aplicativo Web usa o personalizador?

O aplicativo Web usa o personalizador para selecionar a melhor ação na lista de opções de alimentos. Ele faz isso enviando as seguintes informações com cada chamada à API de classificação:
* **ações** com seus recursos, como `taste` e`spiceLevel`
* recursos de **contexto** , como `time` do dia, preferência do usuário `taste` e informações do agente do usuário do navegador e recursos de contexto
* **ações a serem excluídas** , como suco
* **EventID**, que é diferente para cada chamada à API de classificação.

## <a name="personalizer-model-features-in-a-web-app"></a>Recursos do modelo personalizador em um aplicativo Web

O personalizador precisa de recursos para as ações (conteúdo) e o contexto atual (usuário e ambiente). Os recursos são usados para alinhar ações ao contexto atual no modelo. O modelo é uma representação do conhecimento passado do personalizador sobre ações, contexto e seus recursos que permitem que ele tome decisões instruídas.

O modelo, incluindo recursos, é atualizado em uma agenda com base na configuração de **frequência de atualização do modelo** no portal do Azure.

> [!CAUTION]
> Os recursos neste aplicativo destinam-se a ilustrar os recursos e os valores de recursos, mas não necessariamente aos melhores recursos a serem usados em um aplicativo Web.

### <a name="plan-for-features-and-their-values"></a>Planejar recursos e seus valores

Os recursos devem ser selecionados com o mesmo planejamento e design que você aplicaria a qualquer esquema ou modelo em sua arquitetura técnica. Os valores de recurso podem ser definidos com a lógica de negócios ou com sistemas de terceiros. Os valores de recurso não devem ser tão específicos que não se aplicam a um grupo ou uma classe de recursos.

### <a name="generalize-feature-values"></a>Generalizar valores de recurso

#### <a name="generalize-into-categories"></a>Generalizar em categorias

Esse aplicativo usa `time` como um recurso, mas agrupa o tempo em categorias como `morning` , `afternoon` , `evening` e `night` . Esse é um exemplo de como usar as informações de tempo, mas não de uma maneira altamente específica, como `10:05:01 UTC+2` .

#### <a name="generalize-into-parts"></a>Generalizar em partes

Esse aplicativo usa os recursos de solicitação HTTP do navegador. Isso começa com uma cadeia de caracteres muito específica com todos os dados, por exemplo:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

A biblioteca de classes **HttpRequestFeatures** generaliza essa cadeia de caracteres em um objeto **userAgentInfo** com valores individuais. Todos os valores que são muito específicos são definidos como uma cadeia de caracteres vazia. Quando os recursos de contexto para a solicitação são enviados, ele tem o seguinte formato JSON:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Usando o aplicativo Web de exemplo

O aplicativo Web baseado em navegador de exemplo (todo o código é fornecido) precisa dos seguintes aplicativos instalados para executar o aplicativo.

Instale o software a seguir:

* [.Net core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1) -o servidor de back-end de exemplo usa o .NET Core
* [Node.js](https://nodejs.org/) -o cliente/front-end depende deste aplicativo
* [Visual studio 2019](https://visualstudio.microsoft.com/vs/)ou [CLI do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) -use o ambiente de desenvolvedor do Visual Studio 2019 ou o CLI do .NET Core para compilar e executar o aplicativo

### <a name="set-up-the-sample"></a>Configurar o exemplo
1. Clone o repositório de exemplos do personalizador do Azure.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Navegue até _Samples/HttpRequestFeatures_ para abrir a solução, `HttpRequestFeaturesExample.sln` .

    Se solicitado, permita que o Visual Studio atualize o pacote .NET para personalizador.

### <a name="set-up-azure-personalizer-service"></a>Configurar o serviço personalizado do Azure

1. [Crie um recurso personalizado](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) no portal do Azure.

1. Na portal do Azure, localize o `Endpoint` e o `Key1` ou `Key2` (ambos funcionarão) na guia **chaves e pontos de extremidade** . Esses são seus `PersonalizerServiceEndpoint` e seus `PersonalizerApiKey` .
1. Preencha o `PersonalizerServiceEndpoint` no **appsettings.jsem**.
1. Configure o `PersonalizerApiKey` como um [segredo de aplicativo](https://docs.microsoft.com/aspnet/core/security/app-secrets) de uma das seguintes maneiras:

    * Se você estiver usando o CLI do .NET Core, poderá usar o `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` comando.
    * Se você estiver usando o Visual Studio, poderá clicar com o botão direito do mouse no projeto e selecionar a opção de menu **gerenciar segredos do usuário** para configurar as chaves personalizadas. Ao fazer isso, o Visual Studio abrirá um `secrets.json` arquivo no qual você pode adicionar as chaves da seguinte maneira:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Execute o exemplo

Compile e execute o HttpRequestFeaturesExample com um dos seguintes métodos:

* Visual Studio 2019: pressione **F5**
* CLI do .NET Core: `dotnet build` em seguida,`dotnet run`

Por meio de um navegador da Web, você pode enviar uma solicitação de classificação e uma solicitação de recompensa e ver suas respostas, bem como os recursos de solicitação HTTP extraídos do seu ambiente.

> [!div class="mx-imgBorder"]
> ![Compile e execute o projeto HTTPRequestFeaturesExample. Uma janela do navegador é aberta para exibir o aplicativo de página única.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Demonstrar o loop personalizador

1. Selecione o botão **gerar nova solicitação de classificação** para criar um novo objeto JSON para a chamada à API de classificação. Isso cria as ações (com recursos) e os recursos de contexto e exibe os valores para que você possa ver a aparência do JSON.

    Para seu próprio aplicativo futuro, a geração de ações e recursos pode ocorrer no cliente, no servidor, em uma combinação dos dois ou com chamadas para outros serviços.

1. Selecione **Enviar solicitação de classificação** para enviar o objeto JSON para o servidor. O servidor chama a API de classificação do personalizador. O servidor recebe a resposta e retorna a ação de classificação mais alta para o cliente a ser exibido.

1. Defina o valor de recompensa e selecione o botão **Enviar solicitação de recompensa** . Se você não alterar o valor de recompensa, o aplicativo cliente sempre enviará o valor de `1` para o personalizador.

    > [!div class="mx-imgBorder"]
    > ![Compile e execute o projeto HTTPRequestFeaturesExample. Uma janela do navegador é aberta para exibir o aplicativo de página única.](./media/tutorial-web-app/reward-score-api-call.png)

    Para seu próprio aplicativo futuro, a geração da Pontuação de recompensa pode ocorrer após a coleta de informações do comportamento do usuário no cliente, juntamente com a lógica de negócios no servidor.

## <a name="understand-the-sample-web-app"></a>Entender o aplicativo Web de exemplo

O aplicativo Web de exemplo tem um servidor **.NET C#** , que gerencia a coleção de recursos e envia e recebe chamadas http para o ponto de extremidade personalizado.

O aplicativo Web de exemplo usa um **aplicativo cliente de front-end de Knockout** para capturar recursos e processar ações de interface do usuário, como clicar em botões e enviar dados para o servidor .net.

As seções a seguir explicam as partes do servidor e do cliente que um desenvolvedor precisa entender para usar o personalizador.

## <a name="rank-api-client-application-sends-context-to-server"></a>API de classificação: o aplicativo cliente envia o contexto para o servidor

O aplicativo cliente coleta o _agente do usuário_do navegador do usuário.

> [!div class="mx-imgBorder"]
> ![Compile e execute o projeto HTTPRequestFeaturesExample. Uma janela do navegador é aberta para exibir o aplicativo de página única.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>API de classificação: personalização de chamadas de aplicativo de servidor

Este é um aplicativo Web .NET típico com um aplicativo cliente, grande parte do código de placa do preparador é fornecido para você. Qualquer código não específico do personalizador é removido dos trechos de código a seguir para que você possa se concentrar no código específico do personalizador.

### <a name="create-personalizer-client"></a>Criar cliente personalizado

No **Startup.cs**do servidor, o ponto de extremidade e a chave do personalizador são usados para criar o cliente personalizado. O aplicativo cliente não precisa se comunicar com o personalizador neste aplicativo, em vez de depender do servidor para fazer essas chamadas do SDK.

O código de inicialização do .NET do servidor Web é:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Selecione a melhor ação

No **PersonalizerController.cs**do servidor, a API do servidor **GenerateRank** resume a preparação para chamar a API de classificação

* Criar novo `eventId` para a chamada de classificação
* Obter a lista de ações
* Obter a lista de recursos do usuário e criar recursos de contexto
* Opcionalmente, defina as ações excluídas
* API de classificação de chamada, retornar resultados para o cliente

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

O JSON enviado ao personalizador, contendo ambas as ações (com recursos) e os recursos de contexto atuais, é semelhante a:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Retornar rewardActionId personalizado ao cliente

A API de classificação retorna a melhor ação selecionada **rewardActionId** para o servidor.

Exiba a ação retornada em **rewardActionId**.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>Cliente exibe a ação rewardActionId

Neste tutorial, o `rewardActionId` valor é exibido.

Em seu próprio aplicativo futuro, esse pode ser um texto exato, um botão ou uma seção da página da Web realçada. A lista é retornada para qualquer análise posterior de pontuações, não uma ordem do conteúdo. Somente o `rewardActionId` conteúdo deve ser exibido.

## <a name="reward-api-collect-information-for-reward"></a>API de recompensa: coletar informações para recompensa

A [Pontuação de recompensa](concept-rewards.md) deve ser cuidadosamente planejada, assim como os recursos são planejados. A pontuação de recompensa normalmente deve ser um valor de 0 a 1. O valor _pode_ ser calculado parcialmente no aplicativo cliente, com base nos comportamentos do usuário e parcialmente no servidor, com base na lógica e nas metas de negócios.

Se o servidor não chamar a API de recompensa dentro do **tempo de espera de recompensa** configurado no portal do Azure para o recurso personalizador, a **recompensa padrão** (também configurada no portal do Azure) será usada para esse evento.

Neste aplicativo de exemplo, você pode selecionar um valor para ver como a recompensa afeta as seleções.

## <a name="additional-ways-to-learn-from-this-sample"></a>Outras maneiras de aprender com este exemplo

O exemplo usa vários eventos baseados em tempo configurados no portal do Azure para o recurso personalizador. Jogue com esses valores, em seguida, retorne a este aplicativo Web de exemplo para ver como as alterações afetam as chamadas de classificação e recompensa:

* Tempo de espera de recompensa
* Frequência de atualização do modelo

Configurações adicionais para brincar com incluem:
* Recompensa padrão
* Percentual de exploração


## <a name="clean-up-resources"></a>Limpar os recursos

Quando você concluir este tutorial, limpe os seguintes recursos:

* Exclua o diretório do projeto de exemplo.
* Excluir o recurso personalizado – considere um recurso personalizado como dedicado às ações e reutilize o recurso somente se você ainda estiver usando o domínio de assunto pratos as ações.


## <a name="next-steps"></a>Próximas etapas
* [Como funciona o Personalizador](how-personalizer-works.md)
* [Recursos](concepts-features.md): Aprenda conceitos sobre recursos usando com ações e contexto
* [Recompensas](concept-rewards.md): Saiba mais sobre como calcular recompensas