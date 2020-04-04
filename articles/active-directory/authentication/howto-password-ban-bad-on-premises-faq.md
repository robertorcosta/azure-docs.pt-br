---
title: Perguntas frequentes sobre a proteção por senha do Azure AD local
description: Revise perguntas frequentes para o Azure AD Password Protection em um ambiente de serviços de domínio de diretório ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16537ba72eb9f11abd8de47256150818c121a140
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652654"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD Password Protection no local frequentemente faz perguntas

Esta seção fornece respostas para muitas perguntas comumente feitas sobre o Azure AD Password Protection.

## <a name="general-questions"></a>Perguntas gerais

**P: Que orientação os usuários devem ser dados sobre como selecionar uma senha segura?**

A diretriz atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Diretrizes de Senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**P: No local, o Azure AD Password Protection é suportado em nuvens não públicas?**

Não, locais de proteção por senha do Azure AD têm suporte apenas na nuvem pública. Não foi anunciada nenhuma data para a disponibilidade de nuvem não pública.

O portal Azure AD permite a modificação da configuração "Proteção por senha para diretório ativo do Windows Server" específica no local, mesmo em nuvens não públicas; tais mudanças serão persistidas, mas de outra forma nunca entrarão em vigor. O registro de agentes proxy ou florestas no local não é suportado quando credenciais de nuvem não públicas são usadas, e quaisquer tentativas de registro sempre falharão.

**P: Como posso aplicar os benefícios do Azure AD Password Protection a um subconjunto dos meus usuários locais?**

Sem suporte. Depois de implantada e habilitada, a proteção por senha do Azure AD não discrimina, todos os usuários recebem benefícios de segurança iguais.

**P: Qual é a diferença entre uma alteração de senha e um conjunto de senhas (ou reset)?**

Uma alteração de senha é quando um usuário escolhe uma nova senha depois de provar que tem conhecimento da senha antiga. Por exemplo, uma alteração de senha é o que acontece quando um usuário faz login no Windows e, em seguida, é solicitado a escolher uma nova senha.

Um conjunto de senhas (às vezes chamado de reset de senha) é quando um administrador substitui a senha em uma conta por uma nova senha, por exemplo, usando a ferramenta de gerenciamento de Usuários e Computadores do Active Directory. Esta operação requer um alto nível de privilégio (geralmente Admin de domínio), e a pessoa que realiza a operação geralmente não tem conhecimento da senha antiga. Cenários de help desk geralmente executam conjuntos de senhas, por exemplo, ao ajudar um usuário que esqueceu sua senha. Você também verá eventos de definição de senha quando uma nova conta de usuário estiver sendo criada pela primeira vez com uma senha.

A política de validação de senha se comporta da mesma forma, independentemente de uma alteração de senha ou conjunto estar sendo feito. O serviço azure AD Password Protection DC Agent faz log de diferentes eventos para informar se uma operação de alteração de senha ou conjunto foi feita.  Consulte [o monitoramento e o registro de proteção por senha do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**P: Por que eventos de rejeição de senha duplicados são registrados ao tentar definir uma senha fraca usando o snap-in de gerenciamento de usuários e computadores do Active Directory?**

O snap-in de gerenciamento de diretórios ativos e computadores tentará primeiro definir a nova senha usando o protocolo Kerberos. Após a falha, o snap-in fará uma segunda tentativa de definir a senha usando um protocolo legado (SAM RPC) (os protocolos específicos usados não são importantes). Se a nova senha for considerada fraca pelo Azure AD Password Protection, esse comportamento de snap-in resultará em dois conjuntos de eventos de rejeição de redefinição de senha sendo registrados.

**P: Por que os eventos de validação de senha do Azure AD Password Protection estão sendo registrados com um nome de usuário vazio?**

O Active Directory suporta a capacidade de testar uma senha para ver se ela passa nos requisitos atuais de complexidade de senha do domínio, por exemplo, usando a api [NetValidatePasswordPolicy.](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) Quando uma senha é validada dessa forma, o teste também inclui validação por produtos baseados em filtro de senha, como o Azure AD Password Protection - mas os nomes de usuário passados para um determinado filtro de senha dll estarão vazios. Neste cenário, o Azure AD Password Protection ainda validará a senha usando a política de senha em vigor e emitirá uma mensagem de registro de evento para capturar o resultado, no entanto, a mensagem de registro de eventos terá campos de nome de usuário vazios.

**P: É suportado instalar o Azure AD Password Protection lado a lado com outros produtos baseados em filtro de senha?**

Sim. Suporte para várias dlls de filtro de senha registrada é um recurso importante do Windows e não específico para a proteção por senha do Azure AD. Todas as dlls de filtro de senha registrados devem aceitar antes que uma senha seja aceita.

**P: Como posso implantar e configurar o Azure AD Password Protection no meu ambiente active Directory sem usar o Azure?**

Sem suporte. A proteção de senha do Azure AD é um recurso do Azure que dá suporte a ser estendido para um ambiente local do Active Directory.

**P: Como posso modificar o conteúdo da política no nível do Active Directory?**

Sem suporte. A apólice só pode ser administrada usando o portal Azure AD. Confira também a pergunta anterior.

**P: por que o DFSR é necessário para a replicação de sysvol?**

FRS (a tecnologia do predecessor para DFSR) tem vários problemas conhecidos e é totalmente suportada em versões mais recentes do Windows Server Active Directory. Zero testes da proteção por senha do Azure AD serão feitos nos domínios configurados para FRS.

Para obter mais informações, consulte:

[O caso para a replicação do sysvol migrando para DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[O fim é Nigh para FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Se o seu domínio ainda não estiver usando o DFSR, você deve migrar para usar o DFSR antes de instalar o Azure AD Password Protection. Para obter mais informações, consulte o seguinte link:

[Guia de Migração de Replicação SYSVOL: Replicação FRS para DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> O software Azure AD Password Protection DC Agent será instalado atualmente em controladores de domínio em domínios que ainda estão usando FRS para replicação sysvol, mas o software NÃO funcionará corretamente neste ambiente. Efeitos colaterais negativos adicionais incluem arquivos individuais que não conseguem se replicar, e procedimentos de restauração sysvol parecem ter sucesso, mas silenciosamente falhando em replicar todos os arquivos. Você deve migrar seu domínio para usar o DFSR o mais rápido possível, tanto para os benefícios inerentes do DFSR quanto para desbloquear a implantação do Azure AD Password Protection. As versões futuras do software serão automaticamente desativadas ao serem executados em um domínio que ainda está usando FRS.

**P: Quanto espaço em disco o recurso requer no compartilhamento de domínio sysvol?**

O uso do espaço preciso varia, pois depende de fatores como o número e o tamanho dos tokens banidos na lista proibida global do Microsoft e a lista personalizada por locatário, além da sobrecarga de criptografia. O conteúdo dessas listas provavelmente aumentará no futuro. Com isso em mente, uma expectativa razoável é que o recurso precisará de pelo menos cinco (5) megabytes de espaço no compartilhamento de sysvol do domínio.

**P: por que uma reinicialização é necessária para instalar ou atualizar o software do agente de controlador de domínio?**

Esse requisito é causado pelo comportamento principal do Windows.

**P: há alguma maneira de configurar um agente de controlador de domínio para usar um servidor proxy específico?**

Não. Uma vez que o servidor proxy é sem estado, não é importante saber qual servidor proxy específico é usado.

**P: Não há problema em implantar o serviço Proxy de Proteção por Senha ad do Azure lado a lado com outros serviços como o Azure AD Connect?**

Sim. O serviço de Proxy de Proteção de Senha do Azure AD e o Azure AD Connect nunca devem entrar em conflito diretamente entre si.

Infelizmente, foi encontrada uma incompatibilidade entre a versão do serviço Microsoft Ad AD Connect Agent Updater que é instalado pelo software Azure AD Password Protection Proxy e a versão do serviço instalado pelo software Proxy do [Azure Active Directory Application.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) Essa incompatibilidade pode fazer com que o serviço Agent Updater não possa entrar em contato com o Azure para obter atualizações de software. Não é recomendável instalar o Proxy de Proteção por Senha AD do Azure e o Proxy do Aplicativo de Diretório Ativo do Azure na mesma máquina.

**P: Em que ordem os agentes dc e proxies devem ser instalados e registrados?**

Qualquer pedido de instalação de agente proxy, instalação de agente DC, registro florestal e registro proxy é suportado.

**P: Devo me preocupar com o desempenho atingido nos meus controladores de domínio de implantar esse recurso?**

O serviço do Agente de Controlador de Domínio de Proteção por Senha do Azure AD não deve afetar significativamente o desempenho do controlador de domínio em uma implantação íntegra existente do Active Directory.

Para a maioria das implantações do Active Directory, operações de alteração de senha são uma pequena proporção da carga de trabalho geral em qualquer controlador de domínio. Como um exemplo, imagine um domínio do Active Directory com 10 mil contas de usuário e uma política de MaxPasswordAge definida como 30 dias. Em média, esse domínio verá 10.000/30 = ~333 operações de alteração de senha por dia, o que é um número pequeno de operações para até mesmo um único controlador de domínio. Considere um possível cenário de pior hipótese: suponha que essas ~333 alterações de senha em um único controlador de domínio tenham sido feitas em uma única hora. Por exemplo, esse cenário poderá ocorrer quando muitos funcionários voltam a trabalhar na segunda-feira pela manhã. Mesmo nesse caso, estamos ainda frente a ~333/60 minutos = seis alterações de senha por minuto, o que, novamente, não é uma carga significativa.

No entanto se os controladores de domínio atuais já estão executando em níveis de desempenho limitado (por exemplo, tiverem atingido o limite máximo com relação à CPU, ao espaço em disco, E/S de disco etc.), recomendamos adicionar mais controladores de domínio ou expandir o espaço em disco disponível antes de implantar esse recurso. Confira também pergunta acima sobre o uso do espaço em disco sysvol acima.

**P: Eu quero testar a Proteção por Senha AD do Azure em apenas alguns DCs no meu domínio. É possível forçar alterações na senha do usuário para usar esses DCs específicos?**

Não. O sistema operacional de cliente do Windows controla qual controlador de domínio é usado quando um usuário altera sua senha. O controlador de domínio é selecionado com base em fatores como atribuições de diretório ativo e sub-rede, configuração de rede específica do ambiente, etc. O Azure AD Password Protection não controla esses fatores e não pode influenciar qual controlador de domínio é selecionado para alterar a senha de um usuário.

Uma maneira de parcialmente atingir essa meta seria implantar a proteção de senha do Azure AD em todos os controladores de domínio em um determinado site do Active Directory. Essa abordagem fornecerá cobertura razoável para os clientes do Windows atribuídos a esse site e, portanto, também para os usuários que estão fazendo logon nesses clientes e alterando suas senhas.

**P: Se eu instalar o serviço azure AD Password Protection DC Agent em apenas o PDC (Primary Domain Controller, controlador de domínio principal), todos os outros controladores de domínio no domínio também serão protegidos?**

Não. Quando uma senha do usuário for alterada em um controlador de domínio do PDC não fornecido, a senha com texto não criptografado nunca será enviada ao PDC (essa ideia é uma percepção incorreta comum). Depois que uma nova senha é aceita em um determinado controlador de domínio, esse controlador de domínio a usa para criar vários hashes específicos de protocolo de autenticação dessa senha e, em seguida, manter esses hashes no diretório. A senha com texto não criptografado não é persistente. Os hashes atualizados então são replicados para o controlador de domínio primário. As senhas de usuário podem, em alguns casos, ser alteradas diretamente no PDC dependendo de vários fatores, como topologia de rede e design de site do Active Directory. (Confira a pergunta anterior.)

Em resumo, a implantação do serviço de Agente de Controlador de Domínio de Proteção por Senha do Azure AD no PDC é necessária para alcançar 100% de cobertura de segurança do recurso em domínio. Implantar o recurso no PDC apenas não oferece benefícios de segurança de proteção por senha do Azure AD para todos os demais controladores de domínio no domínio.

**P: Por que o bloqueio inteligente personalizado não funciona mesmo depois que os agentes são instalados no meu ambiente de Diretório Ativo no local?**

O bloqueio inteligente personalizado só é suportado no Azure AD. As alterações nas configurações de bloqueio inteligente personalizadas no portal Azure AD não têm efeito no ambiente de Diretório Ativo no local, mesmo com os agentes instalados.

**P: Um pacote de gerenciamento do System Center Operations Manager está disponível para proteção por senha do Azure AD?**

Não.

**P: Por que o Azure AD ainda está rejeitando senhas fracas, mesmo que eu tenha configurado a política para estar no modo de Auditoria?**

O modo de auditoria só é suportado no ambiente de diretório ativo no local. O Azure AD está implicitamente sempre no modo "impor" quando avalia senhas.

**P: Meus usuários veem a mensagem de erro tradicional do Windows quando uma senha é rejeitada pelo Azure AD Password Protection. É possível personalizar esta mensagem de erro para que os usuários saibam o que realmente aconteceu?**

Não. A mensagem de erro vista pelos usuários quando uma senha é rejeitada por um controlador de domínio é controlada pela máquina cliente, não pelo controlador de domínio. Esse comportamento acontece se uma senha é rejeitada pelas políticas de senha padrão do Active Directory ou por uma solução baseada em filtro de senha, como o Azure AD Password Protection.

## <a name="additional-content"></a>Conteúdo adicional

Os links a seguir não fazem parte da principal documentação da Proteção de Senha do Azure AD, mas podem ser uma fonte útil de informações adicionais sobre o recurso.

[A Proteção por Senha AD do Azure já está disponível em geral!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guia de proteção contra phishing por e-mail – Parte 15: Implementar o Serviço de Proteção por Senha ad do Microsoft Azure (para locais também!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[O Bloqueio Inteligente e a Proteção por senha do Azure AD agora estão em Versão Prévia Pública!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Treinamento em suporte da Microsoft Premier\Unified disponível

Se você estiver interessado em aprender mais sobre a proteção por senha do Azure AD e como implantá-la em seu ambiente, poderá aproveitar um serviço proativo da Microsoft disponível para clientes com um contrato de suporte Premier ou Unified. O serviço é chamado de Azure Active Directory: Password Protection. Para obter mais informações, entre em contato com seu Gerente Técnico de Conta.

## <a name="next-steps"></a>Próximas etapas

Se você tiver uma pergunta sobre proteção de senha do Azure AD local que não foi respondida aqui, envie um item de Comentário abaixo. Obrigado!

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
