---
title: Isolamento para VMs no Azure
description: Saiba mais sobre o isolamento de VM funciona no Azure.
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/18/2019
ms.author: sttsinar
ms.reviewer: ayshak
ms.openlocfilehash: 7b1956d704bbdf9475f04efcbdc4b6ee75bb50b2
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996297"
---
# <a name="virtual-machine-isolation-in-azure"></a>Isolamento de máquina virtual no Azure

A Computação do Azure oferece tamanhos de máquina virtual Isolada, para um tipo de hardware específico e dedicada a um único cliente. Os tamanhos isolados residem e operam em uma geração de hardware específica e serão preteridos quando a geração de hardware for desativada.

Os tamanhos de máquinas virtuais isoladas são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de cargas de trabalho de outros clientes por motivos que incluem requisitos de conformidade e regulamentação de atendimento.  Utilizar um tamanho isolado garante que sua máquina virtual será apenas sendo executada na instância de servidor específico. 


Além disso, como as VMs de tamanho isolado são grandes, os clientes podem optar por subdividir os recursos dessas VMs usando o [suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

As ofertas atuais da máquina virtual isolada incluem:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2

> [!NOTE]
> Os tamanhos de VM isolados têm um ciclo de vida limitado por hardware. Consulte abaixo para obter detalhes

## <a name="deprecation-of-isolated-vm-sizes"></a>Substituição de tamanhos de VM isoladas
Como os tamanhos de VM isoladas são os tamanhos de hardware associados, o Azure fornecerá lembretes 12 meses antes da reprovação oficial dos tamanhos.  O Azure também oferecerá um tamanho isolado atualizado em nossa próxima versão de hardware que o cliente poderia considerar ao mover sua carga de trabalho.

| Tamanho | Data de retirada do isolamento | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 15 de maio de 2020 |
| Standard_D15_v2<sup>1</sup>  | 15 de maio de 2020 |

<sup>1</sup>  para obter detalhes sobre o Standard_DS15_v2 e Standard_D15_v2 programa de desativação de isolamento, consulte FAQs


## <a name="faq"></a>Perguntas frequentes
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>P: o tamanho será desativado ou apenas o recurso "isolamento" será?
**R**: se o tamanho da máquina virtual não tiver o subscript "i", somente o recurso "isolamento" será desativado. Se o isolamento não for necessário, não haverá nenhuma ação a ser tomada e a VM continuará funcionando conforme o esperado. Os exemplos incluem Standard_DS15_v2, Standard_D15_v2, Standard_M128ms etc. Se o tamanho da máquina virtual incluir o subscript "i", o tamanho será desativado.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>P: há um tempo de inatividade quando minha VM chega em um hardware não isolado?
**R**: se não houver necessidade de isolamento, nenhuma ação será necessária e não haverá nenhum tempo de inatividade.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>P: há algum Delta de custo para mover para uma máquina virtual não isolada?
**R**: não

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>P: quando os outros tamanhos isolados serão desativados?
**R**: forneceremos lembretes 12 meses antes da reprovação oficial do tamanho isolado.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>P: sou um cliente do Azure Service Fabric contando com as camadas de durabilidade prata ou ouro. Essa alteração me afeta?
**R**: não. As garantias fornecidas pelas camadas de [durabilidade](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) de Service Fabric continuarão a funcionar mesmo após essa alteração. Se você precisar de isolamento de hardware físico por outros motivos, talvez ainda precise executar uma das ações descritas acima. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>P: quais são as etapas para D15_v2 ou desativação de isolamento de DS15_v2? 
**A**: 
 
| Data | Ação |
|---|---| 
| 18 de novembro de 2019 | Disponibilidade de D/DS15i_v2 (PAYG, RI de 1 ano) | 
| 14 de maio de 2020 | Último dia para comprar D/DS15i_v2 RI de 1 ano | 
| 15 de maio de 2020 | Garantia de isolamento D/DS15_v2 removida | 
| 15 de maio de 2021 | Desativar D/DS15i_v2 (todos os clientes exceto quem comprou a RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019)| 
| 17 de novembro de 2022 | Desativar D/DS15i_v2 quando o RIs de 3 anos for concluído (para clientes que compraram uma RI de 3 anos de D/DS15_v2 antes de 18 de novembro de 2019) |

## <a name="next-steps"></a>Próximas etapas

Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais Isoladas usando [o Suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
