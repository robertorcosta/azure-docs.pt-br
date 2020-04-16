---
title: Diretrizes de limitação do Azure Key Vault
description: A limitação do Azure Key Vault limita o número de chamadas simultâneas para evitar o uso excessivo de recursos.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: f32a988ec0d75ca8d8eca04e69edd7226bf283b4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432080"
---
# <a name="azure-key-vault-throttling-guidance"></a>Diretrizes de limitação do Azure Key Vault

A limitação é o processo que você iniciar para limitar o número de chamadas simultâneas para o serviço do Azure a fim de evitar o uso excessivo de recursos. O AKV (Azure Key Vault) foi projetado para lidar com um grande volume de solicitações. No caso de ocorrer um grande número de solicitações, limitar as solicitações do seu cliente ajuda a manter o desempenho ideal e a confiabilidade do serviço AKV.

Limites de limitação variam de acordo com o cenário. Por exemplo, se você estiver executando um grande volume de gravação, a possibilidade de limitação será maior do que se você estiver apenas executando leituras.

## <a name="how-does-key-vault-handle-its-limits"></a>Como o Key Vault trata os próprios limites?

Os limites de serviço no Key Vault evitam o uso indevido de recursos e garantem a qualidade do serviço para todos os clientes do Key Vault. Quando um limite de serviço é excedido, o Key Vault limita quaisquer solicitações adicionais desse cliente por um período de tempo, retorna o código de status HTTP 429 (muitas solicitações) e a solicitação falha. Solicitações com falha que retornam uma contagem de 429 para os limites do acelerador rastreados pelo Key Vault. 

Key Vault foi originalmente projetado para ser usado para armazenar e recuperar seus segredos no momento da implantação.  O mundo evoluiu, e o Key Vault está sendo usado em tempo de execução para armazenar e recuperar segredos, e muitas vezes aplicativos e serviços querem usar o Key Vault como um banco de dados.  Os limites atuais não suportam altas taxas de throughput.

O Key Vault foi originalmente criado com os limites especificados nos [limites de serviço do Azure Key Vault](service-limits.md).  Para maximizar seu Key Vault através de taxas de put, aqui estão algumas diretrizes/práticas recomendadas recomendadas para maximizar seu throughput:
1. Certifique-se de que você tem o estrangulamento no lugar.  O cliente deve honrar políticas de back-off exponencial para 429 e garantir que você esteja fazendo repetições de acordo com a orientação abaixo.
1. Divida o tráfego do Key Vault entre vários cofres e regiões diferentes.   Use um cofre separado para cada domínio de segurança/disponibilidade.   Se você tem cinco aplicativos, cada um em duas regiões, então recomendamos 10 cofres cada um contendo os segredos exclusivos para aplicativo e região.  Um limite de toda a assinatura para todos os tipos de transações é cinco vezes o limite individual do cofre de chaves. Por exemplo, outras transações HSM por assinatura são limitadas a 5.000 transações em 10 segundos por assinatura. Considere arquivar o segredo dentro do seu serviço ou aplicativo para também reduzir o RPS diretamente para o cofre de chaves e/ou lidar com tráfego baseado em rajadas.  Você também pode dividir seu tráfego entre diferentes regiões para minimizar a latência e usar uma assinatura/cofre diferente.  Não envie mais do que o limite de assinatura para o serviço Key Vault em uma única região do Azure.
1. Cache os segredos que você recupera do Azure Key Vault na memória e reutilizar da memória sempre que possível.  Reli do Azure Key Vault somente quando a cópia armazenada em cache parar de funcionar (por exemplo, porque ela foi girada na fonte). 
1. Key Vault foi projetado para seus próprios segredos de serviços.   Se você estiver armazenando os segredos de seus clientes (especialmente para cenários de armazenamento de chave de alto gerenciamento de taxação), considere colocar as chaves em um banco de dados ou conta de armazenamento com criptografia e armazenar apenas a chave mestra no Azure Key Vault.
1. Criptografar, encerrar e verificar operações de chave pública podem ser realizadas sem acesso ao Key Vault, o que não só reduz o risco de estrangulamento, mas também melhora a confiabilidade (desde que você armaque corretamente o material chave do público).
1. Se você usar o Key Vault para armazenar credenciais para um serviço, verifique se esse serviço suporta a autenticação Azure AD para autenticar diretamente. Isso reduz a carga no Key Vault, melhora a confiabilidade e simplifica seu código, já que o Key Vault agora pode usar o token Azure AD.  Muitos serviços passaram a usar o Azure AD Auth.  Veja a lista atual em [Serviços que suportam identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Considere escalonar sua carga/implantação por um período maior de tempo para permanecer sob os limites de RPS atuais.
1. Se o seu aplicativo compreende vários nós que precisam ler os mesmos segredos, então considere usar um padrão de saída de ventilador, onde uma entidade lê o segredo do Key Vault, e fãs para todos os nós.   Cache os segredos recuperados apenas na memória.
Se você descobrir que o acima ainda não atende às suas necessidades, preencha a tabela abaixo e entre em contato conosco para determinar qual capacidade adicional pode ser adicionada (exemplo colocado abaixo apenas para fins ilustrativos).

| Nome do cofre | Região do Cofre | Tipo de objeto (Segredo, Chave ou Cert) | Operação(s)* | Tipo de Chave | Comprimento ou curva da chave | Chave HSM?| RPS de estado estável necessário | Pico RPS necessário |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Chave | Assinar | EC | P-256 | Não | 200 | 1000 |

\*Para obter uma lista completa de valores possíveis, consulte [as operações do Azure Key Vault](/rest/api/keyvault/key-operations).

Se a capacidade adicional for aprovada, observe o seguinte como resultado do aumento da capacidade:
1. O modelo de consistência dos dados muda. Uma vez que um cofre é permitido listado com capacidade adicional de throughput, a garantia de consistência de dados do serviço Key Vault muda (necessário para atender ao RPS de maior volume, uma vez que o serviço de armazenamento Azure subjacente não pode acompanhar).  Resumindo:
  1. **Sem permitir a listagem**: O serviço Key Vault refletirá os resultados de uma operação de gravação (por exemplo. SecretSet, CreateKey) imediatamente em chamadas subseqüentes (por exemplo. SecretGet, KeySign).
  1. **Com a listagem de pertinência**: O serviço Key Vault refletirá os resultados de uma operação de gravação (por exemplo. SecretSet, CreateKey) dentro de 60 segundos em chamadas subseqüentes (por exemplo. SecretGet, KeySign).
1. O código do cliente deve honrar a política de back-off para 429 tentativas. O código do cliente que chama o serviço Key Vault não deve tentar imediatamente as solicitações do Key Vault quando receber um código de resposta 429.  A orientação de estrangulamento do Azure Key Vault publicada aqui recomenda a aplicação de backoff exponencial ao receber um código de resposta 429 Http.

Se você tiver um caso comercial válido para restrições mais altas, entre em contato conosco.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Como restringir o seu aplicativo em resposta aos limites de serviço

A seguir estão **as práticas recomendadas que** você deve implementar quando seu serviço é estrangulado:
- Reduza o número de operações por solicitação.
- Reduza a frequência de solicitações.
- Evite tentativas imediatas. 
    - Todas as solicitações se acumulam em relação a seus limites de uso.

Quando você implementa o tratamento de erro do aplicativo, use o código de erro HTTP 429 para detectar a necessidade de limitação do lado do cliente. Se a solicitação falha novamente com um código de erro HTTP 429, você ainda está encontrando um limite de serviço do Azure. Continue a usar o método de limitação do lado do cliente recomendado, tentando realizar a solicitação novamente até que ela tenha êxito.

O código que implementa a retirada exponencial é mostrado abaixo. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


O uso deste código em um aplicativo C# do cliente é simples. 

### <a name="recommended-client-side-throttling-method"></a>Método recomendado de limitação do lado do cliente

No código de erro HTTP 429, inicie a limitação do cliente usando uma abordagem de retirada exponencial:

1. Aguarde 1 segundo, tente solicitar novamente
2. Se ainda estiver limitado, aguarde 2 segundos e tente a solicitação novamente
3. Se ainda estiver limitado, aguarde 4 segundos e tente a solicitação novamente
4. Se ainda estiver limitado, aguarde 8 segundos e tente a solicitação novamente
5. Se ainda estiver limitado, aguarde 16 segundos e tente a solicitação novamente

Neste ponto, você não deve estar obtendo códigos de resposta HTTP 429.

## <a name="see-also"></a>Confira também

Para obter uma orientação mais profunda de limitação no Microsoft Cloud, consulte [Padrão de Limitação](https://docs.microsoft.com/azure/architecture/patterns/throttling).

