---
title: 'Tutorial: mapear um domínio personalizado existente para o Azure Spring Cloud'
description: Como mapear um nome DNS (Sistema de Nomes de Domínio) personalizado existente para o Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 05107d0246be2273c09e91573bd30a4108ac7795
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290320"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapear um domínio personalizado existente para o Azure Spring Cloud
O DNS (Serviço de Nomes de Domínio) é uma técnica para armazenar nomes de nó de rede em uma rede. Este tutorial mapeia um domínio, como www.contoso.com, usando um registro CNAME. Ele protege o domínio personalizado com um certificado e mostra como impor o protocolo TLS, também conhecido como protocolo SSL. 

Os certificados criptografam o tráfego da Web. Esses certificados TLS/SSL podem ser armazenados no Azure Key Vault. 

## <a name="prerequisites"></a>Pré-requisitos
* Um aplicativo implantado no Azure Spring Cloud (confira [Início rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](spring-cloud-quickstart.md) ou use um aplicativo existente).
* Um nome de domínio com acesso ao Registro DNS para um provedor de domínio, como o GoDaddy.
* Um certificado privado (ou seja, seu certificado autoassinado) de um provedor de terceiros. O certificado precisa corresponder ao domínio.
* Uma instância implantada do [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Importar certificado 
O procedimento para importar um certificado requer que o arquivo codificado como PEM ou PFX esteja no disco e que você tenha a chave privada. 

Para carregar seu certificado autoassinado no cofre de chaves:
1. Vá para a instância do cofre de chaves.
1. No painel de navegação esquerdo, clique em **Certificados**.
1. No menu superior, clique em **Gerar/importar**.
1. Na caixa de diálogo **Criar um certificado** em **Método de criação de certificado**, selecione `Import`.
1. Em **Carregar o Arquivo de Certificado**, navegue até a localização do certificado e selecione-o.
1. Em **Senha**, insira a chave privada para o certificado.
1. Clique em **Criar**.

    ![Importar certificado 1](./media/custom-dns-tutorial/import-certificate-a.png)

Para conceder ao Azure Spring Cloud acesso ao seu cofre de chaves antes de importar o certificado:
1. Vá para a instância do cofre de chaves.
1. No painel de navegação esquerdo, clique em **Política de Acesso**.
1. No menu superior, clique em **Adicionar Política de Acesso**.
1. Preencha as informações e clique no botão **Adicionar** e, em seguida, **Salve** a política de acesso.

| Permissão de segredo | Permissão de certificado | Selecionar entidade de segurança |
|--|--|--|
| Obter, listar | Obter, listar | Gerenciamento de domínio do Azure Spring Cloud |

![Importar certificado 2](./media/custom-dns-tutorial/import-certificate-b.png)

Ou, você pode usar a CLI do Azure para conceder ao Azure Spring Cloud acesso ao cofre de chaves.

Obtenha a ID do objeto por meio do comando a seguir.
```
az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId
```

Conceda ao Azure Spring Cloud acesso de leitura ao cofre de chaves, substitua a ID do objeto no comando a seguir.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list --secret-permissions get list
``` 

Para importar o certificado para o Azure Spring Cloud:
1. Vá para a instância do serviço. 
1. No painel de navegação à esquerda do aplicativo, selecione **Configurações de TLS/SSL**.
1. Em seguida, clique em **Importar o Certificado do Key Vault**.

    ![Importar certificado](./media/custom-dns-tutorial/import-certificate.png)

Ou você pode usar a CLI do Azure para importar o certificado:

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> Conceda ao Azure Spring Cloud acesso ao seu cofre de chaves antes de executar o comando importar certificado anterior. Se não tiver feito isso, execute o comando a seguir para conceder os direitos de acesso.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

Depois de importar o certificado com êxito, você o verá na lista de **Certificados de Chave Privada**.

![Certificado de chave privada](./media/custom-dns-tutorial/key-certificates.png)

Ou, você pode usar a CLI do Azure para mostrar uma lista de certificados:

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, ainda é necessário associar o certificado a um domínio específico. Siga as etapas neste documento sob o título **Adicionar Associação SSL**.

## <a name="add-custom-domain"></a>Adicionar domínio personalizado
Você pode usar um registro CNAME para mapear um nome DNS personalizado para o Azure Spring Cloud. 

> [!NOTE] 
> Não há suporte para o registro A. 

### <a name="create-the-cname-record"></a>Criar um registro CNAME
Acesse seu provedor DNS e adicione um registro CNAME para mapear seu domínio para o <nome_do_serviço>.azuremicroservices.io. Aqui, <nome_do_serviço> é o nome da sua instância do Azure Spring Cloud. Damos suporte a domínio e subdomínio de caractere curinga. Depois de adicionar o CNAME, a página de registros DNS será parecida com o seguinte exemplo: 

![Página de Registros DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapear o domínio personalizado para o aplicativo do Azure Spring Cloud
Se você não tiver um aplicativo no Azure Spring Cloud, siga as instruções em [Início Rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Acesse a página do aplicativo.

1. Selecione **Domínio Personalizado**.
2. Em seguida, selecione **Adicionar Domínio Personalizado**. 

    ![Domínio personalizado](./media/custom-dns-tutorial/custom-domain.png)

3. Digite o nome de domínio totalmente qualificado ao qual você adicionou um registro CNAME, como www.contoso.com. Verifique se o tipo de registro de nome de host está definido como CNAME (<nome_do_serviço>.azuremicroservices.io)
4. Clique em **Validar** para habilitar o botão **Adicionar**.
5. Clique em **Adicionar**.

    ![Adicionar domínio personalizado](./media/custom-dns-tutorial/add-custom-domain.png)

Ou você pode usar a CLI do Azure para adicionar um domínio personalizado:
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

Um aplicativo pode ter vários domínios, mas um domínio só pode ser mapeado para um aplicativo. Quando você tiver mapeado com êxito seu domínio personalizado para o aplicativo, você o verá na tabela de domínios personalizados.

![Tabela de domínios personalizados](./media/custom-dns-tutorial/custom-domain-table.png)

Ou, você pode usar a CLI do Azure para mostrar uma lista de domínios personalizados:
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> Um rótulo **Não Seguro** para seu domínio personalizado significa que ele ainda não está associado a um certificado SSL. Solicitações HTTPS de um navegador para seu domínio personalizado receberão um erro ou aviso.

## <a name="add-ssl-binding"></a>Adicionar associação SSL
Na tabela de domínio personalizado, selecione **Adicionar associação SSL** conforme mostrado na figura anterior.  
1. Selecione seu **certificado** ou importe-o.
1. Clique em **Save** (Salvar).

    ![Adicionar associação SSL 1](./media/custom-dns-tutorial/add-ssl-binding.png)

Ou, você pode usar a CLI do Azure para **Adicionar associação SSL**:
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

Depois de adicionar a associação SSL com êxito, o estado do domínio será seguro: **Íntegro**. 

![Adicionar associação SSL 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Impor HTTPS
Por padrão, qualquer pessoa ainda pode acessar seu aplicativo usando HTTP, mas você pode redirecionar todas as solicitações HTTP para a porta HTTPS.

Na sua página do aplicativo, na navegação esquerda, selecione **Domínio Personalizado**. Em seguida, defina **Somente HTTPS** como *Verdadeiro*.

![Adicionar associação SSL 3](./media/custom-dns-tutorial/enforce-http.png)

Ou, você pode usar a CLI do Azure para impor HTTPS:
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

Quando a operação estiver concluída, navegue até qualquer uma das URLs HTTPS que aponte para seu aplicativo. Observe que as URLs HTTP não funcionam.

## <a name="see-also"></a>Confira também
* [O que é o Cofre da Chave do Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importar um certificado](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Iniciar o aplicativo Spring Cloud usando a CLI do Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

