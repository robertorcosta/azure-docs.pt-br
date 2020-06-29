---
title: Configurar a autenticação
titleSuffix: Azure Machine Learning
description: Saiba como configurar a autenticação para diferentes recursos e fluxos de trabalho no Azure Machine Learning. Há várias maneiras de configurar e usar a autenticação no serviço, desde a simples autenticação baseada na interface do usuário para fins de desenvolvimento ou teste, até a autenticação completa da entidade de serviço do Azure Active Directory.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: 6b2cfa85ea412a5ef8bda47a7ff6e99970ba6b0e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611833"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurar a autenticação para recursos e fluxos de trabalho do Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a configurar a autenticação para diferentes recursos e fluxos de trabalho no Azure Machine Learning. Há várias maneiras de se autenticar no serviço, desde a simples autenticação baseada na interface do usuário para fins de desenvolvimento ou teste, até a autenticação completa da entidade de serviço do Azure Active Directory. Este artigo também explica as diferenças de como a autenticação do serviço Web funciona, além de como autenticar na API REST do Azure Machine Learning.

Este guia de instruções mostra como realizar as seguintes tarefas:

* Usar a autenticação interativa da interface do usuário para teste/desenvolvimento
* Configurar a autenticação de entidade de serviço
* Autenticar em seu workspace
* Obter tokens de portador do OAuth 2.0 para a API REST do Azure Machine Learning
* Entender a autenticação do serviço Web

Confira o [artigo de conceito](concept-enterprise-security.md) para ter uma visão geral da segurança e da autenticação no Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um [Workspace do Azure Machine Learning](how-to-manage-workspace.md).
* [Configure seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o SDK do Azure Machine Learning ou use uma [VM do Notebook do Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) com o SDK já instalado.

## <a name="interactive-authentication"></a>Autenticação interativa

A maioria dos exemplos na documentação desse serviço usa a autenticação interativa em notebooks Jupyter como um método simples de teste e demonstração. Essa é uma forma leve de testar o que você está criando. Há duas chamadas de função que solicitarão automaticamente um fluxo de autenticação baseado na interface do usuário.

Chamar a função `from_config()` emitirá o prompt.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

A função `from_config()` procura um arquivo JSON com as informações de conexão de seu workspace. Você também pode especificar os detalhes da conexão explicitamente usando o construtor `Workspace`, que também solicitará a autenticação interativa. As duas chamadas são equivalentes.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Se você tiver acesso a vários locatários, talvez seja necessário importar a classe e definir explicitamente qual é o locatário de destino. Chamar o construtor para `InteractiveLoginAuthentication` também solicitará que você faça logon de maneira semelhante às chamadas acima.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Embora seja útil para teste e aprendizado, a autenticação interativa não ajuda você a criar fluxos de trabalho automatizados nem sem periféricos. Configurar a autenticação da entidade de serviço é a melhor abordagem para processos automatizados que usam o SDK.

## <a name="set-up-service-principal-authentication"></a>Configurar a autenticação de entidade de serviço

Esse processo é necessário para habilitar a autenticação dissociada de um logon de usuário específico, que permite a autenticação no SDK do Python do Azure Machine Learning em fluxos de trabalho automatizados. A autenticação de entidade de serviço também permite que você [autentique na API REST](#azure-machine-learning-rest-api-auth).

Para configurar a autenticação de entidade de serviço, primeiro crie um registro de aplicativo no Azure Active Directory e conceda ao seu aplicativo acesso baseado em função ao workspace do ML. A maneira mais fácil de concluir essa configuração é usando o [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) no portal do Azure. Depois de fazer logon no portal, clique no ícone de `>_` no canto superior direito da página, ao lado de seu nome, para abrir o shell.

Se ainda não tiver usado o Cloud Shell em sua conta do Azure, você precisará criar um recurso de conta de armazenamento para armazenar os arquivos gravados. Em geral, essa conta de armazenamento incorre em um custo mensal insignificante. Além disso, use o comando a seguir para instalar a extensão de machine learning se você ainda não a usou.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Você precisa ser um administrador na assinatura para executar as etapas a seguir.

Depois, execute o comando a seguir para criar a entidade de serviço. Dê um nome a ele, neste caso **ml-auth**.

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

Em seguida, execute o comando a seguir para obter os detalhes da entidade de serviço recém-criada, usando o valor `clientId` acima como entrada para o parâmetro `--id`.

```azurecli-interactive
az ad sp show --id your-client-id
```

O exemplo a seguir é uma simplificação da saída JSON do comando. Anote o campo `objectId`, pois você precisará do valor dele para a próxima etapa.

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

Depois, use o comando a seguir para atribuir à entidade de serviço acesso ao seu workspace do machine learning. Você precisará do nome do workspace e do nome do grupo de recursos dele para os parâmetros `-w` e `-g`, respectivamente. Para o parâmetro `--user`, use o valor `objectId` da etapa anterior. O parâmetro `--role` permite definir a função de acesso para a entidade de serviço e, em geral, você usará a de **proprietário** ou a de **colaborador**. Ambas têm acesso de gravação a recursos existentes, como clusters de cálculo e armazenamentos de dados, mas somente o **proprietário** pode provisionar esses recursos.

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Essa chamada não produz nenhuma saída, mas agora você tem a autenticação da entidade de serviço configurada para o workspace.

## <a name="authenticate-to-your-workspace"></a>Autenticar no workspace

Agora que tem a autenticação de entidade de serviço habilitada, você pode autenticar seu workspace no SDK sem fazer logon fisicamente como usuário. Use o construtor da classe `ServicePrincipalAuthentication` e os valores obtidos nas etapas anteriores como os parâmetros. O parâmetro `tenant_id` é mapeado para `tenantId` acima, `service_principal_id` é mapeado para `clientId` e `service_principal_password` é mapeado para `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Agora, a variável `sp` contém um objeto de autenticação que você usa diretamente no SDK. Em geral, é uma boa ideia armazenar as IDs/segredos usados acima em variáveis de ambiente, conforme mostrado no código a seguir.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Para fluxos de trabalho automatizados que são executados no Python e usam principalmente o SDK, você pode usar esse objeto no estado em que se encontra na maioria dos casos para autenticação. O código a seguir é autenticado em seu workspace usando o objeto de autenticação recém-criado.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Autenticação da API REST do Azure Machine Learning

A entidade de serviço criada nas etapas acima também pode ser usada para autenticação na [API REST](https://docs.microsoft.com/rest/api/azureml/) do Azure Machine Learning. Use o [fluxo de concessão de credenciais de cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) do Azure Active Directory, que permite fazer chamadas de serviço a serviço para autenticação sem periféricos em fluxos de trabalho automatizados. Os exemplos são implementados com a [Biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) no Python e no Node.js, mas você pode usar qualquer biblioteca de software livre com suporte para OpenID Connect 1.0.

> [!NOTE]
> A MSAL.js é uma biblioteca mais recente do que a ADAL, mas não é possível fazer a autenticação de serviço a serviço usando credenciais do cliente com a MSAL.js, pois ela é basicamente uma biblioteca do lado do cliente destinada à autenticação interativa/de interface do usuário vinculada a um usuário específico. É recomendável usar a ADAL, conforme mostrado abaixo, para criar fluxos de trabalho automatizados com a API REST.

### <a name="nodejs"></a>Node.js

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

A variável `tokenResponse` é um objeto que inclui o token e os metadados associados, como o tempo de expiração. Os tokens são válidos por uma hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. A seguir, temos uma resposta de exemplo.

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

### <a name="python"></a>Python

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

A variável `token_response` é um dicionário que inclui o token e os metadados associados, como o tempo de expiração. Os tokens são válidos por uma hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. A seguir, temos uma resposta de exemplo.

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

Os serviços Web no Azure Machine Learning usam um padrão de autenticação diferente do descrito acima. A maneira mais fácil fazer a autenticação em serviços Web implantados é usar a **autenticação baseada em chave**, que gera chaves de autenticação de portador estáticas, que não precisam ser atualizadas. Se você só precisa fazer a autenticação um serviço Web implantado, não é necessário configurar a autenticação de entidade de serviço conforme mostrado acima.

Os serviços Web implantados no Serviço de Kubernetes do Azure têm a autenticação baseada em chave *habilitada* por padrão. Os serviços implantados nas Instâncias de Contêiner do Azure têm a autenticação baseada em chave *desabilitada* por padrão, mas você pode habilitá-la configurando `auth_enabled=True` ao criar o serviço Web na ACI. A seguir, há um exemplo de como criar uma configuração de implantação nas ACI com a autenticação baseada em chave habilitada.

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

Os serviços Web também dão suporte à autenticação baseada em token, mas somente para implantações do Serviço de Kubernetes do Azure. Consulte as [instruções](how-to-consume-web-service.md) sobre o consumo de serviços Web para obter informações adicionais sobre a autenticação.

### <a name="token-based-web-service-authentication"></a>Autenticação de serviço Web baseada em token

Quando você habilita a autenticação de token para um serviço Web, os usuários precisam apresentar um Token Web JSON do Azure Machine Learning para o serviço Web para acessá-lo. O token expira após um período de tempo determinado e precisa ser atualizado para continuar fazendo chamadas.

* A autenticação de token é **desabilitada por padrão** quando é feita uma implantação no Serviço de Kubernetes do Azure.
* **Não há suporte** para a autenticação de token durante a implantação nas Instâncias de Contêiner do Azure.

Para controlar a autenticação do token, use o parâmetro `token_auth_enabled` ao criar ou atualizar uma implantação.

Se a autenticação de token estiver habilitada, você poderá usar o método `get_token` para recuperar um JWT (Token Web JSON) e a hora da expiração do token:

```python
token, refresh_by = service.get_token()
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

* [Treinar e implantar um modelo de classificação de imagem](tutorial-train-models-with-aml.md).
* [Consumir um modelo de Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md).
