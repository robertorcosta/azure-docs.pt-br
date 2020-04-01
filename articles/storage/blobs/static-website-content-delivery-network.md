---
title: Integre um site estático com O Azure CDN - Armazenamento Azure
description: Aprenda a armazenar conteúdo estático do site de uma conta do Azure Storage usando cdn (Azure Content Delivery Network, rede de entrega de conteúdo do Azure).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: 8eeff5187d27cb75b9e55eba8311dede8970bc4a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435229"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integre um site estático com o Azure CDN

Você pode habilitar [o CDN (Azure Content Delivery Network, rede de entrega de conteúdo de conteúdo)](../../cdn/cdn-overview.md) para armazenar conteúdo em cache de um [site estático](storage-blob-static-website.md) hospedado em uma conta de armazenamento Do Zure. Você pode usar o CDN do Azure para configurar o ponto final de domínio personalizado para o seu site estático, provisionar certificados TLS/SSL personalizados e configurar regras de reescrita personalizadas. Configurar a CDN do Azure resulta em encargos adicionais, mas oferece baixas latências consistentes a seu site em qualquer lugar do mundo. O Azure CDN também fornece criptografia TLS com seu próprio certificado. 

Para saber mais sobre os preços da CDN do Azure, confira [Azure CDN pricing](https://azure.microsoft.com/pricing/details/cdn/) (Preços da CDN do Azure).

## <a name="enable-azure-cdn-for-your-static-website"></a>Habilite o CDN do Azure para o seu site estático

Você pode habilitar o CDN do Azure para seu site estático diretamente da sua conta de armazenamento. Se você deseja especificar as configurações avançadas para o ponto de extremidade CDN, como a [otimização do download de grandes arquivos](../../cdn/cdn-optimization-overview.md#large-file-download), use a [extensão da CDN do Azure](../../cdn/cdn-create-new-endpoint.md) para criar um perfil ou um ponto de extremidade CDN.

1. Localize sua conta de armazenamento no portal do Azure e exiba a visão geral da conta.

2. Selecione **CDN do Azure** no menu **Serviço Blob** para configurar a CDN do Azure.

    A página **CDN do Azure** é exibida.

    ![Criar ponto de extremidade da CDN](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. Na seção **Perfil CDN**, especifique um perfil CDN novo ou existente. 

4. Especifique um tipo de preço para o ponto de extremidade CDN. Para saber mais sobre preços, consulte [os preços da Rede de Entrega de Conteúdo](https://azure.microsoft.com/pricing/details/cdn/). Para obter mais informações sobre os recursos disponíveis em cada camada, consulte [os recursos do produto Compare Azure CDN](../../cdn/cdn-features.md).

5. No campo **Nome do ponto de extremidade CDN**, especifique um nome para o ponto de extremidade CDN. O ponto de extremidade CDN precisa ser exclusivo no Azure.

6. Especifique o ponto de extremidade do site estático no campo **Nome do host de origem**. 

   Para encontrar o ponto de extremidade do site estático, navegue até as configurações de **Site estático** de sua conta de armazenamento.  Copie o ponto final principal e cole-o na configuração do CDN.

   > [!IMPORTANT]
   > Certifique-se de remover o identificador de protocolo *(por exemplo,* HTTPS) e a barra de arrasto na URL. Por exemplo, se o ponto `https://mystorageaccount.z5.web.core.windows.net/`final do `mystorageaccount.z5.web.core.windows.net` site estático for, então você especificaria no campo **Nome de hostOrigin.**

   A seguinte imagem mostra uma configuração de ponto de extremidade de exemplo:

   ![Captura de tela mostrando uma configuração de ponto de extremidade CDN de exemplo](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. Selecione **Criar**e, em seguida, aguarde que ele se propague. Depois que o ponto de extremidade é criado, ele aparece na lista de pontos de extremidade.

8. Para verificar se o ponto de extremidade CDN está configurado corretamente, clique no ponto de extremidade para navegar até as configurações. Na visão geral da CDN de sua conta de armazenamento, localize o nome do host do ponto de extremidade e navegue até o ponto de extremidade, conforme mostrado na imagem a seguir. O formato do ponto de extremidade CDN será semelhante a `https://staticwebsitesamples.azureedge.net`.

    ![Captura de tela mostrando uma visão geral do ponto de extremidade CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

9. Após a conclusão da propagação do ponto de extremidade CDN, navegar até o ponto de extremidade CDN exibirá o conteúdo do arquivo index.html que você carregou anteriormente no site estático.

10. Para examinar as configurações de origem do ponto de extremidade CDN, navegue até **Origem** na seção **Configurações** do ponto de extremidade CDN. Você verá que o campo **Tipo de origem** é definido como *Origem Personalizada* e que o campo **Nome do host de origem** exibe o ponto de extremidade do site estático.

    ![Captura de tela mostrando as Configurações de origem para o ponto de extremidade CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Remover o conteúdo da CDN do Azure

Se não desejar mais armazenar em cache um objeto na CDN do Azure, você poderá executar uma das seguintes etapas:

* Torne o contêiner particular em vez de público. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](storage-manage-access-to-resources.md).
* Desabilite ou exclua o ponto de extremidade CDN usando o Portal do Azure.
* Modifique seu serviço hospedado para não responder a solicitações do objeto.

Um objeto que já está armazenado em cache na CDN do Azure permanecerá em cache até que o período de vida útil do objeto expire ou até que o ponto de extremidade seja [limpo](../../cdn/cdn-purge-endpoint.md). Quando o período de vida expira, a CDN do Azure determina se o ponto de extremidade CDN ainda é válido e se o objeto ainda pode ser acessado anonimamente. Se não for, o objeto não estará mais armazenado em cache.

## <a name="next-steps"></a>Próximas etapas

(Opcional) Adicione um domínio personalizado ao ponto final do CDN do Azure. Veja [tutorial: Adicione um domínio personalizado ao seu ponto final do CDN do Azure](../../cdn/cdn-map-content-to-custom-domain.md).
