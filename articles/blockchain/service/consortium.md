---
title: Consórcio Azure Blockchain Service
description: Visão geral de como o serviço Azure Blockchain implementa redes blockchain de consórcio.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247612"
---
# <a name="azure-blockchain-service-consortium"></a>Consórcio Azure Blockchain Service

Usando o Azure Blockchain Service, você pode criar redes blockchain de consórcio privado onde cada rede blockchain pode ser limitada a participantes específicos da rede. Apenas os participantes da rede blockchain de consórcio privado podem visualizar e interagir com o blockchain. As redes de consórcio no Azure Blockchain Service podem conter dois tipos de funções participantes:

* **Administrador** - Participantes privilegiados que podem tomar ações de gestão de consórcios e podem participar de transações blockchain.

* **Usuário** - Participantes que não podem tomar qualquer ação de gestão de consórcio, mas podem participar de transações blockchain.

As redes de consórcio podem ser uma mistura de funções participantes e podem ter um número arbitrário de cada tipo de função. Deve haver pelo menos um administrador.

O diagrama a seguir mostra uma rede de consórciocom vários participantes:

![Diagrama de rede de consórcio privado](./media/consortium/network-diagram.png)

Com o gerenciamento de consórcios no Azure Blockchain Service, você pode gerenciar os participantes da rede de consórcios. A gestão do consórcio baseia-se no modelo de consenso da rede. Na versão de pré-visualização atual, o Azure Blockchain Service fornece um modelo de consenso centralizado para o gerenciamento de consórcios. Qualquer participante privilegiado com um papel de administração pode assumir ações de gestão de consórcios, como adicionar ou remover participantes de uma rede.

## <a name="roles"></a>Funções

Os participantes de um consórcio podem ser indivíduos ou organizações e podem ser designados uma função de usuário ou uma função de administrador. A tabela a seguir lista as diferenças de alto nível entre os dois papéis:

| Ação | Função de usuário | Função de administrador
|--------|:----:|:------------:|
| Criar um novo membro | Sim | Sim |
| Convide novos membros | Não | Sim |
| Definir ou alterar a função participante do membro | Não | Sim |
| Alterar o nome de exibição do membro | Só para o próprio membro | Só para o próprio membro |
| Remover membros | Só para o próprio membro | Sim |
| Participe de transações blockchain | Sim | Sim |

### <a name="user-role"></a>Função de usuário

Os usuários são participantes do consórcio sem recursos de administrador. Eles não podem participar na gestão de membros relacionados ao consórcio. Os usuários podem alterar seu nome de exibição de membro e podem se remover de um consórcio.

### <a name="administrator"></a>Administrador

Um administrador pode gerenciar membros dentro do consórcio. Um administrador pode convidar membros, remover membros ou atualizar funções de membros dentro do consórcio.
Deve haver sempre pelo menos um administrador dentro de um consórcio. O último administrador deve especificar outro participante como um papel de administrador antes de sair de um consórcio.

## <a name="managing-members"></a>Membros gerenciais

Apenas os administradores podem convidar outros participantes para o consórcio. Os administradores convidam os participantes usando seu ID de assinatura do Azure.

Uma vez convidados, os participantes podem se juntar ao consórcio blockchain implantando um novo membro no Azure Blockchain Service. Para visualizar e participar do consórcio convidado, você deve especificar o mesmo ID de assinatura do Azure usado no convite pelo administrador da rede.

Os administradores podem remover qualquer participante do consórcio, incluindo outros administradores. Os membros só podem se retirar de um consórcio.

## <a name="consortium-management-smart-contract"></a>Contrato inteligente de gestão de consórcios

A gestão de consórcios no Azure Blockchain Service é feita através de contratos inteligentes de gerenciamento de consórcio. Os contratos inteligentes são automaticamente implantados em seus nós quando você implanta um novo membro blockchain.

O endereço do contrato inteligente de gestão de consórcio raiz pode ser visto no portal Azure. O **endereço RootContract** está na seção de visão geral do membro blockchain.

![Endereço RootContract](./media/consortium/rootcontract-address.png)

Você pode interagir com o contrato inteligente de gerenciamento de consórcio usando o [módulo PowerShell](manage-consortium-powershell.md)de gerenciamento de consórcio , portal Azure, ou diretamente através do contrato inteligente usando a conta Ethereum gerada pelo Azure Blockchain Service.

## <a name="ethereum-account"></a>Conta Ethereum

Quando um membro é criado, uma chave de conta Ethereum é criada. O Azure Blockchain Service usa a chave para criar transações relacionadas ao gerenciamento de consórcios. A chave da conta Ethereum é gerenciada automaticamente pelo Azure Blockchain Service.

A conta do membro pode ser vista no portal Azure. A conta do membro está na seção de visão geral do membro blockchain.

![Conta do membro](./media/consortium/member-account.png)

Você pode redefinir sua conta do Ethereum clicando na sua conta de membro e digitando uma nova senha. Tanto o endereço da conta Ethereum quanto a senha serão redefinidos.  

## <a name="next-steps"></a>Próximas etapas

As ações de gestão do consórcio podem ser acessadas através do PowerShell. Para obter mais informações, consulte [Gerenciar membros do consórcio no Azure Blockchain Service usando o PowerShell](manage-consortium-powershell.md).
