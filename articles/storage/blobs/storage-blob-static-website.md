---
title: Hospedagem de site estático no Armazenamento do Microsoft Azure
description: O Armazenamento do Microsoft Azure agora oferece um site estático hospedand, fornecendo uma solução econômica e dimensionável para hospedar aplicativos web modernos.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370484"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hospedagem de site estático no Armazenamento do Microsoft Azure

Você pode servir conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner de armazenamento chamado *$web*. Hospedar seu conteúdo no Azure Storage permite que você use arquiteturas sem servidor que incluem [funções do Azure](/azure/azure-functions/functions-overview) e outros serviços de plataforma como serviço (PaaS).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Se o seu site depender do código do lado do servidor, use [o Azure App Service.](/azure/app-service/overview)

## <a name="setting-up-a-static-website"></a>Configuração de um site estático

Hospedagem de sites estáticos é um recurso que você tem que habilitar na conta de armazenamento.

Para habilitar a hospedagem estática do site, selecione o nome do seu arquivo padrão e, em seguida, opcionalmente, forneça um caminho para uma página 404 personalizada. Se um recipiente de armazenamento blob chamado **$web** já não existe na conta, um será criado para você. Adicione os arquivos do seu site a este contêiner.

Para obter orientação passo a passo, consulte [Hospedar um site estático no Azure Storage](storage-blob-static-website-how-to.md).

![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Os arquivos no **contêiner $web** são sensíveis a maiúsculas e minúsculas, atendidos por meio de solicitações de acesso anônimo e estão disponíveis apenas através de operações de leitura.

## <a name="uploading-content"></a>Carregando conteúdo

Você pode usar qualquer uma dessas ferramentas para carregar conteúdo **no** $web contêiner:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Módulo do Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Explorador de armazenamento azure](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Extensão visual studio code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visualização de conteúdo

Os usuários podem visualizar o conteúdo do site a partir de um navegador usando a URL pública do site. Você pode encontrar a URL usando o portal Azure, Azure CLI ou PowerShell. Use essa tabela como um guia.

|Ferramenta| Orientação |
|----|----|
|**Portal Azure** | [Encontre a URL do site usando o portal Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Encontre a URL do site usando o Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Módulo do Azure PowerShell** | [Encontre a URL do site usando o PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

A URL do seu site contém um código regional. Por exemplo, `https://contosoblobaccount.z22.web.core.windows.net/` a `z22`URL contém código regional .

Embora esse código deva permanecer na URL, ele é apenas para uso interno, e você não terá que usar esse código de qualquer outra maneira.

O documento de índice que você especifica quando habilita a hospedagem estática do site, `https://contosoblobaccount.z22.web.core.windows.net`aparece quando os usuários abrem o site e não especificam um arquivo específico (Por exemplo: ).  

Se o servidor retornar um erro 404 e você não tiver especificado um documento de erro quando você habilitou o site, então uma página padrão 404 será devolvida ao usuário.

> [!NOTE]
> [O CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) não é suportado com site estático.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impacto da configuração do nível de acesso público do contêiner web

Você pode modificar o nível de acesso público do contêiner **$web,** mas isso não tem impacto no ponto final do site estático primário porque esses arquivos são atendidos através de solicitações de acesso anônimo. Isso significa acesso público (somente leitura) a todos os arquivos.

A captura de tela a seguir mostra a configuração do nível de acesso público no portal Azure:

![Captura de tela mostrando como definir o nível de acesso público no portal](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Embora o ponto final do site estático primário não seja afetado, uma alteração no nível de acesso público afeta o ponto final do serviço de blob principal.

Por exemplo, se você alterar o nível de acesso público do **contêiner $web** de **Privado (sem acesso anônimo)** para **Blob (acesso de leitura anônima apenas para blobs)**, então o nível de acesso público ao ponto `https://contosoblobaccount.z22.web.core.windows.net/index.html` final do site estático primário não muda.

No entanto, o acesso público ao `https://contosoblobaccount.blob.core.windows.net/$web/index.html` ponto final do serviço de blob primário muda de privado para público. Agora os usuários podem abrir esse arquivo usando qualquer um desses dois pontos finais.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapeando um domínio personalizado para uma URL de site estático

Você pode disponibilizar seu site estático através de um domínio personalizado. 

É mais fácil habilitar o acesso HTTP para o seu domínio personalizado, porque o Azure Storage o suporta nativamente. Para habilitar https, você terá que usar o Azure CDN porque o Azure Storage ainda não suporta HTTPS nativamente com domínios personalizados. consulte [Mapear um domínio personalizado para um ponto final do Azure Blob Storage](storage-custom-domain-name.md) para obter orientação passo a passo.

Se a conta de armazenamento estiver configurada para [exigir transferência segura](../common/storage-require-secure-transfer.md) em HTTPS, os usuários devem usar o ponto final HTTPS. 

> [!TIP]
> Considere hospedar seu domínio no Azure. Para obter mais informações, consulte [Hospedar seu domínio no Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Adicionando cabeçalhos HTTP

Não há como configurar cabeçalhos como parte do recurso de site estático. No entanto, você pode usar o CDN do Azure para adicionar cabeçalhos e anexar valores de cabeçalho (ou sobregravar). Consulte [A referência padrão do mecanismo de regras para CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Se você quiser usar cabeçalhos para controlar o cache, consulte [o comportamento de cache do CDN Control Azure com regras de cache](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="pricing"></a>Preços

Você pode habilitar a hospedagem de sites estáticos gratuitamente. Você é cobrado apenas pelo armazenamento blob que seu site utiliza e custos de operações. Para obter mais detalhes sobre preços para Armazenamento de Blob do Azure, confira [Página de Preços do Armazenamento de Blob do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Métricas

Você pode habilitar métricas em páginas estáticas do site. Depois de habilitar métricas, estatísticas de tráfego em arquivos no **$web** contêiner são relatadas no painel de métricas.

Para habilitar métricas em suas páginas estáticas do site, consulte [Habilitar métricas em páginas estáticas do site](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Próximas etapas

* [Hospede um site estático no Azure Storage](storage-blob-static-website-how-to.md)
* [Mapear um domínio personalizado para um ponto de extremidade do Armazenamento de Blobs do Azure](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Serviço de aplicativo do Azure](/azure/app-service/overview)
* [Compile seu primeiro aplicativo web sem servidor](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Hospedar seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)
