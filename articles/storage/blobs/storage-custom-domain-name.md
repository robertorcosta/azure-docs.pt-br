---
title: Mapear um domínio personalizado para um ponto de extremidade do Armazenamento de Blobs do Azure
titleSuffix: Azure Storage
description: Mapeie um domínio personalizado para um Blob Storage ou um ponto final da Web em uma conta de armazenamento Do Zure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370467"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mapear um domínio personalizado para um ponto de extremidade do Armazenamento de Blobs do Azure

Você pode mapear um domínio personalizado para um ponto final de serviço blob ou um ponto final [de site estático.](storage-blob-static-website.md) 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Este mapeamento funciona apenas para subdomínios (por exemplo: `www.contoso.com`). Se você quiser que seu ponto final da Web `contoso.com`esteja disponível no domínio raiz (por exemplo: ), então você terá que usar o CDN do Azure. Para obter orientação, consulte o Mapa de um domínio personalizado com a seção [habilitada HTTPS](#enable-https) deste artigo. Como você vai à seção deste artigo para habilitar o domínio raiz do seu domínio personalizado, a etapa dentro dessa seção para habilitar https é opcional. 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mapeie um domínio personalizado com apenas HTTP ativado

Essa abordagem é mais fácil, mas permite apenas o acesso HTTP. Se a conta de armazenamento estiver configurada para [exigir transferência segura](../common/storage-require-secure-transfer.md) em HTTPS, então você deve habilitar o acesso HTTPS para o seu domínio personalizado. 

Para habilitar o acesso HTTPS, consulte o Mapa de um domínio personalizado com a seção [habilitada HTTPS](#enable-https) deste artigo. 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>Mapeie um domínio personalizado

> [!IMPORTANT]
> Seu domínio personalizado ficará brevemente indisponível para os usuários enquanto você concluir a configuração. Se o seu domínio atualmente suportar um aplicativo com um contrato de nível de serviço (SLA) que exija zero tempo de inatividade, siga as etapas do Mapa uma seção personalizada com zero tempo de [inatividade](#zero-down-time) deste artigo para garantir que os usuários possam acessar seu domínio enquanto o mapeamento de DNS ocorre.

Se você não estiver preocupado que o domínio esteja brevemente indisponível para seus usuários, siga essas etapas.

:heavy_check_mark: Passo 1: Obtenha o nome de host do seu ponto final de armazenamento.

:heavy_check_mark: Passo 2: Crie um registro de nome canônico (CNAME) com seu provedor de domínio.

:heavy_check_mark: Passo 3: Registre o domínio personalizado com o Azure. 

:heavy_check_mark: Passo 4: Teste seu domínio personalizado.

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passo 1: Obtenha o nome de host do seu ponto final de armazenamento 

O nome do host é a URL do ponto final de armazenamento sem o identificador de protocolo e a barra de arrasto. 

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menu, em **Configurações,** selecione **Propriedades**.  

3. Copie o valor do ponto final do **serviço de blob primário** ou do ponto final do site **estático primário** para um arquivo de texto. 

4. Remova o identificador de protocolo *(por exemplo,* HTTPS) e a barra de arrasto dessa seqüência. A tabela a seguir contém exemplos.

   | Tipo de ponto final |  endpoint | nome de host |
   |------------|-----------------|-------------------|
   |blob serviço  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |site estático  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Reserve este valor para depois.

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Passo 2: Crie um registro de nome canônico (CNAME) com seu provedor de domínio

Crie um registro CNAME para apontar para o nome do host. Um registro CNAME é um tipo de registro DNS que mapeia um nome de domínio de origem para um nome de domínio de destino.

1. Faça login no site do seu registrador de domínioe, em seguida, vá para a página para gerenciar a configuração de DNS.

   Você pode encontrar a página em uma seção denominada **Domain Name**, **DNS** ou **Name Server Management**.

2. Encontre a seção para gerenciar registros CNAME. 

   Talvez seja necessário acessar uma página de configurações avançadas e procurar **CNAME**, **Alias** ou **Subdomínios**.

3. Crie um registro CNAME. Como parte desse registro, forneça os seguintes itens: 

   - O alias de `www` subdomínio, tais como ou `photos`. O subdomínio é necessário, os domínios raiz não são suportados. 
      
   - O nome de host que você obteve na [seção Obter o nome de host da sua](#endpoint) seção ponto final de armazenamento no início deste artigo. 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Passo 3: Registre seu domínio personalizado com o Azure

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menu, em **Serviço de Blob**, selecione **Domínio personalizado**.  

   ![opção de domínio personalizado](./media/storage-custom-domain-name/custom-domain-button.png "domínio personalizado")

   O painel **Domínio personalizado** é aberto.

3. Na caixa de texto **nome de domínio,** digite o nome do seu domínio personalizado, incluindo o subdomínio  
   
   Por exemplo, se o seu domínio estiver *contoso.com* e `www.contoso.com`o alias do subdomínio for *www,* digite . Se o subdomínio for `photos.contoso.com` *fotos,* digite .

4. Para registrar o domínio personalizado, escolha o botão **Salvar.**

   Depois que o registro CNAME tiver se propagado através do DNS (Domain Name Servers, servidor de nomes de domínio) e se seus usuários tiverem as permissões apropriadas, eles podem visualizar dados blob usando o domínio personalizado.

#### <a name="step-4-test-your-custom-domain"></a>Passo 4: Teste seu domínio personalizado

Para confirmar que seu domínio personalizado está mapeado para o ponto de extremidade do serviço de blob, crie um blob em um contêiner público em sua conta de armazenamento. Em seguida, em um navegador da Web, acesse o blob usando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para acessar um formulário da web no contêiner *myforms* no subdomínio personalizado *photos.contoso.com*, você pode usar o seguinte URI: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mapeie um domínio personalizado com zero tempo de inatividade

> [!NOTE]
> Se você não estiver preocupado que o domínio esteja brevemente indisponível para seus usuários, então considere seguir as etapas do Mapa uma seção [de domínio personalizado](#map-a-domain) deste artigo. É uma abordagem mais simples com menos passos.  

Se o seu domínio atualmente suportar um aplicativo com um contrato de nível de serviço (SLA) que exija zero tempo de inatividade, siga essas etapas para garantir que os usuários possam acessar seu domínio enquanto o mapeamento do DNS ocorre. 

:heavy_check_mark: Passo 1: Obtenha o nome de host do seu ponto final de armazenamento.

:heavy_check_mark: Passo 2: Crie um registro de nome canônico intermediário (CNAME) com seu provedor de domínio.

:heavy_check_mark: Passo 3: Pré-registre o domínio personalizado com o Azure.

:heavy_check_mark: Passo 4: Crie um registro CNAME com seu provedor de domínio.

:heavy_check_mark: Passo 5: Teste seu domínio personalizado.

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passo 1: Obtenha o nome de host do seu ponto final de armazenamento 

O nome do host é a URL do ponto final de armazenamento sem o identificador de protocolo e a barra de arrasto. 

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menu, em **Configurações,** selecione **Propriedades**.  

3. Copie o valor do ponto final do **serviço de blob primário** ou do ponto final do site **estático primário** para um arquivo de texto. 

4. Remova o identificador de protocolo *(por exemplo,* HTTPS) e a barra de arrasto dessa seqüência. A tabela a seguir contém exemplos.

   | Tipo de ponto final |  endpoint | nome de host |
   |------------|-----------------|-------------------|
   |blob serviço  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |site estático  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Reserve este valor para depois.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Passo 2: Crie um registro de nome canônico intermediário (CNAME) com seu provedor de domínio

Crie um registro CNAME temporário para apontar para o nome do host. Um registro CNAME é um tipo de registro DNS que mapeia um nome de domínio de origem para um nome de domínio de destino.

1. Faça login no site do seu registrador de domínioe, em seguida, vá para a página para gerenciar a configuração de DNS.

   Você pode encontrar a página em uma seção denominada **Domain Name**, **DNS** ou **Name Server Management**.

2. Encontre a seção para gerenciar registros CNAME. 

   Talvez seja necessário acessar uma página de configurações avançadas e procurar **CNAME**, **Alias** ou **Subdomínios**.

3. Crie um registro CNAME. Como parte desse registro, forneça os seguintes itens: 

   - O alias de `www` subdomínio, tais como ou `photos`. O subdomínio é necessário, os domínios raiz não são suportados.

     Adicione `asverify` o subdomínio ao alias. Por exemplo: `asverify.www` ou `asverify.photos`.
       
   - O nome de host que você obteve na [seção Obter o nome de host da sua](#endpoint) seção ponto final de armazenamento no início deste artigo. 

     Adicione o `asverify` subdomínio ao nome do host. Por exemplo: `asverify.mystorageaccount.blob.core.windows.net`.

4. Para registrar o domínio personalizado, escolha o botão **Salvar.**

   Se o registro for bem-sucedido, o portal notificará que sua conta de armazenamento foi atualizada com sucesso. Seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo encaminhado para sua conta de armazenamento.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Passo 3: Pré-registre seu domínio personalizado com o Azure

Quando você pré-registra seu domínio personalizado com o Azure, você permite que o Azure reconheça seu domínio personalizado sem ter que modificar o registro DNS para o domínio. Dessa forma, quando você modificar o registro DNS para o domínio, ele será mapeado para o ponto final blob sem tempo de inatividade.

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menu, em **Serviço de Blob**, selecione **Domínio personalizado**.  

   ![opção de domínio personalizado](./media/storage-custom-domain-name/custom-domain-button.png "domínio personalizado")

   O painel **Domínio personalizado** é aberto.

3. Na caixa de texto **nome de domínio,** digite o nome do seu domínio personalizado, incluindo o subdomínio  
   
   Por exemplo, se o seu domínio estiver *contoso.com* e `www.contoso.com`o alias do subdomínio for *www,* digite . Se o subdomínio for `photos.contoso.com` *fotos,* digite .

4. Selecione a caixa de seleção **Usar validação de CNAME indireta**.

5. Para registrar o domínio personalizado, escolha o botão **Salvar.**
  
   Depois que o registro CNAME tiver se propagado através do DNS (Domain Name Servers, servidor de nomes de domínio) e se seus usuários tiverem as permissões apropriadas, eles podem visualizar dados blob usando o domínio personalizado.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Passo 4: Crie um registro CNAME com seu provedor de domínio

Crie um registro CNAME temporário para apontar para o nome do host.

1. Faça login no site do seu registrador de domínioe, em seguida, vá para a página para gerenciar a configuração de DNS.

   Você pode encontrar a página em uma seção denominada **Domain Name**, **DNS** ou **Name Server Management**.

2. Encontre a seção para gerenciar registros CNAME. 

   Talvez seja necessário acessar uma página de configurações avançadas e procurar **CNAME**, **Alias** ou **Subdomínios**.

3. Crie um registro CNAME. Como parte desse registro, forneça os seguintes itens: 

   - O alias de `www` subdomínio, tais como ou `photos`. O subdomínio é necessário, os domínios raiz não são suportados.
      
   - O nome de host que você obteve na [seção Obter o nome de host da sua](#endpoint-2) seção ponto final de armazenamento no início deste artigo. 

#### <a name="step-5-test-your-custom-domain"></a>Passo 5: Teste seu domínio personalizado

Para confirmar que seu domínio personalizado está mapeado para o ponto de extremidade do serviço de blob, crie um blob em um contêiner público em sua conta de armazenamento. Em seguida, em um navegador da Web, acesse o blob usando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para acessar um formulário da web no contêiner *myforms* no subdomínio personalizado *photos.contoso.com*, você pode usar o seguinte URI: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Remova um mapeamento de domínio personalizado

Para remover um mapeamento de domínio personalizado, desregistre o domínio personalizado. Use um dos procedimentos a seguir.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover a configuração do domínio personalizado, faça o seguinte:

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

2. No painel de menu, em **Serviço de Blob**, selecione **Domínio personalizado**.  
   O painel **Domínio personalizado** é aberto.

3. Limpe o conteúdo da caixa de texto que contém seu nome de domínio personalizado.

4. Selecione o botão **Salvar**.

Depois que o domínio personalizado for removido com sucesso, você verá uma notificação do portal de que sua conta de armazenamento foi atualizada com sucesso

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para remover um registro de domínio personalizado, use o comando da CLI [az storage account](https://docs.microsoft.com/cli/azure/storage/account) e, em seguida, especifique uma cadeia vazia (`""`) para o valor do argumento `--custom-domain`.

* Formato do comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Exemplo de comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para remover um registro de domínio personalizado, use o cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) do PowerShell e especifique uma sequência vazia (`""`) para o valor do argumento `-CustomDomainName`.

* Formato do comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemplo de comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>Mapeie um domínio personalizado com HTTPS ativado

Essa abordagem envolve mais etapas, mas permite o acesso HTTPS. 

Se você não precisar que os usuários acessem seu conteúdo blob ou web usando HTTPS, então consulte o Mapa um domínio personalizado com apenas a seção [habilitada HTTP](#enable-http) deste artigo. 

Para mapear um domínio personalizado e habilitar o acesso HTTPS, faça o seguinte:

1. Habilite [o CDN do Azure](../../cdn/cdn-overview.md) no seu blob ou no ponto final da Web. 

   Para obter um ponto final de armazenamento blob, consulte [Integrar uma conta de armazenamento Azure com cdn do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Para obter um ponto final de site estático, consulte [Integrar um site estático com CDN do Azure](static-website-content-delivery-network.md).

2. [Mapeie conteúdo da CDN do Azure para um domínio personalizado](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Habilite https em um domínio personalizado do CDN do Azure](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Quando você atualizar seu site estático, certifique-se de limpar conteúdo armazenado em cache nos servidores de borda do CDN, limpando o ponto final do CDN. Para obter mais informações, confira [Como limpar um ponto de extremidade do CDN do Azure](../../cdn/cdn-purge-endpoint.md).

4. (Opcional) Reveja as seguintes orientações:

   * [Tokens de assinatura de acesso compartilhado (SAS) com CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [Redirecionamento HTTP-para-HTTPS com CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

   * [Preços e faturamento ao usar o Blob Storage com CDN Do Zure](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre hospedagem de sites estáticos no armazenamento Azure Blob](storage-blob-static-website.md)
