---
title: Como os trusts funcionam para os serviços de domínio do Azure AD | Microsoft Docs
description: Saiba mais sobre como a confiança na floresta funciona com os serviços de domínio azure AD
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 8b79e0fb24c15d2e9f16640e90d62f7df5c21f32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233695"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Como funcionam as relações de confiança para florestas de recursos nos serviços de domínio do Diretório Ativo do Azure

O Active Directory Domain Services (AD DS) fornece segurança em vários domínios ou florestas através de relacionamentos de domínio e confiança florestal. Antes que a autenticação possa ocorrer entre os trusts, o Windows deve primeiro verificar se o domínio que está sendo solicitado por um usuário, computador ou serviço tem uma relação de confiança com o domínio da conta solicitante.

Para verificar essa relação de confiança, o sistema de segurança do Windows computa um caminho de confiança entre o controlador de domínio (DC) para o servidor que recebe a solicitação e um DC no domínio da conta solicitante.

Os mecanismos de controle de acesso fornecidos pelo AD DS e pelo modelo de segurança distribuída do Windows fornecem um ambiente para a operação de fundos de domínio e floresta. Para que essas confianças funcionem corretamente, cada recurso ou computador deve ter um caminho de confiança direto para uma DC no domínio em que está localizado.

O caminho de confiança é implementado pelo serviço Net Logon usando uma conexão de chamada de procedimento remoto autenticada (RPC) à autoridade de domínio confiável. Um canal seguro também se estende a outros domínios AD DS através de relações de confiança entre domínios. Este canal protegido é usado para obter e verificar informações de segurança, incluindo identificadores de segurança (SIDs) para usuários e grupos.

## <a name="trust-relationship-flows"></a>Fluxos de relacionamento de confiança

O fluxo de comunicações seguras sobre trusts determina a elasticidade de uma confiança. Como você cria ou configura uma confiança determina até onde a comunicação se estende dentro ou através das florestas.

O fluxo de comunicação sobre os trusts é determinado pela direção da confiança. As confianças podem ser unidirecionais ou bidirecionais, e podem ser transitivas ou não transitivas.

O diagrama a seguir mostra que todos os domínios da *Árvore 1* e da *Árvore 2* têm relações de confiança transitivas por padrão. Como resultado, os usuários da *Árvore 1* podem acessar recursos em domínios na *Árvore 2* e os usuários da Árvore *1* podem acessar recursos na *Árvore 2,* quando as permissões adequadas são atribuídas ao recurso.

![Diagrama das relações de confiança entre duas florestas](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Confianças unidirecionais e bidirecionais

Relacionamentos de confiança permitem que o acesso aos recursos possa ser unidirecional ou bidirecional.

Uma confiança unidirecional é um caminho de autenticação unidirecional criado entre dois domínios. Em uma confiança unidirecional entre *o domínio A* e o domínio *B,* os usuários do *Domínio A* podem acessar recursos no *Domínio B*. No entanto, os usuários do *Domínio B* não podem acessar recursos no *Domínio A*.

Algumas confianças unidirecionais podem ser não transitivas ou transitivas, dependendo do tipo de confiança que está sendo criada.

Em uma confiança bidirecional, *o Domínio A* confia no domínio *B* e o *Domínio B* confia no *domínio A*. Essa configuração significa que as solicitações de autenticação podem ser passadas entre os dois domínios em ambas as direções. Algumas relações bidirecionais podem ser não transitivas ou transitivas, dependendo do tipo de confiança que está sendo criada.

Todas as confianças de domínio em uma floresta AD DS são confianças transitivas de duas vias. Quando um novo domínio filho é criado, uma relação de confiança transitiva bidirecional é automaticamente criada entre o novo domínio filho e o domínio pai.

### <a name="transitive-and-non-transitive-trusts"></a>Confianças transitivas e não transitivas

A transitoriedade determina se uma confiança pode ser estendida fora dos dois domínios com os quais foi formada.

* Uma confiança transitiva pode ser usada para estender relações de confiança com outros domínios.
* Uma confiança não transitiva pode ser usada para negar relações de confiança com outros domínios.

Cada vez que você cria um novo domínio em uma floresta, uma relação de confiança transitiva de duas vias é criada automaticamente entre o novo domínio e seu domínio pai. Se os domínios filho forem adicionados ao novo domínio, o caminho de confiança flue para cima através da hierarquia de domínio, estendendo o caminho de confiança inicial criado entre o novo domínio e seu domínio-mãe. As relações de confiança transitivas seguem em direção ao topo de uma árvore de domínio formada, criando esse tipo de relação entre todos os domínios da árvore.

As solicitações de autenticação seguem esses caminhos de confiança, para que contas de qualquer domínio na floresta possam ser autenticadas por qualquer outro domínio na floresta. Com um processo de logon simples, as contas com as permissões apropriadas podem acessar os recursos em qualquer domínio da floresta.

## <a name="forest-trusts"></a>Relações de confiança de floresta

Os fundos florestais ajudam você a gerenciar infra-estruturas AD DS segmentadas e a apoiar o acesso a recursos e outros objetos em várias florestas. Os fundos florestais são úteis para prestadores de serviços, empresas que sofrem fusões ou aquisições, extranets de negócios colaborativos e empresas que buscam uma solução para autonomia administrativa.

Usando fundos florestais, você pode ligar duas florestas diferentes para formar uma relação de confiança transitiva unidirecional ou unidirecional. Um fundo florestal permite que os administradores conectem duas florestas AD DS com uma única relação de confiança para fornecer uma experiência perfeita de autenticação e autorização através das florestas.

Uma confiança florestal só pode ser criada entre um domínio de raiz florestal em uma floresta e um domínio de raiz florestal em outra floresta. Os fundos florestais só podem ser criados entre duas florestas e não podem ser implicitamente estendidos a uma terceira floresta. Esse comportamento significa que se uma confiança florestal é criada entre *a Floresta 1* e a Floresta *2*, e outra confiança florestal é criada entre a *Floresta 2* e a Floresta *3*, a *Floresta 1* não tem uma confiança implícita com a *Floresta 3*.

O diagrama a seguir mostra duas relações de confiança florestal separadas entre três florestas AD DS em uma única organização.

![Diagrama da floresta confia nas relações dentro de uma única organização](./media/concepts-forest-trust/forest-trusts.png)

Esta configuração de exemplo fornece o seguinte acesso:

* Os usuários *da Floresta 2* podem acessar recursos em qualquer domínio na Floresta *1* ou *floresta 3*
* Usuários na *Floresta 3* podem acessar recursos em qualquer domínio na *Floresta 2*
* Usuários na *Floresta 1* podem acessar recursos em qualquer domínio na *Floresta 2*

Essa configuração não permite que os usuários da *Floresta 1* acessem recursos na *Floresta 3* ou vice-versa. Para permitir que os usuários da *Floresta 1* e da *Floresta 3* compartilhem recursos, deve ser criada uma confiança transitiva bidirecional entre as duas florestas.

Se um fundo florestal unidirecional for criado entre duas florestas, os membros da floresta confiável podem utilizar recursos localizados na floresta confiável. Entretanto, a relação de confiança opera em apenas uma direção.

Por exemplo, quando uma confiança florestal unidirecional é criada entre a *Floresta 1* (a floresta confiável) e a *Floresta 2* (a floresta confiável):

* Os membros da *Floresta 1* podem acessar recursos localizados na *Floresta 2*.
* Os membros da *Forest 2* não podem acessar recursos localizados na *Floresta 1* usando a mesma confiança.

> [!IMPORTANT]
> A floresta de recursos do Azure AD Domain Services só suporta uma confiança florestal unidirecional para o Active Directory no local.

### <a name="forest-trust-requirements"></a>Requisitos de confiança florestal

Antes de criar um fundo florestal, você precisa verificar se você tem a infra-estrutura correta do Sistema de Nome de Domínio (DNS) em vigor. Os fundos florestais só podem ser criados quando uma das seguintes configurações de DNS estiver disponível:

* Um único servidor DNS raiz é o servidor DNS raiz para ambos os namespaces de DNS da floresta - a zona raiz contém delegações para cada um dos namespaces do DNS e as dicas raiz de todos os servidores DNS incluem o servidor DNS raiz.
* Onde não há servidor DNS raiz compartilhado e os servidores DNS raiz para cada espaço de nome DNS da floresta usam encaminhadores condicionais DNS para cada namespace DNS para encaminhar consultas para nomes no outro namespace.

    > [!IMPORTANT]
    > A floresta de recursos do Azure AD Domain Services deve usar essa configuração de DNS. Hospedar um namespace DNS diferente do namespace dns da floresta de recursos não é uma característica dos Serviços de Domínio AD do Azure. Os encaminhadores condicionais são a configuração adequada.

* Quando não há servidor DNS raiz compartilhado e os servidores DNS raiz para cada namespace dNS florestal são usados zonas secundárias dNS são configuradas em cada namespace DNS para encaminhar consultas para nomes no outro namespace.

Para criar um fundo florestal, você deve ser um membro do grupo Administradores de Domínio (no domínio raiz da floresta) ou do grupo Enterprise Admins no Active Directory. Cada confiança é atribuída a uma senha que os administradores de ambas as florestas devem saber. Os membros dos Administradores Corporativos em ambas as florestas podem criar os trusts em ambas as florestas ao mesmo tempo e, neste cenário, uma senha que é criptograficamente aleatória é gerada automaticamente e escrita para ambas as florestas.

O fundo florestal de saída para os Serviços de Domínio Azure AD é criado no portal Azure. Você não cria manualmente a confiança com o domínio gerenciado em si. A confiança florestal de entrada deve ser configurada por um usuário com os privilégios previamente observados no Active Directory no local.

## <a name="trust-processes-and-interactions"></a>Processos de confiança e interações

Muitas transações inter-domínio e interflorestal dependem de confiança de domínio ou floresta para concluir várias tarefas. Esta seção descreve os processos e interações que ocorrem à medida que os recursos são acessados em trusts e referências de autenticação são avaliadas.

### <a name="overview-of-authentication-referral-processing"></a>Visão geral do processamento de referência de autenticação

Quando uma solicitação de autenticação é referida a um domínio, o controlador de domínio nesse domínio deve determinar se existe uma relação de confiança com o domínio do qual a solicitação vem. A direção da confiança e se a confiança é transitiva ou não transitiva também deve ser determinada antes de autenticar o usuário para acessar recursos no domínio. O processo de autenticação que ocorre entre domínios confiáveis varia de acordo com o protocolo de autenticação em uso. Os protocolos Kerberos V5 e NTLM processam referências para autenticação em um domínio de forma diferente

### <a name="kerberos-v5-referral-processing"></a>Processamento de referência Kerberos V5

O protocolo de autenticação Kerberos V5 depende do serviço Net Logon em controladores de domínio para informações de autenticação e autorização do cliente. O protocolo Kerberos se conecta a um Centro de Distribuição de Chaves online (KDC) e à loja de conta do Active Directory para ingressos de sessão.

O protocolo Kerberos também usa trusts para serviços de concessão de bilhetes (TGS) e para validar Os Certificados de Atributos Privilege (PACs) através de um canal seguro. O protocolo Kerberos realiza autenticação cross-realm apenas com reinos Kerberos de sistema operacional não-Windows, como um reino MIT Kerberos e não precisa interagir com o serviço Net Logon.

Se o cliente usar o Kerberos V5 para autenticação, ele solicitará um ticket para o servidor no domínio de destino de um controlador de domínio em seu domínio de conta. O Kerberos KDC atua como um intermediário confiável entre o cliente e o servidor e fornece uma chave de sessão que permite que as duas partes se autentiquem. Se o domínio de destino for diferente do domínio atual, o KDC seguirá um processo lógico para determinar se uma solicitação de autenticação pode ser encaminhada:

1. O domínio atual é confiável diretamente pelo domínio do servidor que está sendo solicitado?
    * Se sim, envie ao cliente um encaminhamento para o domínio solicitado.
    * Se não, vá para o próximo passo.

2. Existe uma relação de confiança transitiva entre o domínio atual e o próximo domínio no caminho da confiança?
    * Se sim, envie ao cliente uma referência para o próximo domínio no caminho da confiança.
    * Se não, envie ao cliente uma mensagem negada pelo logon.

### <a name="ntlm-referral-processing"></a>Processamento de referência NTLM

O protocolo de autenticação NTLM depende do serviço Net Logon em controladores de domínio para informações de autenticação e autorização do cliente. Este protocolo autentica clientes que não usam autenticação Kerberos. O NTLM usa trusts para passar solicitações de autenticação entre domínios.

Se o cliente usar NTLM para autenticação, a solicitação inicial de autenticação vai diretamente do cliente para o servidor de recursos no domínio de destino. Esse servidor cria um desafio ao qual o cliente responde. Em seguida, o servidor envia a resposta do usuário a um controlador de domínio em seu domínio de conta de computador. Este controlador de domínio verifica a conta de usuário em relação ao banco de dados de suas contas de segurança.

Se a conta não existir no banco de dados, o controlador de domínio determina se deve executar a autenticação de passagem, encaminhar a solicitação ou negar a solicitação usando a seguinte lógica:

1. O domínio atual tem uma relação direta de confiança com o domínio do usuário?
    * Se sim, o controlador de domínio envia as credenciais do cliente para um controlador de domínio no domínio do usuário para autenticação de passagem.
    * Se não, vá para o próximo passo.

2. O domínio atual tem uma relação de confiança transitiva com o domínio do usuário?
    * Se sim, passe a solicitação de autenticação para o próximo domínio no caminho de confiança. Este controlador de domínio repete o processo verificando as credenciais do usuário em relação ao seu próprio banco de dados de contas de segurança.
    * Se não, envie ao cliente uma mensagem negada pelo logon.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Pedidos de autenticação baseados em Kerberos sobre trusts florestais

Quando duas florestas são conectadas por um fundo florestal, pedidos de autenticação feitos usando os protocolos Kerberos V5 ou NTLM podem ser roteados entre florestas para fornecer acesso a recursos em ambas as florestas.

Quando um fundo florestal é estabelecido pela primeira vez, cada floresta coleta todos os namespaces confiáveis em sua floresta parceira e armazena as informações em um [objeto de domínio confiável](#trusted-domain-object). Os namespaces confiáveis incluem nomes de árvore de domínio, sufixos de nome principal de usuário (UPN), sufixos de nome principal de serviço (SPN) e espaços de nomes de ID de segurança (SID) usados na outra floresta. Os objetos TDO são replicados para o catálogo global.

Antes que os protocolos de autenticação possam seguir o caminho da confiança florestal, o nome principal do serviço (SPN) do computador de recursos deve ser resolvido em um local na outra floresta. Um SPN pode ser um dos seguintes:

* O nome DNS de um hospedeiro.
* O nome DNS de um domínio.
* O nome distinto de um objeto de ponto de conexão de serviço.

Quando uma estação de trabalho em uma floresta tenta acessar dados em um computador de recursos em outra floresta, o processo de autenticação kerberos entra em contato com o controlador de domínio para um ticket de serviço para o SPN do computador de recursos. Uma vez que o controlador de domínio consulta o catálogo global e determina que o SPN não está na mesma floresta que o controlador de domínio, o controlador de domínio envia uma referência para seu domínio-mãe de volta à estação de trabalho. Nesse ponto, a estação de trabalho consulta o domínio pai para o ticket de serviço e continua a seguir a cadeia de referência até chegar ao domínio onde o recurso está localizado.

O diagrama e as etapas a seguir fornecem uma descrição detalhada do processo de autenticação do Kerberos que é usado quando os computadores que executam o Windows tentam acessar recursos de um computador localizado em outra floresta.

![Diagrama do processo kerberos sobre um fundo florestal](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *O Usuário1* faz logon no *Workstation1* usando credenciais do domínio *europe.tailspintoys.com.* Em seguida, o usuário tenta acessar um recurso compartilhado no *FileServer1* localizado na floresta *usa.wingtiptoys.com.*

2. *Workstation1* entra em contato com o Kerberos KDC em um controlador de domínio em seu domínio, *ChildDC1,* e solicita um ticket de serviço para o *FileServer1* SPN.

3. *O ChildDC1* não encontra o SPN em seu banco de dados de domínios e consulta o catálogo global para ver se algum domínio na floresta *tailspintoys.com* contém este SPN. Como um catálogo global é limitado à sua própria floresta, o SPN não é encontrado.

    O catálogo global então verifica seu banco de dados para obter informações sobre quaisquer fundos florestais que sejam estabelecidos com sua floresta. Se encontrado, ele compara os sufixos de nome listados no objeto de domínio confiável (TDO) da confiança da floresta com o sufixo do SPN de destino para encontrar uma correspondência. Uma vez que uma correspondência é encontrada, o catálogo global fornece uma dica de roteamento de volta ao *ChildDC1*.

    As dicas de roteamento ajudam a direcionar solicitações de autenticação para a floresta de destino. As dicas só são usadas quando todos os canais tradicionais de autenticação, como controlador de domínio local e, em seguida, catálogo global, não conseguem localizar um SPN.

4. *ChildDC1* envia um encaminhamento para seu domínio pai de volta para *Workstation1*.

5. *Workstation1* entra em contato com um controlador de domínio no *ForestRootDC1* (seu domínio-mãe) para uma referência a um controlador de domínio *(ForestRootDC2)* no domínio raiz florestal da *floresta wingtiptoys.com.*

6. *Workstation1* entra em contato com *forestrootDC2* na *floresta wingtiptoys.com* para um ticket de serviço para o serviço solicitado.

7. *ForestRootDC2* entra em contato com seu catálogo global para encontrar o SPN, e o catálogo global encontra uma correspondência para o SPN e o envia de volta para *ForestRootDC2*.

8. *ForestRootDC2* então envia o encaminhamento para *usa.wingtiptoys.com* de volta para *Workstation1*.

9. *Workstation1* entra em contato com o KDC no *ChildDC2* e negocia o ticket para que o *Usuário1* tenha acesso ao *FileServer1*.

10. Uma vez *que o Workstation1* tenha um ticket de serviço, ele envia o ticket de serviço para *fileServer1*, que lê as credenciais de segurança do *Usuário1*e constrói um token de acesso de acordo.

## <a name="trusted-domain-object"></a>Objeto de domínio confiável

Cada domínio ou confiança florestal dentro de uma organização é representado por um TDO (Trusted Domain Object, objeto de domínio confiável) armazenado no contêiner *do Sistema* dentro de seu domínio.

### <a name="tdo-contents"></a>Conteúdo TDO

As informações contidas em um TDO variam dependendo se um TDO foi criado por um fundo de domínio ou por um fundo florestal.

Quando uma confiança de domínio é criada, atributos como o nome de domínio DNS, o SID de domínio, o tipo de confiança, a transitividade de confiança e o nome de domínio recíproco são representados no TDO. Os TDOs da Forest Trust armazenam atributos adicionais para identificar todos os namespaces confiáveis da floresta parceira. Esses atributos incluem nomes de árvore de domínio, sufixos de nome principal de usuário (UPN), sufixos de nome principal de serviço (SPN) e espaços de nomes de ID de segurança (SID).

Como os trusts são armazenados no Active Directory como TDOs, todos os domínios em uma floresta têm conhecimento das relações de confiança que estão em vigor em toda a floresta. Da mesma forma, quando duas ou mais florestas são unidas através de fundos florestais, os domínios da raiz florestal em cada floresta têm conhecimento das relações de confiança que estão em vigor em todos os domínios em florestas confiáveis.

### <a name="tdo-password-changes"></a>Alterações de senha do TDO

Ambos os domínios em um relacionamento de confiança compartilham uma senha, que é armazenada no objeto TDO no Active Directory. Como parte do processo de manutenção da conta, a cada 30 dias o controlador de domínio confiável altera a senha armazenada no TDO. Como todas as confianças bidirecionais são na verdade duas confianças unidirecionais indo em direções opostas, o processo ocorre duas vezes para confianças bidirecionais.

Um fundo tem um lado confiável e confiável. No lado confiável, qualquer controlador de domínio gravável pode ser usado para o processo. No lado de confiança, o emulador PDC executa a alteração de senha.

Para alterar uma senha, os controladores de domínio completam o seguinte processo:

1. O emulador pdc (controlador de domínio primário) no domínio de confiança cria uma nova senha. Um controlador de domínio no domínio confiável nunca inicia a alteração de senha. É sempre iniciado pelo emulador PDC do domínio de confiança.

2. O emulador DePDC no domínio de confiança define o campo *OldPassword* do objeto TDO para o campo *NewPassword* atual.

3. O emulador PDC no domínio de confiança define o campo *NewPassword* do objeto TDO para a nova senha. Manter uma cópia da senha anterior torna possível reverter para a senha antiga se o controlador de domínio no domínio confiável não receber a alteração, ou se a alteração não for replicada antes de uma solicitação ser feita que usa a nova senha de confiança.

4. O emulador de PDC no domínio de confiança faz uma chamada remota para um controlador de domínio no domínio confiável pedindo que ele defina a senha na conta fiduciário para a nova senha.

5. O controlador de domínio no domínio confiável altera a senha de confiança para a nova senha.

6. Em cada lado da confiança, as atualizações são replicadas para os outros controladores de domínio no domínio. No domínio de confiança, a alteração desencadeia uma replicação urgente do objeto de domínio confiável.

A senha agora é alterada em ambos os controladores de domínio. A replicação normal distribui os objetos TDO para os outros controladores de domínio no domínio. No entanto, é possível que o controlador de domínio no domínio de confiança altere a senha sem atualizar com sucesso um controlador de domínio no domínio confiável. Esse cenário pode ocorrer porque um canal seguro, que é necessário para processar a mudança de senha, não pôde ser estabelecido. Também é possível que o controlador de domínio no domínio confiável possa estar indisponível em algum momento durante o processo e pode não receber a senha atualizada.

Para lidar com situações em que a alteração de senha não é comunicada com sucesso, o controlador de domínio no domínio de confiança nunca altera a nova senha a menos que tenha autenticado com sucesso (configurar um canal protegido) usando a nova senha. Esse comportamento é o motivo pelo qual as senhas antigas e novas são mantidas no objeto TDO do domínio de confiança.

Uma alteração de senha não é finalizada até que a autenticação usando a senha seja bem sucedida. A senha antiga e armazenada pode ser usada no canal protegido até que o controlador de domínio no domínio confiável receba a nova senha, permitindo assim o serviço ininterrupto.

Se a autenticação usando a nova senha falhar porque a senha é inválida, o controlador de domínio confiável tentará autenticar usando a senha antiga. Se autenticar com sucesso com a senha antiga, ele retomará o processo de alteração de senha em 15 minutos.

As atualizações de senha de confiança precisam ser replicadas aos controladores de domínio de ambos os lados da confiança dentro de 30 dias. Se a senha de confiança for alterada após 30 dias e um controlador de domínio tiver apenas a senha N-2, ela não poderá usar a confiança do lado de confiança e não poderá criar um canal seguro no lado confiável.

## <a name="network-ports-used-by-trusts"></a>Portas de rede usadas por trusts

Como os trusts devem ser implantados em vários limites de rede, eles podem ter que passar por um ou mais firewalls. Quando este for o caso, você pode fazer um túnel de confiança no tráfego através de um firewall ou abrir portas específicas no firewall para permitir que o tráfego passe.

> [!IMPORTANT]
> Os Active Directory Domain Services não suportam restringir o tráfego de RPC do Active Directory a portas específicas.

Leia a seção **Windows Server 2008 e versões posteriores** do Artigo de Suporte da Microsoft [Como configurar um firewall para domínios e trusts do Active Directory](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) para aprender sobre as portas necessárias para um fundo florestal.

## <a name="supporting-services-and-tools"></a>Suporte a serviços e ferramentas

Para suportar trusts e autenticação, alguns recursos adicionais e ferramentas de gerenciamento são usados.

### <a name="net-logon"></a>Logon de Rede

O serviço Net Logon mantém um canal protegido de um computador baseado no Windows para um DC. Também é usado nos seguintes processos relacionados à confiança:

* Configuração e gerenciamento de confiança - O Net Logon ajuda a manter senhas de confiança, coleta informações de confiança e verifica confianças interagindo com o processo LSA e o TDO.

    Para os trusts da Forest, as informações de confiança incluem o registro Forest Trust Information *(FTInfo),* que inclui o conjunto de namespaces que uma floresta confiável afirma gerenciar, anotado com um campo que indica se cada reivindicação é confiável pela floresta confiável.

* Autenticação – Fornece credenciais de usuário sobre um canal seguro para um controlador de domínio e retorna os SIDs de domínio e os direitos do usuário para o usuário.

* Localização do controlador de domínio – Ajuda a encontrar ou localizar controladores de domínio em um domínio ou em domínios.

* Validação de passagem – As credenciais dos usuários em outros domínios são processadas pelo Net Logon. Quando um domínio confiável precisa verificar a identidade de um usuário, ele passa as credenciais do usuário através do Net Logon para o domínio confiável para verificação.

* Verificação do Privilege Attribute Certificate (PAC) – Quando um servidor que usa o protocolo Kerberos para autenticação precisa verificar o PAC em um ticket de serviço, ele envia o PAC através do canal seguro para seu controlador de domínio para verificação.

### <a name="local-security-authority"></a>Autoridade de Segurança Local

A Autoridade De Segurança Local (LSA) é um subsistema protegido que mantém informações sobre todos os aspectos da segurança local em um sistema. Conhecida coletivamente como política de segurança local, a LSA fornece vários serviços para tradução entre nomes e identificadores.

O subsistema de segurança LSA fornece serviços tanto no modo kernel quanto no modo de usuário para validar o acesso a objetos, verificar privilégios de usuário e gerar mensagens de auditoria. A LSA é responsável por verificar a validade de todos os tickets de sessão apresentados por serviços em domínios confiáveis ou não confiáveis.

### <a name="management-tools"></a>Ferramentas de gerenciamento

Os administradores podem usar *domínios e trustções do Active Directory,* *Netdom* e *Nltest* para expor, criar, remover ou modificar trusts.

* *Active Directory Domains and Trusts* é o Microsoft Management Console (MMC) que é usado para administrar trusts de domínio, níveis funcionais de domínio e floresta, e sufixos de nome principal do usuário.
* As ferramentas de linha de comando *Netdom* e *Nltest* podem ser usadas para encontrar, exibir, criar e gerenciar trusts. Essas ferramentas se comunicam diretamente com a autoridade LSA em um controlador de domínio.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre florestas de recursos, [veja como os fundos florestais funcionam no Azure AD DS?][concepts-trust]

Para começar com a criação de um domínio gerenciado pelo Azure AD DS com uma floresta de recursos, consulte [Criar e configurar um domínio gerenciado pelo Azure AD DS][tutorial-create-advanced]. Em seguida, você pode [criar uma confiança florestal de saída para um domínio local (visualização)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
