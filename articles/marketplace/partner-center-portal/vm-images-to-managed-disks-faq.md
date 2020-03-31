---
title: Estamos movendo imagens de máquina virtual (VM) para armazenamento de disco gerenciado no Azure Marketplace
description: Para fornecer armazenamento e suporte mais rápidos e confiáveis para novos recursos e recursos do mercado, estamos movendo imagens de VM do mercado para armazenamento em disco gerenciado.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285105"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Estamos movendo imagens de máquina virtual (VM) no Azure Marketplace para armazenamento de disco gerenciado

Para fornecer armazenamento e suporte mais rápidos e confiáveis para novos recursos e recursos do mercado, estamos movendo imagens de VM do mercado para armazenamento em disco gerenciado.

A partir de 2 de janeiro de 2020, vamos mover imagens de VM para armazenamento de disco gerenciado em fases. Na primeira fase, só moveremos imagens sem novas implantações ou executando VMs nos últimos 90 dias. Antes de mover qualquer uma das imagens, enviaremos um e-mail para informar ao editor quais imagens se moverão e quando serão movidas.

Editores ou consumidores não precisam tomar nenhuma ação, e os usuários não serão afetados. As ofertas do marketplace permanecerão disponíveis e os clientes ainda poderão implantar VMs gerenciadas a partir dessas imagens, durante e após a mudança.

Em caso de dúvidas, entre [em contato conosco.](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)

## <a name="faqs"></a>Perguntas frequentes

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Os usuários das minhas imagens VM sofreriam uma paralisação?

Os usuários das imagens VM não sofrerão uma paralisação. 

Na primeira fase, só moveremos imagens de VM que não possuem VMs em execução. Como não há usuários para essas imagens, não haverá nenhum impacto. Para as fases subseqüentes também, não haverá impacto sobre os usuários.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Quanto tempo leva para o processo ser concluído?

Pode levar até 24 horas para concluir a migração.

### <a name="do-i-need-to-take-any-action"></a>Preciso tomar alguma atitude?

Não. Editores ou consumidores não precisam tomar nenhuma ação.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Tenho que atualizar meu sistema para chamar as APIs do Portal da Nuvem de uma maneira diferente depois que elas são movidas para armazenamento em disco gerenciado?

Não. Suas chamadas de API existentes continuarão a funcionar.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Todas as minhas imagens de VM seriam movidas para disco gerenciado ao mesmo tempo?

Vamos mover todas as suas imagens de VM no mesmo dia. Vamos notificá-lo assim que forem movidos.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Posso solicitar a movimentação das minhas imagens vm para mais tarde?

Recomendamos mover as imagens na data agendada. No entanto, se você tiver preocupações, por favor, entre em contato conosco para remarcar a mudança.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Posso publicar atualizações para minhas Imagens VM durante a mudança?

Atualizações das imagens VM não podem ser feitas durante a mudança.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>O processo de publicação mudará depois que minha imagem VM for movida para disco gerenciado?

Não, o processo de publicação permanecerá o mesmo. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>O editor pode mover suas ofertas para o Disco Gerenciado?

Não, os editores não podem mover suas ofertas para o Disco Gerenciado. Eles terão que esperar e suas imagens serão movidas automaticamente. Enviaremos notificações ao editor antes de fazer qualquer alteração.
