---
title: Material Theme
description: Como aplicar um tema ao seu aplicativo Xamarin. Android com o tema material
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 22549bc7410b62e04cdb34c5c102a2fed18b0cf9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457088"
---
# <a name="material-theme"></a>Material Theme

O *tema do material* é um estilo de interface do usuário que determina a aparência das exibições e atividades a partir do Android 5,0 (pirulito). O tema do material é incorporado ao Android 5,0, portanto, ele é usado pela interface do usuário do sistema, bem como por aplicativos. O tema do material não é um "tema" no sentido de uma opção de aparência de todo o sistema que um usuário pode escolher dinamicamente em um menu de configurações. Em vez disso, o tema do material pode ser considerado como um conjunto de estilos base internos relacionados que você pode usar para personalizar a aparência do seu aplicativo.

O Android fornece três tipos de tema materiais:

- `Theme.Material`&ndash;Versão escura do tema do material; esse é o tipo padrão no Android 5,0.

- `Theme.Material.Light`&ndash;Versão leve do tema do material.

- `Theme.Material.Light.DarkActionBar`&ndash;Versão leve do tema do material, mas com uma barra de ação escura.

Exemplos desses tipos de tema de material são exibidos aqui:

[![Exemplo de capturas de tela do tema escuro, tema claro e tema da barra de ação escura](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

Você pode derivar do tema material para criar seu próprio tema, substituindo alguns ou todos os atributos de cor. Por exemplo, você pode criar um tema derivado de `Theme.Material.Light` , mas substitui a cor da barra do aplicativo para corresponder à cor da marca. Você também pode estilizar modos de exibição individuais; por exemplo, você pode criar um estilo para [CardView](~/android/user-interface/controls/card-view.md) que tenha mais cantos arredondados e use uma cor de fundo mais escura.

Você pode usar um único tema para um aplicativo inteiro ou pode usar temas diferentes para telas diferentes (atividades) em um aplicativo. Nas capturas de tela acima, por exemplo, um único aplicativo usa um tema diferente para cada atividade para demonstrar os esquemas de cores internos. Botões de opção alternam o aplicativo para atividades diferentes e, como resultado, exibem temas diferentes.

Como o tema material tem suporte apenas no Android 5,0 e posterior, você não pode usá-lo (ou um tema personalizado derivado do tema material) para aplicar um tema ao seu aplicativo para ser executado em versões anteriores do Android. No entanto, você pode configurar seu aplicativo para usar o tema de material em dispositivos Android 5,0 e retornar de volta a um tema anterior quando ele for executado em versões mais antigas do Android (consulte a seção [compatibilidade](#compatibility) deste artigo para obter detalhes).

## <a name="requirements"></a>Requisitos

O seguinte é necessário para usar os novos recursos de tema do material do Android 5,0 em aplicativos baseados no Xamarin:

- **Xamarin. Android** &ndash; xamarin. Android 4,20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac. 

- **SDK do Android** &ndash; O Android 5,0 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

- **Java JDK 1,8** &ndash; O JDK 1,7 pode ser usado se você estiver especificamente direcionado para a API de nível 23 e anterior. O JDK 1,8 está disponível na [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Para saber como configurar um projeto de aplicativo do Android 5,0, consulte [Configurando um projeto do android 5,0](~/android/platform/lollipop.md).

## <a name="using-the-built-in-themes"></a>Usando os temas internos

A maneira mais fácil de usar o tema material é configurar seu aplicativo para usar um tema interno sem personalização. Se você não quiser configurar explicitamente um tema, seu aplicativo usará como padrão `Theme.Material` (o tema escuro). Se seu aplicativo tiver apenas uma atividade, você poderá configurar um tema no nível de atividade. Se seu aplicativo tiver várias atividades, você poderá configurar um tema no nível do aplicativo para que ele use o mesmo tema em todas as atividades, ou você pode atribuir diferentes temas a diferentes atividades. As seções a seguir explicam como configurar temas no nível do aplicativo e no nível da atividade.

### <a name="theming-an-application"></a>Um aplicativo

Para configurar um aplicativo inteiro para usar um tipo de tema de material, defina o `android:theme` atributo do nó do aplicativo em **AndroidManifest.xml** como um dos seguintes:

- `@android:style/Theme.Material`&ndash;Tema escuro.

- `@android:style/Theme.Material.Light`&ndash;Tema claro.

- `@android:style/Theme.Material.Light.DarkActionBar`&ndash;Tema claro com barra de ação escura.

O exemplo a seguir configura o aplicativo *MyApp* para usar o tema claro:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

Como alternativa, você pode definir o `Theme` atributo Application em **AssemblyInfo.cs** (ou **Properties.cs**). Por exemplo:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Quando o tema do aplicativo for definido como `@android:style/Theme.Material.Light` , todas as atividades em *MyApp* serão exibidas usando `Theme.Material.Light` .

### <a name="theming-an-activity"></a>Uma atividade

Para aplicar um tema a uma atividade, você adiciona uma `Theme` configuração ao `[Activity]` atributo acima da declaração da atividade e atribui `Theme` ao tipo de tema do material que deseja usar. O exemplo a seguir temas de uma atividade com `Theme.Material.Light` :

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Outras atividades neste aplicativo usarão o `Theme.Material` esquema de cores escuro padrão (ou, se configurado, a configuração de tema do aplicativo).

<a name="customtheme"></a>

## <a name="using-custom-themes"></a>Usando temas personalizados

Você pode aprimorar sua marca criando um tema personalizado que modela seu aplicativo com as cores s de sua marca &rsquo; . Para criar um tema personalizado, você define um novo estilo que deriva de um tipo de tema de material interno, substituindo os atributos de cor que você deseja alterar. Por exemplo, você pode definir um tema personalizado que deriva de `Theme.Material.Light.DarkActionBar` e altera a cor do plano de fundo da tela para bege em vez de branco.

O tema material expõe os seguintes atributos de layout para personalização:

- `colorPrimary`&ndash;A cor da barra de aplicativos.

- `colorPrimaryDark`&ndash;A cor da barra de status e das barras de aplicativo contextuais; essa é normalmente uma versão escura do `colorPrimary` .

- `colorAccent`&ndash;A cor dos controles da interface do usuário, como caixas de seleção, botões de opção e editar caixas de texto.

- `windowBackground`&ndash;A cor do plano de fundo da tela.

- `textColorPrimary`&ndash;A cor do texto da interface do usuário na barra de aplicativos.

- `statusBarColor`&ndash;A cor da barra de status.

- `navigationBarColor`&ndash;A cor da barra de navegação.

Essas áreas de tela são rotuladas no seguinte diagrama:

[![Diagrama de atributos e suas áreas de tela associadas](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

Por padrão, `statusBarColor` é definido como o valor de `colorPrimaryDark` . Você pode definir `statusBarColor` como uma cor sólida ou pode defini-la como `@android:color/transparent` para tornar a barra de status transparente. A barra de navegação também pode se tornar transparente definindo `navigationBarColor` como `@android:color/transparent` .

### <a name="creating-a-custom-app-theme"></a>Criando um tema de aplicativo personalizado

Você pode criar um tema de aplicativo personalizado criando e modificando arquivos na pasta de **recursos** do seu projeto de aplicativo. Para estilizar seu aplicativo com um tema personalizado, use as seguintes etapas:

- Crie um arquivo de **colors.xml** em **recursos/valores** &mdash; Use esse arquivo para definir suas cores de tema personalizadas. Por exemplo, você pode colar o código a seguir em **colors.xml** para ajudá-lo a começar:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

- Modifique este arquivo de exemplo para definir os nomes e códigos de cores para os recursos de cores que serão usados em seu tema personalizado.

- Crie uma pasta **Resources/Values-v21** . Nessa pasta, crie um arquivo de **styles.xml** :

    [![Local de styles.xml na pasta resources/values-21.xml](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Observe que **os recursos/valores-v21** é específico para &ndash; as versões anteriores do Android 5,0 do Android não lerá os arquivos nessa pasta.

- Adicione um `resources` nó a **styles.xml** e defina um `style` nó com o nome do seu tema personalizado. Por exemplo, aqui está um arquivo **styles.xml** que define *MyCustomTheme* (derivado do estilo de tema interno `Theme.Material.Light` ):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

- Neste ponto, um aplicativo que usa *MyCustomTheme* exibirá o tema de ações `Theme.Material.Light` sem personalizações:

    [![Aparência do tema personalizado antes das personalizações](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

- Adicione personalizações de cor a **styles.xml** definindo as cores dos atributos de layout que você deseja alterar. Por exemplo, para alterar a cor da barra de aplicativos para `my_blue` e alterar a cor dos controles da interface do usuário para o `my_purple` , adicione substituições de cores a **styles.xml** que se referem aos recursos de cores configurados no **colors.xml**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

Com essas alterações em vigor, um aplicativo que usa o *MyCustomTheme* exibirá uma cor da barra de aplicativo no `my_blue` e controles de interface do usuário no `my_purple` , mas usará o `Theme.Material.Light` esquema de cores em todos os outros lugares:

[![Aparência do tema personalizado após as personalizações](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

Neste exemplo, *MyCustomTheme* empresta cores de `Theme.Material.Light` para a cor do plano de fundo, a barra de status e as cores do texto, mas altera a cor da barra do aplicativo para `my_blue` e define a cor do botão de opção como `my_purple` .

<a name="customview"></a>

### <a name="creating-a-custom-view-style"></a>Criando um estilo de exibição personalizado

O Android 5,0 também possibilita a você Estilizar uma exibição individual. Depois de criar **colors.xml** e **styles.xml** (conforme descrito na seção anterior), você pode adicionar um estilo de exibição a **styles.xml**.
Para estilizar um modo de exibição individual, use as seguintes etapas:

- Edite **recursos/valores-v21/styles.xml** e adicione um `style` nó com o nome do estilo de exibição personalizado. Defina os atributos de cor personalizados para sua exibição dentro deste `style` nó. Por exemplo, para criar um estilo de [CardView](~/android/user-interface/controls/card-view.md) personalizado que tenha mais cantos arredondados e use `my_blue` como a cor do plano de fundo do cartão, adicione um `style` nó a **styles.xml** (dentro do `resources` nó) e configure a cor do plano de fundo e o raio do canto:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

- No layout, defina o `style` atributo dessa exibição para corresponder ao nome de estilo personalizado que você escolheu na etapa anterior. Por exemplo:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

A captura de tela a seguir fornece um exemplo do padrão `CardView` (mostrado à esquerda) em comparação a um `CardView` que tenha sido estilizado com o `CardView.MyBlue` tema personalizado (mostrado à direita):

[![Exemplos de CardView padrão e CardView personalizados](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

Neste exemplo, o personalizado `CardView` é exibido com a cor do plano de fundo `my_blue` e um raio do canto do 18dp.

## <a name="compatibility"></a>Compatibilidade

Para estilizar seu aplicativo de modo que ele use o tema de material no Android 5,0, mas é revertido automaticamente para um estilo compatível com as versões anteriores do Android, use as seguintes etapas:

- Defina um tema personalizado em **Resources/Values-v21/styles.xml** que deriva de um estilo de tema de material. Por exemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- Defina um tema personalizado em **recursos/valores/styles.xml** que deriva de um tema mais antigo, mas que usa o mesmo nome de tema acima. Por exemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- Em **AndroidManifest.xml**, configure seu aplicativo com o nome do tema personalizado. 
    Por exemplo:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

- Como alternativa, você pode Estilizar uma atividade específica usando seu tema personalizado:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Se seu tema usa cores definidas em um arquivo de **colors.xml** , certifique-se de posicionar esse arquivo em **recursos/valores** (em vez de **recursos/valores-v21**) para que ambas as versões do seu tema personalizado possam acessar suas definições de cores.

Quando seu aplicativo for executado em um dispositivo Android 5,0, ele usará a definição de tema especificada em **Resources/Values-v21/styles.xml**. Quando esse aplicativo é executado em dispositivos Android mais antigos, ele automaticamente volta para a definição de tema especificada em **Resources/Values/styles.xml**.

Para obter mais informações sobre a compatibilidade de temas com versões mais antigas do Android, consulte [recursos alternativos](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Resumo

Este artigo introduziu o novo estilo de interface de usuário tema do material incluído no Android 5,0 (pirulito). Ele descreveu os três tipos de tema de material interno que você pode usar para estilizar seu aplicativo, explicado como criar um tema personalizado para a identidade visual de seu aplicativo e forneceu um exemplo de como aplicar um tema a um modo de exibição individual. Por fim, este artigo explicou como usar o tema material em seu aplicativo, mantendo a compatibilidade com versões anteriores do Android.

## <a name="related-links"></a>Links Relacionados

- [ThemeSwitcher (exemplo)](/samples/xamarin/monodroid-samples/android50-themeswitcher)
- [Introdução à pirulito](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [Recursos alternativos](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Pirulito do Android](https://developer.android.com/about/versions/lollipop)
- [Desenvolvedor de pizza do Android](https://developer.android.com/about/versions/pie/)
- [Design de material](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [Princípios de design de material](https://material.io/design/)
- [Mantendo a compatibilidade](https://developer.android.com/training/backward-compatible-ui/)