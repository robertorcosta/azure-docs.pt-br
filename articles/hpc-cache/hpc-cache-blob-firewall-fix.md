---
title: Trabalhe em torno das configurações de firewall de armazenamento
description: Uma configuração de firewall de rede de conta de armazenamento pode causar falha ao criar um alvo de armazenamento Azure Blob no Cache Azure HPC. Este artigo fornece uma solução para a limitação até que uma correção de software esteja em vigor.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174402"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Ajustar as configurações de firewall da conta de armazenamento de blobs

Uma configuração específica usada em firewalls de conta de armazenamento pode fazer com que a criação do alvo de armazenamento Blob falhe. A equipe de cache do Azure HPC está trabalhando em uma correção de software para este problema, mas você pode contorná-lo seguindo as instruções deste artigo.

A configuração de firewall que permite o acesso apenas a partir de "redes selecionadas" pode impedir que o cache crie um destino de armazenamento Blob. Essa configuração está na página de configurações de **Firewalls e redes virtuais** da conta de armazenamento.

O problema é que o serviço de cache usa uma rede virtual de serviço oculto separada dos ambientes do cliente. Não é possível autorizar explicitamente essa rede para acessar sua conta de armazenamento.

Quando você cria um alvo de armazenamento Blob, o serviço de cache usa essa rede para verificar se o contêiner está ou não vazio. Se o firewall não permitir o acesso da rede oculta, a verificação falhará e a criação do destino de armazenamento falhará.

Para contornar o problema, altere temporariamente as configurações do firewall ao criar o destino de armazenamento:

1. Vá para a página **firewalls e redes virtuais** da conta de armazenamento e altere a configuração "Permitir acesso" para **Todas as redes**.
1. Crie o destino de armazenamento Blob no cache Do Azure HPC.
1. Depois que o destino de armazenamento for criado com sucesso, altere a configuração de firewall da conta para **redes selecionadas**.

O Cache Azure HPC não usa a rede virtual do serviço para acessar o destino de armazenamento acabado.

Para obter ajuda com esta solução, entre [em contato com o Serviço e suporte da Microsoft.](hpc-cache-support-ticket.md)
