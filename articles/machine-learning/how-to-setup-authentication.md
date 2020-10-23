---
title: Configurar a autenticação
titleSuffix: Azure Machine Learning
description: Saiba como configurar a autenticação para diferentes recursos e fluxos de trabalho no Azure Machine Learning. Há várias maneiras de configurar e usar a autenticação no serviço, desde a simples autenticação baseada na interface do usuário para fins de desenvolvimento ou teste, até a autenticação completa da entidade de serviço do Azure Active Directory.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js
ms.openlocfilehash: a1d89def944529235a0141d7e700049f15d1d0a7
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424978"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurar a autenticação para recursos e fluxos de trabalho do Azure Machine Learning


Saiba como autenticar em seu espaço de trabalho do Azure Machine Learning e modelos implantados como serviços Web.

Em geral, há dois tipos de autenticação que você pode usar com Azure Machine Learning:

* __Interativo__: você usa sua conta no Azure Active Directory para autenticar-se diretamente ou para obter um token que é usado para autenticação. A autenticação interativa é usada durante a experimentação e o desenvolvimento iterativo. Ou onde você deseja controlar o acesso a recursos (como um serviço Web) por usuário.
* __Entidade de serviço__: Crie uma conta de entidade de serviço no Azure Active Directory e use-a para autenticar ou obter um token. Uma entidade de serviço é usada quando você precisa de um processo automatizado para autenticar o serviço sem a necessidade de interação do usuário. Por exemplo, um script de implantação e integração contínua que treina e testa um modelo toda vez que o código de treinamento é alterado. Você também pode usar uma entidade de serviço para recuperar um token para autenticar em um serviço Web, se não quiser exigir que o usuário final do serviço seja autenticado. Ou onde a autenticação do usuário final não é executada diretamente usando Azure Active Directory.

Independentemente do tipo de autenticação usado, o controle de acesso baseado em função do Azure (RBAC do Azure) é usado para o escopo do nível de acesso permitido aos recursos. Por exemplo, uma conta que é usada para obter o token de acesso para um modelo implantado precisa apenas de acesso de leitura ao espaço de trabalho. Para obter mais informações sobre o RBAC do Azure, consulte [gerenciar o acesso ao Azure Machine Learning espaço de trabalho](how-to-assign-roles.md).

## <a name="prerequisites"></a>Pré-requisitos

* Criar um [Workspace do Azure Machine Learning](how-to-manage-workspace.md).
* [Configure seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o SDK do Azure Machine Learning ou use uma [VM do Notebook do Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) com o SDK já instalado.

## <a name="interactive-authentication"></a>Autenticação interativa

> [!IMPORTANT]
> A autenticação interativa usa seu navegador e requer cookies (incluindo cookies de terceiros). Se você tiver desabilitado cookies, poderá receber um erro como "não foi possível conectá-lo". Esse erro também pode ocorrer se você tiver habilitado a [autenticação multifator do Azure](/azure/active-directory/authentication/concept-mfa-howitworks).

A maioria dos exemplos na documentação e nos exemplos usa a autenticação interativa. Por exemplo, ao usar o SDK, há duas chamadas de função que irão solicitar automaticamente um fluxo de autenticação baseado em interface do usuário:

* Chamar a função `from_config()` emitirá o prompt.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    A função `from_config()` procura um arquivo JSON com as informações de conexão de seu workspace.

* O uso do `Workspace` construtor para fornecer a assinatura, o grupo de recursos e as informações do espaço de trabalho também solicitará a autenticação interativa.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Se você tiver acesso a vários locatários, talvez seja necessário importar a classe e definir explicitamente qual é o locatário de destino. Chamar o construtor para `InteractiveLoginAuthentication` também solicitará que você faça logon de maneira semelhante às chamadas acima.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

## <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para usar a autenticação do SP (entidade de serviço), você deve primeiro criar o SP e conceder a ele acesso ao seu espaço de trabalho. Como mencionado anteriormente, o controle de acesso baseado em função do Azure (RBAC do Azure) é usado para controlar o acesso, portanto, você também deve decidir qual acesso conceder ao SP.

> [!IMPORTANT]
> Ao usar uma entidade de serviço, conceda a ela o __acesso mínimo necessário para a tarefa para a__ qual ela é usada. Por exemplo, você não concederá a um proprietário de entidade de serviço ou acesso de colaborador se tudo o que for usado para o estiver lendo o token de acesso para uma implantação da Web.
>
> O motivo para conceder o acesso mínimo é que uma entidade de serviço usa uma senha para autenticar e a senha pode ser armazenada como parte de um script de automação. Se a senha for vazada, o acesso mínimo necessário para uma tarefa específica minimizará o uso mal-intencionado do SP.

A maneira mais fácil de criar um SP e conceder acesso ao seu espaço de trabalho é usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Para criar uma entidade de serviço e conceder acesso ao seu espaço de trabalho, use as seguintes etapas:

> [!NOTE]
> Você deve ser um administrador na assinatura para executar todas essas etapas.

1. Autentique-se na assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

    Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, você precisará abrir um navegador e seguir as instruções na linha de comando. As instruções envolvem a navegação para [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e a inserção de um código de autorização.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    Para outros métodos de autenticação, confira [Entrar com a CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

1. Instale a extensão de Azure Machine Learning:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Crie a entidade de serviço. No exemplo a seguir, um SP chamado **ml-auth** é criado:

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    A saída será um JSON semelhante ao seguinte. Anote os campos `clientId`, `clientSecret` e `tenantId`, pois você precisará deles para outras etapas neste artigo.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Recupere os detalhes da entidade de serviço usando o `clientId` valor retornado na etapa anterior:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    O JSON a seguir é um exemplo simplificado da saída do comando. Anote o campo `objectId`, pois você precisará do valor dele para a próxima etapa.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Permitir que o SP acesse seu espaço de trabalho do Azure Machine Learning. Você precisará do nome do workspace e do nome do grupo de recursos dele para os parâmetros `-w` e `-g`, respectivamente. Para o parâmetro `--user`, use o valor `objectId` da etapa anterior. O `--role` parâmetro permite que você defina a função de acesso para a entidade de serviço. No exemplo a seguir, o SP é atribuído à função de **proprietário** . 

    > [!IMPORTANT]
    > O acesso do proprietário permite que a entidade de serviço faça praticamente qualquer operação em seu espaço de trabalho. Ele é usado neste documento para demonstrar como conceder acesso; em um ambiente de produção, a Microsoft recomenda conceder a entidade de serviço o acesso mínimo necessário para executar a função para a qual você pretendia. Para obter mais informações, consulte [gerenciar o acesso ao Azure Machine Learning espaço de trabalho](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Essa chamada não produz nenhuma saída em caso de êxito.

### <a name="use-a-service-principal-from-the-sdk"></a>Usar uma entidade de serviço do SDK

Para se autenticar no seu espaço de trabalho do SDK, usando a entidade de serviço, use o `ServicePrincipalAuthentication` Construtor de classe. Use os valores que você obteve ao criar o provedor de serviços como os parâmetros. O parâmetro `tenant_id` é mapeado para `tenantId` acima, `service_principal_id` é mapeado para `clientId` e `service_principal_password` é mapeado para `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Agora, a variável `sp` contém um objeto de autenticação que você usa diretamente no SDK. Em geral, é uma boa ideia armazenar as IDs/segredos usados acima em variáveis de ambiente, conforme mostrado no código a seguir. O armazenamento em variáveis de ambiente impede que as informações sejam verificadas acidentalmente em um repositório GitHub.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Para fluxos de trabalho automatizados que são executados no Python e usam principalmente o SDK, você pode usar esse objeto no estado em que se encontra na maioria dos casos para autenticação. O código a seguir é autenticado em seu espaço de trabalho usando o objeto de autenticação que você criou.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Usar uma entidade de serviço do CLI do Azure

Você pode usar uma entidade de serviço para CLI do Azure comandos. Para obter mais informações, consulte [entrar usando uma entidade de serviço](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest&preserve-view=true#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Usar uma entidade de serviço com a API REST (versão prévia)

A entidade de serviço também pode ser usada para autenticar para a [API REST](https://docs.microsoft.com/rest/api/azureml/) do Azure Machine Learning (versão prévia). Use o [fluxo de concessão de credenciais de cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) do Azure Active Directory, que permite fazer chamadas de serviço a serviço para autenticação sem periféricos em fluxos de trabalho automatizados. Os exemplos são implementados com a [Biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) no Python e no Node.js, mas você pode usar qualquer biblioteca de software livre com suporte para OpenID Connect 1.0.

> [!NOTE]
> A MSAL.js é uma biblioteca mais recente do que a ADAL, mas não é possível fazer a autenticação de serviço a serviço usando credenciais do cliente com a MSAL.js, pois ela é basicamente uma biblioteca do lado do cliente destinada à autenticação interativa/de interface do usuário vinculada a um usuário específico. É recomendável usar a ADAL, conforme mostrado abaixo, para criar fluxos de trabalho automatizados com a API REST.

#### <a name="nodejs"></a>Node.js

Use as etapas a seguir para gerar um token de autenticação usando o Node.js. Em seu ambiente, execute `npm install adal-node`. Em seguida, use `tenantId`, `clientId` e `clientSecret` da entidade de serviço criada nas etapas acima como valores para as variáveis correspondentes no script a seguir.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

A variável `tokenResponse` é um objeto que inclui o token e os metadados associados, como o tempo de expiração. Os tokens são válidos por uma hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. O trecho a seguir é uma resposta de exemplo.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Use a propriedade `accessToken` para buscar o token de autenticação. Consulte a [Documentação da API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) para obter exemplos de como usar o token para fazer chamadas à API.

#### <a name="python"></a>Python

Use as etapas a seguir para gerar um token de autenticação usando o Python. Em seu ambiente, execute `pip install adal`. Em seguida, use `tenantId`, `clientId` e `clientSecret` da entidade de serviço criada nas etapas acima como valores para as variáveis adequadas no script a seguir.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

A variável `token_response` é um dicionário que inclui o token e os metadados associados, como o tempo de expiração. Os tokens são válidos por uma hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. O trecho a seguir é uma resposta de exemplo.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Use `token_response["accessToken"]` para buscar o token de autenticação. Consulte a [Documentação da API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) para obter exemplos de como usar o token para fazer chamadas à API.

## <a name="web-service-authentication"></a>Autenticação do serviço Web

As implantações de modelo criadas pelo Azure Machine Learning fornecem dois métodos de autenticação:

* **baseado em chave**: uma chave estática é usada para autenticar o serviço Web.
* **baseado em token**: um token temporário deve ser obtido no espaço de trabalho e usado para autenticar o serviço Web. Esse token expira após um período de tempo e deve ser atualizado para continuar trabalhando com o serviço Web.

    > [!NOTE]
    > A autenticação baseada em token só está disponível durante a implantação no serviço kubernetes do Azure.

### <a name="key-based-web-service-authentication"></a>Autenticação de serviço Web baseada em chave

Os serviços Web implantados no AKS (serviço de kubernetes do Azure) têm a autenticação baseada em chave *habilitada* por padrão. Os serviços implantados ACI (instâncias de contêiner do Azure) têm a autenticação baseada em chave *desabilitada* por padrão, mas você pode habilitá-la definindo `auth_enabled=True` ao criar o serviço Web do ACI. O código a seguir é um exemplo de criação de uma configuração de implantação ACI com a autenticação baseada em chave habilitada.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Em seguida, você pode usar a configuração personalizada das ACI na implantação usando a classe `Model`.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Para buscar chaves de autenticação, use `aci_service.get_keys()`. Para regenerar uma chave, use a função `regen_key()` e passe como **Primária** ou **Secundária**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Para obter mais informações sobre como autenticar para um modelo implantado, consulte [criar um cliente para um modelo implantado como um serviço Web](how-to-consume-web-service.md).

### <a name="token-based-web-service-authentication"></a>Autenticação de serviço Web baseada em token

Quando você habilita a autenticação de token para um serviço Web, os usuários precisam apresentar um Token Web JSON do Azure Machine Learning para o serviço Web para acessá-lo. O token expira após um período de tempo determinado e precisa ser atualizado para continuar fazendo chamadas.

* A autenticação de token é **desabilitada por padrão** quando é feita uma implantação no Serviço de Kubernetes do Azure.
* **Não há suporte** para a autenticação de token durante a implantação nas Instâncias de Contêiner do Azure.
* A autenticação de token **não pode ser usada ao mesmo tempo que a autenticação baseada em chave**.

Para controlar a autenticação de tokens, use o `token_auth_enabled` parâmetro ao criar ou atualizar uma implantação:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Se a autenticação de token estiver habilitada, você poderá usar o método `get_token` para recuperar um JWT (Token Web JSON) e a hora da expiração do token:

> [!TIP]
> Se você usar uma entidade de serviço para obter o token e quiser que ele tenha o mínimo de acesso necessário para recuperar um token, atribua-o à função **leitor** do espaço de trabalho.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Será necessário solicitar um novo token depois do período de `refresh_by` do token. Se você precisar atualizar os tokens fora do SDK do Python, uma opção é usar a API REST com a autenticação de entidade de serviço para fazer a chamada `service.get_token()` periodicamente, conforme discutido anteriormente.
>
> É altamente recomendável criar seu workspace do Azure Machine Learning na mesma região que o cluster do Serviço de Kubernetes do Azure.
>
> Para autenticar com um token, o serviço Web fará uma chamada para a região em que o workspace do Azure Machine Learning foi criado. Se a região do workspace não estiver disponível, não será possível buscar um token para o serviço Web, mesmo que o cluster esteja em uma região diferente do espaço de trabalho. O resultado é que a autenticação do Azure AD permanece indisponível até que a região do workspace volte a ficar disponível.
>
> Além disso, quanto maior a distância entre a região do cluster e a região do workspace, mais tempo será necessário para buscar o token.

## <a name="next-steps"></a>Próximas etapas

* [Como usar segredos no treinamento](how-to-use-secrets-in-runs.md).
* [Treinar e implantar um modelo de classificação de imagem](tutorial-train-models-with-aml.md).
* [Consumir um modelo de Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md).
