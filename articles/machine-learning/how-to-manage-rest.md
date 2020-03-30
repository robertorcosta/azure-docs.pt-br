---
title: Use rest para gerenciar recursos ML
titleSuffix: Azure Machine Learning
description: Como usar APIs REST para criar, executar e excluir recursos do Azure ML
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580620"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Crie, execute e exclua os recursos do Azure ML usando rest

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Existem várias maneiras de gerenciar seus recursos do Azure ML. Você pode usar o [portal](https://portal.azure.com/), [interface de linha de comando](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)ou Python [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Ou, você pode escolher a API REST. A API REST usa verbos HTTP de forma padrão para criar, recuperar, atualizar e excluir recursos. A API REST funciona com qualquer idioma ou ferramenta que possa fazer solicitações HTTP. A estrutura simples do REST muitas vezes faz dele uma boa escolha em ambientes de scripting e para automação de MLOps. 

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Recupere um token de autorização
> * Crie uma solicitação REST devidamente formatada usando a autenticação principal do serviço
> * Use solicitações GET para recuperar informações sobre os recursos hierárquicos do Azure ML
> * Use solicitações PUT e POST para criar e modificar recursos
> * Use solicitações DELETE para limpar recursos 
> * Use autorização baseada em chaves para pontuar modelos implantados

## <a name="prerequisites"></a>Pré-requisitos

- Uma **assinatura do Azure** para a qual você tem direitos administrativos. Se você não tem uma assinatura dessas, experimente a [assinatura pessoal gratuita ou paga](https://aka.ms/AMLFree)
- Um [espaço de trabalho de aprendizado de máquina do Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- As solicitações administrativas REST usam autenticação principal do serviço. Siga as etapas em [Configurar autenticação para recursos e fluxos de trabalho do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) para criar um principal de serviço em seu espaço de trabalho
- O **utilitário curl.** O programa **curl** está disponível no [Subsistema Windows para Linux](https://aka.ms/wslinstall/) ou qualquer distribuição UNIX. No PowerShell, **o cacho** é um alias para **Invoke-WebRequest** e `curl -d "key=val" -X POST uri` torna-se `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Recupere um token de autenticação principal do serviço

As solicitações de REST administrativo são autenticadas com um fluxo implícito OAuth2. Esse fluxo de autenticação usa um token fornecido pelo diretor de serviço satisfatorial da sua assinatura. Para recuperar este token, você precisará:

- Seu ID de inquilino (identificando a organização à qual sua assinatura pertence)
- Seu ID do cliente (que será associado ao token criado)
- Seu cliente secreto (que você deve proteger)

Você deve ter esses valores desde a resposta à criação do seu principal de serviço, conforme discutido na [autenticação de Configuração para recursos e fluxos de trabalho do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication). Se você estiver usando a assinatura da sua empresa, você pode não ter permissão para criar um diretor de serviço. Nesse caso, você deve usar uma [assinatura pessoal gratuita ou paga.](https://aka.ms/AMLFree)

Para recuperar um token:

1. Abrir uma janela de terminal
1. Digite o seguinte código na linha de comando
1. Substitua seus `{your-tenant-id}`próprios valores por , `{your-client-id}`e `{your-client-secret}`. Ao longo deste artigo, strings cercadas por colchetes cacheados são variáveis que você terá que substituir com seus próprios valores apropriados.
1. Executar o comando

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

A resposta deve fornecer um token de acesso bom por uma hora:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Anote o token, pois você o usará para autenticar todas as solicitações administrativas subseqüentes. Você fará isso definindo um cabeçalho de autorização em todas as solicitações:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Observe que o valor começa com a string "Bearer" incluindo um único espaço antes de adicionar o token.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Obtenha uma lista de grupos de recursos associados à sua assinatura

Para recuperar a lista de grupos de recursos associados à sua assinatura, execute:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Através do Azure, muitas APIs REST são publicadas. Cada provedor de serviços atualiza sua API em sua própria cadência, mas o faz sem quebrar os programas existentes. O provedor de `api-version` serviços usa o argumento para garantir a compatibilidade. O `api-version` argumento varia de serviço para serviço. Para o Serviço de Aprendizagem de Máquina, `2019-11-01`por exemplo, a versão atual da API é . Para contas de armazenamento, é. `2019-06-01` Para cofres chave, é `2019-09-01`. Todas as chamadas `api-version` REST devem definir o argumento para o valor esperado. Você pode confiar na sintaxe e semântica da versão especificada, mesmo que a API continue a evoluir. Se você enviar uma solicitação `api-version` a um provedor sem o argumento, a resposta conterá uma lista de valores suportados por humanos. 

A chamada acima resultará em uma resposta JSON compactada do formulário: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Aprofunde-se em espaços de trabalho e seus recursos

Para recuperar o conjunto de espaços de trabalho em `{your-subscription-id}`um `{your-resource-group}`grupo `{your-access-token}`de recursos, execute o seguinte, substituindo e : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Novamente você receberá uma lista JSON, desta vez contendo uma lista, cada item que detalha um espaço de trabalho:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Para trabalhar com recursos dentro de um espaço de trabalho, você mudará do servidor **de management.azure.com** geral para um servidor de API REST específico para a localização do espaço de trabalho. Observe o valor `discoveryUrl` da chave na resposta JSON acima. Se você receber essa URL, você receberá uma resposta algo como:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

O valor `api` da resposta é a URL do servidor que você usará para solicitações adicionais. Para listar experimentos, por exemplo, envie o seguinte comando. Substitua pelo `regional-api-server` valor `api` da resposta `centralus.api.azureml.ms`(por exemplo, ). Substitua `your-subscription-id` `your-resource-group`também, `your-workspace-name` `your-access-token` e como de costume:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Da mesma forma, para recuperar modelos registrados em seu espaço de trabalho, envie:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Observe que para listar experimentos `history/v1.0` o caminho começa com `modelmanagement/v1.0`enquanto lista modelos, o caminho começa com . A API REST é dividida em vários grupos operacionais, cada um com um caminho distinto. Os docs de referência da API nos links abaixo listam as operações, parâmetros e códigos de resposta para as várias operações.

|Área|Caminho|Referência|
|-|-|-|
|Artefatos|artefato/v2.0/|[Referência de API REST](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Armazenamentos de dados|datastore/v1.0/|[Referência de API REST](https://docs.microsoft.com/rest/api/azureml/datastores)|
|Ajuste de hiperparâmetro|hiperdrive/v1.0/|[Referência de API REST](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modelos|gerenciamento de modelos/v1.0/|[Referência de API REST](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Histórico da execução|execução/v1.0/ e histórico/v1.0/|[Referência de API REST](https://docs.microsoft.com/rest/api/azureml/runs)|

Você pode explorar a API REST usando o padrão geral de:

|Componente DE URL|Exemplo|
|-|-|
| https://| |
| regional-api-servidor/ | centralus.api.azureml.ms/ |
| caminho de operações/ | história/v1.0/ |
| assinaturas/{sua assinatura-id}/ | assinaturas/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{your-resource-group}/ | resourceGroups/MyResourceGroup/ |
| provedores/provedor de operação/ | provedores/Microsoft.MachineLearningServices/ |
| provedor-recurso-caminho/ | espaços de trabalho/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| ponto final de operações/ | artefatos/metadados/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Criar e modificar recursos usando solicitações PUT e POST

Além da recuperação de recursos com o verbo GET, a API REST apoia a criação de todos os recursos necessários para treinar, implantar e monitorar soluções ML. 

Treinar e executar modelos ML requerem recursos computacionais. Você pode listar os recursos de computação de um espaço de trabalho com: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Para criar ou substituir um recurso de computação nomeado, você usará uma solicitação PUT. No seguinte, além das substituições agora `your-subscription-id`familiares `your-workspace-name`de `your-access-token`, `your-compute-name` `your-resource-group`e , `vmSize`substituto `vmPriority` `scaleSettings`, `adminUserName`e `adminUserPassword`valores para `location`, , , , , e . Conforme especificado na referência em [Machine Learning Compute - Create or Update SDK Reference](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), o seguinte comando cria um Standard_D1 dedicado de nó único (um recurso básico de computação de CPU) que será reduzido após 30 minutos:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> Nos terminais do Windows, você pode ter que escapar dos símbolos de dupla cotação ao enviar dados JSON. Ou seja, texto `"location"` `\"location\"`como se torna. 

Uma solicitação bem-sucedida obterá uma `201 Created` resposta, mas note que essa resposta simplesmente significa que o processo de provisionamento já começou. Você precisará fazer uma enquete (ou usar o portal) para confirmar sua conclusão bem sucedida.

### <a name="create-an-experimental-run"></a>Criar uma corrida experimental

Para iniciar uma execução dentro de um experimento, você precisa de uma pasta zip contendo seu script de treinamento e arquivos relacionados, e uma definição de execução de arquivo JSON. A pasta zip deve ter o arquivo de entrada Python em seu diretório raiz. Como exemplo, feche um programa Python trivial, como o seguinte em uma pasta chamada **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Salve este próximo trecho como **definition.json**. Confirme que o valor "Script" corresponde ao nome do arquivo Python que você acabou de fechar. Confirme o valor "Destino" corresponde ao nome de um recurso de computação disponível. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Poste esses arquivos no `multipart/form-data` servidor usando conteúdo:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Uma solicitação POST bem-sucedida gerará um `200 OK` status, com um órgão de resposta contendo o identificador da execução criada:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Você pode monitorar uma corrida usando o padrão REST-ful que agora deve ser familiar:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Excluir recursos que você não precisa mais

Alguns, mas não todos, os recursos suportam o verbo DELETE. Verifique a referência da [API](https://docs.microsoft.com/rest/api/azureml/) antes de se comprometer com a API REST para casos de uso de exclusão. Para excluir um modelo, por exemplo, você pode usar:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Use REST para marcar um modelo implantado

Embora seja possível implantar um modelo para que ele se autentique com um diretor de serviço, a maioria das implantações voltadas para o cliente usam autenticação baseada em chaves. Você pode encontrar a chave apropriada na página de sua implantação na guia **Endpoints** do Studio. O mesmo local mostrará o URI de pontuação do seu ponto final. As entradas do seu modelo devem ser modeladas como uma matriz JSON chamada `data`:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Crie um espaço de trabalho usando REST 

Cada espaço de trabalho do Azure ML tem uma dependência de quatro outros recursos do Azure: um registro de contêiner com administração ativada, um cofre chave, um recurso application Insights e uma conta de armazenamento. Você não pode criar um espaço de trabalho até que esses recursos existam. Consulte a referência da API REST para obter os detalhes da criação de cada recurso.

Para criar um espaço de trabalho, coloque `management.azure.com`uma chamada semelhante à seguinte para . Embora esta chamada exija que você defina um grande número de variáveis, é estruturalmente idêntica a outras chamadas que este artigo discutiu. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Você deve `202 Accepted` receber uma resposta e, nos `Location` cabeçalhos retornados, um URI. Você pode obter este URI para obter informações sobre a implantação, incluindo informações úteis de depuração se houver um problema com um de seus recursos dependentes (por exemplo, se você esqueceu de habilitar o acesso de admin no seu registro de contêiner). 

## <a name="troubleshooting"></a>Solução de problemas

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Movendo o espaço de trabalho

> [!WARNING]
> A mudança do espaço de trabalho do Azure Machine Learning para uma assinatura diferente, ou a mudança da assinatura própria para um novo inquilino, não é suportada. Fazer isso pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Excluindo o Registro de Contêineres do Azure

O espaço de trabalho Azure Machine Learning usa o Azure Container Registry (ACR) para algumas operações. Ele criará automaticamente uma instância ACR quando precisar de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Próximas etapas

- Explore a referência completa da [API AzureML REST](https://docs.microsoft.com/rest/api/azureml/).
- Aprenda a usar o Studio & Designer para [prever o preço do automóvel com o designer (preview)](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Explore [o Azure Machine Learning com notebooks Jupyter](https://docs.microsoft.com/azure//machine-learning/samples-notebooks).
