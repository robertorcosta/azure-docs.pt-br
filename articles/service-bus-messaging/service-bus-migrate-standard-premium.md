---
title: Migrar espaços de nome do Ônibus de Serviço Do Azure - padrão para premium
description: Guia para permitir a migração de espaços de nomes padrão do Azure Service Bus existentes para premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385020"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrar os namespaces padrão do Azure Service Bus existentes para o nível premium

Anteriormente, o Azure Service Bus oferecia espaços de nome apenas no nível padrão. Namespaces são configurações de vários locatários que são otimizadas para ambientes de baixa throughput e desenvolvedor. O nível premium oferece recursos dedicados por namespace para latência previsível e aumento da oferta de throughput a um preço fixo. O nível premium é otimizado para ambientes de alta produção e de produção que requerem recursos corporativos adicionais.

Este artigo descreve como migrar os namespaces de nível padrão existentes para o nível premium.  

>[!WARNING]
> A migração destina-se a que os namespaces padrão do Service Bus sejam atualizados para o nível premium. A ferramenta de migração não suporta rebaixamento.

Alguns dos pontos a serem observados:

- Essa migração deve acontecer no local, o que significa que os aplicativos remetente e receptor existentes **não requerem alterações no código ou configuração**. A seqüência de conexões existente apontará automaticamente para o novo namespace premium.
- O **namespace premium** não deve ter **entidades** nele para que a migração tenha sucesso.
- Todas as **entidades** no namespace padrão são **copiadas** para o namespace premium durante o processo de migração.
- A migração suporta **1.000 entidades por unidade de mensagens** no nível premium. Para identificar quantas unidades de mensagens você precisa, comece com o número de entidades que você tem no seu namespace padrão atual.
- Você não pode migrar diretamente do **nível básico** para o **nível premium,** mas você pode fazê-lo indiretamente migrando do básico para o padrão primeiro e, em seguida, do padrão para o premium na próxima etapa.

## <a name="migration-steps"></a>Etapas da migração

Algumas condições estão associadas ao processo de migração. Familiarize-se com os seguintes passos para reduzir a possibilidade de erros. Essas etapas descrevem o processo de migração e os detalhes passo a passo são listados nas seções a seguir.

1. Crie um novo namespace premium.
1. Emparelhe os namespaces padrão e premium entre si.
1. Sincronizar (copiar) entidades do padrão para o espaço de nome premium.
1. Cometa a migração.
1. Drenar entidades no namespace padrão usando o nome pós-migração do namespace.
1. Exclua o namespace padrão.

>[!IMPORTANT]
> Depois que a migração tiver sido comprometida, acesse o antigo namespace padrão e drene as filas e assinaturas. Depois que as mensagens forem drenadas, elas podem ser enviadas para o novo namespace premium a ser processado pelos aplicativos receptores. Depois que as filas e assinaturas forem drenadas, recomendamos que você exclua o antigo namespace padrão.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migre usando o Azure CLI ou PowerShell

Para migrar o namespace padrão do Service Bus para o premium usando a ferramenta Azure CLI ou PowerShell, siga estas etapas.

1. Crie um novo espaço de nome premium do Service Bus. Você pode referenciar os modelos do [Azure Resource Manager](service-bus-resource-manager-namespace.md) ou usar [o portal Azure](service-bus-create-namespace-portal.md). Certifique-se de selecionar **o prêmio** para o parâmetro **serviceBusSku.**

1. Defina as seguintes variáveis de ambiente para simplificar os comandos de migração.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > O alias/nome de pós-migração (post_migration_dns_name) será usado para acessar a migração de post de namespace padrão antigo. Use isso para drenar as filas e as assinaturas e, em seguida, exclua o namespace.

1. Emparelhe os namespaces padrão e premium e inicie a sincronização usando o seguinte comando:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Verifique o status da migração usando o seguinte comando:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    A migração é considerada completa quando você vê os seguintes valores:

    * Estados migratórios = "Ativo"
    * pendenteReplicationsOperationsCount = 0
    * provisionamentoEstado = "Bem sucedido"

    Este comando também exibe a configuração de migração. Verifique se os valores estão definidos corretamente. Verifique também o namespace premium no portal para garantir que todas as filas e tópicos foram criados, e que eles correspondem ao que existia no namespace padrão.

1. Comprometa a migração executando o seguinte comando completo:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migre usando o portal Azure

A migração usando o portal Azure tem o mesmo fluxo lógico que migrar usando os comandos. Siga estes passos para migrar usando o portal Azure.

1. No menu **Navegação** no painel esquerdo, selecione **Migrar para premium**. Clique no botão **'Iniciar',** para continuar até a próxima página.
    ![Página de aterrissagem da migração][]

1. **Configuração completa**.
   ![Espaço de nome de configuração][]
   1. Crie e atribua o namespace premium para migrar o namespace padrão existente para.
        ![Espaço de nome de configuração - criar namespace premium][]
   1. Escolha um **nome de migração de postagem**. Você usará este nome para acessar o namespace padrão depois que a migração estiver concluída.
        ![Espaço de nome de configuração - escolher nome de migração de postagem][]
   1. Selecione **'Próximo'** para continuar.
1. Sincronize as entidades entre os namespaces padrão e premium.
    ![Espaço de nome de configuração - entidades de sincronização - iniciar][]

   1. Selecione **Iniciar sincronizar** para começar a sincronizar as entidades.
   1. Selecione **Sim** na caixa de diálogo para confirmar e iniciar a sincronização.
   1. Aguarde até que a sincronização esteja completa. O status está disponível na barra de status.
        ![Espaço de nome de configuração - entidades de sincronização - progresso][]
        >[!IMPORTANT]
        > Se você precisar abortar a migração por qualquer motivo, por favor, revise o fluxo de abortagem na seção FAQ deste documento.
   1. Depois que a sincronização estiver concluída, selecione **Next** na parte inferior da página.

1. Revisar alterações na página de resumo. Selecione **Migração completa** para alternar namespaces e concluir a migração.
    ![Mudar namespace - menu de switch][]  
    A página de confirmação aparece quando a migração estiver concluída.
    ![Trocar namespace - sucesso][]

## <a name="caveats"></a>Advertências

Alguns dos recursos fornecidos pelo nível Azure Service Bus Standard não são suportados pelo nível Azure Service Bus Premium. Estes são por design, uma vez que o nível premium oferece recursos dedicados para throughput e latência previsíveis.

Aqui está uma lista de recursos não suportados pelo Premium e sua mitigação -

### <a name="express-entities"></a>Entidades expressas

   Entidades expressas que não comprometem nenhum dado de mensagem ao armazenamento não são suportadas no Premium. Os recursos dedicados proporcionaram uma melhoria significativa do throughput, garantindo que os dados sejam persistidos, como é esperado de qualquer sistema de mensagens corporativas.

   Durante a migração, qualquer uma de suas entidades expressas em seu namespace Padrão será criada no namespace Premium como uma entidade não expressa.

   Se você utilizar os modelos ARM (Azure Resource Manager, gerenciador de recursos) do Azure, certifique-se de remover o sinalizador 'enableExpress' da configuração de implantação para que seus fluxos de trabalho automatizados funcionem sem erros.

### <a name="partitioned-entities"></a>Entidades particionadas

   As entidades particionadas foram suportadas no nível Padrão para fornecer melhor disponibilidade em uma configuração de vários inquilinos. Com o fornecimento de recursos dedicados disponíveis por namespace no nível Premium, isso não é mais necessário.

   Durante a migração, qualquer entidade particionada no namespace Padrão é criada no namespace Premium como uma entidade não particionada.

   Se o modelo ARM definir 'habilitar partição' para 'verdadeiro' para uma fila ou tópico específico, ele será ignorado pelo corretor.

## <a name="faqs"></a>Perguntas frequentes

### <a name="what-happens-when-the-migration-is-committed"></a>O que acontece quando a migração é comprometida?

Depois que a migração for comprometida, a seqüência de conexões que apontou para o namespace padrão apontará para o namespace premium.

Os aplicativos de remetente e receptor se desconectarão do Namespace padrão e se reconectarão automaticamente ao namespace premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>O que faço depois que o padrão de migração premium estiver completo?

O padrão de migração premium garante que os metadados da entidade, como tópicos, assinaturas e filtros, sejam copiados do namespace padrão para o namespace premium. Os dados de mensagem que foram comprometidos com o namespace padrão não são copiados do namespace padrão para o namespace premium.

O namespace padrão pode ter algumas mensagens que foram enviadas e comprometidas enquanto a migração estava em andamento. Drene manualmente essas mensagens do Namespace padrão e envie-as manualmente para o Namespace premium. Para drenar manualmente as mensagens, use um aplicativo de console ou um script que drene as entidades de namespace padrão usando o nome DNS de migração de postagem que você especificou nos comandos de migração. Envie essas mensagens para o namespace premium para que possam ser processadas pelos receptores.

Depois que as mensagens forem drenadas, exclua o namespace padrão.

>[!IMPORTANT]
> Depois que as mensagens do namespace padrão forem drenadas, exclua o namespace padrão. Isso é importante porque a seqüência de conexões que inicialmente se referia ao namespace padrão agora se refere ao namespace premium. Você não precisará mais do namespace padrão. A exclusão do namespace padrão que você migrou ajuda a reduzir a confusão posterior.

### <a name="how-much-downtime-do-i-expect"></a>Quanto tempo de inatividade eu esperava?

O processo de migração visa reduzir o tempo de inatividade esperado para os aplicativos. O tempo de inatividade é reduzido usando a seqüência de conexões que os aplicativos remetente e receptor usam para apontar para o novo namespace premium.

O tempo de inatividade experimentado pelo aplicativo é limitado ao tempo necessário para atualizar a entrada do DNS para apontar para o namespace premium. O tempo de inatividade é de aproximadamente 5 minutos.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Tenho que fazer alguma alteração de configuração durante a migração?

Não, não há mudanças de código ou configuração necessárias para fazer a migração. A seqüência de conexões que os aplicativos de remetente e receptor usam para acessar o Namespace padrão é automaticamente mapeada para funcionar como um alias para o namespace premium.

### <a name="what-happens-when-i-abort-the-migration"></a>O que acontece quando eu abortar a migração?

A migração pode ser abortada usando o `Abort` comando ou usando o portal Azure.

#### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portal do Azure

![Fluxo abortar][]
![- abortar fluxo abortabort - abortar completo][]

Quando o processo de migração é abortado, ele aborta o processo de copiar as entidades (tópicos, assinaturas e filtros) do padrão para o namespace premium e quebra o emparelhamento.

A seqüência de conexões não é atualizada para apontar para o namespace premium. Seus aplicativos existentes continuam a funcionar como antes de você iniciar a migração.

No entanto, ele não exclui as entidades no namespace premium ou exclui o namespace premium. Exclua as entidades manualmente se você decidir não avançar com a migração.

>[!IMPORTANT]
> Se você decidir abortar a migração, exclua o Namespace premium que você provisionou para a migração para que você não seja cobrado pelos recursos.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Não quero ter que drenar as mensagens. O que devo fazer?

Pode haver mensagens que são enviadas pelos aplicativos de remetente e comprometidas com o armazenamento no Namespace padrão enquanto a migração está ocorrendo e pouco antes da migração ser comprometida.

Durante a migração, os dados/carga de mensagens reais não são copiados do padrão para o namespace premium. As mensagens devem ser drenadas manualmente e enviadas para o espaço de nome premium.

No entanto, se você pode migrar durante uma janela planejada de manutenção/limpeza, e não quiser drenar manualmente e enviar as mensagens, siga estas etapas:

1. Pare as aplicações do remetente. Os aplicativos receptores processarão as mensagens que estão atualmente no namespace padrão e drenarão a fila.
1. Depois que as filas e assinaturas no Namespace padrão estiverem vazias, siga o procedimento descrito anteriormente para executar a migração do padrão para o namespace premium.
1. Depois que a migração estiver concluída, você pode reiniciar os aplicativos de remetente.
1. Os remetentes e receptores agora se conectarão automaticamente com o espaço de nome premium.

    >[!NOTE]
    > Você não precisa parar os aplicativos receptores para a migração.
    >
    > Depois que a migração for concluída, os aplicativos do receptor se desconectarão do namespace padrão e se conectarão automaticamente ao namespace premium.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [diferenças entre o padrão e o premium de Mensagens](./service-bus-premium-messaging.md).
* Conheça os [aspectos de recuperação de alta disponibilidade e geo-desastre para o prêmio service bus](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Página de aterrissagem da migração]: ./media/service-bus-standard-premium-migration/1.png
[Espaço de nome de configuração]: ./media/service-bus-standard-premium-migration/2.png
[Espaço de nome de configuração - criar namespace premium]: ./media/service-bus-standard-premium-migration/3.png
[Espaço de nome de configuração - escolher nome de migração de postagem]: ./media/service-bus-standard-premium-migration/4.png
[Espaço de nome de configuração - entidades de sincronização - iniciar]: ./media/service-bus-standard-premium-migration/5.png
[Espaço de nome de configuração - entidades de sincronização - progresso]: ./media/service-bus-standard-premium-migration/8.png
[Mudar namespace - menu de switch]: ./media/service-bus-standard-premium-migration/9.png
[Trocar namespace - sucesso]: ./media/service-bus-standard-premium-migration/12.png

[Fluxo de abortar - abortar sincronização]: ./media/service-bus-standard-premium-migration/abort1.png
[Fluxo de abortar - abortar completo]: ./media/service-bus-standard-premium-migration/abort3.png
