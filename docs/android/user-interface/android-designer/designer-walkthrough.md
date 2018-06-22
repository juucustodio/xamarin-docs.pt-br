---
title: Usando o Designer de Android
description: Este tópico é um passo a passo do Designer xamarin. Ele demonstra como criar uma interface do usuário para um aplicativo de navegador coloridas; Essa interface do usuário é criado inteiramente no Designer.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/10/2018
ms.openlocfilehash: 8d1dc410d5336d9c2505a18720cc7f734e838c39
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798626"
---
# <a name="using-the-android-designer"></a>Usando o Designer de Android

_Este tópico é um passo a passo do Designer xamarin. Ele demonstra como criar uma interface do usuário para um aplicativo de navegador coloridas; Essa interface do usuário é criado inteiramente no Designer._


## <a name="overview"></a>Visão geral

Interfaces de usuário do Android podem ser criadas declarativamente usando arquivos XML ou programaticamente, escrevendo código. O Designer de xamarin permite aos desenvolvedores criar e modificar layouts declarativos visualmente, sem a necessidade de lidar com o tédio edição manual de arquivos de XML. O Designer também fornece comentários em tempo real, o que permite ao desenvolvedor a avaliar as alterações de interface do usuário sem precisar reimplantar o aplicativo para um dispositivo ou emulador. Isso pode acelerar o desenvolvimento do Android da interface do usuário bastante. Neste artigo, apresentamos um passo a passo que mostra como usar o Designer do xamarin para criar visualmente uma interface do usuário.


## <a name="walkthrough"></a>Passo a passo

O objetivo deste passo a passo é usar o Designer de Android para criar uma interface do usuário para um aplicativo de navegador de cor de exemplo que apresenta uma lista de cores, seus nomes e seus valores RGB. Vamos examinar como adicionar widgets a superfície de Design, bem como para dispor esses widgets visualmente. Depois disso, vamos explicar como modificar interativamente na superfície de Design ou usando o Designer de widgets **propriedade preenchimento**. Finalmente, veremos como nosso design aparece ao executar o aplicativo.

Vamos começar!


### <a name="creating-a-new-project"></a>Criando um novo projeto

A primeira etapa é criar um novo projeto de xamarin.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Inicie o Visual Studio e clique em **novo projeto...**  , em seguida, escolha o **Visual C\# > Android > aplicativo do Android (Xamarin)** modelo:

[![Aplicativo do Android em branco](designer-walkthrough-images/vs/01-android-app-sml.w157.png)](designer-walkthrough-images/vs/01-android-app.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Inicie o Visual Studio para Mac e clique em **nova solução...** . Escolha o **aplicativo Android** modelo e clique em **próximo**:

[![Aplicativo do Android em branco](designer-walkthrough-images/xs/01-android-app-sml.png)](designer-walkthrough-images/xs/01-android-app.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Nomeie o novo aplicativo **Designer** e clique em **Okey**.

[![Nome de aplicativo](designer-walkthrough-images/vs/02-name-app-sml.png)](designer-walkthrough-images/vs/02-name-app.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Nomeie o novo aplicativo **Designer**. Em **plataformas de destino**, selecione **mais recente e maior** e clique em **próximo**:

[![Nome de aplicativo](designer-walkthrough-images/xs/02-designer-walkthrough-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough.png#lightbox)

Na próxima tela da caixa de diálogo, clique em **criar**.

-----



### <a name="adding-a-layout"></a>Adicionando um Layout

Vamos criar um **LinearLayout** que usaremos para manter nossos usuário elementos da interface.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No Visual Studio, clique com botão direito **recursos/layout** no **Solution Explorer** e selecione **Adicionar > Novo Item...** . No **Adicionar Novo Item** caixa de diálogo, selecione **Layout Android**. Nomeie o arquivo **ListItem.axml** e clique em **adicionar**:

[![Novo layout](designer-walkthrough-images/vs/03-new-layout-sml.w157.png)](designer-walkthrough-images/vs/03-new-layout.w157.png#lightbox)

O novo **ListItem** layout é exibido no Designer:

[![Designer de exibição](designer-walkthrough-images/vs/04-designer-view-sml.png)](designer-walkthrough-images/vs/04-designer-view.png#lightbox)

Clique o **fonte** guia na parte inferior do Designer para exibir a origem XML para este layout:

[![XML Designer](designer-walkthrough-images/vs/05-designer-xml-sml.png)](designer-walkthrough-images/vs/05-designer-xml.png#lightbox)

Do **exibição** menu, clique em **outras janelas > esboço de documento** para abrir o **esboço de documento**. O **esboço de documento** mostra que o layout atualmente contém um único **LinearLayout** widget:

[![Estrutura de tópicos do documento](designer-walkthrough-images/vs/06-document-outline-sml.png)](designer-walkthrough-images/vs/06-document-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

No Visual Studio para Mac, clique com botão direito **recursos/layout** no **solução** preenchimento e selecione **Adicionar > novo arquivo...** . No **novo arquivo** caixa de diálogo, selecione **Android > Layout**. Nomeie o arquivo **ListItem** e clique em **novo**:

[![Novo layout](designer-walkthrough-images/xs/03-new-layout-sml.png)](designer-walkthrough-images/xs/03-new-layout.png#lightbox)

O novo **ListItem** layout é exibido no Designer:

[![Designer de exibição](designer-walkthrough-images/xs/04-designer-view-sml.png)](designer-walkthrough-images/xs/04-designer-view.png#lightbox)

Clique o **fonte** guia na parte inferior do Designer para exibir a origem XML para este layout. Quando você clica o **esboço de documento** guia à direita, ele mostra que o layout atualmente contém um único **LinearLayout** widget:

[![XML Designer](designer-walkthrough-images/xs/05-designer-xml-sml.png)](designer-walkthrough-images/xs/05-designer-xml.png#lightbox)

-----



### <a name="creating-the-list-item-user-interface"></a>Criando a Interface de usuário do Item de lista

Em seguida, vamos criar a interface do usuário para o aplicativo de navegador de cor.
Clique o **Designer** guia para retornar à superfície de Design.
No **caixa de ferramentas**, role para baixo até o **imagens & mídia** seção e examinar cada item até localizar um *ImageView*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Localizar ImageView](designer-walkthrough-images/vs/07-locate-imageview-sml.png)](designer-walkthrough-images/vs/07-locate-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Localizar ImageView](designer-walkthrough-images/xs/06-locate-imageview-sml.png)](designer-walkthrough-images/xs/06-locate-imageview.png#lightbox)

-----

Como alternativa, você pode inserir *ImageView* na barra de pesquisa para localizar o `ImageView` widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Pesquisa ImageView](designer-walkthrough-images/vs/08-imageview-search-sml.png)](designer-walkthrough-images/vs/08-imageview-search.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Pesquisa ImageView](designer-walkthrough-images/xs/07-imageview-search-sml.png)](designer-walkthrough-images/xs/07-imageview-search.png#lightbox)

-----

Arraste esta `ImageView` na superfície de Design:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![ImageView na tela](designer-walkthrough-images/vs/09-imageview-on-canvas-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![ImageView na tela](designer-walkthrough-images/xs/08-imageview-on-canvas-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas.png#lightbox)

-----

Isso `ImageView` será usado para exibir uma amostra de cor em nosso aplicativo de navegador de cor.

Em seguida, arraste um `LinearLayout (Vertical)` widget do **caixa de ferramentas** no Designer. Observe que um contorno azul indica os limites do adicionado `LinearLayout`e o **esboço de documento** mostra que ela reside diretamente abaixo `imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Contorno azul](designer-walkthrough-images/vs/10-blue-outline-sml.png)](designer-walkthrough-images/vs/10-blue-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Contorno azul](designer-walkthrough-images/xs/10-blue-outline-sml.png)](designer-walkthrough-images/xs/10-blue-outline.png#lightbox)

-----

Quando você seleciona o `ImageView` no Designer, a estrutura de tópicos azul move para envolver a `ImageView`; no **esboço de documento**, move a seleção para `imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selecione ImageView](designer-walkthrough-images/vs/11-select-imageview-sml.png)](designer-walkthrough-images/vs/11-select-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Selecione ImageView](designer-walkthrough-images/xs/11-select-imageview-sml.png)](designer-walkthrough-images/xs/11-select-imageview.png#lightbox)

-----

Em seguida, arraste um `Text (Large)` widget do **caixa de ferramentas** no recém-adicionado `LinearLayout`. Observe que o Designer usa verde destaca para indicar onde o novo widget será inserido:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Destaques verdes](designer-walkthrough-images/vs/12-green-highlight-sml.png)](designer-walkthrough-images/vs/12-green-highlight.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Destaques verdes](designer-walkthrough-images/xs/12-green-highlight-sml.png)](designer-walkthrough-images/xs/12-green-highlight.png#lightbox)

-----

Em seguida, adicione um `Text (Small)` widget abaixo o `Text (Large)` widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Adicionar texto pequeno widget](designer-walkthrough-images/vs/13-add-small-text-sml.png)](designer-walkthrough-images/vs/13-add-small-text.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Adicionar texto pequeno widget](designer-walkthrough-images/xs/13-add-small-text-sml.png)](designer-walkthrough-images/xs/13-add-small-text.png#lightbox)

-----

Neste ponto, o Designer deve ser semelhante a captura de tela a seguir:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Layout do Designer](designer-walkthrough-images/vs/14-raw-layout-sml.png)](designer-walkthrough-images/vs/14-raw-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Layout do Designer](designer-walkthrough-images/xs/14-raw-layout-sml.png)](designer-walkthrough-images/xs/14-raw-layout.png#lightbox)

-----

Se os dois `textView` widgets não estão dentro `linearLayout1`, você pode arrastá-los para `linearLayout1` no **esboço de documento** e posicioná-los para que elas apareçam como mostrado na captura de tela anterior (recuado sob `linearLayout1`).



### <a name="arranging-the-user-interface"></a>Organizando a Interface do usuário

Vamos modificar a interface do usuário para exibir o `ImageView` à esquerda, com os dois `TextView` widgets empilhada à direita do `ImageView`.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Selecione o `ImageView`.

2.  No **janela propriedades**, clique no **categorizado** classificar ícone e role para baixo até o **Layout - ViewGroup** seção.

3.  Alterar o `layout_width` definindo como `wrap_content`:

![Defina a disposição de conteúdo](designer-walkthrough-images/vs/15-wrap-content.png "conjunto wrap conteúdo")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Com o `ImageView` selecionado, clique o **propriedades** guia.

2.  Logo abaixo do **propriedades** , clique em **Layout**.

3.  Role para baixo até **ViewGroup** e altere o `Width` definindo como `wrap_content`:

[![Conteúdo do conjunto de wrap](designer-walkthrough-images/xs/15-wrap-content-sml.png)](designer-walkthrough-images/xs/15-wrap-content.png#lightbox)

-----

Outra maneira de alterar o `Width` configuração é clique no triângulo no lado direito do widget para alternar a configuração de sua largura para `wrap_content`:

[![Arraste para definir a largura](designer-walkthrough-images/xs/16-width-arrow-sml.png)](designer-walkthrough-images/xs/16-width-arrow.png#lightbox)

Clique no triângulo novamente retorna o `Width` definindo como `match_parent`.

Em seguida, alterne para o **esboço de documento** e selecione a raiz `LinearLayout`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selecione raiz LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Selecione raiz LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout.png#lightbox)

-----
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Com a raiz `LinearLayout` selecionada, retornar para o **propriedades** janela, clique no **alfabético** classificar ícone e role até encontrar `orientation`. Alterar o `orientation` definindo como `horizontal`:

![Selecione a orientação horizontal](designer-walkthrough-images/vs/17-horizontal-orientation.png "selecione orientação horizontal")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Com a raiz `LinearLayout` selecionada, retornar para o **propriedades** guia e clique em **Widget**. Alterar o `Orientation` definindo como `horizontal`:

[![Selecione a orientação horizontal](designer-walkthrough-images/xs/18-horizontal-orientation-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation.png#lightbox)

-----

Neste ponto, o Designer deve ser semelhante a captura de tela a seguir:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Layout do Designer](designer-walkthrough-images/vs/18-designer-layout-sml.png)](designer-walkthrough-images/vs/18-designer-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Layout do Designer](designer-walkthrough-images/xs/19-designer-layout-sml.png)](designer-walkthrough-images/xs/19-designer-layout.png#lightbox)

-----


### <a name="modifying-the-spacing"></a>Modificando o espaçamento

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Em seguida, modificaremos configurações de preenchimento e margem na interface de usuário para fornecer mais espaço entre os widgets. Selecione o `ImageView`, clique no **categorizado** ícone de pesquisa no **propriedades** janela e role para baixo até o **Layout** seção. Alterar o `Min Height` para `70dp`, o `Min Width` para `50dp`e o `padding` para `10dp`. Isso se aplica preenchimento ao redor de todos os lados do `ImageView` e elongates-verticalmente:

[![Definir preenchimento](designer-walkthrough-images/vs/19-padding-widths-sml.png)](designer-walkthrough-images/vs/19-padding-widths.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Em seguida, modificaremos configurações de preenchimento e margem na interface de usuário para fornecer mais espaço entre os widgets. Selecione o `ImageView` e clique no **Layout** guia **propriedades**. Alterar o `Padding` para `10dp`, o `Min Width` para `50dp`e o `Min Height` para `70dp`. Isso se aplica preenchimento ao redor de todos os lados do `ImageView` e elongates-verticalmente:

[![Definir preenchimento](designer-walkthrough-images/xs/20-padding-widths-sml.png)](designer-walkthrough-images/xs/20-padding-widths.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A parte inferior, esquerda, direita e configurações de preenchimento superior pode ser definida independentemente inserindo valores para o `paddingBottom`, `paddingLeft`, `paddingRight`, e `paddingTop` campos, respectivamente.
Por exemplo, definir a `paddingLeft` campo `5dp` e `paddingBottom`, `paddingRight`, e `paddingTop` campos de `10dp`:

[![Configurações de preenchimento personalizada](designer-walkthrough-images/vs/20-custom-padding-sml.png)](designer-walkthrough-images/vs/20-custom-padding.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A parte superior, direita, inferior e configurações de preenchimento à esquerda podem ser definidos independentemente inserindo valores para o `Top`, `Right`, `Bottom`, e `Left` preenchimento de campos, respectivamente. Por exemplo, definir a `Left` para o valor de preenchimento `5dp` e `Top`, `Right`, e `Bottom` preenchimento valores `10dp`. Observe que o `Padding` configuração muda para uma lista separada por vírgulas desses valores:

[![Configurações de preenchimento personalizada](designer-walkthrough-images/xs/21-custom-padding-sml.png)](designer-walkthrough-images/xs/21-custom-padding.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Em seguida, ajustar a posição do `LinearLayout` widget que contém os dois `TextView` widgets. No **esboço de documento**, selecione `linearLayout1`. No **propriedades** janela, role até a **Layout - ViewGroup** seção. Definir `layout_marginBottom`, `layout_marginLeft`, `layout_marginRight`, e `layout_marginTop` para `5dp`, `5dp`, `0dp`, e `5dp` respectivamente:

[![Definir margens](designer-walkthrough-images/vs/21-margins-sml.png)](designer-walkthrough-images/vs/21-margins.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Em seguida, ajustar a posição do `LinearLayout` widget que contém os dois `TextView` widgets. No **esboço de documento**, selecione `linearLayout1`. No **propriedades** painel, selecione o **Layout** guia. Role para baixo até o **ViewGroup** seção e defina o `Left`, `Top`, `Right`, e `Bottom` margens para `5dp`, `5dp`, `0dp`, e `5dp` respectivamente:

[![Definir margens](designer-walkthrough-images/xs/22-margins-sml.png)](designer-walkthrough-images/xs/22-margins.png#lightbox)

-----



### <a name="removing-the-default-image"></a>Removendo a imagem padrão

Como estamos usando o `ImageView` para exibir cores (em vez de imagens), vamos remover a origem da imagem padrão adicionada pelo modelo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Selecione o `ImageView`.

2.  Em **propriedades**, localize o `src` campo.

3.  Limpar o `src` configuração de forma que ela está em branco:

![Limpar a configuração de src ImageView](designer-walkthrough-images/vs/22-clear-img-src.png "desmarcar a configuração de src ImageView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Selecione o `ImageView`.

2.  Clique o **Widget** guia **propriedades**.

3.  Limpar o `Src` configuração de forma que ela está em branco:

[![Limpar a configuração de src ImageView](designer-walkthrough-images/xs/23-clear-src-sml.png)](designer-walkthrough-images/xs/23-clear-src.png#lightbox)

-----


### <a name="adding-a-listview-container"></a>Adicionar um contêiner de ListView

Agora que o **ListItem** layout foi definido, vamos adicionar uma `ListView` ao layout do principal. Isso `ListView` conterá uma lista de **ListItems**.
No **caixa de ferramentas**, localize o `ListView` widget e arraste-o para a superfície de Design. O `ListView` no Designer, ficará em branco, exceto linhas azuis que descrevem sua borda quando ele é selecionado. Você pode exibir o **esboço de documento** para verificar se o **ListView** foi adicionada corretamente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Novo ListView](designer-walkthrough-images/vs/23-new-listview.png "novo ListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Novo ListView](designer-walkthrough-images/xs/24-new-listview-sml.png)](designer-walkthrough-images/xs/24-new-listview.png#lightbox)

-----

Por padrão, o `ListView` recebe um `Id` valor `@+id/listView1`.
Abra o **Widget** guia **propriedades** e altere o `Id` para `@+id/myListView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Renomeie o id para myListView](designer-walkthrough-images/vs/24-change-id.png "id de renomeação para myListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Renomeie o id para myListView](designer-walkthrough-images/xs/25-change-id-sml.png)](designer-walkthrough-images/xs/25-change-id.png#lightbox)

-----

Neste momento, nossa interface do usuário está pronta para uso.



### <a name="running-the-application"></a>Executando o aplicativo


Abra **MainActivity.cs** e substitua o código a seguir:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity (Label = "DesignerWalkthrough", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem> ();
        ListView listView;

        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);

            SetContentView (Resource.Layout.Main);
            listView = FindViewById<ListView> (Resource.Id.myListView);

            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.DarkRed,
                                               ColorName = "Dark Red", Code = "8B0000" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.SlateBlue,
                                               ColorName = "Slate Blue", Code = "6A5ACD" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.ForestGreen,
                                               ColorName = "Forest Green", Code = "228B22" });

            listView.Adapter = new ColorAdapter (this, colorItems);
        }
    }
    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView (int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.ListItem, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

Esse código usa um personalizado `ListView` adaptador para carregar informações de cor e exibir esses dados na interface de usuário que acabamos de criar. Para manter este exemplo curto, as informações de cor são codificado em uma lista, mas o adaptador pode ser modificado para extrair informações de cor de uma fonte de dados ou para o cálculo em tempo real. Para obter mais informações sobre `ListView` adaptadores, consulte [ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md).

Crie e execute o aplicativo. Captura de tela a seguir está um exemplo de como o aplicativo é exibido durante a execução em um dispositivo:

[![Captura de tela final](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)



## <a name="summary"></a>Resumo

Neste artigo, percorremos como usar o Designer de xamarin no Visual Studio para Mac para criar uma interface do usuário. Em seguida, demonstramos como criar a interface para um único item em uma lista.
Ao longo do caminho, vimos como adicionar widgets e dispor-los visualmente, bem como para atribuir recursos e para definir várias propriedades os widgets. Portanto, é ilustrado como a interface que criamos no Designer é executado em um aplicativo de exemplo.
