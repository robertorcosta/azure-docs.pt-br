---
title: Práticas recomendadas de tag de imagem
description: As melhores práticas para marcar e versificar imagens de contêiner Docker ao empurrar imagens para e retirar imagens de um registro de contêiner do Azure
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445745"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Recomendações para marcar e versiar imagens de contêineres

Ao empurrar a implantação de imagens de contêiner para um registro de contêiner e, em seguida, implantá-las, você precisa de uma estratégia para marcação e versão de imagem. Este artigo discute duas abordagens e onde cada uma se encaixa durante o ciclo de vida do contêiner:

* **Tags estáveis** - Tags que você reutiliza, por exemplo, para indicar uma versão maior ou menor, como *mycontainerimage:1.0*.
* **Tags exclusivas** - Uma tag diferente para cada imagem que você empurra para um registro, como *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Tags estáveis

**Recomendação**: Use etiquetas estáveis para manter **imagens básicas** para a construção do contêiner. Evite implantações com tags estáveis, pois essas tags continuam a receber atualizações e podem introduzir inconsistências nos ambientes de produção.

*Tags estáveis* significam que um desenvolvedor, ou um sistema de compilação, pode continuar a puxar uma tag específica, que continua a receber atualizações. Estável não significa que o conteúdo está congelado. Em vez disso, estável implica que a imagem deve ser estável para a intenção dessa versão. Para se manter "estável", ele pode ser reparado para aplicar patches de segurança ou atualizações de quadros.

### <a name="example"></a>Exemplo

Uma equipe de estrutura envia versão 1.0. Eles sabem que enviarão atualizações, incluindo pequenas atualizações. Para suportar etiquetas estáveis para uma determinada versão maior e menor, eles têm dois conjuntos de tags estáveis.

* `:1`– uma etiqueta estável para a versão principal. `1`representa a versão "mais recente" ou "mais recente" 1.*
* `:1.0`- uma tag estável para a versão 1.0, permitindo que um desenvolvedor se vincule a atualizações de 1.0, e não seja enrolado para 1.1 quando for lançado.

A equipe também `:latest` usa a tag, que aponta para a mais recente tag estável, não importa qual seja a versão principal atual.

Quando as atualizações de imagem base estão disponíveis, ou qualquer tipo de liberação de manutenção do framework, as imagens com as tags estáveis são atualizadas para o mais novo digesto que representa a versão estável mais atual dessa versão.

Neste caso, tanto as etiquetas principais quanto as menores estão sendo continuamente atendidas. A partir de um cenário de imagem base, isso permite que o proprietário da imagem forneça imagens atendidas.

### <a name="delete-untagged-manifests"></a>Excluir manifestos não marcados

Se uma imagem com uma tag estável for atualizada, a imagem marcada anteriormente não será marcada, resultando em uma imagem órfã. Os dados de camada da imagem anterior e de camada única permanecem no registro. Para manter o tamanho do registro, você pode excluir periodicamente manifestos não marcados resultantes de atualizações estáveis de imagem. Por exemplo, [a eliminação automática](container-registry-auto-purge.md) de manifestos não marcados mais antigos do que uma duração especificada ou define uma política de [retenção](container-registry-retention-policy.md) para manifestos não marcados.

## <a name="unique-tags"></a>Tags exclusivas

**Recomendação**: Use tags exclusivas para **implantações,** especialmente em um ambiente que possa ser dimensionado em vários nódulos. Você provavelmente quer implantações deliberadas de uma versão consistente de componentes. Se o contêiner reiniciar ou um orquestrador dimensionar mais instâncias, os hosts não puxarão acidentalmente uma versão mais recente, inconsistente com os outros nós.

A marcação exclusiva significa simplesmente que cada imagem empurrada para um registro tem uma tag única. As etiquetas não são reutilizadas. Existem vários padrões que você pode seguir para gerar tags exclusivas, incluindo:

* **Carimbo de data-hora** - Esta abordagem é bastante comum, já que você pode dizer claramente quando a imagem foi construída. Mas, como correlacionar de volta ao seu sistema de construção? Você tem que encontrar a construção que foi concluída ao mesmo tempo? Em que fuso horário você está? Todos os seus sistemas de construção estão calibrados para utc?
* **Git commit** – Essa abordagem funciona até que você comece a suportar atualizações de imagem base. Se uma atualização de imagem base acontecer, seu sistema de compilação será iniciar com o mesmo git commit da compilação anterior. No entanto, a imagem base tem novo conteúdo. Em geral, um compromisso *semi*Git fornece uma etiqueta semi-estável.
* **Digestão manifesto** - Cada imagem do recipiente empurrada para um registro de contêiner está associada a um manifesto, identificado por um hash SHA-256 único, ou digest. Embora único, o digesto é longo, difícil de ler e não correlacionado com seu ambiente de construção.
* **Build ID** - Esta opção pode ser melhor, pois é provavelmente incremental, e permite que você se correlaciona de volta à compilação específica para encontrar todos os artefatos e logs. No entanto, como um digerir manifesto, pode ser difícil para um humano ler.

  Se sua organização possui vários sistemas de compilação, prefixar a tag `<build-system>-<build-id>`com o nome do sistema de compilação é uma variação nesta opção: . Por exemplo, você pode diferenciar compilações do sistema de compilação Jenkins da equipe de API e do sistema de compilação Azure Pipelines da equipe web.

### <a name="lock-deployed-image-tags"></a>Bloquear tags de imagem implantadas

Como uma prática recomendada, recomendamos que você [bloqueie](container-registry-image-lock.md) qualquer tag de imagem implantada, definindo seu `write-enabled` atributo para `false`. Essa prática impede que você remoção inadvertidamente uma imagem do registro e possivelmente interrompa suas implantações. Você pode incluir a etapa de bloqueio em seu pipeline de liberação.

Bloquear uma imagem implantada ainda permite remover outras imagens não implantadas do seu registro usando recursos do Registro de Contêiner do Azure para manter seu registro. Por exemplo, [expurgar automaticamente](container-registry-auto-purge.md) manifestos não marcados ou imagens desbloqueadas com mais de uma duração especificada ou definir uma política de [retenção](container-registry-retention-policy.md) para manifestos não marcados.

## <a name="next-steps"></a>Próximas etapas

Para uma discussão mais detalhada dos conceitos neste artigo, consulte o post do blog [Docker Tagging: Melhores práticas para marcar e versia imagens docker](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Para ajudar a maximizar o desempenho e o uso econômico do registro de contêineres do Azure, consulte [As melhores práticas para o Registro de Contêineres do Azure](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

