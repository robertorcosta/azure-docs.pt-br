---
title: Provisionar um usuário sob demanda usando Azure Active Directory
description: Forçar sincronização
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 7799e873afb117481cebafd982df59a3267f4405
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051546"
---
# <a name="on-demand-provisioning"></a>Provisionamento sob demanda
O provisionamento sob demanda permite provisionar um usuário em um aplicativo em segundos. Você pode usar a capacidade de solucionar problemas de configuração rapidamente, validar expressões definidas, filtros de escopo de teste e muito mais. 

## <a name="how-to-use-on-demand-provisioning"></a>Como usar o provisionamento sob demanda 

1. Faça logon no **Portal do Azure**.
2. Navegue até **aplicativos empresariais**.
3. Selecione seu aplicativo e navegue até a página de configuração de provisionamento.
4. Configure o provisionamento fornecendo suas credenciais de administrador.
5. Clique em **provisionar sob demanda**.
6. Pesquisar um usuário por nome, sobrenome, nome de exibição, nome principal de usuário ou email.
7. Selecione provisionar na parte inferior da página.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Sob demanda provisionar um usuário.":::

## <a name="understanding-the-provisioning-steps"></a>Noções básicas sobre as etapas de provisionamento
O recurso de provisionamento sob demanda tenta mostrar as etapas que o serviço de provisionamento usa ao provisionar um usuário. Normalmente, há cinco etapas para provisionar um usuário, e uma ou mais das etapas a seguir serão mostradas na experiência de provisionamento sob demanda.

### <a name="step-1-test-connection"></a>Etapa 1: testar a conexão
O serviço de provisionamento tenta autorizar o acesso ao aplicativo de destino fazendo uma solicitação para um "usuário de teste". O serviço de provisionamento espera uma resposta indicando que está autorizado a continuar com as etapas de provisionamento. Esta etapa só é mostrada quando há uma falha na etapa. Ele não é mostrado na experiência de provisionamento sob demanda quando a etapa é bem-sucedida. 

**Dicas de solução de problemas**
* Verifique se você forneceu credenciais válidas para o aplicativo de destino, como o token secreto e a URL do locatário. As credenciais necessárias variam de acordo com o aplicativo. Os tutoriais de configuração detalhados podem ser encontrados [aqui](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 
* Verifique se o aplicativo de destino dá suporte à filtragem nos atributos correspondentes definidos na folha mapeamentos de atributo. Talvez seja necessário verificar a documentação da API fornecida pelo desenvolvedor do aplicativo para entender os filtros aos quais eles dão suporte.  
* Para aplicativos SCIM, você pode usar uma ferramenta como o postmaster para garantir que o aplicativo responda às solicitações de autorização à medida que o serviço de provisionamento do Azure AD espera. Uma solicitação de exemplo pode ser encontrada [aqui](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3).

### <a name="step-2-import-user"></a>Etapa 2: importar usuário
Em seguida, o serviço de provisionamento recupera o usuário do sistema de origem. Os atributos de usuário recuperados pelo serviço são usados posteriormente para avaliar se o usuário está no escopo do provisionamento, verificando o sistema de destino para um usuário existente e determinando quais atributos de usuário devem ser exportados para o sistema de destino. 

**Exibir detalhes**

A seção Exibir detalhes mostra as propriedades do usuário que foram importados do sistema de origem (por exemplo, Azure AD).

**Dicas de solução de problemas**
* A importação do usuário pode falhar quando o atributo correspondente estiver ausente no objeto de usuário no sistema de origem. Você pode resolver essa falha atualizando o objeto de usuário com um valor para o atributo correspondente ou alterando o atributo correspondente em sua configuração de provisionamento.  
* Se um atributo que você estava esperando estiver faltando na lista que foi importada, verifique se o atributo tem um valor no objeto de usuário no sistema de origem. O serviço de provisionamento atualmente não dá suporte ao provisionamento de atributos nulos. 
* Verifique se a página de mapeamento de atributo de configuração de provisionamento contém o atributo que você está esperando. 

### <a name="step-3-determine-if-user-is-in-scope"></a>Etapa 3: determinar se o usuário está no escopo
Em seguida, o serviço de provisionamento determina se o usuário está no [escopo](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) para provisionamento. O serviço considerará aspectos como se o usuário está atribuído ao aplicativo, se o escopo está definido para sincronização atribuída ou sincronização de todos, e os filtros de escopo definidos em sua configuração de provisionamento.  

**Exibir detalhes**

A seção Exibir detalhes mostra as condições de escopo que foram avaliadas. Você pode ver uma ou mais das seguintes propriedades:
* **Ativo no sistema de origem** indica que o usuário tem a propriedade está ativa definida como true no Azure AD.
* **Atribuído ao aplicativo** indica que o usuário está atribuído ao aplicativo no Azure AD
* **Sincronização de escopo todos** indica que a configuração de escopo permite todos os usuários e grupos no locatário.
* O **usuário tem a função necessária** indica que o usuário tem as funções necessárias para serem provisionadas no aplicativo. 
* Os **filtros de escopo** também serão mostrados se você tiver definido filtros de escopo para seu aplicativo. O filtro será exibido com o seguinte formato-{escopo do filtro de escopo} {atributo de filtro de escopo} {operador de filtro de escopo} {valor de filtro de escopo}. 

**Dicas de solução de problemas**
* Verifique se você definiu uma função de escopo válida. Por exemplo, evite usar o operador ["maior que"](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) com um valor não inteiro. 
* Se o usuário não tiver a função necessária, examine as dicas descritas [aqui](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role). 

### <a name="step-4-match-user-between-source-and-target"></a>Etapa 4: corresponder o usuário entre a origem e o destino
Nesta etapa. o serviço tenta corresponder ao usuário recuperado na etapa de importação com um usuário no sistema de destino. 

**Exibir detalhes**

As páginas Exibir detalhes mostram as propriedades dos usuários que foram correspondidos no sistema de destino. As propriedades que você vê no painel de contexto irão variar da seguinte maneira:
* Se não houver nenhum usuário correspondente no sistema de destino, você não verá nenhuma propriedade.
* Se houver um usuário correspondente no sistema de destino, você verá as propriedades desse usuário correspondente do sistema de destino.
* Se vários usuários forem correspondidos, você verá as propriedades de ambos os usuários correspondentes.
* Se vários atributos correspondentes fizerem parte dos mapeamentos de atributo, cada atributo correspondente será avaliado em sequência e os usuários correspondentes serão mostrados. 

**Detalhes da solução de problemas**
* O serviço de provisionamento não pode corresponder exclusivamente a um usuário na origem com um usuário no destino. Isso pode ser resolvido garantindo que o atributo correspondente seja exclusivo. 
* Verifique se o aplicativo de destino dá suporte à filtragem no atributo definido como o atributo correspondente.  

### <a name="step-5-perform-action"></a>Etapa 5: executar a ação
Por fim, o serviço de provisionamento executa uma ação como criar, atualizar, excluir ou ignorar o usuário. 

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Provisionamento de usuário bem-sucedido.":::

**Exibir detalhes**

A seção Exibir detalhes exibe os atributos que foram modificados no aplicativo de destino. Isso representa a saída final da atividade do serviço de provisionamento e os atributos que foram exportados. Se essa etapa falhar, os atributos exibidos representarão os atributos que o serviço de provisionamento tentou Modificar.  

**Dicas de solução de problemas**
* As falhas de exportação de alterações podem variar muito. Confira a [documentação](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) de logs de provisionamento para falhas comuns.


## <a name="frequently-asked-questions"></a>Perguntas frequentes
**Você precisa desativar o provisionamento para usar o provisionamento sob demanda?.** Para aplicativos que usam um token de portador de vida longa ou nome de usuário e senha para autorização, nenhuma etapa adicional é necessária. Os aplicativos que usam o OAuth para autorização atualmente exigem que o trabalho de provisionamento seja interrompido antes de usar o provisionamento sob demanda. Aplicativos como o G Suite, o box, o local de trabalho por Facebook e a margem de atraso se enquadram nessa categoria. O trabalho está em andamento para permitir a execução de provisionamento sob demanda para todos os aplicativos, sem a necessidade de interromper o provisionamento. 

**Quanto tempo leva o provisionamento sob demanda?** Geralmente leva menos de 30 segundos. 

## <a name="known-limitations"></a>Limitações conhecidas
Atualmente, existem algumas limitações conhecidas. Poste na [voz do usuário](https://aka.ms/appprovisioningfeaturerequest) para que possamos priorizar melhor os aprimoramentos a serem feitos em seguida. Observe que essas limitações são específicas para o recurso de provisionamento sob demanda. para obter informações específicas sobre se um aplicativo dá suporte a grupos de provisionamento, exclusões, etc., consulte o tutorial do aplicativo. 

* Os aplicativos workday, AWS e SuccessFactors não oferecem suporte ao provisionamento sob demanda.
* Não há suporte para o provisionamento de grupos e funções sob demanda.
* O provisionamento sob demanda dá suporte à desabilitação de usuários que foram desatribuídos do aplicativo, mas não dá suporte à desabilitação ou exclusão de usuários que foram desabilitados ou excluídos do Azure Active Directory (esses usuários não aparecerão ao procurar por um usuário).

## <a name="next-steps"></a>Próximas etapas

* [Solucionando problemas de provisionamento](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
