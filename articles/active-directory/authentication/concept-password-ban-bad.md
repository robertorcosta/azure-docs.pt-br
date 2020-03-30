---
title: Senhas proibidas dinamicamente - Azure Active Directory
description: Proibir senhas fracas do seu ambiente com senhas proibidas dinamicamente pelo Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263992"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminar senhas incorretas na organização

Os líderes do setor dizem para você não usar a mesma senha em vários lugares, para torná-la complexa e não torná-la simples como "Password123". Como as organizações podem garantir que seus usuários estejam seguindo as orientações recomendadas? Como eles podem garantir que os usuários não estejam usando senhas fracas ou mesmo variações em senhas fracas?

O passo inicial em ter senhas mais fortes é fornecer orientação aos seus usuários. A diretriz atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Diretrizes de Senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Ter uma boa orientação é importante, mas mesmo com isso sabemos que muitos usuários ainda vão acabar escolhendo senhas fracas. O Azure AD Password Protection protege sua organização detectando e bloqueando senhas fracas conhecidas e suas variantes, além de bloquear opcionalmente termos fracos adicionais específicos para sua organização.

Para obter mais informações sobre os esforços atuais de segurança, consulte o [Relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Lista de senhas proibidas globalmente

A equipe de proteção de identidade do Azure AD analisa constantemente os dados de telemetria de segurança Do Azure AD que procuram senhas fracas ou comprometidas comumente usadas, ou mais especificamente, os termos base fracos que muitas vezes são usados como base para senhas fracas. Quando esses termos fracos são encontrados, eles são adicionados à lista global de senhas banidas. O conteúdo da lista global de senhas banidas não é baseado em nenhuma fonte de dados externo. A lista global de senhas banidas é baseada inteiramente nos resultados contínuos da telemetria e análise de segurança do Azure AD.

Sempre que uma nova senha é alterada ou redefinida para qualquer usuário em qualquer inquilino no Azure AD, a versão atual da lista de senhas banidas global é usada como a entrada chave ao validar a força da senha. Essa validação resulta em senhas muito mais fortes para todos os clientes Azure AD.

> [!NOTE]
> Os criminosos cibernéticos também usam estratégias semelhantes em seus ataques. Portanto, a Microsoft não publica o conteúdo desta lista publicamente.

## <a name="custom-banned-password-list"></a>Lista personalizada de senhas banidas

Algumas organizações podem querer melhorar ainda mais a segurança adicionando suas próprias personalizações no topo da lista global de senhas banidas no que a Microsoft chama de lista de senhas banidas personalizadas. A Microsoft recomenda que os termos adicionados a esta lista sejam focados principalmente em termos específicos da organização, como:

- Marcas
- Nomes de produtos
- Locais (por exemplo, como sede da empresa)
- Termos internos específicos da empresa
- Abreviaturas que têm significado específico da empresa.

Uma vez que os termos sejam adicionados à lista de senhas proibidas personalizadas, eles serão combinados com os termos da lista global de senhas banidas ao validar senhas.

> [!NOTE]
> A lista de senhas proibidas personalizadas é limitada a ter um máximo de 1000 termos. Ele não foi projetado para bloquear listas extremamente grandes de senhas. A fim de aproveitar totalmente os benefícios da lista de senhas banidas personalizadas, a Microsoft recomenda que você primeiro revise e entenda o algoritmo de avaliação de senhas (veja [Como as senhas são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated)) antes de adicionar novos termos à lista proibida personalizada. Entender como o algoritmo funciona permitirá que sua empresa detecte e bloqueie eficientemente um grande número de senhas fracas e suas variantes.

Por exemplo: considere um cliente chamado "Contoso", que tem sede em Londres, e que faz um produto chamado "Widget". Para tal cliente, seria um desperdício, bem como menos seguro tentar bloquear variações específicas destes termos, tais como:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondonHQ"
- ... Etc

Em vez disso, é muito mais eficiente e seguro para bloquear apenas os termos-chave da base:

- "Contoso"
- "Londres"
- "Widget"

O algoritmo de validação de senha bloqueará automaticamente variantes fracas e combinações do acima.

A lista de senhas proibidas personalizada e a capacidade de habilitar a integração do Active Directory local são gerenciadas usando o portal do Azure.

![Modifique a lista de senhas banidas personalizadas em Métodos de Autenticação](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataques de spray de senha e listas de senhas comprometidas por terceiros

Um dos principais benefícios de proteção por senha do Azure AD é ajudá-lo a se defender contra ataques de spray de senha. A maioria dos ataques de spray de senha não tenta atacar nenhuma conta individual mais do que algumas vezes, uma vez que tal comportamento aumenta muito a probabilidade de detecção, seja através de bloqueio de conta ou outros meios. A maioria dos ataques de spray de senha, portanto, depende do envio de apenas um pequeno número das senhas mais fracas conhecidas contra cada uma das contas em uma empresa. Essa técnica permite que o invasor procure rapidamente por uma conta facilmente comprometida e, ao mesmo tempo, evite potenciais limiares de detecção.

A proteção por senha do Azure AD foi projetada para bloquear com eficiência todas as senhas fracas conhecidas que provavelmente serão usadas em ataques de spray de senha, com base em dados de telemetria de segurança do mundo real, como visto pelo Azure AD.  A Microsoft está ciente de sites de terceiros que enumeram milhões de senhas que foram comprometidas em violações de segurança conhecidas publicamente anteriormente. É comum que produtos de validação de senhas de terceiros sejam baseados em comparação de força bruta com esses milhões de senhas. A Microsoft acha que essas técnicas não são a melhor maneira de melhorar a força geral da senha, dadas as estratégias típicas usadas pelos atacantes de spray de senha.

> [!NOTE]
> A lista global de senhas banidas da Microsoft não é baseada em nenhuma fonte de dados de terceiros, incluindo listas de senhas comprometidas.

Embora a lista global banida da Microsoft seja pequena em comparação com algumas listas em massa de terceiros, seus efeitos de segurança são amplificados pelo fato de que ela é originada de telemetria de segurança do mundo real em ataques reais de spray de senha, além do fato de que a Microsoft algoritmo de validação de senha usa técnicas inteligentes de correspondência de difusos. O resultado final é que ele detectará e bloqueará com eficiência milhões das senhas fracas mais comuns de serem usadas em sua empresa. Os clientes que optam por adicionar termos específicos da organização à lista de senhas proibidas personalizadas também se beneficiam do mesmo algoritmo.

Informações adicionais sobre problemas de segurança baseados em senha podem ser revisadas no [Seu Pa$$word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos locais

A proteção de contas somente na nuvem é útil, mas muitas organizações mantêm cenários híbridos, incluindo o Windows Server Active Directory local. Os benefícios de segurança da proteção por senha do Azure AD também podem ser estendidos ao ambiente do Windows Server Active Directory através da instalação de agentes locais. Agora, usuários e administradores que alteram ou redefinem senhas no Active Directory são obrigados a cumprir a mesma política de senha que os usuários somente na nuvem.

## <a name="how-are-passwords-evaluated"></a>Como as senhas são avaliadas

Sempre que um usuário altera ou redefine sua senha, a nova senha é verificada para obter força e complexidade, validando-a contra a lista combinada de termos das listas de senhas proibidas globais e personalizadas (se esta última estiver configurada).

Mesmo que a senha do usuário contenha uma senha proibida, ela ainda poderá ser aceita se a senha geral for forte o suficiente em outros aspectos. Uma senha configurada recentemente passará pelas etapas a seguir para avaliar a força geral e determinar se ela deverá ser aceita ou rejeitada.

### <a name="step-1-normalization"></a>Passo 1: Normalização

Primeiro, uma nova senha passa por um processo de normalização. Essa técnica permite que um pequeno conjunto de senhas proibidas seja mapeado para um conjunto muito maior de senhas potencialmente fracas.

A normalização tem duas partes.  Primeiros, todas as letras maiúsculas são alteradas para letras minúsculas.  Por exemplo, as substituições de caractere comuns são realizadas, como:  

| Letra original  | Letra substituída |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

Exemplo: suponha que a senha "em branco" seja proibida e um usuário tente alterar a senha para “Bl@nK”. Mesmo que “Bl@nk” não seja especificamente proibida, o processo de normalização converterá essa senha como "em branco", que é uma senha banida.

### <a name="step-2-check-if-password-is-considered-banned"></a>Passo 2: Verifique se a senha é considerada proibida

#### <a name="fuzzy-matching-behavior"></a>Comportamento da correspondência difusa

Correspondência difusa é usada na senha normalizada para identificar se ela contém uma senha encontrada na lista de senhas banidas global ou personalizada. O processo de correspondência baseia-se em uma distância de edição de comparação de um (1).  

Exemplo: suponha que a senha “abcdef” seja proibida e um usuário tente alterar a senha para uma dos seguintes:

'abcdeg' *(último caractere alterado de 'f' para 'g')* 'abcdefg' *'(g' anexado ao fim)* 'abcde' *('f' foi excluído do fim)*

Cada uma das senhas acima não corresponde especificamente à senha banida "abcdef". No entanto, uma vez que cada exemplo está dentro de uma distância de edição de 1 do termo proibido 'abcdef', todos eles são considerados como um jogo para "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Correspondência de subcadeia de caracteres (em termos específicos)

A correspondência de subcadeia de caracteres é usada na senha normalizada para verificar o nome e o sobrenome do usuário, bem como o nome do locatário (observe que a correspondência de nome de locatário não é feita ao validar senhas em um controlador de domínio do Active Directory).

Exemplo: suponha que temos um usuário, Pol, que quer redefinir sua senha para "P0l123fb". Após a normalização, essa senha se tornaria "pol123fb". A correspondência de substring encontra que a senha contém o primeiro nome do usuário "Pol". Embora "P0l123fb" não estivesse especificamente na lista de senhas proibidas, a correspondência de substring encontrou "Pol" na senha. Portanto, essa senha deve ser rejeitada.

#### <a name="score-calculation"></a>Cálculo de pontuação

A próxima etapa é identificar todas as instâncias de senhas banidas na nova senha normalizada do usuário. Em seguida:

1. Cada senha banida encontrada na senha do usuário recebe um ponto.
2. Cada caractere exclusivo restante recebe um ponto.
3. Uma senha deve ser de pelo menos cinco (5) pontos para que ela seja aceita.

Para os próximos dois exemplos, vamos supor que a Contoso esteja usando a proteção de senha do Azure AD e tenha "contoso" em suas listas personalizadas. Vamos supor também que "em branco" está na lista global.

Exemplo: um usuário altera sua senha para “C0ntos0Blank12”

Após a normalização, essa senha se torna “contosoblank12”. O processo de correspondência localiza que essa senha contém duas senhas banidas: contoso e em branco. Essa senha então recebe uma pontuação:

[contoso] + [em branco] + [1] + [2] = 4 pontos Uma vez que esta senha está abaixo de cinco (5) pontos, ela será rejeitada.

Exemplo: um usuário altera a senha para "ContoS0Bl@nkf9!".

Após a normalização, essa senha se torna “contosoblankf9!”. O processo de correspondência localiza que essa senha contém duas senhas banidas: contoso e em branco. Essa senha então recebe uma pontuação:

[contoso] + [em branco] + [f] + [9] + [!] = 5 pontos Uma vez que esta senha é pelo menos cinco (5) pontos, ela é aceita.

   > [!IMPORTANT]
   > Observe que o algoritmo de senhas banidas junto com a lista global pode mudar e muda a qualquer momento no Azure com base em análise e pesquisa de segurança contínuas. Para o serviço de agente DC no local, os algoritmos atualizados só entrarão em vigor depois que o software do agente DC for reinstalado.

## <a name="license-requirements"></a>Requisitos de licença

|   | Proteção por senha do AD do Azure com a lista de senhas banidas global | Proteção por senha do AD do Azure com a lista de senhas banidas personalizado|
| --- | --- | --- |
| Usuários somente na nuvem | AD do Azure Gratuito | O Azure AD Premium P1 ou P2 |
| Os usuários sincronizados no local Windows Server Active Directory | O Azure AD Premium P1 ou P2 | O Azure AD Premium P1 ou P2 |

> [!NOTE]
> No local, os usuários do Windows Server Active Directory que não estão sincronizados com o Azure Active Directory também se beneficiam da proteção de senha do Azure AD com base no licenciamento existente para usuários sincronizados.

Informações adicionais sobre licenciamento, incluindo custos, podem ser encontradas no [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>O que os usuários veem

Quando um usuário tentar redefinir uma senha para alguma que seria proibida, a seguinte mensagem de erro será exibida:

Infelizmente, sua senha contém uma palavra, uma frase ou um padrão que pode ser facilmente adivinhado. Tente novamente com uma senha diferente.

## <a name="next-steps"></a>Próximas etapas

- [Configurar a lista de senhas proibidas personalizada](howto-password-ban-bad.md)
- [Habilitar agentes de proteção por senha do Azure AD local](howto-password-ban-bad-on-premises-deploy.md)
