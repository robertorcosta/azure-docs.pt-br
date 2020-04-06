---
title: Solucionar problemas de domínio e certificados TLS/SSL
description: Encontre soluções para os problemas comuns que você pode encontrar quando configurar um certificado de domínio ou TLS/SSL no Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: d61b95c7136a4cbce11789a58d27cc1a164ae374
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668016"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Problemas de domínio e certificado TLS/SSL no Serviço de Aplicativos Do Azure

Este artigo lista problemas comuns que você pode encontrar quando configura um certificado de domínio ou TLS/SSL para seus aplicativos web no Azure App Service. Adicionalmente, descreve as possíveis causas e soluções para esses problemas.

Se você precisar de mais ajuda em qualquer ponto deste artigo, contate os especialistas do Azure nos [fóruns do Azure e do Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problemas de certificado

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Você não pode adicionar um certificado TLS/SSL vinculado a um aplicativo 

#### <a name="symptom"></a>Sintoma

Quando você adiciona uma vinculação TLS, você recebe a seguinte mensagem de erro:

"Falha ao adicionar associação SSL. Não é possível definir o certificado para o VIP existente, pois outro VIP já usa esse certificado."

#### <a name="cause"></a>Causa

Esse problema pode ocorrer se você tiver várias associações SSL com base em IP para o mesmo endereço IP entre vários aplicativos. Por exemplo, o aplicativo A tem um SSL com base em um IP com certificado antigo. O aplicativo B tem um SSL com base em IP com um certificado novo para o mesmo endereço IP. Quando você atualiza o aplicativo TLS vinculativo com o novo certificado, ele falha com esse erro porque o mesmo endereço IP está sendo usado para outro aplicativo. 

#### <a name="solution"></a>Solução 

Para corrigir esse problema, use um dos seguintes métodos:

- Exclua a associação SSL baseada em IP no aplicativo que usa o certificado antigo. 
- Crie uma nova associação SSL com base em IP que usa o novo certificado.

### <a name="you-cant-delete-a-certificate"></a>Não é possível excluir um certificado 

#### <a name="symptom"></a>Sintoma

Quando você tentar excluir um certificado, a seguinte mensagem de erro é exibida:

"Não é possível excluir o certificado porque ele está sendo usado atualmente em uma vinculação TLS/SSL. A vinculação TLS deve ser removida antes que você possa excluir o certificado."

#### <a name="cause"></a>Causa

Esse problema pode ocorrer se outro aplicativo usar o certificado.

#### <a name="solution"></a>Solução

Remova a vinculação TLS para esse certificado dos aplicativos. Em seguida, tente excluir o certificado. Se você ainda não conseguir excluir o certificado, limpe o cache do navegador da Internet e reabra o portal do Azure em uma nova janela do navegador. Em seguida, tente excluir o certificado.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Não é possível comprar um certificado do Serviço de Aplicativo 

#### <a name="symptom"></a>Sintoma
Não é possível comprar um [certificado do Serviço de Aplicativo do Azure](./configure-ssl-certificate.md#import-an-app-service-certificate) pelo portal do Azure.

#### <a name="cause-and-solution"></a>Causa e solução
Esse problema pode ocorrer por qualquer um dos seguintes motivos:

- O plano do Serviço de Aplicativo é Gratuito ou Compartilhado. Esses níveis de preços não suportam TLS. 

    **Solução**: Atualize o plano de serviço do aplicativo para o padrão.

- A assinatura não possui um cartão de crédito válido.

    **Solução**: adicione um cartão de crédito à sua assinatura. 

- A oferta de assinatura não dá suporte para compra de um certificado do Serviço de Aplicativo, como o Microsoft Student.  

    **Solução**: atualize sua assinatura. 

- A assinatura alcançou o limite de compras permitidas em uma assinatura.

    **Solução**: certificados do Serviço de Aplicativo têm um limite de 10 compras de certificado para os tipos de assinatura EA e Pagamento Conforme o Uso. Para outros tipos de assinatura, o limite é 3. Para aumentar o limite, contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O certificado de Serviço de Aplicativo foi marcado como fraude. Você recebeu mensagem de erro a seguir: "Seu certificado foi sinalizado para uma possível fraude. A solicitação está sendo examinada. Se o certificado não for utilizável dentro de 24 horas, entre em contato com o Suporte azure."

    **Solução**: se o certificado estiver marcado como fraude e não for resolvido após 24 horas, siga estas etapas:

    1. Entre no [portal do Azure](https://portal.azure.com).
    2. Vá para **Certificados do Serviço de Aplicativo** e selecione o certificado.
    3. Selecione **a configuração** > do certificado**Etapa 2: Verifique a** > **verificação de domínio**. Essa etapa envia uma notificação de email para o provedor de certificados do Azure para resolver o problema.

## <a name="custom-domain-problems"></a>Problemas de domínio personalizados

### <a name="a-custom-domain-returns-a-404-error"></a>Um domínio personalizado retorna um erro 404 

#### <a name="symptom"></a>Sintoma

Ao navegar para o site usando o nome de domínio personalizado, você pode receber a seguinte mensagem de erro:

"Erro 404 - aplicativo Web não encontrado."

#### <a name="cause-and-solution"></a>Causa e solução

**Causa 1** 

O domínio personalizado configurado não tem um registro CNAME ou A. 

**Solução para a causa 1**

- Se você adicionou um registro A, verifique se um registro TXT também foi adicionado. Para obter mais informações, consulte [Criar o registro A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Se você não precisar usar o domínio raiz para o aplicativo, é recomendável usar um registro CNAME em vez de um registro A.
- Não use um registro CNAME e um registro A para o mesmo domínio. Esse problema pode causar um conflito e impedir que o domínio seja resolvido. 

**Causa 2** 

O navegador da Internet ainda pode estar armazenando o endereço IP antigo em cache para o domínio. 

**Solução para a Causa 2**

Limpe o navegador. Para dispositivos do Windows, você pode executar o comando `ipconfig /flushdns`. Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o domínio aponta para o endereço IP do aplicativo. 

### <a name="you-cant-add-a-subdomain"></a>Não é possível adicionar um subdomínio 

#### <a name="symptom"></a>Sintoma

Não é possível adicionar um novo nome do host a um aplicativo para atribuir um subdomínio.

#### <a name="solution"></a>Solução

- Verifique com o administrador de assinatura para certificar-se de que você tem permissões para adicionar um nome do host ao aplicativo.
- Se você precisar de mais subdomínios, recomendamos que você altere a hospedagem de domínio para O Serviço de Nome de Domínio do Azure (DNS). Usando o DNS do Azure, será possível adicionar 500 nomes do host ao aplicativo. Para obter mais informações, consulte [Adicionar um subdomínio](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>DNS não pode ser resolvido

#### <a name="symptom"></a>Sintoma

Você recebeu a mensagem de erro a seguir:

"O registro DNS não pôde ser localizado."

#### <a name="cause"></a>Causa
Esse problema ocorre por um dos seguintes motivos:

- O período de TTL (vida útil) não expirou. Verifique a configuração de DNS para seu domínio para determinar o valor de TTL e aguarde até que o período expire.
- A configuração do DNS está incorreta.

#### <a name="solution"></a>Solução
- Aguarde 48 horas para que esse problema seja resolvido sozinho.
- Se você puder alterar a configuração de TTL em sua configuração de DNS, altere o valor para 5 minutos para ver se isso resolve o problema.
- Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o domínio aponta para o endereço IP do aplicativo. Se isso não estiver, configure o registro A para o endereço IP correto do aplicativo.

### <a name="you-need-to-restore-a-deleted-domain"></a>É necessário restaurar um domínio excluído 

#### <a name="symptom"></a>Sintoma
O domínio não está mais visível no portal do Azure.

#### <a name="cause"></a>Causa 
O proprietário da assinatura pode ter excluído acidentalmente o domínio.

#### <a name="solution"></a>Solução
Se o domínio foi excluído há menos de sete dias, o domínio ainda não iniciou o processo de exclusão. Nesse caso, você poderá comprar o mesmo domínio novamente no portal do Azure com a mesma assinatura. (Certifique-se de digitar o nome de domínio exato na caixa de pesquisa.) Você não será cobrado novamente por este domínio. Se o domínio foi excluído há mais de sete dias, entre em contato com [o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter ajuda na restauração do domínio.

## <a name="domain-problems"></a>Problemas de domínio

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Você comprou um certificado TLS/SSL para o domínio errado

#### <a name="symptom"></a>Sintoma

Você comprou um certificado do Serviço de Aplicativo para o domínio incorreto. Não é possível atualizar o certificado para usar o domínio correto.

#### <a name="solution"></a>Solução

Exclua o certificado e, em seguida, compre um novo.

Se o certificado atual que usa o domínio incorreto estiver no estado "Emitido", você também será cobrado pelo certificado. Certificados de Serviço de Aplicativo não são reembolsáveis, mas você pode contatar o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ver se há outras opções. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Um certificado do Serviço de Aplicativo foi renovado, mas o aplicativo mostra o certificado antigo 

#### <a name="symptom"></a>Sintoma

O certificado do Serviço de Aplicativo foi renovado, mas o aplicativo que usa o certificado do Serviço de Aplicativo ainda está usando o certificado antigo. Além disso, você recebeu um aviso de que o protocolo HTTPS é necessário.

#### <a name="cause"></a>Causa 
O App Service sincroniza automaticamente seu certificado dentro de 48 horas. Ao girar ou atualiza um certificado, às vezes, o aplicativo ainda está recuperando o certificado antigo e não o certificado atualizado recentemente. O motivo é que o trabalho para sincronizar o recurso de certificado ainda não foi executado. Clique em Sincronizar. A operação de sincronização atualiza automaticamente as vinculações do nome de host para o certificado no App Service sem causar qualquer tempo de inatividade aos seus aplicativos.
 
#### <a name="solution"></a>Solução

Você pode forçar uma sincronização do certificado:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione os **Certificados do Serviço de Aplicativo** e, em seguida, selecione o certificado.
2. Selecione **Rekey e Sync**e selecione **Sync**. A sincronização leva algum tempo para terminar. 
3. Quando a sincronização for concluída, você verá a notificação a seguir: "Todos os recursos atualizados com êxito com o certificado mais recente."

### <a name="domain-verification-is-not-working"></a>A verificação de domínio não está funcionando 

#### <a name="symptom"></a>Sintoma 
O certificado do Serviço de Aplicativo requer a verificação de domínio antes do certificado estar pronto para uso. Ao selecionar **Verificar**, o processo falha.

#### <a name="solution"></a>Solução
Verifique seu domínio manualmente adicionando um registro TXT:
 
1.  Acesse o provedor de Serviço de Nomes de Domínio (DNS) que hospeda o nome de domínio.
2.  Adicione um registro TXT ao domínio que usa o valor do token de domínio exibido no portal do Azure. 

Aguarde alguns minutos para que a propagação de DNS seja executada e, em seguida, selecione o botão **Atualizar** para disparar a verificação. 

Como uma alternativa, é possível usar o método de páginas da Web em HTML para verificar o domínio manualmente. Esse método permite que a autoridade de certificação confirme a propriedade do domínio do domínio para o qual o certificado é emitido.

1.  Crie um arquivo HTML nomeado {token de verificação de domínio}.html. O conteúdo desse arquivo deve ser o valor do token de verificação de domínio.
3.  Carregue esse arquivo na raiz do servidor Web que hospeda o domínio.
4.  Selecione **Atualizar** para verificar o status do certificado. Pode demorar alguns minutos até a verificação ser concluída.

Por exemplo, se você estiver comprando um certificado padrão do azure.com utilizando o token de verificação de domínio 1234abcd, uma solicitação da Web feita para https://azure.com/1234abcd.html deverá retornar 1234abcd. 

> [!IMPORTANT]
> A ordem de um certificado tem apenas 15 dias para concluir a operação de verificação de domínio. Após 15 dias, a autoridade de certificação negará o certificado e você não será cobrado pelo certificado. Nessa situação, exclua esse certificado e tente novamente.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Não é possível comprar um domínio

#### <a name="symptom"></a>Sintoma
Não é possível comprar um domínio do serviço de aplicativo no portal do Azure.

#### <a name="cause-and-solution"></a>Causa e solução

Esse problema ocorre por um dos seguintes motivos:

- Não há cartão de crédito na assinatura do Azure ou o cartão de crédito é inválido.

    **Solução**: adicione um cartão de crédito à sua assinatura.

- Você não é o proprietário da assinatura, portanto, não tem permissão para comprar um domínio.

    **Solução**: [atribua a função de Proprietário](../role-based-access-control/role-assignments-portal.md) à sua conta. Ou contate o administrador da assinatura para obter permissão para comprar um domínio.
- Você atingiu o limite para comprar os domínios na sua assinatura. O limite atual é 20.

    **Solução**: para solicitar um aumento até o limite, contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O tipo de assinatura do Azure não dá suporte para compra de um domínio do Serviço de Aplicativo.

    **Solução**: atualize a assinatura do Azure para outro tipo de assinatura, como uma assinatura de Pagamento Conforme o Uso.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Não é possível adicionar um nome do host a um aplicativo 

#### <a name="symptom"></a>Sintoma

Ao adicionar um nome do host, o processo falha ao validar e verificar o domínio.

#### <a name="cause"></a>Causa 

Esse problema ocorre por um dos seguintes motivos:

- Você não tem permissão para adicionar um nome do host.

    **Solução**: solicite ao administrador da assinatura a conceder-lhe permissão para adicionar um nome do host.
- Não foi possível verificar a propriedade do domínio.

    **Solução**: verifique se o registro A ou CNAME está configurado corretamente. Para mapear um domínio personalizado para um aplicativo, crie um registro A ou um registro CNAME. Se quiser utilizar um domínio raiz, use os registros TXT e A:

    |Tipo de registro|Host|Apontar para|
    |------|------|-----|
    |Um|@|Endereço IP para um aplicativo|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Perguntas frequentes

**Tenho que configurar meu domínio personalizado para o meu site assim que comprá-lo?**

Quando você compra um domínio no portal Azure, o aplicativo App Service é configurado automaticamente para usar esse domínio personalizado. Você não tem que dar nenhum passo adicional. Para obter mais informações, assista [self help do serviço de aplicativos do Azure: adicione um nome de domínio personalizado](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) no Canal9.

**Posso usar um domínio comprado no portal Azure para apontar para uma VM Azure?**

Sim, você pode apontar o domínio para uma VM. Para obter mais informações, consulte [Usar o DNS do Azure para fornecer as configurações de domínio personalizadas para um serviço do Azure](../dns/dns-custom-domain.md).

**Meu domínio está hospedado pelo GoDaddy ou pelo Azure DNS?**

Os domínios do serviço de aplicativos usam o GoDaddy para registro de domínios e o Azure DNS para hospedar os domínios. 

**Eu tenho renovação automática ativada, mas ainda recebi um aviso de renovação para o meu domínio por e-mail. O que eu devo fazer?**

Se você tiver a renovação automática ativada, você não precisa tomar nenhuma ação. O e-mail de aviso é fornecido para informar que o domínio está próximo de expirar e para renovar manualmente se a renovação automática não estiver ativada.

**Serei cobrado pelo Azure DNS hospedando meu domínio?**

O custo inicial da compra de domínio se aplica apenas ao registro de domínio. Além do custo de inscrição, existem taxas incorrendo para o Azure DNS com base no seu uso. Para obter mais informações, consulte [os preços do Azure DNS](https://azure.microsoft.com/pricing/details/dns/) para obter mais detalhes.

**Eu comprei meu domínio mais cedo do portal Azure e quero passar da hospedagem godaddy para hospedagem Do Azure DNS. Como posso fazer isso?**

Não é obrigatório migrar para hospedagem Do Azure DNS. Se você quiser migrar para o Azure DNS, a experiência de gerenciamento de domínio no portal Azure fornece informações sobre as etapas necessárias para se mudar para o Azure DNS. Se o domínio foi comprado através do App Service, a migração da hospedagem godaddy para o Azure DNS é um procedimento relativamente perfeito.

**Gostaria de comprar meu domínio do App Service Domain, mas posso hospedar meu domínio no GoDaddy em vez do DNS do Azure?**

A partir de 24 de julho de 2017, os domínios do App Service comprados no portal estão hospedados no Azure DNS. Se você preferir usar um provedor de hospedagem diferente, você deve ir ao seu site para obter uma solução de hospedagem de domínio.

**Tenho que pagar pela proteção de privacidade do meu domínio?**

Ao comprar um domínio através do portal Azure, você pode optar por adicionar privacidade sem nenhum custo adicional. Este é um dos benefícios de comprar seu domínio através do Azure App Service.

**Se eu decidir que não quero mais meu domínio, posso pegar meu dinheiro de volta?**

Quando você compra um domínio, você não é cobrado por um período de cinco dias, durante o qual você pode decidir que não quer o domínio. Se você decidir que não quer o domínio dentro desse período de cinco dias, você não será cobrado. (.os domínios do reino unido são uma exceção a isso. Se você comprar um domínio .uk, você será cobrado imediatamente e não poderá ser reembolsado.)

**Posso usar o domínio em outro aplicativo do Azure App Service na minha assinatura?**

Sim. Quando você acessa a lâmina Dedomínios Personalizados e TLS no portal Azure, você vê os domínios que você comprou. Você pode configurar seu aplicativo para usar qualquer um desses domínios.

**Posso transferir um domínio de uma assinatura para outra assinatura?**

Você pode mover um domínio para outro grupo de assinatura/recurso usando o [cmdlet Move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell.

**Como posso gerenciar meu domínio personalizado se não tenho atualmente um aplicativo azure App Service?**

Você pode gerenciar seu domínio mesmo que não tenha um App Service Web App App App App. O domínio pode ser usado para serviços do Azure como máquina virtual, armazenamento etc. Se você pretende usar o domínio para aplicativos web de serviço de aplicativo, então você precisa incluir um Aplicativo da Web que não esteja no plano de serviço de aplicativo gratuito para vincular o domínio ao seu aplicativo web.

**Posso mover um aplicativo web com um domínio personalizado para outra assinatura ou do App Service Environment v1 para V2?**

Sim, você pode mover seu aplicativo web através de assinaturas. Siga as orientações em [Como mover recursos no Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Existem algumas limitações ao mover o aplicativo web. Para obter mais informações, consulte [Limitações para mover recursos do App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

Depois de mover o aplicativo web, as vinculações do nome host dos domínios dentro da configuração de domínios personalizados devem permanecer as mesmas. Não são necessárias etapas adicionais para configurar as vinculações de nome do host.
