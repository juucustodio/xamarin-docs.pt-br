---
title: Recursos alternativos
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: f7833e238afe41d4a5ac7b8dde4c168f298752fc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="alternate-resources"></a>Recursos alternativos

Recursos alternativos são aqueles recursos que se destinam a um dispositivo específico ou a configuração de tempo de execução, como o idioma atual, tamanho da tela específica ou a densidade de pixels. Se Android pode corresponder a um recurso que é mais específico para um determinado dispositivo ou configuração que o recurso de padrão, em seguida, esse recurso será usado. Se não encontrar um recurso alternativo que corresponde à configuração atual, os recursos padrão serão carregados. Como o Android decide quais recursos serão usados por um aplicativo serão abordados com mais detalhes abaixo, na seção de local do recurso

Recursos alternativos são organizados como um subdiretório dentro da pasta de recursos de acordo com o tipo de recurso, como recursos padrão. O nome do subdiretório recurso alternativo está no formato: _ResourceType_-_qualificador_

*Qualificador* é um nome que identifica uma configuração de dispositivo específico.
Pode haver mais de um qualificador, um nome de cada um deles separado por um traço. Por exemplo, a captura de tela abaixo mostra um projeto simple que tem recursos alternativos para várias configurações, como a localidade, densidade da tela, tamanho da tela e orientação:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Recursos alternativos](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Recursos alternativos](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

As seguintes regras se aplicam ao adicionar qualificadores para um tipo de recurso:

1. Pode haver mais de um qualificador, com cada qualificador separado por um traço.

2. Os qualificadores que pode ser especificados somente uma vez.

3. Qualificadores devem estar na ordem em que aparecem na tabela a seguir.

Os qualificadores possíveis estão listados abaixo para referência:

- **MCC e MNC** &ndash; o [código de país móveis](http://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) e, opcionalmente, o [código de rede móvel](http://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). O cartão SIM fornecerá MCC, enquanto o dispositivo está conectado à rede fornecerá o MNC. Embora seja possível localidades de destino usando o código do país móveis, a abordagem recomendada é usar o qualificador de idioma especificado abaixo. Por exemplo, para recursos de destino para Alemanha, o qualificador seria `mcc262`. Recursos de destino para o T-Mobile nos EUA, o qualificador é `mcc310-mnc026`.
  Para obter uma lista completa de códigos de país móveis e de rede móvel consulte <http://mcc-mnc.com/>.

- **Idioma** &ndash; de duas letras [código do ISO 639-1 idioma](http://en.wikipedia.org/wiki/ISO_639-1) e opcionalmente seguido de duas letras [código de região ISO 3166-alpha 2](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). 
  Se ambos os qualificadores são fornecidos, eles serão separados por um `-r`. Por exemplo, para localidades falando em francês do destino, em seguida, o qualificador de `fr` é usado. Para direcionar o francês canadense localidades, a `fr-rCA` será usado. Para obter uma lista completa de códigos de idioma e região, consulte [códigos para a representação de nomes de idiomas](http://www.loc.gov/standards/iso639-2/php/English_list.php) e [nomes de país e elementos de código](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm).

- **Menor largura** &ndash; Especifica a largura da tela menor que o aplicativo deve ser executada no. Abordado mais detalhadamente na [criando recursos para telas sensíveis ao Varying](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponível no nível de API 13 (Android 3.2) e acima. Por exemplo, o qualificador `sw320dp` é usado para dispositivos de destino cujo altura e largura é pelo menos 320dp.

- **Largura disponível** &ndash; a largura mínima da tela no formato l*N*dp, onde *N* é a largura em densidade de pixels independentes.
  Esse valor pode alterar como o usuário gira o dispositivo. Abordado mais detalhadamente na [criando recursos para telas sensíveis ao Varying](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponível no nível de API 13 (Android 3.2) e acima. Exemplo: w720dp o qualificador é usado para dispositivos que têm uma largura de menos 720dp de destino.

- **Altura disponível** &ndash; a altura mínima da tela em que o formato h*N*dp, onde *N* é a altura no ponto de distribuição. Esse valor pode alterar como o usuário gira o dispositivo. Abordado mais detalhadamente na [criando recursos para telas sensíveis ao Varying](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponível no nível de API 13 (Android 3.2) e acima. Por exemplo, o qualificador h720dp é usado para dispositivos que têm uma altura de 720dp menos de destino

- **Tamanho da tela** &ndash; esse qualificador é uma generalização do que esses recursos são para o tamanho da tela. Ele é abordado em mais detalhes em [criando recursos para telas sensíveis ao Varying](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Os valores possíveis são `small`, `normal`, `large` e `xlarge`. Adicionado no nível de API 9 (Android 2.3.1/Android 2.3/Android 2.3.2)

- **Tela de aspecto** &ndash; tem como base a taxa de proporção, não da orientação da tela. Uma tela longa é mais ampla. Adicionado no nível de API 4 (Android 1.6). Os valores possíveis são longos e notlong.

- **Orientação de tela** &ndash; retrato ou paisagem orientação da tela. Isso pode mudar durante o tempo de vida de um aplicativo.
  Os valores possíveis são `port` e `land`.

- **Encaixar modo** &ndash; para dispositivos de um carro encaixar ou encaixar um suporte técnico. Adicionado no nível de API 8 (Android 2.2). Os valores possíveis são `car` e `desk`.

- **Modo de noite** &ndash; ou não o aplicativo é executado durante a noite ou no dia. Isso pode ser alterado durante o tempo de vida de um aplicativo e destina-se para dar aos desenvolvedores a oportunidade de usar versões mais escuras de uma interface à noite. Adicionado no nível de API 8 (Android 2.2). Os valores possíveis são `night` e `notnight`.

- **Tela de densidade de pixels (dpi)** &ndash; o número de pixels em uma determinada área na tela física. Geralmente expresso como pontos por polegada (dpi). Os possíveis valores são:

    - `ldpi` &ndash; Telas de baixa densidade.

    - `mdpi` &ndash; Telas de densidade média

    - `hdpi` &ndash; Telas de alta densidade

    - `xhdpi` &ndash; Telas de mais alta densidade

    - `nodpi` &ndash; Recursos que não devem ser expandida

    - `tvdpi` &ndash; Introduzido no nível de API 13 (Android 3.2) para telas entre mdpi e hdpi.

- **Tipo de tela sensível ao toque** &ndash; Especifica o tipo de um dispositivo pode ter tela sensível ao toque. Os valores possíveis são `notouch` (nenhuma tela sensível ao toque), `stylus` (um resistente a tela sensível ao toque adequado para uma caneta), e `finger` (uma tela sensível ao toque).

- **Disponibilidade de teclado** &ndash; Especifica que tipo de teclado está disponível. Isso pode ser alterado durante o tempo de vida de um aplicativo &ndash; por exemplo quando um usuário abre um teclado de hardware. Os possíveis valores são:

    - `keysexposed` &ndash; O dispositivo tem um teclado disponível. Se não houver nenhum teclado software habilitado, então isso é usado apenas quando o teclado de hardware é aberto.

    - `keyshidden` &ndash; O dispositivo tem um teclado de hardware, mas está oculta e sem teclado de software está habilitado.

    - `keyssoft` &ndash; o dispositivo tem um teclado de software habilitado.

- **Método de entrada de texto principal** &ndash; Use para especificar quais tipos de chaves de hardware estão disponíveis para entrada. Os possíveis valores são:

    - `nokeys` &ndash; Não há nenhuma chave de hardware para a entrada.

    - `qwerty` &ndash; Há um teclado qwerty disponível.

    - `12key` &ndash; Há um teclado de hardware de 12-chave


- **Disponibilidade de chave de navegação** &ndash; para quando a forma de 5 ou direcional navegação de (teclado direcional) está disponível. Isso pode mudar durante o tempo de vida do aplicativo. Os possíveis valores são:

    - `navexposed` &ndash; as teclas de navegação estão disponíveis para o usuário

    - `navhidden` &ndash; as teclas de navegação não estão disponíveis.

-  **Método principal de navegação não toque** &ndash; o tipo de navegação disponível no dispositivo. Os possíveis valores são:

    - `nonav` &ndash; o recurso de navegação apenas disponível é a tela sensível ao toque

    - `dpad` &ndash; um teclado de d (teclado direcional) está disponível para navegação

    - `trackball` &ndash; o dispositivo tem um trackball para navegação

    - `wheel` &ndash; o cenário comum em que há um ou mais direcional roda disponíveis

-  **Versão da plataforma (API nível)** &ndash; nível de API o suporte do dispositivo no formato v*N*, onde *N* é o nível de API que está sendo direcionada. Por exemplo, v11 será direcionada a um nível de API 11 (3.0 Android) dispositivo.


Para obter mais informações sobre recursos qualificadores consulte [fornecendo recursos](http://developer.android.com/guide/topics/resources/providing-resources.html) no site de desenvolvedores do Android.


## <a name="how-android-determines-what-resources-to-use"></a>Como o Android determina quais recursos em uso

É provável que um aplicativo do Android contém muitos recursos e bastante possíveis. É importante entender como o Android irá selecionar os recursos de um aplicativo quando ele é executado em um dispositivo.

Android determina os recursos de base por iteração sobre o teste de regras a seguir:

- **Eliminar qualificadores contraditórios** &ndash; por exemplo, se a orientação do dispositivo é retrato, em seguida, todos os diretórios de recurso paisagem serão rejeitados.

- **Ignorar qualificadores não tem suportadas** &ndash; nem todos os qualificadores estão disponíveis para todos os níveis de API. Se um diretório de recursos contém um qualificador que não há suporte para o dispositivo, esse diretório de recursos será ignorado.

- **Identificar o qualificador de prioridade mais alto próximo** &ndash; referindo-se a tabela acima a seleção do qualificador de prioridade mais alto Avançar (de cima para baixo).

- **Mantenha quaisquer diretórios de recursos para o qualificador** &ndash; se houver quaisquer diretórios de recursos que correspondam o qualificador na tabela acima, selecione o próximo qualificador de prioridade mais alto (de cima para baixo).

Essas regras também são ilustradas no fluxograma a seguir:

[![Fluxograma de recursos](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Quando o sistema está procurando por recursos específicos de densidade e não é possível encontrá-los, ele tentará localizar outros recursos específicos de densidade e dimensionamento-los. Android necessariamente não pode usar os recursos padrão.
Por exemplo, quando procurando um recurso de baixa densidade e ele não estiver disponível, Android pode selecionar versão de alta densidade do recurso sobre os recursos padrão ou a densidade média. Isso ocorre porque o recurso de alta densidade pode ser reduzido por um fator de 0,5, o que resulta em menos problemas de visibilidade de dimensionamento de um recurso de densidade média que exija um fator de 0,75.

Por exemplo, considere um aplicativo que tenha os seguintes diretórios de recursos drawable:

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
- **Tela de densidade** &ndash; hdpi
- **Tipo de tela sensível ao toque** &ndash; notouch
- **Método de entrada primário** &ndash; 12key

Em primeiro lugar, os recursos em francês são eliminados conforme eles entram em conflito com a localidade do `en-GB`, ficando com:

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

Em seguida, o qualificador primeiro está selecionado na tabela acima qualificadores: MCC e MNC. Não há nenhum diretório de recursos que contêm esse qualificador para que o código MCC/MNC será ignorado.

O qualificador de Avançar é selecionado, o que é o idioma. Há recursos que correspondam ao código de idioma. Todos os diretórios de recursos que não coincidem com o código do idioma `en` são rejeitadas, para que a lista de recursos é agora:

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

O qualificador Avançar presente é para a orientação da tela, para que todos os diretórios de recursos que não correspondem a orientação da tela de `port` são eliminadas:

    drawable-en-port
    drawable-en-port-ldpi

Next é o qualificador para a densidade da tela, `ldpi`, que resulta na exclusão de um diretório de recursos mais:

    drawable-en-port-ldpi

Como resultado desse processo, o Android usará os recursos drawable no diretório de recursos `drawable-en-port-ldpi` para o dispositivo.

> [!NOTE]
> Os qualificadores de tamanho de tela fornecem uma exceção a esse processo de seleção. É possível para o Android selecionar os recursos que são criados para uma tela menor do que o dispositivo atual fornece. Por exemplo, um dispositivo de tela grande pode usar os recursos fornecem uma tela de tamanho normal. No entanto o oposto isso não é possível: o mesmo dispositivo de tela grande não usará os recursos fornecidos para uma tela de xlarge. Se o Android não é possível encontrar um conjunto de recursos que corresponde a um tamanho de tela de determinado, o aplicativo falhará.
