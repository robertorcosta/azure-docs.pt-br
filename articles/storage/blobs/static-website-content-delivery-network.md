---
title: Integrar um site estático com a CDN do Azure-armazenamento do Azure
description: Saiba como armazenar em cache o conteúdo de site estático de uma conta de armazenamento do Azure usando a CDN (rede de distribuição de conteúdo) do Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: aaf61ccbb3577036c614aa6196d2af57124550fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908547"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrar um site estático com a CDN do Azure

Você pode habilitar a [CDN (rede de distribuição de conteúdo) do Azure](../../cdn/cdn-overview.md) para armazenar em cache o conteúdo de um [site estático](storage-blob-static-website.md) hospedado em uma conta de armazenamento do Azure. Você pode usar a CDN do Azure para configurar o ponto de extremidade de domínio personalizado para seu site estático, provisionar certificados SSL personalizados e configurar regras de reescrita personalizadas. Configurar a CDN do Azure resulta em encargos adicionais, mas oferece baixas latências consistentes a seu site em qualquer lugar do mundo. A CDN do Azure também fornece criptografia SSL com o próprio certificado. 

Para saber mais sobre os preços da CDN do Azure, confira [Azure CDN pricing](https://azure.microsoft.com/pricing/details/cdn/) (Preços da CDN do Azure).

## <a name="enable-azure-cdn-for-your-static-website"></a>Habilitar a CDN do Azure para seu site estático

Você pode habilitar a CDN do Azure para seu site estático diretamente da sua conta de armazenamento. Se você deseja especificar as configurações avançadas para o ponto de extremidade CDN, como a [otimização do download de grandes arquivos](../../cdn/cdn-optimization-overview.md#large-file-download), use a [extensão da CDN do Azure](../../cdn/cdn-create-new-endpoint.md) para criar um perfil ou um ponto de extremidade CDN.

1. Localize sua conta de armazenamento no portal do Azure e exiba a visão geral da conta.

2. Selecione **CDN do Azure** no menu **Serviço Blob** para configurar a CDN do Azure.

    A página **CDN do Azure** é exibida.

    ![Criar ponto de extremidade da CDN](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. Na seção **Perfil CDN**, especifique um perfil CDN novo ou existente. 

4. Especifique um tipo de preço para o ponto de extremidade CDN. Para saber mais sobre preços, confira [preços de rede de distribuição de conteúdo](https://azure.microsoft.com/pricing/details/cdn/). Para obter mais informações sobre os recursos disponíveis em cada camada, consulte [comparar recursos do produto CDN do Azure](../../cdn/cdn-features.md).

5. No campo **Nome do ponto de extremidade CDN**, especifique um nome para o ponto de extremidade CDN. O ponto de extremidade CDN precisa ser exclusivo no Azure.

6. Especifique o ponto de extremidade do site estático no campo **Nome do host de origem**. 

   Para encontrar o ponto de extremidade do site estático, navegue até as configurações de **Site estático** de sua conta de armazenamento.  Copie o ponto de extremidade primário e cole-o na configuração da CDN.

   > [!IMPORTANT]
   > Certifique-se de remover o identificador de protocolo (*por exemplo*, HTTPS) e a barra à direita na URL. Por exemplo, se o ponto de extremidade de site estático for `https://mystorageaccount.z5.web.core.windows.net/`, você deverá especificar `mystorageaccount.z5.web.core.windows.net` no campo **nome de host de origem** .

   A seguinte imagem mostra uma configuração de ponto de extremidade de exemplo:

   ![Captura de tela mostrando uma configuração de ponto de extremidade CDN de exemplo](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. Selecione **criar**e aguarde sua propagação. Depois que o ponto de extremidade é criado, ele aparece na lista de pontos de extremidade.

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

## <a name="next-steps"></a>Próximos passos

Adicional Adicione um domínio personalizado ao ponto de extremidade da CDN do Azure. Consulte [tutorial: adicionar um domínio personalizado ao seu ponto de extremidade da CDN do Azure](../../cdn/cdn-map-content-to-custom-domain.md).
