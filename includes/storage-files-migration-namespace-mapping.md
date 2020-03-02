---
title: Mapear uma estrutura de pastas para uma topologia Sincronização de Arquivos do Azure
description: Mapear uma estrutura de arquivos e pastas existente para compartilhamentos de arquivos do Azure para uso com Sincronização de Arquivos do Azure. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209552"
---
Nesta etapa, você está avaliando Quantos compartilhamentos de arquivos do Azure são necessários. Um único Windows Server (ou cluster) pode sincronizar até 30 compartilhamentos de arquivos do Azure.

Você pode ter mais pastas em seu StorSimple que são atualmente compartilhadas localmente como compartilhamentos SMB para seus usuários e aplicativos. A mais fácil seria prever um compartilhamento local para mapear 1:1 para um compartilhamento de arquivos do Azure. Se esse número for muito pequeno, ou seja, abaixo de 30 para um único Windows Server, ou se você planeja ter dois servidores Windows (60) e assim por diante, é recomendável um mapeamento 1:1.

Se você tiver mais compartilhamentos do que 30, muitas vezes é desnecessário mapear um compartilhamento local 1:1 para um compartilhamento de arquivos do Azure.
Considere as seguintes opções:

#### <a name="share-grouping"></a>Agrupamento de compartilhamento

Por exemplo, se o departamento de RH tiver um total de 15 compartilhamentos, você poderá considerar armazenar todos os dados de RH em um único compartilhamento de arquivos do Azure. O armazenamento de vários compartilhamentos locais em um compartilhamento de arquivos do Azure não impede que você crie os 15 compartilhamentos SMB usuais em seu servidor Windows local. Isso significa apenas que você organiza as pastas raiz desses 15 compartilhamentos como subpastas em uma pasta comum. Em seguida, sincronize essa pasta comum com um compartilhamento de arquivos do Azure. Dessa forma, apenas um único compartilhamento de arquivos do Azure na nuvem é necessário para esse grupo de compartilhamentos locais.

#### <a name="volume-sync"></a>Sincronização de volume

O Sincronização de Arquivos do Azure dá suporte à sincronização da raiz de um volume para um compartilhamento de arquivos do Azure.
Se você sincronizar a pasta raiz, todas as subpastas e arquivos serão encerrados no mesmo compartilhamento de arquivos do Azure.

#### <a name="other-best-practices-to-consider"></a>Outras práticas recomendadas a serem consideradas

Além dos 30 limites de sincronização de compartilhamento de arquivos do Azure por servidor, a principal consideração é a eficiência da sincronização.

Quando há vários compartilhamentos em seu servidor sincronizados com seu próprio compartilhamento de arquivos do Azure, a sincronização pode funcionar em paralelo para todos eles. O vetor de escala não é o tamanho de todos os arquivos em um escopo de sincronização. É o número de itens (arquivos e pastas) que precisam de processamento.

Um único compartilhamento de arquivos do Azure pode conter até 100 TiB.
Sincronização de Arquivos do Azure dá suporte à sincronização de até 100 milhões itens por compartilhamento de arquivos do Azure.

A sincronização do volume raiz nem sempre será a melhor resposta. Há benefícios em sincronizar vários locais, fazendo isso ajuda a manter o número de itens menores por escopo de sincronização. A configuração da sincronização de arquivos do Azure com um número menor de itens não é apenas importante para a sincronização, mas também beneficia a restauração do lado da nuvem a partir de backups, bem como auxiliar a velocidade de recuperação de desastres caso você perca seu servidor e provisione um novo que se conecte ao mesmo fil do Azure e compartilhamentos.

Use uma combinação dos conceitos acima para ajudar a determinar quantos compartilhamentos de arquivos do Azure são necessários e quais partes de seus dados do StorSimple existentes acabarão com o compartilhamento de arquivos do Azure.

Crie uma lista que registra suas ideias, de modo que você possa consultá-las na próxima etapa. Manter-se organizado aqui é importante, pois pode ser fácil perder detalhes do seu plano de mapeamento ao provisionar muitos recursos de uma vez.
