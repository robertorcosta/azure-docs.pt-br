---
title: Conceitos de gestão para serviços de domínio Azure AD | Microsoft Docs
description: Saiba como administrar um domínio gerenciado do Azure Active Directory Domain Services e o comportamento de contas de usuário e senhas
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 682935fa2324b8de4992ab2f90c7f71e05c4f8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264226"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Conceitos de gerenciamento de contas de usuário, senhas e administração em Serviços de Domínio do Diretório Ativo do Azure

Quando você cria e executa um domínio gerenciado aD DS (AD DS) do Azure Active Directory, há algumas diferenças de comportamento em comparação com um ambiente AD DS tradicional no local. Você usa as mesmas ferramentas administrativas no Azure AD DS como um domínio autogerenciado, mas não pode acessar diretamente os controladores de domínio (DC). Há também algumas diferenças de comportamento para políticas de senha e hashes de senha, dependendo da fonte da criação da conta de usuário.

Este artigo conceitual detalha como administrar um domínio gerenciado pelo Azure AD DS e o comportamento diferente das contas de usuário, dependendo da maneira como elas são criadas.

## <a name="domain-management"></a>Gerenciamento de domínios

No Azure AD DS, os controladores de domínio (DCs) que contêm todos os recursos, como usuários e grupos, credenciais e políticas fazem parte do serviço gerenciado. Para redundância, dois DCs são criados como parte de um domínio gerenciado pelo Azure AD DS. Você não pode fazer login nesses DCs para executar tarefas de gerenciamento. Em vez disso, você cria uma VM de gerenciamento que se juntou ao domínio gerenciado pelo Azure AD DS e, em seguida, instala suas ferramentas de gerenciamento DeD AD regulares. Você pode usar os snap-ins do Centro Administrativo do Diretório Ativo ou do Microsoft Management Console (MMC) como objetos DNS ou De Política de Grupo, por exemplo.

## <a name="user-account-creation"></a>Criação de conta de usuário

As contas de usuário podem ser criadas no Azure AD DS de várias maneiras. A maioria das contas de usuário são sincronizadas a partir do Azure AD, que também pode incluir a conta de usuário sincronizada a partir de um ambiente AD DS no local. Você também pode criar contas manualmente diretamente no Azure AD DS. Alguns recursos, como sincronização inicial de senha ou política de senha, se comportam de forma diferente dependendo de como e onde as contas de usuário são criadas.

* A conta de usuário pode ser sincronizada a partir do Azure AD. Isso inclui contas de usuário somente na nuvem criadas diretamente no Azure AD e contas de usuários híbridos sincronizadas a partir de um ambiente AD DS local usando o Azure AD Connect.
    * A maioria das contas de usuário no Azure AD DS são criadas através do processo de sincronização do Azure AD.
* A conta de usuário pode ser criada manualmente em um domínio gerenciado pelo Azure AD DS e não existe no Azure AD.
    * Se você precisar criar contas de serviço para aplicativos que só são executados no Azure AD DS, você pode criá-las manualmente no domínio gerenciado. Como a sincronização é uma forma do Azure AD, as contas de usuário criadas no Azure AD DS não são sincronizadas de volta ao Azure AD.

## <a name="password-policy"></a>Política de senha

O Azure AD DS inclui uma política de senha padrão que define configurações para coisas como bloqueio de conta, idade máxima de senha e complexidade de senha. Configurações como política de bloqueio de contas se aplicam a todos os usuários no Azure AD DS, independentemente de como o usuário foi criado como descrito na seção anterior. Algumas configurações, como comprimento mínimo de senha e complexidade de senha, só se aplicam a usuários criados diretamente no Azure AD DS.

Você pode criar suas próprias políticas de senha personalizadas para substituir a política padrão no Azure AD DS. Essas políticas personalizadas podem então ser aplicadas a grupos específicos de usuários, conforme necessário.

Para obter mais informações sobre as diferenças na forma como as políticas de senha são aplicadas dependendo da fonte de criação do usuário, consulte políticas de bloqueio de [senha e conta em domínios gerenciados][password-policy].

## <a name="password-hashes"></a>Hashes senha

Para autenticar os usuários no domínio gerenciado, o Azure AD DS precisa de hashes de senha em um formato adequado para a autenticação NTLM (Gerenciador de LAN NT) e Kerberos. O Azure AD não gera nem armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos, até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena credenciais de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente essas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

Para contas de usuário somente de nuvem, os usuários devem alterar suas senhas antes de usar o Azure AD DS. Esse processo de alteração de senhas faz com que as hashes de senha para a autenticação Kerberos e NTLM sejam geradas e armazenadas no Azure AD.

Para usuários sincronizados a partir de um ambiente AD DS no local usando o Azure AD Connect, [habilite a sincronização de hashes de senha][hybrid-phs].

> [!IMPORTANT]
> O Azure AD Connect só sincroniza hashes de senha legados quando você habilita o Azure AD DS para o seu inquilino Azure AD. Os hashes de senha legados não são usados se você usar apenas o Azure AD Connect para sincronizar um ambiente AD DS no local com o Azure AD.
>
> Se seus aplicativos legados não usarem autenticação NTLM ou obrigações simples lDAP, recomendamos que você desative a sincronização de hash de senha NTLM para o Azure AD DS. Para obter mais informações, consulte [Desativar suítes de cifra fraca e sincronização de hash de credencial NTLM][secure-domain].

Uma vez configurado adequadamente, as hashes de senha utilizáveis são armazenadas no domínio gerenciado Azure AD DS. Se você excluir o domínio gerenciado do Azure AD DS, todas as hashes de senha armazenadas nesse ponto também serão excluídas. As informações de credencial sincronizadas no Azure AD não podem ser reutilizadas se você criar posteriormente um domínio gerenciado pelo Azure AD DS - você deve reconfigurar a sincronização hash de senha para armazenar os hashes de senha novamente. As VMs ou os usuários previamente unidos ao domínio não poderão autenticar imediatamente – o Azure AD precisa gerar e armazenar as hashes de senha no novo domínio gerenciado do Azure AD DS. Para obter mais informações, confira [Processo de sincronização de hash de senha para o Azure AD DS e o Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes do AD DS locais. Não há suporte para instalar o Azure AD Connect em um domínio gerenciado do Azure AD DS para sincronizar objetos de volta ao Azure AD.

## <a name="forests-and-trusts"></a>Florestas e relações de confiança

Uma *floresta* é um construto lógico usado pelo Active Directory Domain Services (AD DS) para agrupar um ou mais *domínios*. Os domínios então armazenam objetos para usuários ou grupos e fornecem serviços de autenticação.

No Azure AD DS, a floresta contém apenas um domínio. As florestas AD DS no local geralmente contêm muitos domínios. Em grandes organizações, especialmente após fusões e aquisições, você pode acabar com várias florestas locais que, em seguida, contêm vários domínios.

Por padrão, um domínio gerenciado pelo Azure AD DS é criado como uma floresta *de usuários.* Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente do AD DS local. As contas de usuário podem autenticar diretamente contra o domínio gerenciado pelo Azure AD DS, como fazer login em uma VM com um domínio. Uma floresta de usuários funciona quando os hashes de senha podem ser sincronizados e os usuários não estão usando métodos exclusivos de login, como autenticação de cartão inteligente.

Em uma floresta de *recursos* Azure AD DS, os usuários autenticam sobre uma *confiança* florestal unidirecional a partir de seus AD DS no local. Com essa abordagem, os objetos do usuário e os hashes de senha não são sincronizados com o Azure AD DS. Os objetos e credenciais do usuário só existem no AD DS no local. Essa abordagem permite que as empresas hospedem recursos e plataformas de aplicativos no Azure que dependem da autenticação clássica, como LDAPS, Kerberos ou NTLM, mas quaisquer problemas ou preocupações de autenticação são removidos. As florestas de recursos Azure AD DS estão atualmente em pré-visualização.

Para obter mais informações sobre os tipos de florestas no Azure AD DS, veja quais são as [florestas][concepts-trust] [de recursos?][concepts-forest]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKUs

No Azure AD DS, o desempenho disponível e os recursos são baseados no SKU. Você seleciona um SKU quando cria o domínio gerenciado e pode alternar SKUs à medida que seus requisitos de negócios mudam após a implantação do domínio gerenciado. A tabela a seguir descreve as SKUs disponíveis e as diferenças entre elas:

| Nome do SKU   | Contagem máxima de objetos | Frequência de backup | Número máximo de fundos florestais de saída |
|------------|----------------------|------------------|----|
| Standard   | Ilimitado            | A cada 7 dias     | 0  |
| Enterprise | Ilimitado            | A cada 3 dias     | 5  |
| Premium    | Ilimitado            | Diário            | 10 |

Antes dessas SKUs AD AD Azure, um modelo de faturamento baseado no número de objetos (contas de usuário e computador) no domínio gerenciado do Azure AD DS foi usado. Não há mais preços variáveis com base no número de objetos no domínio gerenciado.

Para obter mais informações, consulte a [página de preços do Azure AD DS][pricing].

### <a name="managed-domain-performance"></a>Desempenho gerenciado de domínio

O desempenho do domínio varia de acordo com a forma como a autenticação é implementada para um aplicativo. Recursos adicionais de computação podem ajudar a melhorar o tempo de resposta da consulta e reduzir o tempo gasto em operações de sincronização. À medida que o nível de SKU aumenta, os recursos de computação disponíveis para o domínio gerenciado são aumentados. Monitore o desempenho de suas aplicações e planeje os recursos necessários.

Se o seu negócio ou aplicativo exigir alterações e você precisar de poder de computação adicional para o seu domínio gerenciado pelo Azure AD DS, você pode mudar para um SKU diferente.

### <a name="backup-frequency"></a>Frequência de backup

A freqüência de backup determina com que frequência um instantâneo do domínio gerenciado é tomado. Backups são um processo automatizado gerenciado pela plataforma Azure. No caso de um problema com seu domínio gerenciado, o suporte do Azure pode ajudá-lo a restaurar o backup. Como a sincronização ocorre apenas de uma maneira *a partir do* Azure AD, quaisquer problemas em um domínio gerenciado pelo Azure AD DS não afetarão os ambientes e funcionalidades do Azure AD Ou no local.

À medida que o nível de SKU aumenta, a frequência desses instantâneos de backup aumenta. Revise os requisitos de negócios e o RPO (Recovery Point Objective, objetivo de ponto de recuperação) para determinar a freqüência de backup necessária para o domínio gerenciado. Se os requisitos de sua empresa ou aplicativo mudarem e você precisar de backups mais freqüentes, você pode mudar para um SKU diferente.

### <a name="outbound-forests"></a>Florestas de saída

A seção anterior detalhava os fundos florestais de saída unidirecional de um domínio gerenciado pelo Azure AD DS para um ambiente AD DS no local (atualmente em visualização). O SKU determina o número máximo de fundos florestais que você pode criar para um domínio gerenciado pelo Azure AD DS. Revise os requisitos de seus negócios e aplicativos para determinar quantas confianças você realmente precisa e escolha o Azure AD SKU apropriado. Novamente, se os requisitos do seu negócio mudarem e você precisar criar fundos florestais adicionais, você pode mudar para um SKU diferente.

## <a name="next-steps"></a>Próximas etapas

Para começar, [crie um domínio gerenciado pelo Azure AD DS][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
