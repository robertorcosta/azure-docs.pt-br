---
title: O que é o recurso de registro automático das zonas privadas do Azure DNS
description: Visão geral do recurso de registro automático das zonas privadas do Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961209"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Qual é o recurso de registro automático das zonas privadas do Azure DNS

O recurso de registro automático de zonas privadas do Azure DNS tira a dor do gerenciamento de registros DNS para máquinas virtuais implantadas em uma rede virtual. Quando você [vincula uma rede virtual](./private-dns-virtual-network-links.md) a uma zona dns privada e habilita o registro automático para todas as máquinas virtuais, os registros de DNS para as máquinas virtuais implantadas na rede virtual são criados automaticamente na zona de DNS privada. Além dos registros de olhar futuro (Registros A), os registros de busca reversa (registros PTR) também são criados automaticamente para as máquinas virtuais.
Se você adicionar mais máquinas virtuais à rede virtual, os registros DeDNS para essas máquinas virtuais também serão criados automaticamente na zona de DNS privada vinculada.

Quando você exclui uma máquina virtual, os registros De DNS para a máquina virtual são automaticamente excluídos da zona DNS privada.

Você pode habilitar o registro automático selecionando a opção "Ativar registro automático" ao criar um link de rede virtual.

![Habilite o registro automático](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Restrições

* O registro automático funciona apenas para máquinas virtuais. Para todos os outros recursos, como balanceadores de carga interna, etc., você pode criar registros DNS manualmente na zona dns privada vinculada à rede virtual.
* Os registros DNS são criados automaticamente apenas para a NIC da máquina virtual primária . Se suas máquinas virtuais tiverem mais de uma NIC, você poderá criar manualmente os registros De DNS para outras interfaces de rede.
* o registro automático para IPv6 (registros AAAA) não é suportado.

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma zona privada no DNS do Azure usando o [Azure PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia mais sobre alguns [cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas no DNS do Azure.

* Para ver perguntas comuns e obter respostas sobre zonas privadas no DNS do Azure, incluindo o comportamento específico que se pode esperar para determinados tipos de operações, confira as [Perguntas frequentes sobre DNS privado](./dns-faq-private.md).
