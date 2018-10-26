---
title: Recursos alternativos
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 0384d96ddc96f8d0b16a42f691305f26ea25881d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108750"
---
# <a name="alternate-resources"></a>Recursos alternativos

Recursos alternativos são aqueles recursos que se destinam a um dispositivo específico ou a configuração de tempo de execução, como o idioma atual, o tamanho de tela específica ou a densidade de pixels. Se o Android pode corresponder a um recurso que é mais específico para um determinado dispositivo ou a configuração do que o recurso padrão, em seguida, esse recurso será usado em vez disso. Se não encontrar um recurso alternativo que corresponde à configuração atual, em seguida, os recursos padrão serão carregados. Como Android decide quais recursos serão usados por um aplicativo serão abordados em mais detalhes abaixo, na seção local do recurso

Recursos alternativos são organizados como um subdiretório dentro da pasta de recursos de acordo com o tipo de recurso, assim como os recursos padrão. O nome do subdiretório recurso alternativo está no formato: _ResourceType_-_qualificador_

*Qualificador* é um nome que identifica uma configuração de dispositivo específico.
Pode haver mais de um qualificador em um nome, cada um deles separados por um traço. Por exemplo, a captura de tela abaixo mostra um projeto simple que tem recursos alternativos para várias configurações, como a localidade, densidade da tela, tamanho da tela e orientação:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

As seguintes regras se aplicam ao adicionar os qualificadores a um tipo de recurso:

1. Pode haver mais de um qualificador, com cada qualificador separado por um traço.

2. Os qualificadores talvez especificados somente uma vez.

3. Qualificadores devem estar na ordem em que eles aparecem na tabela a seguir.

Os qualificadores possíveis estão listados abaixo para referência:

- **MCC e MNC** &ndash; as [código do país móveis](http://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) e, opcionalmente, o [código de rede móvel](http://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). O cartão SIM fornecerá o MCC, enquanto o dispositivo está conectado à rede fornecerá o MNC. Embora seja possível para localidades de destino usando o código do país móveis, a abordagem recomendada é usar o qualificador de idioma especificado abaixo. Por exemplo, para recursos de destino para a Alemanha, o qualificador seria `mcc262`. Para recursos de destino para o T-Mobile nos EUA, o qualificador é `mcc310-mnc026`.
  Para obter uma lista completa de códigos de país móveis e de rede móvel consulte <http://mcc-mnc.com/>.

- **Linguagem** &ndash; de duas letras [código do ISO 639-1 idioma](http://en.wikipedia.org/wiki/ISO_639-1) e, opcionalmente, seguido de duas letras [código de região ISO 3166-alfa 2](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). 
  Se os dois qualificadores são fornecidos, eles são separados por um `-r`. Por exemplo, para as localidades falando em francês de destino e em seguida, o qualificador de `fr` é usado. Para ter como destino de localidades do francês canadense, a `fr-rCA` seria usado. Para obter uma lista completa de códigos de idioma e região, consulte [códigos para a representação de nomes de idiomas](http://www.loc.gov/standards/iso639-2/php/English_list.php) e [nomes de países e elementos de código](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm).

- **Menor largura** &ndash; Especifica a largura da tela menor que o aplicativo deve ser executado em. Abordado em mais detalhes nas [criação de recursos para telas variados](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponível no nível da API 13 (Android 3.2) e versões posteriores. Por exemplo, o qualificador `sw320dp` é usado para dispositivos de destino cuja altura e largura é pelo menos 320dp.

- **Largura disponível** &ndash; a largura mínima da tela no formato l*N*dp, onde *N* é a largura na densidade de pixels independentes.
  Esse valor pode ser alterado conforme o usuário gira o dispositivo. Abordado em mais detalhes nas [criação de recursos para telas variados](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponível no nível da API 13 (Android 3.2) e versões posteriores. Exemplo: o qualificador w720dp é usado para dispositivos que têm uma largura de menos 720dp de destino.

- **Altura disponível** &ndash; a altura mínima da tela em que o formato h*N*dp, onde *N* é a altura no ponto de distribuição. Esse valor pode ser alterado conforme o usuário gira o dispositivo. Abordado em mais detalhes nas [criação de recursos para telas variados](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponível no nível da API 13 (Android 3.2) e versões posteriores. Por exemplo, o qualificador h720dp é usado para direcionar dispositivos que têm uma altura de 720dp mínimo

- **Tamanho da tela** &ndash; esse qualificador é uma generalização do que esses recursos são para o tamanho da tela. Ele é abordado mais detalhadamente [criação de recursos para telas variados](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Os valores possíveis são `small`, `normal`, `large` e `xlarge`. Adicionado no nível da API 9 (Android 2.3.1/Android 2.3/Android 2.3.2)

- **Tela de aspecto** &ndash; baseia-se sobre a taxa de proporção, e não a orientação da tela. Uma tela longa é mais ampla. Adicionado no nível da API 4 (Android 1.6). Os valores possíveis são longos e notlong.

- **Orientação de tela** &ndash; orientação retrato ou paisagem da tela. Isso pode mudar durante o tempo de vida de um aplicativo.
  Os valores possíveis são `port` e `land`.

- **Modo de encaixe** &ndash; para dispositivos de um carro encaixar ou encaixar um suporte técnico. Adicionado no nível da API 8 (Android 2.2. x). Os valores possíveis são `car` e `desk`.

- **Modo noturno** &ndash; ou não o aplicativo é executado durante a noite ou no dia. Isso pode ser alterado durante o tempo de vida de um aplicativo e destina-se para dar aos desenvolvedores uma oportunidade para usar versões mais escuras de uma interface durante a noite. Adicionado no nível da API 8 (Android 2.2. x). Os valores possíveis são `night` e `notnight`.

- **Tela de densidade de pixels (dpi)** &ndash; o número de pixels em uma determinada área na tela física. Geralmente expresso como pontos por polegada (dpi). Os possíveis valores são:

    - `ldpi` &ndash; Telas de densidade baixo.

    - `mdpi` &ndash; Telas de densidade média

    - `hdpi` &ndash; Telas de alta densidade

    - `xhdpi` &ndash; Telas de densidade muito alta

    - `nodpi` &ndash; Recursos que não devem ser dimensionados

    - `tvdpi` &ndash; Introduzido no nível da API 13 (Android 3.2) para telas entre mdpi e hdpi.

- **Tipo de tela sensível ao toque** &ndash; Especifica o tipo de um dispositivo pode ter de tela sensível ao toque. Os valores possíveis são `notouch` (sem tela sensível ao toque), `stylus` (um resistente a tela sensível ao toque adequado para uma caneta), e `finger` (uma tela sensível ao toque).

- **Disponibilidade de teclado** &ndash; Especifica que tipo de teclado está disponível. Isso pode ser alterado durante o tempo de vida de um aplicativo &ndash; por exemplo quando um usuário abre um teclado de hardware. Os possíveis valores são:

    - `keysexposed` &ndash; O dispositivo tem um teclado disponível. Se não houver nenhum teclado software habilitado, em seguida, isso é usado apenas quando o teclado de hardware é aberto.

    - `keyshidden` &ndash; O dispositivo tiver um teclado de hardware, mas ele está oculto e sem teclado de software está habilitado.

    - `keyssoft` &ndash; o dispositivo tem um teclado de software habilitado.

- **Método de entrada de texto primário** &ndash; Use para especificar quais tipos de chaves de hardware estão disponíveis para a entrada. Os possíveis valores são:

    - `nokeys` &ndash; Não há nenhuma chave de hardware para a entrada.

    - `qwerty` &ndash; Há um teclado qwerty disponível.

    - `12key` &ndash; Há um teclado de hardware de 12 teclas


- **Disponibilidade de tecla de navegação** &ndash; para quando a navegação (teclado direcional) de forma 5 ou direcional está disponível. Isso pode mudar durante o tempo de vida do seu aplicativo. Os possíveis valores são:

    - `navexposed` &ndash; as teclas de navegação estão disponíveis para o usuário

    - `navhidden` &ndash; as teclas de navegação não estão disponíveis.

-  **Método de navegação principal não-toque** &ndash; o tipo de navegação disponível no dispositivo. Os possíveis valores são:

    - `nonav` &ndash; o recurso de navegação apenas disponível é a tela sensível ao toque

    - `dpad` &ndash; um painel de d (teclado direcional) está disponível para navegação

    - `trackball` &ndash; o dispositivo tem um trackball para navegação

    - `wheel` &ndash; o cenário incomuns em que há um ou mais direções roda disponíveis

-  **Versão da plataforma (nível da API)** &ndash; nível a API com suporte pelo dispositivo no formato v*N*, onde *N* é o nível de API que está sendo direcionado. Por exemplo, v11 será direcionada a um nível de API 11 (Android 3.0) dispositivo.


Para obter informações mais completas sobre o recurso Consulte qualificadores [fornecendo recursos](http://developer.android.com/guide/topics/resources/providing-resources.html) no site de desenvolvedores do Android.


## <a name="how-android-determines-what-resources-to-use"></a>Como o Android determina quais recursos para uso

É muito possível e é provável que um aplicativo do Android contém muitos recursos. É importante entender como o Android selecionará os recursos para um aplicativo quando ele é executado em um dispositivo.

Android determina os recursos base iterando sobre o teste das regras a seguir:

- **Eliminar qualificadores contraditórios** &ndash; por exemplo, se a orientação do dispositivo é retrato, em seguida, todos os diretórios de recursos de paisagem serão rejeitados.

- **Ignorar qualificadores nepodporuje** &ndash; nem todos os qualificadores estão disponíveis para todos os níveis de API. Se um diretório de recurso contém um qualificador que não é compatível com o dispositivo, em seguida, esse diretório de recursos será ignorado.

- **Identificar o qualificador de prioridade mais alto próxima** &ndash; referindo-se a tabela acima a seleção do qualificador de prioridade mais alto próximo (de cima para baixo).

- **Manter quaisquer diretórios de recursos para o qualificador** &ndash; se houver quaisquer diretórios de recursos que correspondem o qualificador na tabela acima, selecione o próximo qualificador de prioridade mais alto (de cima para baixo).

Essas regras também são ilustradas no fluxograma a seguir:

[![Fluxograma de recursos](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Quando o sistema está procurando por recursos específicos de densidade e não é possível encontrá-los, ele tentará localizar outros recursos específicos de densidade e dimensioná-los. Android necessariamente não poderá usar os recursos padrão.
Por exemplo, quando procurando um recurso de baixa densidade e ele não estiver disponível, o Android pode selecionar versão de alta densidade do recurso sobre os recursos padrão ou a densidade média. Isso ocorre porque o recurso de alta densidade pode ser reduzido verticalmente por um fator de 0,5, o que resultará em menos problemas de visibilidade de dimensionamento de um recurso de densidade média, o que exigiria um fator de 0,75.

Por exemplo, considere um aplicativo que tem os diretórios desenháveis recursos a seguir:

    drawable
    drawable-en
    drawable-fr-rCA
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

E agora o aplicativo é executado em um dispositivo com a seguinte configuração:

- **Localidade** &ndash; en-GB
- **Orientação** &ndash; porta
- **Densidade de tela** &ndash; hdpi
- **Tipo de tela sensível ao toque** &ndash; notouch
- **Método de entrada principal** &ndash; 12key

Para começar, os recursos em francês são eliminados conforme eles entram em conflito com a localidade de `en-GB`, deixando-nos com:

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

Em seguida, o qualificador a primeira é selecionado na tabela qualificadores acima: MCC e MNC. Não há nenhum diretório de recursos que contêm esse qualificador para que o código do MCC/MNC será ignorado.

O qualificador de Avançar é selecionado, o que é o idioma. Há recursos que correspondem ao código de idioma. Todos os diretórios de recursos que não correspondem a código do idioma `en` são rejeitadas, para que a lista de recursos agora é:

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

O qualificador de Avançar que está presente é para a orientação da tela, portanto, todos os diretórios de recursos que não correspondem a orientação da tela `port` são eliminadas:

    drawable-en-port
    drawable-en-port-ldpi

O qualificador para a densidade da tela, em seguida, vem `ldpi`, que resulta na exclusão de um diretório de recursos mais:

    drawable-en-port-ldpi

Como resultado desse processo, o Android usará os recursos desenháveis no diretório do recurso `drawable-en-port-ldpi` para o dispositivo.

> [!NOTE]
> Os qualificadores de tamanho de tela fornecem uma exceção a esse processo de seleção. É possível para o Android selecionar os recursos que são criados para uma tela menor do que o dispositivo atual fornece. Por exemplo, um dispositivo de tela grande pode usar os recursos fornecem para uma tela de tamanho normal. No entanto o inverso do que isso não é verdadeiro: o mesmo dispositivo de tela grande não usará os recursos fornecidos para uma tela de xlarge. Se o Android não é possível localizar um conjunto de recursos que corresponde a um tamanho de tela, o aplicativo falhará.
