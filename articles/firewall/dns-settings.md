---
title: Configurações de DNS do firewall do Azure (versão prévia)
description: Você pode configurar o Firewall do Azure com o servidor DNS e as configurações de proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 09ffac4f19d50d9a386110e1b89f8f147652a2cd
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131999"
---
# <a name="azure-firewall-dns-settings-preview"></a>Configurações de DNS do firewall do Azure (versão prévia)

> [!IMPORTANT]
> Atualmente, as configurações de DNS do firewall do Azure estão em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode configurar um servidor DNS personalizado e habilitar o proxy DNS para o Firewall do Azure. Você pode definir essas configurações ao implantar o firewall ou posterior na página de **configurações de DNS** .

## <a name="dns-servers"></a>Servidores DNS

Um servidor DNS mantém e resolve nomes de domínio para endereços IP. Por padrão, o Firewall do Azure usa o DNS do Azure para resolução de nomes. A configuração do **servidor DNS** permite que você configure seus próprios servidores DNS para a resolução de nomes do firewall do Azure. Você pode configurar um único ou vários servidores.

### <a name="configure-custom-dns-servers-preview"></a>Configurar servidores DNS personalizados (visualização)

1. Em **configurações**do firewall do Azure, selecione **configurações de DNS**.
2. Em **servidores DNS**, você pode digitar ou adicionar servidores DNS existentes que foram especificados anteriormente em sua rede virtual.
3. Clique em **Salvar**.
4. O firewall agora direciona o tráfego DNS para os servidores DNS especificados para a resolução de nomes.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Servidores DNS":::

## <a name="dns-proxy-preview"></a>Proxy DNS (visualização)

Você pode configurar o Firewall do Azure para atuar como um proxy DNS. Um proxy DNS atua como um intermediário para solicitações de DNS de máquinas virtuais de cliente para um servidor DNS. Se você configurar um servidor DNS personalizado, deverá habilitar o proxy DNS para evitar a incompatibilidade de resolução de DNS e habilitar a filtragem de FQDN em regras de rede.

Se você não habilitar o proxy DNS, as solicitações de DNS do cliente poderão viajar para um servidor DNS em um momento diferente ou retornar uma resposta diferente em comparação com a do firewall. O proxy DNS coloca o Firewall do Azure no caminho das solicitações do cliente para evitar inconsistências.

A configuração de proxy DNS requer três etapas:
1. Habilite o proxy DNS nas configurações de DNS do firewall do Azure.
2. Opcionalmente, configure seu servidor DNS personalizado ou use o padrão fornecido.
3. Por fim, você deve configurar o endereço IP privado do firewall do Azure como um endereço DNS personalizado em suas configurações de servidor DNS de rede virtual. Isso garante que o tráfego DNS seja direcionado para o Firewall do Azure.

### <a name="configure-dns-proxy-preview"></a>Configurar o proxy DNS (visualização)

Para configurar o proxy DNS, você deve definir a configuração de servidores DNS da rede virtual para usar o endereço IP privado do firewall. Em seguida, habilite o proxy DNS nas **configurações de DNS**da política de firewall do Azure.

#### <a name="configure-virtual-network-dns-servers"></a>Configurar servidores DNS da rede virtual

1. Selecione a rede virtual na qual o tráfego DNS será roteado por meio do firewall do Azure.
2. Em **Configurações**, selecione **Servidores DNS**.
3. Selecione **personalizado** em **servidores DNS**.
4. Insira o endereço IP privado do firewall.
5. Clique em **Salvar**.
6. Reinicie as VMs que estão conectadas à rede virtual, para que as novas configurações do servidor DNS sejam atribuídas a elas. As VMs continuam a usar suas configurações de DNS atuais até que elas sejam reiniciadas.

#### <a name="enable-dns-proxy-preview"></a>Habilitar proxy DNS (visualização)

1. Selecione o Firewall do Azure.
2. Em **configurações**, selecione **configurações de DNS**.
3. Por padrão, o **proxy DNS** está desabilitado. Quando habilitado, o firewall escuta na porta 53 e encaminha as solicitações de DNS para os servidores DNS configurados.
4. Examine a configuração dos **servidores DNS** para certificar-se de que as configurações são apropriadas para o seu ambiente.
5. Clique em **Salvar**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Servidores DNS":::

## <a name="next-steps"></a>Próximas etapas

[Filtragem de FQDN em regras de rede](fqdn-filtering-network-rules.md)
