---
title: Apache Kafka proxy REST-Azure HDInsight
description: Saiba como fazer Apache Kafka operações usando um proxy REST do Kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759002"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagir com clusters Apache Kafka no Azure HDInsight usando um proxy REST

O proxy REST do Kafka permite que você interaja com o cluster do Kafka por meio de uma API REST sobre HTTP. Essa ação significa que os clientes do Kafka podem estar fora de sua rede virtual. Os clientes podem fazer chamadas HTTP simples para o cluster Kafka, em vez de depender de bibliotecas Kafka. Este artigo mostrará como criar um cluster Kafka habilitado para proxy REST. Também fornece um código de exemplo que mostra como fazer chamadas para o proxy REST.

## <a name="rest-api-reference"></a>Referência da API REST

Para operações com suporte pela API REST do Kafka, consulte [referência da API de proxy REST do HDInsight Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Tela de fundo

![Design de proxy REST do Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Para obter a especificação completa das operações com suporte pela API, consulte [Apache Kafka API de proxy REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Ponto de extremidade do proxy REST

A criação de um cluster HDInsight Kafka com proxy REST cria um novo ponto de extremidade público para o cluster, que pode ser encontrado nas **Propriedades** do cluster HDInsight na portal do Azure.

### <a name="security"></a>Segurança

O acesso ao proxy REST do Kafka é gerenciado com Azure Active Directory grupos de segurança. Ao criar o cluster Kafka, forneça o grupo de segurança do Azure AD com acesso de ponto de extremidade REST. Os clientes Kafka que precisam de acesso ao proxy REST devem ser registrados nesse grupo pelo proprietário do grupo. O proprietário do grupo pode se registrar por meio do portal ou por meio do PowerShell.

Para solicitações de ponto de extremidade de proxy REST, os aplicativos cliente devem obter um token OAuth. O token é usado para verificar a associação de grupo de segurança. Encontre um [exemplo de aplicativo cliente](#client-application-sample) abaixo que mostra como obter um token OAuth. O aplicativo cliente passa o token OAuth na solicitação HTTP para o proxy REST.

> [!NOTE]  
> Consulte [gerenciar o acesso a aplicativos e recursos usando grupos de Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md)para saber mais sobre os grupos de segurança do AAD. Para obter mais informações sobre como os tokens OAuth funcionam, consulte [autorizar o acesso a Azure Active Directory aplicativos Web usando o fluxo de concessão de código oauth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Pré-requisitos

1. Registrar um aplicativo com o Azure AD. Os aplicativos cliente que você escreve para interagir com o proxy REST do Kafka usarão a ID e o segredo do aplicativo para autenticar no Azure.

1. Crie um grupo de segurança do Azure AD. Adicione o aplicativo que você registrou com o Azure AD ao grupo de segurança como um **membro** do grupo. Esse grupo de segurança será usado para controlar quais aplicativos têm permissão para interagir com o proxy REST. Para obter mais informações sobre como criar grupos do Azure AD, consulte [criar um grupo básico e adicionar membros usando Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Validar se o grupo é do tipo **segurança**.
    ![Grupo de Segurança](./media/rest-proxy/rest-proxy-group.png)

    Valide se o aplicativo é membro do grupo.
    ![Verificar associação](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Criar um cluster Kafka com o proxy REST habilitado

1. Durante o fluxo de trabalho de criação do cluster Kafka, na guia **segurança + rede** , marque a opção **habilitar proxy REST do Kafka** .

     ![Habilitar o proxy REST do Kafka e selecionar grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Clique em **selecionar grupo de segurança**. Na lista de grupos de segurança, selecione o grupo de segurança que você deseja ter acesso ao proxy REST. Você pode usar a caixa de pesquisa para localizar o grupo de segurança apropriado. Clique no botão **selecionar** na parte inferior.

     ![Habilitar o proxy REST do Kafka e selecionar grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Conclua as etapas restantes para criar o cluster, conforme descrito em [criar Apache Kafka cluster no Azure HDInsight usando portal do Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Depois que o cluster for criado, vá para as propriedades do cluster para registrar a URL do proxy REST do Kafka.

     ![exibir URL do proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exemplo de aplicativo cliente

Você pode usar o código Python abaixo para interagir com o proxy REST no cluster Kafka. Para usar o exemplo de código, siga estas etapas:

1. Salve o código de exemplo em um computador com o Python instalado.
1. Instale as dependências necessárias do `pip3 install msal`Python executando.
1. Modifique a seção de código **Configure essas propriedades** e atualize as seguintes propriedades para o seu ambiente:

    |Propriedade |Descrição |
    |---|---|
    |ID do locatário|O locatário do Azure onde está sua assinatura.|
    |ID do cliente|A ID do aplicativo que você registrou no grupo de segurança.|
    |Segredo do cliente|O segredo para o aplicativo que você registrou no grupo de segurança.|
    |Kafkarest_endpoint|Obtenha esse valor na guia **Propriedades** na visão geral do cluster, conforme descrito na [seção implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Ele deve estar no seguinte formato –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Na linha de comando, execute o arquivo Python executando`sudo python3 <filename.py>`

Esse código faz a seguinte ação:

1. Busca um token OAuth do Azure AD.
1. Mostra como fazer uma solicitação para o proxy REST do Kafka.

Para obter mais informações sobre como obter tokens OAuth no Python, consulte [Python AuthenticationContext Class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Você pode ver um atraso enquanto `topics` eles não são criados ou excluídos por meio do proxy REST Kafka são refletidos lá. Esse atraso é devido à atualização do cache.

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

Encontre abaixo outro exemplo de como obter um token do Azure para o proxy REST usando um comando de ondulação. **Observe que precisamos do `scope=https://hib.azurehdinsight.net/.default` especificado ao obter um token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Próximas etapas

* [Documentos de referência da API de proxy REST do Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
