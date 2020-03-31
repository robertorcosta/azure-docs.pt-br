---
title: Proteção por senha do Azure AD - Azure Active Directory
description: Banir senhas fracas no Active Directory no local usando a proteção por senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848639"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Impor a proteção por senha do Azure AD para o Active Directory do Windows Server

A proteção por senha do Azure AD é um recurso que melhora as políticas de senha em uma organização. A implantação local de proteção por senha usa as listas de senhas proibidas globais e personalizadas armazenadas no Azure AD. Ele faz as mesmas verificações no local que o Azure AD faz para alterações baseadas em nuvem. Essas verificações são realizadas durante alterações de senha e cenários de redefinição de senha.

## <a name="design-principles"></a>Princípios de design

A proteção por senha do Azure AD é projetada com esses princípios em mente:

* Os controladores de domínio nunca têm que se comunicar diretamente com a internet.
* Nenhuma porta de rede nova é aberta em controladores de domínio.
* Nenhuma alteração de esquema do Active Directory é necessária. O software usa o **contêiner** active directory existente e objetos de esquema **serviceConnectionPoint.**
* Não é necessário nenhum domínio mínimo do Active Directory ou nível funcional florestal (DFL/FFL).
* O software não cria ou exige contas nos domínios do Active Directory que ele protege.
* Senhas de texto claro do usuário nunca saem do controlador de domínio, seja durante operações de validação de senha ou em qualquer outro momento.
* O software não depende de outros recursos do Azure AD; por exemplo, a sincronização de hash de senha do Azure AD não está relacionada e não é necessária para que a proteção por senha do Azure AD funcione.
* A implantação incremental é suportada, no entanto, a política de senha só é aplicada quando o Agente controlador de domínio (Agente DC) é instalado. Veja o próximo tópico para mais detalhes.

## <a name="incremental-deployment"></a>Implantação incremental

A proteção por senha do Azure AD suporta a implantação incremental entre controladores de domínio em um domínio do Active Directory, mas é importante entender o que isso realmente significa e quais são as trocas.

O software de agente DC de proteção de senha do Azure AD só pode validar senhas quando ele está instalado em um controlador de domínio, e apenas para alterações de senha enviadas a esse controlador de domínio. Não é possível controlar quais controladores de domínio são escolhidos pelas máquinas clientes do Windows para processar alterações de senha do usuário. Para garantir um comportamento consistente e a aplicação universal da segurança de proteção por senha, o software do agente DC DEVE ser instalado em todos os controladores de domínio em um domínio.

Muitas organizações vão querer fazer testes cuidadosos de proteção por senha do Azure AD em um subconjunto de seus controladores de domínio antes de fazer uma implantação completa. A proteção por senha do Azure AD suporta a implantação parcial, ou seja, o software do agente DC em um determinado DC validará ativamente senhas mesmo quando outros DCs no domínio não tiverem o software do agente DC instalado. Implantações parciais deste tipo NÃO são seguras e NÃO são recomendadas além de para fins de teste.

## <a name="architectural-diagram"></a>Diagrama de arquitetura

É importante entender os conceitos de design e função subjacentes antes de implantar a proteção por senha Do Azure AD em um ambiente de Diretório Ativo no local. O diagrama a seguir mostra como os componentes da proteção por senha funcionam em conjunto:

![Como os componentes de proteção por senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* O serviço de Proxy de Proteção por Senha do Azure AD é executado em qualquer computador ingressado no domínio na floresta atual do Active Directory. Seu principal objetivo é encaminhar solicitações de download de políticas de senha de controladores de domínio para o Azure AD. Em seguida, ele retorna as respostas do Azure AD para o controlador de domínio.
* O filtro de senha DLL do Agente DC recebe solicitações de validação de senha do usuário do sistema operacional. Ele os encaminha para o serviço dc agent que está sendo executado localmente no controlador de domínio.
* O serviço dc agent de proteção por senha recebe solicitações de validação de senha do dLL do filtro de senha do Agente DC. Processa-os usando a política de senha atual (localmente disponível) e retorna o resultado: *passe* ou *falhe*.

## <a name="how-password-protection-works"></a>Como funciona a proteção por senha

Cada instância de serviço proxy de proteção por senha do Azure AD anuncia-se aos controladores de domínio na floresta, criando um objeto **serviceConnectionPoint** no Active Directory.

Cada serviço do Agente DC para proteção por senha também cria um objeto **serviceConnectionPoint** no Active Directory. Este objeto é usado principalmente para relatórios e diagnósticos.

O serviço DC Agent é responsável por começar o download de uma nova política de senha do Azure AD. O primeiro passo é localizar um serviço proxy de proteção por senha azure AD consultando a floresta para objetos **do serviço proxyConnectionPoint.** Quando um serviço proxy disponível é encontrado, o Agente DC envia uma solicitação de download de política de senha para o serviço proxy. O serviço proxy, por sua vez, envia a solicitação ao Azure AD. O serviço proxy então retorna a resposta ao serviço dc agent.

Depois que o serviço dc agent recebe uma nova política de senha do Azure AD, o serviço armazena a diretiva em uma pasta dedicada na raiz de seu compartilhamento de pasta *sysvol* de domínio. O serviço dc agent também monitora esta pasta no caso de políticas mais novas se replicarem de outros serviços do AGENTE DC no domínio.

O serviço dc agent sempre solicita uma nova política na inicialização do serviço. Depois que o serviço do Agente DC é iniciado, ele verifica a idade da política localmente disponível de hora em hora. Se a política for superior a uma hora, o Agente DC solicitará uma nova política do Azure AD através do serviço proxy, conforme descrito anteriormente. Se a política atual não for superior a uma hora, o Agente DC continua a usar essa política.

Sempre que uma política de senha de proteção por senha do Azure AD é baixada, essa política é específica para um inquilino. Em outras palavras, as políticas de senha são sempre uma combinação da lista global de senhas banidas da Microsoft e da lista de senhas proibidas personalizadas por inquilino.

O Agente DC comunica-se com o serviço proxy via RPC via TCP. O serviço proxy ouve essas chamadas em uma porta RPC dinâmica ou estática, dependendo da configuração.

O Agente DC nunca ouve em uma porta disponível em rede.

O serviço proxy nunca chama o serviço de agente DC.

O serviço proxy é apátrida. Ele nunca armazena políticas ou qualquer outro estado baixado do Azure.

O serviço dc agent sempre usa a política de senha localmente disponível mais recente para avaliar a senha de um usuário. Se nenhuma política de senha estiver disponível no DC local, a senha será aceita automaticamente. Quando isso acontece, uma mensagem de evento é registrada para avisar o administrador.

A proteção por senha do Azure AD não é um mecanismo de aplicativo de política em tempo real. Pode haver um atraso entre quando uma alteração de configuração de diretiva de senha é feita no Azure AD e quando essa alteração atinge e é aplicada em todos os controladores de domínio.

A proteção por senha do Azure AD atua como um complemento às políticas de senha do Active Directory existentes, não uma substituição. Isso inclui quaisquer outros dlls de filtro de senha de terceiros que possam ser instalados. O Active Directory sempre exige que todos os componentes de validação de senha concordem antes de aceitar uma senha.

## <a name="foresttenant-binding-for-password-protection"></a>Vinculação florestal/inquilino para proteção por senha

A implantação da proteção por senha do Azure AD em uma floresta do Active Directory requer o registro dessa floresta com o Azure AD. Cada serviço proxy implantado também deve ser registrado no Azure AD. Esses registros florestais e proxy estão associados a um inquilino Azure AD específico, que é identificado implicitamente pelas credenciais que são usadas durante o registro.

A floresta do Active Directory e todos os serviços proxy implantados dentro de uma floresta devem ser registrados com o mesmo inquilino. Não é suportado ter uma floresta de Diretório Ativo ou quaisquer serviços proxy naquela floresta sendo registrados para diferentes inquilinos Azure AD. Os sintomas de uma implantação tão mal configurada incluem a incapacidade de baixar políticas de senha.

## <a name="download"></a>Baixar

Os dois instaladores de agentes necessários para proteção de senha Azure AD estão disponíveis no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Próximas etapas
[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
