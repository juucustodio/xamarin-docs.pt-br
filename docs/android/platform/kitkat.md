---
title: Recursos de KitKat
description: Android 4.4 (KitKat) vem carregado com uma infinidade de recursos para desenvolvedores e usuários. Este guia destaca vários desses recursos e fornece exemplos de código e detalhes de implementação para ajudá-lo a aproveitar ao máximo KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 7b7fa2ea99a58b875bc1dc579455511aa9bf72d1
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172087"
---
# <a name="kitkat-features"></a>Recursos de KitKat

_Android 4.4 (KitKat) vem carregado com uma infinidade de recursos para desenvolvedores e usuários. Este guia destaca vários desses recursos e fornece exemplos de código e detalhes de implementação para ajudá-lo a aproveitar ao máximo KitKat._

## <a name="overview"></a>Visão geral

Android 4.4 (API nível 19), também conhecido como "KitKat", foi lançado em 2013 tardia. KitKat oferece uma variedade de novos recursos e aprimoramentos, incluindo:

-  [Experiência do usuário](#user_experience) &ndash; animações fácil com o framework de transição, translúcidas barras de status e de navegação e modo de tela inteira imersivo ajudar a criar uma melhor experiência do usuário.

-  [Conteúdo do usuário](#user_content) &ndash; simplificado de gerenciamento de arquivos do usuário com a estrutura de acesso de armazenamento; impressão de imagens, sites da web e outros tipos de conteúdo é mais fácil com as APIs de impressão aprimorado.

-  [Hardware](#hardware) &ndash; transforme qualquer aplicativo em um cartão de NFC com NFC baseado em Host emulação do cartão; executar sensores de baixo consumo de energia com o `SensorManager` .

-  [Ferramentas de desenvolvedor](#developer_tools) &ndash; aplicativos Screencast em ação com o cliente Android Debug Bridge, disponível como parte do SDK do Android.


Este guia fornece orientações sobre como migrar um aplicativo xamarin. Android existente KitKat, bem como uma visão geral do KitKat para desenvolvedores do xamarin. Android.

## <a name="requirements"></a>Requisitos

Para desenvolver aplicativos xamarin. Android usando KitKat, você precisa *xamarin. Android 4.11.0* ou posterior e Android 4.4 (API nível 19) instalado por meio do Gerenciador de SDK do Android, conforme ilustrado pela captura de tela a seguir:

[![Selecionando o Android 4.4 no Gerenciador de SDK do Android](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrando seu aplicativo para KitKat

Esta seção fornece alguns itens da primeira resposta para ajudar a fazer a transição aplicativos existentes para o Android 4.4.

### <a name="check-system-version"></a>Verificar a versão do sistema

Se um aplicativo precisar ser compatível com versões mais antigas do Android, certifique-se de encapsular qualquer código KitKat específico em uma verificação de versão do sistema, conforme ilustrado pelo exemplo de código abaixo:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>O envio em lote de alarme

Android usa os serviços de alarme para ativar um aplicativo em segundo plano em um horário especificado. KitKat leva isso um passo adiante por envio em lote alarmes para preservar a energia. Isso significa que, no lugar de despertar cada aplicativo em um horário específico, KitKat prefere para agrupar vários aplicativos que estão registrados para ativar durante o mesmo intervalo de tempo e ativá-los ao mesmo tempo.
Para informar ao Android para ativar um aplicativo durante um intervalo de tempo especificado, chame `SetWindow` sobre o [ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/), passando o mínimo e o tempo máximo, em milissegundos, que pode decorrer antes do aplicativo é ativado e a operação a ser executada na ativação.
O código a seguir fornece um exemplo de um aplicativo que precisa ser ativado entre meia hora e uma hora a partir do momento em que a janela é definida:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Para continuar a ativação de um aplicativo em um horário específico, use `SetExact`, passando no momento exato em que o aplicativo deve ser ativado e a operação a ser executada:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat não permite definir um alarme de repetição exato. Aplicativos que usam [`SetRepeating`](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/)
e exigem alarmes exatas funcione será agora precisa disparar cada alarme manualmente.

### <a name="external-storage"></a>Armazenamento externo

Armazenamento externo agora é dividido em dois tipos - armazenamento exclusivo para seu aplicativo e dados compartilhados por vários aplicativos. Lendo e gravando em local específico do seu aplicativo no armazenamento externo não requer nenhuma permissão especial. Interagir com os dados no armazenamento compartilhado agora requer o `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE` permissão. Os dois tipos podem ser classificados como tal:

-  Se você estiver recebendo um caminho de arquivo ou diretório, chamando um método na `Context` - por exemplo, [`GetExternalFilesDir`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/)
   Ou [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - seu aplicativo não requer nenhuma permissão extra.

-  Se você estiver recebendo um caminho de arquivo ou diretório ao acessar uma propriedade ou chamar um método em `Environment` , como [`GetExternalStorageDirectory`](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/)
   Ou [`GetExternalStoragePublicDirectory`](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/)
   , seu aplicativo requer o `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE` permissão.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` implica o `READ_EXTERNAL_STORAGE` permissão, portanto, você só precisará definir uma permissão.

### <a name="sms-consolidation"></a>Consolidação de SMS

KitKat simplifica o envio de mensagens para o usuário, agregando todo o conteúdo SMS no aplicativo de um padrão selecionado pelo usuário. O desenvolvedor é responsável por fazer com que o aplicativo selecionável como o aplicativo de mensagens padrão e se comportando apropriadamente no código e na vida pessoal se o aplicativo não estiver selecionado. Para obter mais informações sobre a transição de seu aplicativo SMS para KitKat, consulte o [obtendo o SMS aplicativos prontos para KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) guia do Google.

### <a name="webview-apps"></a>Aplicativos do WebView

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) tem uma transformação no KitKat. A maior mudança é mais segurança para carregar o conteúdo em um `WebView`. Embora a maioria dos aplicativos destinados a versões mais antigas de API devem funcionar conforme o esperado, teste de aplicativos que usam o `WebView` classe é altamente recomendável. Para obter mais informações sobre o WebView APIs afetadas, consulte o Android [migrando para o WebView no Android 4.4](http://developer.android.com/guide/webapps/migrating.html) documentação.

<a name="user_experience" />

## <a name="user-experience"></a>Experiência do Usuário

KitKat vem com várias novas APIs para aprimorar a experiência do usuário, incluindo a nova estrutura de transição para lidar com as animações de propriedade e uma opção de interface do usuário translúcida para temas. Essas alterações são abordadas a seguir.

### <a name="transition-framework"></a>Estrutura de transição

A estrutura de transição faz com que as animações mais fácil de implementar. KitKat permite que você executar uma animação de propriedade simples com apenas uma linha de código ou personalizar as transições usando *cenas*.

#### <a name="simple-property-animation"></a>Animação de propriedade simples

A nova biblioteca de transições Android simplifica o código por trás de animações de propriedade. O framework permite que você executar animações simples com o mínimo de código. Por exemplo, o exemplo de código a seguir usa [`TransitionManager.BeginDelayedTransition`](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/)
para animar mostrando e ocultando um `TextView`:

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

O exemplo acima usa a estrutura de transição para criar um automático, a transição padrão entre os valores de propriedade de variáveis. Porque a animação é identificada por uma única linha de código, você pode facilmente fazer isso compatível com versões mais antigas do Android, encapsulando o `BeginDelayedTransition` chamar em uma verificação de versão do sistema. Consulte a [migrando seu aplicativo para KitKat](#Migrating_Your_App_to_KitKat) seção para obter mais informações.

Captura de tela abaixo mostra o aplicativo antes da animação:

[![Captura de tela do aplicativo antes do início da animação](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

Captura de tela abaixo mostra o aplicativo depois que a animação:

[![Captura de tela do aplicativo após a conclusão de animação](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Você pode obter mais controle sobre a transição com cenas, que são abordadas na próxima seção.

#### <a name="android-scenes"></a>Cenas de Android

[Cenas](https://developer.xamarin.com/api/type/Android.Transitions.Scene/) foram introduzidos como parte do framework para dar ao desenvolvedor mais controle sobre animações de transição. Cenas criar uma área dinâmica na interface do usuário: especificar um contêiner e várias versões ou "cenas", para o conteúdo XML dentro do contêiner, e Android faz o resto do trabalho para animar as transições entre as cenas. Cenas Android permitem que você crie animações complexas com o mínimo de trabalho no lado do desenvolvimento.

O elemento de interface do usuário estático que hospeda o conteúdo dinâmico é um chamado um *recipiente* ou *cena base*. O exemplo a seguir usa o Designer do Android para criar uma `RelativeLayout` chamado `container`:

[![Usando o Designer do Android para criar um contêiner de RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

O layout de exemplo também define um botão chamado `sceneButton` abaixo de `container`. Esse botão irá disparar a transição.

O conteúdo dinâmico dentro do contêiner requer dois novos layouts de Android. Esses layouts especificam somente o código *dentro de* o contêiner.
O código de exemplo a seguir define um layout chamado *Scene1* que contém dois campos de texto de leitura "Kit" e "Kat", respectivamente, e um segundo layout chamado *Scene2* que contém os mesmos campos de texto invertidos. O XML é da seguinte maneira:

 **Scene1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

O exemplo acima usa `merge` para tornar o código de exibição mais curto e simplificar a hierarquia de exibição. Você pode ler mais sobre `merge` layouts [aqui](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Uma cena é criada chamando [ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/), passando o objeto de contêiner, a ID de recurso do arquivo de layout da cena e atual `Context`, conforme ilustrado pelo exemplo de código abaixo:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Clicando no botão gira entre duas cenas, o que Android anima com os valores de transição padrão:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

Captura de tela abaixo ilustra a cena antes da animação:

[![Captura de tela do aplicativo antes de inicia a animação](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Captura de tela abaixo ilustra a cena depois que a animação:

[![Captura de tela do aplicativo depois que a animação é concluída](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> Há um [bug conhecido](https://code.google.com/p/android/issues/detail?id=62450) as transições Android biblioteca que faz com que o segundo plano criado usando `GetSceneForLayout` interrompido quando um usuário navega por meio de uma atividade na segunda vez. Uma solução alternativa de java é descrita [aqui](http://www.doubleencore.com/2013/11/new-transitions-framework/).


##### <a name="custom-transitions-in-scenes"></a>Transições personalizadas em cenas

Uma transição personalizada pode ser definida em um arquivo de recurso xml na `transition` diretório sob `Resources`, conforme ilustrado pela captura de tela abaixo:

[![Local do arquivo transition.xml no diretório de recursos/transição](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

O exemplo de código a seguir define uma transição que anima por 5 segundos e usa o [com o risco de interpolador](http://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

A transição é criada na atividade usando o [TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/), conforme ilustrado pelo código a seguir:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

A nova transição é adicionada ao `Go` chamada que inicia a animação:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interface do usuário translúcido

KitKat dá mais controle sobre temas seu aplicativo com opcional translúcidos barras de status e a navegação. Você pode alterar a translucência de elementos de interface do usuário do sistema no mesmo arquivo XML que você usa para definir o tema do Android. KitKat apresenta as seguintes propriedades:

-  `windowTranslucentStatus` -Quando definido como true, torna a barra de status superior translúcido.

-  `windowTranslucentNavigation` -Quando definido como true, torna a barra de navegação inferior translúcido.

-  `fitsSystemWindows` -Definir a barra superior ou inferior como transcluent desloca os elementos de interface do usuário transparentes o conteúdo por padrão. Definir essa propriedade como `true` é uma maneira simples para impedir que o conteúdo sobrepostas com os elementos de interface do usuário do sistema translúcido.


O código a seguir define um tema com barras de status e a navegação translúcidos:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

Captura de tela abaixo mostra o tema acima com status translúcido e barras de navegação:

[![Captura de tela de exemplo de aplicativo com translúcido barras de status e navegação](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Conteúdo do usuário

### <a name="storage-access-framework"></a>Estrutura de acesso de armazenamento

O Framework de acesso de armazenamento (SAF) é uma nova maneira para os usuários interajam com o conteúdo armazenado como imagens, vídeos e documentos. Em vez de apresentar os usuários com uma caixa de diálogo para escolher um aplicativo para lidar com conteúdo, KitKat abre uma nova interface do usuário que permite aos usuários acessar seus dados em um local de agregação. Depois que o conteúdo foi escolhido, o usuário será retornado para o aplicativo que solicitou o conteúdo e a experiência de aplicativo continuará normalmente.

Essa alteração exige duas ações no lado do desenvolvedor: primeiro, os aplicativos que exigem o conteúdo de provedores precisam ser atualizados para uma nova maneira de solicitar conteúdo. Segundo, os aplicativos que gravam dados em um `ContentProvider` precisam ser modificados para usar a nova estrutura. Ambos os cenários dependem de novo [`DocumentsProvider`](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/)
API.

#### <a name="documentsprovider"></a>DocumentsProvider

No KitKat, interações com `ContentProviders` são abstraídos com o `DocumentsProvider` classe. Isso significa que SAF não importa onde os dados são fisicamente, desde que ele pode ser acessado por meio de `DocumentsProvider` API. Provedores de locais, serviços de nuvem e dispositivos de armazenamento externo todos usam a mesma interface e é tratada da mesma forma, fornecendo o usuário e o desenvolvedor com um único local para interagir com o conteúdo do usuário.

Esta seção aborda como carregar e salvar o conteúdo com o Framework de acesso de armazenamento.

#### <a name="request-content-from-a-provider"></a>Conteúdo da solicitação de um provedor

Podemos dizer KitKat que queremos separar o conteúdo usando a UI SAF com o `ActionOpenDocument` intenção, o que significa que desejamos conectar-se a todos os provedores de conteúdo disponíveis para o dispositivo. Você pode adicionar alguns filtragem para esse propósito, especificando `CategoryOpenable`, que significa que somente o conteúdo que pode ser aberto (ou seja, acessível e úteis conteúdo) será retornado. KitKat também permite a filtragem de conteúdo com o `MimeType`. Por exemplo, o código a seguir filtros para resultados da imagem especificando a imagem `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

Chamar `StartActivityForResult` inicia o SAF UI, que o usuário pode procurar para escolher uma imagem:

[![Captura de tela de exemplo de um aplicativo usando a estrutura de acesso de armazenamento para navegar para uma imagem](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Depois que o usuário escolheu uma imagem `OnActivityResult` retorna o `Android.Net.Uri` do arquivo escolhido. O exemplo de código a seguir exibe a seleção de imagem do usuário:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>Gravar o conteúdo em um provedor

Além de carregar o conteúdo a UI SAF, KitKat também permite que você salve o conteúdo em qualquer `ContentProvider` que implementa o `DocumentProvider` API. Salvando conteúda usa um `Intent` com `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

O exemplo de código acima da UI SAF, permitindo que o usuário altere o nome do arquivo e selecione um diretório para abrigar o novo arquivo é carregado:

[![Captura de tela do usuário alterando o nome do arquivo para NewDoc no diretório de Downloads](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Quando o usuário pressiona **salve**, `OnActivityResult` é passado a `Android.Net.Uri` do arquivo criado recentemente, que pode ser acessado com `data.Data`. O uri pode ser usado para transmitir dados para o novo arquivo:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

Observe que [`ContentResolver.OpenOutputStream(Android.Net.Uri)`](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri))
Retorna um `System.IO.Stream`, portanto, todo o processo de streaming pode ser escrito em .NET.

Para obter mais informações sobre o carregamento, criando e editando conteúdo com a estrutura de acesso de armazenamento, consultem o [documentação do Android para o Framework de acesso de armazenamento](http://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Imprimindo

Conteúdo de impressão é simplificado no KitKat com a introdução do [serviços de impressão](https://developer.xamarin.com/api/namespace/Android.PrintServices/) e `PrintManager`. KitKat também é a primeira versão de API para aproveitar totalmente a [APIs do serviço do Google Cloud Print](https://developers.google.com/cloud-print/) usando o [aplicativo do Google Cloud Print](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
A maioria dos dispositivos que vêm com KitKat automaticamente baixar o aplicativo de impressão do Google Cloud e o [plug-in de serviço de impressão HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)quando eles se conectam pela primeira vez ao Wi-Fi. Um usuário pode verificar as configurações de impressão do seu dispositivo navegando até **Configurações > sistema > imprimindo**:

[![Captura de tela de exemplo da tela de configurações de impressão](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Embora as APIs de impressão são configuradas para trabalhar com Google de impressão em nuvem por padrão, o Android ainda permite aos desenvolvedores preparar conteúdo impresso usando as novas APIs e enviá-lo para outros aplicativos para lidar com a impressão.



#### <a name="printing-html-content"></a>Conteúdo HTML de impressão

KitKat cria automaticamente um [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) para uma exibição da web com `WebView.CreatePrintDocumentAdapter`. Conteúdo da web de impressão é um esforço coordenado entre um [ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) que aguarda o conteúdo HTML a ser carregado e permite que a atividade de saber para disponibilizar a opção de impressão no menu de opções e a atividade, que aguarda o usuário Selecione a opção de impressão e chama `Print`sobre o `PrintManager`. Esta seção aborda a configuração básica necessária na tela de imprimir conteúdo HTML.

Observe que ao carregar e imprimir o conteúdo da web requer a permissão de Internet:

[![Definindo permissão de Internet nas opções do aplicativo](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Item do Menu Imprimir

A opção de impressão normalmente aparecerão na atividade de [menu de opções](http://developer.android.com/guide/topics/ui/menus.html#options-menu).
O menu de opções permite aos usuários executar ações em uma atividade. Ele está no canto superior direito da tela e tem esta aparência:

[![Captura de tela de exemplo do item de menu Imprimir exibido no canto superior direito da tela](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Itens de menu adicionais podem ser definidos na *menus*diretório sob *recursos*. O código a seguir define um amostra item de menu chamado [impressão](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

Interação com o menu de opções na atividade ocorre por meio de `OnCreateOptionsMenu` e `OnOptionsItemSelected` métodos.
`OnCreateOptionsMenu` é o lugar para adicionar novos itens de menu, como a opção de impressão, a partir de *menu* diretório de recursos.
`OnOptionsItemSelected` escuta o usuário selecionando a opção Imprimir no menu e começa a ser impressa:

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

O código acima também define uma variável chamada `dataLoaded` para acompanhar o status do conteúdo HTML. O `WebViewClient` definirá essa variável como true quando todo o conteúdo foi carregado, portanto, sabe que a atividade para adicionar o item de menu de impressão para o menu de opções.

##### <a name="webviewclient"></a>WebViewClient

O trabalho do `WebViewClient` é para garantir que os dados no `WebView` é totalmente carregado antes da opção de impressão é exibida no menu, que é feito com o `OnPageFinished` método. `OnPageFinished` escuta para o conteúdo da web para concluir o carregamento e informa a atividade para recriar seu menu de opções com `InvalidateOptionsMenu`:

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` também define o `dataLoaded` de valor para `true`, então `OnCreateOptionsMenu` pode recriar o menu com a opção de impressão em vigor.

##### <a name="printmanager"></a>PrintManager

O exemplo de código a seguir imprime o conteúdo de um `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` usa como argumentos: um nome para o trabalho de impressão ("MyWebPage" neste exemplo), um [`PrintDocumentAdapter`](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/)
que gera o documento de impressão de conteúdo, e [`PrintAttributes`](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/)
(`null` no exemplo acima). Você pode especificar `PrintAttributes` para ajudar a dispor o conteúdo na página impressa, embora os atributos padrão devem lidar com a maioria dos cenários.

Chamar `Print` carrega a interface do usuário impressão, que lista as opções para o trabalho de impressão. A interface do usuário dá aos usuários a opção de imprimir ou salvar o conteúdo HTML a um PDF, como ilustrado pelas capturas de tela abaixo:

[![Captura de tela de PrintHtmlActivity exibindo o menu Imprimir](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Captura de tela de PrintHtmlActivity exibindo Salvar como menu PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat adiciona várias APIs para acomodar os novos recursos do dispositivo. O mais notável deles são baseadas em Host emulação do cartão e o novo `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulação de cartão baseado em host em NFC

Emulação de cartão com base em host (HCE) permite que os aplicativos se comportam como cartões de NFC ou leitores de cartão de NFC sem depender de elemento de proteger proprietárias da operadora. Antes de configurar HCE, certifique-se de HCE está disponível no dispositivo com `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE requer que ambos os o recurso HCE e o `Nfc` permissão ser registrados com o aplicativo `AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Definir a permissão de NFC nas opções do aplicativo](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Para trabalhar, HCE deve ser capaz de executar em segundo plano, e ele deve iniciar quando o usuário faz uma transação de NFC, mesmo que o aplicativo usando HCE não está em execução. Podemos fazer isso, escrever o código HCE como um `Service`. Implementa um serviço HCE o `HostApduService` interface, que implementa os seguintes métodos:

-  *ProcessCommandApdu* -unidade de dados de protocolo de aplicativo um (APDU) é o que é enviado entre o leitor de NFC e o serviço de HCE. Esse método consome um ADPU do leitor e retorna uma unidade de dados na resposta.

-  *OnDeactivated* – o `HostAdpuService` é desativado quando o serviço HCE não está se comunicando com o leitor de NFC.


Um serviço HCE também precisa ser registrado com o manifesto do aplicativo e decorada com as permissões adequadas, filtro intencional e metadados. O código a seguir é um exemplo de uma `HostApduService` registrados com o manifesto do Android usando o `Service` atributo (para obter mais informações sobre atributos, consulte o Xamarin [trabalhar com o manifesto do Android](~/android/platform/android-manifest.md) guia):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

O serviço acima fornece uma maneira para o leitor de NFC interagir com o aplicativo, mas o leitor de NFC tem ainda não há como saber se esse serviço está emulando o cartão de NFC que ele precisa examinar. Para ajudar o leitor de NFC para identificar o serviço, podemos atribuir o serviço de um único *ID do aplicativo (Ajuda)*. Podemos especificar um AUXÍLIO, juntamente com outros metadados sobre o serviço HCE, em um arquivo de recurso xml registrado com o `MetaData` atributo (consulte o exemplo de código acima). Esse arquivo de recurso Especifica um ou mais filtros de AUXÍLIO - identificador exclusivo cadeias de caracteres em formato hexadecimal que correspondem aos auxílios de um ou mais dispositivos de leitor de NFC:

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

Além dos filtros da Ajuda, o arquivo de recurso xml também fornece uma descrição de voltadas ao usuário do serviço HCE, especifica um grupo de material de apoio (aplicativo de pagamento em comparação com "outros") e, no caso de um aplicativo de pagamento, uma faixa de dp 260 x 96 para exibir para o usuário.

O programa de instalação descrito acima fornece os blocos de construção básicos para um aplicativo emulando um cartão de NFC. NFC em si requer várias etapas adicionais e os testes adicionais para configurar. Para obter mais informações sobre a emulação de cartão com base em Host, consulte o [portal de documentação Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Para obter mais informações sobre como usar o NFC com Xamarin, confira a [exemplos de Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensores

KitKat fornece acesso aos sensores do dispositivo por meio de um [ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/).
O `SensorManager` permite que o sistema operacional para agendar a entrega de informações do sensor em um aplicativo em lotes, preservando a vida útil da bateria.

KitKat também é fornecido com dois novos tipos de sensor para acompanhar as etapas do usuário. Elas se baseiam no accelerometer e incluem:

-  *StepDetector* -aplicativo é notificado/ativado quando o usuário dá um passo e o detector fornece um valor de tempo para quando a etapa ocorreu.

-  *StepCounter* -mantém controle sobre o número de etapas que o usuário tenha feito desde que o sensor foi registrado *até a próxima reinicialização do dispositivo*.

Captura de tela abaixo ilustra o contador de etapa em ação:

[![Captura de tela do aplicativo SensorsActivity exibindo um contador de etapa](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Você pode criar uma `SensorManager` chamando `GetSystemService(SensorService)` e convertendo o resultado como um `SensorManager`. Para usar o contador de etapa, chame `GetDefaultSensor` sobre o `SensorManager`. Você pode registrar o sensor e ouvir as alterações na contagem de etapa com a Ajuda das [`ISensorEventListener`](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/)
interface, conforme ilustrado pelo exemplo de código abaixo:

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged` é chamado se a contagem de etapa de atualizações enquanto o aplicativo estiver em primeiro plano. Se o aplicativo entra em segundo plano, ou se o dispositivo estiver no estado de suspensão, `OnSensorChanged` não será chamado; no entanto, as etapas continuarão a serem contados até `UnregisterListener` é chamado.

Tenha em mente que *o valor de contagem da etapa é cumulativo em todos os aplicativos que registram o sensor*. Isso significa que, mesmo se você desinstalar e reinstalar o aplicativo e inicializa o `count` variável em 0 na inicialização do aplicativo, o valor reportado pelo sensor permanecerá o número total de etapas executadas durante o registro do sensor, se pelo seu aplicativo ou outro. Você pode impedir que seu aplicativo adicionando-se para o contador de etapa chamando `UnregisterListener` sobre o `SensorManager`, conforme ilustrado pelo código a seguir:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Reinicialização do dispositivo redefine a contagem de etapa para 0. Seu aplicativo exige código extra para garantir que ele está relatando uma contagem precisa para o aplicativo, independentemente de outros aplicativos que usam o sensor ou o estado do dispositivo.


> [!NOTE]
> Enquanto a API para a detecção de etapa e a contagem é fornecido com KitKat, nem todos os telefones são equipados com o sensor. Você pode verificar se o sensor está disponível executando `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`, ou verifique se o valor retornado de `GetDefaultSensor` não é `null`.


<a name="developer_tools" />

## <a name="developer-tools"></a>Ferramentas para Desenvolvedores

### <a name="screen-recording"></a>Gravação de tela

KitKat inclui a nova tela de gravação de recursos para que os desenvolvedores podem gravar aplicativos em ação. Gravação de tela está disponível por meio de [Android Debug Bridge (ADB)](http://developer.android.com/tools/help/adb.html) cliente, que pode ser baixado como parte do SDK do Android.

Para registrar sua tela, conecte o dispositivo; em seguida, localize sua instalação do SDK do Android, navegue até a **ferramentas de plataforma** diretório e execute o **adb** cliente:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

O comando acima será gravado um vídeo de 3 minutos padrão com a resolução padrão de 4 Mbps. Para editar o comprimento, adicione a *– limite de tempo* sinalizador.
Para alterar a resolução, adicione a *– taxa de bits* sinalizador. O comando a seguir registrará um vídeo de longa de minuto em 8Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Você pode encontrar seu vídeo em seu dispositivo - ela aparecerá na sua galeria quando a gravação for concluída.


## <a name="other-kitkat-additions"></a>Outras adições KitKat

Além das alterações descritas acima, KitKat permite que você:

-  *Use a tela inteira* -KitKat apresenta um novo [modo imersivo](http://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) para conteúdo de navegação, em jogos e que executam outros aplicativos que podem se beneficiar de uma experiência de tela inteira.

-  *Personalizar notificações* -obter detalhes adicionais sobre as notificações do sistema com o [`NotificationListenerService`](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/)
   . Isso permite que você apresente as informações de forma diferente dentro de seu aplicativo.

-  *Espelhar recursos Desenháveis* -recursos Desenháveis possui um novo [`autoMirrored`](http://developer.android.com/reference/android/R.attr.html#autoMirrored)
   atributo que informa ao sistema de criar uma versão espelhada para imagens que exigem a inversão de layouts da esquerda para a direita.

-  *Pausar animações* -pausar e retomar animações criadas com o [`Animator`](https://developer.xamarin.com/api/type/Android.Animation.Animator/)
   .

-  *Ler texto alterando dinamicamente* -denotam partes da interface do usuário que atualizam dinamicamente com o novo texto como "regiões dinâmicas" com o novo [ `accessibilityLiveRegion`](http://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   atributo para que o novo texto serão lidos automaticamente no modo de acessibilidade.

-  *Aprimorar a experiência de áudio* -Verifique faixas de volume com o [`LoudnessEnhancer`](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/)
   , localize o pico e o RMS de um fluxo de áudio com o [`Visualizer`](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/)
   classe e obtenha informações de um [timestamp áudio](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/) para ajudar com a sincronização de áudio do vídeo.

-  *Em intervalo personalizado de sincronização ContentResolver* -KitKat adiciona uma variação para a hora em que uma solicitação de sincronização é executada. Sincronização de uma `ContentResolver` em tempo personalizado ou intervalo chamando `ContentResolver.RequestSync` e passar em um `SyncRequest`.

-  *Distinguir entre os controladores* -no KitKat, controladores são atribuídos os identificadores de inteiro exclusivo que podem ser acessados por meio do dispositivo `ControllerNumber` propriedade. Isso torna mais fácil diferenciar os jogadores separados em um jogo.

-  *Controle remoto* -com poucas alterações no lado de hardware e software, KitKat permite que você ative um dispositivo equipado com um transmissor IR em um controle remoto usando o `ConsumerIrService`e interaja com dispositivos periféricos com o novo [`RemoteController`](https://developer.xamarin.com/api/type/Android.Media.RemoteController/)
   APIs.

Para obter mais informações sobre as alterações de API acima, consulte o Google [APIs do Android 4.4](http://developer.android.com/about/versions/android-4.4.html) visão geral.


## <a name="summary"></a>Resumo

Este artigo introduziu algumas das novas APIs disponíveis no Android 4.4 (API nível 19) e abordadas as práticas recomendadas quando a transição de um aplicativo para KitKat. IT contornadas alterações para as APIs que afetam o usuário experiência, incluindo o *framework transição* e as novas opções de *temas*. Em seguida, ele introduziu o *estrutura de acesso de armazenamento* e `DocumentsProvider` classe, bem como a nova *APIs de impressão*. Ele explorou *emulação do cartão com base em host NFC* e como trabalhar com *sensores de baixa energia*, incluindo dois novos sensores para acompanhar as etapas do usuário. Por fim, ele demonstrou demonstrações em tempo real de aplicativos com a captura *gravação de tela*e forneceu uma lista detalhada de adições e alterações na API KitKat.


## <a name="related-links"></a>Links relacionados

- [Exemplo de KitKat](https://developer.xamarin.com/samples/KitKat/)
- [Android 4.4 APIs](http://developer.android.com/about/versions/android-4.4.html)
- [KitKat Android](http://developer.android.com/about/versions/kitkat.html)
