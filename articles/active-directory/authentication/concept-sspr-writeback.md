---
title: Regravação de senha no local com redefinição de senha de autoatendimento - Azure Active Directory
description: Saiba como a alteração de senha ou redefinição de eventos no Azure Active Directory pode ser escrita de volta para um ambiente de diretório local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787c15c11c995c7eb30662131302658175c7f877
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393015"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Como funciona o reset de redefinição de senha de autoatendimento no Azure Active Directory?

O Reset de senha de autoatendimento (SSPR) do Azure Active Directory (Azure AD) permite que os usuários resetem suas senhas na nuvem, mas a maioria das empresas também tem um ambiente de AD DS (Active Directory Domain Services, serviços de domínio de diretório ativo) no local onde seus usuários existem. O write-back de senha é um recurso habilitado com [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de senha na nuvem possam efetuar write-back para um diretório local existente em tempo real. Nesta configuração, à medida que os usuários alteram ou redefinem suas senhas usando SSPR na nuvem, as senhas atualizadas também são reescritas no ambiente AD DS local

A regravação de senha é suportada em ambientes que usam os seguintes modelos de identidade híbrida:

* [Sincronização de hash de senha](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticação de passagem](../hybrid/how-to-connect-pta.md)
* [Serviços da Federação de Diretórios Ativos](../hybrid/how-to-connect-fed-management.md)

O write-back de senha fornece os seguintes recursos:

* **Aplicação das políticas de senha do Active Directory Domain Services (AD DS) no local**: Quando um usuário redefine sua senha, é verificado para garantir que ele atenda à sua política AD DS no local antes de comprometê-la a esse diretório. Esta revisão inclui verificar o histórico, complexidade, idade, filtros de senha e quaisquer outras restrições de senha que você define no AD DS.
* **Comentários sem atraso** – o write-back de senha é uma operação síncrona. Os usuários são notificados imediatamente se sua senha não atender à política ou não pode ser redefinida ou alterada por qualquer motivo.
* **Suporta alterações de senha do painel de acesso e do Office 365**: Quando usuários sincronizados com hash federados ou com senha saem para alterar suas senhas vencidas ou não expiradas, essas senhas são regravadas para AD DS.
* **Suporta a regravação de senha quando um administrador as redefine do portal Azure**: Quando um administrador redefine a senha de um usuário no [portal Azure](https://portal.azure.com), se esse usuário estiver federado ou o hash de senha sincronizado, a senha será reescrita no local. Essa funcionalidade não é compatível atualmente com o Portal de Administração do Office.
* **Não requer nenhuma regra de firewall de entrada**: a regravação de senha usa um relé de ônibus de serviço do Azure como um canal de comunicação subjacente. Toda a comunicação é de saída pela porta 443.

> [!NOTE]
> Contas de administrador existentes em grupos protegidos no AD local podem ser usadas com o write-back de senha. Os administradores podem alterar sua senha na nuvem, mas não podem usar a redefinição de senha para redefinir uma senha esquecida. Para obter mais informações sobre grupos protegidos, consulte [contas protegidas e grupos no Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="how-password-writeback-works"></a>Como funciona o write-back de senha

Quando um usuário federado ou sincronizado com hash de senha tenta redefinir ou alterar a senha na nuvem, ocorrem as seguintes ações:

1. Uma verificação é realizada para ver qual tipo de senha o usuário tem. Se a senha do usuário for gerenciada no local:
   * Uma verificação será executada para ver se o serviço de write-back está ativo e em execução. Se estiver, o usuário poderá prosseguir.
   * Se o serviço de write-back estiver inativo, o usuário será informado de que a senha não poderá ser redefinida agora.
1. Em seguida, o usuário passa pelas entradas de autenticação pertinentes e alcança a página de **redefinição de senha**.
1. O usuário seleciona uma nova senha e a confirma.
1. Quando o usuário seleciona **Enviar**, a senha de texto não criptografado é criptografada com uma chave simétrica criada durante o processo de configuração de write-backback.
1. A senha criptografada é incluída em uma carga que é enviada por um canal HTTPS para a retransmissão de barramento de serviço específica do locatário (que é configurada para você durante o processo de configuração de write-back). Esta retransmissão é protegida por uma senha gerada aleatoriamente que somente a sua instalação local sabe.
1. Depois que a mensagem chega ao barramento de serviço, o ponto de extremidade de redefinição de senha é automaticamente reativado e vê que há uma solicitação de redefinição pendente.
1. O serviço procurará o usuário em questão usando o atributo de âncora de nuvem. Para que esta busca seja bem sucedida, as seguintes condições devem ser atendidas:

   * O objeto de usuário deve existir no espaço conector do Active Directory.
   * O objeto de usuário deve estar vinculado ao objeto de MV (metaverso) correspondente.
   * O objeto de usuário deve estar vinculado ao objeto de do conector do Azure Active Directory correspondente.
   * O vínculo do objeto de conector do Active Directory com o MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no vínculo.

   Quando a chamada vem da nuvem, o mecanismo de sincronização usa o atributo **cloudAnchor** para procurar o objeto de espaço do conector do Azure Active Directory. Ele então segue o vínculo de volta para o objeto de MV e segue o vínculo de volta para o objeto do Active Directory. Como pode haver vários objetos do Active Directory (várias florestas) para o mesmo usuário, o mecanismo de sincronização depende do vínculo `Microsoft.InfromADUserAccountEnabled.xxx` para escolher o correto.

1. Depois que a conta do usuário é localizada, será feita uma tentativa para redefinir a senha diretamente na floresta apropriada do Active Directory.
1. Se a operação de definição de senha for realizada com êxito, o usuário será informado de que a senha foi alterada.

   > [!NOTE]
   > Se o hash de senha do usuário estiver sincronizado com o Azure AD usando a sincronização de hash de senha, há uma chance de que a política de senha no local seja mais fraca do que a política de senha na nuvem. Nesse caso, a política local é imposta. Essa política garante que a política local seja imposta na nuvem, independentemente de você usar federação ou sincronização de hash de senha para fornecer logon único.

1. Se a operação de definição de senha falhar, um erro solicitará que o usuário tente novamente. A operação pode falhar devido às seguintes razões:
    * O serviço foi desativado.
    * A senha selecionada não atende às políticas da organização.
    * Não é possível localizar o usuário no Active Directory local.

   As mensagens de erro fornecem diretrizes aos usuários para que possam tentar resolver sem intervenção do administrador.

## <a name="password-writeback-security"></a>Segurança de write-back de senha

O write-back de senha é um serviço altamente seguro. Para garantir que suas informações estão protegidas, um modelo de segurança de quatro níveis está ativado da seguinte forma:

* **Retransmissão do barramento de serviço específica ao locatário**
   * Quando você configura o serviço, é configurada uma retransmissão de barramento de serviço específica de locatário protegida por uma senha forte gerada aleatoriamente à qual a Microsoft nunca tem acesso.
* **Chave de criptografia de senha criptograficamente forte e bloqueada**
   * Depois que o relé de barramento de serviço é criado, uma chave simétrica forte é criada que é usada para criptografar a senha à medida que ela vem sobre o fio. Essa chave existe apenas no repositório secreto de sua empresa na nuvem, que é amplamente bloqueado e auditado, assim como qualquer outra senha no diretório.
* **Segurança de camada de transporte (TLS) padrão do setor**
   1. Quando ocorre uma redefinição de senha ou operação de alteração na nuvem, a senha de texto sem formatação é criptografada com a chave pública.
   1. A senha criptografada é colocada em uma mensagem HTTPS enviada por um canal criptografado usando certs Microsoft TLS/SSL no relé de barramento de serviço.
   1. Depois que a mensagem chega ao barramento de serviço, o agente local é ativado e se autentica no barramento de serviço usando a senha forte gerada anteriormente.
   1. O agente local recebe a mensagem criptografada e a descriptografa usando a chave privada.
   1. O agente local tenta definir a senha por meio da API SetPassword do AD DS. Essa etapa é o que permite a imposição da diretiva de senha local do Active Directory (como complexidade, idade, histórico e filtros) na nuvem.
* **Políticas de expiração de mensagem**
   * Caso a mensagem fique no barramento de serviço devido ao serviço local estar desativado, ela atingirá o tempo limite e será removida após alguns minutos. O tempo limite e a remoção da mensagem aumenta ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de criptografia de write-back de senha

Depois que um usuário envia uma redefinição de senha, a solicitação de redefinição passa por várias etapas de criptografia antes de chegar no seu ambiente local. Essas etapas de criptografia garantem a máxima segurança e confiabilidade do serviço. Elas são descritas da seguinte maneira:

1. **Criptografia de senha com chave RSA de 2048 bits**: Depois que um usuário envia uma senha para ser gravada de volta no local, a senha enviada em si é criptografada com uma chave RSA de 2048 bits.
1. **Criptografia em nível de pacote com AES-GCM**: Todo o pacote, a senha mais os metadados necessários, é criptografado usando o AES-GCM. Essa criptografia impede que qualquer pessoa com acesso direto ao canal do barramento de serviço subjacente exiba ou viole o conteúdo.
1. **Toda comunicação ocorre sobre TLS/SSL**: Toda a comunicação com a ServiceBus acontece em um canal SSL/TLS. Essa criptografia protege o conteúdo de terceiros não autorizados.
1. **Substituição de chave automática a cada seis meses**: todas as chaves serão substituídas a cada seis meses ou sempre que o write-back de senha for desabilitado e, em seguida, habilitado novamente no Azure AD Connect, para garantir a máxima segurança e segurança do serviço.

### <a name="password-writeback-bandwidth-usage"></a>Uso de largura de banda de write-back de senha

O write-back de senha é um serviço de largura de banda baixa que envia solicitações de volta para o agente local somente nas seguintes circunstâncias:

* Duas mensagens são enviadas quando o recurso for habilitado ou desabilitado por meio do Azure AD Connect.
* Uma mensagem é enviada a cada cinco minutos como uma pulsação de serviço enquanto o serviço está em execução.
* Duas mensagens são enviadas sempre que uma nova senha é enviada:
   * A primeira mensagem é uma solicitação para executar a operação.
   * A segunda mensagem contém o resultado da operação e é enviada nas seguintes circunstâncias:
      * Sempre que uma nova senha é enviada durante uma redefinição de senha de autoatendimento do usuário.
      * Sempre que uma nova senha é enviada durante uma operação de alteração de senha do usuário.
      * Sempre que uma nova senha é enviada durante uma redefinição de senha do usuário iniciada pelo administrador (somente nos portais de administração do Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações sobre a largura de banda e o tamanho da mensagem

O tamanho de cada mensagem descrita anteriormente normalmente é inferior a 1 KB. Mesmo sob cargas extremas, o próprio serviço de write-back de senha consome alguns quilobits por segundo de largura de banda. Como cada mensagem é enviada em tempo real, somente quando for solicitado por uma operação de atualização de senha, e como o tamanho da mensagem é bem pequeno, o uso da largura de banda da capacidade de write-back será efetivamente muito pequeno para ter qualquer impacto real mensurável.

## <a name="supported-writeback-operations"></a>Operações de write-back com suporte

É feito o write-back das senhas em todas as seguintes situações:

* **Operações do usuário final com suporte**
   * Qualquer operação de senha de alteração voluntária de autoatendimento do usuário final.
   * Qualquer força de autoatendimento do usuário final altera a operação de senha, por exemplo, o vencimento da senha.
   * Qualquer redefinição de senha de autoatendimento do usuário final originada do portal de [redefinição](https://passwordreset.microsoftonline.com)de senha .

* **Operações do administrador com suporte**
   * Qualquer operação de senha de alteração voluntária de autoatendimento do administrador.
   * Qualquer força de autoatendimento do administrador altera a operação de senha, por exemplo, o vencimento da senha.
   * Qualquer redefinição de senha de autoatendimento do administrador originada do [portal de redefinição](https://passwordreset.microsoftonline.com)de senhas .
   * Qualquer redefinição de senha de usuário final iniciada pelo administrador a partir do [portal Azure](https://portal.azure.com).

## <a name="unsupported-writeback-operations"></a>Operações de write-back sem suporte

As senhas não são escritas de volta em nenhuma das seguintes situações:

* **Operações do usuário final sem suporte**
   * Qualquer usuário final redefinindo sua própria senha usando a versão 1 do PowerShell, a versão 2 ou a API do Microsoft Graph.
* **Operações do administrador sem suporte**
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador a partir da versão 1, versão 2 do PowerShell ou da API do Microsoft Graph.
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador a partir do [centro de administração do Microsoft 365](https://admin.microsoft.com).

> [!WARNING]
> O uso da caixa de seleção "O usuário deve alterar a senha no próximo logon" em ferramentas administrativas AD DS no local, como Usuários e Computadores ativos do Diretório Ativo ou o Centro Administrativo do Diretório Ativo é suportado como um recurso de visualização do Azure AD Connect. Para obter mais informações, consulte [Implementar a sincronização de senha com a sincronização do Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Próximas etapas

Para começar com o writeback do SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Habilite a redefinição de senha de autoatendimento (SSPR)](tutorial-enable-writeback.md)
