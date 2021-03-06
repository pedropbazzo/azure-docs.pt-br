---
title: Solucionar problemas de conectividade do ponto de extremidade privado do Azure
description: Diretrizes passo a passo para diagnosticar a conectividade de ponto de extremidade particular
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77538527"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Solucionar problemas de conectividade do ponto de extremidade privado do Azure

Este artigo fornece orientações passo a passo para validar e diagnosticar sua configuração de conectividade de ponto de extremidade privado do Azure.

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço de link privado. Essa solução ajuda você a proteger suas cargas de trabalho no Azure fornecendo conectividade privada aos recursos de serviço do Azure de sua rede virtual. Essa solução efetivamente coloca esses serviços em sua rede virtual.

Aqui estão os cenários de conectividade disponíveis com o ponto de extremidade privado:

- Rede virtual da mesma região
- Redes virtuais emparelhadas de região
- Redes virtuais emparelhadas globalmente
- Cliente local sobre VPN ou circuitos do Azure ExpressRoute

## <a name="diagnose-connectivity-problems"></a>Diagnosticar problemas de conectividade 

Examine essas etapas para verificar se todas as configurações usuais estão conforme o esperado para resolver problemas de conectividade com a configuração do ponto de extremidade particular.

1. Examine a configuração do ponto de extremidade privado navegando no recurso.

    a. Vá para o **centro de links privado**.

      ![Centro de links privado](./media/private-endpoint-tsg/private-link-center.png)

    b. No painel esquerdo, selecione **pontos de extremidade privados**.
    
      ![Pontos de extremidade privados](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtre e selecione o ponto de extremidade privado que você deseja diagnosticar.

    d. Examine as informações de rede virtual e DNS.
     - Valide se o estado da conexão foi **aprovado**.
     - Verifique se a VM tem conectividade com a rede virtual que hospeda os pontos de extremidade privados.
     - Verifique se as informações do FQDN (cópia) e o endereço IP privado estão atribuídos.
    
       ![Configuração de rede virtual e DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Use [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para ver se os dados estão fluindo.

    a. No recurso de ponto de extremidade privado, selecione **Monitor**.
     - Selecione **dados** ou **dados de saída**. 
     - Veja se os dados estão fluindo quando você tenta se conectar ao ponto de extremidade privado. Espere um atraso de aproximadamente 10 minutos.
    
       ![Verificar telemetria de ponto de extremidade privado](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Use a **solução de problemas de conexão de VM** do observador de rede do Azure.

    a. Selecione a VM do cliente.

    b. Selecione **solução de problemas de conexão**e, em seguida, selecione a guia **conexões de saída** .
    
      ![Observador de rede-testar conexões de saída](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Selecione **usar observador de rede para rastreamento de conexão detalhado**.
    
      ![Observador de rede-solução de problemas de conexão](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Selecione **testar por FQDN**.
     - Cole o FQDN do recurso de ponto de extremidade privado.
     - Forneça uma porta. Normalmente, use 443 para o armazenamento do Azure ou Azure Cosmos DB e 1336 para SQL.

    e. Selecione **teste**e valide os resultados do teste.
    
      ![Observador de rede – resultados do teste](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. A resolução de DNS dos resultados de teste deve ter o mesmo endereço IP privado atribuído ao ponto de extremidade privado.

    a. Se as configurações de DNS estiverem incorretas, siga estas etapas:
     - Se você usar uma zona privada: 
       - Verifique se a rede virtual VM do cliente está associada à zona privada.
       - Verifique se o registro da zona DNS privada existe. Se ele não existir, crie-o.
     - Se você usar o DNS personalizado:
       - Examine as configurações de DNS personalizadas e valide se a configuração de DNS está correta.
       Para obter diretrizes, consulte [visão geral do ponto de extremidade privado: configuração de DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Se a conectividade estiver falhando devido a NSGs (grupos de segurança de rede) ou a rotas definidas pelo usuário:
     - Examine as regras de saída do NSG e crie as regras de saída apropriadas para permitir o tráfego.
    
       ![Regras de saída do NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Se a conexão tiver resultados validados, o problema de conectividade poderá estar relacionado a outros aspectos, como segredos, tokens e senhas na camada de aplicativo.
   - Nesse caso, examine a configuração do recurso de link privado associado ao ponto de extremidade privado. Para obter mais informações, consulte o [Guia de solução de problemas do link privado do Azure](troubleshoot-private-link-connectivity.md).

1. Entre em contato com a equipe de [suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se seu problema ainda não estiver resolvido e um problema de conectividade ainda existir.

## <a name="next-steps"></a>Próximas etapas

 * [Criar um ponto de extremidade privado na sub-rede atualizada (portal do Azure)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Guia de solução de problemas do link privado do Azure](troubleshoot-private-link-connectivity.md)
