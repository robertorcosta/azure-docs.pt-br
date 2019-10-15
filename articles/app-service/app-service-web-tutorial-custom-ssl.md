---
title: Carregar e associar um certificado SSL – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como associar um certificado SSL personalizado a seu aplicativo Web, back-end do aplicativo móvel ou aplicativo de API no Serviço de Aplicativo do Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c5095bc8c274ef0985b00459b0d088371ab24d88
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177040"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Tutorial: Carregar e associar certificados SSL ao Serviço de Aplicativo do Azure

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como proteger um domínio personalizado no Serviço de Aplicativo com um certificado adquirido de uma autoridade de certificação confiável. Também mostra como carregar certificados privados e públicos necessários para seu aplicativo. Ao terminar, você poderá acessar o aplicativo no ponto de extremidade HTTPS do seu domínio DNS personalizado.

![Aplicativo Web com certificado SSL personalizado](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Atualizar o tipo de preço do aplicativo
> * Proteger um domínio personalizado com um certificado
> * Carregar um certificado privado
> * Carregar um certificado público
> * Renovar certificados
> * Impor HTTPS
> * Impor o TLS 1.1/1.2
> * Automatizar o gerenciamento de TLS com scripts

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/)
- [Mapear um nome DNS personalizado para o aplicativo do Serviço de Aplicativo](app-service-web-tutorial-custom-domain.md) (se um domínio personalizado estiver sendo protegido)
- Adquira um certificado de uma autoridade de certificado confiável
- Tenha a chave privada usada para assinar a solicitação de certificado (para certificados privados)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Prepare um certificado privado

Para proteger um domínio, o certificado precisa atender a todos os seguintes requisitos:

* Configurado para a autenticação de servidor
* Assinado por uma autoridade de certificado confiável
* Exportado como um arquivo PFX protegido por senha
* Conter chave privada com pelo menos 2.048 bits de extensão
* Conter todos os certificados intermediários na cadeia de certificados

> [!TIP]
> Caso você precise obter um certificado SSL personalizado, obtenha-o diretamente no portal do Azure e importe-o para o aplicativo. Siga o [tutorial de Certificados do Serviço de Aplicativo](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> Os **certificados ECC (Criptografia de Curva Elíptica)** podem funcionar com o Serviço de Aplicativo, mas não são abordados neste artigo. Trabalhe com sua autoridade de certificação seguindo exatamente estas etapas para criar certificados ECC.

Depois de obter um certificado do provedor de certificados, siga as etapas desta seção para prepará-lo para o Serviço de Aplicativo.

### <a name="merge-intermediate-certificates"></a>Mesclar certificados intermediários

Se a autoridade de certificação fornecer vários certificados na cadeia de certificados, você precisará mesclar os certificados na ordem.

Para fazer isso, abra cada certificado recebido em um editor de texto.

Crie um arquivo para o certificado mesclado, chamado _mergedcertificate.crt_. Em um editor de texto, copie o conteúdo de cada certificado para esse arquivo. A ordem de seus certificados deve seguir a ordem na cadeia de certificados, começando com o seu certificado e terminando com o certificado raiz. Ela se parece com o seguinte exemplo:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o certificado SSL mesclado com a chave privada com a qual a solicitação de certificado foi gerada.

Se você gerou a solicitação de certificado usando o OpenSSL, isso significa que você criou um arquivo de chave privada. Para exportar o certificado para PFX, execute o comando a seguir. Substitua os espaços reservados _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ pelos caminhos para a sua chave privada e o arquivo de certificado mesclado.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando solicitado, defina uma senha de exportação. Você usará essa senha quando carregar o certificado SSL para o Serviço de Aplicativo posteriormente.

Se você usou o IIS ou o _Certreq.exe_ para gerar a solicitação de certificado, instale o certificado no computador local e, em seguida, [exporte o certificado para PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

Agora você está pronto para carregar o certificado no Serviço de Aplicativo.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Proteger um domínio personalizado

> [!TIP]
> Se precisar obter um certificado SSL personalizado, você poderá obtê-lo diretamente no portal do Azure e associá-lo ao seu aplicativo. Siga o [tutorial de Certificados do Serviço de Aplicativo](web-sites-purchase-ssl-web-site.md).

Para proteger um [domínio personalizado](app-service-web-tutorial-custom-domain.md) com um certificado de terceiros, você carrega o [certificado privado preparado](#prepare-a-private-certificate) e, em seguida, associa-o ao domínio personalizado, mas o Serviço de Aplicativo simplifica o processo para você. Execute as seguintes etapas:

Clique em **Domínios personalizados** no painel de navegação à esquerda do aplicativo e, em seguida, clique em **Adicionar associação** para o domínio que deseja proteger. Se a opção **Adicionar associação** não for exibida para um domínio, isso indicará que ele já está seguro e deve ter um estado de SSL igual a **Seguro**.

![Adicionar uma associação ao domínio](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Clique em **Carregar Certificado**.

Em **Arquivo de Certificado PFX**, selecione o arquivo PFX. Em **Senha do certificado**, digite a senha que você criou ao exportar o arquivo PFX.

Clique em **Carregar**.

![Carregar o certificado para o domínio](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Aguarde até o Azure carregar o certificado e iniciar a caixa de diálogo de associações SSL.

Na caixa de diálogo de associações SSL, selecione o certificado carregado e o tipo de SSL e, em seguida, clique em **Adicionar associação**.

> [!NOTE]
> Há suporte para os seguintes tipos de SSL:
>
> - **[SSL baseado em SNI](https://en.wikipedia.org/wiki/Server_Name_Indication)** – várias associações SSL baseadas em SNI podem ser adicionadas. Esta opção permite que vários certificados SSL protejam vários domínios no mesmo endereço IP. Navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) dão suporte ao SNI (encontre informações de suporte ao navegador mais abrangentes em [Indicação de Nome de Servidor](https://wikipedia.org/wiki/Server_Name_Indication)).
> - **SSL baseado em IP** – apenas uma associação SSL baseada em IP pode ser adicionada. Esta opção permite apenas um certificado SSL para proteger um endereço IP público dedicado. Para proteger vários domínios, você deve protegê-los todos usando o mesmo certificado SSL. Essa é a opção tradicional para associação de SSL.

![Associar o SSL ao domínio](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

O estado de SSL do domínio agora deverá ser alterado para **Seguro**.

![Domínio protegido](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> Um estado **Seguro** nos **Domínios personalizados** significa que ele é protegido com um certificado, mas o Serviço de Aplicativo não verifica se o certificado é autoassinado ou expirou, por exemplo, o que também pode fazer com que os navegadores mostrem um erro ou um aviso.

## <a name="remap-a-record-for-ip-ssl"></a>Remapear um registro para IP SSL

Caso não use SSL com base em IP no aplicativo, pule para [Testar o HTTPS para o domínio personalizado](#test).

Por padrão, o aplicativo usa um endereço IP público compartilhado. Quando você associa um certificado com SSL baseado em IP, o Serviço de Aplicativo cria um novo endereço IP dedicado para o aplicativo.

Se você mapeou um registro A para o aplicativo, atualize o registro do domínio com esse novo endereço IP dedicado.

A página **Domínio personalizado** de seu aplicativo é atualizada com o novo endereço IP dedicado. [Copie esse endereço IP](app-service-web-tutorial-custom-domain.md#info) e, em seguida, [remapeie o registro](app-service-web-tutorial-custom-domain.md#map-an-a-record) para esse novo endereço IP.

<a name="test"></a>

## <a name="test-https"></a>Testar HTTPS

Agora, tudo o que resta fazer é certificar-se de que o HTTPS funcione com seu domínio personalizado. Em vários navegadores, navegue até `https://<your.custom.domain>` para ver se ele leva até seu aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Caso seu aplicativo retorne erros de validação de certificado, você provavelmente está usando um certificado autoassinado.
>
> Se não for o caso, talvez você tenha deixado de fora certificados intermediários quando exportou o certificado para o arquivo PFX.

## <a name="renew-certificates"></a>Renovar certificados

Seu endereço IP de entrada pode ser alterado ao excluir uma associação, mesmo se essa associação for baseada em IP. Isso é especialmente importante quando você renova um certificado que já está em uma associação com base em IP. Para evitar uma alteração no endereço IP do seu aplicativo, siga estas etapas pela ordem:

1. Carregar o novo certificado.
2. Associe o novo certificado para o domínio personalizado que você deseja sem excluir o antigo. Essa ação substitui a associação em vez de remover a antiga.
3. Exclua o certificado antigo. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Impor HTTPS

Por padrão, qualquer pessoa ainda pode acessar seu aplicativo usando HTTP. Você pode redirecionar todas as solicitações HTTP para a porta HTTPS.

Na página do seu aplicativo, na navegação esquerda, selecione **Configurações SSL**. Depois, em **HTTPS somente**, selecione **Ligado**.

![Impor HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Quando a operação estiver concluída, navegue até qualquer uma das URLs HTTP que aponte para seu aplicativo. Por exemplo:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Impor as versões do TLS

Seu aplicativo permite o [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 por padrão, que é o nível TLS recomendado segundo os padrões do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões diferentes do TLS, execute estas etapas:

Na página do seu aplicativo, na navegação esquerda, selecione **Configurações SSL**. Depois, em **Versão do TLS**, selecione a versão mínima desejada do TLS. Essa configuração controla somente as chamadas de entrada. 

![Impor o TLS 1.1 ou 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Após a conclusão da operação, seu aplicativo rejeitará todas as conexões com versões inferiores do TLS.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar associações SSL para seu aplicativo com scripts, usando a [CLI do Azure](/cli/azure/install-azure-cli) ou o [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando a seguir carrega um arquivo PFX exportado e obtém a impressão digital.

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

O comando a seguir adiciona uma associação de SSL baseado em SNI usando a impressão digital do comando anterior.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

O comando a seguir força o aplicativo a usar https.

```azurecli-interactive
az webapp update \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --https-only true
```

O comando a seguir impõe a versão mínima do TLS como 1.2.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando a seguir carrega um arquivo PFX exportado e adiciona uma associação de SSL baseado em SNI.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Usar certificados no código

Se o aplicativo precisar se conectar a recursos remotos e o recurso remoto exigir autenticação de certificado, você poderá carregar certificados privados ou públicos no aplicativo. Você não precisa associar esses certificados a qualquer domínio personalizado no aplicativo. Para obter mais informações, consulte [Usar um certificado SSL no código do aplicativo no Serviço de Aplicativo do Azure](app-service-web-ssl-cert-load.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Atualizar o tipo de preço do aplicativo
> * Associar seu certificado personalizado ao Serviço de Aplicativo
> * Renovar certificados
> * Impor HTTPS
> * Impor o TLS 1.1/1.2
> * Automatizar o gerenciamento de TLS com scripts

Vá para o próximo tutorial para saber como usar a Rede de Distribuição de Conteúdo do Azure.

> [!div class="nextstepaction"]
> [Adicionar uma CDN (Rede de Distribuição de Conteúdo) a um Serviço de Aplicativo do Azure](../cdn/cdn-add-to-web-app.md)

Para obter mais informações, consulte [Usar um certificado SSL no código do aplicativo no Serviço de Aplicativo do Azure](app-service-web-ssl-cert-load.md).
