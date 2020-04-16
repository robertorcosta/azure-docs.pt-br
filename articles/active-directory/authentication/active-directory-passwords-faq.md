---
title: Perguntas frequentes de redefinição de senha – Azure Active Directory
description: Perguntas frequentes sobre o autoatendimento de redefinição de senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1246b5b980f60c2f3e65aa5b32a7d79dd6efc7d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407171"
---
# <a name="password-management-frequently-asked-questions"></a>Perguntas frequentes sobre gerenciamento de senhas

Veja abaixo algumas perguntas frequentes (FAQ) sobre tudo relativo à redefinição de senhas.

Caso você tenha uma pergunta geral sobre o Azure Active Directory (Azure AD) e o autoatendimento de redefinição de senha (SSPR) que não foi respondida aqui, peça ajuda à comunidade nos [fóruns do Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Os membros da comunidade incluem engenheiros, gerentes de produto, MVPs e colegas profissionais de TI.

Esta seção de perguntas frequentes é dividida nas seguintes seções:

* [Perguntas sobre registro de redefinição de senha](#password-reset-registration)
* [Perguntas sobre a redefinição de senha](#password-reset)
* [Perguntas sobre alteração de senha](#password-change)
* [Perguntas sobre relatórios de gerenciamento de senhas](#password-management-reports)
* [Perguntas sobre o write-back de senha](#password-writeback)

## <a name="password-reset-registration"></a>Registro de redefinição de senha

* **P: Meus usuários podem registrar seus próprios dados de redefinição de senha?**

  > **A:** Sim. Desde que a redefinição de senha esteja habilitada e eles sejam licenciados, os usuários podem ir para o portal de registro de redefinição de senha (https://aka.ms/ssprsetup)) para registrar as informações de autenticação. Os usuários também podem registrar por meio do painel de acesso (https://myapps.microsoft.com)). Para registrar-se por meio do Painel de Acesso, eles precisam selecionar a imagem do perfil, selecionar **Perfil** e, em seguida, selecionar a opção **Registrar-se para redefinição de senha**.
  >
  >
* **P: se habilitar a redefinição de senha para um grupo e, em seguida, optar por habilitá-la para todos, meu usuários deverão se registrar novamente?**

  > **R:** Não. Os usuários que possuem dados de autenticação populados não devem se registrar novamente.
  >
  >
* **P: Posso definir dados de redefinição de senha em nome dos meus usuários?**

  > **A:** Sim, você pode fazê-lo com o Azure AD Connect, PowerShell, o [portal Azure](https://portal.azure.com)ou o [centro de administradores Microsoft 365.](https://admin.microsoft.com) Para obter mais informações, consulte [Dados usados pelo autoatendimento de redefinição de senha do Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **P: posso sincronizar dados de perguntas de segurança do local?**

  > **R:** não, isso não é possível atualmente.
  >
  >
* **P: meus usuários podem registrar dados de forma que outros usuários não possam ver esses dados?**

  > **A:** Sim. Quando os usuários registram dados usando o portal de registro de redefinição de senha, os dados são salvos em campos de autenticação privada visíveis apenas por administradores globais e pelo usuário.
  >
  >
* **P: Meus usuários têm que ser registrados antes que possam usar redefinição de senha?**

  > **R:** Não. Se você definir informações de autenticação suficientes em nome deles, os usuários não precisarão se registrar. A redefinição de senha funcionará desde que você tenha formatado corretamente os dados armazenados nos campos apropriados no diretório.
  >
  >
* **P: Posso sincronizar ou definir o telefone de autenticação, e-mail de autenticação ou campos de telefone de autenticação alternativos em nome dos meus usuários?**

  > **R:** Os campos que podem ser definidos por um Administrador Global são definidos no artigo [Requisitos de dados SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **P: como o portal de registro determina quais opções mostrar aos meus usuários?**

  > **R:** O portal de registro de redefinição de senha só mostra as opções que você habilitou para os usuários. Essas opções são encontradas na seção **Diretiva de redefinição** de senha do usuário da guia **Configurar** do diretório. Por exemplo, se você não habilitar perguntas de segurança, os usuários não poderão se registrar para essa opção.
  >
  >
* **P: Quando um usuário é considerado registrado?**

  > **R:** Um usuário é considerado registrado no SSPR quando ele registrou, pelo menos, o **Número de métodos obrigatórios para a redefinição** que você definiu no [portal do Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Redefinição de senha

* **P: você impede que usuários realizem várias tentativas para redefinir uma senha em um curto período de tempo?**

  > **R:** sim, existem recursos de segurança internos na redefinição de senha para proteção contra uso indevido. 
  >
  > Os usuários poderão realizar apenas cinco tentativas de redefinição de senha dentro de um período de 24 horas, antes de serem bloqueados por 24 horas. 
  >
  > Os usuários poderão tentar validar um número de telefone, enviar um SMS ou validar questões de segurança e responder apenas cinco vezes em uma hora, antes de serem bloqueados por 24 horas. 
  >
  > Os usuários poderão enviar um email no máximo 10 vezes dentro de um período de 10 minutos, antes de serem bloqueados por 24 horas.
  >
  > Os contadores serão redefinidos quando o usuário redefinir sua senha.
  >
  >
* **P: Quanto tempo devo esperar para receber um e-mail, SMS ou telefonema da redefinição de senha?**

  > **R:** Emails, mensagens SMS e chamadas telefônicas devem chegar em menos de um minuto. O caso comum é de 5 a 20 segundos.
  > Se você não receber a notificação nesse período:
  > * Verifique a pasta Lixo Eletrônico.
  > * Verifique se o número ou o email de contato é aquele que você espera.
  > * Verifique se os dados de autenticação no diretório estão corretamente formatados, por exemplo, +1 4255551234 ou *contoso.com do usuário\@*. 
* **P: Quais idiomas são suportados por redefinição de senha?**

  > **R:** A interface do usuário de redefinição de senha, as mensagens SMS e as chamadas de voz estão localizadas nos mesmos idiomas com suporte no Office 365.
  >
  >
* **P: Quais partes da experiência de redefinição de senha ficam marcadas quando eu defina os itens de marca organizacional na guia configurar do meu diretório?**

  > **R:** O portal de redefinição de senha mostra o logotipo de sua organização e também permite que você configure o link Contate o administrador para apontar para uma URL ou um email personalizado. Qualquer e-mail enviado por redefinição de senha inclui o logotipo, cores e nome da sua organização no corpo do e-mail e é personalizado a partir das configurações para esse nome em particular.
  >
  >
* **P: como eu instruo os usuários sobre onde acessar para redefinir as suas senhas?**

  > **A:** Experimente algumas das sugestões em nosso artigo [de implantação do SSPR.](howto-sspr-deployment.md#plan-communications)
  >
  >
* **P: posso usar essa página em um dispositivo móvel?**

  > **R:** sim, essa página funciona em dispositivos móveis.
  >
  >
* **P: Você suporta desbloquear contas ativas locais do Diretório quando os usuários redefinem suas senhas?**

  > **A:** Sim. Quando um usuário redefine sua senha, se a regravação de senha for implantada através do Azure AD Connect, a conta do usuário é desbloqueada automaticamente quando ele redefine sua senha.
  >
  >
* **P: Como posso integrar a redefinição de senha diretamente na experiência de login da área de trabalho do meu usuário?**

  > **R:** se você for um cliente do Azure AD Premium, poderá instalar o Microsoft Identity Manager sem custo adicional e implantar a solução de redefinição de senha local.
  >
  >
* **P: posso definir perguntas de segurança diferentes para diferentes localidades?**

  > **R:** não, isso não é possível atualmente.
  >
  >
* **P: quantas perguntas posso configurar para a opção de autenticação com perguntas de segurança?**

  > **R:** Você pode configurar até 20 perguntas de segurança personalizadas no [portal do Azure](https://portal.azure.com).
  >
  >
* **P: qual o tamanho máximo das perguntas de segurança?**

  > **R:** perguntas de segurança podem ter de 3 a 200 caracteres.
  >
  >
* **P: qual o tamanho máximo que as respostas às perguntas de segurança podem ter?**

  > **R:** as respostas podem ter entre 3 e 40 caracteres.
  >
  >
* **P: As respostas duplicadas às perguntas de segurança são rejeitadas?**

  > **R:** sim, rejeitamos respostas duplicadas para perguntas de segurança.
  >
  >
* **P: um usuário pode registrar a mesma pergunta de segurança mais de uma vez?**

  > **R:** Não. Quando um usuário registra uma pergunta específica, ele não pode registrar essa pergunta uma segunda vez.
  >
  >
* **P: é possível definir um limite mínimo de perguntas de segurança para registro e redefinição?**

  > **R:** sim, um limite pode ser definido para o registro e outro para a redefinição. Podem ser necessárias de três a cinco perguntas para registro, e de três a cinco perguntas podem ser necessárias para redefinição.
  >
  >
* **P: configurei minha política para exigir que os usuários usem perguntas de segurança para redefinição, mas os administradores do Azure parecem ter configurado de maneira diferente.**

  > **R:** Esse é um comportamento esperado. A Microsoft impõe uma política padrão forte de redefinição de senha de dois portões para qualquer função de administrador do Azure. Isso impede que administradores usem perguntas de segurança. Mais informações sobre essa política podem ser encontradas no artigo [Políticas e restrições de senha no Azure Active Directory](concept-sspr-policy.md).
  >
  >
* **P: se um usuário tiver registrado mais do que o número máximo de perguntas obrigatórias para a redefinição, como as perguntas de segurança serão selecionadas durante a redefinição?**

  > **R:** *X* perguntas de segurança são selecionadas aleatoriamente do número total de perguntas que um usuário registrou, em que *X* é a quantidade que é definida para a opção **Número de perguntas obrigatórias para a redefinição**. Por exemplo, se um usuário tiver registrado cinco perguntas de segurança, mas apenas três forem necessárias para redefinir uma senha, três das cinco perguntas serão selecionadas aleatoriamente e serão apresentadas na reinicialização. Se o usuário der respostas erradas, o processo de seleção ocorrerá novamente para evitar hammering de perguntas.
  >
  >
* **P: por quanto tempo vale a senha de uso único por email e SMS?**

  > **R:** O tempo de vida da sessão para a redefinição de senha é de 15 minutos. Desde o início da operação de redefinição de senha, o usuário tem 15 minutos para redefinir sua senha. A senha única de e-mail e SMS são válidas por 5 minutos durante a sessão de redefinição de senha.
  >
  >
* **P: Posso impedir os usuários de redefinirem sua senha?**

  > **R:** sim, se você usar um grupo para habilitar SSPR, poderá remover um usuário individual do grupo que permite que os usuários redefinam suas senhas. Se o usuário for um Administrador Global, ele manterá a capacidade de redefinir sua senha, e isso não poderá ser desabilitado.
  >
  >

## <a name="password-change"></a>Alteração de senha

* **P: onde os usuários devem ir para alterar suas senhas?**

  > **R:** os usuários podem alterar suas senhas em qualquer lugar em que veem seus ícones ou imagens de perfil, como no canto superior direito das experiências do portal do [Office 365](https://portal.office.com) ou do [Painel de Acesso](https://myapps.microsoft.com). Os usuários podem alterar suas senhas na [página de perfil do Painel de Acesso](https://account.activedirectory.windowsazure.com/r#/profile). Os usuários também poderão ser solicitados a alterar suas senhas automaticamente na tela de conexão do Azure AD se elas expirarem. Por fim, os usuários poderão navegar até o [portal de alteração de senha do Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) diretamente se desejarem alterar suas senhas.
  >
  >
* **P: Meus usuários podem ser notificados no portal do Office quando sua senha no local expirar?**

  > **R:** sim, isso será possível hoje mesmo se você usar os Serviços de Federação do Active Directory (AD FS). Se você usar o AD FS, siga as instruções no artigo [Envio de declarações de política de senha com o AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396). Se você usar a sincronização de hash de senha, isso não será possível atualmente. Nós não sincronizamos políticas de senha locais e, portanto, não é possível postar as notificações de expiração para experiências de nuvem. Em ambos os casos, também é possível [notificar os usuários cujas senhas estejam prestes a expirar usando o PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **P: Posso impedir os usuários de alterem sua senha?**

  > **R:** para usuários somente na nuvem, as alterações de senha não podem ser bloqueadas. Para usuários locais, você pode definir a opção **O usuário não pode alterar a senha** como selecionada. Os usuários selecionados não podem alterar sua senha.
  >
  >

## <a name="password-management-reports"></a>Relatórios de gerenciamento de senha

* **P: Quanto tempo leva para os dados aparecerem nos relatórios de gerenciamento de senhas?**

  > **R:** os dados devem ser exibidos nos relatórios de gerenciamento de senha entre 5 e 10 minutos. Em algumas instâncias, pode levar até uma hora para que sejam exibidos.
  >
  >
* **P: Como posso filtrar os relatórios de gerenciamento de senhas?**

  > **R:** para filtrar os relatórios de gerenciamento de senhas, selecione a lupa pequena na extremidade direita dos rótulos de coluna, próximo ao início do relatório. Se você quiser fazer uma filtragem mais avançada, poderá baixar o relatório do Excel e criar uma tabela dinâmica.
  >
  >
* **P: qual é o número máximo de eventos armazenados nos relatórios de gerenciamento de senha?**

  > **R:** até 75 mil redefinições de senha ou eventos de registro de redefinição de senha são armazenados nos relatórios de gerenciamento de senha relativo ao período de, no máximo, 30 dias anteriores. Estamos trabalhando para expandir esse número e incluir mais eventos.
  >
  >
* **P: Até onde vão os relatórios de gerenciamento de senhas?**

  > **R:** os relatórios de gerenciamento de senha mostram operações ocorridas nos últimos 30 dias. Por enquanto, se você precisar arquivar esses dados, pode baixar os relatórios periodicamente e salvá-los em um local separado.
  >
  >
* **P: existe um número máximo de linhas que podem ser exibidas nos relatórios de gerenciamento de senha?**

  > **A:** Sim. Um máximo de 75 mil linhas pode aparecer nos relatórios de gerenciamento de senha, quer seja mostrado na interface do usuário ou baixado.
  >
  >
* **P: Existe uma API para acessar os dados de redefinição de senha ou relatórios de registro?**

  > **A:** Sim. Para saber como você pode acessar os dados de relatórios de redefinição de senha, consulte o [Azure Log Analytics REST API Reference](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>write-back de senha

* **P: como funciona os bastidores do write-back de senha?**

  > **R:** Consulte o artigo [Como funciona o write-back de senha](howto-sspr-writeback.md) para obter uma explicação detalhada do que acontece quando você habilita o write-back de senha e como os dados fluem pelo sistema novamente ao seu ambiente local.
  >
  >
* **P: Quanto tempo a regravação de senha leva para funcionar? Existe um atraso de sincronização como há com a sincronização de hash de senha?**

  > **R:** o write-back de senha é instantâneo. É um pipeline síncrono que funciona de forma essencialmente diferente da sincronização com hash de senha. O write-back de senha permite aos usuários obter comentários em tempo real sobre o sucesso da operação de redefinição ou de alteração de senha. O tempo médio para um write-back bem-sucedido de uma senha é abaixo de 500 ms.
  >
  >
* **P: se minha conta local estiver desabilitada, como minha conta ou meu acesso na nuvem será afetado?**

  > **R:** se sua ID local estiver desabilitada, a ID e o acesso na nuvem também estarão desabilitados no próximo intervalo de sincronização por meio do Azure AD Connect. Por padrão, esta sincronização ocorre a cada 30 minutos.
  >
  >
* **P: se minha conta local for restrita por uma política de senha do Active Directory local, o SSPR respeitará essa política quando eu alterar a senha?**

  > **R:** sim, o SSPR depende e age de acordo com a política de senha do Active Directory local. Esta política inclui a política de senha de domínio do Active Directory típica, bem como as políticas de senha refinadas definidas que são direcionadas para um usuário.
  >
  >
* **P: o write-back de senha funciona com quais tipos de conta?**

  > **R:** O write-back de senha funciona para contas de usuário que são sincronizadas do Active Directory no local ao Azure AD, incluindo federado, o hash de senha sincronizado e os usuários de autenticação de passagem.
  >
  >
* **P: A regravação de senha impõe as políticas de senha do meu domínio?**

  > **A:** Sim. O write-back de senha impõe a duração, o histórico e a complexidade da senha, filtros e qualquer outra restrição que você possa impor nas senhas no domínio local.
  >
  >
* **P: A regravação de senha é segura?  Como posso ter certeza que não serei hackeado?**

  > **R:** Sim, o write-back de senha é seguro. Para ler mais sobre as várias camadas de segurança implementadas pelo serviço de write-back de senha, verifique a seção [Segurança de write-back de senha](concept-sspr-writeback.md#password-writeback-security) no artigo [Visão geral do write-back de senha](howto-sspr-writeback.md).
  >
  >

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registre-se para redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md)
* [Você tem uma pergunta de licenciamento?](concept-sspr-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo está quebrado. Como faço para solucionar problemas de SSPR?](active-directory-passwords-troubleshoot.md)
