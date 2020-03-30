---
title: Autorize o acesso com uma assinatura de acesso compartilhado no Azure Event Hubs
description: Este artigo fornece informações sobre a autorização do acesso aos recursos do Azure Event Hubs usando assinaturas de acesso compartilhado (SAS).
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992790"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorizando o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado
Uma assinatura de acesso compartilhado (SAS) fornece uma maneira de conceder acesso limitado aos recursos em seu namespace do Event Hubs. O SAS protege o acesso aos recursos do Event Hubs com base nas regras de autorização. Essas regras são configuradas em um namespace ou em uma entidade (hub de eventos ou tópico). Este artigo fornece uma visão geral do modelo SAS e revisa as práticas recomendadas da SAS.

## <a name="what-are-shared-access-signatures"></a>Quais são as assinaturas de acesso compartilhada?
Uma assinatura de acesso compartilhado (SAS) fornece acesso delegado aos recursos do Event Hubs com base nas regras de autorização. Uma regra de autorização tem nome, está associada a direitos específicos e executa um par de chaves de criptografia. Você usa o nome e a chave da regra através dos clientes do Event Hubs ou em seu próprio código para gerar tokens SAS. Um cliente pode então passar o token para event Hubs para provar a autorização para a operação solicitada.

O SAS é um mecanismo de autorização baseado em sinistros usando tokens simples. Usando SAS, chaves nunca são passadas na conexão. As chaves são usadas para assinar criptograficamente informações que posteriormente podem ser verificadas pelo serviço. A SAS pode ser usada como esquema de nome de usuário e senha no qual o cliente está em posse imediata de um nome de regra de autorização e uma chave correspondente. O SAS pode ser usado semelhante a um modelo de segurança federado, onde o cliente recebe um token de acesso limitado e assinado por tempo limitado de um serviço de token de segurança sem nunca ter entrado em posse da chave de assinatura.

> [!NOTE]
> O Azure Event Hubs suporta autorizar a autorização para os recursos do Event Hubs usando o Azure Active Directory (Azure AD). Autorizar usuários ou aplicativos usando o token OAuth 2.0 devolvido pelo Azure AD proporciona maior segurança e facilidade de uso sobre assinaturas de acesso compartilhado (SAS). Com o Azure AD, não há necessidade de armazenar os tokens em seu código e arriscar possíveis vulnerabilidades de segurança.
>
> A Microsoft recomenda o uso do Azure AD com seus aplicativos Azure Event Hubs quando possível. Para obter mais informações, consulte [Autorizar o acesso ao recurso Azure Event Hubs usando o Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Os tokens SAS (Assinaturas de Acesso Compartilhado) são essenciais para proteger seus recursos. Ao mesmo tempo em que fornece granularidade, o SAS concede aos clientes acesso aos recursos do Event Hubs. Eles não devem ser compartilhados publicamente. Ao compartilhar, se necessário para solucionar problemas, considere usar uma versão reduzida de qualquer arquivo de log ou excluir os tokens SAS (se presentes) dos arquivos de log e certifique-se de que as capturas de tela não contenham as informações do SAS também.

## <a name="shared-access-authorization-policies"></a>Políticas de autorização de acesso compartilhado
Cada namespace do Event Hubs e cada entidade do Event Hubs (uma instância do hub de eventos ou um tópico Kafka) tem uma política de autorização de acesso compartilhado composta por regras. A política no nível de namespace se aplica a todas as entidades dentro do namespace, independentemente de suas configurações de política individuais.
Para cada regra de política de autorização, você toma decisões quanto a três informações: nome, escopo e direitos. O nome é um nome único nesse escopo. O escopo é o URI do recurso em questão. Para um namespace do Event Hubs, o escopo é o nome `https://<yournamespace>.servicebus.windows.net/`de domínio totalmente qualificado (FQDN), como .

Os direitos previstos na regra da política podem ser uma combinação de:
- **Enviar** – Dá o direito de enviar mensagens para a entidade
- **Ouça** – Dá o direito de ouvir ou receber à entidade
- **Gerenciar** – Dá o direito de gerenciar a topologia do namespace, incluindo criação e exclusão de entidades

> [!NOTE]
> O **direito gerenciar** inclui os direitos de **Envio** e **Escuta.**

Um namespace ou uma política de entidade pode conter até 12 regras de autorização de acesso compartilhado, dando espaço para os três conjuntos de regras, cada um cobrindo os direitos básicos e a combinação de Enviar e Ouvir. Esse limite sublinha que a loja de políticas SAS não se destina a ser uma loja de conta de usuário ou serviço. Se seu aplicativo precisar conceder acesso aos recursos do Event Hubs com base em identidades de usuários ou serviços, ele deve implementar um serviço de token de segurança que emite tokens SAS após uma verificação de autenticação e acesso.

Uma regra de autorização é atribuída a uma **chave primária** e uma **chave secundária**. Essas chaves são chaves criptograficamente fortes. Não os perca ou vaze. Eles sempre estarão disponíveis no portal Azure. Você pode usar qualquer uma das chaves geradas e pode gerá-las novamente a qualquer momento. Se você gerar novamente ou alterar uma chave na política, todos os tokens emitidos anteriormente com base na chave tornam-se inválidos instantaneamente. No entanto, conexões contínuas criadas com base em tais tokens continuarão funcionando até o token expirar.

Quando você cria um namespace do Event Hubs, uma regra de diretiva chamada **RootManageSharedAccessKey** é criada automaticamente para o namespace. Esta política **tem permissões de gerenciamento** para todo o namespace. Recomenda-se que você trate essa regra como uma conta raiz administrativa e não a use em sua aplicação. Você pode criar regras adicionais de diretiva na guia **Configurar** para o namespace no portal, via PowerShell ou Azure CLI.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS
Ao usar assinaturas de acesso compartilhado nos seus aplicativos, você precisará estar ciente de dois riscos possíveis:

- Se um SAS for vazado, ele pode ser usado por qualquer um que obtenha, o que pode comprometer potencialmente os recursos do Event Hubs.
- Se um SAS fornecido a um aplicativo cliente expirar e o aplicativo não conseguir recuperar um novo SAS do seu serviço, então a funcionalidade do aplicativo pode ser prejudicada.

As recomendações a seguir para usar assinaturas de acesso compartilhado podem ajudar a atenuar esses riscos:

- **Que os clientes renovem automaticamente o SAS, se necessário:** Os clientes devem renovar o SAS bem antes do vencimento, para dar tempo de repetições se o serviço que fornece o SAS não estiver disponível. Se o seu SAS deve ser usado para um pequeno número de operações imediatas e de curta duração que devem ser concluídas dentro do prazo de validade, então pode ser desnecessário, pois não se espera que o SAS seja renovado. No entanto, se você tiver um cliente que está sempre fazendo solicitações via SAS, surge a possibilidade de expiração. A principal consideração é equilibrar a necessidade de o SAS ter vida curta (como dito anteriormente) com a necessidade de garantir que o cliente esteja solicitando a renovação antecipadamente (para evitar a interrupção devido ao vencimento do SAS antes de uma renovação bem-sucedida).
- **Tenha cuidado com o tempo de início do SAS**: Se você definir a hora de início do SAS para **agora,** então devido à distorção do relógio (diferenças no tempo atual de acordo com diferentes máquinas), as falhas podem ser observadas intermitentemente durante os primeiros minutos. Em geral, defina a hora de início para pelo menos 15 minutos no passado. Ou, não defina em tudo, o que vai torná-lo válido imediatamente em todos os casos. O mesmo geralmente se aplica ao tempo de validade também. Lembre-se de que você pode observador até 15 minutos de inclinação do relógio em qualquer direção em qualquer solicitação. 
- **Seja específico com o recurso a ser acessado**: Uma prática recomendada de segurança é fornecer ao usuário os privilégios mínimos necessários. Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda-lhe acesso de leitura a essa única entidade, e não acesso de leitura/gravação/exclusão a todas as entidades. Também ajuda a diminuir o dano se um SAS for comprometido porque o SAS tem menos poder nas mãos de um invasor.
- **Nem sempre use SAS**: Às vezes, os riscos associados a uma determinada operação contra seus Hubs de Eventos superam os benefícios do SAS. Para tais operações, crie um serviço intermediário que grava em seus Hubs de Eventos após a validação, autenticação e auditoria das regras de negócios.
- **Use sempre HTTPs**: Use sempre https para criar ou distribuir um SAS. Se um SAS for passado por HTTP e interceptado, um invasor executando um anexo man-in-the-middle é capaz de ler o SAS e usá-lo da forma como o usuário pretendido poderia ter, potencialmente comprometendo dados confidenciais ou permitindo a corrupção de dados pelo usuário mal-intencionado.

## <a name="conclusion"></a>Conclusão
As assinaturas de acesso ao compartilhamento são úteis para fornecer permissões limitadas aos recursos do Event Hubs para seus clientes. Eles são parte vital do modelo de segurança para qualquer aplicativo usando o Azure Event Hubs. Se você seguir as melhores práticas listadas neste artigo, você pode usar o SAS para fornecer maior flexibilidade de acesso aos seus recursos, sem comprometer a segurança do seu aplicativo.

## <a name="next-steps"></a>Próximas etapas
Veja os seguintes artigos relacionados: 

- [Autenticar solicitações ao Azure Event Hubs a partir de um aplicativo usando o Azure Active Directory](authenticate-application.md)
- [Autenticar uma identidade gerenciada com o Azure Active Directory para acessar recursos do Event Hubs](authenticate-managed-identity.md)
- [Autenticar solicitações ao Azure Event Hubs usando assinaturas de acesso compartilhadas](authenticate-shared-access-signature.md)
- [Autorize o acesso aos recursos do Event Hubs usando o Azure Active Directory](authorize-access-azure-active-directory.md)


