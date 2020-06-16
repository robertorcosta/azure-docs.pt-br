---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2020
ms.locfileid: "84464932"
---
Navegue até a conta de armazenamento para a qual gostaria de restringir o ponto de extremidade público para redes virtuais específicas. No sumário da conta de armazenamento, selecione **Firewalls e redes virtuais**. 

Na parte superior da página, selecione o botão de opção **Redes selecionadas**. Isso exibirá várias configurações para controlar a restrição do ponto de extremidade público. Clique em **+ Adicionar rede virtual existente** para selecionar a rede virtual específica que deve ter permissão para acessar a conta de armazenamento por meio do ponto de extremidade público. Para isso, será necessário selecionar uma rede virtual e uma sub-rede para essa rede virtual. 

Marque **Permitir que serviços Microsoft confiáveis acessem esta conta de serviço** para permitir que serviços Microsoft confiáveis, como a Sincronização de Arquivos do Azure, acessem a conta de armazenamento.

[![Captura de tela da folha Firewalls e redes virtuais com uma rede virtual específica com permissão para acessar a conta de armazenamento por meio do ponto de extremidade público](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)