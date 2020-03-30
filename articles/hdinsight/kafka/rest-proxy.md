---
title: Apache Kafka REST proxy - Azure HDInsight
description: Aprenda a executar operações Apache Kafka usando um proxy Kafka REST no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d99a3b803b80dc41990a63e647d3ba928deb31af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198898"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interaja com clusters Apache Kafka no Azure HDInsight usando um proxy REST

Kafka REST Proxy permite que você interaja com seu cluster Kafka através de uma API REST via HTTP. Isso significa que seus clientes Kafka podem estar fora de sua rede virtual. Além disso, os clientes podem fazer chamadas HTTP simples para enviar e receber mensagens para o cluster Kafka, em vez de depender de bibliotecas Kafka. Este tutorial mostrará como criar um cluster Kafka habilitado para proxy REST e fornecer um código de exemplo que mostra como fazer chamadas para o proxy REST.

## <a name="rest-api-reference"></a>Referência da API REST

Para obter a especificação completa das operações suportadas pela API Kafka REST, consulte [HDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Segundo plano

![Arquitetura proxy Kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Para obter a especificação completa das operações suportadas pela API, consulte a [API proxy Apache Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Ponto final do PROXY REST

A criação de um cluster HDInsight Kafka com proxy REST cria um novo ponto final público para o seu cluster, que você pode encontrar no cluster HDInsight "Propriedades" no portal Azure.

### <a name="security"></a>Segurança

O acesso ao proxy Kafka REST é gerenciado com grupos de segurança do Azure Active Directory. Ao criar o cluster Kafka com o proxy REST ativado, você fornecerá o grupo de segurança do Azure Active Directory que deve ter acesso ao ponto final REST. Os clientes Kafka (aplicativos) que precisam de acesso ao proxy REST devem ser registrados neste grupo pelo proprietário do grupo. O proprietário do grupo pode fazer isso através do Portal ou via Powershell.

Antes de fazer solicitações para o ponto final do proxy REST, o aplicativo cliente deve obter um token OAuth para verificar a adesão ao grupo de segurança certo. Por favor, encontre uma [amostra de aplicativo cliente](#client-application-sample) abaixo que mostre como obter um token OAuth. Uma vez que o aplicativo cliente tenha o token OAuth, ele deve passar esse token na solicitação HTTP feita ao proxy REST.

> [!NOTE]  
> Consulte [Gerenciar o acesso a aplicativos e recursos usando grupos do Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md), para saber mais sobre grupos de segurança AAD. Para obter mais informações sobre como os tokens OAuth funcionam, consulte [Autorizar o acesso aos aplicativos web do Azure Active Directory usando o fluxo de concessão de código OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Pré-requisitos

1. Registrar um aplicativo com o Azure AD. Os aplicativos clientes que você escreve para interagir com o proxy Kafka REST usarão o ID e o segredo deste aplicativo para autenticar o Azure.
1. Crie um grupo de segurança Azure AD e adicione o aplicativo que você registrou no Azure AD ao grupo de segurança. Esse grupo de segurança será usado para controlar quais aplicativos podem interagir com o proxy REST. Para obter mais informações sobre a criação de grupos AD do Azure, consulte [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Crie um cluster Kafka com proxy REST ativado

1. Durante o fluxo de trabalho de criação de cluster kafka, na guia "Segurança + rede", verifique a opção "Ativar proxy Kafka REST".

     ![Habilite o proxy Kafka REST e selecione o grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Clique **em Selecionar grupo de segurança**. Na lista de grupos de segurança, selecione o grupo de segurança que deseja ter acesso ao proxy REST. Você pode usar a caixa de pesquisa para encontrar o grupo de segurança apropriado. Clique no botão **Selecionar** na parte inferior.

     ![Habilite o proxy Kafka REST e selecione o grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Complete as etapas restantes para criar seu cluster conforme descrito no [cluster Create Apache Kafka no Azure HDInsight usando o portal Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Uma vez que o cluster é criado, vá para as propriedades do cluster para gravar a URL proxy Kafka REST.

     ![exibir URL proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Amostra de aplicativo de cliente

Você pode usar o código python abaixo para interagir com o proxy REST em seu cluster Kafka. Para usar a amostra de código, siga estas etapas:

1. Salve o código de exemplo em uma máquina com Python instalado.
1. Instale as dependências `pip3 install adal` de `pip install msrestazure`python necessárias executando e .
1. Modificar a seção de código *Configure essas propriedades* e atualize as seguintes propriedades para o seu ambiente:
    1.  *ID do inquilino* – O inquilino do Azure onde está sua assinatura.
    1.  *ID do cliente* – O ID do aplicativo que você registrou no grupo de segurança.
    1.  *Client Secret* – O segredo para o aplicativo que você registrou no grupo de segurança
    1.  *Kafkarest_endpoint* – obtenha esse valor na guia "propriedades" na visão geral do cluster conforme descrito na [seção de implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve ser no seguinte formato –`https://<clustername>-kafkarest.azurehdinsight.net`
3. A partir da linha de comando, execute o arquivo python executando`python <filename.py>`

O código faz o seguinte:

1. Busca um token OAuth do Azure AD
1. Mostra como fazer uma solicitação ao proxy Kafka REST

Para obter mais informações sobre como obter tokens OAuth em python, consulte [classe Python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Você pode ver um atraso enquanto os tópicos que não são criados ou excluídos através do proxy Kafka REST são refletidos lá. Esse atraso deve-se à atualização do cache.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

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

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

## <a name="next-steps"></a>Próximas etapas

* [Kafka REST proxy Documentos de referência da API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
