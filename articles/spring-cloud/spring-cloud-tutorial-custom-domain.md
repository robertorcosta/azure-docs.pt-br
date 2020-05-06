---
title: 'Tutorial: mapear um domínio personalizado existente para o Azure Spring Cloud'
description: Como mapear um nome DNS (Sistema de Nomes de Domínio) personalizado existente para o Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: 5b57a2463815d5db1c83d3bc4e8cd56314fb204d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82176831"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapear um domínio personalizado existente para o Azure Spring Cloud
O DNS (Sistema de Nomes de Domínio) é uma técnica para armazenar nomes de nó de rede em uma rede. Este tutorial mapeia um domínio, como www.contoso.com, usando um registro CNAME. Ele protege o domínio personalizado com um certificado e mostra como impor o protocolo TLS, também conhecido como protocolo SSL. 

Os certificados criptografam o tráfego da Web. Esses certificados TLS/SSL podem ser armazenados no Azure Key Vault. 

## <a name="prerequisites"></a>Pré-requisitos
* Um aplicativo implantado no Azure Spring Cloud (confira [Início rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](spring-cloud-quickstart-launch-app-portal.md) ou use um aplicativo existente).
* Um nome de domínio com acesso ao Registro DNS para um provedor de domínio, como o GoDaddy.
* Um certificado privado de um provedor de terceiros. O certificado precisa corresponder ao domínio.
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

Para importar o certificado para o Azure Spring Cloud:
1. Vá para a instância do serviço. 
1. No painel de navegação à esquerda do aplicativo, selecione **Configurações de TLS/SSL**.
1. Em seguida, clique em **Importar o Certificado do Key Vault**.

![Importar certificado](./media/custom-dns-tutorial/import-certificate.png)

Depois de importar o certificado com êxito, você o verá na lista de **certificados de chave privada**.

![Certificado de chave privada](./media/custom-dns-tutorial/key-certificates.png)

>[!IMPORTANT] 
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

Um aplicativo pode ter vários domínios, mas um domínio só pode ser mapeado para um aplicativo. Quando você tiver mapeado com êxito seu domínio personalizado para o aplicativo, você o verá na tabela de domínios personalizados.

![Tabela de domínios personalizados](./media/custom-dns-tutorial/custom-domain-table.png)

>[!NOTE]
> Um rótulo **Não Seguro** para seu domínio personalizado significa que ele ainda não está associado a um certificado SSL. Solicitações HTTPS de um navegador para seu domínio personalizado receberão um erro ou aviso.

## <a name="add-ssl-binding"></a>Adicionar associação SSL
Na tabela de domínio personalizado, selecione **Adicionar associação SSL** conforme mostrado na figura anterior.  
1. Selecione seu **certificado** ou importe-o.
1. Clique em **Save** (Salvar).

![Adicionar associação SSL](./media/custom-dns-tutorial/add-ssl-binding.png)

Depois de adicionar a associação SSL com êxito, o estado do domínio será seguro: **Íntegro**. 

![Adicionar associação SSL](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Impor HTTPS
Por padrão, qualquer pessoa ainda pode acessar seu aplicativo usando HTTP, mas você pode redirecionar todas as solicitações HTTP para a porta HTTPS.

Na sua página do aplicativo, na navegação esquerda, selecione **Domínio Personalizado**. Em seguida, defina **Somente HTTPS** como *Verdadeiro*.

![Adicionar associação SSL](./media/custom-dns-tutorial/enforce-http.png)

Quando a operação estiver concluída, navegue até qualquer uma das URLs HTTPS que aponte para seu aplicativo. Observe que as URLs HTTP não funcionam.

## <a name="see-also"></a>Confira também
* [O que é o Cofre da Chave do Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importar um certificado](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Iniciar o aplicativo Spring Cloud usando a CLI do Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

