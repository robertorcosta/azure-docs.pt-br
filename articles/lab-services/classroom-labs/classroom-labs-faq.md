---
title: Laboratórios de sala de aula no Azure Lab Services – Perguntas frequentes | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre os laboratórios de sala de aula no Azure Lab Services.
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
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: 11cb0d995537b8979c3727bf508045c6e31fc208
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592603"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Laboratórios de sala de aula no Azure Lab Services – Perguntas frequentes
Obtenha respostas para algumas das perguntas mais comuns sobre laboratórios de sala de aula no Azure Lab Services. 

## <a name="quotas"></a>Cotas

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>A cota é por usuário ou por semana ou por toda a duração do laboratório? 
A cota que você define para um laboratório é para cada aluno para toda a duração do laboratório. E, o [tempo de execução agendado de VMs](how-to-create-schedules.md) não conta como a cota alocada a um usuário. A cota é para o tempo fora dos horários agendados que um aluno passa em VMs.  Para obter mais informações sobre cotas, confira [Definir cotas para usuários](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Se o educador ativar uma VM de estudante, isso afetará a cota do aluno? 
Não. Não. Quando o professor liga a VM do aluno, ela não afeta a cota alocada para o aluno. 

## <a name="schedules"></a>Agendas

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Todas as VMs no laboratório são iniciadas automaticamente quando um agendamento é definido? 
Não. Nem todas as VMs. Somente as VMs que são atribuídas aos usuários em um agendamento. As VMs que não estão atribuídas a um usuário não são iniciadas automaticamente. Isso faz parte do design. 

## <a name="lab-accounts"></a>Contas de laboratório

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Por que não consigo criar um laboratório devido à indisponibilidade do intervalo de endereços? 
Os laboratórios de sala de aula podem criar VMs de laboratório dentro de um intervalo de endereços IP especificado quando sua conta de laboratório é criada no portal do Azure. Quando um intervalo de endereços é fornecido, cada laboratório criado depois de serem alocados 512 endereços IP para VMs de laboratório. O intervalo de endereços para a conta do laboratório deve ser grande o suficiente para acomodar todos os laboratórios que você pretende criar na conta do laboratório. 

Por exemplo, se você tiver um bloco de /19 - 10.0.0.0/19, esse intervalo de endereços acomoda 8192 endereços IP e 16 laboratórios (8192/512 = 16 laboratórios). Nesse caso, a criação de laboratório falha na décima sétima criação de laboratório.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quais intervalos de porta devo abrir na configuração de firewall da minha organização para conexão a máquinas virtuais de laboratório via RDP/SSH?

As portas são: 49152 a 65535. Os laboratórios de sala de aula ficam atrás de um balanceador de carga. Cada laboratório tem um único endereço IP público e cada máquina virtual no laboratório tem uma porta exclusiva. 

Você também pode ver o endereço IP privado de cada máquina virtual na guia **Pool de máquinas virtuais** da página inicial para o laboratório no portal do Azure. Se você republicar um laboratório, o endereço IP público do laboratório não será alterado, mas o IP privado e o número da porta de cada máquina virtual no laboratório poderão mudar. Saiba mais no artigo: [Configurações de firewall para o Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Qual intervalo de endereços IP públicos devo abrir na configuração de firewall da minha organização para conexão a máquinas virtuais de laboratório via RDP/SSH?
Consulte [Intervalos de IP do Azure e marcas de serviço – Nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519), que fornece o intervalo de endereços IP públicos para datacenters no Azure. Você pode abrir os endereços IP para as regiões nas quais estão as suas contas de laboratório.

## <a name="virtual-machine-images"></a>Imagens de máquinas virtuais

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Como um criador de laboratório, por que não é possível habilitar opções de imagem adicionais no menu suspenso de imagens de máquinas virtuais ao criar um novo laboratório?

Quando um administrador adiciona você como um criador de laboratório a uma conta de laboratório, você recebe as permissões para criar laboratórios. Mas, você não tem as permissões para editar as configurações dentro da conta do laboratório, incluindo a lista de imagens de máquinas virtuais habilitadas. Para habilitar imagens adicionais, entre em contato com o administrador da conta do laboratório para fazer isso ou peça ao administrador para adicioná-lo com uma função de Colaborador à conta do laboratório. A função Colaborador fornecerá as permissões para editar a lista de imagens de máquinas virtuais na conta do laboratório.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>Posso anexar discos adicionais a uma máquina virtual?
Não. Não é possível anexar discos adicionais a uma VM em um laboratório de sala de aula. 

## <a name="users"></a>Usuários

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Quantos usuários podem estar em um laboratório de sala de aula?
Você pode adicionar até 400 usuários a um laboratório de sala de aula. 

## <a name="blog-post"></a>Postagem no blog
Assine o [blog do Azure Lab Services](https://aka.ms/azlabs-blog).

## <a name="update-notifications"></a>Atualizar notificações
Assine as [atualizações do Lab Services](https://azure.microsoft.com/updates/?product=lab-services) para se manter informado sobre os novos recursos no Lab Services.

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?
Caso sua pergunta não esteja listada aqui, fale conosco e nós o ajudaremos a encontrar uma resposta.

- Poste uma pergunta no final destas Perguntas Frequentes. 
- Para alcançar um público maior, poste uma pergunta no [Azure Lab Services – Fórum da comunidade técnica](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices). 
- Para solicitações de recurso, envie solicitações e ideias para o [Azure Lab Services – Opinião do usuário](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

