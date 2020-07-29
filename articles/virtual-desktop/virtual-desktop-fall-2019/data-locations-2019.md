---
title: Locais de dados para a área de trabalho virtual do Windows (clássico)-Azure
description: Uma breve visão geral de quais locais os dados e metadados da área de trabalho virtual do Windows (clássico) são armazenados.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0e12e17914374a64ecd2d17a8e73c6ebf20328d9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87270032"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Locais de dados para a área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à área de trabalho virtual do Windows (clássico), que não dá suporte a Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver tentando gerenciar Azure Resource Manager objetos da área de trabalho virtual do Windows, consulte [Este artigo](../data-locations.md).

A área de trabalho virtual do Windows está disponível atualmente para todas as localizações geográficas. Inicialmente, os metadados de serviço só podem ser armazenados na geografia de Estados Unidos (EUA). Os administradores podem escolher o local para armazenar os dados do usuário ao criarem as máquinas virtuais do pool de hosts e serviços associados, como servidores de arquivos. Saiba mais sobre as geografias do Azure no [mapa de datacenter do Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft não controla nem limita as regiões nas quais você ou seus usuários podem acessar seus dados específicos do aplicativo e do usuário.

>[!IMPORTANT]
>A área de trabalho virtual do Windows armazena informações de metadados globais, como nomes de locatário, nomes de pool de hosts, nomes de grupos de aplicativos e nomes de entidades de usuário em um Datacenter localizado no Estados Unidos. Os metadados armazenados são criptografados em repouso e os espelhos com redundância geográfica são mantidos dentro do Estados Unidos. Todos os dados do cliente, como configurações do aplicativo e dados do usuário, residem no local escolhido pelo cliente e não são gerenciados pelo serviço.

Os metadados de serviço são replicados no Estados Unidos para fins de recuperação de desastre.