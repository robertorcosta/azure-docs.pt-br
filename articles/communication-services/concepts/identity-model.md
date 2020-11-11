---
title: Modelo de identidade
titleSuffix: An Azure Communication Services concept
description: Saiba mais sobre as identidades e tokens de acesso
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 336e708334778e107331f5f393476c4b3dbb98a3
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507422"
---
# <a name="identity-model"></a>Modelo de identidade

Os serviços de comunicação do Azure são serviços independentes de identidade. Esse design tem vários benefícios:
- Reutilizar identidades existentes do seu sistema de gerenciamento de identidade
- Flexibilidade para cenários de integração
- Mantém suas identidades privadas para os serviços de comunicação do Azure

Em vez de duplicar as informações existentes no seu sistema, você manterá a relação de mapeamento específica ao seu caso comercial. Por exemplo, mapeamento de identidades 1:1, 1: N, N:1, N:M. Identificadores externos (como números de telefone, usuários, dispositivos, aplicativos, GUID) não podem ser usados como identidade de comunicação do Azure. Os tokens de acesso gerados para a identidade do serviço de comunicação do Azure são usados para acessar primitivos como chat ou chamada. 

## <a name="identity"></a>Identidade

As identidades são criadas com a biblioteca de administração do serviço de comunicação do Azure. A identidade serve como identificador nas conversas e é usada para a criação de tokens de acesso. A mesma identidade pode participar de várias sessões simultâneas em vários dispositivos. A identidade pode ter vários tokens de acesso ativos ao mesmo tempo. A exclusão de identidade, recurso ou assinatura causa invalidação de todos os seus tokens de acesso e a exclusão de todos os dados que são armazenados para essa identidade. A identidade excluída não pode emitir novos tokens de acesso, nem acessar dados armazenados anteriormente (por exemplo, mensagens de chat). 

Você não é cobrado pelo número de identidades que tem, mas pelo uso de primitivos. O número de identidades não precisa ser restringido, como mapear as identidades de seu aplicativo para as identidades dos serviços de comunicação do Azure. Com a liberdade de mapeamento vem a responsabilidade nos termos de privacidade. Quando o usuário do seu aplicativo quiser ser excluído do seu sistema, você precisará excluir todas as identidades que foram associadas a esse usuário.

Os serviços de comunicação do Azure não fornecem identidades especiais para usuários anônimos. Ele não mantém o mapeamento entre os usuários e as identidades, mas não sabe se a identidade é anônima. Você pode criar o conceito para atender às suas necessidades. Nossa recomendação é criar uma nova identidade para o usuário anônimo de cada aplicativo. Com a posse do token de acesso válido, qualquer pessoa pode obter acesso ao conteúdo de identidade não excluída. Por exemplo, mensagens de chat enviadas pelo usuário. O acesso é restrito apenas a escopos, que fazem parte do token de acesso. Mais detalhes sobre escopos estão na seção *token de acesso*.

### <a name="mapping-of-identities"></a>Mapeamento de identidades

Os serviços de comunicação do Azure não estão replicando a funcionalidade do IMS. Ele não fornece uma maneira para os clientes usarem identidades específicas do cliente. Por exemplo, número de telefone ou endereço de email. Em vez disso, ele fornece identificadores exclusivos que você pode atribuir às identidades do seu aplicativo. Os serviços de comunicação do Azure não armazenam qualquer tipo de informação, que pode revelar a identidade real dos usuários.

Em vez de duplicar, você é incentivado a projetar, como os usuários do seu domínio de identidade serão mapeados para identidades do serviço de comunicação do Azure. Você pode seguir qualquer tipo de padrão 1:1, 1: N, N:1 ou M:N. Você pode decidir se um único usuário está mapeado para identidade única ou para várias identidades. Quando uma nova identidade é criada, você é incentivado a armazenar o mapeamento dessa identidade para o usuário ou os usuários do seu aplicativo. Como as identidades exigem os tokens de acesso para uso dos primitivos, a identidade precisa ser conhecida para o usuário ou os usuários do seu aplicativo.

Se você estiver usando um banco de dados relacional para armazenamento de usuários, a implementação poderá ser diferente com base no seu cenário de mapeamento. Para cenários com mapeamento 1:1 ou N:1, você pode adicionar uma coluna *CommunicationServicesId* à tabela para armazenar sua identidade dos serviços de comunicação do Azure. Em cenários com a relação 1: N ou N:M, você pode considerar a criação de uma tabela separada no banco de dados relacional.

## <a name="access-token"></a>Token de acesso

O token de acesso é um token JWT que pode ser usado para obter acesso aos primitivos do serviço de comunicação do Azure. O token de acesso emitido tem proteção de integridade, suas declarações não podem ser alteradas após a emissão. Ou seja, a alteração manual de propriedades, como identidade, expiração ou escopos, tornará o token de acesso inválido. O uso de primitivos com Tokens invalidados levará à negação do acesso ao primitivo. 

As propriedades do token de acesso são: *identidade, expiração* e *escopos*. O token de acesso é sempre válido por 24 horas. Após esse tempo, o token de acesso é invalidado e não pode ser usado para acessar nenhum primitivo. A identidade deve ter uma forma, como solicitar novo token de acesso do serviço do servidor. O *escopo* do parâmetro define um conjunto não vazio de primitivos, que pode ser usado. Os serviços de comunicação do Azure dão suporte aos seguintes escopos para tokens de acesso:

|Name|Descrição|
|---|---|
|Chat|  Concede a capacidade de participar de um chat|
|VoIP|  Concede a capacidade de chamar identidades e números de telefone|


Se você quiser revogar o token de acesso antes de sua expiração, poderá usar a biblioteca de administração do serviço de comunicação do Azure para fazer isso. A revogação do token não é imediata e leva até 15 minutos para ser propagada. A remoção da identidade, do recurso ou da assinatura causará a revogação de todos os tokens de acesso. Se você quiser remover a capacidade do usuário de acessar a funcionalidade específica, revogue todos os tokens de acesso. Em seguida, emita um novo token de acesso com um conjunto mais limitado de escopos.
A rotação de chaves de acesso do serviço de comunicação do Azure causará a revogação de todos os tokens de acesso ativos que foram criados com a chave de acesso anterior. Todas as identidades perderão o acesso ao serviço de comunicação do Azure e serão necessárias para emitir novos tokens de acesso. 

É recomendável emitir tokens de acesso em seu serviço do lado do servidor e não no aplicativo do cliente. O raciocínio é que a emissão exige a chave de acesso ou a identidade gerenciada. Não é recomendável por motivos de segurança para compartilhar as chaves de acesso com o aplicativo do cliente. O aplicativo cliente deve usar um ponto de extremidade de serviço confiável que possa autenticar seus clientes e emitir o token de acesso em seu nome. Mais detalhes sobre a arquitetura podem ser encontrados [aqui](./client-and-server-architecture.md).

Se você armazenar em cache tokens de acesso a um repositório de backup, recomendamos o uso da criptografia. O token de acesso é de dados confidenciais e pode ser usado para atividades mal-intencionadas se não estiver protegido. Com a posse do token de acesso, você pode inicializar o SDK e obter acesso à API. A API acessível é restrita somente com base em escopos, que o token de acesso tem. É recomendável emitir tokens de acesso somente com escopos, que são necessários.