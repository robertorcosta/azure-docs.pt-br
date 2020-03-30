---
title: Localizações de dados para Windows Virtual Desktop - Azure
description: Uma breve visão geral de quais locais os dados e metadados do Windows Virtual Desktop são armazenados.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128048"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Localizações de dados para Windows Virtual Desktop

O Windows Virtual Desktop está disponível atualmente para todas as localizações geográficas. Inicialmente, os metadados de serviço só podem ser armazenados na geografia dos Estados Unidos (EUA). Os administradores podem escolher o local para armazenar dados do usuário quando criam máquinas virtuais do pool de host e serviços associados, como servidores de arquivos. Saiba mais sobre as geografias do Azure no mapa do datacenter do [Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft não controla ou limita as regiões onde você ou seus usuários podem acessar dados específicos do usuário e do aplicativo.

>[!IMPORTANT]
>O Windows Virtual Desktop armazena informações globais de metadados, como nomes de inquilinos, nomes de pool de host, nomes de grupos de aplicativos e nomes principais de usuários em um data center localizado nos Estados Unidos. Os metadados armazenados são criptografados em repouso, e espelhos geo-redundantes são mantidos dentro dos Estados Unidos. Todos os dados do cliente, como configurações do aplicativo e dados do usuário, residem no local que o cliente escolhe e não é gerenciado pelo serviço.

Os metadados de serviço são replicados nos Estados Unidos para fins de recuperação de desastres.