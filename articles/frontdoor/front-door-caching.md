---
title: Cache de porta frontal do Azure | Microsoft Docs
description: Este artigo ajuda você a entender o comportamento da porta frontal com as regras de roteamento que habilitaram o Caching.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2020
ms.author: duau
ms.openlocfilehash: 9279b3e77147449ae0ede0cc0b76e57f130c9a44
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398024"
---
# <a name="caching-with-azure-front-door"></a>Caching com a porta frontal do Azure
O documento a seguir especifica o comportamento do Azure Front Door Service com as regras de roteamento que tem o cache habilitado. A porta frontal é uma CDN (rede de distribuição de conteúdo) moderna e, assim, com a aceleração de site dinâmica e o balanceamento de carga, ele também dá suporte a comportamentos de cache, assim como qualquer outra CDN.

## <a name="delivery-of-large-files"></a>Entrega de arquivos grandes
A porta frontal do Azure fornece arquivos grandes sem limite de tamanho de arquivo. O Front Door usa uma técnica chamada agrupamento de objeto. Quando um arquivo grande é solicitado, o Front Door recupera partes menores do arquivo a partir do back-end. Após receber uma solicitação de arquivo completo ou de intervalo de bytes, um ambiente do Front Door solicitará o arquivo do back-end em partes de 8 MB.

</br>Depois que a parte chega no ambiente do Front Door, ela é armazenada em cache e imediatamente distribuída para o usuário. Em seguida, o Front Door efetua a pré-busca da próxima parte em paralelo. Essa pré-busca garante que o conteúdo permaneça uma parte à frente do usuário, o que reduz a latência. Esse processo continua até que todo o arquivo é baixado (se solicitado), todos os intervalos de bytes estão disponíveis (se solicitado) ou o cliente encerra a conexão.

</br>Para obter mais informações sobre a solicitação de intervalo de bytes, leia [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
O Front Door armazena em cache todas as partes conforme são recebidas e, portanto, o arquivo inteiro não precisa ser armazenado no cache do Front Door. As solicitações subsequentes para o arquivo ou intervalos de bytes são disponibilizadas pela cache. Se nem todas as partes forem armazenadas em cache, a pré-busca será usada para solicitar as partes do back-end. Essa otimização depende da capacidade do servidor de origem para dar suporte a solicitações de intervalo de bytes; se o servidor de origem não dá suporte a solicitações de intervalo de bytes, essa otimização não será efetiva.

## <a name="file-compression"></a>Compactação de arquivo
O Front Door pode compactar dinamicamente conteúdo na borda, resultando em uma resposta menor e mais rápida aos clientes. Todos os arquivos são qualificados para compactação. No entanto, o arquivo deve ser um tipo MIME que seja qualificado para a lista de compactação. Atualmente, o Front Door não permite que essa lista seja alterada. A lista atual é:</br>
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- “application/json”
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

Além disso, o arquivo também deve ter entre 1 KB e 8 MB de tamanho, inclusive.

Esses perfis dão suporte às seguintes codificações de compactação:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Se uma solicitação com suporte para gzip e compactação Brotli, a compactação Brotli terá precedência.</br>
Quando uma solicitação de um ativo especificar a compactação e os resultados da solicitação em um cache forem perdidos, o Front Door realiza a compactação do ativo diretamente no servidor POP. Depois disso, o arquivo compactado será servido do cache. O item resultante é retornado com uma codificação de transferência: em partes.

## <a name="query-string-behavior"></a>Comportamento da cadeia de caracteres de consulta
Com o Front Door, é possível controlar como os arquivos são armazenados em cache para uma solicitação da Web que contenha uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares de chave-valor, no qual o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, a ordem não importa.
- **Ignorar cadeias de caracteres de consulta**: nesse modo, a porta frontal passa as cadeias de caracteres de consulta do solicitante para o back-end na primeira solicitação e armazena o ativo em cache. Todas as solicitações subsequentes para esse ativo que forem atendidas pelo ambiente do Front Door ignoram a cadeia de caracteres de consulta até que o ativo em cache expire.

- **Armazenar em cada cache URL exclusiva**: nesse modo, cada solicitação com um URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta do servidor de back-end para uma solicitação de `www.example.ashx?q=test1` é armazenada em cache no Front Door e retornada para caches subsequentes com a mesma cadeia de caracteres de consulta. Uma solicitação `www.example.ashx?q=test2` é armazenada em cache como um ativo separado com sua própria configuração de vida útil.

## <a name="cache-purge"></a>Limpeza do cache

A porta frontal armazena ativos em cache até o tempo de vida (TTL) do ativo expirar. Depois que a TTL do ativo expira, quando um cliente solicita o ativo, o ambiente de porta de front-end recupera uma nova cópia atualizada do ativo para atender à solicitação do cliente e armazenar a atualização do cache.

A prática recomendada para garantir que os usuários sempre obtenham a cópia mais recente de seus ativos é verter os ativos para cada atualização e publicá-los como novas URLs. O Front Door recuperará imediatamente os novos ativos para as próximas solicitações do cliente. Às vezes, convém limpar o conteúdo em cache de todos os nós de borda e forçá-los a recuperar novos ativos atualizados. Isso pode ocorrer devido a atualizações do aplicativo Web ou para atualizar rapidamente ativos que contenham informações incorretas.

Selecione os ativos que você deseja limpar dos nós de borda. Para limpar todos os ativos, selecione **limpar tudo**. Caso contrário, em **caminho**, insira o caminho de cada ativo que você deseja limpar.

Esses formatos têm suporte nas listas de caminhos a serem limpos:

- **Limpeza de caminho único**: Limpe ativos individuais especificando o caminho completo do ativo (sem o protocolo e o domínio), com a extensão de arquivo, por exemplo,/Pictures/strasbourg.png;
- **Limpeza de caractere curinga**: o asterisco (\*) pode ser usado como um caractere curinga. Limpe todas as pastas, subpastas e arquivos em um ponto de extremidade com/ \* no caminho ou limpe todas as subpastas e arquivos em uma pasta específica, especificando a pasta seguida por/ \* , por exemplo,/Pictures/ \* .
- **Limpeza do domínio raiz**: limpe a raiz do ponto de extremidade com "/" no caminho.

> [!NOTE]
> A **limpeza de domínios curinga**: a especificação de caminhos armazenados em cache para limpeza, conforme discutido nesta seção, não se aplica a domínios curinga que estejam associados à porta frontal. No momento, não há suporte para a limpeza direta de domínios curinga. Você pode limpar caminhos de subdomínios específicos especificando o subdomínio específico e o caminho de limpeza. Por exemplo, se minha porta frontal tiver `*.contoso.com` , posso limpar os ativos do meu subdomínio `foo.contoso.com` digitando `foo.contoso.com/path/*` . Atualmente, a especificação de nomes de host no caminho de conteúdo de limpeza é imited a subdomínios de domínios curinga, se aplicável.
>

As limpezas de cache do Front Door diferenciam maiúsculas de minúsculas. Além disso, as limpezas são independentes da cadeia de caracteres de consulta, significando que a limpeza de uma URL apagará todas as variações da cadeia de caracteres de consulta dessa URL. 

## <a name="cache-expiration"></a>Expiração do cache
A ordem de cabeçalhos a seguir é usada para determinar por quanto tempo um item será armazenado em nosso cache:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: Max-age =\<seconds>
3. Expira \<http-date>

Cabeçalhos de resposta de controle de cache que indicam que a resposta não será armazenada em cache, como Cache-Control: privado, Cache-Control: no-cache e Cache-Control: no-Store são respeitados.  Se nenhum controle de cache estiver presente, o comportamento padrão é que AFD armazenará em cache o recurso por X quantidade de tempo em que X é separado aleatoriamente entre 1 e 3 dias.

## <a name="request-headers"></a>Cabeçalhos de solicitação

Os seguintes cabeçalhos de solicitação não serão encaminhados para um back-end ao usar o cache.
- Content-Length
- Transfer-Encoding

## <a name="cache-duration"></a>Duração do cache

A duração do cache pode ser configurada no designer de porta frontal e no mecanismo de regras. A duração do cache definida no designer de frontdoor é a duração mínima do cache. Essa substituição não funcionará se o cabeçalho de controle de cache da origem tiver um TTL maior do que o valor de substituição. 

A duração do cache definida por meio do mecanismo de regras é uma substituição de cache verdadeira, o que significa que ele usará o valor de substituição, independentemente do cabeçalho de resposta da origem.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
