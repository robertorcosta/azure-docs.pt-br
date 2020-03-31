---
title: Configurar a autenticação
titleSuffix: Azure Machine Learning
description: Saiba como configurar e configurar a autenticação para vários recursos e fluxos de trabalho no Azure Machine Learning. Existem várias maneiras de configurar e usar a autenticação dentro do serviço, desde auth simples baseados em interface do usuário para fins de desenvolvimento ou teste, até autenticação principal do serviço de diretório ativo do Azure Active.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283531"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configure a autenticação para recursos e fluxos de trabalho do Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende como configurar e configurar a autenticação para vários recursos e fluxos de trabalho no Azure Machine Learning. Existem várias maneiras de autenticar o serviço, desde o auth baseado em interface do usuário simples para fins de desenvolvimento ou teste até a autenticação principal do serviço de diretório ativo do Azure. Este artigo também explica as diferenças em como funciona a autenticação de serviços web, bem como como como autenticar para a API Azure Machine Learning REST.

Esta forma de como fazer mostra como fazer as seguintes tarefas:

* Use autenticação interativa de IU para teste/desenvolvimento
* Configurar a autenticação principal do serviço
* Autenticando para o seu espaço de trabalho
* Obtenha tokens tipo OAuth2.0 para a API de aprendizado de máquina do Azure
* Entenda a autenticação do web service

Consulte o [artigo conceitual](concept-enterprise-security.md) para uma visão geral da segurança e autenticação dentro do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um [Workspace do Azure Machine Learning](how-to-manage-workspace.md).
* [Configure seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK ou use um [VM de notebook de aprendizado de máquina do Azure](concept-azure-machine-learning-architecture.md#compute-instance) com o SDK já instalado.

## <a name="interactive-authentication"></a>Autenticação interativa

A maioria dos exemplos na documentação para este serviço usa autenticação interativa em notebooks Jupyter como um método simples para testes e demonstração. Esta é uma maneira leve de testar o que você está construindo. Existem duas chamadas de função que o solicitarão automaticamente com um fluxo de autenticação baseado em UI.

Chamar `from_config()` a função emitirá o prompt.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

A `from_config()` função procura um arquivo JSON contendo suas informações de conexão no espaço de trabalho. Você também pode especificar os detalhes da conexão explicitamente usando o construtor, o `Workspace` que também solicitará a autenticação interativa. Ambas as chamadas são equivalentes.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Se você tiver acesso a vários inquilinos, talvez seja necessário importar a classe e definir explicitamente qual inquilino você está mirando. Chamar o construtor `InteractiveLoginAuthentication` também solicitará que você faça login semelhante às chamadas acima.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Embora útil para testes e aprendizados, a autenticação interativa não o ajudará na construção de fluxos de trabalho automatizados ou sem cabeça. A configuração da autenticação principal do serviço é a melhor abordagem para processos automatizados que usam o SDK.

## <a name="set-up-service-principal-authentication"></a>Configurar a autenticação principal do serviço

Esse processo é necessário para habilitar a autenticação que é dissociada de um login específico do usuário, o que permite que você se autentique no Azure Machine Learning Python SDK em fluxos de trabalho automatizados. A autenticação principal do serviço também permitirá que você [se comvocê autenticar para a API REST](#azure-machine-learning-rest-api-auth).

Para configurar a autenticação principal do serviço, primeiro crie um registro de aplicativo no Azure Active Directory e, em seguida, conceda ao seu aplicativo acesso baseado em função ao seu espaço de trabalho ML. A maneira mais fácil de concluir essa configuração é através da [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) no portal Azure. Depois de fazer login no `>_` portal, clique no ícone no canto superior direito da página perto do seu nome para abrir o shell.

Se você não tiver usado o shell de nuvem antes em sua conta do Azure, você precisará criar um recurso de conta de armazenamento para armazenar quaisquer arquivos que estejam escritos. Em geral, esta conta de armazenamento incorrerá em um custo mensal insignificante. Além disso, instale a extensão de aprendizado de máquina se você não a tiver usado anteriormente com o seguinte comando.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Você deve ser um admin na assinatura para executar as seguintes etapas.

Em seguida, execute o seguinte comando para criar o principal de serviço. Dê-lhe um nome, neste caso **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

A saída será um JSON semelhante ao seguinte. Tome nota `clientSecret`dos `tenantId` `clientId`campos e campos, pois você precisará deles para outras etapas neste artigo.

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

Em seguida, execute o seguinte comando para obter os detalhes `clientId` sobre o principal de `--id` serviço que você acabou de criar, usando o valor de cima como a entrada para o parâmetro.

```azurecli-interactive
az ad sp show --id your-client-id
```

A seguir está um exemplo simplificado da saída JSON do comando. Tome nota do `objectId` campo, pois você precisará de seu valor para o próximo passo.

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

Em seguida, use o seguinte comando para atribuir seu acesso principal de serviço ao seu espaço de trabalho de aprendizado de máquina. Você precisará do seu nome de espaço de `-w` `-g` trabalho e do nome do grupo de recursos para os parâmetros e parâmetros, respectivamente. Para `--user` o parâmetro, `objectId` use o valor da etapa anterior. O `--role` parâmetro permite definir a função de acesso para o diretor do serviço e, em geral, você usará **o proprietário** ou **o contribuinte.** Ambos têm acesso à gravação de recursos existentes, como clusters de computação e datastores, mas apenas **o proprietário** pode provisionar esses recursos. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Esta chamada não produz nenhuma saída, mas agora você tem a autenticação principal do serviço configurada para o seu espaço de trabalho.

## <a name="authenticate-to-your-workspace"></a>Autenticar em seu espaço de trabalho

Agora que você tem o auth principal de serviço ativado, você pode autenticar para o seu espaço de trabalho no SDK sem fazer login fisicamente como usuário. Use `ServicePrincipalAuthentication` o construtor de classe e use os valores que você obteve das etapas anteriores como parâmetros. O `tenant_id` parâmetro `tenantId` mapeia `service_principal_id` para `clientId`de `service_principal_password` cima, mapas para , e mapas para `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

A `sp` variável agora contém um objeto de autenticação que você usa diretamente no SDK. Em geral, é uma boa ideia armazenar os ids/segredos usados acima em variáveis de ambiente, conforme mostrado no código a seguir.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Para fluxos de trabalho automatizados que são executados em Python e usam o SDK principalmente, você pode usar este objeto como está na maioria dos casos para sua autenticação. O código a seguir autentica-se no seu espaço de trabalho usando o objeto auth que você acabou de criar.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API auth

O principal de serviço criado nas etapas acima também pode ser usado para autenticar a [API](https://docs.microsoft.com/rest/api/azureml/)Azure Machine Learning REST . Você usa o [fluxo de concessão de credenciais do cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)do Azure Active Directory, que permite chamadas de serviço para autenticação sem cabeça em fluxos de trabalho automatizados. Os exemplos são implementados com a [biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) em Python e Node.js, mas você também pode usar qualquer biblioteca de código aberto que suporte o OpenID Connect 1.0. 

> [!NOTE]
> O MSAL.js é uma biblioteca mais nova que a ADAL, mas você não pode fazer autenticação serviço-serviço usando credenciais de cliente com MSAL.js, uma vez que é principalmente uma biblioteca do lado do cliente destinada à autenticação interativa/UI vinculada a um usuário específico. Recomendamos o uso do ADAL como mostrado abaixo para construir fluxos de trabalho automatizados com a API REST.

### <a name="nodejs"></a>Node.js

Use as seguintes etapas para gerar um token auth usando Node.js. Em seu ambiente, corra. `npm install adal-node` Em seguida, `tenantId` `clientId`use `clientSecret` o seu , e a partir do principal de serviço que você criou nas etapas acima como valores para as variáveis correspondentes no script a seguir.

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

A `tokenResponse` variável é um objeto que inclui o token e os metadados associados, como o tempo de expiração. Os tokens são válidos por 1 hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. A seguir, uma resposta amostral.

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

Use `accessToken` a propriedade para buscar o token auth. Consulte a documentação da [API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) para exemplos sobre como usar o token para fazer chamadas de API.

### <a name="python"></a>Python 

Use as seguintes etapas para gerar um token auth usando Python. Em seu ambiente, corra. `pip install adal` Em seguida, `tenantId` `clientId`use `clientSecret` o seu , e a partir do principal de serviço que você criou nas etapas acima como valores para as variáveis apropriadas no script a seguir.

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

A `token_response` variável é um dicionário que inclui o token e metadados associados, como o tempo de expiração. Os tokens são válidos por 1 hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. A seguir, uma resposta amostral.

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

Use `token_response["accessToken"]` para buscar o token auth. Consulte a documentação da [API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) para exemplos sobre como usar o token para fazer chamadas de API.

## <a name="web-service-authentication"></a>Autenticação de serviço web

Os serviços web no Azure Machine Learning usam um padrão de autenticação diferente do descrito acima. A maneira mais fácil de autenticar para serviços web implantados é usar **autenticação baseada em chaves,** que gera chaves de autenticação do tipo portador estático que não precisam ser atualizadas. Se você só precisa autenticar para um serviço web implantado, você não precisa configurar a autenticação do princípio do serviço como mostrado acima.

Os serviços da Web implantados no Azure Kubernetes Service têm auth baseado em chaves *ativado* por padrão. Os serviços implantados do Azure Container Instances têm o auth `auth_enabled=True`baseado em chaves *desativado* por padrão, mas você pode habilitá-lo configurando ao criar o serviço web ACI. O seguinte é um exemplo de criação de uma configuração de implantação de ACI com auth baseado em chave ativado.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Em seguida, você pode usar a `Model` configuração ACI personalizada em implantação usando a classe.

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

Para buscar as chaves `aci_service.get_keys()`auth, use . Para regenerar uma tecla, use a `regen_key()` função e passe **primária** ou **secundária.**

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Os serviços da Web também suportam autenticação baseada em tokens, mas apenas para implantações do Azure Kubernetes Service. Consulte [o como consumir](how-to-consume-web-service.md) serviços web para obter informações adicionais sobre autenticação.

### <a name="token-based-web-service-authentication"></a>Autenticação de serviço web baseada em token

Quando você habilita a autenticação de token para um serviço web, os usuários devem apresentar um Azure Machine Learning JSON Web Token para o serviço web para acessá-lo. O token expira após um período de tempo especificado e precisa ser atualizado para continuar fazendo chamadas.

* A autenticação do token é **desativada por padrão** quando você é implantado no Azure Kubernetes Service.
* A autenticação de **token não é suportada** quando você implanta no Azure Container Instances.

Para controlar a autenticação `token_auth_enabled` do token, use o parâmetro quando criar ou atualizar uma implantação.

Se a autenticação do token estiver `get_token` ativada, você poderá usar o método para recuperar um JSON Web Token (JWT) e o tempo de expiração desse token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Você precisará solicitar um novo token após o `refresh_by` tempo do token. Se você precisar atualizar tokens fora do Python SDK, uma opção é usar a API `service.get_token()` REST com autenticação principal de serviço para fazer a chamada periodicamente, como discutido anteriormente.
>
> Recomendamos fortemente que você crie seu espaço de trabalho azure Machine Learning na mesma região que seu cluster Azure Kubernetes Service. 
>
> Para autenticar com um token, o serviço web fará uma chamada para a região em que seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho não estiver disponível, você não poderá buscar um token para o seu serviço web, mesmo que seu cluster esteja em uma região diferente do seu espaço de trabalho. O resultado é que a autenticação Azure AD não está disponível até que a região do seu espaço de trabalho esteja disponível novamente. 
>
> Além disso, quanto maior a distância entre a região do seu cluster e a região do seu espaço de trabalho, mais tempo levará para buscar um token.

## <a name="next-steps"></a>Próximas etapas

* [Treine e implante um modelo de classificação de imagem](tutorial-train-models-with-aml.md).
* [Consumir um modelo de Machine Learning do Azure implantado como um serviço web](how-to-consume-web-service.md).
