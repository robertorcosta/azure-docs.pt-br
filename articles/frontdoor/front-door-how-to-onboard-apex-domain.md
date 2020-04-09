---
title: A bordo de um domínio raiz ou ápice para um portal front door existente - Azure
description: Aprenda a embarcar em um domínio raiz ou ápice para uma porta frontal existente usando o portal Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 4b74338f22a82d76ef13126ee0862b841bd89a99
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878877"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>A bordo de um domínio raiz ou ápice em sua porta da frente
O Azure Front Door usa registros CNAME para validar a propriedade de domínios para onboarding de domínios personalizados. Além disso, o Front Door não expõe o endereço IP frontend associado ao seu perfil da Porta da Frente e, portanto, você não pode mapear seu domínio ápice para um endereço IP, se a intenção é abordo dele para o Azure Front Door.

O protocolo DNS previne a atribuição de registros CNAME no ápice da zona. Por exemplo, se `contoso.com`o seu domínio é; você pode criar registros `somelabel.contoso.com`CNAME para; mas você não pode criar `contoso.com` CNAME para si mesmo. Essa restrição apresenta um problema para os proprietários de aplicativos que têm aplicativos balanceados de carga atrás do Azure Front Door. Uma vez que o uso de um perfil da Porta da Frente requer a criação de um registro CNAME, não é possível apontar para o perfil da Porta da Frente a partir do ápice da região.

Este problema é resolvido usando registros de alias no DNS do Azure. Ao contrário dos registros CNAME, os registros de alias são criados no ápice da região e os proprietários de aplicativos podem usá-lo para apontar o registro do ápice da região para um perfil do Front Door que tem pontos finais públicos. Os proprietários de aplicativos apontam para o mesmo perfil do Front Door que é usado para qualquer outro domínio dentro de sua região de DNS. Por `contoso.com` exemplo, `www.contoso.com` e pode apontar para o mesmo perfil da Porta da Frente. 

Mapear seu ápice ou domínio raiz para o perfil da Porta da Frente basicamente requer o achatamento do CNAME ou perseguição de DNS, que é um mecanismo onde no provedor de DNS resolve recursivamente a entrada CNAME até atingir um endereço IP. Essa funcionalidade é suportada pelo Azure DNS para pontos finais da Porta da Frente. 

> [!NOTE]
> Existem outros provedores de DNS também que suportam o achatamento do CNAME ou perseguição de DNS, no entanto, o Azure Front Door recomenda o uso do Azure DNS para seus clientes para hospedar seus domínios.

Você pode usar o portal Azure para embarcar um domínio apex em sua Porta da Frente e habilitar HTTPS nele associando-o a um certificado de rescisão TLS. Os domínios apex também são chamados de domínios raiz ou nu.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Crie um registro de alias que aponte para o perfil da Porta da Frente
> * Adicione o domínio raiz à porta da frente
> * Configurar HTTPS no domínio raiz

> [!NOTE]
> Este tutorial requer que você já tenha um perfil da Porta da Frente criado. Consulte outros tutoriais como [Quickstart: Crie uma porta frontal](./quickstart-create-front-door.md) ou crie uma porta da frente com HTTP para o [redirecionamento HTTPS](./front-door-how-to-redirect-https.md) para começar.

## <a name="create-an-alias-record-for-zone-apex"></a>Crie um registro de alias para o ápice da zona

1. Abra a configuração **do Azure DNS** para que o domínio seja a bordo.
2. Crie ou edite o registro para o ápice da região.
3. Selecione o **tipo** de registro como _Um_ registro e, em seguida, selecione _Sim_ para conjunto de **registros Alias**. **O tipo de alias** deve ser definido como _recurso Do Zure_.
4. Escolha a assinatura do Azure onde o perfil da Porta da Frente está hospedado e selecione o recurso Porta da Frente na lista suspensa **do recurso Dozure.**
5. Clique em **OK** para enviar suas alterações.

    ![Registro de alias para ápice de zona](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. A etapa acima criará um registro de ápice de zona apontando para o recurso Front Door `afdverify.contosonews.com`e `afdverify.<name>.azurefd.net` também um mapeamento de registro CNAME 'afdverify' (exemplo - ) para o qual será usado para onboarding do domínio no perfil da Porta da Frente.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>A bordo do domínio personalizado em sua porta da frente

1. Na guia de designer front door, clique no ícone '+' na seção Hosts do Frontend para adicionar um novo domínio personalizado.
2. Digite o nome de domínio raiz ou ápice `contosonews.com`no campo de nome de host personalizado, exemplo .
3. Uma vez que o mapeamento CNAME do domínio para a porta da frente seja validado, clique em **Adicionar** para adicionar o domínio personalizado.
4. Clique **em Salvar** para enviar as alterações.

![Menu de domínio personalizado](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Habilite https em seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e na seção **Domínio personalizado HTTPS,** altere o status para **Ativado**.
2. Selecione o **tipo de gerenciamento de certificados** para _'Usar meu próprio certificado'_.

> [!WARNING]
> O tipo de gerenciamento gerenciado do Front Door não é suportado atualmente para domínios ápices ou root. A única opção disponível para habilitar HTTPS em um ápice ou domínio raiz para o Front Door é usar seu próprio certificado TLS/SSL personalizado hospedado no Azure Key Vault.

3. Certifique-se de configurar as permissões certas para o Front Door acessar seu cofre de chaves, conforme observado na ui, antes de prosseguir para a próxima etapa.
4. Escolha uma **conta do Key Vault** da sua assinatura atual e selecione a versão **secreta** e **secreta** apropriada para mapear para o certificado certo.
5. Clique em **Atualizar** para salvar a seleção e, em seguida, clique **em Salvar**.
6. Clique **em Atualizar** após alguns minutos e clique novamente no domínio personalizado para ver o progresso do provisionamento de certificados. 

> [!WARNING]
> Certifique-se de que você criou regras de roteamento apropriadas para o seu domínio apex ou adicionou o domínio às regras de roteamento existentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).