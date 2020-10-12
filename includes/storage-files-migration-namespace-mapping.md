---
title: Mapear uma estrutura de pastas para uma topologia Sincronização de Arquivos do Azure
description: Mapear uma estrutura de arquivos e pastas existente para compartilhamentos de arquivos do Azure para uso com Sincronização de Arquivos do Azure. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 16b9342f0374377349f338db7ce5c8389c77ea18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87425206"
---
Nesta etapa, você está avaliando Quantos compartilhamentos de arquivos do Azure são necessários. Uma única instância do Windows Server (ou cluster) pode sincronizar até 30 compartilhamentos de arquivos do Azure.

Você pode ter mais pastas em seus volumes que estão sendo compartilhados localmente como compartilhamentos SMB para seus usuários e aplicativos. A maneira mais fácil é desconceber um compartilhamento local que mapeia 1:1 para um compartilhamento de arquivos do Azure. Se você tiver um número pequeno o suficiente, abaixo de 30 para uma única instância do Windows Server, recomendamos um mapeamento 1:1.

Se você tiver mais compartilhamentos do que 30, muitas vezes é desnecessário mapear um compartilhamento local 1:1 para um compartilhamento de arquivos do Azure. Considere as opções a seguir.

#### <a name="share-grouping"></a>Agrupamento de compartilhamento

Se o seu departamento de RH (por exemplo) tiver um total de 15 compartilhamentos, você poderá considerar armazenar todos os dados de RH em um único compartilhamento de arquivos do Azure. O armazenamento de vários compartilhamentos locais em um compartilhamento de arquivos do Azure não impede que você crie os 15 compartilhamentos SMB usuais em sua instância local do Windows Server. Isso significa apenas que você organiza as pastas raiz desses 15 compartilhamentos como subpastas em uma pasta comum. Em seguida, sincronize essa pasta comum com um compartilhamento de arquivos do Azure. Dessa forma, apenas um único compartilhamento de arquivos do Azure na nuvem é necessário para esse grupo de compartilhamentos locais.

#### <a name="volume-sync"></a>Sincronização de volume

O Sincronização de Arquivos do Azure dá suporte à sincronização da raiz de um volume para um compartilhamento de arquivos do Azure. Se você sincronizar a pasta raiz, todas as subpastas e arquivos vão para o mesmo compartilhamento de arquivos do Azure.

A sincronização da raiz do volume nem sempre é a melhor resposta. Há benefícios na sincronização de vários locais. Por exemplo, isso ajuda a manter o número de itens menores por escopo de sincronização. Configurar Sincronização de Arquivos do Azure com um número menor de itens não é apenas benéfico para a sincronização de arquivos. Um número menor de itens também beneficia cenários como estes:

* A restauração do lado da nuvem de um instantâneo de compartilhamento de arquivos do Azure pode ser realizada como um backup.
* A recuperação de desastres de um servidor local pode acelerar significativamente.
* As alterações feitas diretamente em um compartilhamento de arquivos do Azure (fora da sincronização) podem ser detectadas e sincronizadas mais rapidamente.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Uma abordagem estruturada para um mapa de implantação

Antes de implantar o armazenamento em nuvem em uma etapa posterior, é importante criar um mapa entre pastas locais e compartilhamentos de arquivos do Azure. Esse mapeamento informará quantas e quais Sincronização de Arquivos do Azure os recursos do *grupo de sincronização* que você provisionar. Um grupo de sincronização vincula o compartilhamento de arquivos do Azure e a pasta em seu servidor em conjunto e estabelece uma conexão de sincronização.

Para tomar a decisão sobre quantos compartilhamentos de arquivos do Azure você precisa, examine os limites e as melhores práticas a seguir. Isso ajudará você a otimizar seu mapa.

* Um servidor com o agente de Sincronização de Arquivos do Azure instalado pode sincronizar com até 30 compartilhamentos de arquivos do Azure.
* Um compartilhamento de arquivos do Azure é implantado dentro de uma conta de armazenamento. Isso torna a conta de armazenamento um destino de escala para números de desempenho, como IOPS e taxa de transferência. 

  Dois compartilhamentos de arquivos do Azure padrão (não Premium) podem, teoricamente, saturar o desempenho máximo que uma conta de armazenamento pode fornecer. Se você planeja anexar apenas Sincronização de Arquivos do Azure a esses compartilhamentos de arquivos, o agrupamento de vários compartilhamentos de arquivos do Azure na mesma conta de armazenamento não criará um problema. Examine os destinos de desempenho do compartilhamento de arquivos do Azure para obter informações mais aprofundadas sobre as métricas relevantes a serem consideradas. 

  Se você planeja levantar um aplicativo para o Azure que usará o compartilhamento de arquivos do Azure nativamente, talvez precise de mais desempenho do compartilhamento de arquivos do Azure. Se essa for uma possibilidade, mesmo no futuro, o mapeamento de um compartilhamento de arquivos do Azure para sua própria conta de armazenamento é o melhor.
* Há um limite de 250 contas de armazenamento por assinatura em uma única região do Azure.

> [!TIP]
> Com essas informações em mente, geralmente é necessário agrupar várias pastas de nível superior em seus volumes em um diretório raiz comum e novo. Em seguida, você sincroniza esse novo diretório raiz e todas as pastas que você agrupau para ele para um único compartilhamento de arquivos do Azure. Essa técnica permite que você permaneça dentro do limite de 30 sincronizações de compartilhamento de arquivos do Azure por servidor.
>
> Esse agrupamento sob uma raiz comum não tem impacto sobre o acesso aos seus dados. Suas ACLs permanecem como estão. Você precisaria apenas ajustar os caminhos de compartilhamento (como compartilhamentos SMB ou NFS) que você pode ter nas pastas de servidor que você alterou em uma raiz comum. Nada mais muda.

Outro aspecto importante do Sincronização de Arquivos do Azure e de um desempenho e experiência balanceada é compreender os fatores de escala para o desempenho de Sincronização de Arquivos do Azure. Obviamente, quando os arquivos são sincronizados pela Internet, os arquivos maiores levam mais tempo e largura de banda para sincronização.

> [!IMPORTANT]
> O vetor de escala mais importante para Sincronização de Arquivos do Azure é o número de itens (arquivos e pastas) que precisam ser sincronizados.

O Sincronização de Arquivos do Azure dá suporte à sincronização de até 100 milhões itens para um único compartilhamento de arquivos do Azure. Esse limite pode ser excedido e mostra apenas o que a equipe de Sincronização de Arquivos do Azure testa regularmente.

É uma prática recomendada manter o número de itens por escopo de sincronização baixo. Esse é um fator importante a ser considerado no mapeamento de pastas para compartilhamentos de arquivos do Azure.

Em sua situação, é possível que um conjunto de pastas possa ser sincronizado logicamente com o mesmo compartilhamento de arquivos do Azure (usando a nova abordagem de pasta raiz comum mencionada anteriormente). Mas talvez ainda seja melhor reagrupar pastas de forma que elas sincronizem com duas, em vez de um compartilhamento de arquivos do Azure. Você pode usar essa abordagem para manter o número de arquivos e pastas por compartilhamento de arquivos balanceados pelo servidor.

#### <a name="create-a-mapping-table"></a>Criar uma tabela de mapeamento

:::row:::
    :::column:::
        [![Um exemplo de uma tabela de mapeamento. Baixe o arquivo abaixo para experimentar e usar o conteúdo desta imagem.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Use uma combinação dos conceitos anteriores para ajudar a determinar quantos compartilhamentos de arquivos do Azure são necessários e quais partes dos dados existentes acabarão com o compartilhamento de arquivos do Azure.
        
        Crie uma tabela que registra suas ideias, para que você possa consultá-las na próxima etapa. Manter-se organizado é importante, pois pode ser fácil perder detalhes do seu plano de mapeamento quando você estiver Provisionando muitos recursos do Azure de uma vez. Para ajudá-lo a criar um mapeamento completo, você pode baixar um arquivo do Microsoft Excel como um modelo.

[//]: # (O HTML é exibido como a única maneira de realizar a adição de uma tabela aninhada de duas colunas com análise de imagem de trabalho e texto/hiperlink na mesma linha.)

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
