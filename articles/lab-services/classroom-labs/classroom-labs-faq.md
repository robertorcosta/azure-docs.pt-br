---
title: Laboratórios de sala de aula no Azure Lab Services — FAQ | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes (FAQ) sobre laboratórios de sala de aula no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443492"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Laboratórios de sala de aula no Azure Lab Services — Perguntas frequentes (FAQ)
Obtenha respostas para algumas das perguntas mais comuns sobre laboratórios de sala de aula no Azure Lab Services. 

## <a name="quotas"></a>Cotas

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>A cota por usuário ou por semana ou por toda a duração do laboratório? 
A cota que você estabeleceu para um laboratório é para cada aluno durante toda a duração do laboratório. E, o [tempo de execução programado das VMs](how-to-create-schedules.md) não conta com a cota atribuída a um usuário. A cota é para o tempo fora dos horários agendados que um aluno passa em VMs.  Para obter mais informações sobre cotas, consulte [Definir cotas para usuários](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Se o professor liga um VM estudantil, isso afeta a cota de alunos? 
Não. Não é assim. Quando o professor liga a VM do aluno, isso não afeta a cota atribuída ao aluno. 

## <a name="schedules"></a>Agendas

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Todas as VMs do laboratório começam automaticamente quando um horário é definido? 
Não. Nem todas as VMs. Apenas as VMs que são atribuídas aos usuários em um cronograma. As VMs que não são atribuídas a um usuário não são iniciadas automaticamente. É por design. 

## <a name="lab-accounts"></a>Contas de laboratório

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Por que não consigo criar um laboratório por causa da indisponibilidade do intervalo de endereços? 
Os laboratórios de sala de aula podem criar VMs de laboratório dentro de um intervalo de endereços IP que você especifica ao criar sua conta de laboratório no portal Azure. Quando um intervalo de endereços é fornecido, cada laboratório que é criado depois de ter atribuído 512 endereços IP para VMs de laboratório. O intervalo de endereços para a conta do laboratório deve ser grande o suficiente para acomodar todos os laboratórios que você pretende criar a conta do laboratório. 

Por exemplo, se você tiver um bloco de /19 - 10.0.0.0/19, esta faixa de endereços acomoda 8192 endereços IP e 16 laboratórios (8192/512 = 16 laboratórios). Neste caso, a criação de laboratório falha na criação do 17º laboratório.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quais faixas de porta devo abrir na configuração de firewall da minha organização para me conectar a máquinas virtuais do Laboratório via RDP/SSH?

As portas são: 49152-65535. Os laboratórios da sala de aula estão atrás de um equilibrador de carga. Cada laboratório tem um único endereço IP público e cada máquina virtual no laboratório tem uma porta única. 

Você também pode ver o endereço IP privado de cada máquina virtual na guia pool de **máquinas virtuais** da página inicial para laboratório no portal Azure. Se você republicar um laboratório, o endereço IP público do laboratório não mudará, mas o IP privado e o número da porta de cada máquina virtual no laboratório podem mudar. Você pode saber mais no artigo: [Configurações de firewall para o Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Que faixa pública de endereçoip devo abrir nas configurações de firewall da minha organização para me conectar a máquinas virtuais do Lab via RDP/SSH?
Consulte [Azure IP Ranges and Service Tags — Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519), que fornece a gama pública de endereços IP para data centers no Azure. Você pode abrir os endereços IP para as regiões onde estão suas contas de laboratório.

## <a name="virtual-machine-images"></a>Imagens de máquinas virtuais

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Como criador de laboratório, por que não posso habilitar opções adicionais de imagem na lista de imagens de máquinas virtuais ao criar um novo laboratório?

Quando um administrador adiciona você como um criador de laboratório a uma conta de laboratório, você recebe as permissões para criar laboratórios. Mas, você não tem as permissões para editar quaisquer configurações dentro da conta do laboratório, incluindo a lista de imagens de máquinas virtuais habilitadas. Para habilitar imagens adicionais, entre em contato com o administrador da conta do laboratório para fazê-lo para você ou peça ao administrador para adicioná-lo como um papel contribuinte à conta do laboratório. A função Contribuinte lhe dará as permissões para editar a lista de imagens da máquina virtual na conta do laboratório.

## <a name="users"></a>Usuários

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Quantos usuários podem estar em um laboratório de sala de aula?
Você pode adicionar até 400 usuários a um laboratório de sala de aula. 

## <a name="blog-post"></a>Postagem no blog
Inscreva-se no [blog Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Notificações de atualização
Inscreva-se nas [atualizações do Lab Services](https://azure.microsoft.com/updates/?product=lab-services) para ficar informado sobre novos recursos no Lab Services.

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?
Se sua pergunta não estiver listada aqui, avise-nos, para que possamos ajudá-lo a encontrar uma resposta.

- Poste uma pergunta no final destas Perguntas Frequentes. 
- Para alcançar um público mais amplo, poste uma pergunta no [Azure Lab Services — Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Para solicitações de recursos, envie suas solicitações e ideias para [o Azure Lab Services — User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

