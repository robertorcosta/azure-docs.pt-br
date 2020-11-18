---
title: Suporte ao ciclo de vida estendido do Red Hat Enterprise Linux
description: Saiba mais sobre como adicionar o suporte ao ciclo de vida estendido do Red Hat Enterprise
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 124ff65087887a437e0b82fbd7b1e4c72e4f7b4c
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684348"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) suporte ao ciclo de vida estendido
Este artigo fornece informações sobre o suporte ao ciclo de vida estendido para as imagens do Red Hat Enterprise:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Ciclo de vida do Red Hat Enterprise Linux 6
A partir de 30 de novembro de 2020, o Red Hat Enterprise Linux 6 atingirá o fim da fase de manutenção. A fase de manutenção é seguida pela fase de vida estendida. Como Red Hat Enterprise Linux 6 transições das fases completa/de manutenção, é altamente recomendável atualizar para o Red Hat Enterprise Linux 7 ou 8. Se os clientes tiverem que permanecer em Red Hat Enterprise Linux 6, é recomendável adicionar o complemento Red Hat Enterprise Linux ELS (suporte ao ciclo de vida estendido).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Estou executando o Red Hat Enterprise Linux 6 e não é possível migrar para uma versão posterior no momento. Quais as opções disponíveis?
* Continue a execução do Red Hat Enterprise Linux 6 e adquira o ELS (suporte do ciclo de vida estendido) Add-On repositórios para continuar a receber manutenção limitada de software e suporte técnico (consulte o processo de atualização e detalhes de preços abaixo).
* Migre para o Red Hat Enterprise Linux 7 ou 8 assim que possível.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Qual é o custo adicional para usar Red Hat Enterprise Linux complemento de suporte ao ciclo de vida estendida (ELS)?

|Tamanho da VM|Período de tempo de cobrança adicional|Valor de dólar adicional (USD)| Observações|
|---|---|---|---|
| Pequeno convidado virtual (<= 4 núcleos) | Custo adicional por hora | TBC | |
|  | Custo adicional mensal | TBC | Para instâncias reservadas |
|  | Custo adicional anual | TBC | Para instâncias reservadas |
| Grande convidado virtual (>4 núcleos) | Custo adicional por hora | TBC | |
|  | Custo adicional mensal | TBC | Para instâncias reservadas |
|  | Custo adicional anual | TBC | Para instâncias reservadas |

#### <a name="what-is-the-process-to-add-extended-life-cycle-support-els-repositories-to-continue-to-receive-software-maintenance-bug-and-security-fixes-and--support-for-red-hat-enterprise-linux-6"></a>Qual é o processo para adicionar repositórios de ELS (suporte ao ciclo de vida estendida) para continuar a receber manutenção de software (correções de bugs e de segurança) e suporte para o Red Hat Enterprise Linux 6?

O processo de ponta a ponta de se inscrever no ELS estará disponível em breve (mais recente em 30 de novembro de 2020).

## <a name="next-steps"></a>Próximas etapas

* Para exibir a lista completa de imagens RHEL no Azure, confira [Red Hat Enterprise Linux (RHEL) imagens disponíveis no Azure](./redhat-imagelist.md).
* Para saber mais sobre a infraestrutura de atualização do Red Hat do Azure, confira [infraestrutura de atualização do Red Hat para VMs do RHEL sob demanda no Azure](./redhat-rhui.md).
* Para saber mais sobre a oferta BYOS do RHEL, confira [Red Hat Enterprise Linux imagens Gold de sua própria assinatura no Azure](./byos.md).
* Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).