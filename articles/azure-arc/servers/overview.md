---
title: Visão geral do Azure Arc para servidores (versão prévia)
description: Saiba como usar o Azure Arc para servidores para gerenciar computadores que estão hospedados fora do Azure como se fosse um recurso do Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: e775945526a5453085946ed4eea2a2e19761ba78
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482183"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>O que é o Azure Arc para servidores (versão prévia)?

O Azure Arc para servidores (versão prévia) permite que você gerencie computadores Windows e Linux hospedados fora do Azure em sua rede corporativa ou em outro provedor de nuvem, semelhante à maneira como você gerencia máquinas virtuais do Azure nativas. Quando um computador híbrido é conectado ao Azure, ele se torna um computador conectado e é tratado como um recurso no Azure. Cada computador conectado tem uma ID de Recurso, é gerenciado como parte de um grupo de recursos dentro de uma assinatura e se beneficia de constructos padrão do Azure, tais como o Azure Policy e a aplicação de marcas.

Para oferecer essa experiência com seus computadores híbridos hospedados fora do Azure, o agente do Azure Connected Machine precisa ser instalado em cada computador que você planeja conectar ao Azure. Esse agente não oferece nenhuma outra funcionalidade e não substitui o [agente do Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-intro.md).

>[!NOTE]
>Essa versão prévia destina-se a fins de avaliação. Recomendamos que você não gerencie computadores de produção críticos.
>

## <a name="supported-scenarios"></a>Cenários com suporte

O Azure Arc para servidores (versão prévia) é compatível com os seguintes cenários com computadores conectados:

- Atribua [configurações de convidado do Azure Policy](../../governance/policy/concepts/guest-configuration.md) usando a mesma experiência que a atribuição de política para máquinas virtuais do Azure.
- Dados de log coletados pelo agente do Log Analytics, armazenados no workspace do Log Analytics em que o computador está registrado. Os dados de log do computador híbrido agora contêm propriedades específicas do computador, como uma ID de recurso, que podem ser usadas para dar suporte a acesso ao log pelo [contexto de recurso](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Regiões com suporte

Com o Azure Arc para servidores (versão prévia), só há suporte a determinadas regiões:

- EastUS
- WestUS2
- WestEurope
- SoutheastAsia

Na maioria dos casos, o local selecionado durante a criação do script de instalação deve ser a região do Azure mais próxima à localização do seu computador. Os dados em repouso serão armazenados na geografia do Azure que contém a região especificada por você, o que também pode afetar sua escolha de região se você tiver requisitos de residência de dados. Se a região do Azure a qual seu computador está conectado for afetada por uma interrupção, a máquina conectada não será afetada, mas talvez as operações de gerenciamento que usam o Azure não possam terminar. Para obter resiliência no caso de uma interrupção regional, se houver várias localizações que fornecem um serviço com redundância geográfica, será melhor conectar os computadores em cada localização a uma região diferente do Azure.

### <a name="agent-status"></a>Status do agente

O agente do Connected Machine envia uma mensagem de pulsação regular para o serviço a cada 5 minutos. Se o serviço parar de receber essas mensagens de pulsação de um computador, esse computador será considerado offline e o status será alterado automaticamente para **Desconectado** no portal no período de 15 a 30 minutos. Ao receber uma mensagem de pulsação subsequente do agente do Connected Machine, o status dela será alterado automaticamente para **Conectado**.

## <a name="next-steps"></a>Próximas etapas

Antes de avaliar ou habilitar o Arc para servidores (versão prévia) em vários computadores híbridos, examine o artigo [Visão geral do agente do Computador Conectado](agent-overview.md) para entender o que é necessário, os detalhes técnicos sobre o agente e os métodos de implantação.
