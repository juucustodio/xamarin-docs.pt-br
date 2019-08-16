---
title: Recursos alternativos
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 7c48244e316854b04234ec8dee4eebeb55a4f119
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526386"
---
# <a name="alternate-resources"></a>Recursos alternativos

Os recursos alternativos são aqueles que visam um dispositivo específico ou uma configuração de tempo de execução, como o idioma atual, o tamanho de tela específico ou a densidade de pixel. Se o Android puder corresponder a um recurso mais específico para um determinado dispositivo ou configuração do que o recurso padrão, esse recurso será usado em vez disso. Se não encontrar um recurso alternativo que corresponda à configuração atual, os recursos padrão serão carregados. Como o Android decide quais recursos serão usados por um aplicativo será abordado em mais detalhes abaixo, na seção local do recurso

Os recursos alternativos são organizados como um subdiretório dentro da pasta de recursos de acordo com o tipo de recurso, assim como os recursos padrão. O nome do subdiretório de recursos alternativo está no formato: _ResourceType_-_Qualifier_

*Qualificador* é um nome que identifica uma configuração de dispositivo específica.
Pode haver mais de um qualificador em um nome, cada um deles separado por um traço. Por exemplo, a captura de tela a seguir mostra um projeto simples que tem recursos alternativos para várias configurações, como localidade, densidade do ecrã, tamanho da tela e orientação:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

As regras a seguir se aplicam ao adicionar qualificadores a um tipo de recurso:

1. Pode haver mais de um qualificador, com cada qualificador separado por um traço.

2. Os qualificadores talvez especificaram apenas uma vez.

3. Os qualificadores devem estar na ordem em que aparecem na tabela abaixo.

Os qualificadores possíveis estão listados abaixo para referência:

- **MCC e MNC** O código do [país móvel](https://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) e, opcionalmente, o [código de rede móvel](https://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). &ndash; O cartão SIM fornecerá o MCC, enquanto a rede à qual o dispositivo está conectado fornecerá o MNC. Embora seja possível direcionar as localidades usando o código do país móvel, a abordagem recomendada é usar o qualificador de idioma especificado abaixo. Por exemplo, para direcionar os recursos para a Alemanha, o `mcc262`qualificador seria. Para direcionar recursos para o T-Mobile nos EUA, o qualificador é `mcc310-mnc026`.
  Para obter uma lista completa de códigos de país móvel e códigos de <http://mcc-mnc.com/>rede móvel, consulte.

- **Idioma** do O código de [idioma ISO 639-1](https://en.wikipedia.org/wiki/ISO_639-1) de duas letras e, opcionalmente, seguido pelo [código de região ISO-3166-Alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)de duas letras. &ndash; 
  Se ambos os qualificadores forem fornecidos, eles serão separados por um `-r`. Por exemplo, para direcionar as localidades de língua francesa, o `fr` qualificador de é usado. Para direcionar as localidades canadenses francesas, o `fr-rCA` seria usado. Para obter uma lista completa de códigos de idiomas e códigos de região, consulte [códigos para a representação de nomes de idiomas](http://www.loc.gov/standards/iso639-2/php/English_list.php) e [nomes de países e elementos de código](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm).

- **Menor largura** &ndash; Especifica a menor largura de tela na qual o aplicativo deve ser executado. Abordados mais detalhadamente na [criação de recursos para diferentes telas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponível no nível de API 13 (Android 3,2) e acima. Por exemplo, o qualificador `sw320dp` é usado para direcionar dispositivos cuja altura e largura seja pelo menos 320dp.

- **Largura disponível** A largura mínima da tela no formato w*N*DP, em que N é a largura em pixels independentes de densidade. &ndash;
  Esse valor pode ser alterado conforme o usuário gira o dispositivo. Abordados mais detalhadamente na [criação de recursos para diferentes telas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponível no nível de API 13 (Android 3,2) e acima. Exemplo: o qualificador w720dp é usado para direcionar dispositivos que têm uma largura de pelo menos 720dp.

- **Altura disponível** A altura mínima da tela no formato h*N*DP, em que N é a altura em DP. &ndash; Esse valor pode ser alterado conforme o usuário gira o dispositivo. Abordados mais detalhadamente na [criação de recursos para diferentes telas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponível no nível de API 13 (Android 3,2) e acima. Por exemplo, o qualificador h720dp é usado para direcionar dispositivos que têm uma altura de menos 720dp

- **Tamanho da tela** &ndash; Esse qualificador é uma generalização do tamanho da tela para o qual esses recursos se destinam. Ele é abordado em mais detalhes na [criação de recursos para diferentes telas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Os valores possíveis são `small`, `normal`, `large` e `xlarge`. Adicionado no nível de API 9 (Android 2.3/Android 2.3.1/Android 2.3.2)

- **Aspecto da tela** &ndash; Isso se baseia na taxa de proporção, não na orientação da tela. Uma tela longa é mais larga. Adicionado no nível de API 4 (Android 1,6). Os valores possíveis são Long e nonlong.

- **Orientação da tela** &ndash; Orientação de tela retrato ou paisagem. Isso pode ser alterado durante o tempo de vida de um aplicativo.
  Os valores possíveis são `port` e `land`.

- **Modo Dock** &ndash; Para dispositivos em um encaixe de carro ou um encaixe de mesa. Adicionado no nível de API 8 (Android 2.2. x). Os valores possíveis são `car` e `desk`.

- **Modo noturno** &ndash; Se o aplicativo está ou não sendo executado à noite ou no dia. Isso pode ser alterado durante o tempo de vida de um aplicativo e deve dar aos desenvolvedores uma oportunidade de usar versões mais escuras de uma interface à noite. Adicionado no nível de API 8 (Android 2.2. x). Os valores possíveis são `night` e `notnight`.

- **Densidade de pixel da tela (DPI)** &ndash; O número de pixels em uma determinada área na tela física. Normalmente expressos como pontos por polegada (DPI). Os possíveis valores são:

    - `ldpi`&ndash; Telas de baixa densidade.

    - `mdpi`&ndash; Telas de densidade média

    - `hdpi`&ndash; Telas de alta densidade

    - `xhdpi`&ndash; Telas de alta densidade extra

    - `nodpi`&ndash; Recursos que não devem ser dimensionados

    - `tvdpi`&ndash; Introduzido no nível de API 13 (Android 3,2) para telas entre mdpi e hdpi.

- **Tipo de tela touch** &ndash; Especifica o tipo de tela Touch que um dispositivo pode ter. Os valores possíveis `notouch` são (sem tela sensível ao `stylus` toque), (um resistive tela de toque adequada para uma `finger` caneta) e (um toque).

- **Disponibilidade do teclado** &ndash; Especifica que tipo de teclado está disponível. Isso pode ser alterado durante o tempo de vida &ndash; de um aplicativo, por exemplo, quando um usuário abre um teclado de hardware. Os possíveis valores são:

    - `keysexposed`&ndash; O dispositivo tem um teclado disponível. Se não houver nenhum teclado de software habilitado, isso só será usado quando o teclado de hardware for aberto.

    - `keyshidden`&ndash; O dispositivo tem um teclado de hardware, mas está oculto e nenhum teclado de software está habilitado.

    - `keyssoft`&ndash; o dispositivo tem um teclado de software habilitado.

- **Método de entrada de texto primário** &ndash; Use para especificar quais tipos de chaves de hardware estão disponíveis para entrada. Os possíveis valores são:

    - `nokeys`&ndash; Não há chaves de hardware para entrada.

    - `qwerty`&ndash; Há um teclado de QWERTY disponível.

    - `12key`&ndash; Há um teclado de hardware de 12 teclas


- **Disponibilidade da chave de navegação** &ndash; Para quando a navegação de 5 vias ou d-Pad (direcional-pad) estiver disponível. Isso pode ser alterado durante o tempo de vida do seu aplicativo. Os possíveis valores são:

    - `navexposed`&ndash; as chaves de navegação estão disponíveis para o usuário

    - `navhidden`&ndash; as chaves de navegação não estão disponíveis.

- **Método de navegação não toque primário** &ndash; O tipo de navegação disponível no dispositivo. Os possíveis valores são:

    - `nonav`&ndash; a única instalação de navegação disponível é a tela sensível ao toque

    - `dpad`&ndash; um d-Pad (direcional-pad) está disponível para navegação

    - `trackball`&ndash; o dispositivo tem um trackball para navegação

    - `wheel`&ndash; o cenário incomum em que há uma ou mais rodas direcionais disponíveis

- **Versão da plataforma (nível de API)** O nível de API com suporte do dispositivo no formato v*N*, em que N é o nível de API que está sendo direcionado. &ndash; Por exemplo, v11 se destinará a um dispositivo de nível de API 11 (Android 3,0).


Para obter informações mais completas sobre qualificadores de recursos, consulte [fornecendo recursos](https://developer.android.com/guide/topics/resources/providing-resources.html) no site de desenvolvedores do Android.


## <a name="how-android-determines-what-resources-to-use"></a>Como o Android determina quais recursos usar

É muito possível e, provavelmente, que um aplicativo Android conterá muitos recursos. É importante entender como o Android selecionará os recursos para um aplicativo quando ele for executado em um dispositivo.

O Android determina a base de recursos Iterando o seguinte teste de regras:

- **Eliminar qualificadores contraditórios** &ndash; por exemplo, se a orientação do dispositivo for retrato, todos os diretórios de recursos de paisagem serão rejeitados.

- **Ignorar qualificadores sem suporte** &ndash; Nem todos os qualificadores estão disponíveis para todos os níveis de API. Se um diretório de recursos contiver um qualificador que não tenha suporte do dispositivo, esse diretório de recursos será ignorado.

- **Identificar o próximo qualificador de prioridade mais alta** &ndash; referindo-se à tabela acima, selecione o próximo qualificador de prioridade mais alta (de cima para baixo).

- **Manter todos os diretórios de recursos para** o qualificador &ndash; se houver quaisquer diretórios de recursos que correspondam ao qualificador para a tabela acima, selecione o próximo qualificador de prioridade mais alta (de cima para baixo).

Essas regras também são ilustradas no fluxograma a seguir:

[![Fluxograma de recursos](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Quando o sistema estiver procurando recursos específicos de densidade e não puder encontrá-los, ele tentará localizar outros recursos específicos de densidade e dimensioná-los. O Android pode não usar necessariamente os recursos padrão.
Por exemplo, ao procurar um recurso de baixa densidade e ele não estiver disponível, o Android poderá selecionar a versão de alta densidade do recurso nos recursos padrão ou de densidade média. Ele faz isso porque o recurso de alta densidade pode ser reduzido por um fator de 0,5, o que resultará em menos problemas de visibilidade do que o dimensionamento de um recurso de densidade média que exigiria um fator de 0,75.

Como exemplo, considere um aplicativo que tenha os seguintes diretórios de recursos desenháveis:

```
drawable
drawable-en
drawable-fr-rCA
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

E agora o aplicativo é executado em um dispositivo com a seguinte configuração:

- **Localidade** do &ndash; en-GB
- **Orientação** do &ndash; porta do
- **Densidade da tela** &ndash; hdpi
- **Tipo de tela touch** &ndash; NoTouch
- **Método de entrada primário** &ndash; 12Key

Para começar, os recursos franceses são eliminados à medida que entram em conflito com `en-GB`a localidade de, deixando-nos:

```
drawable
drawable-en
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

Em seguida, o primeiro qualificador é selecionado na tabela qualificadores acima: MCC e MNC. Não há diretórios de recursos que contenham esse qualificador para que o código MCC/MNC seja ignorado.

O próximo qualificador é selecionado, que é Language. Há recursos que correspondem ao código de idioma. Todos os diretórios de recursos que não correspondem ao código de `en` idioma de são rejeitados, para que a lista de recursos agora seja:

```
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
```

O próximo qualificador presente é para a orientação da tela, de modo que todos os diretórios de recursos que não correspondam à orientação da tela de `port` são eliminados:

```
drawable-en-port
drawable-en-port-ldpi
```

Em seguida, está o qualificador para `ldpi`a densidade da tela, o que resulta na exclusão de um mais diretório de recursos:

```
drawable-en-port-ldpi
```

Como resultado desse processo, o Android usará os recursos de desenho no diretório `drawable-en-port-ldpi` de recursos para o dispositivo.

> [!NOTE]
> Os qualificadores de tamanho de tela fornecem uma exceção para esse processo de seleção. É possível que o Android selecione recursos que são projetados para uma tela menor do que o que o dispositivo atual fornece. Por exemplo, um dispositivo de tela grande pode usar os recursos fornecidos para uma tela de tamanho normal. No entanto, o inverso disso não é verdadeiro: o mesmo dispositivo de tela grande não usará os recursos fornecidos para uma tela XLarge. Se o Android não puder localizar um conjunto de recursos que corresponda a um determinado tamanho de tela, o aplicativo falhará.
