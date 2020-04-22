---
title: Apache Kafka REST proxy - Azure HDInsight
description: Aprenda a fazer operações Apache Kafka usando um proxy Kafka REST no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759002"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interaja com clusters Apache Kafka no Azure HDInsight usando um proxy REST

Kafka REST Proxy permite que você interaja com seu cluster Kafka através de uma API REST via HTTP. Essa ação significa que seus clientes Kafka podem estar fora de sua rede virtual. Os clientes podem fazer chamadas HTTP simples para o cluster Kafka, em vez de depender de bibliotecas Kafka. Este artigo mostrará como criar um cluster Kafka habilitado para proxy REST. Também fornece um código de exemplo que mostra como fazer chamadas para proxy REST.

## <a name="rest-api-reference"></a>Referência da API REST

Para obter operações suportadas pela API Kafka REST, consulte [HDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Segundo plano

![Design de proxy Kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Para obter a especificação completa das operações suportadas pela API, consulte [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Ponto final do PROXY REST

A criação de um cluster HDInsight Kafka com proxy REST cria um novo ponto final público para o seu cluster, que você pode encontrar no cluster HDInsight **Propriedades** no portal Azure.

### <a name="security"></a>Segurança

O acesso ao proxy Kafka REST é gerenciado com grupos de segurança do Azure Active Directory. Ao criar o cluster Kafka, forneça ao grupo de segurança Azure AD acesso ao ponto final REST. Os clientes Kafka que precisam de acesso ao proxy REST devem ser registrados neste grupo pelo proprietário do grupo. O proprietário do grupo pode se inscrever através do Portal ou via PowerShell.

Para solicitações de ponto final de proxy REST, os aplicativos clientes devem receber um token OAuth. O token é usado para verificar a adesão ao grupo de segurança. Encontre uma [amostra de aplicativo cliente](#client-application-sample) abaixo que mostre como obter um token OAuth. O aplicativo cliente passa o token OAuth na solicitação HTTP para o proxy REST.

> [!NOTE]  
> Consulte [Gerenciar o acesso a aplicativos e recursos usando grupos do Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md), para saber mais sobre grupos de segurança AAD. Para obter mais informações sobre como os tokens OAuth funcionam, consulte [Autorizar o acesso aos aplicativos web do Azure Active Directory usando o fluxo de concessão de código OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Pré-requisitos

1. Registrar um aplicativo com o Azure AD. Os aplicativos clientes que você escreve para interagir com o proxy Kafka REST usarão o ID e o segredo deste aplicativo para autenticar o Azure.

1. Crie um grupo de segurança Azure AD. Adicione o aplicativo que você registrou no Azure AD ao grupo de segurança como **membro** do grupo. Esse grupo de segurança será usado para controlar quais aplicativos podem interagir com o proxy REST. Para obter mais informações sobre a criação de grupos AD do Azure, consulte [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Validar o grupo é do tipo **Segurança**.
    ![Grupo de Segurança](./media/rest-proxy/rest-proxy-group.png)

    Valide que a aplicação é membro do Grupo.
    ![Verifique a adesão](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Crie um cluster Kafka com proxy REST ativado

1. Durante o fluxo de trabalho de criação de cluster kafka, na guia **Segurança + rede,** verifique a opção **Ativar proxy Kafka REST.**

     ![Habilite o proxy Kafka REST e selecione o grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Clique **em Selecionar grupo de segurança**. Na lista de grupos de segurança, selecione o grupo de segurança que deseja ter acesso ao proxy REST. Você pode usar a caixa de pesquisa para encontrar o grupo de segurança apropriado. Clique no botão **Selecionar** na parte inferior.

     ![Habilite o proxy Kafka REST e selecione o grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Complete as etapas restantes para criar seu cluster conforme descrito no [cluster Create Apache Kafka no Azure HDInsight usando o portal Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Uma vez que o cluster é criado, vá para as propriedades do cluster para gravar a URL proxy Kafka REST.

     ![exibir URL proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Amostra de aplicativo de cliente

Você pode usar o código python abaixo para interagir com o proxy REST em seu cluster Kafka. Para usar a amostra de código, siga estas etapas:

1. Salve o código de exemplo em uma máquina com Python instalado.
1. Instale as dependências `pip3 install msal`de python necessárias executando .
1. Modificar a seção de código **Configure essas propriedades** e atualize as seguintes propriedades para o seu ambiente:

    |Propriedade |Descrição |
    |---|---|
    |ID do locatário|O inquilino do Azure onde está sua assinatura.|
    |ID do Cliente|O ID do aplicativo que você registrou no grupo de segurança.|
    |Segredo do cliente|O segredo para a aplicação que você registrou no grupo de segurança.|
    |Kafkarest_endpoint|Obtenha esse valor na guia **Propriedades** na visão geral do cluster conforme descrito na [seção de implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve ser no seguinte formato –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. A partir da linha de comando, execute o arquivo python executando`sudo python3 <filename.py>`

Este código faz a seguinte ação:

1. Busca um token OAuth do Azure AD.
1. Mostra como fazer uma solicitação ao proxy Kafka REST.

Para obter mais informações sobre como obter tokens OAuth em python, consulte [classe Python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Você pode ver `topics` um atraso enquanto que não são criados ou excluídos através do proxy Kafka REST são refletidos lá. Esse atraso é por causa da atualização do cache.

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

Encontre abaixo outra amostra de como obter um token do Azure para proxy REST usando um comando curl. **Observe que precisamos `scope=https://hib.azurehdinsight.net/.default` do especificado ao receber um token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Próximas etapas

* [Kafka REST proxy Documentos de referência da API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
