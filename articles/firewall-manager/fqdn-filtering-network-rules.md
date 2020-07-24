---
title: Filtragem do Gerenciador de firewall do Azure em regras de rede (versão prévia)
description: Como usar a filtragem de FQDN em regras de rede
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 599620c5fcc3ad1802527bd66e2dbead1b97d11d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079020"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtragem de FQDN em regras de rede (versão prévia)

> [!IMPORTANT]
> A filtragem de FQDN nas regras de rede está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um FQDN (nome de domínio totalmente qualificado) representa um nome de domínio de um host. Um nome de domínio é associado a um único ou vários endereços IP. Você pode permitir ou bloquear FQDNs e marcas de FQDN em regras de aplicativo. Usando as configurações personalizadas de proxy DNS e DNS, você também pode usar a filtragem de FQDN em regras de rede.

## <a name="how-it-works"></a>Como ele funciona

O Firewall do Azure traduz o FQDN para um ou mais endereços IP usando suas configurações de DNS e executa o processamento de regras com base no DNS do Azure ou em uma configuração de DNS personalizada.

Para usar FQDNs em regras de rede, você deve habilitar o proxy DNS. Se você não habilitar o proxy DNS, o processamento de regras confiáveis estará em risco. Quando habilitado, o tráfego DNS é direcionado para o Firewall do Azure, no qual você pode configurar seu servidor DNS personalizado. Em seguida, o firewall e os clientes usam o mesmo servidor DNS configurado. Se o proxy DNS não estiver habilitado, o Firewall do Azure poderá produzir uma resposta diferente, pois o cliente e o firewall podem usar servidores diferentes para a resolução de nomes. A filtragem de FQDN nas regras de rede poderá ser defeituosa ou inconsistente se o cliente e o firewall receberem respostas DNS diferentes.

## <a name="next-steps"></a>Próximas etapas

[Configurações de DNS do firewall do Azure](dns-settings.md)
