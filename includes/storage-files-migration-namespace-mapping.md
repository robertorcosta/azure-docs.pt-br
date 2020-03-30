---
title: Mapeie uma estrutura de pasta para uma topologia do Azure File Sync
description: Mapeando uma estrutura de arquivos e pastas existentes para compartilhamentos de arquivos do Azure para uso com o Azure File Sync. Um bloco de texto comum, compartilhado entre os docs de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124715"
---
Nesta etapa, você está avaliando quantos compartilhamentos de arquivos do Azure você precisa. Um único Servidor Windows (ou cluster) pode sincronizar até 30 compartilhamentos de arquivos Do Zure.

Você pode ter mais pastas em seus volumes que você compartilha atualmente localmente como smb compartilha para seus usuários e aplicativos. O mais fácil seria imaginar um compartilhamento no local para mapear 1:1 para um compartilhamento de arquivos Do Zure. Se você tiver um número pequeno o suficiente, abaixo de 30 para um único Servidor Windows, então um mapeamento 1:1 é recomendado.

Se você tiver mais ações do que 30, muitas vezes é desnecessário mapear um compartilhamento no local 1:1 para um compartilhamento de arquivos Azure.
Considere as seguintes opções:

#### <a name="share-grouping"></a>Agrupamento de ações

Por exemplo, se o seu departamento de RH tiver um total de 15 ações, então você pode considerar armazenar todos os dados de RH em um único compartilhamento de arquivos Azure. Armazenar várias ações no local em um compartilhamento de arquivos do Azure não impede que você crie os shares usuais de 15 SMB no seu Servidor Windows local. Isso significa apenas que você organiza as pastas raiz dessas 15 ações como subpastas em uma pasta comum. Em seguida, você sincroniza esta pasta comum com um compartilhamento de arquivos Azure. Dessa forma, apenas uma única participação de arquivo Azure na nuvem é necessária para esse grupo de ações no local.

#### <a name="volume-sync"></a>Sincronização de volume

O Azure File Sync suporta sincronizar a raiz de um volume para um compartilhamento de arquivos Do Zure.
Se você sincronizar a pasta raiz, todas as subpastas e arquivos acabarão no mesmo compartilhamento de arquivos do Azure.

Sincronizar a raiz do volume nem sempre será a melhor resposta. Há benefícios na sincronização de vários locais, fazendo isso ajuda a manter o número de itens menor por escopo de sincronização. Configurar o Azure File Sync com um número menor de itens não é apenas benéfico para sincronização de arquivos. Um número menor de itens também beneficia outros cenários como:

* restauração do lado da nuvem a partir de um instantâneo de compartilhamento de arquivos Do Zure tomado como um backup
* recuperação de desastres de um servidor no local pode acelerar significativamente
* alterações feitas diretamente em um compartilhamento de arquivos Do Azure (fora da sincronização) podem ser detectadas e sincronizadas mais rapidamente

#### <a name="a-structured-approach-to-a-deployment-map"></a>Uma abordagem estruturada para um mapa de implantação

Antes de implantar o armazenamento em nuvem em uma etapa subseqüente, é importante criar um mapa entre pastas no local e compartilhamentos de arquivos Do Zure. Esse mapeamento informará em seguida quantos e quais recursos do Azure File Sync "sync group" você irá prová-lo. Um grupo de sincronização une o compartilhamento de arquivos Azure e a pasta no servidor e estabelece uma conexão de sincronização.

Para tomar a decisão sobre quantos compartilhamentos de arquivos do Azure você precisa, revise os seguintes limites e práticas recomendadas. Isso ajudará você a otimizar seu mapa:

* Um servidor com o agente Azure File Sync instalado, pode sincronizar com até 30 compartilhamentos de arquivos Do Zure.
* Um compartilhamento de arquivos Do Zure é implantado dentro de uma conta de armazenamento. Isso faz da conta de armazenamento um alvo de escala para números de desempenho, como IOPS e throughput. Duas ações padrão (não premium) de arquivos Do Zure poderiam teoricamente saturar o desempenho máximo que uma conta de armazenamento pode oferecer. Se você planeja anexar apenas o Azure File Sync a esses compartilhamentos de arquivos, então agrupar vários compartilhamentos de arquivos do Azure na mesma conta de armazenamento não está criando um problema. Revise as metas de desempenho de compartilhamento de arquivos do Azure para obter uma visão mais profunda das métricas relevantes a serem consideradas. Se você planeja levantar um aplicativo para o Azure que usará o compartilhamento de arquivos do Azure nativamente, então você pode precisar de mais desempenho do seu compartilhamento de arquivos Do Azure. Se essa é uma possibilidade, mesmo no futuro, então mapear um compartilhamento de arquivos do Azure para sua própria conta de armazenamento é o melhor.
* Há um limite de 250 contas de armazenamento por assinatura em uma única região do Azure.

> [!TIP]
> Com essas informações em mente, muitas vezes torna-se necessário agrupar várias pastas de nível superior em seus volumes em um diretório raiz comum e novo. Em seguida, você sincronizará este novo diretório raiz e todas as pastas que você agrupado nele, para um único compartilhamento de arquivo Azure.                                                    

Essa técnica permite que você fique dentro do limite de sincronização de compartilhamento de compartilhamento de arquivos do Azure de 30 azure por servidor.
Esse agrupamento uma raiz comum não tem impacto no acesso aos seus dados. Suas ACLs permanecem como estão, você só precisa ajustar quaisquer caminhos de compartilhamento (como ações SMB ou NFS) que você possa ter nas pastas de servidor que agora você transformou em uma raiz comum. Nada mais muda.

Outro aspecto importante do Azure File Sync e um desempenho e experiência equilibrados é a compreensão dos fatores de escala para o desempenho do Azure File Sync. Obviamente, quando os arquivos são sincronizados pela internet, arquivos maiores levam mais tempo e largura de banda para sincronizar.

> [!IMPORTANT]
> O vetor de escala mais importante para o Azure File Sync é o número de itens (arquivos e pastas) que precisam ser sincronizados.

O Azure File Sync suporta sincronização de até 100.000 itens para um único compartilhamento de arquivos Azure. Esse limite pode ser excedido e retrata apenas o que a equipe do Azure File Sync testa regularmente.

É uma prática recomendada para manter o número de itens por escopo de sincronização baixo. Esse aspecto é um fator importante a ser considerado no seu mapeamento de pastas para compartilhamentos de arquivos do Azure.

Mesmo que em sua situação um conjunto de pastas possa sincronizar logicamente com o mesmo compartilhamento de arquivos do Azure (usando a nova abordagem de pasta raiz comum de cima) ainda pode ser melhor reagrupar pastas de tal forma que elas sincronizem com duas em vez de um compartilhamento de arquivo Azure. Essa abordagem pode ser usada para manter o número de arquivos e pastas por compartilhamento de arquivos equilibrado em todo o servidor.

#### <a name="create-a-mapping-table"></a>Crie uma tabela de mapeamento

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Use uma combinação dos conceitos anteriores para ajudar a determinar quantos compartilhamentos de arquivos do Azure você precisa e quais partes de seus dados existentes acabarão em que o compartilhamento de arquivos do Azure será compartilhado.
        
        Crie uma tabela que registre seus pensamentos, de tal forma que você possa se referir a ela no próximo passo. Manter-se organizado é importante, pois pode ser fácil perder detalhes do seu plano de mapeamento ao provisionar muitos recursos do Azure ao mesmo tempo. Para ajudá-lo a criar um mapeamento completo, você pode baixar um arquivo microsoft excel como um modelo.

[//]: # (HTML aparece como a única maneira de realizar a adição de uma tabela aninhada de duas colunas com análise de imagem de trabalho e texto/hiperlink na mesma linha.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Baixe um modelo de mapeamento de namespace.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
