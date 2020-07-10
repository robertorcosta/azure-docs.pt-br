---
title: Hospedagem de site estático no Armazenamento do Microsoft Azure
description: O Armazenamento do Microsoft Azure agora oferece um site estático hospedand, fornecendo uma solução econômica e dimensionável para hospedar aplicativos web modernos.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/14/2020
ms.subservice: blobs
ms.openlocfilehash: ccad51d18a5e76f68633103af64e9ba6cc3f19c0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203372"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hospedagem de site estático no Armazenamento do Microsoft Azure

Você pode fornecer conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner de armazenamento denominado *$web*. Hospedar seu conteúdo no Armazenamento do Azure permite que você use arquiteturas sem servidor que incluem [Azure Functions](/azure/azure-functions/functions-overview) e outros serviços de PaaS (plataforma como serviço).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

> [!NOTE]
> Se o seu site depende do código do servidor, use [Azure App Service](/azure/app-service/overview).
Certifique-se de criar uma conta de armazenamento padrão de uso geral v2. Os sites estáticos não estão disponíveis em nenhum outro tipo de conta de armazenamento.

## <a name="setting-up-a-static-website"></a>Configuração de um site estático

A hospedagem de site estática é um recurso que você precisa habilitar na conta de armazenamento.

Para habilitar a hospedagem do site estático, selecione o nome do arquivo padrão e, opcionalmente, forneça um caminho para uma página 404 personalizada. Se um contêiner de armazenamento de Blobs chamado **$web** ainda não existir na conta, isso será criado para você. Adicione os arquivos do seu site a esse contêiner.

Para obter orientações passo a passo, confira [Hospedar site estático no Armazenamento do Azure](storage-blob-static-website-how-to.md).

![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Os arquivos no contêiner **$web** diferenciam maiúsculas de minúsculas, são atendidos por meio de solicitações de acesso anônimo e estão disponíveis somente por meio de operações de leitura.

## <a name="uploading-content"></a>Carregamento de conteúdo

Você pode usar qualquer uma dessas ferramentas para carregar conteúdo para o contêiner **$web**:

> [!div class="checklist"]
> * [CLI do Azure](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Módulo do Azure PowerShell](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Extensão do Visual Studio Code](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Exibição de conteúdo

Os usuários podem exibir o conteúdo do site de um navegador usando a URL pública do site. É possível localizar a URL usando o portal do Azure, a CLI do Azure ou o PowerShell. Confira [Localizar a URL do site](storage-blob-static-website-how-to.md#portal-find-url).

Se o servidor retornar um erro 404 e você não tiver especificado um documento de erro ao habilitar o site, uma página 404 padrão será retornada ao usuário.

> [!NOTE]
> Não há suporte para [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) com site estático.

### <a name="regional-codes"></a>Códigos regionais

A URL do seu site contém um código regional. Por exemplo, a URL `https://contosoblobaccount.z22.web.core.windows.net/` contém o código regional `z22`.

Embora esse código deva permanecer na URL, ele é apenas para uso interno, e você não precisará usar esse código de nenhuma outra maneira.

O documento de índice especificado quando você habilita a hospedagem de site estático é exibido quando os usuários abrem o site e não especificam um arquivo (por exemplo: `https://contosoblobaccount.z22.web.core.windows.net`).

### <a name="secondary-endpoints"></a>Pontos de extremidade secundários

Se você configurar a [redundância em uma região secundária](../common/storage-redundancy.md#redundancy-in-a-secondary-region), também poderá acessar o conteúdo do site usando um ponto de extremidade secundário. Como os dados são replicados para regiões secundárias de forma assíncrona, os arquivos que estão disponíveis no ponto de extremidade secundário nem sempre estão em sincronia com os arquivos disponíveis no ponto de extremidade primário.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impacto da configuração do nível de acesso público do contêiner da Web

Você pode modificar o nível de acesso público do contêiner **$web**, mas isso não tem impacto sobre o ponto de extremidade de site estático primário porque esses arquivos são servidos por meio de solicitações de acesso anônimo. Isso significa acesso público (somente leitura) a todos os arquivos.

A seguinte captura de tela mostra a configuração do nível de acesso público no portal do Azure:

![Captura de tela mostrando como definir o nível de acesso público no portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Embora o ponto de extremidade de site estático primário não seja afetado, uma alteração no nível de acesso público tem impacto sobre o ponto de extremidade de serviço blob primário.

Por exemplo, se você alterar o nível de acesso público do contêiner **$web** de **Privado (sem acesso anônimo)** para **Blob (acesso de leitura anônimo somente para blobs)** , o nível de acesso público ao ponto de extremidade do site estático primário `https://contosoblobaccount.z22.web.core.windows.net/index.html` não será alterado.

No entanto, o acesso público ao ponto de extremidade de serviço blob primário `https://contosoblobaccount.blob.core.windows.net/$web/index.html` muda de privado para público. Agora os usuários podem abrir esse arquivo usando qualquer um desses dois pontos de extremidade.

Desabilitar o acesso público em uma conta de armazenamento não afeta sites estáticos hospedados nessa conta de armazenamento. Para obter mais informações, consulte [Configurar acesso de leitura público anônimo para contêineres e blobs](anonymous-read-access-configure.md).

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapeamento de um domínio personalizado para uma URL de site estático

Você pode disponibilizar seu site estático por meio de um domínio personalizado.

É mais fácil habilitar o acesso HTTP para seu domínio personalizado, pois o Armazenamento do Azure dá suporte nativo a ele. Para habilitar o HTTPS, você precisará usar a CDN do Azure porque o Armazenamento do Azure ainda não oferece suporte nativo a HTTPS com domínios personalizados. Confira [Mapear um domínio personalizado para um ponto de extremidade do Armazenamento de Blobs do Azure](storage-custom-domain-name.md) para ter orientações passo a passo.

Se a conta de armazenamento estiver configurada para [exigir transferência segura](../common/storage-require-secure-transfer.md) via HTTPS, os usuários deverão usar o ponto de extremidade HTTPS.

> [!TIP]
> Hospede seu domínio no Azure. Para saber mais, confira [Hospedar seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Adição de cabeçalhos de HTTP

Não há como configurar cabeçalhos como parte do recurso de site estático. No entanto, você pode usar a CDN do Azure para adicionar cabeçalhos e acrescentar (ou substituir) valores de cabeçalho. Confira [Referência do mecanismo de regras Standard para a CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Se quiser usar cabeçalhos para controlar o armazenamento em cache, confira [Controlar o comportamento de cache da CDN do Azure com regras de cache](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="pricing"></a>Preços

Você pode habilitar a hospedagem de sites estáticos gratuitamente. Você é cobrado apenas pelo armazenamento de blobs que seu site utiliza e pelos custos operacionais. Para obter mais detalhes sobre preços para Armazenamento de Blob do Azure, confira [Página de Preços do Armazenamento de Blob do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Métricas

Você pode habilitar métricas em páginas de sites estáticos. Depois que você tiver habilitado as métricas, as estatísticas de tráfego nos arquivos no contêiner **$web** serão relatadas no painel de métricas.

Para habilitar as métricas em suas páginas de sites estáticos, confira [Habilitar métricas em páginas de sites estáticos](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Próximas etapas

* [Hospedar site estático no Armazenamento do Azure](storage-blob-static-website-how-to.md)
* [Mapear um domínio personalizado para um ponto de extremidade do Armazenamento de Blobs do Azure](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Serviço de Aplicativo do Azure](/azure/app-service/overview)
* [Compile seu primeiro aplicativo web sem servidor](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Hospede seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)
