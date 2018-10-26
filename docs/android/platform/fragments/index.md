---
title: Fragmentos
description: Android 3.0 introduziu fragmentos, mostrando como dar suporte a designs mais flexíveis para os tamanhos de tela diferentes muitos encontrados em telefones e tablets. Este artigo abordará como usar fragmentos para desenvolver aplicativos xamarin. Android e também como dar suporte a fragmentos em Android anterior dispositivos (API nível 11) 3.0.
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: bc4441c7ee0c36af990297bad1b0c2f0e77123f3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113281"
---
# <a name="fragments"></a>Fragmentos

_Android 3.0 introduziu fragmentos, mostrando como dar suporte a designs mais flexíveis para os tamanhos de tela diferentes muitos encontrados em telefones e tablets. Este artigo abordará como usar fragmentos para desenvolver aplicativos xamarin. Android e também como dar suporte a fragmentos em Android anterior dispositivos (API nível 11) 3.0._

## <a name="fragments-overview"></a>Visão geral de fragmentos

Os maiores tamanhos de tela encontrados na maioria dos tablets adicionado uma camada extra de complexidade ao desenvolvimento de Android — um layout projetado para a tela pequena não necessariamente funciona bem para maior telas e vice-versa. Para reduzir o número de complicações introduzido por isso, o Android 3.0 adicionou dois novos recursos, *fragmentos* e *pacotes de suporte*.

Fragmentos podem ser pensados como módulos de interface do usuário. Elas permitem que o desenvolvedor dividir a interface do usuário em partes isolados e reutilizáveis que podem ser executados em atividades separadas. Em tempo de execução, as atividades em si serão decidir quais fragmentos para usar.

Pacotes de suporte foram originalmente chamados *bibliotecas de compatibilidade* e permitido fragmentos a ser usado em dispositivos que executam versões do Android anteriores à 3.0 Android (API nível 11).

Por exemplo, a imagem a seguir ilustra como um único aplicativo usa os fragmentos em diferentes fatores forma de dispositivo.

[![Diagrama de como os fragmentos são usados em Tablets e telefones](images/00.png)](images/00.png#lightbox)

*Fragmento de um* contém uma lista, enquanto *fragmento B* contém detalhes de um item selecionado na lista. Quando o aplicativo é executado em um tablet, ele pode exibir ambos os fragmentos na mesma atividade. Quando o mesmo aplicativo é executado em um aparelho (com seu tamanho de tela menor), os fragmentos são hospedados em duas atividades separadas. Fragmento de A e B do fragmento são os mesmos em ambos os fatores de formulário, mas as atividades que os hospedam são diferentes.

Para ajudar a uma atividade coordenar e gerenciar todos esses fragmentos, o Android introduziu uma nova classe chamada a *FragmentManager*. Cada atividade tem sua própria instância de um `FragmentManager` para adicionar, excluir e Localizando hospedadas fragmentos. O diagrama a seguir ilustra o relacionamento entre fragmentos e atividades:

[![Diagrama mostrando as relações entre a atividade, o Gerenciador de fragmento e fragmentos](images/01.png)](images/01.png#lightbox)

Em alguns aspectos, fragmentos podem ser pensados como controles de composição ou mini atividades. Eles agrupam partes da interface do usuário em módulos reutilizáveis que podem ser usados independentemente por desenvolvedores em atividades. Um fragmento tem uma hierarquia de exibição — assim como uma atividade — mas, ao contrário de uma atividade, ele pode ser compartilhado entre telas. Modos de exibição são diferentes de fragmentos de fragmentos têm seu próprio ciclo de vida; modos de exibição, não.

Enquanto a atividade é um host para um ou mais fragmentos, não é diretamente compatível com os fragmentos em si. Da mesma forma, os fragmentos não são diretamente compatível com outros fragmentos na atividade de hospedagem. No entanto, os fragmentos e atividades são atento a `FragmentManager` em suas atividades. Usando o `FragmentManager`, é possível que um fragmento ou uma atividade obter uma referência a uma instância específica de um fragmento e, em seguida, chamar métodos nessa instância. Dessa forma, a atividade ou fragmentos podem se comunicar e interagir com outros fragmentos.

Este guia contém uma cobertura abrangente sobre como usar os fragmentos, incluindo:

-   **Criando fragmentos** – como criar um fragmento básico e os métodos principais que devem ser implementados.
-   **Transações e o gerenciamento de fragmento** – como manipular fragmentos em tempo de execução.
-   **Pacote de suporte ao Android** – como usar as bibliotecas que permitem que os fragmentos a ser usado em versões mais antigas do Android.


## <a name="requirements"></a>Requisitos

Fragmentos estão disponíveis no SDK do Android, começando com o nível de API 11 (Android 3.0), conforme mostrado na seguinte captura de tela:

[![Seleção do nível de API no Gerenciador de SDK do Android](images/02.png)](images/02.png#lightbox)

Fragmentos estão disponíveis no xamarin. Android 4.0 e superior. Um aplicativo xamarin. Android deve ter como destino pelo menos o nível de API 11 (Android 3.0) ou superior para usar os fragmentos. A estrutura de destino pode ser definida no projeto de propriedades conforme mostrado abaixo:

[![Definindo o nível de API da estrutura de destino nas opções do projeto](images/03-sml.png)](images/03.png#lightbox)

É possível usar os fragmentos em versões mais antigas do Android usando o pacote de suporte ao Android e xamarin. Android 4.2 ou posterior. Como fazer isso é abordado mais detalhadamente nos documentos desta seção.


## <a name="related-links"></a>Links relacionados

- [Galeria de Honeycomb (amostra)](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [Fragmentos](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacote de suporte](http://developer.android.com/sdk/compatibility-library.html)
- [MOTODEV Webinar: Introdução ao fragmentos](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
