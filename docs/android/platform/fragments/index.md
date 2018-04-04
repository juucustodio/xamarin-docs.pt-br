---
title: Fragmentos
description: Android 3.0 apresentou fragmentos, mostrando como dar suporte a projetos mais flexíveis para os vários tamanhos de tela diferentes encontrados em telefones e tablets. Este artigo aborda como usar fragmentos para desenvolver aplicativos xamarin e também como dar suporte a fragmentos em previamente Android dispositivos (API nível 11) 3.0.
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: 08c2edb3acc15518c7d5a69f227fb9ef819887be
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="fragments"></a>Fragmentos

_Android 3.0 apresentou fragmentos, mostrando como dar suporte a projetos mais flexíveis para os vários tamanhos de tela diferentes encontrados em telefones e tablets. Este artigo aborda como usar fragmentos para desenvolver aplicativos xamarin e também como dar suporte a fragmentos em previamente Android dispositivos (API nível 11) 3.0._

## <a name="fragments-overview"></a>Visão geral de fragmentos

Os tamanhos de tela maior encontrados na maioria dos tablets adicionado uma camada extra de complexidade ao desenvolvimento Android — um layout projetado para a tela pequena não necessariamente funciona bem para maior telas e vice-versa. Para reduzir o número de complicações essa introduzida, o Android 3.0 adicionados dois novos recursos, *fragmentos* e *pacotes de suporte*.

Fragmentos podem ser pensados como módulos de interface do usuário. Elas permitem que o desenvolvedor dividir a interface do usuário em partes isolados e reutilizáveis que podem ser executados em atividades separadas. Em tempo de execução, as atividades em si decidirá qual fragmentos para usar.

Pacotes de suporte foram originalmente chamados *compatibilidade bibliotecas* e permitido fragmentos para ser usado em dispositivos que executam versões do Android antes 3.0 Android (API nível 11).

Por exemplo, a imagem a seguir ilustra como um único aplicativo usa os fragmentos em diferentes fatores forma de dispositivo.

[![Diagrama de como os fragmentos são usados em Tablets e telefones](images/00.png)](images/00.png#lightbox)

*Um fragmento de* contém uma lista, enquanto *fragmento B* contém detalhes de um item selecionado na lista. Quando o aplicativo é executado em um tablet, ele pode exibir ambos os fragmentos na mesma atividade. Quando o mesmo aplicativo é executado em um telefone (com o menor tamanho da tela), os fragmentos são hospedados em duas atividades separadas. Fragmento A e B de fragmento são os mesmos em ambos os formatos, mas as atividades que os hospedam são diferentes.

Para ajudar a uma atividade coordenar e gerenciar todos esses fragmentos, o Android introduziu uma nova classe chamada a *FragmentManager*. Cada atividade tem sua própria instância de um `FragmentManager` para adicionar, excluir e Localizando hospedados fragmentos. O diagrama a seguir ilustra o relacionamento entre fragmentos e atividades:

[![Diagrama de ilustrar relações entre fragmentos, Gerenciador de fragmento e atividade](images/01.png)](images/01.png#lightbox)

Em alguns aspectos, fragmentos podem ser pensados como controles compostos ou mini atividades. Eles reunir partes da interface do usuário em módulos reutilizáveis que podem ser usados independentemente por desenvolvedores nas atividades. Um fragmento tem uma hierarquia de exibição — como uma atividade — mas, ao contrário de uma atividade, ele pode ser compartilhado entre telas. Modos de exibição são diferentes de fragmentos de fragmentos têm seu próprios ciclo de vida; modos de exibição, não.

Enquanto a atividade é um host para um ou mais fragmentos, não é diretamente compatível com os fragmentos em si. Da mesma forma, os fragmentos não são diretamente compatível com outros fragmentos na atividade de hospedagem. No entanto, fragmentos e atividades são atento a `FragmentManager` em sua atividade. Usando o `FragmentManager`, é possível que uma atividade ou um fragmento obter uma referência a uma instância específica de um fragmento e, em seguida, chamar métodos nessa instância. Dessa forma, a atividade ou fragmentos podem se comunicar e interagir com outros fragmentos.

Este guia contém uma cobertura abrangente sobre como usar os fragmentos, incluindo:

-   **Criando fragmentos** – como criar um fragmento básico e métodos importantes que devem ser implementados.
-   **Gerenciamento e transações de fragmento** – como manipular fragmentos em tempo de execução.
-   **Pacote de suporte Android** – como fragmentos de usar as bibliotecas que permitem a ser usado em versões anteriores do Android.


## <a name="requirements"></a>Requisitos

Fragmentos estão disponíveis no SDK do Android, começando com o nível de API 11 (3.0 Android), conforme mostrado na seguinte captura de tela:

[![Selecionando o nível de API no Gerenciador de SDK do Android](images/02.png)](images/02.png#lightbox)

Fragmentos estão disponíveis no xamarin 4.0 e superior. Um aplicativo xamarin deve ter como destino pelo menos o nível de API 11 (3.0 Android) ou superior para usar fragmentos. A estrutura de destino pode ser definida no projeto propriedades conforme mostrado abaixo:

[![Definir o nível de API da estrutura de destino nas opções de projeto](images/03-sml.png)](images/03.png#lightbox)

É possível usar os fragmentos em versões mais antigas do Android usando o pacote de suporte Android e xamarin 4.2 ou posterior. Como fazer isso é abordado mais detalhadamente nos documentos desta seção.


## <a name="related-links"></a>Links relacionados

- [Galeria de Honeycomb (exemplo)](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [Fragmentos](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacote de suporte](http://developer.android.com/sdk/compatibility-library.html)
- [Webinar MOTODEV: Apresentando fragmentos](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
