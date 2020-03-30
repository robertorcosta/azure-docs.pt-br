---
title: Azure VMware Solution by CloudSimple - Aloque endereços IP públicos
description: Descreve como alocar endereços IP públicos para máquinas virtuais no ambiente Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024289"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Aloque endereços IP públicos para ambiente de Nuvem Privada

Abra a guia IPs públicos na página Rede para alocar endereços IP públicos para máquinas virtuais em seu ambiente de Nuvem Privada.

1. [Acesse o portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **IPs Públicos**.
3. Clique **em Novo IP público**.

    ![Página de IPs públicos](media/public-ips-page.png)

4. Digite um nome para identificar a entrada do endereço IP.
5. Mantenha o local padrão.
6. Use o controle deslizante para alterar o tempo inocioso, se necessário.
7. Digite o endereço IP local para o qual você deseja atribuir um endereço IP público.
8. Digite um nome DNS associado.
9. Clique em **Enviar**.

![Alocar IPs públicos](media/network-public-ip-allocate.png)

A tarefa de alocar o endereço IP público começa. Você pode verificar o status da tarefa na página **Atividade > Tarefas.** Quando a alocação estiver concluída, a nova entrada será mostrada na página de IPs públicos.
