---
title: Proxy REST do Apache Kafka – Azure HDInsight
description: Saiba como executar operações no Apache Kafka usando o proxy REST do Kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: 660e200b673da53af1ee00e4de1e2ce3298e861d
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876437"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagir com os clusters do Apache Kafka no Azure HDInsight por meio de um proxy REST

O Proxy REST do Kafka permite interagir com o cluster do Kafka por meio de uma API REST via HTTP. Dessa maneira, os clientes do Kafka podem ficar fora da rede virtual. Os clientes podem fazer chamadas HTTP simples ao cluster do Kafka em vez de usar as bibliotecas da Kafka. Neste artigo, você verá como criar um proxy REST habilitado para o cluster do Kafka. Além disso, verá um exemplo de código que mostra como fazer chamadas ao proxy REST.

## <a name="rest-api-reference"></a>Referência da API REST

Para ver as operações compatíveis com a API REST do Kafka, confira [Referência de API do Proxy REST do Kafka no HDInsight](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Segundo plano

![Design do proxy REST do Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Para ver a especificação completa das operações compatíveis com a API, confira [API do Proxy REST do Apache Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Ponto de extremidade de proxy REST

Quando você cria um cluster do Kafka no HDInsight com o proxy REST, um novo ponto de extremidade público é gerado para o cluster. É possível encontrá-lo no cluster **Propriedades** do HDInsight no portal do Azure.

### <a name="security"></a>Segurança

O acesso ao proxy REST do Kafka é gerenciado com os grupos de segurança do Azure Active Directory. Ao criar o cluster do Kafka, forneça o grupo de segurança do Azure AD com o acesso de ponto de extremidade REST. Os clientes do Kafka que precisam de acesso ao proxy REST devem ser registrados neste grupo pelo proprietário do grupo. O proprietário do grupo pode registrar por meio do Portal ou do PowerShell.

Para solicitações de ponto de extremidade de proxy REST, os aplicativos clientes precisam obter um token OAuth. O token é usado para verificar a associação ao grupo de segurança. Encontre um [exemplo de aplicativo cliente](#client-application-sample) abaixo que mostre como obter um token OAuth. O aplicativo cliente passa o token OAuth da solicitação para o proxy REST.

> [!NOTE]
> Para saber mais sobre os grupos de segurança do AAD, confira [Gerenciar o acesso a aplicativos e recursos por meio dos grupos do Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Para saber mais sobre como os tokens OAuth funcionam, confira [Autorizar o acesso aos aplicativos Web do Azure Active Directory por meio do fluxo de concessão de código do OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Proxy REST do Kafka com os Grupos de Segurança de Rede
Se você colocar a VNet e controlar o tráfego de rede nos grupos de segurança de rede, permita o tráfego de **entrada** na porta **9400** além de na porta 443. Dessa maneira, o servidor proxy REST do Kafka fica acessível.

## <a name="prerequisites"></a>Pré-requisitos

1. Registrar um aplicativo com o Azure AD. Os aplicativos cliente que você escreve para interagir com o proxy REST do Kafka usarão a ID e o segredo do aplicativo para autenticação no Azure.

1. Crie um grupo de segurança do Azure AD. Adicione o aplicativo registrado no Azure AD ao grupo de segurança como um **membro** do grupo. Esse grupo de segurança será usado para controlar quais aplicativos têm permissão para interagir com o proxy REST. Para obter mais informações sobre como criar grupos do Azure AD, confira [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Valide se o grupo é do tipo **Segurança**.
    ![Grupo de Segurança](./media/rest-proxy/rest-proxy-group.png)

    Valide se o aplicativo é membro do grupo.
    ![Verificar Associação](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Criar um cluster do Kafka com o proxy REST habilitado

Use o portal do Azure nas etapas a seguir. Para ver um exemplo de como usar a CLI do Azure, confira [Criar cluster do proxy REST do Apache Kafka com a CLI do Azure](tutorial-cli-rest-proxy.md).

1. Durante o fluxo de trabalho de criação do cluster do Kafka, na guia **Segurança + rede**, verifique a opção **Habilitar proxy REST do Kafka**.

     ![Habilitar o proxy REST do Kafka e selecionar o grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Clique em **Selecionar Grupo de Segurança**. Na lista de grupos de segurança, selecione aqueles que você quer que tenham acesso ao proxy REST. Use a caixa de pesquisa para encontrar o grupo de segurança apropriado. Clique no botão **Selecionar** na parte inferior.

     ![Habilitar o proxy REST do Kafka e selecionar o grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Conclua as etapas restantes para criar o cluster, conforme descrito em [Criar um cluster do Apache Kafka no Azure HDInsight usando o portal do Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Após a criação do cluster, acesse as propriedades dele para registrar a URL do proxy REST do Kafka.

     ![Exibir a URL do proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exemplo de aplicativo cliente

Use o código Python a seguir para interagir com o proxy REST no cluster do Kafka. Para usar o exemplo de código, siga estas etapas:

1. Salve o exemplo de código em um computador com o Python instalado.
1. Instale as dependências necessárias do Python executando `pip3 install msal`.
1. Modifique a seção de código **Configurar estas propriedades** e atualize as seguintes propriedades do seu ambiente:

    |Propriedade |Descrição |
    |---|---|
    |ID do locatário|O locatário do Azure em que sua assinatura está.|
    |ID do Cliente|A ID do aplicativo que você registrou no grupo de segurança.|
    |Segredo do cliente|O segredo para o aplicativo que você registrou no grupo de segurança.|
    |Kafkarest_endpoint|Obtenha esse valor na guia **Propriedades** da visão geral do cluster, conforme descrito na [seção de implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Ele deve estar no seguinte formato: `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Na linha de comando, execute o arquivo Python com `sudo python3 <filename.py>`

Esse código faz o seguinte:

1. Busca um token OAuth no Azure AD.
1. Mostra como fazer uma solicitação para o proxy REST do Kafka.

Para saber mais sobre como obter tokens OAuth no Python, confira [Classe AuthenticationContext do Python](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Talvez haja um atraso enquanto `topics` que não são criados ou excluídos por meio do proxy REST do Kafka são refletidos. Esse atraso ocorre por causa da atualização do cache.

```python
#Required python packages
#pip3 install msal

import msal

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Veja abaixo outro exemplo de como obter um token do Azure para o proxy REST usando um comando curl. **Observe que o `scope=https://hib.azurehdinsight.net/.default` especificado é necessário para obter o token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Próximas etapas

* [Documentos de referência da API do proxy REST do Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
